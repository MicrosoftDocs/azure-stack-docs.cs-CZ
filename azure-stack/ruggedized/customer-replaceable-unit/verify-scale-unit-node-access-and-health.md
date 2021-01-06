---
title: Ověření přístupu k uzlu jednotky škálování a stavu
description: Zjistěte, jak ověřit přístup a stav uzlu jednotky škálování.
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 74addf295c35099e90e3a7fe4fd95aad34e47361
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2021
ms.locfileid: "97910631"
---
# <a name="verifying-scale-unit-node-access-and-health"></a>Ověření přístupu k uzlu jednotky škálování a stavu



Přihlaste se k pracovní stanici privilegovaného přístupu, spusťte portál pro správu, ověřte stav systému, Získejte IP adresy privilegovaného koncového bodu a určete, které uzly bude nutné vyprázdnit nebo obnovit.

1.  Pomocí vzdálené plochy se připojte k pracovní stanici privilegovaného přístupu.

2.  Přejděte na portál správce centra Azure Stack.

    Přihlaste se k portálu pro správu centra Azure Stack s přihlašovacími údaji získanými od zákazníka.
        
3.  Získejte IP adresy privilegovaného koncového bodu.


    Vyberte dlaždici **Správa oblastí** a vyberte **vlastnosti**. Posuňte se do dolní části podokna a vyhledejte IP adresy v poli **IP adresy privilegovaného koncového bodu** . Poznamenejte si je, jak může být potřeba později v tomto postupu, nebo bude potřeba podpora v případě jakýchkoli problémů.

    [![Snímek obrazovky zobrazující stránku pro správu s zvýrazněným oddílem privilegovaného koncového bodu I P adresy.](media/image-18-inline.png)](media/image-18-expanded.png)
    
4.  Projděte si všechny aktuální výstrahy.

    V části **Správa oblastí** vyberte **výstrahy** a zkontrolujte aktuální výstrahy. Pokud jsou k dispozici nějaké neočekávané výstrahy, ověřte u technologie Dell Technologies podporu, že je lze vymazat nebo bezpečně ignorovat.
    
    [ ![ Snímek obrazovky zobrazující stránku vlastnosti s zvýrazněným oddílem název](media/image-19-inline.png)](media/image-19-expanded.png)]
    
5.  Identifikujte uzly jednotek škálování.

    Pokud jste zadali pouze značku služby a nemůžete zjistit, který uzel má problém z portálu pro správu centra Azure Stack (stav napájení uzlu je již zastaven), pak pomocí následujících kroků korelujte uzel jednotky škálování se značkou služby:
    
    1.  V části **Správa oblastí** vyberte **jednotky škálování** a pak vyberte cluster **s** clustery. Vyberte **uzly**.
    
    1.  Chcete-li získat značku služby uzlu, vyberte odkaz IP adresa **řadiče pro správu základní desky** , který otevře webové rozhraní iDRAC serveru na nové kartě nebo v okně.

        [![Snímek obrazovky zobrazující stránku Nodes se zvýrazněným sloupcem BMC](media/image-20-inline.png)](media/image-20-expanded.png) 
    
    1.  Přihlaste se k rozhraní iDRAC a ověřte tag služby uzlu v podokně **Systémové informace** .
    
    1.  Tento postup opakujte pro každý uzel a korelujte tyto značky služeb s plánovaným nahrazením hardwaru, abyste zjistili, který uzel nebo uzly je potřeba obsluhovat.

        [![Snímek obrazovky, který zobrazuje "řídicí panel" se zvýrazněnou značkou služby](media/image-21-inline.png)](media/image-21-expanded.png)
    
