---
title: Nasazení clusteru Kubernetes s modulem AKS v centru Azure Stack
description: Postup nasazení clusteru Kubernetes na rozbočovači Azure Stack z virtuálního počítače klienta, na kterém běží modul AKS.
author: mattbriggs
ms.topic: article
ms.date: 4/23/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 4/23/2020
ms.openlocfilehash: 85f9e789db3ce86b04b490be83f355eb73e7329e
ms.sourcegitcommit: c51e7787e36c49d34ee86cabf9f823fb98b61026
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82218819"
---
# <a name="deploy-a-kubernetes-cluster-with-the-aks-engine-on-azure-stack-hub"></a>Nasazení clusteru Kubernetes s modulem AKS v centru Azure Stack

Cluster Kubernetes můžete v Azure Stack hub nasadit z virtuálního počítače klienta, na kterém běží modul AKS. V tomto článku se podíváme na zápis specifikace clusteru, nasazení clusteru se `apimodel.json` souborem a kontrolu clusteru nasazením MySQL pomocí Helm.

## <a name="define-a-cluster-specification"></a>Definování specifikace clusteru

Specifikaci clusteru můžete zadat v souboru dokumentu pomocí formátu JSON, který se nazývá [model rozhraní API](https://github.com/Azure/aks-engine/blob/master/docs/topics/architecture.md#architecture-diagram). Modul AKS používá k vytvoření clusteru specifikaci clusteru v modelu rozhraní API. 

### <a name="update-the-api-model"></a>Aktualizace modelu rozhraní API

V této části se podíváme na vytvoření modelu rozhraní API pro váš cluster.

1.  Začněte tím, že použijete [ukázkový](https://github.com/Azure/aks-engine/tree/master/examples/azure-stack) soubor modelu rozhraní API Azure Stack hub a vytvoříte místní kopii pro vaše nasazení. Z počítače jste nainstalovali modul AKS, spusťte:

    ```bash
    curl -o kubernetes-azurestack.json https://raw.githubusercontent.com/Azure/aks-engine/master/examples/azure-stack/kubernetes-azurestack.json
    ```

    > [!Note]  
    > Pokud se odpojíte, můžete soubor stáhnout a ručně ho zkopírovat do odpojeného počítače, kde ho chcete upravit. Soubor můžete zkopírovat do počítače se systémem Linux pomocí nástrojů, jako jsou například výstupy [nebo WinSCP](https://www.suse.com/documentation/opensuse103/opensuse103_startup/data/sec_filetrans_winssh.html).

2.  K otevření modelu rozhraní API v editoru můžete použít nano:

    ```bash
    nano ./kubernetes-azurestack.json
    ```

    > [!Note]  
    > Pokud nemáte nainstalovaný nano, můžete nainstalovat nano na Ubuntu: `sudo apt-get install nano`.

3.  V souboru Kubernetes-azurestack. JSON Najděte orchestratorRelease a orchestratorVersion. Vyberte jednu z podporovaných verzí Kubernetes. Například pro `orchestratorRelease` použití 1,14 nebo 1,15 a pro `orchestratorVersion` použití 1.14.7 nebo 1.15.10. Zadejte `orchestratorRelease` jako x. XX a orchestratorVersion jako x. xx. x. Seznam aktuálních verzí najdete v tématu [podporované verze modulu AKS](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-aks-engine-versions) .

4.  Vyhledejte `customCloudProfile` adresu URL portálu tenanta a poskytněte ji. Například, `https://portal.local.azurestack.external`. 

5. Přidejte `"identitySystem":"adfs"` , pokud používáte AD FS. Například:

    ```JSON  
        "customCloudProfile": {
            "portalURL": "https://portal.local.azurestack.external",
            "identitySystem": "adfs"
        },
    ```

    > [!Note]  
    > Pokud pro svůj systém identit používáte Azure AD, nemusíte přidávat pole **identitySystem** .

6. Vyhledejte `portalURL` adresu URL portálu tenanta a poskytněte ji. Například, `https://portal.local.azurestack.external`.

7.  V `masterProfile`nástroji nastavte následující pole:

    | Pole | Popis |
    | --- | --- |
    | Pole dnsprefix | Zadejte jedinečný řetězec, který bude sloužit k identifikaci názvu hostitele virtuálních počítačů. Například název založený na názvu skupiny prostředků. |
    | count |  Zadejte počet hlavních serverů, které chcete pro nasazení nasadit. Minimální pro nasazení HA je 3, ale 1 je povolená pro nasazení bez vysoké dostupnosti. |
    | vmSize |  Zadejte [velikost podporovanou Azure Stack hub](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes), například `Standard_D2_v2`. |
    | distribuce | Zadejte `aks-ubuntu-16.04`. |

8.  V `agentPoolProfiles` nástroji Update:

    | Pole | Popis |
    | --- | --- |
    | count | Zadejte počet agentů, které chcete pro nasazení. Maximální počet uzlů, které se mají použít na předplatné, je 50. Pokud nasazujete více než jeden cluster na předplatné, zajistěte, aby celkový počet agentů nepřesahuje 50. Ujistěte se, že jste používali položky konfigurace zadané v [ukázkovém souboru JSON modelu rozhraní API](https://github.com/Azure/aks-engine/blob/master/examples/azure-stack/kubernetes-azurestack.json).  |
    | vmSize | Zadejte [velikost podporovanou Azure Stack hub](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes), například `Standard_D2_v2`. |
    | distribuce | Zadejte `aks-ubuntu-16.04`. |




9.  V `linuxProfile` nástroji Update:

    | Pole | Popis |
    | --- | --- |
    | adminUsername | Zadejte uživatelské jméno správce virtuálního počítače. |
    | protokoly | Zadejte veřejný klíč, který se bude používat pro ověřování SSH s virtuálními počítači. Použijte `ssh-rsa` a pak klíč. Pokyny k vytvoření veřejného klíče najdete v tématu [vytvoření klíče SSH pro Linux](create-ssh-key-on-windows.md). |

    Pokud nasazujete do vlastní virtuální sítě, najdete pokyny k vyhledání a přidání požadovaných klíčů a hodnot do příslušných polí v modelu rozhraní API v tématu [nasazení clusteru Kubernetes do vlastní virtuální sítě](kubernetes-aks-engine-custom-vnet.md).

### <a name="more-information-about-the-api-model"></a>Další informace o modelu rozhraní API

- Úplný přehled všech dostupných možností v modelu rozhraní API najdete v tématu [definice clusteru](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md).  
- Nejdůležitější informace o konkrétních možnostech centra Azure Stack najdete v tématu věnovaném [specifickým definicím clusteru Azure Stack hub](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#cluster-definition-aka-api-model).  

## <a name="deploy-a-kubernetes-cluster"></a>Nasazení clusteru Kubernetes

Po shromáždění všech požadovaných hodnot v modelu rozhraní API můžete vytvořit svůj cluster. V tomto okamžiku byste měli:

Požádejte svého operátora centra Azure Stack:

- Ověřte stav systému, navrhněte spuštění `Test-AzureStack` a nástroj pro monitorování hardwaru od dodavatele OEM.
- Ověřte kapacitu systému včetně prostředků, jako jsou paměť, úložiště a veřejné IP adresy.
- Poskytněte podrobnosti o kvótě přidružené k vašemu předplatnému, abyste mohli ověřit, jestli je pro počet virtuálních počítačů, které chcete používat, dost místa.

Pokračujte v nasazení clusteru:

1.  Přečtěte si dostupné parametry pro modul AKS v části Azure Stack centra [CLI](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#cli-flags).

    | Parametr | Příklad | Popis |
    | --- | --- | --- |
    | Azure – ENV | AzureStackCloud | K indikaci AKS Engine, že vaše cílová platforma je Azure Stack použití `AzureStackCloud`centra. |
    | Identita – systém | službou | Nepovinný parametr. Pokud používáte federované služby Active Directory (AD FS), zadejte svoje řešení pro správu identit. |
    | location | local | Název oblasti centra Azure Stack. Pro ASDK je oblast nastavena na `local`. |
    | resource-group | Kube – RG | Zadejte název nové skupiny prostředků nebo vyberte existující skupinu prostředků. Název prostředku musí být alfanumerický a malý. |
    | rozhraní API – model | ./kubernetes-azurestack.json | Cesta ke konfiguračnímu souboru clusteru nebo modelu rozhraní API. |
    | výstupní adresář | Kube – RG | Zadejte název adresáře, který bude obsahovat výstupní soubor `apimodel.json` i další generované soubory. |
    | ID klienta | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Zadejte identifikátor GUID instančního objektu služby. ID klienta identifikované jako ID aplikace, když správce centra Azure Stack vytvořil instanční objekt. |
    | tajný kód klienta | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Zadejte tajný klíč objektu služby. Při vytváření služby nastavíte tajný klíč klienta. |
    | ID předplatného | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Zadejte ID předplatného. Další informace najdete v tématu [přihlášení k odběru nabídky](https://docs.microsoft.com/azure-stack/user/azure-stack-subscribe-services#subscribe-to-an-offer) . |

    Zde naleznete příklad:

    ```bash  
    aks-engine deploy \
    --azure-env AzureStackCloud \
    --location <for asdk is local> \
    --resource-group kube-rg \
    --api-model ./kubernetes-azurestack.json \
    --output-directory kube-rg \
    --client-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --client-secret xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --subscription-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --identity-system adfs # required if using AD FS
    ```

2.  Pokud z nějakého důvodu spuštění selhalo po vytvoření výstupního adresáře, můžete problém vyřešit a znovu spustit příkaz. Pokud znovu spustíte nasazení a předtím používali stejný výstupní adresář, modul AKS vrátí chybu oznamující, že adresář již existuje. Existující adresář můžete přepsat pomocí příznaku: `--force-overwrite`.

3.  Uložte konfiguraci clusteru modulu AKS do zabezpečeného, šifrovaného umístění.

    Vyhledejte soubor `apimodel.json`. Uložte ho do zabezpečeného umístění. Tento soubor se použije jako vstup ve všech dalších operacích AKS Engine.

    Vygenerovaný `apimodel.json` obsahuje veřejný klíč služby, tajný klíč a veřejný klíč SSH, který použijete ve vstupním modelu rozhraní API. Má také všechna ostatní metadata, která modul AKS potřebuje k provádění všech dalších operací. Pokud ho ztratíte, modul AKS nebude moci konfigurovat cluster.

    Tajné kódy nejsou **šifrované**. Ponechte soubor zašifrovaným a bezpečným místem. 

## <a name="verify-your-cluster"></a>Ověření clusteru

Ověřte cluster tak, že nasadíte MySql s Helm a zkontrolujete cluster.

1. Získejte veřejnou IP adresu jednoho z vašich hlavních uzlů pomocí portálu Azure Stack hub.

2. Z počítače s přístupem k instanci centra Azure Stack se připojte přes SSH k novému hlavnímu uzlu pomocí klienta, jako je například zápis do výstupu nebo MobaXterm. 

3. Pro uživatelské jméno SSH použijete "azureuser" a soubor privátního klíče páru klíčů, který jste zadali pro nasazení clusteru.

4. Spuštěním následujících příkazů vytvořte ukázkové nasazení Redis hlavního serveru (jenom pro připojená razítka):

   ```bash
   kubectl apply -f https://k8s.io/examples/application/guestbook/redis-master-deployment.yaml
   ```

    1. Dotaz na seznam lusků:

       ```bash
       kubectl get pods
       ```

    2. Odpověď by měla vypadat nějak takto:

       ```shell
       NAME                            READY     STATUS    RESTARTS   AGE
       redis-master-1068406935-3lswp   1/1       Running   0          28s
       ```

    3. Zobrazit protokoly nasazení:

       ```shell
       kubectl logs -f <pod name>
       ```

    Kompletní nasazení ukázkové aplikace PHP, která zahrnuje hlavní server Redis, najdete podle [pokynů uvedených tady](https://kubernetes.io/docs/tutorials/stateless-application/guestbook/).

5. V případě odpojeného razítka by měly být dostatečné následující příkazy:

    1. Nejdřív ověřte, že jsou spuštěné koncové body clusteru:

       ```bash
       kubectl cluster-info
       ```

       Výstup by měl vypadat nějak takto:

       ```shell
       Kubernetes master is running at https://democluster01.location.domain.com
       CoreDNS is running at https://democluster01.location.domain.com/api/v1/namespaces/kube-system/services/kube-dns:dns/proxy
       kubernetes-dashboard is running at https://democluster01.location.domain.com/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy
       Metrics-server is running at https://democluster01.location.domain.com/api/v1/namespaces/kube-system/services/https:metrics-server:/proxy
       ```

    2. Pak zkontrolujte stavy uzlů:

       ```bash
       kubectl get nodes
       ```

       Výstup by měl vypadat přibližně takto:

       ```shell
       k8s-linuxpool-29969128-0   Ready      agent    9d    v1.15.5
       k8s-linuxpool-29969128-1   Ready      agent    9d    v1.15.5
       k8s-linuxpool-29969128-2   Ready      agent    9d    v1.15.5
       k8s-master-29969128-0      Ready      master   9d    v1.15.5
       k8s-master-29969128-1      Ready      master   9d    v1.15.5
       k8s-master-29969128-2      Ready      master   9d    v1.15.5
       ```

6. Chcete-li vyčistit Redis POD nasazením z předchozího kroku, spusťte následující příkaz:

    ```bash
    kubectl delete deployment -l app=redis
    ```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Řešení potíží s modulem AKS v centru Azure Stack](azure-stack-kubernetes-aks-engine-troubleshoot.md)