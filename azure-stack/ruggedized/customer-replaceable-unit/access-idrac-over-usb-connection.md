---
title: Přístup k iDRAC prostřednictvím připojení USB
description: Přečtěte si, jak přistupovat k iDRAC prostřednictvím připojení USB.
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: ba480afc0c94207b76df5c61b5a4f747636b4ece
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97867906"
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
    
