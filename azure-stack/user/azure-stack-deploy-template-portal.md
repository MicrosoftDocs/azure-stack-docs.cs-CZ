---
title: Nasazení šablony pomocí portálu ve službě Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak pomocí portálu Azure Stack pro nasazení šablony.
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
ms.date: 05/07/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: 8f61667351a6d22094d5f8a0ba39348cc92549a8
ms.sourcegitcommit: 7f39bdc83717c27de54fe67eb23eb55dbab258a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2019
ms.locfileid: "66692014"
---
# <a name="deploy-a-template-using-the-portal-in-azure-stack"></a>Nasazení šablony pomocí portálu ve službě Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Na portálu můžete použít k nasazení šablony Azure Resource Manageru ke službě Azure Stack.

## <a name="to-deploy-a-template"></a>K nasazení šablony

1. Přihlaste se k portálu vyberte **+ vytvořit prostředek**a pak vyberte **vlastní**.

   ![Vytvořit prostředek v portálu Azure Stack](media/azure-stack-deploy-template-portal/template-deploy1.png)

1. Vyberte **Template deployment**.

   ![Nasazení šablony na portálu Azure Stack](media/azure-stack-deploy-template-portal/template-deploy2.png)

1. Vyberte **úpravy šablony**a vložte kód JSON šablony do okna kódu. Vyberte **Uložit**.

   ![Upravit šablonu v portálu Azure Stack](media/azure-stack-deploy-template-portal/template-deploy3.png)

1. Vyberte **upravit parametry**, zadejte hodnoty pro parametry, které jsou zobrazeny a pak vyberte **OK**.

   ![Upravit parametry v portálu Azure Stack](media/azure-stack-deploy-template-portal/template-deploy4.png)

1. Vyberte **předplatné**. Zvolte předplatné, které chcete použít a potom vyberte **OK**.

   ![Vyberte předplatné na portálu Azure Stack](media/azure-stack-deploy-template-portal/template-deploy5.png)

1. Vyberte **skupiny prostředků**. Zvolte existující skupinu prostředků nebo vytvořte novou a potom vyberte **OK**.

   ![Vyberte skupinu prostředků na portálu Azure Stack](media/azure-stack-deploy-template-portal/template-deploy6.png)

1. Vyberte **Vytvořit**. Nová dlaždice na řídicím panelu sleduje průběh nasazování šablony.

   ![Vytvoření šablony na portálu Azure Stack](media/azure-stack-deploy-template-portal/template-deploy7.png)

## <a name="next-steps"></a>Další postup

Další informace o nasazení šablony, najdete v následujícím článku:

- [Nasazení šablon pomocí PowerShellu](azure-stack-deploy-template-powershell.md)
