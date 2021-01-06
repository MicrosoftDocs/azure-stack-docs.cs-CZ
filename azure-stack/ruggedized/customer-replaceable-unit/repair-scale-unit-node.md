---
title: Zapnout a opravit uzel jednotky škálování
description: Zjistěte, jak zapnout a opravit uzel jednotky škálování.
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: d05c696f62aad4d9305137edab777d69b6442814
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2021
ms.locfileid: "97909918"
---
# <a name="powering-on-and-repairing-a-scale-unit-node"></a>Zapnutí a oprava uzlu jednotky škálování

**Kroky**

Chcete-li opravit a přenést uzel jednotek škálování zpátky do produkčního prostředí, je nutné spustit postup opravy centra Azure Stack.

> [!NOTE]
> K dokončení postupu opravy trvá přibližně tři hodiny.

1.  Na **portálu pro správu** vyberte uzel a vyberte **opravit**.

    ![Snímek obrazovky, který zobrazuje stránku pro správu uzlů s uzlem a vybranou akcí opravit](media/image-52.png)

1.  Zadejte **IP adresu řadiče pro správu základní desky** , která odpovídá uzlu, který opravujete, a vyberte **opravit**.

    ![Snímek obrazovky, který zobrazuje stránku pro správu uzlů s vybraným uzlem, adresa I P se zvýrazní a zobrazí se dialogové okno opravit uzel.](media/image-53.png)

1.  Sledujte průběh v podokně oznámení:

    ![Snímek obrazovky, který zobrazuje podokno oznámení a zobrazená možnost opravit běžící uzel.](media/image-54.png)
    
    
    > [!NOTE]
    > Pokud se postup opravy nedokončí za tři hodiny, nebo dojde k problému, otevřete případ s podporou technologií Dell, kteří mohou podpora Microsoftu řešit další řešení potíží.
    
    Po dokončení procesu opravy se uzel vrátí do **běžícího provozního stavu**.
    
