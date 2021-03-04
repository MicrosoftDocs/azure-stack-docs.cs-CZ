---
title: Připojení služby Azure Kubernetes na Azure Stack clusteru HCI do Azure ARC pro Kubernetes
description: Připojení služby Azure Kubernetes na Azure Stack clusteru HCI do Azure ARC pro Kubernetes
author: abha
ms.topic: how-to
ms.date: 12/02/2020
ms.author: abha
ms.reviewer: ''
ms.openlocfilehash: 96e1996cbf22e354b960b1a46a8848543942b0cc
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "101839857"
---
# <a name="connect-an-azure-kubernetes-service-on-azure-stack-hci-cluster-to-azure-arc-for-kubernetes"></a>Připojení služby Azure Kubernetes na Azure Stack clusteru HCI do Azure ARC pro Kubernetes

> Platí pro: AKS on Azure Stack HCI, AKS runtime na Windows serveru 2019 Datacenter

Když je služba Azure Kubernetes v Azure Stack clusteru HCI připojená ke službě Azure ARC, zobrazí se v Azure Portal. Bude mít ID Azure Resource Manager a spravovanou identitu. Clustery jsou připojené ke standardním předplatným Azure, jsou umístěné ve skupině prostředků a můžou přijímat značky stejně jako všechny ostatní prostředky Azure.

Aby mohl správce clusteru připojit ke službě Azure cluster Kubernetes, musí nasazovat agenty. Tito agenti se spouštějí v oboru názvů Kubernetes s názvem `azure-arc` a jsou standardními nasazeními Kubernetes. Agenti jsou odpovědní za připojení k Azure, shromažďování protokolů a metrik Azure ARC a sledování žádostí o konfiguraci.

Kubernetes s povoleným ARC Azure podporuje standardní protokol SSL pro zabezpečení přenášených dat. Data jsou také uložená v klidovém stavu v Azure Cosmos DB databázi, aby se zajistila důvěrnost dat.

Následující kroky poskytují návod ke zprovoznění služby Azure Kubernetes na Azure Stack clustery HCI do Azure ARC. **Tyto kroky můžete přeskočit, pokud jste už cluster Kubernetes připojili do Azure ARC prostřednictvím centra pro správu systému Windows.**

## <a name="before-you-begin"></a>Než začnete

Ověřte, že máte připravené tyto požadavky:

* Služba Azure Kubernetes v Azure Stack clusteru HCI s alespoň jedním pracovním uzlem Linux, který je v provozu. 

* Pro nasazení agentů Kubernetes s podporou ARC budete potřebovat soubor kubeconfig pro přístup ke clusteru a roli Správce clusteru v clusteru.
* Musí být nainstalovaná služba Azure Kubernetes v Azure Stack modul prostředí HCL pro rozhraní HCI.
* Pro instalaci rozšíření CLI s povoleným rozšířením Azure Arc se vyžaduje Azure CLI verze 2.3 +. Nainstalujte rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true). Můžete také aktualizovat na nejnovější verzi, abyste měli jistotu, že máte Azure CLI verze 2.3 +.
* Předplatné Azure, na kterém jste vlastníkem nebo přispěvatelem. 
* Spusťte příkazy v tomto dokumentu v okně pro správu prostředí PowerShell.


## <a name="network-requirements"></a>Požadavky sítě

Agenti Azure ARC vyžadují, aby následující protokoly/porty/odchozí adresy URL fungovaly.

* TCP na portu 443 – > `https://:443`
* TCP na portu 9418 – > `git://:9418`

| Koncový bod (DNS)                                                                                               | Popis                                                                                                                 |
| ------------------------------------------------------------------------------------------------------------ | --------------------------------------------------------------------------------------------------------------------------- |
| `https://management.azure.com`                                                                                 | Vyžaduje se, aby se agent připojil k Azure a zaregistroval cluster.                                                        |
| `https://eastus.dp.kubernetesconfiguration.azure.com`, `https://westeurope.dp.kubernetesconfiguration.azure.com` | Koncový bod roviny dat pro agenta, aby načetl stav a načetl informace o konfiguraci                                      |
| `https://docker.io`                                                                                            | Požadováno pro načtení imagí kontejneru                                                                                         |
| `https://github.com`, git://github.com                                                                         | Příklady úložišť GitOps jsou hostované na GitHubu. Agent konfigurace vyžaduje připojení k libovolnému koncovému bodu Git, který zadáte. |
| `https://login.microsoftonline.com`                                                                            | Požadováno pro načtení a aktualizaci tokenů Azure Resource Manager                                                                                    |
| `https://azurearcfork8s.azurecr.io`                                                                            | Požadováno pro získání imagí kontejneru pro agenty Azure ARC                                                                  |
| `https://eus.his.arc.azure.com`, `https://weu.his.arc.azure.com`                                                                            |  Požadováno pro získání certifikátů spravovaných identitou přidělené systémem                                                                  |

## <a name="step-1-log-in-to-azure"></a>Krok 1: přihlášení k Azure

Přihlaste se k Azure a po přihlášení nastavte předplatné Azure, na kterém jste vlastníkem nebo přispěvatelem jako výchozí předplatné.

```console
az login
az account set --subscription "00000000-aaaa-bbbb-cccc-000000000000"
```

## <a name="step-2-register-the-two-providers-for-azure-arc-enabled-kubernetes"></a>Krok 2: registrace dvou poskytovatelů pro Kubernetes s povoleným ARC Azure:

Tento krok můžete přeskočit, pokud jste už zaregistrovali dva poskytovatele pro službu Kubernetes s podporou ARC Azure v rámci vašeho předplatného. Registrace je asynchronní proces a musí být jednou pro každé předplatné. Registrace může trvat přibližně 10 minut. 

```console
az provider register --namespace Microsoft.Kubernetes
az provider register --namespace Microsoft.KubernetesConfiguration
```

Můžete ověřit, jestli jste se zaregistrovali pomocí následujících příkazů:

```console
az provider show -n Microsoft.Kubernetes -o table
az provider show -n Microsoft.KubernetesConfiguration -o table
```

## <a name="step-3-create-a-resource-group"></a>Krok 3: Vytvoření skupiny prostředků

Potřebujete skupinu prostředků pro uložení prostředku připojeného clusteru. Můžete použít existující skupinu prostředků v umístění Východní USA nebo Západní Evropa. Pokud v umístění Východní USA nebo Západní Evropa nemáte existující skupinu prostředků, vytvořte novou skupinu prostředků pomocí následujícího příkazu:

```console
az group create --name AzureArcTest -l EastUS -o table
```

## <a name="step-4-create-a-new-service-principal"></a>Krok 4: vytvoření nového instančního objektu


Tento krok můžete přeskočit, pokud jste již vytvořili instanční objekt s `contributor` rolí a znáte hodnoty AppID, Password a tenant instančního objektu.

Vytvořte nový instanční objekt s informativním názvem. Tento název musí být pro vašeho tenanta Azure Active Directory jedinečný. Výchozí role instančního objektu je `Contributor` . Tato role má v účtu Azure úplná oprávnění ke čtení i zápisu. Tento instanční objekt můžete také znovu použít na zprovoznění více clusterů do služby Azure ARC. Nastavte rozsah instančního objektu na *předplatná/skupina prostředků*. *Nezapomeňte uložit hodnoty appID, Password a tenant instančního objektu, protože budete potřebovat tyto podrobnosti v následujících krocích.*

```console
az ad sp create-for-RBAC --name "azure-arc-for-k8s" --scope /subscriptions/{Subscription ID}/resourceGroups/{Resource Group Name}
```

**Výkonem**

```
{
  "appId": "00000000-0000-0000-0000-000000000000",
  "displayName": "azure-arc-for-k8s",
  "name": "https://azure-arc-for-k8s",
  "password": "aaaaaaaa-bbbb-cccc-dddd-eeeeeeeeeeee",
  "tenant": "ffffffff-gggg-hhhh-iiii-jjjjjjjjjjjj"
}
```
## <a name="step-5-save-service-principal-details"></a>Krok 5: uložení podrobností objektu služby
Uložte vytvořené hodnoty appId, hesla a tenanta instančního objektu a název clusteru, ID předplatného Azure, název skupiny prostředků a umístění v proměnných PowerShellu. Tím se zajistí, že budete moci znovu použít podrobnosti v dalších kurzech. V případě, že chcete ukončit relaci PowerShellu, nezapomeňte tyto hodnoty uložit i v poznámkovém bloku.

```PowerShell
$clusterName = #<name of your Kubernetes cluster>
$resourceGroup = #<Azure resource group to store your connected Kubernetes cluster in Azure Arc>
$location = #<Azure resource group location. This can only be eastus or westeurope for Azure Arc for Kubernetes>
$subscriptionId = #<Azure subscription Id>
$appId = #<appID from the service principal created above>
$password = #<password from the service principal created above>
$tenant = #<tenant from the service principal created above>
```
Ujistěte se, že jste k proměnným přiřadili správné hodnoty spuštěním:

```PowerShell
echo $clusterName 
echo $resourceGroup
echo $location 
echo $subscriptionId 
echo $appId 
echo $password 
echo $tenant 
```

## <a name="step-6-connect-to-azure-arc-using-service-principal-and-the-aks-hci-powershell-module"></a>Krok 6: připojení ke službě Azure ARC pomocí instančního objektu a modulu Aks-Hci PowerShellu

Dále připojíme náš cluster Kubernetes k Azure s použitím instančního objektu a modulu Aks-Hci PowerShellu. V tomto kroku nasadíte agenty Azure ARC pro Kubernetes do `azure-arc` oboru názvů.

Odkažte na nově vytvořený instanční objekt a spusťte příkaz, který je `Install-AksHciArcOnboarding` k dispozici v modulu Aks-Hci PowerShellu.

```PowerShell
Install-AksHciArcOnboarding -name $clusterName -resourcegroup $resourceGroup -location $location -subscriptionid $subscriptionId -clientid $appId -clientsecret $password -tenantid $tenant
```
## <a name="verify-connected-cluster"></a>Ověřit připojený cluster

Prostředek clusteru Kubernetes můžete zobrazit na [Azure Portal](https://portal.azure.com/). Jakmile otevřete portál v prohlížeči, přejděte do skupiny prostředků a prostředku Kubernetes s podporou ARC Azure, který je založený na názvech prostředků a názvech skupin prostředků používaných dříve v `Install-AksHciArcOnboarding` příkazu PowerShellu.

> [!NOTE]
> Po připojení clusteru trvá přibližně 5 až 10 minut pro metadata clusteru (verze clusteru, verze agenta, počet uzlů) na plochu na stránce Přehled prostředku Kubernetes s podporou ARC Azure v Azure Portal.

Pokud chcete odstranit cluster nebo připojit cluster, pokud se nachází za odchozím proxy server, navštivte téma [připojení clusteru Kubernetes s povoleným ARC Azure](/azure/azure-arc/kubernetes/connect-cluster).

## <a name="azure-arc-agents-for-kubernetes"></a>Agenti Azure ARC pro Kubernetes

Kubernetes s podporou ARC Azure nasadí několik operátorů do `azure-arc` oboru názvů. Tato nasazení a lusky můžete zobrazit `kubectl` níže. 

```console
kubectl -n azure-arc get deployments,pods
```

Kubernetes s povoleným ARC Azure se skládá z několika agentů (operátorů), které běží v clusteru nasazeném do `azure-arc` oboru názvů.

* `deployment.apps/config-agent`: sleduje připojený cluster pro prostředky konfigurace správy zdrojového kódu použité v clusteru a aktualizuje stav dodržování předpisů.
* `deployment.apps/controller-manager`: je operátor operátorů a orchestruje interakce mezi komponentami ARC Azure.
* `deployment.apps/metrics-agent`: shromažďuje metriky jiných agentů ARC, aby se zajistilo, že tito agenti vykazují optimální výkon.
* `deployment.apps/cluster-metadata-operator`: shromažďuje metadata clusteru – verze clusteru, počet uzlů a verzi agenta Azure ARC.
* `deployment.apps/resource-sync-agent`: synchronizuje výše uvedená metadata clusteru do Azure.
* `deployment.apps/clusteridentityoperator`: Kubernetes s podporou ARC Azure aktuálně podporuje identitu přiřazenou systémem. clusteridentityoperator udržuje certifikát MSI (Managed Service identity), který používají jiní agenti pro komunikaci s Azure.
* `deployment.apps/flux-logs-agent`: shromažďuje protokoly z operátorů toku nasazených jako součást konfigurace správy zdrojového kódu.

## <a name="next-steps"></a>Další kroky

* [Použití GitOps v připojeném clusteru](/azure/azure-arc/kubernetes/use-gitops-connected-cluster)
* [Řízení konfigurace clusteru pomocí Azure Policy](/azure/azure-arc/kubernetes/use-azure-policy)
* [Povolení Azure Monitor v clusteru Kubernetes s povoleným ARC Azure](/azure/azure-monitor/insights/container-insights-enable-arc-enabled-clusters)
