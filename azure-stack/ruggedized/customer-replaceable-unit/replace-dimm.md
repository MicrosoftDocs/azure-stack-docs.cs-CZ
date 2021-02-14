---
title: Výměna DIMM
description: Informace o tom, jak nahradit DIMM
author: PatAltimore
ms.topic: how-to
ms.date: 02/05/2021
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: f20cdf77cb5ed3b63bcbd9b57770039efb6d29e5
ms.sourcegitcommit: 5ea0e915f24c8bcddbcaf8268e3c963aa8877c9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100487931"
---
# <a name="replacing-a-dimm"></a>Výměna DIMM

K nahrazení modulu DIMM použijte následující postup.

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

2.  Ověřte, zda je uzel vypnutý. Indikátor LED napájení by měl být oranžový.

    > [!CAUTION]
    > Před odpojením kabelů na serveru, na kterém pracujete, zajistěte, aby byl každý kabel správně označený. Kabely je potřeba znovu připojit ke stejným portům.
    
3.  Nahraďte moduly DIMM a DIMM.

    Dodržujte [proces nahrazení modulu paměti](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/dell-emc-poweredge-r640-overview?guid=guid-f39be9ba-158c-45e3-b8b1-f07bb750d6d4) pro uzly jednotek škálování nebo hostitele životního cyklu.
    
4.  Zapněte uzel.

    Pokud se po opětovném připojení k napájení Server nerestartuje automaticky, potom stisknutím tlačítka napájení uzel znovu zapněte.
    
## <a name="next-steps"></a>Další kroky

Pokud pracujete s uzlem jednotky škálování:

1.  Dokončete napájení uzlu jednotky škálování.

2.  Dokončilo se ověřování stavu uzlu jednotky škálování. Pokud pracujete s hostitelem životního cyklu hardwaru, dokončete ověření stavu hostitele životního cyklu hardwaru.
    
