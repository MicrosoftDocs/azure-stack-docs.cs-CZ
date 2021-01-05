---
title: Ověření přístupu k uzlu jednotky škálování a stavu
description: Zjistěte, jak ověřit přístup a stav uzlu jednotky škálování.
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: c28d58d4416ab15885d687e9f0cbe7b6cdc8bdf8
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97874451"
---
# <a name="verifying-scale-unit-node-access-and-health"></a>Ověření přístupu k uzlu jednotky škálování a stavu



Přihlaste se k pracovní stanici privilegovaného přístupu, spusťte portál pro správu, ověřte stav systému, Získejte IP adresy privilegovaného koncového bodu a určete, které uzly bude nutné vyprázdnit nebo obnovit.

1.  Pomocí vzdálené plochy se připojte k pracovní stanici privilegovaného přístupu.

2.  Přejděte na portál správce centra Azure Stack.

    Přihlaste se k portálu pro správu centra Azure Stack s přihlašovacími údaji získanými od zákazníka.
        
3.  Získejte IP adresy privilegovaného koncového bodu.


    Vyberte dlaždici **Správa oblastí** a vyberte **vlastnosti**. Posuňte se do dolní části podokna a vyhledejte IP adresy v poli **IP adresy privilegovaného koncového bodu** . Poznamenejte si je, jak může být potřeba později v tomto postupu, nebo bude potřeba podpora v případě jakýchkoli problémů.

    [![](media/image-18-inline.png)](media/image-18-expanded.png)
    
4.  Projděte si všechny aktuální výstrahy.

    V části **Správa oblastí** vyberte **výstrahy** a zkontrolujte aktuální výstrahy. Pokud jsou k dispozici nějaké neočekávané výstrahy, ověřte u technologie Dell Technologies podporu, že je lze vymazat nebo bezpečně ignorovat.
    
    [![](media/image-19-inline.png)](media/image-19-expanded.png)
    
5.  Identifikujte uzly jednotek škálování.

    Pokud jste zadali pouze značku služby a nemůžete zjistit, který uzel má problém z portálu pro správu centra Azure Stack (stav napájení uzlu je již zastaven), pak pomocí následujících kroků korelujte uzel jednotky škálování se značkou služby:
    
    1.  V části **Správa oblastí** vyberte **jednotky škálování** a pak vyberte cluster **s** clustery. Vyberte **uzly**.
    
    1.  Chcete-li získat značku služby uzlu, vyberte odkaz IP adresa **řadiče pro správu základní desky** , který otevře webové rozhraní iDRAC serveru na nové kartě nebo v okně.

        [![](media/image-20-inline.png)](media/image-20-expanded.png) 
    
    1.  Přihlaste se k rozhraní iDRAC a ověřte tag služby uzlu v podokně **Systémové informace** .
    
    1.  Tento postup opakujte pro každý uzel a korelujte tyto značky služeb s plánovaným nahrazením hardwaru, abyste zjistili, který uzel nebo uzly je potřeba obsluhovat.

        [![](media/image-21-inline.png)](media/image-21-expanded.png)
    
