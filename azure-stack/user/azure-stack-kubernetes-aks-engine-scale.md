---
title: Škálování clusteru Kubernetes na Azure Stack | Microsoft Docs
description: Naučte se škálovat cluster Kubernetes na Azure Stack.
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
ms.openlocfilehash: 7847d79d0f2816aa56940fd7b81d25cbb1a3a7f2
ms.sourcegitcommit: 09d14eb77a43fd585e7e6be93c32fa427770adb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/16/2019
ms.locfileid: "71019213"
---
# <a name="scale-a-kubernetes-cluster-on-azure-stack"></a>Škálování clusteru Kubernetes na Azure Stack

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Pomocí příkazu **Scale** můžete škálovat cluster pomocí modulu AKS. Příkaz **Scale** znovu použije váš konfigurační soubor clusteru (`apimodel.json`) ve výstupním adresáři jako vstup pro nové nasazení Azure Resource Manager. Modul provádí operaci škálování v zadaném fondu agentů. Když je operace škálování hotová, modul aktualizuje definici clusteru v daném `apimodel.json` souboru tak, aby odrážela nové počty uzlů, aby odrážela aktualizovanou aktuální konfiguraci clusteru.

## <a name="scale-a-cluster"></a>Škálování clusteru

Příkaz může zvýšit nebo snížit počet uzlů v existujícím fondu agentů `aks-engine` v clusteru Kubernetes. `aks-engine scale` Uzly budou na konci fondu agentů vždy přidány nebo odebrány. Uzly budou uzavřené a vyprázdněny před odstraněním.

### <a name="values-for-the-scale-command"></a>Hodnoty pro příkaz Scale

Následující parametry používá příkaz Scale k vyhledání souboru definice clusteru a aktualizaci clusteru.

| Parametr | Příklad | Popis |
| --- | --- | --- | 
| Azure – ENV | AzureStackCloud | Při použití Azure Stack musí být názvy prostředí nastaveny na `AzureStackCloud`. | 
| location | místní | Toto je oblast pro vaši instanci Azure Stack. V případě ASDK je oblast nastavena na `local`.  | 
| resource-group | Kube – RG | Název skupiny prostředků, která obsahuje váš cluster. | 
| ID předplatného |  | Identifikátor GUID předplatného, které obsahuje prostředky používané vaším clusterem. Ujistěte se, že máte dostatečnou kvótu pro škálování v rámci vašeho předplatného. | 
| ID klienta |  | ID klienta instančního objektu používaného při vytváření clusteru z modulu AKS. | 
| client-secret |  | Tajný klíč instančního objektu použitý při vytváření clusteru | 
| rozhraní API – model | Kube-RG/apimodel. JSON | Cesta k souboru definice clusteru (apimodel. JSON) Může to být: _Output/\<pole dnsprefix >/apimodel.JSON | 
| -New-Node-Count | 9 | Požadovaný počet uzlů. | 
| – hlavní – plně kvalifikovaný název domény |  | Hlavní plně kvalifikovaný název domény. Nutné při horizontálním navýšení kapacity. | 

Při škálování clusteru v Azure Stack musíte zadat parametr **– Azure-ENV** . Další informace o parametrech a jejich hodnotách, které se používají v příkazu **Scale** pro modul AKS, najdete v tématu [Scale-Parameters](https://github.com/Azure/aks-engine/blob/master/docs/topics/scale.md#parameters).

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
    --master-FQDN <master FQDN> \
```

## <a name="next-steps"></a>Další postup

- Přečtěte si o modulu [AKS na Azure Stack](azure-stack-kubernetes-aks-engine-overview.md)
- [Upgrade clusteru Kubernetes na Azure Stack](azure-stack-kubernetes-aks-engine-upgrade.md)