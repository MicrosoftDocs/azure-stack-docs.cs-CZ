---
title: Upgrade clusteru Kubernetes na rozbočovači Azure Stack
description: Naučte se upgradovat cluster Kubernetes na rozbočovači Azure Stack.
author: mattbriggs
ms.topic: article
ms.date: 09/02/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/02/2020
ms.openlocfilehash: 7cfde51b5cfbdaf6d6ad752951ad4df3e4f95823
ms.sourcegitcommit: b80d529ff47b15b8b612d8a787340c7b0f68165b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2020
ms.locfileid: "89473040"
---
# <a name="upgrade-a-kubernetes-cluster-on-azure-stack-hub"></a>Upgrade clusteru Kubernetes na rozbočovači Azure Stack

## <a name="upgrade-a-cluster"></a>Upgrade clusteru

Modul AKS umožňuje upgradovat cluster, který byl původně nasazen pomocí nástroje. Clustery můžete udržovat pomocí modulu AKS. Vaše úlohy údržby jsou podobné jakémukoli IaaS systému. Měli byste si uvědomit o dostupnosti nových aktualizací a použít modul AKS k jejich použití.

Příkaz pro upgrade aktualizuje verzi Kubernetes a základní image operačního systému. Pokaždé, když spustíte příkaz pro upgrade pro každý uzel clusteru, vytvoří modul AKS nový virtuální počítač pomocí základní image AKS přidružené k používané verzi **AKS modulu** . Pomocí `aks-engine upgrade` příkazu můžete zachovat měnu všech hlavních uzlů a uzlů agentů v clusteru. 

Microsoft váš cluster nespravuje. Společnost Microsoft ale poskytuje nástroj a image virtuálních počítačů, které můžete použít ke správě clusteru. 

Pro nasazení nasazených upgradů clusteru:

-   Kubernetes
-   Poskytovatel Kubernetesů centra Azure Stack
-   Základní operační systém

Při upgradu produkčního clusteru Vezměte v úvahu:

-   Používáte pro cílový cluster správnou specifikaci clusteru ( `apimodel.json` ) a skupinu prostředků?
-   Používáte pro klientský počítač spolehlivý počítač ke spuštění modulu AKS a ze kterého provádíte operace upgradu?
-   Ujistěte se, že máte cluster pro zálohování a že je funkční.
-   Pokud je to možné, spusťte příkaz z virtuálního počítače v prostředí Azure Stack hub, abyste snížili počet směrování sítě a potenciální problémy s připojením.
-   Ujistěte se, že vaše předplatné má dostatek místa pro celý proces. Proces během procesu přiděluje nové virtuální počítače.
-   Nejsou plánovány žádné aktualizace systému ani naplánované úlohy.
-   Nastavte připravený upgrade na cluster, který je nakonfigurovaný přesně jako produkční cluster, a před tím, než to uděláte, otestujte v produkčním clusteru.

## <a name="steps-to-upgrade-to-a-newer-kubernetes-version"></a>Postup upgradu na novější verzi Kubernetes

> [!NOTE]  
> Základní bitová kopie AKS bude upgradována i v případě, že používáte novější verzi modulu AKS a bitová kopie je k dispozici na webu Marketplace.

Následující pokyny používají minimální postup k provedení upgradu. Pokud chcete další podrobnosti, přečtěte si článek [upgrade Kubernetes clusterů](https://github.com/Azure/aks-engine/blob/master/docs/topics/upgrade.md).

1. Nejdřív musíte určit verze, které můžete pro upgrade cílit. Tato verze závisí na verzi, kterou máte aktuálně k dispozici, a potom k provedení upgradu použijte tuto hodnotu verze. Verze Kubernetes podporované v nejnovější aktualizaci jsou 1.14.7 a 1.15.10. Pro dostupné upgrady použijte tuto tabulku:

| Aktuální verze | Dostupný upgrade |
| ------------------------- | ----------------------- |
| 1.15.10 | 1.15.12 |
| 1.15.12, 1.16.8, 1.16.9 | 1.16.14 |
| 1.16.8, 1.16.9, 1.16.14 | 1.17.11 |

Úplné mapování modulu AKS, verze AKS Base image a Kubernetes najdete v článku [podporované verze modulu AKS](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-aks-engine-versions).

2. Shromážděte informace, které budete potřebovat ke spuštění `upgrade` příkazu. Upgrade používá následující parametry:

    | parametr | Příklad | Popis |
    | --- | --- | --- |
    | Azure – ENV | AzureStackCloud | K indikaci AKS Engine, že vaše cílová platforma je Azure Stack použití centra `AzureStackCloud` . |
    | location | local | Název oblasti centra Azure Stack. Pro ASDK je oblast nastavena na `local` . |
    | resource-group | Kube – RG | Zadejte název nové skupiny prostředků nebo vyberte existující skupinu prostředků. Název prostředku musí být alfanumerický a malý. |
    | ID předplatného | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Zadejte ID předplatného. Další informace najdete v tématu [přihlášení k odběru nabídky](./azure-stack-subscribe-services.md#subscribe-to-an-offer) . |
    | rozhraní API – model | ./kubernetes-azurestack.jsna | Cesta ke konfiguračnímu souboru clusteru nebo modelu rozhraní API. |
    | ID klienta | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Zadejte identifikátor GUID instančního objektu služby. ID klienta identifikované jako ID aplikace, když správce centra Azure Stack vytvořil instanční objekt. |
    | tajný kód klienta | xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx | Zadejte tajný klíč objektu služby. Toto je tajný kód klienta, který jste nastavili při vytváření služby. |
    | Identita – systém | službou | Nepovinný parametr. Pokud používáte federované služby Active Directory (AD FS), zadejte svoje řešení pro správu identit. |

3. Po zadání vašich hodnot spusťte následující příkaz:

    ```bash  
    aks-engine upgrade \
    --azure-env AzureStackCloud \
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

1. Projděte si [tabulku Supported-Kubernetes-Versions](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-aks-engine-versions) a určete, jestli máte naou verzi AKS a image AKS Base, kterou plánujete upgradovat. Chcete-li zobrazit verzi modulu AKS Runtime: `aks-engine version` .
2. Upgradujte modul AKS odpovídajícím způsobem na počítači, kde jste nainstalovali AKS modul runtime: `./get-akse.sh --version vx.xx.x` nahraďte **x. xx. x** cílovou verzí.
3. Požádejte svého operátora centra Azure Stack, aby přidal verzi AKS základního obrázku, kterou potřebujete na webu centra Azure Stack, který chcete použít.
4. Spusťte `aks-engine upgrade` příkaz pomocí stejné verze Kubernetes, kterou jste již používali, ale přidejte `--force` . V takovém případě se můžete podívat na příklad [vynucení upgradu](#forcing-an-upgrade).


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

- Přečtěte si o modulu [AKS v centru Azure Stack](azure-stack-kubernetes-aks-engine-overview.md) .
- [Škálování clusteru Kubernetes na rozbočovači Azure Stack](azure-stack-kubernetes-aks-engine-scale.md)
