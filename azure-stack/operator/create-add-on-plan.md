---
title: V tomto článku se dozvíte, jak aktualizovat Azure Stack nabídky a plány | Microsoft Docs
description: Tento článek popisuje, jak zobrazit a upravit existující Azure Stack nabídky a plány.
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
ms.topic: conceptual
ms.custom: mvc
ms.date: 06/11/2019
ms.author: bryanla
ms.reviewer: efemmano
ms.lastreviewed: 06/11/2019
ms.openlocfilehash: d34b57a76f654e4d1825124f0f13ca26049332e5
ms.sourcegitcommit: 1bae55e754d7be75e03af7a4db3ec43fd7ff3e9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71319184"
---
# <a name="azure-stack-add-on-plans"></a>Azure Stack plány pro Doplňky

Jako operátor Azure Stack vytvoříte doplňkovou plány pro úpravu [základního plánu](azure-stack-create-plan.md) , pokud chcete nabízet další služby nebo zvýšit kapacitu *počítačů*, *úložiště*nebo *síťových* kvót mimo počáteční nabídku základní plán. Plány doplňku upravují základní plán a jsou volitelná rozšíření, která si uživatelé můžou zvolit k přihlášení k odběru.

Existují situace, kdy kombinace všeho v jednom plánu je optimální. Jindy možná budete chtít mít základní plán a potom nabízet další služby pomocí doplňkových plánů. Například se můžete rozhodnout nabízet služby IaaS jako součást základního plánu, přičemž všechny služby PaaS se zpracují jako doplňková plán.

Dalším důvodem pro použití doplňkových plánů je pomáhat sledovat využití prostředků. Uděláte to tak, že začnete se základním plánem, který obsahuje relativně malá kvóta (v závislosti na požadovaných službách). Až uživatelé dosáhnou kapacity, budou upozorněni, že využili přidělení prostředků na základě přiřazeného plánu. Odtud můžou uživatelé vybrat plán doplňku, který poskytuje další prostředky.

> [!NOTE]
> Pokud nechcete, aby kvóta rozšířila plán doplňku, můžete také zvolit [úpravu původní konfigurace kvóty](azure-stack-quota-types.md#edit-a-quota).

Když do stávajícího předplatného nabídky přidáte plán doplňku, může trvat až hodinu, než se zobrazí další prostředky.

Plány doplňku se vytvářejí úpravou stávající nabídky.

::: moniker range=">=azs-1902"
## <a name="create-an-add-on-plan-1902-and-later"></a>Vytvoření doplňkového plánu (1902 a novější)

1. Přihlaste se k portálu pro správu Azure Stack jako správce cloudu.
2. Použijte stejný postup, který slouží k [Vytvoření nového základního plánu](azure-stack-create-plan.md) pro vytvoření nového plánu, který nabízí služby, které nebyly dříve nabídnuty.
3. Na portálu pro správu klikněte na **nabídky** a potom vyberte nabídku, která se má aktualizovat pomocí doplňkového plánu.

   ![Vytvořit plán doplňku](media/create-add-on-plan/add-on1.png)

4. V dolní části vlastností nabídky vyberte možnost **plány doplňku**. Klikněte na **Přidat**.

    ![Vytvořit plán doplňku](media/create-add-on-plan/add-on2.png)

5. Vyberte plán, který chcete přidat. V tomto příkladu se plán nazývá **20-storageaccounts**. Po výběru plánu klikněte na **Vybrat** a přidejte plán do nabídky. Měli byste obdržet oznámení, že plán byl úspěšně přidán do nabídky.

    ![Vytvořit plán doplňku](media/create-add-on-plan/add-on3.png)

6. Zkontrolujte seznam doplňkových plánů, který je součástí nabídky, a ověřte, že je uvedený nový plán doplňku.

    [![Vytvořit plán doplňku](media/create-add-on-plan/add-on4.png "Vytvořit plán doplňku")](media/create-add-on-plan/add-on4lg.png#lightbox)
::: moniker-end

::: moniker range="<=azs-1901"
## <a name="create-an-add-on-plan-1901-and-earlier"></a>Vytvoření doplňkového plánu (1901 a starší)

1. Přihlaste se k portálu pro správu Azure Stack jako správce cloudu.
2. Použijte stejný postup, který slouží k [Vytvoření nového základního plánu](azure-stack-create-plan.md) pro vytvoření nového plánu, který nabízí služby, které nebyly dříve nabídnuty. V tomto příkladu budou do nového plánu zahrnuté služby Key Vault (**Microsoft. klíčů trezor**).
3. Na portálu pro správu klikněte na **nabídky** a potom vyberte nabídku, která se má aktualizovat pomocí doplňkového plánu.

   ![Vytvořit plán doplňku](media/create-add-on-plan/1.PNG)

4. Posuňte se do dolní části vlastností nabídky a vyberte možnost **plány doplňku**. Klikněte na **Přidat**.

    ![Vytvořit plán doplňku](media/create-add-on-plan/2.PNG)

5. Vyberte plán, který chcete přidat. V tomto příkladu se plán nazývá **plán trezoru klíčů**. Po výběru plánu klikněte na **Vybrat** a přidejte plán do nabídky. Měli byste obdržet oznámení, že plán byl úspěšně přidán do nabídky.

    ![Vytvořit plán doplňku](media/create-add-on-plan/3.PNG)

6. Zkontrolujte seznam doplňkových plánů, který je součástí nabídky, a ověřte, že je uvedený nový plán doplňku.

    ![Vytvořit plán doplňku](media/create-add-on-plan/4.PNG)
::: moniker-end

## <a name="next-steps"></a>Další kroky

* [Vytvoření nabídky](azure-stack-create-offer.md)
