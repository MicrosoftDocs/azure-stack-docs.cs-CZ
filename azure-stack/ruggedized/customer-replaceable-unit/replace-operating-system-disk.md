---
title: Výměna disku s operačním systémem
description: Přečtěte si, jak vyměnit disk s operačním systémem.
author: myoungerman
ms.topic: how-to
ms.date: 11/13/2020
ms.author: v-myoung
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 529a81a8c63e2fe72da4618e7f359832b2267ea3
ms.sourcegitcommit: 3bd42be22e626564b62e560dc037aed4d462011f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2020
ms.locfileid: "97390759"
---
# <a name="replacing-an-operating-system-disk"></a>Výměna disku s operačním systémem

Následující postup použijte k nahrazení neúspěšného disku operačního systému v uzlu jednotky škálování.

## <a name="prerequisites"></a>Předpoklady

1.  *Poznámky, upozornění a upozornění* na začátku tohoto průvodce.

2.  Kontrola zpracování opatření.

3.  V případě, že pracujete s uzlem jednotky škálování, je nutné znát znalosti pro práci s uzly jednotek škálování v taktické cloudovém zařízení.

4.  Dokončila se ověřování přístupu k uzlu jednotky škálování a stavu.

5.  Bylo dokončeno vypínání uzlů jednotek škálování.

    V případě uzlů Azure Stack jednotky škálování centra se operační systém spouští ze zrcadleného páru modulů M. 2 SSD, který je umístěný na kartě pro vycházející úložiště optimalizované pro počítače Dell PowerEdge. Systém musí být vypnutý, aby bylo možné vyměnit disk s operačním systémem.
    
## <a name="steps"></a>Postup

1.  Vyhledejte fyzický uzel v racku.

2.  Ověřte, zda je uzel vypnutý. Indikátor LED napájení by měl být oranžový.

    > [!CAUTION]
    > Před odpojením kabelů na serveru, na kterém pracujete, zajistěte, aby byl každý kabel správně označený. Kabely je potřeba znovu připojit ke stejným portům.
    
3.  Nahraďte neúspěšný modul SSD. 2 SSD.

    Postupujte podle procesu nahrazení modulem M. 2 SSD v [příručce pro instalaci Dell EMC PowerEdge R640 a ruční služby](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/dell-emc-poweredge-r640-overview?guid=guid-f39be9ba-158c-45e3-b8b1-f07bb750d6d4) pro uzly jednotek škálování.
    
4.  Zapněte uzel.

    Pokud se po opětovném připojení k napájení Server nerestartuje automaticky, potom stisknutím tlačítka napájení uzel znovu zapněte.
    
## <a name="next-steps"></a>Další kroky

1.  Dokončete zapnutí a opravte uzel jednotky škálování.

2.  Dokončilo se ověřování stavu uzlu jednotky škálování.

