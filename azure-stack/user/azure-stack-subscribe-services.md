---
title: Vytvoření předplatného s nabídkou v Azure Stack hub | Microsoft Docs
description: Naučte se vytvářet nové předplatné s nabídkou v Azure Stack hub a potom nabídku otestovat pomocí testovacího virtuálního počítače.
services: azure-stack
documentationcenter: ''
author: bryanla
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
ms.author: bryanla
ms.reviewer: efemmano
ms.lastreviewed: 11/13/2018
ms.openlocfilehash: 216fe342bcda090d69452e8623f34f6c43cf70b9
ms.sourcegitcommit: d62400454b583249ba5074a5fc375ace0999c412
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "76023183"
---
# <a name="tutorial-create-and-test-a-subscription-in-azure-stack-hub"></a>Kurz: vytvoření a otestování předplatného v centru Azure Stack

V tomto kurzu se dozvíte, jak vytvořit předplatné obsahující nabídku a jak ji otestovat. Pro test se přihlašujete k portálu User Portal služby Azure Stack hub jako správce cloudu, přihlásíte se k odběru nabídky a pak vytvoříte virtuální počítač (VM).

> [!TIP]
> Pro pokročilejší prostředí hodnocení můžete [Vytvořit předplatné pro konkrétního uživatele](../operator/azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator) a pak se přihlásit jako tento uživatel na portálu User Portal.

V tomto kurzu se dozvíte, jak se přihlásit k odběru nabídky centra Azure Stack.

Naučíte se:

> [!div class="checklist"]
> * Přihlášení k odběru nabídky 
> * Test nabídky

## <a name="subscribe-to-an-offer"></a>Přihlášení k odběru nabídky

Pokud se chcete přihlásit k odběru nabídky jako uživatel, přihlaste se k portálu User Portal služby Azure Stack a ověřte dostupné služby, které nabízí operátor centra Azure Stack.

1. Přihlaste se k portálu User Portal a vyberte **získat předplatné**.

   ![Získat předplatné](media/azure-stack-subscribe-services/get-subscription.png)

2. Do pole **Zobrazovaný název** zadejte název předplatného. Pak vyberte **nabídku** a zvolte jednu z dostupných nabídek v části **vybrat nabídku** . Potom vyberte **Vytvořit**.

   ![Vytvoření nabídky](media/azure-stack-subscribe-services/create-subscription.png)

   > [!TIP]
   > Aktualizujte portál User Portal, abyste mohli začít používat vaše předplatné.

3. Pokud chcete zobrazit předplatné, které jste vytvořili, vyberte **všechny služby**. Pak v kategorii **Obecné** vyberte **předplatná**a potom vyberte nové předplatné. Po přihlášení k odběru nabídky aktualizujte portál a podívejte se, jestli byly nové služby zahrnuté jako součást nového předplatného. V tomto příkladu se přidaly **virtuální počítače** .

   ![Zobrazit předplatné](media/azure-stack-subscribe-services/view-subscription.png)

## <a name="test-the-offer"></a>Test nabídky

Když jste přihlášení k portálu User Portal, otestujte nabídku tím, že zřídíte virtuální počítač s využitím nových možností předplatného.

> [!NOTE]
> Tento test vyžaduje, aby se virtuální počítač Windows Server 2016 Datacenter nejdřív přidal do Marketplace centra Azure Stack.

1. Přihlaste se k portálu User Portal.

2. Na portálu User Portal vyberte **Virtual Machines**, pak **Přidat**, **Windows Server 2016 Datacenter**a pak vyberte **vytvořit**.

3. V části **základy** zadejte **jméno**, **uživatelské jméno**a **heslo**, zvolte **předplatné**, vytvořte **skupinu prostředků** (nebo vyberte existující) a pak vyberte **OK**.

4. V části **zvolit velikost** vyberte **a1 Standard**a pak zvolte **Vybrat**.  

5. V okně **Nastavení** přijměte výchozí hodnoty a pak vyberte **OK**.

6. V části **Souhrn** vyberte **OK** a vytvořte virtuální počítač.  

7. Pokud chcete zobrazit nový virtuální počítač, vyberte **virtuální počítače**, vyhledejte nový virtuální počítač a vyberte jeho jméno.

    ![Všechny prostředky](media/azure-stack-subscribe-services/view-vm.png)

> [!NOTE]
> Dokončení nasazení virtuálního počítače trvá několik minut.

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Vytvoření virtuálního počítače ze šablony komunity](azure-stack-create-vm-template.md)
