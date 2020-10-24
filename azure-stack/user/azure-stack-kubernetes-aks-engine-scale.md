---
title: Škálování clusteru Kubernetes na rozbočovači Azure Stack
description: Naučte se škálovat cluster Kubernetes na rozbočovači Azure Stack.
author: mattbriggs
ms.topic: article
ms.date: 09/02/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/02/2020
ms.openlocfilehash: 16ffbd474886d477f37b7e2d39695e896984eb30
ms.sourcegitcommit: 74b3e14abfa33c8136eccdd4677868eb622c843e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92523537"
---
# <a name="scale-a-kubernetes-cluster-on-azure-stack-hub"></a>Škálování clusteru Kubernetes na rozbočovači Azure Stack

Pomocí příkazu **Scale** můžete škálovat cluster pomocí modulu AKS. Příkaz **Scale** znovu použije váš konfigurační soubor clusteru ( `apimodel.json` ) ve výstupním adresáři jako vstup pro nové nasazení Azure Resource Manager. Modul provádí operaci škálování v zadaném fondu agentů. Když je operace škálování hotová, modul aktualizuje definici clusteru v daném `apimodel.json` souboru tak, aby odrážela nové počty uzlů, aby odrážela aktualizovanou aktuální konfiguraci clusteru.

## <a name="scale-a-cluster"></a>Škálování clusteru

`aks-engine scale`Příkaz může zvýšit nebo snížit počet uzlů v existujícím fondu agentů v `aks-engine` clusteru Kubernetes. Uzly budou na konci fondu agentů vždy přidány nebo odebrány. Uzly budou uzavřené a vyprázdněny před odstraněním.

### <a name="values-for-the-scale-command"></a>Hodnoty pro příkaz Scale

Následující parametry používá příkaz Scale k vyhledání souboru definice clusteru a aktualizaci clusteru.

| Parametr | Příklad | Popis |
| --- | --- | --- | 
| Azure – ENV | AzureStackCloud | Při použití centra Azure Stack musí být názvy prostředí nastavené na `AzureStackCloud` . | 
| location | local | Toto je oblast pro vaši instanci centra Azure Stack. V případě ASDK je oblast nastavena na `local` .  | 
| resource-group | Kube – RG | Název skupiny prostředků, která obsahuje váš cluster. | 
| ID předplatného |  | Identifikátor GUID předplatného, které obsahuje prostředky používané vaším clusterem. Ujistěte se, že máte dostatečnou kvótu pro škálování v rámci vašeho předplatného. | 
| ID klienta |  | ID klienta instančního objektu používaného při vytváření clusteru z modulu AKS. | 
| tajný kód klienta |  | Tajný klíč instančního objektu použitý při vytváření clusteru | 
| rozhraní API – model | Kube-RG/apimodel.jszapnuto | Cesta k souboru definice clusteru (apimodel.jszapnutá). Může to být: _output/ \<dnsPrefix> /apimodel.jsna | 
| nový uzel-počet | 9 | Požadovaný počet uzlů. | 
| apiserver |  | Hlavní plně kvalifikovaný název domény. Nutné při horizontálním navýšení kapacity. |
| Identita – systém | službou | Nepovinný parametr. Pokud používáte federované služby Active Directory (AD FS), zadejte svoje řešení pro správu identit. |

Při škálování clusteru v Azure Stackovém centru musíte zadat parametr **--Azure-ENV** . Další informace o parametrech a jejich hodnotách, které se používají v příkazu **Scale** pro modul AKS, najdete v tématu [Scale-Parameters](https://github.com/Azure/aks-engine/blob/master/docs/topics/scale.md#parameters).

### <a name="command-to-scale-your-cluster"></a>Příkaz pro horizontální navýšení kapacity clusteru

Pokud chcete škálovat cluster, spusťte následující příkaz:

```bash
aks-engine scale \
    --azure-env AzureStackCloud   \
    --location <for an ASDK is local> \
    --resource-group <cluster resource group>
    --subscription-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --client-id xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --client-secret xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx \
    --api-model <path to your apomodel.json file>
    --new-node-count <desired node count> \
    --apiserver <master FQDN> \
    --identity-system adfs # required if using AD FS
```

## <a name="next-steps"></a>Další kroky

- Přečtěte si o modulu [AKS v centru Azure Stack](azure-stack-kubernetes-aks-engine-overview.md) .
- [Upgrade clusteru Kubernetes na rozbočovači Azure Stack](azure-stack-kubernetes-aks-engine-upgrade.md)
