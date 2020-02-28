---
title: Vytvoření doplňkových plánů
titleSuffix: Azure Stack Hub
description: Naučte se vytvářet doplňková schémata v centru Azure Stack.
author: bryanla
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: bryanla
ms.reviewer: efemmano
ms.lastreviewed: 06/11/2019
ms.openlocfilehash: 2fd22acfe6f47d3a12502d5528e1f3fd96294460
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77696477"
---
# <a name="create-add-on-plans-in-azure-stack-hub"></a>Vytvoření doplňkových plánů v centru Azure Stack

Jako operátor centra Azure Stack vytvoříte plány doplňku pro úpravu [základního plánu](azure-stack-create-plan.md) , pokud chcete nabízet další služby nebo zvýšit kapacitu *počítačů*, *úložišť*nebo *síťových* kvót mimo počáteční nabídku základní plán. Plány doplňku upravují základní plán a jsou volitelná rozšíření, která si uživatelé můžou povolit ve svém předplatném.

Existují situace, kdy kombinace všeho v jednom plánu je optimální. Jindy možná budete chtít mít základní plán a potom nabízet další služby pomocí doplňkových plánů. Například se můžete rozhodnout, že chcete nabízet služby IaaS jako součást základního plánu se všemi PaaS službami, které se považují za plány doplňku.

Dalším důvodem pro použití doplňkových plánů je pomáhat sledovat využití prostředků. Uděláte to tak, že začnete se základním plánem, který obsahuje relativně malá kvóta (v závislosti na požadovaných službách). Až uživatelé dosáhnou kapacity, budou upozorněni, že využili přidělení prostředků na základě přiřazeného plánu. Odtud můžou uživatelé vybrat plán doplňku, který poskytuje další prostředky.

> [!NOTE]
> Pokud nechcete, aby kvóta rozšířila plán, můžete také zvolit [úpravu původní konfigurace kvóty](azure-stack-quota-types.md#edit-a-quota).

Plány doplňku jsou [vytvořeny stejným způsobem](azure-stack-create-plan.md) jako základní plán. Rozdíl mezi těmito dvěma hodnotami je určen při přidání plánu do nabídky. Je určený buď jako základní plán, nebo jako plán doplňku. Když do existující nabídky přidáte plán doplňku, může trvat až hodinu, než se v předplatném zobrazí další prostředky.

::: moniker range=">=azs-1902"
## <a name="create-an-add-on-plan-1902-and-later"></a>Vytvoření doplňkového plánu (1902 a novější)

1. Přihlaste se k portálu správce Azure Stackového centra jako správce cloudu.
2. Použijte stejný postup, který slouží k [Vytvoření nového základního plánu](azure-stack-create-plan.md) pro vytvoření nového plánu, který nabízí služby, které nebyly dříve nabídnuty.
3. Na portálu pro správu vyberte **nabídky** a potom vyberte nabídku, která se má aktualizovat pomocí doplňkového plánu.

   ![Vyberte nabídku, která se má aktualizovat pomocí doplňku plán na portálu Azure Stack správce.](media/create-add-on-plan/add-on1.png)

4. V dolní části vlastností nabídky vyberte možnost **plány doplňku**. Vyberte **Přidat**.

    ![Výběr doplňkových plánů na portálu pro správu Azure Stack](media/create-add-on-plan/add-on2.png)

5. Vyberte plán, který chcete přidat. V tomto příkladu se plán nazývá **20-storageaccounts**. Po výběru plánu klikněte na **Vybrat** a přidejte plán do nabídky. Měli byste obdržet oznámení, že plán byl úspěšně přidán do nabídky.

    ![Výběr plánu doplňku pro přidání na portál Azure Stack správce](media/create-add-on-plan/add-on3.png)

6. Zkontrolujte seznam doplňkových plánů, který je součástí nabídky, a ověřte, že je uvedený nový plán doplňku.

    ![[Kontrola seznamu doplňkových plánů na portálu pro správu Azure Stack] (Media/Create-Add-on-Plan/Add-on4. png "vytvořit doplněk plán")](media/create-add-on-plan/add-on4lg.png#lightbox)
::: moniker-end

::: moniker range="<=azs-1901"

## <a name="create-an-add-on-plan-1901-and-earlier"></a>Vytvoření doplňkového plánu (1901 a starší)

1. Přihlaste se k portálu správce Azure Stackového centra jako správce cloudu.
2. Použijte stejný postup, který slouží k [Vytvoření nového základního plánu](azure-stack-create-plan.md) pro vytvoření nového plánu, který nabízí služby, které nebyly dříve nabídnuty. V tomto příkladu budou do nového plánu zahrnuté služby Key Vault (**Microsoft. klíčů trezor**).
3. Na portálu pro správu vyberte **nabídky** a potom vyberte nabídku, která se má aktualizovat pomocí doplňkového plánu.

   ![Vyberte nabídku, která se má aktualizovat pomocí doplňku plán na portálu Azure Stack správce.](media/create-add-on-plan/1.PNG)

4. Posuňte se do dolní části vlastností nabídky a vyberte možnost **plány doplňku**. Vyberte **Přidat**.

    ![Výběr doplňkových plánů na portálu pro správu Azure Stack](media/create-add-on-plan/2.PNG)

5. Vyberte plán, který chcete přidat. V tomto příkladu se plán nazývá **plán trezoru klíčů**. Po výběru plánu klikněte na **Vybrat** a přidejte plán do nabídky. Měli byste obdržet oznámení, že plán byl úspěšně přidán do nabídky.

    ![Výběr plánu doplňku pro přidání na portál Azure Stack správce](media/create-add-on-plan/3.PNG)

6. Zkontrolujte seznam doplňkových plánů, který je součástí nabídky, a ověřte, že je uvedený nový plán doplňku.

    ![Kontrola seznamu doplňkových plánů na portálu pro správu Azure Stack](media/create-add-on-plan/4.PNG)

::: moniker-end

## <a name="next-steps"></a>Další kroky

* [Vytvoření nabídky](azure-stack-create-offer.md)
