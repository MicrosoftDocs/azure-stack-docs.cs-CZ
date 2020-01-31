---
title: V tomto článku se dozvíte, jak aktualizovat nabídky a plány centra Azure Stack.
description: Tento článek popisuje, jak zobrazit a upravit existující nabídky a plány centra Azure Stack.
author: bryanla
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: bryanla
ms.reviewer: efemmano
ms.lastreviewed: 06/11/2019
ms.openlocfilehash: c4c01db560bbcd0f8312d205ad4643d4af48fed5
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76880005"
---
# <a name="azure-stack-hub-add-on-plans"></a>Plány doplňku centra Azure Stack

Jako operátor centra Azure Stack vytvoříte plány doplňku pro úpravu [základního plánu](azure-stack-create-plan.md) , pokud chcete nabízet další služby nebo zvýšit kapacitu *počítačů*, *úložišť*nebo *síťových* kvót mimo počáteční nabídku základní plán. Plány doplňku upravují základní plán a jsou volitelná rozšíření, která si uživatelé můžou povolit ve svém předplatném.

Existují situace, kdy kombinace všeho v jednom plánu je optimální. Jindy možná budete chtít mít základní plán a potom nabízet další služby pomocí doplňkových plánů. Například se můžete rozhodnout nabízet služby IaaS jako součást základního plánu, přičemž všechny služby PaaS se zpracují jako doplňková plán.

Dalším důvodem pro použití doplňkových plánů je pomáhat sledovat využití prostředků. Uděláte to tak, že začnete se základním plánem, který obsahuje relativně malá kvóta (v závislosti na požadovaných službách). Až uživatelé dosáhnou kapacity, budou upozorněni, že využili přidělení prostředků na základě přiřazeného plánu. Odtud můžou uživatelé vybrat plán doplňku, který poskytuje další prostředky.

> [!NOTE]
> Pokud nechcete, aby kvóta rozšířila plán doplňku, můžete také zvolit [úpravu původní konfigurace kvóty](azure-stack-quota-types.md#edit-a-quota).

Plány doplňku jsou [vytvořeny stejným způsobem](azure-stack-create-plan.md) jako základní plán. Rozdíl mezi těmito dvěma hodnotami je určen při přidání plánu do nabídky. Je určený buď jako základní plán, nebo jako plán doplňku. Když do existující nabídky přidáte plán doplňku, může trvat až hodinu, než se v předplatném zobrazí další prostředky.

::: moniker range=">=azs-1902"
## <a name="create-an-add-on-plan-1902-and-later"></a>Vytvoření doplňkového plánu (1902 a novější)

1. Přihlaste se k portálu správce Azure Stackového centra jako správce cloudu.
2. Použijte stejný postup, který slouží k [Vytvoření nového základního plánu](azure-stack-create-plan.md) pro vytvoření nového plánu, který nabízí služby, které nebyly dříve nabídnuty.
3. Na portálu pro správu klikněte na **nabídky** a potom vyberte nabídku, která se má aktualizovat pomocí doplňkového plánu.

   ![Vytvořit plán doplňku](media/create-add-on-plan/add-on1.png)

4. V dolní části vlastností nabídky vyberte možnost **plány doplňku**. Klikněte na tlačítko **Add** (Přidat).

    ![Vytvořit plán doplňku](media/create-add-on-plan/add-on2.png)

5. Vyberte plán, který chcete přidat. V tomto příkladu se plán nazývá **20-storageaccounts**. Po výběru plánu klikněte na **Vybrat** a přidejte plán do nabídky. Měli byste obdržet oznámení, že plán byl úspěšně přidán do nabídky.

    ![Vytvořit plán doplňku](media/create-add-on-plan/add-on3.png)

6. Zkontrolujte seznam doplňkových plánů, který je součástí nabídky, a ověřte, že je uvedený nový plán doplňku.

    [![Vytvořit plán doplňku](media/create-add-on-plan/add-on4.png "Vytvořit plán doplňku")](media/create-add-on-plan/add-on4lg.png#lightbox)
::: moniker-end

::: moniker range="<=azs-1901"
## <a name="create-an-add-on-plan-1901-and-earlier"></a>Vytvoření doplňkového plánu (1901 a starší)

1. Přihlaste se k portálu správce Azure Stackového centra jako správce cloudu.
2. Použijte stejný postup, který slouží k [Vytvoření nového základního plánu](azure-stack-create-plan.md) pro vytvoření nového plánu, který nabízí služby, které nebyly dříve nabídnuty. V tomto příkladu budou do nového plánu zahrnuté služby Key Vault (**Microsoft. klíčů trezor**).
3. Na portálu pro správu klikněte na **nabídky** a potom vyberte nabídku, která se má aktualizovat pomocí doplňkového plánu.

   ![Vytvořit plán doplňku](media/create-add-on-plan/1.PNG)

4. Posuňte se do dolní části vlastností nabídky a vyberte možnost **plány doplňku**. Klikněte na tlačítko **Add** (Přidat).

    ![Vytvořit plán doplňku](media/create-add-on-plan/2.PNG)

5. Vyberte plán, který chcete přidat. V tomto příkladu se plán nazývá **plán trezoru klíčů**. Po výběru plánu klikněte na **Vybrat** a přidejte plán do nabídky. Měli byste obdržet oznámení, že plán byl úspěšně přidán do nabídky.

    ![Vytvořit plán doplňku](media/create-add-on-plan/3.PNG)

6. Zkontrolujte seznam doplňkových plánů, který je součástí nabídky, a ověřte, že je uvedený nový plán doplňku.

    ![Vytvořit plán doplňku](media/create-add-on-plan/4.PNG)
::: moniker-end

## <a name="next-steps"></a>Další kroky

* [Vytvoření nabídky](azure-stack-create-offer.md)
