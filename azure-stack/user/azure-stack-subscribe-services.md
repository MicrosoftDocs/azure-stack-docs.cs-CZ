---
title: Vytvořte předplatné s nabídkou v Azure Stack | Microsoft Docs
description: Naučte se, jak vytvořit nové předplatné s nabídkou v Azure Stack a pak otestovat nabídku pomocí testovacího virtuálního počítače.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 06/04/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 11/13/2018
ms.openlocfilehash: 5b72ccee255aadd5d8f42aefea9e397ba310812c
ms.sourcegitcommit: 72d45bb935db0db172d4d7c37d8e48e79e25af64
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/22/2019
ms.locfileid: "68376804"
---
# <a name="tutorial-create-and-test-a-subscription-in-azure-stack"></a>Kurz: Vytvoření a otestování předplatného v Azure Stack

V tomto kurzu se dozvíte, jak vytvořit předplatné obsahující nabídku a jak ji otestovat. Pro tento test se přihlašujete k portálu Azure Stack User Portal jako správce cloudu, přihlaste se k odběru nabídky a pak vytvořte virtuální počítač (VM).

> [!TIP]
> Pro pokročilejší prostředí hodnocení můžete [Vytvořit předplatné pro konkrétního uživatele](../operator/azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator) a pak se přihlásit jako tento uživatel na portálu User Portal.

V tomto kurzu se dozvíte, jak se přihlásit k odběru Azure Stack nabídky.

Naučíte se:

> [!div class="checklist"]
> * Přihlášení k odběru nabídky 
> * Test nabídky

## <a name="subscribe-to-an-offer"></a>Přihlášení k odběru nabídky

Pokud se chcete přihlásit k odběru nabídky jako uživatel, přihlaste se k portálu Azure Stack User Portal a ověřte dostupné služby nabízené operátorem Azure Stack.

1. Přihlaste se k portálu User Portal a vyberte **získat předplatné**.

   ![Získat předplatné](media/azure-stack-subscribe-services/get-subscription.png)

2. Do pole **Zobrazovaný název** zadejte název předplatného. Pak vyberte **nabídku** a zvolte jednu z dostupných nabídek v části **vybrat nabídku** . Potom vyberte **Vytvořit**.

   ![Vytvoření nabídky](media/azure-stack-subscribe-services/create-subscription.png)

   > [!TIP]
   > Aktualizujte portál User Portal, abyste mohli začít používat vaše předplatné.

3. Pokud chcete zobrazit předplatné, které jste vytvořili, vyberte **všechny služby**. Pak v kategorii **Obecné** vyberte předplatná a potom vyberte nové předplatné. Po přihlášení k odběru nabídky aktualizujte portál a podívejte se, jestli byly nové služby zahrnuté jako součást nového předplatného. V tomto příkladu se přidaly **virtuální počítače** .

   ![Zobrazit předplatné](media/azure-stack-subscribe-services/view-subscription.png)

## <a name="test-the-offer"></a>Test nabídky

Když jste přihlášení k portálu User Portal, otestujte nabídku tím, že zřídíte virtuální počítač s využitím nových možností předplatného.

> [!NOTE]
> Tento test vyžaduje, aby se virtuální počítač Windows Server 2016 Datacenter nejdřív přidal do webu Azure Stack Marketplace.

1. Přihlaste se k portálu User Portal.

2. Na portálu User Portal vyberte **Virtual Machines**, pak **Přidat**, **Windows Server 2016 Datacenter**a pak vyberte **vytvořit**.

3. V části **základy** zadejte **jméno**, **uživatelské jméno**a **heslo**, zvolte **předplatné**, vytvořte **skupinu prostředků** (nebo vyberte existující) a pak vyberte **OK**.

4. V části **zvolit velikost** vyberte **a1 Standard**a pak zvolte **Vybrat**.  

5. V okně **Nastavení** přijměte výchozí hodnoty a pak vyberte **OK**.

6. V části **Souhrn** vyberte **OK** a vytvořte virtuální počítač.  

7. Pokud chcete zobrazit nový virtuální počítač, vyberte **virtuální počítače**, vyhledejte nový virtuální počítač a vyberte jeho jméno.

    ![Všechny zdroje](media/azure-stack-subscribe-services/view-vm.png)

> [!NOTE]
> Dokončení nasazení virtuálního počítače trvá několik minut.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření virtuálního počítače ze šablony komunity](azure-stack-create-vm-template.md)