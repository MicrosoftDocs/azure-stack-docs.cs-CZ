---
title: Nasazení clusteru Kubernetes s modulem AKS v Azure Stack | Microsoft Docs
description: Jak nasadit cluster Kubernetes na Azure Stack z virtuálního počítače klienta, na kterém běží modul AKS.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na (Kubernetes)
ms.devlang: nav
ms.topic: article
ms.date: 09/14/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/14/2019
ms.openlocfilehash: 8891ac7a1fb4b5b5af110e800f49c0d8fa873f66
ms.sourcegitcommit: 09d14eb77a43fd585e7e6be93c32fa427770adb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/16/2019
ms.locfileid: "71019323"
---
# <a name="deploy-a-kubernetes-cluster-with-the-aks-engine-on-azure-stack"></a>Nasazení clusteru Kubernetes s modulem AKS na Azure Stack

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Cluster Kubernetes můžete nasadit na Azure Stack z virtuálního počítače klienta, na kterém běží modul AKS. V tomto článku se podíváme na zápis specifikace clusteru, nasazení clusteru se `apimodel.json` souborem a kontrolu clusteru nasazením MySQL pomocí Helm.

## <a name="define-a-cluster-specification"></a>Definování specifikace clusteru

Specifikaci clusteru můžete zadat v souboru dokumentu pomocí formátu JSON, který se nazývá [model rozhraní API](https://github.com/Azure/aks-engine/blob/master/docs/topics/architecture.md#architecture-diagram). Modul AKS používá k vytvoření clusteru specifikaci clusteru v modelu rozhraní API. 

### <a name="update-the-api-model"></a>Aktualizace modelu rozhraní API

V této části se podíváme na vytvoření modelu rozhraní API pro váš cluster.

1.  Začněte pomocí [ukázkového](https://github.com/Azure/aks-engine/tree/master/examples/azure-stack) souboru modelu rozhraní API Azure Stack a vytvořte místní kopii pro vaše nasazení. Z počítače jste nainstalovali modul AKS, spusťte:

    ```bash
    curl -o kubernetes-azurestack.json https://raw.githubusercontent.com/Azure/aks-engine/master/examples/azure-stack/kubernetes-azurestack.json
    ```

    > [!Note]  
    > Pokud se odpojíte, můžete soubor stáhnout a ručně ho zkopírovat do odpojeného počítače, kde ho chcete upravit. Soubor můžete zkopírovat do počítače se systémem Linux pomocí nástrojů, jako jsou například výstupy [nebo WinSCP](https://www.suse.com/documentation/opensuse103/opensuse103_startup/data/sec_filetrans_winssh.html).

2.  Pokud chcete otevřít v editoru, můžete použít nano:

    ```bash
    nano ./kubernetes-azurestack.json
    ```

    > [!Note]  
    > Pokud nemáte nainstalovaný nano, můžete nainstalovat nano na Ubuntu: `sudo apt-get install nano`.

3.  V souboru Kubernetes-azurestack. JSON Najděte `orchestratorRelease`. Vyberte jednu z podporovaných verzí Kubernetes. Například 1,11, 1,12, 1,13, 1,14. Verze jsou často aktualizace. Zadejte verzi jako x. xx místo x. xx. x. Seznam aktuálních verzí najdete v tématu [podporované verze Kubernetes](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions). Podporovanou verzi můžete zjistit spuštěním následujícího příkazu AKS Engine:

    ```bash
    aks-engine get-versions
    ```

4.  Vyhledejte `portalURL` adresu URL portálu tenanta a poskytněte ji. Například, `https://portal.local.azurestack.external`.

5.  `masterProfile`V poli nastavte následující pole:

    | Pole | Popis |
    | --- | --- |
    | Pole dnsprefix | Zadejte jedinečný řetězec, který bude sloužit k identifikaci názvu hostitele virtuálních počítačů. Například název založený na názvu skupiny prostředků. |
    | count |  Zadejte počet hlavních serverů, které chcete pro nasazení nasadit. Minimální pro nasazení HA je 3, ale 1 je povolená pro nasazení bez vysoké dostupnosti. |
    | vmSize |  Zadejte [velikost podporovanou Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes), například `Standard_D2_v2`. |
    | distribuce | Zadejte `aks-ubuntu-16.04`. |

6.  V poli aktualizace `agentPoolProfiles` pole:

    | Pole | Popis |
    | --- | --- |
    | count | Zadejte počet agentů, které chcete pro nasazení. |
    | vmSize | Zadejte [velikost podporovanou Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes), například `Standard_D2_v2`. |
    | distribuce | Zadejte `aks-ubuntu-16.04`. |

7.  V poli aktualizace `linuxProfile` pole:

    | Pole | Popis |
    | --- | --- |
    | adminUsername | Zadejte uživatelské jméno správce virtuálního počítače. |
    | SSH | Zadejte veřejný klíč, který se bude používat pro ověřování SSH s virtuálními počítači. |

### <a name="more-information-about-the-api-model"></a>Další informace o modelu rozhraní API

- Úplný přehled všech dostupných možností v modelu rozhraní API najdete v tématu [definice clusteru](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md).  
- Nejdůležitější informace o konkrétních možnostech Azure Stack najdete v tématu [Azure Stack specifické definice clusteru](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#cluster-definition-aka-api-model).  

## <a name="deploy-a-kubernetes-cluster"></a>Nasazení clusteru Kubernetes

Po shromáždění všech požadovaných hodnot v modelu rozhraní API můžete vytvořit svůj cluster. V tomto okamžiku byste měli:

Zeptejte se Azure Stack operátora na:

- Ověřte stav systému, navrhněte spuštění `Test-AzureStack` a nástroj pro monitorování hardwaru od dodavatele OEM.
- Ověřte kapacitu systému včetně prostředků, jako jsou paměť, úložiště a veřejné IP adresy.
- Poskytněte podrobnosti o kvótě přidružené k vašemu předplatnému, abyste mohli ověřit, jestli je pro počet virtuálních počítačů, které chcete používat, dost místa.

Pokračujte v nasazení clusteru:

1.  Přečtěte si dostupné parametry pro modul AKS Azure Stack v části Příznaky rozhraní příkazového [řádku (CLI](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#cli-flags)).

    | Parametr | Příklad | Popis |
    | --- | --- | --- |
    | Azure – ENV | AzureStackCloud | K indikaci AKS Engine, že vaše cílová platforma je Azure Stack `AzureStackCloud`použít. |
    | location | místní | Název oblasti pro váš Azure Stack. Pro ASDK oblasti je nastavena na `local`. |
    | resource-group | Kube – RG | Zadejte název nové skupiny prostředků nebo vyberte existující skupinu prostředků. Název prostředku musí být alfanumerické znaky a malá písmena. |
    | rozhraní API – model | ./kubernetes-azurestack.json | Cesta ke konfiguračnímu souboru clusteru nebo modelu rozhraní API. |
    | výstupní adresář | Kube – RG | Zadejte název adresáře, který bude obsahovat výstupní soubor `apimodel.json` i další generované soubory. |
    | ID klienta | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Zadejte identifikátor GUID instančního objektu služby. ID klienta identifikované jako ID aplikace, když správce Azure Stack vytvořil instanční objekt. |
    | client-secret | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Zadejte tajný klíč objektu služby. Toto je tajný kód klienta, který jste nastavili při vytváření služby. |
    | ID předplatného | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Zadejte ID předplatného. Další informace najdete v tématu [přihlášení k odběru nabídky](https://docs.microsoft.com/azure-stack/user/azure-stack-subscribe-services#subscribe-to-an-offer) . |

    Zde naleznete příklad:

    ```bash  
    aks-engine deploy \\
    --azure-env AzureStackCloud
    --location <for asdk is local>\\
    --resource-group kube-rg \\
    --api-model ./kubernetes-azurestack.json \\
    --output-directory kube-rg \\
    --client-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \\
    --client-secret xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \\
    --subscription-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    ```

2.  Pokud z nějakého důvodu spuštění selhalo po vytvoření výstupního adresáře, můžete problém vyřešit a znovu spustit příkaz. Pokud znovu spustíte nasazení a předtím používali stejný výstupní adresář, modul AKS vrátí chybu oznamující, že adresář již existuje. Existující adresář můžete přepsat pomocí příznaku: `--force-overwrite`.

3.  Uložte konfiguraci clusteru modulu AKS do zabezpečeného, šifrovaného umístění.

    Vyhledejte soubor `apimodel.json`. Uložte ho do zabezpečeného umístění. Tento soubor se použije jako vstup ve všech dalších operacích AKS Engine.

    Vygenerovaný `apimodel.json` obsahuje veřejný klíč služby, tajný klíč a veřejný klíč SSH, který použijete ve vstupním modelu rozhraní API. Má také všechna ostatní metadata, která modul AKS potřebuje k provádění všech dalších operací. Pokud ho ztratíte, modul AKS nebude moci konfigurovat cluster.

    Tajné kódy nejsou **šifrované**. Ponechte soubor zašifrovaným a bezpečným místem. 

## <a name="verify-your-cluster"></a>Ověření clusteru

Ověřte cluster tak, že nasadíte MySQL s Helm a zkontrolujete cluster.

1. Získejte veřejnou IP adresu jednoho z vašich hlavních uzlů pomocí portálu Azure Stack.

2. Z počítače s přístupem k instanci Azure Stack se připojte přes SSH k novému hlavnímu uzlu pomocí klienta, jako je například vstup do výstupu nebo MobaXterm. 

3. Pro uživatelské jméno SSH použijete "azureuser" a soubor privátního klíče páru klíčů, který jste zadali pro nasazení clusteru.

4.  Spusťte následující příkazy:

    ```bash
    sudo snap install helm –classic
    helm repo update
    helm install stable/mysql
    ```

5. Pokud se po pokusu `install stable/mysql` o spuštění zobrazí chyba, `Error: incompatible versions client[v2.XX.X] server[v2.YY.Y]`například. Spusťte následující příkazy:

    ```bash 
    helm init --force-upgrade
    and retry:
    helm install stable/mysql
    ```

6.  Chcete-li vyčistit test, vyhledejte název používaný pro nasazení MySQL. V následujícím příkladu je `wintering-rodent`název. Pak ho odstraňte. 

    Spusťte následující příkazy:

    ```bash
    helm ls
    NAME REVISION UPDATED STATUS CHART APP VERSION NAMESPACE
    wintering-rodent 1 Thu Oct 18 15:06:58 2018 DEPLOYED mysql-0.10.1 5.7.14 default
    helm delete wintering-rodent
    ```

    Zobrazí se rozhraní příkazového řádku:
    ```bash
    release "wintering-rodent" deleted
    ```

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Řešení potíží s modulem AKS na Azure Stack](azure-stack-kubernetes-aks-engine-troubleshoot.md)