---
title: Výměna síťové dceřiné karty
description: Informace o tom, jak nahradit kartu síťovou dceřinou síť
author: PatAltimore
ms.topic: how-to
ms.date: 02/05/2021
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 2d63631cf1c5300c617f3a7164d1681d2edd7bc4
ms.sourcegitcommit: 5ea0e915f24c8bcddbcaf8268e3c963aa8877c9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100487897"
---
# <a name="replacing-a-network-daughter-card"></a>Výměna síťové dceřiné karty

K nahrazení síťové dceřiné karty nebo karet použijte následující postup.

## <a name="prerequisites"></a>Požadavky

1.  *Poznámky, upozornění a upozornění* na začátku tohoto průvodce

2.  Kontrola zpracování opatření.

3.  Opakování

    -   Požadované znalosti pro práci s uzly jednotky škálování v Azure Stackovém centru jsou robustní

    -   Požaduje se znalost pro práci s hostitelem životního cyklu hardwaru na stránce 5, pokud pracujete s hostitelem životního cyklu hardwaru.

4.  Dokončit

    -   Ověřování přístupu k uzlu jednotky škálování a stavu při práci s uzlem jednotky škálování

    -   Ověření přístupu a stavu hostitele životního cyklu životnosti při práci s hostitelem životního cyklu životní cyklus hardwaru

5.  Dokončit

    -   Vypínání uzlů jednotek škálování, pokud pracujete s uzlem jednotky škálování

    -   Vypnutí hostitele životního cyklu životního cyklu, pokud pracujete s hostitelem životního cyklu životní cyklus hardwaru


## <a name="steps"></a>Postup

1.  Vyhledejte fyzický uzel v racku.

2.  Ověřte, zda je uzel vypnutý. Indikátor LED napájení by měl být oranžový.

    > [!CAUTION]
    > Před odpojením kabelů na serveru, na kterém pracujete, zajistěte, aby byl každý kabel správně označený. Kabely je potřeba znovu připojit ke stejným portům.
    
3.  Nahraďte síťové dceřiné karty nebo karty.

    Použijte [proces nahrazení síťové dceřiné karty sítě](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/dell-emc-poweredge-r640-overview?guid=guid-f39be9ba-158c-45e3-b8b1-f07bb750d6d4) pro uzly jednotek škálování nebo hostitele životního cyklu.
    
4.  Zapněte uzel.

    Pokud se po opětovném připojení k napájení Server nerestartuje automaticky, klikněte na tlačítko napájení a zapněte uzel znovu.
    
## <a name="next-steps"></a>Další kroky

Pokud pracujete s uzlem jednotky škálování:

1.  Dokončete zapnutí a opravte uzel jednotky škálování.

2.  Dokončilo se ověřování stavu uzlu jednotky škálování. Pokud pracujete s hostitelem životního cyklu hardwaru:

    -   Dokončení ověřování stavu hostitele životního cyklu hardwaru
    
