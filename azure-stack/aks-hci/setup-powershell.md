---
title: Rychlý Start pro nastavení hostitele služby Azure Kubernetes na Azure Stack HCI pomocí prostředí Windows PowerShell
description: Přečtěte si, jak nastavit hostitele služby Azure Kubernetes na Azure Stack HCL pomocí Windows PowerShellu.
author: jessicaguan
ms.topic: quickstart
ms.date: 09/23/2020
ms.author: jeguan
ms.openlocfilehash: 089488e246bdb7c12bbd0808ef2e92a4c83b0fce
ms.sourcegitcommit: b50dd116d6d1f89d42bd35ad0f85bb25c5192921
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "92253956"
---
# <a name="quickstart-set-up-an-azure-kubernetes-service-host-on-azure-stack-hci-using-powershell"></a>Rychlý Start: nastavení hostitele služby Azure Kubernetes na Azure Stack HCI pomocí prostředí PowerShell

> Platí pro: Azure Stack HCI

V tomto rychlém startu se dozvíte, jak nastavit hostitele služby Azure Kubernetes na Azure Stack HCI pomocí prostředí PowerShell. Pokud chcete místo toho použít Centrum pro správu systému Windows, přečtěte si téma [nastavení v centru pro správu Windows](setup.md).

## <a name="before-you-begin"></a>Než začnete

Než začnete, ujistěte se, že máte cluster 2-4 Azure Stack HCI nebo jeden uzel Azure Stack HCI. **Doporučujeme, abyste měli Azure Stack clusteru HCI v uzlu 2-4.** Pokud to neuděláte, postupujte podle pokynů na [stránce registrace rozhraní HCI Azure Stack](https://azure.microsoft.com/products/azure-stack/hci/hci-download/).

## <a name="step-1-download-and-install-the-akshci-powershell-module"></a>Krok 1: stažení a instalace modulu AksHci PowerShellu

Stáhněte si `AKS-HCI-Public=Preview-Oct-2020` ze [služby Azure Kubernetes na stránce registrace HCL Azure Stack](https://aka.ms/AKS-HCI-Evaluate). Soubor zip `AksHci.Powershell.zip` obsahuje modul PowerShellu.

Pokud jste dříve nainstalovali službu Azure Kubernetes Service na Azure Stack HCL pomocí prostředí PowerShell nebo centra pro správu Windows, před pokračováním spusťte následující příkaz.

   ```powershell
   Uninstall-AksHci
   ```

**Zavřete všechna okna PowerShellu.** Odstraňte všechny existující adresáře pro AksHci, AksHci. Day2 a MSK8sDownloadAgent, které se nacházejí v cestě `%systemdrive%\program files\windowspowershell\modules` . Až to uděláte, můžete extrahovat obsah nového souboru ZIP. Přesvědčte se, zda je soubor zip extrahován ve správném umístění ( `%systemdrive%\program files\windowspowershell\modules` ).

   ```powershell
   Import-Module AksHci
   ```

Po spuštění výše uvedeného příkazu zavřete všechna okna prostředí PowerShell a znovu otevřete relaci pro správu a spusťte příkazy v následujících krocích.

## <a name="step-2-prepare-your-machines-for-deployment"></a>Krok 2: Příprava počítačů na nasazení

Spuštěním kontrol na každém fyzickém uzlu zjistíte, jestli jsou splněné všechny požadavky, aby se nainstalovala služba Azure Kubernetes na Azure Stack HCL.

Otevřete PowerShell jako správce a spusťte následující příkaz.

   ```powershell
   Initialize-AksHciNode
   ```

Po dokončení kontrol se zobrazí text "Hotovo" zobrazený zeleným textem.

## <a name="step-3-configure-your-deployment"></a>Krok 3: konfigurace nasazení

Nastavte nastavení konfigurace pro hostitele služby Azure Kubernetes. **V případě clusteru 2-4 Azure Stack HCL musíte zadat `MultiNode` v `-deploymentType` `wssdImageDir` `cloudConfigLocation` parametrech a.** V případě clusteru s 1 uzly Azure Stack HCL jsou všechny parametry volitelné a nastavené na výchozí hodnoty. Pro zajištění optimálního výkonu však doporučujeme **použít Azure Stack nasazení clusteru HCI v uzlu 2-4.**

Nakonfigurujte nasazení pomocí následujícího příkazu.

   ```powershell
   Set-AksHciConfig [-deploymentType {SingleNode, MultiNode}]
                    [-wssdImageDir]
                    [-cloudConfigLocation]
                    [-nodeConfigLocation]
                    [-vnetName]
                    [-controlPlaneVmSize]
                    [-loadBalancerVmSize]
                    [-sshPublicKey]
                    [-vipPoolStartIp]
                    [-vipPoolEndIp]
                    [-macPoolStart]
                    [-macPoolEnd]
                    [-vlanID]
                    [-cloudServiceCidr]
                    [-wssdDir]
                    [-akshciVersion]
                    [-vnetType]
                    [-nodeAgentPort]
                    [-nodeAgentAuthorizerPort]
                    [-clusterRoleName]
                    [-skipHostLimitChecks]
                    [-insecure]
                    [-skipUpdates]
                    [-forceDnsReplication]

   ```

### <a name="optional-parameters"></a>Volitelné parametry

`-deploymentType`

Typ nasazení. Přijaté hodnoty: SingleNode, více uzlů. Výchozí hodnota je SingleNode.

`-wssdImageDir`

Cesta k adresáři, do kterého služba Azure Kubernetes v Azure Stack HCL uloží své image VHD. Výchozí hodnota `%systemdrive%\wssdimagestore` pro nasazení s jedním uzlem. *U nasazení s více uzly je potřeba tento parametr zadat*. Cesta musí ukazovat na cestu ke sdílenému úložišti, například  `C:\ClusterStorage\Volume2\ImageStore`   nebo na sdílenou složku SMB, jako je například  `\\FileShare\ImageStore` .

`-cloudConfigLocation`

Umístění, do kterého bude cloudový agent ukládat svou konfiguraci. Výchozí hodnota `%systemdrive%\wssdimagestore` pro nasazení s jedním uzlem. Umístění může být stejné jako cesta  `-wssdImageDir` uvedená výše.U *nasazení s více uzly je potřeba tento parametr zadat*.

`-nodeConfigLocation`

Umístění, kam budou v agentech zaukládána konfigurace. Musí se jednat o místní cestu.

`-vnetName`

Název virtuálního přepínače, ke kterému se mají připojit virtuální počítače Použije se výchozí název external. Přepínač bude vytvořen, pokud neexistuje.  

`-controlPlaneVmSize`

Velikost virtuálního počítače, který se má vytvořit pro plochu ovládacího prvku. Pokud chcete získat seznam dostupných velikostí virtuálních počítačů, spusťte příkaz `Get-AksHciVmSize` .

`-loadBalancerVmSize`

Velikost virtuálního počítače, který se má vytvořit pro virtuální počítače s Load Balancer Pokud chcete získat seznam dostupných velikostí virtuálních počítačů, spusťte příkaz `Get-AksHciVmSize` .

`-sshPublicKey`

Cesta k souboru veřejného klíče SSH Pomocí tohoto veřejného klíče se budete moct přihlásit ke každému virtuálnímu počítači vytvořenému službou Azure Kubernetes při nasazení Azure Stack HCI. Pokud není zadaný žádný klíč, budeme ho podíváme za  `%systemdrive%\Users\<username>\.ssh\id_rsa.pub` . Pokud soubor neexistuje, vygeneruje se a použije pár klíčů SSH ve výše uvedeném umístění.  

`-vipPoolStartIp`

Při použití fondů VIP pro vaše nasazení tento parametr určuje spuštění sítě ve fondu. Výchozí hodnota je none.

`-vipPoolEndIp`

Při použití fondů VIP pro vaše nasazení tento parametr určuje síťový konec fondu. Výchozí hodnota je none.

`-macPoolStart`

Tato možnost slouží k určení začátku adresy MAC pro fond adres MAC, který chcete použít pro virtuální počítač hostitele služby Azure Kubernetes. Syntaxe adresy MAC vyžaduje, aby nejméně významný bit prvního bajtu měl vždy hodnotu 0 a první bajt by měl být vždy sudým číslem (tj. 00, 02, 04, 06...). Typická adresa MAC může vypadat takto: 02:1E: 2B: 78:00:00. Výchozí hodnota je none.

`-macPoolEnd`

Tato možnost slouží k určení konce adresy MAC pro fond adres MAC, který chcete použít pro virtuální počítač hostitele služby Azure Kubernetes. Syntaxe adresy MAC vyžaduje, aby nejméně významný bit prvního bajtu měl vždy hodnotu 0 a první bajt by měl být vždy sudým číslem (tj. 00, 02, 04, 06...). První bajt předané adresy jako `-macPoolEnd` by měl být stejný jako první bajt adresy předané jako `-macPoolStart` . Výchozí hodnota je none.

`-vlandID`

Dá se použít k zadání ID sítě VLAN. Síťové adaptéry hostitele služby Azure Kubernetes a síťové adaptéry Kubernetes clusteru se označí pomocí zadaného ID sítě VLAN. Výchozí hodnota je none.

`cloudServiceCidr`

Dá se použít k zadání předpony statické IP adresy nebo sítě, která se má přiřadit ke službě Course CloudAgent. Tato hodnota by měla být poskytnuta pomocí formátu CIDR. (Příklad: 192.168.1.2/16). Výchozí hodnota je none.

`-wssdDir`

Toto je pracovní adresář, který modul používá k ukládání malých souborů. `%PROGRAMFILES%\AksHci`   U většiny nasazení je výchozí hodnota a neměla by se měnit.  

`-akshciVersion`

Verze služby Azure Kubernetes na Azure Stack HCL, kterou chcete nasadit. Výchozí hodnota je nejnovější verze.

`-vnetType`

Typ virtuálního přepínače, ke kterému se chcete připojit nebo kterou chcete vytvořit. Toto je výchozí nastavení typu přepínače "externí".

`-nodeAgentPort`

Číslo portu TCP/IP, na kterém má nodeagents naslouchat. Výchozí hodnota je 45000.  

`-nodeAgentAuthorizerPort`

Číslo portu TCP/IP, které nodeagents má použít pro svůj autorizační port. Výchozí hodnota je 45001.  

`-clusterRoleName`

Určuje název, který se má použít při vytváření cloudagent jako generické služby v rámci clusteru. Ve výchozím nastavení se jedná o jedinečný název s předponou CA – a příponou GUID (například CA-9e6eb299-bc0b-4f00-9fd7-942843820c26).

`-skipHostLimitChecks`

Vyžádá skript, aby přeskočil všechny kontroly, které potvrdí, že je k dispozici paměť a místo na disku, než umožní nasazení pokračovat.

`-insecure`

Nasadí službu Azure Kubernetes na Azure Stack komponenty HCI, jako je cloudagent a nodeagent (s) v nezabezpečeném režimu (bez zabezpečených připojení TLS).Nedoporučujeme používat v produkčním prostředí nezabezpečený režim.

`-skipUpdates`

Tento příznak použijte, pokud chcete přeskočit všechny dostupné aktualizace.

`-forceDnsReplication`

Replikace DNS může v některých systémech trvat až hodinu. Tím dojde k pomalému nasazení. Pokud dojde k tomuto problému, uvidíte, že Install-AksHci zablokování ve smyčce. Pokud chcete tento problém obdržet, zkuste použít tento příznak. `-forceDnsReplication`Příznak není zaručená oprava. Pokud logika za příznakem selže, bude chyba skrytá a příkaz bude proveden, jako by nebyl poskytnut příznak.

### <a name="reset-the-azure-kubernetes-service-on-azure-stack-hci-configuration"></a>Resetování služby Azure Kubernetes v Azure Stack konfiguraci rozhraní HCI

Pokud chcete resetovat službu Azure Kubernetes na Azure Stack konfiguraci HCI, spusťte následující příkaz. Spuštění tohoto příkazu na vlastní nastavení obnoví výchozí hodnoty konfigurace.

```powershell
Set-AksHciConfig
```

## <a name="step-4-start-a-new-deployment"></a>Krok 4: zahájení nového nasazení

Po dokončení konfigurace nasazení musíte spustit nasazení. Tím se nainstaluje služba Azure Kubernetes na Azure Stack agenti a služby HCI a hostitele služby Azure Kubernetes.

Nasazení zahájíte spuštěním následujícího příkazu.

```powershell
Install-AksHci
```

### <a name="verify-your-deployed-azure-kubernetes-service-host"></a>Ověření nasazeného hostitele služby Azure Kubernetes

Pokud chcete zajistit, aby byl nasazený hostitel služby Azure Kubernetes, spusťte následující příkaz. Po nasazení budete moci získat clustery s Kubernetes pomocí stejného příkazu.

```powershell
Get-AksHciCluster
```

## <a name="step-5-access-your-clusters-using-kubectl"></a>Krok 5: přístup ke svým clusterům pomocí kubectl

Pokud chcete získat přístup k hostiteli služby Azure Kubernetes nebo ke clusteru Kubernetes pomocí kubectl, spusťte následující příkaz. Tato akce použije zadaný soubor kubeconfig clusteru jako výchozí soubor kubeconfig pro kubectl.

```powershell
Get-AksHciCredential -clusterName
                     [-outputLocation]
```

### <a name="required-parameters"></a>Povinné parametry

`clusterName`

Název clusteru.

### <a name="optional-parameters"></a>Volitelné parametry

`outputLocation`

Umístění chcete stáhnout kubeconfig. Výchozí je `%USERPROFILE%\.kube`.

## <a name="get-logs"></a>Získání protokolů

Pokud chcete získat protokoly ze všech lusků, spusťte následující příkaz. Tento příkaz vytvoří výstupní složku zip s názvem `akshcilogs` v cestě `C:\wssd\akshcilogs` .

```powershell
Get-AksHciLogs
```

## <a name="reinstall-azure-kubernetes-service-on-azure-stack-hci"></a>Přeinstalujte službu Azure Kubernetes na Azure Stack HCL.

Přeinstalování služby Azure Kubernetes v systému Azure Stack HCI odstraní všechny clustery Kubernetes, pokud existují, a hostitele služby Azure Kubernetes. Odinstaluje taky službu Azure Kubernetes na Azure Stack agenty a služby HCI v uzlech. Pak se vrátí zpět kroky původní instalace, dokud se hostitel znovu nevytvoří. Služba Azure Kubernetes na Azure Stack konfiguraci HCI, kterou jste nakonfigurovali prostřednictvím nástroje `Set-AksHciConfig` , a stažené image VHDX se zachovají.

Pokud chcete přeinstalovat službu Azure Kubernetes na Azure Stack HCI, spusťte následující příkaz.

```powershell
Restart-AksHci
```

## <a name="remove-azure-kubernetes-service-on-azure-stack-hci"></a>Odebrat službu Azure Kubernetes v Azure Stack HCI

Pokud chcete odebrat službu Azure Kubernetes v Azure Stack HCI, spusťte následující příkaz.

```powershell
Uninstall-AksHci
```

## <a name="next-steps"></a>Další kroky

- [Vytvoření clusteru Kubernetes pro vaše aplikace](create-kubernetes-cluster-powershell.md)
