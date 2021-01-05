---
title: Výměna neúspěšného disku externího úložiště
description: Informace o tom, jak nahradit neúspěšný disk externího úložiště
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 476801acccecb463e97d42f38067b2463cfbd86c
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97874638"
---
# <a name="replacing-a-failed-external-storage-disk"></a>Výměna neúspěšného disku externího úložiště

K nahrazení neúspěšného externího disku použijte následující postup.

## <a name="prerequisites"></a>Požadavky

1.  *Poznámky, upozornění a upozornění* na začátku tohoto průvodce

2.  Kontrola zpracování opatření.

3.  Opakování

    -   Požadované znalosti pro práci s uzly jednotek škálování v cloudovém zařízení taktické, pokud pracujete s uzlem jednotky škálování

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

    Ověřte, jestli je nový diskový model uvedený jako podporovaný v [integrovaném systému pro 14Gou podporu Microsoft Azure Stack pro centra pro podporu](https://www.dell.com/support/home/product-support/product/cloud-for-microsoft-azure-stack14g/docs#q%3Dsupport%20matrix%26sort%3Ddate%20descending%26f%3Alang%3D%5Ben%5D) 
     [](https://www.dell.com/support/home/product-support/product/cloud-for-microsoft-azure-stack14g/docs#q%3Dsupport%20matrix%26sort%3Ddate%20descending%26f%3Alang%3D%5Ben%5D).
    Pokud disk není v matrici podpory, musíte požádat o další náhradu.
    
    > [!CAUTION]
    > Vložení disku, který není v maticové matrici, způsobí, že se nový disk zaznamená do karantény.
        
    Chcete-li zkontrolovat model, vyhledejte popisek a ověřte, zda je komponenta v matici podpory.
    
3.  Nahraďte neúspěšný disk externího úložiště.

    Postupujte podle procesu nahrazení nosné jednotky na diskové jednotce v [příručce pro instalaci Dell EMC PowerEdge R640 a ruční služby](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/dell-emc-poweredge-r640-overview?guid=guid-f39be9ba-158c-45e3-b8b1-f07bb750d6d4) pro uzly jednotek škálování nebo hostitele životního cyklu hardwarových zařízení.
    
    Také je nutné Přečtěte si relevantní části týkající se odebrání diskové jednotky z a instalace diskové jednotky do operátoru diskové jednotky nebo adaptéru diskové jednotky.
    
4.  Po nahrazení disku ověřte, že je firmware podporovaný v souladu s maticí podpory. Chcete-li načíst \' verzi firmwaru disku, postupujte následovně:

    1.  Přihlaste se k iDRAC pomocí adresy IP nebo přímého přístupu k rozhraní iDRAC přes přímé připojení USB.

    1.  V horní nabídce vyberte **úložiště**:

        ![](media/image-30.png)
    
    1.  Vyhledejte nahrazený disk a rozbalte položku. Porovnejte **revizi** disku s revizí v matici podpory. Pokud disk není v matrici podpory, popojte ho a pokuste se problém vyřešit.

        ![](media/image-31.png)
        
## <a name="next-steps"></a>Další kroky

Pokud pracujete s uzlem jednotky škálování:

1.  Dokončení ověřování stavu uzlu jednotky škálování

2.  Při práci s hostitelem životního cyklu hardwaru dokončete ověření stavu disku uzlu jednotky škálování:

    -   Dokončení ověřování stavu hostitele životního cyklu hardwaru
    
