---
title: Nasazení šablony pomocí portálu v Azure Stack | Microsoft Docs
description: Naučte se používat portál Azure Stack k nasazení šablony.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: eafa60f2-16c9-4ef1-b724-47709e9ea29e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/13/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: bbbbbc5548397f82752a43c7a1aaca7b62151b75
ms.sourcegitcommit: e8aa26b078a9bab09c8fafd888a96785cc7abb4d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2019
ms.locfileid: "71708993"
---
# <a name="deploy-a-template-using-the-portal-in-azure-stack"></a>Nasazení šablony pomocí portálu v Azure Stack

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Portál můžete použít k nasazení Azure Resource Manager šablon pro Azure Stack.

## <a name="to-deploy-a-template"></a>Nasazení šablony

1. Přihlaste se k portálu, vyberte **+ vytvořit prostředek**a pak vyberte **vlastní**.

   ![Vytvoření prostředku na portálu Azure Stack](media/azure-stack-deploy-template-portal/template-deploy1.png)

1. Vyberte **Template deployment**.

   ![Nasazení šablony na portálu Azure Stack](media/azure-stack-deploy-template-portal/template-deploy2.png)

1. Vyberte **Upravit šablonu**a vložte kód šablony JSON do okna Code (kód). Vyberte **Uložit**.

   ![Upravit šablonu na portálu Azure Stack](media/azure-stack-deploy-template-portal/template-deploy3.png)

1. Vyberte **Upravit parametry**, zadejte hodnoty parametrů, které jsou zobrazeny, a pak vyberte **OK**.

   ![Úprava parametrů na portálu Azure Stack](media/azure-stack-deploy-template-portal/template-deploy4.png)

1. Vyberte **předplatné**. Zvolte předplatné, které chcete použít, a pak vyberte **OK**.

   ![Výběr předplatného na portálu Azure Stack](media/azure-stack-deploy-template-portal/template-deploy5.png)

1. Vyberte **skupinu prostředků**. Zvolte existující skupinu prostředků nebo vytvořte novou, a pak vyberte **OK**.

   ![Výběr skupiny prostředků na portálu Azure Stack](media/azure-stack-deploy-template-portal/template-deploy6.png)

1. Vyberte **Vytvořit**. Nová dlaždice na řídicím panelu sleduje průběh nasazení šablony.

   ![Vytvoření šablony na portálu Azure Stack](media/azure-stack-deploy-template-portal/template-deploy7.png)

## <a name="next-steps"></a>Další kroky

Další informace o nasazování šablon najdete v následujícím článku:

- [Nasazení šablon pomocí PowerShellu](azure-stack-deploy-template-powershell.md)
