---
title: Výměna datové jednotky, která selhala
description: Informace o tom, jak nahradit datovou jednotku, která selhala
author: PatAltimore
ms.topic: how-to
ms.date: 02/05/2021
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: a5b7796794a0444a434fdb9a628ff04b93adcc03
ms.sourcegitcommit: 5ea0e915f24c8bcddbcaf8268e3c963aa8877c9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100488101"
---
# <a name="replacing-a-failed-data-drive"></a>Výměna datové jednotky, která selhala

K nahrazení datové jednotky, která selhala, použijte následující postup.

## <a name="prerequisites"></a>Požadavky

1.  *Poznámky, upozornění a upozornění* na začátku tohoto průvodce

2.  Kontrola zpracování opatření.

3.  Opakování

    -   Povinné znalosti pro práci s uzly jednotek škálování v robustním cloudovém zařízení, pokud pracujete s uzlem jednotky škálování

    -   Požadované znalosti pro práci s hostitelem životního cyklu hardwaru, pokud pracujete s hostitelem životního cyklu hardwaru

4.  Dokončit

    -   Ověřování přístupu k uzlu jednotky škálování a stavu při práci s uzlem jednotky škálování

    -   Ověření přístupu a stavu hostitele životního cyklu životnosti při práci s hostitelem životního cyklu životní cyklus hardwaru

5.  Dokončit

    -   Vypínání uzlů jednotek škálování, pokud pracujete s uzlem jednotky škálování

    -   Vypnutí hostitele životního cyklu životního cyklu, pokud pracujete s hostitelem životního cyklu životní cyklus hardwaru

## <a name="steps"></a>Postup

1.  Vyhledejte fyzický uzel v racku.

2.  Ověřte model disku.

    Ověřte, že je nový diskový model uvedený v [matici podpory](https://www.dell.com/support/home/product-support/product/cloud-for-microsoft-azure-stack14g/docs#q%3Dsupport%20matrix%26sort%3Ddate%20descending%26f%3Alang%3D%5Ben%5D)jako podporovaný.
    Pokud disk není v matrici podpory, musíte požádat o další náhradu.
    
    > [!CAUTION]
    > Vložení disku, který není v maticové matrici, způsobí, že se nový disk zaznamená do karantény.
        
    Chcete-li zkontrolovat model, vyhledejte popisek a ověřte, zda je komponenta v matici podpory.
    
3.  Nahraďte datovou jednotku, která selhala.

    Dodržujte [proces nahrazení nosné jednotky](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/dell-emc-poweredge-r640-overview?guid=guid-f39be9ba-158c-45e3-b8b1-f07bb750d6d4) u uzlů jednotek škálování nebo hostitelů životního cyklu hardwaru.
    
    Také je nutné Přečtěte si relevantní části týkající se odebrání diskové jednotky z a instalace diskové jednotky do operátoru diskové jednotky nebo adaptéru diskové jednotky.
    
4.  Po nahrazení disku ověřte, že je firmware podporovaný v souladu s maticí podpory. Chcete-li načíst \' verzi firmwaru disku, postupujte následovně:

    1.  Přihlaste se k iDRAC pomocí adresy IP nebo přímého přístupu k rozhraní iDRAC přes přímé připojení USB.

    1.  V horní nabídce vyberte **úložiště**:

        ![Snímek obrazovky zobrazující stránku iDRAC s vybranou akcí úložiště](media/image-30.png)
    
    1.  Vyhledejte nahrazený disk a rozbalte položku. Porovnejte **revizi** disku s revizí v matici podpory. Pokud disk není v matrici podpory, popojte ho a pokuste se problém vyřešit.

        ![Snímek obrazovky, který zobrazuje stránku iDRAC ' Pokročilé vlastnosti ' se zvýrazněným ' výrobcem ', ' ID produktu ' a ' revize '.](media/image-31.png)
        
## <a name="next-steps"></a>Další kroky

Pokud pracujete s uzlem jednotky škálování:

1.  Dokončení ověřování stavu uzlu jednotky škálování

2.  Při práci s hostitelem životního cyklu hardwaru dokončete ověření stavu disku uzlu jednotky škálování:

    -   Dokončení ověřování stavu hostitele životního cyklu hardwaru
    
