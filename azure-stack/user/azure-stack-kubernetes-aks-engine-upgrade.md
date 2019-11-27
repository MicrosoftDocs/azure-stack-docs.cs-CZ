---
title: Upgrade clusteru Kubernetes na Azure Stack | Microsoft Docs
description: Přečtěte si, jak upgradovat cluster Kubernetes na Azure Stack.
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
ms.date: 11/21/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: c7db293cb1a24079f66dcf0b047cf66218857003
ms.sourcegitcommit: 0b783e262ac87ae67929dbd4c366b19bf36740f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74310126"
---
# <a name="upgrade-a-kubernetes-cluster-on-azure-stack"></a>Upgrade clusteru Kubernetes na Azure Stack

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

## <a name="upgrade-a-cluster"></a>Upgrade clusteru

Modul AKS umožňuje upgradovat cluster, který byl původně nasazen pomocí nástroje. Clustery můžete udržovat pomocí modulu AKS. Vaše úlohy údržby jsou podobné jakémukoli IaaS systému. Měli byste si uvědomit o dostupnosti nových aktualizací a použít modul AKS k jejich použití.

Příkaz pro upgrade aktualizuje verzi Kubernetes a základní image operačního systému. Pokaždé, když spustíte příkaz pro upgrade pro každý uzel clusteru, vytvoří modul AKS nový virtuální počítač pomocí základní image AKS přidružené k používané verzi **AKS modulu** . Pomocí příkazu `aks-engine upgrade` můžete v clusteru zachovat měnu všech hlavních uzlů a uzlů agentů. 

Microsoft váš cluster nespravuje. Společnost Microsoft ale poskytuje nástroj a image virtuálních počítačů, které můžete použít ke správě clusteru. 

Pro nasazení nasazených upgradů clusteru:

-   Kubernetes
-   Poskytovatel Azure Stack Kubernetes
-   Základní operační systém

Při upgradu produkčního clusteru Vezměte v úvahu:

-   Používáte pro cílový cluster správnou specifikaci clusteru (`apimodel.json`) a skupinu prostředků?
-   Používáte pro klientský počítač spolehlivý počítač ke spuštění modulu AKS a ze kterého provádíte operace upgradu?
-   Ujistěte se, že máte cluster pro zálohování a že je funkční.
-   Pokud je to možné, spusťte příkaz z virtuálního počítače v prostředí Azure Stack, abyste snížili počet směrování sítě a potenciální problémy s připojením.
-   Ujistěte se, že vaše předplatné má dostatek místa pro celý proces. Proces během procesu přiděluje nové virtuální počítače.
-   Nejsou plánovány žádné aktualizace systému ani naplánované úlohy.
-   Nastavte připravený upgrade na cluster, který je nakonfigurovaný přesně jako produkční cluster, a před tím, než to uděláte, otestujte v produkčním clusteru.

## <a name="steps-to-upgrade-to-a-newer-kubernetes-version"></a>Postup upgradu na novější verzi Kubernetes

> [!Note]  
> Základní bitová kopie AKS bude upgradována i v případě, že používáte novější verzi modulu AKS a bitová kopie je k dispozici na webu Marketplace.

Následující pokyny používají minimální postup k provedení upgradu. Pokud chcete další podrobnosti, přečtěte si článek [upgrade Kubernetes clusterů](https://github.com/Azure/aks-engine/blob/master/docs/topics/upgrade.md).

1. Nejdřív musíte určit verze, které můžete pro upgrade cílit. Tato verze závisí na verzi, kterou máte aktuálně k dispozici, a potom k provedení upgradu použijte tuto hodnotu verze.

    Spusťte následující příkazy:

    ```bash  
    $ aks-engine get-versions
    Version Upgrades
    1.15.0
    1.14.3  1.15.0
    1.14.1  1.14.3, 1.15.0
    1.13.7  1.14.1, 1.14.3
    1.13.5  1.13.7, 1.14.1, 1.14.3
    1.12.8  1.13.5, 1.13.7
    1.12.7  1.12.8, 1.13.5, 1.13.7
    1.11.10 1.12.7, 1.12.8
    1.11.9  1.11.10, 1.12.7, 1.12.8
    1.10.13 1.11.9, 1.11.10
    1.10.12 1.10.13, 1.11.9, 1.11.10
    1.9.11  1.10.12, 1.10.13
    1.9.10  1.9.11, 1.10.12, 1.10.13
    1.6.9   1.9.10, 1.9.11
    ```

    Například v závislosti na výstupu příkazu `get-versions`, pokud je vaše aktuální verze Kubernetes "1.13.5", můžete upgradovat na "1.13.7, 1.14.1, 1.14.3".

2. Shromážděte informace, které budete potřebovat ke spuštění příkazu `upgrade`. Upgrade používá následující parametry:

    | Parametr | Příklad | Popis |
    | --- | --- | --- |
    | Azure – ENV | AzureStackCloud | K indikaci AKS Engine, že vaše cílová platforma je Azure Stack použít `AzureStackCloud`. |
    | location | místní | Název oblasti pro váš Azure Stack. Pro ASDK je oblast nastavená na `local`. |
    | resource-group | Kube – RG | Zadejte název nové skupiny prostředků nebo vyberte existující skupinu prostředků. Název prostředku musí být alfanumerické znaky a malá písmena. |
    | ID předplatného | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Zadejte ID předplatného. Další informace najdete v tématu [přihlášení k odběru nabídky](https://docs.microsoft.com/azure-stack/user/azure-stack-subscribe-services#subscribe-to-an-offer) . |
    | rozhraní API – model | ./kubernetes-azurestack.json | Cesta ke konfiguračnímu souboru clusteru nebo modelu rozhraní API. |
    | ID klienta | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Zadejte identifikátor GUID instančního objektu služby. ID klienta identifikované jako ID aplikace, když správce Azure Stack vytvořil instanční objekt. |
    | client-secret | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Zadejte tajný klíč objektu služby. Toto je tajný kód klienta, který jste nastavili při vytváření služby. |
    | Identita – systém | službou | Volitelná. Pokud používáte federované služby Active Directory (AD FS), zadejte svoje řešení pro správu identit. |

3. Po zadání vašich hodnot spusťte následující příkaz:

    ```bash  
    aks-engine upgrade \
    --azure-env AzureStackCloud   
    --location <for an ASDK is local> \
    --resource-group kube-rg \
    --subscription-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --api-model kube-rg/apimodel.json \
    --upgrade-version 1.13.5 \
    --client-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --client-secret xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --identity-system adfs # required if using AD FS
    ```

4.  Pokud z nějakého důvodu dojde k selhání operace upgradu, můžete po vyřešení problému znovu spustit upgrade příkazu. Modul AKS bude pokračovat v operaci, kde se předchozí krok nezdařil.

## <a name="steps-to-only-upgrade-the-os-image"></a>Postup upgradu image operačního systému

1. Projděte si [tabulku Supported-Kubernetes-Versions](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions) a určete, jestli máte naou verzi AKS a image AKS Base, kterou plánujete upgradovat. Chcete-li zobrazit verzi modulu AKS Run: `aks-engine version`.
2. Upgradujte modul AKS odpovídajícím způsobem v počítači, do kterého jste nainstalovali AKS modul runtime: `./get-akse.sh --version vx.xx.x` nahraďte **x. xx. x** cílovou verzí.
3. Požádejte svého operátora Azure Stack, aby přidal verzi AKS základního obrázku, kterou potřebujete na Azure Stack Marketplace, kterou plánujete použít.
4. Spusťte příkaz `aks-engine upgrade` pomocí stejné verze Kubernetes, kterou jste již používali, ale přidejte `--force`. V takovém případě se můžete podívat na příklad [vynucení upgradu](#forcing-an-upgrade).


## <a name="forcing-an-upgrade"></a>Vynucení upgradu

Můžou nastat situace, kdy budete chtít vynutit upgrade clusteru. Například jeden den nasadíte cluster v odpojeném prostředí pomocí nejnovější verze Kubernetes. Následující den Ubuntu uvolní opravu ohrožení zabezpečení, pro kterou Microsoft vygeneruje novou **základní image AKS**. Novou bitovou kopii můžete použít vynucením upgradu pomocí stejné verze Kubernetes, kterou jste už nasadili.

```bash  
aks-engine upgrade \
--azure-env AzureStackCloud   
--location <for an ASDK is local> \
--resource-group kube-rg \
--subscription-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
--api-model kube-rg/apimodel.json \
--upgrade-version 1.13.5 \
--client-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
--client-secret xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
--force
```

Pokyny najdete v tématu [vynucení upgradu](https://github.com/Azure/aks-engine/blob/master/docs/topics/upgrade.md#force-upgrade).

## <a name="next-steps"></a>Další kroky

- Přečtěte si o modulu [AKS na Azure Stack](azure-stack-kubernetes-aks-engine-overview.md)
- [Škálování clusteru Kubernetes na Azure Stack](azure-stack-kubernetes-aks-engine-scale.md)