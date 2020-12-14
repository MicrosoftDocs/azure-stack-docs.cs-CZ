---
title: Výměna chladicího ventilátoru
description: Informace o tom, jak vyměnit chladicí ventilátor
author: myoungerman
ms.topic: how-to
ms.date: 11/13/2020
ms.author: v-myoung
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 6138dc5d1ca5b606893daf856a6cf23c525a06c7
ms.sourcegitcommit: 3bd42be22e626564b62e560dc037aed4d462011f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2020
ms.locfileid: "97390755"
---
# <a name="replacing-a-cooling-fan"></a>Výměna chladicího ventilátoru

K nahrazení chladicího ventilátoru nebo ventilátorů použijte následující postup.

## <a name="prerequisites"></a>Předpoklady

1.  *Poznámky, upozornění a upozornění* na začátku tohoto průvodce

2.  Kontrola zpracování opatření.

3.  Opakování

    -   Požadované znalosti pro práci s uzly jednotek škálování v cloudovém zařízení taktické

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
    
3.  Nahraďte ventilátor nebo ventilátory.

    Dodržujte proces nahrazení ventilátoru v [příručce pro instalaci a službu Dell EMC PowerEdge R640](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/dell-emc-poweredge-r640-overview?guid=guid-f39be9ba-158c-45e3-b8b1-f07bb750d6d4) pro uzly jednotek škálování nebo hostitele životního cyklu.
    
4.  Zapněte uzel.

    Pokud se po opětovném připojení k napájení Server nerestartuje automaticky, potom stisknutím tlačítka napájení uzel znovu zapněte.
    
## <a name="next-steps"></a>Další kroky

Pokud pracujete s uzlem jednotky škálování:

1.  Dokončení napájení uzlu jednotky škálování

2.  Dokončilo se ověřování stavu uzlu jednotky škálování. Pokud pracujete s hostitelem životního cyklu hardwaru:

    -   Dokončení ověřování stavu hostitele životního cyklu hardwaru
    
