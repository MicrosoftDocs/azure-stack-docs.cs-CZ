---
title: Co je modul AKS v Azure Stack? | Dokumenty Microsoft
description: Naučte se, jak pomocí nástroje příkazového řádku AKS Engine nasadit a spravovat cluster Kubernetes v Azure a Azure Stack.
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
ms.openlocfilehash: cb531570191216ea13a0b1a7a6d36738c0765cd3
ms.sourcegitcommit: 0b783e262ac87ae67929dbd4c366b19bf36740f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74310260"
---
# <a name="what-is-the-aks-engine-on-azure-stack"></a>Co je modul AKS v Azure Stack?

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Pomocí nástroje příkazového řádku AKS Engine můžete nasadit a spravovat cluster Kubernetes v Azure a Azure Stack. Pomocí modulu AKS můžete vytvářet, upgradovat a škálovat Azure Resource Manager nativních clusterů. Modul můžete použít k nasazení clusteru v připojených i odpojených prostředích. Tento článek poskytuje přehled modulu AKS, podporované scénáře použití modulu s Azure Stack a Úvod k operacím, jako je nasazení, upgrade a škálování.

## <a name="overview-of-the-aks-engine"></a>Přehled modulu AKS

[Modul AKS](https://github.com/Azure/aks-engine) poskytuje nástroj příkazového řádku pro zavedení clusterů Kubernetes v Azure a Azure Stack. Pomocí Azure Resource Manager modul AKS pomáhá vytvářet a spravovat clustery běžící na virtuálních počítačích, virtuálních sítích a dalších prostředcích infrastruktury jako služby (IaaS) v Azure Stack.

## <a name="aks-engine-on-azure-stack-considerations"></a>AKS modul s informacemi o Azure Stack

Než začnete používat modul AKS v Azure Stack, je důležité porozumět rozdílům mezi Azure Stack a Azure. Tato část popisuje různé funkce a klíčové důležité pokyny při použití Azure Stack s modulem AKS ke správě clusteru Kubernetes.

Další informace o konkrétních AKS Azure Stack modulu a jeho rozdílech s ohledem na Azure najdete v tématu [AKS Engine on Azure Stack](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md).

## <a name="supported-scenarios-with-the-aks-engine"></a>Podporované scénáře s modulem AKS

Tým podpory Azure Stack podporuje následující scénáře:

1.  AKS Engine nasadí všechny artefakty clusteru podle pokynů v této dokumentaci a pomocí [následující šablony](https://github.com/Azure/aks-engine/tree/master/examples/azure-stack).
2.  AKS Engine nasadí cluster do existující virtuální sítě. Další informace najdete v tématu [použití vlastní virtuální sítě s modulem AKS](https://github.com/Azure/aks-engine/blob/master/docs/tutorials/custom-vnet.md).
3.  Operace [upgradu](azure-stack-kubernetes-aks-engine-upgrade.md) a [škálování](azure-stack-kubernetes-aks-engine-scale.md) .

Další informace o modulu AKS a Azure Stack najdete v tématu [zásady podpory pro modul AKS na Azure Stack](azure-stack-kubernetes-aks-engine-support.md).

## <a name="install-the-aks-engine-and-deploy-a-kubernetes-cluster"></a>Instalace modulu AKS a nasazení clusteru Kubernetes

Nasazení clusteru Kubernetes s modulem AKS na Azure Stack:

1. [Nastavení požadavků pro modul AKS](azure-stack-kubernetes-aks-engine-set-up.md)
2. Nainstalujte modul AKS do počítače s přístupem k vašemu Azure Stack prostředí.
     - [Nainstalujte modul AKS ve Windows do Azure Stack](azure-stack-kubernetes-aks-engine-deploy-windows.md)
     - [Instalace modulu AKS v systému Linux v Azure Stack](azure-stack-kubernetes-aks-engine-deploy-linux.md)
3. [Nasazení clusteru Kubernetes s modulem AKS na Azure Stack](azure-stack-kubernetes-aks-engine-deploy-cluster.md)

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Nastavení požadavků pro modul AKS](azure-stack-kubernetes-aks-engine-set-up.md)