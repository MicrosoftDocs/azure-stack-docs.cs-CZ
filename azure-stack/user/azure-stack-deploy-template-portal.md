---
title: Nasazení šablony pomocí portálu v Azure Stackovém centru
description: Naučte se používat portál centra Azure Stack k nasazení šablony.
author: mattbriggs
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: 8425b4f8492e57502ca93b0be802b63601f6ba34
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77704314"
---
# <a name="deploy-a-template-using-the-portal-in-azure-stack-hub"></a>Nasazení šablony pomocí portálu v Azure Stackovém centru

Portál můžete použít k nasazení Azure Resource Manager šablon do centra Azure Stack.

## <a name="to-deploy-a-template"></a>Nasazení šablony

1. Přihlaste se k portálu, vyberte **+ vytvořit prostředek**a pak vyberte **vlastní**.

   ![Vytvoření prostředku na portálu Azure Stack hub](media/azure-stack-deploy-template-portal/template-deploy1.png)

1. Vyberte **Template deployment**.

   ![Nasazení šablony na portálu centra Azure Stack](media/azure-stack-deploy-template-portal/template-deploy2.png)

1. Vyberte **Upravit šablonu**a vložte kód šablony JSON do okna Code (kód). Vyberte **Save** (Uložit).

   ![Upravit šablonu na portálu centra Azure Stack](media/azure-stack-deploy-template-portal/template-deploy3.png)

1. Vyberte **Upravit parametry**, zadejte hodnoty parametrů, které jsou zobrazeny, a pak vyberte **OK**.

   ![Upravit parametry na portálu centra Azure Stack](media/azure-stack-deploy-template-portal/template-deploy4.png)

1. Vyberte **Předplatné**. Zvolte předplatné, které chcete použít, a pak vyberte **OK**.

   ![Výběr předplatného na portálu Azure Stack hub](media/azure-stack-deploy-template-portal/template-deploy5.png)

1. Vyberte **skupinu prostředků**. Zvolte existující skupinu prostředků nebo vytvořte novou, a pak vyberte **OK**.

   ![Vybrat skupinu prostředků na portálu Azure Stack hub](media/azure-stack-deploy-template-portal/template-deploy6.png)

1. Vyberte **Vytvořit**. Nová dlaždice na řídicím panelu sleduje průběh nasazení šablony.

   ![Vytvořit šablonu na portálu Azure Stack hub](media/azure-stack-deploy-template-portal/template-deploy7.png)

## <a name="next-steps"></a>Další kroky

Další informace o nasazování šablon najdete v následujícím článku:

- [Nasazení šablon pomocí PowerShellu](azure-stack-deploy-template-powershell.md)
