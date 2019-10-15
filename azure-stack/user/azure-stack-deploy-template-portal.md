---
title: Nasazení šablony pomocí portálu v Azure Stack | Microsoft Docs
description: Naučte se používat portál Azure Stack k nasazení šablony.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: eafa60f2-16c9-4ef1-b724-47709e9ea29e
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: 7777fc41de282e9fedec8f967d3a9387f7da8b0b
ms.sourcegitcommit: 7226979ece29d9619c959b11352be601562b41d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2019
ms.locfileid: "72304128"
---
# <a name="deploy-a-template-using-the-portal-in-azure-stack"></a>Nasazení šablony pomocí portálu v Azure Stack

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Portál můžete použít k nasazení Azure Resource Manager šablon pro Azure Stack.

## <a name="to-deploy-a-template"></a>Nasazení šablony

1. Přihlaste se k portálu, vyberte **+ vytvořit prostředek**a pak vyberte **vlastní**.

   ![Vytvoření prostředku na portálu Azure Stack](media/azure-stack-deploy-template-portal/template-deploy1.png)

1. Vyberte **Template deployment**.

   ![Nasazení šablony na portálu Azure Stack](media/azure-stack-deploy-template-portal/template-deploy2.png)

1. Vyberte **Upravit šablonu**a vložte kód šablony JSON do okna Code (kód). Vyberte **Save** (Uložit).

   ![Upravit šablonu na portálu Azure Stack](media/azure-stack-deploy-template-portal/template-deploy3.png)

1. Vyberte **Upravit parametry**, zadejte hodnoty parametrů, které jsou zobrazeny, a pak vyberte **OK**.

   ![Úprava parametrů na portálu Azure Stack](media/azure-stack-deploy-template-portal/template-deploy4.png)

1. Vyberte **předplatné**. Zvolte předplatné, které chcete použít, a pak vyberte **OK**.

   ![Výběr předplatného na portálu Azure Stack](media/azure-stack-deploy-template-portal/template-deploy5.png)

1. Vyberte **skupinu prostředků**. Zvolte existující skupinu prostředků nebo vytvořte novou, a pak vyberte **OK**.

   ![Výběr skupiny prostředků na portálu Azure Stack](media/azure-stack-deploy-template-portal/template-deploy6.png)

1. Vyberte **Create** (Vytvořit). Nová dlaždice na řídicím panelu sleduje průběh nasazení šablony.

   ![Vytvoření šablony na portálu Azure Stack](media/azure-stack-deploy-template-portal/template-deploy7.png)

## <a name="next-steps"></a>Další kroky

Další informace o nasazování šablon najdete v následujícím článku:

- [Nasazení šablon pomocí PowerShellu](azure-stack-deploy-template-powershell.md)
