---
title: Vytvoření nabídky v centru Azure Stack | Microsoft Docs
description: Naučte se, jak vytvořit nabídku pro uživatele v centru Azure Stack.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: bryanla
ms.reviewer: efemmano
ms.lastreviewed: 06/11/2019
ms.openlocfilehash: 50ba0848c5e52787b9864260a35b80b07ec982c8
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75804635"
---
# <a name="create-an-offer-in-azure-stack-hub"></a>Vytvoření nabídky v centru Azure Stack

[Nabídky](azure-stack-overview.md) jsou skupiny jednoho nebo několika plánů, které poskytovatelé předkládají uživatelům ke koupi nebo k přihlášení k odběru. Tento článek popisuje, jak vytvořit nabídku zahrnující [plán, který jste vytvořili](azure-stack-create-plan.md). Tato nabídka poskytuje předplatitelům možnost nastavovat virtuální počítače.

::: moniker range=">=azs-1902"
## <a name="create-an-offer-1902-and-later"></a>Vytvoření nabídky (1902 a novější)

1. Přihlaste se k [portálu správce centra Azure Stack](https://adminportal.local.azurestack.external) a vyberte **+ vytvořit prostředek**, pak **nabídky + plány**a pak **nabídku**.

   ![Vytvoření nabídky v centru Azure Stack](media/azure-stack-create-offer/offers.png)

2. Zobrazí se uživatelské rozhraní s kartami, které umožňuje definovat název nabídky. Můžete také přidat existující a vytvořit nové základní plány a plány doplňku. Nejdůležitější je, že před tím, než se rozhodnete vytvořit, si můžete prohlédnout podrobnosti nabídky, kterou vytvoříte.

   Na kartě **základy** zadejte **Zobrazovaný název** a **název prostředku**a potom v části **Skupina prostředků**vyberte **vytvořit novou** nebo **použít existující**. Zobrazovaný název je popisný název nabídky. Tento popisný název je jediná informace o nabídce, kterou uživatelé uvidí při přihlášení k odběru nabídky na portálu User Portal. Používejte intuitivní název, který uživatelům pomůže pochopit, co se dodává s nabídkou. Pouze správce může zobrazit název prostředku. Je to název, který správci používají pro práci s nabídkou jako s prostředkem Azure Resource Manageru. Na této kartě se můžete také rozhodnout, že tuto nabídku nastavíte jako veřejnou, nebo ji uložíte soukromou. Výchozí nastavení je privátní. [Veřejný nebo soukromý stav nabídky](#change-the-state-of-an-offer) můžete kdykoli změnit.

   ![Nová nabídka v centru Azure Stack](media/azure-stack-create-offer/new-offer.png)
  
3. Vyberte kartu **základní plány** nebo klikněte na tlačítko **Další: základní plány >** . Vyberte plány, které chcete zahrnout do nabídky.

   ![Vyberte plán, který chcete zahrnout do nabídky centra Azure Stack.](media/azure-stack-create-offer/select-plan.png)

4. V tomto okamžiku můžete vytvořit plán doplňku pro úpravu základního plánu, ale to je volitelné. V dalším článku máte příležitost vytvořit plán doplňku, [Azure Stack plány pro doplňky centra](create-add-on-plan.md).

5. Vyberte kartu **Revize + vytvořit** . Prohlédněte si souhrn nabídky a ujistěte se, že jsou všechny hodnoty správné. Rozhraní vám umožní rozšířit kvóty ve vybraných plánech po jednom, aby se zobrazily podrobnosti o každé kvótě v plánu. Můžete se také vrátit, abyste provedli nezbytné úpravy.

6. Vyberte **vytvořit** a vytvořte nabídku.

   ![Kontrola a vytvoření nabídky v centru Azure Stack](media/azure-stack-create-offer/review-offer.png)

### <a name="change-the-state-of-an-offer"></a>Změna stavu nabídky

Po vytvoření nabídky můžete změnit její stav. Aby mohli uživatelé získat úplné zobrazení, když se přihlásí k odběru, je nutné nabídky **zveřejnit** . Nabídky můžou být:

- **Veřejné**: viditelné pro uživatele.
- **Private**: viditelná jenom pro správce cloudu. Toto nastavení je užitečné při konceptu plánu nebo nabídky, nebo pokud chce správce cloudu [vytvořit každé předplatné pro uživatele](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator).
- **Vyřazeno z provozu**: Uzavřené pro nové odběratele. Správce cloudu může vyřadit z provozu nabídky, aby nedocházelo k budoucím předplatným, ale neovlivnili stávající předplatitele.

  > [!TIP]  
  > Změny nabídky nejsou okamžitě viditelné pro uživatele. Aby se změny projevily, uživatelé se možná budou muset odhlásit a znovu přihlásit na portál User Portal, aby se nová nabídka zobrazila.

Existují dva způsoby, jak změnit stav nabídky:

1. V části **všechny prostředky**vyberte název nabídky. Na obrazovce **Přehled** pro nabídku vyberte **změnit stav**. Vyberte stav, který chcete použít (například **Public**).

   ![Změnit stav nabídky centra Azure Stack](media/azure-stack-create-offer/change-state.png)

2. Vyberte **nastavení nabídky**. Zvolte stav, který chcete použít (například **Public**), a pak vyberte **Uložit**.

   ![Nastavení nabídky centra Azure Stack](media/azure-stack-create-offer/offer-settings.png)
::: moniker-end

::: moniker range="<=azs-1901"
## <a name="create-an-offer-1901-and-earlier"></a>Vytvoření nabídky (1901 a starší)

1. Přihlaste se k [portálu pro správu centra Azure Stack](https://adminportal.local.azurestack.external) a vyberte **+ vytvořit prostředek**, pak **nabídky tenanta + plány**a potom **nabídku**.

   ![Vytvoření nabídky v centru Azure Stack](media/azure-stack-create-offer/image01.png)
  
2. V části **Nová nabídka**zadejte **Zobrazovaný název** a **název prostředku**a potom v části **Skupina prostředků**vyberte **vytvořit novou** nebo **použít existující**. Zobrazovaný název je popisný název nabídky. Tento popisný název je jediná informace o nabídce, kterou uživatelé uvidí při přihlášení k odběru nabídky. Používejte intuitivní název, který uživatelům pomůže pochopit, co se dodává s nabídkou. Pouze správce může zobrazit název prostředku. Je to název, který správci používají pro práci s nabídkou jako s prostředkem Azure Resource Manageru.

   ![Nová nabídka v centru Azure Stack](media/azure-stack-create-offer/image01a.png)
  
3. Vyberte **základní plány** a otevřete **plán**. Vyberte plány, které chcete zahrnout do nabídky, a pak zvolte **Vybrat**. Chcete-li vytvořit nabídku, vyberte možnost **vytvořit**.

   ![Vyberte plán, který chcete zahrnout do nabídky centra Azure Stack.](media/azure-stack-create-offer/image02.png)
  
4. Po vytvoření nabídky můžete změnit její stav. Aby mohli uživatelé získat úplné zobrazení, když se přihlásí k odběru, je nutné nabídky **zveřejnit** . Nabídky můžou být:

   - **Veřejné**: viditelné pro uživatele.
   - **Private**: viditelná jenom pro správce cloudu. Toto nastavení je užitečné při konceptu plánu nebo nabídky, nebo pokud chce správce cloudu [vytvořit každé předplatné pro uživatele](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator).
   - **Vyřazeno z provozu**: Uzavřené pro nové odběratele. Správce cloudu může vyřadit z provozu nabídky, aby nedocházelo k budoucím předplatným, ale neovlivnili stávající předplatitele.

   > [!TIP]  
   > Změny nabídky nejsou okamžitě viditelné pro uživatele. Aby se změny projevily, uživatelé se možná budou muset odhlásit a znovu přihlásit na portál User Portal, aby se nová nabídka zobrazila.

   Na obrazovce Přehled pro nabídku vyberte možnost **stav přístupnosti**. Zvolte stav, který chcete použít (například **Public**), a pak vyberte **Uložit**.

     ![Změnit stav nabídky centra Azure Stack](media/azure-stack-create-offer/change-stage-1807.png)

     Jako alternativu vyberte **změnit stav** a pak zvolte stav.

    ![Vyberte stav přístupnosti pro vaši nabídku centra Azure Stack.](media/azure-stack-create-offer/change-stage-select-1807.png)

> [!NOTE]
> PowerShell můžete také použít k vytvoření výchozích nabídek, plánů a kvót. Další informace najdete v tématu [Azure Stack 1.4.0 Hub modul PowerShellu](/powershell/azure/azure-stack/overview?view=azurestackps-1.4.0).
::: moniker-end

## <a name="next-steps"></a>Další kroky

- Pokud chcete zjistit, jak upravit nabídku a poskytnout uživatelům plán doplňku, pokračujte v tématu [Vytvoření plánu doplňku](create-add-on-plan.md) (volitelné).
- V opačném případě přejděte k [odběru nabídky](azure-stack-subscribe-plan-provision-vm.md)