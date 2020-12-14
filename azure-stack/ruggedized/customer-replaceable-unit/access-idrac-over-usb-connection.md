---
title: Přístup k iDRAC prostřednictvím připojení USB
description: Přečtěte si, jak přistupovat k iDRAC prostřednictvím připojení USB.
author: myoungerman
ms.topic: how-to
ms.date: 11/13/2020
ms.author: v-myoung
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 7381aae6b7b08de01e27f895d79519a024a1955a
ms.sourcegitcommit: 3bd42be22e626564b62e560dc037aed4d462011f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2020
ms.locfileid: "97391490"
---
# <a name="accessing-the-idrac-interface-over-a-direct-usb-connection"></a>Přístup k rozhraní iDRAC přes přímé připojení USB

Funkce iDRAC Direct vám umožní přímo připojit svůj notebook k portu USB iDRAC. Tato funkce vám umožní komunikovat přímo s iDRACmi rozhraními, jako jsou webové rozhraní, RACADM a WSMan pro pokročilou správu a údržbu serverů.



Pokud chcete získat přístup k rozhraní iDRAC přes port USB, proveďte následující z přenosného počítače.

**Kroky**

1.  Z přenosného počítače vypněte všechny bezdrátové sítě a odpojte se od ostatních pevných sítí.

2.  Připojte kabel mikrosběrnice USB od přenosného počítače k iDRAC přímému portu umístěnému na začátku serveru.
    V diagramu se podívejte na položku 4.

    ![](media/image-67.png)

3.  Počkejte, až přenosný počítač získá IP adresu 169.254.0.4.

    Získání IP adres může trvat několik sekund. IDRAC získá IP adresu 169.254.0.3.

4.  Připojte se k webovému rozhraní iDRAC.

    Pro přístup k webovému rozhraní iDRAC otevřete prohlížeč a přejděte na 169.254.0.3.

5.  Dokončete požadované aktivity.

    

    > [!NOTE]
    > Když iDRAC používá port USB, což je položka 3 ve výše uvedeném diagramu, indikátor LED zaznamená aktivitu. Frekvence blikání je 4 za sekundu.
    
6.  Odpojte kabel Micro USB.

    Po dokončení požadovaných akcí odpojte kabel USB od systému. Indikátor LED se vypne.
    
