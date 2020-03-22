---
title: Co je modul AKS v centru Azure Stack?
description: Naučte se, jak pomocí nástroje příkazového řádku AKS Engine nasadit a spravovat cluster Kubernetes v Azure a centra Azure Stack.
author: mattbriggs
ms.topic: article
ms.date: 3/19/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 3/19/2020
ms.openlocfilehash: ac4604d438cf0be03e2dd8c2d6050456ff2ac3a5
ms.sourcegitcommit: 17be49181c8ec55e01d7a55c441afe169627d268
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/21/2020
ms.locfileid: "80068978"
---
# <a name="what-is-the-aks-engine-on-azure-stack-hub"></a>Co je modul AKS v centru Azure Stack?

Pomocí nástroje příkazového řádku AKS Engine můžete nasadit a spravovat cluster Kubernetes v Azure a centra Azure Stack. Pomocí modulu AKS můžete vytvářet, upgradovat a škálovat Azure Resource Manager nativních clusterů. Modul můžete použít k nasazení clusteru v připojených i odpojených prostředích. Tento článek poskytuje přehled modulu AKS, podporované scénáře použití modulu s rozbočovačem Azure Stack a Úvod k operacím, jako je nasazení, upgrade a škálování.

## <a name="overview-of-the-aks-engine"></a>Přehled modulu AKS

[Modul AKS](https://github.com/Azure/aks-engine) poskytuje nástroj příkazového řádku pro spouštění clusterů Kubernetes v Azure a centra Azure Stack. Pomocí Azure Resource Manager modul AKS pomáhá vytvářet a spravovat clustery běžící na virtuálních počítačích, virtuálních sítích a dalších prostředcích infrastruktury jako služby (IaaS) v centru Azure Stack.

## <a name="aks-engine-on-azure-stack-hub-considerations"></a>AKS Engine v centru Azure Stack – požadavky

Než začnete používat modul AKS v Azure Stackovém centru, je důležité porozumět rozdílům mezi centrem Azure Stack a Azure. Tato část popisuje různé funkce a klíčové důležité pokyny při použití Azure Stack hub s modulem AKS ke správě clusteru Kubernetes.

Další informace o konkrétních AKS modulu v centru pro Azure Stack a jeho rozdílech s ohledem na Azure najdete v tématu [AKS Engine v centru Azure Stack](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md).

## <a name="supported-scenarios-with-the-aks-engine"></a>Podporované scénáře s modulem AKS

Následující scénáře jsou podporovány týmem podpory centra Azure Stack:

1.  AKS Engine nasadí všechny artefakty clusteru podle pokynů v této dokumentaci a pomocí [následující šablony](https://github.com/Azure/aks-engine/tree/master/examples/azure-stack).
2.  AKS Engine nasadí cluster do existující virtuální sítě. Další informace najdete v tématu [použití vlastní virtuální sítě s modulem AKS](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/custom-vnet.md).
3.  Operace [upgradu](azure-stack-kubernetes-aks-engine-upgrade.md) a [škálování](azure-stack-kubernetes-aks-engine-scale.md) .

Další informace o modulu AKS a centru Azure Stack najdete v tématu [zásady podpory pro modul AKS v centru Azure Stack](azure-stack-kubernetes-aks-engine-support.md).

## <a name="install-the-aks-engine-and-deploy-a-kubernetes-cluster"></a>Instalace modulu AKS a nasazení clusteru Kubernetes

Nasazení clusteru Kubernetes s modulem AKS v centru Azure Stack:

1. [Nastavení požadavků pro modul AKS](azure-stack-kubernetes-aks-engine-set-up.md)
2. Nainstalujte modul AKS do počítače s přístupem k prostředí centra Azure Stack.
     - [Instalace stroje AKS ve Windows do centra Azure Stack](azure-stack-kubernetes-aks-engine-deploy-windows.md)
     - [Instalace modulu AKS v systému Linux v centru Azure Stack](azure-stack-kubernetes-aks-engine-deploy-linux.md)
3. [Nasazení clusteru Kubernetes s modulem AKS v centru Azure Stack](azure-stack-kubernetes-aks-engine-deploy-cluster.md)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Nastavení požadavků pro modul AKS](azure-stack-kubernetes-aks-engine-set-up.md)