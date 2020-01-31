---
title: Použití Azure Monitor pro kontejnery v centru Azure Stack
description: Naučte se používat Azure Monitor pro kontejnery v centru Azure Stack.
author: mattbriggs
ms.topic: article
ms.date: 11/15/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 11/15/2019
ms.openlocfilehash: 5b3172695fd6e0536360eed2dc4e370dd8bccecc
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76885249"
---
# <a name="use-azure-monitor-for-containers-on-azure-stack-hub"></a>Použití Azure Monitor pro kontejnery v centru Azure Stack

K monitorování kontejnerů v AKS nasazeném clusteru Kubernetes v centru Azure Stack můžete použít [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/) pro kontejnery. 

> [!IMPORTANT]
> Azure Monitor pro kontejnery v centru Azure Stack je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Můžete zkontrolovat výkon kontejneru pomocí Azure Monitor shromažďováním metrik paměti a procesoru z řadičů, uzlů a kontejnerů, které jsou dostupné v Kubernetes prostřednictvím rozhraní API metrik. Kromě toho služba shromažďuje protokoly kontejnerů. Pomocí těchto protokolů můžete diagnostikovat problémy v místním clusteru z Azure. Po nastavení monitorování z clusterů Kubernetes se tyto metriky a protokoly automaticky shromažďují. Kontejnerová verze agenta Azure Monitor Log Analytics pro Linux shromažďuje protokoly. Azure Monitor ukládá metriky a protokoly v pracovním prostoru Log Analytics, které jsou dostupné ve vašem předplatném Azure.

Existují dva způsoby, jak povolit Azure Monitor v clusteru. Oba způsoby vyžadují, abyste nastavili pracovní prostor Azure Monitor Log Analytics v Azure.

## <a name="prerequisites"></a>Požadavky

Obě metody vyžadují [požadavky](https://github.com/Helm/charts/tree/master/incubator/azuremonitor-containers#pre-requisites) uvedené v [kontejnerech Azure monitor –](https://github.com/Helm/charts/tree/master/incubator/azuremonitor-containers).

## <a name="method-one"></a>Metoda One

Pomocí grafu [Helm](https://helm.sh/) můžete také nainstalovat agenty monitorování do clusteru. Postupujte podle pokynů v následujícím článku [Azure monitor – kontejnery](https://github.com/Helm/charts/tree/master/incubator/azuremonitor-containers).

## <a name="method-two"></a>Metoda dvě

**Doplněk** můžete zadat v souboru JSON specifikace clusteru AKS Engine. Tento soubor se označuje také jako model rozhraní API. V tomto doplňku zajistěte, aby byla v tomto doplňku zakódovaná verze **WorkspaceGUID** a **WorkspaceKey** pracovního prostoru služby Azure Log Analytics, kde budou uloženy informace o monitorování.

Podporované definice rozhraní API pro cluster centra Azure Stack najdete v tomto příkladu: [Kubernetes-Container-monitoring_existing_workspace_id_and_key. JSON](https://github.com/Azure/aks-engine/blob/master/examples/addons/container-monitoring/kubernetes-container-monitoring_existing_workspace_id_and_key.json). Konkrétně Najděte vlastnost **Doplňky** v **kubernetesConfig**:

```JSON  
 "orchestratorType": "Kubernetes",
       "kubernetesConfig": {
         "addons": [
           {
             "name": "container-monitoring",
             "enabled": true,
             "config": {
               "workspaceGuid": "<Azure Log Analytics Workspace Guid in Base-64 encoded>",
               "workspaceKey": "<Azure Log Analytics Workspace Key in Base-64 encoded>"
             }
           }
         ]
       }
```

## <a name="next-steps"></a>Další kroky

- Přečtěte si o modulu [AKS v centru Azure Stack](azure-stack-kubernetes-aks-engine-overview.md) .  
- Přehled o [Azure monitor for Containers](https://docs.microsoft.com/azure/azure-monitor/insights/container-insights-overview)
