---
title: Co je modul AKS v centru Azure Stack?
description: Naučte se, jak pomocí nástroje příkazového řádku AKS Engine nasadit a spravovat cluster Kubernetes v Azure a centra Azure Stack.
author: mattbriggs
ms.topic: article
ms.date: 2/1/2021
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/02/2020
ms.openlocfilehash: 42ccc5c8858bf113b7308e4375ee3261a0a96a1f
ms.sourcegitcommit: ccc4ee05d71496653b6e27de1bb12e4347e20ba4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102231485"
---
# <a name="what-is-the-aks-engine-on-azure-stack-hub"></a>Co je modul AKS v centru Azure Stack?

Pomocí nástroje příkazového řádku AKS Engine můžete nasadit a spravovat cluster Kubernetes v Azure a centra Azure Stack. Pomocí modulu AKS můžete vytvářet, upgradovat a škálovat Azure Resource Manager nativních clusterů. Modul můžete použít k nasazení clusteru v připojených i odpojených prostředích. Tento článek poskytuje přehled modulu AKS, podporované scénáře použití modulu s rozbočovačem Azure Stack a Úvod k operacím, jako je nasazení, upgrade a škálování.

## <a name="overview-of-the-aks-engine"></a>Přehled modulu AKS

[Modul AKS](https://github.com/Azure/aks-engine) poskytuje nástroj příkazového řádku pro spouštění clusterů Kubernetes v Azure a centra Azure Stack. Pomocí Azure Resource Manager modul AKS pomáhá vytvářet a spravovat clustery běžící na virtuálních počítačích, virtuálních sítích a dalších prostředcích infrastruktury jako služby (IaaS) v centru Azure Stack.

## <a name="aks-engine-on-azure-stack-hub-considerations"></a>AKS Engine v centru Azure Stack – požadavky

Než začnete používat modul AKS v Azure Stackovém centru, je důležité porozumět rozdílům mezi centrem Azure Stack a Azure. Tato část popisuje různé funkce a klíčové důležité pokyny při použití Azure Stack hub s modulem AKS ke správě clusteru Kubernetes.

Další informace o konkrétních AKS modulu v centru pro Azure Stack a jeho rozdílech s ohledem na Azure najdete v tématu [AKS Engine v centru Azure Stack](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md).

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