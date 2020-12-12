---
title: Postup odebrání Event Hubs v centru Azure Stack
description: Naučte se, jak odebrat poskytovatele prostředků Event Hubs v centru Azure Stack.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 12/09/2019
ms.reviewer: jfggdl
ms.lastreviewed: 12/09/2019
ms.openlocfilehash: b69d5b7a7b621d22745c89c18a3e7f000e54bcbd
ms.sourcegitcommit: f56a5b287c90b2081ae111385c8b7833931d4059
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2020
ms.locfileid: "97343741"
---
# <a name="how-to-remove-event-hubs-on-azure-stack-hub"></a>Postup odebrání Event Hubs v centru Azure Stack

> [!WARNING]
> Po odinstalování Event Hubs se odebere (smaže) poskytovatel prostředků a všechny prostředky Event Hubs clustery, obory názvů a centra událostí vytvořené uživatelem. Tím se odeberou také přidružená data události.  
> Před rozhodnutím o odinstalaci Event Hubs prosím pokračujte s mimořádnou opatrností. Odinstalováním **Event Hubs** neodstraníte balíčky používané pro instalaci Event Hubs. Chcete-li to dosáhnout, přečtěte si prosím, jak [odstranit Event Hubs balíčky](#delete-event-hubs-packages).

## <a name="uninstall-event-hubs"></a>Odinstalace Event Hubs

Tato posloupnost kroků odstraní všechny prostředky Event Hubs, včetně clusterů, oborů názvů, Center událostí a poskytovatele prostředků.

Chcete-li odebrat Event Hubs a všechny související prostředky vytvořené uživateli, proveďte následující kroky:

1. Přihlaste se k portálu pro správu služby Azure Stack Hub.
2. Na levé straně vyberte **Správa Marketplace** .
3. Vyberte **Poskytovatelé prostředků**.
4. V seznamu poskytovatelů prostředků vyberte **Event Hubs** . Seznam můžete chtít filtrovat zadáním příkazu "Event Hubs" do zadaného textového pole hledání.

   [![Odebrat centra událostí 1](media/event-hubs-rp-remove/1-uninstall.png)](media/event-hubs-rp-remove/1-uninstall.png#lightbox)

5. Z možností uvedených v horní části stránky vyberte **odinstalovat** .

   [![Odebrat centra událostí 2](media/event-hubs-rp-remove/2-uninstall.png)](media/event-hubs-rp-remove/2-uninstall.png#lightbox)

6. Zadejte název poskytovatele prostředků a pak vyberte **odinstalovat**. Tato akce potvrdí, že vaše přání k odinstalaci:
   - Poskytovatel prostředků Event Hubs
   - Všechny uživatelem vytvořené clustery, obory názvů, centra událostí a data událostí.

   [![Odebrat centra událostí 3](media/event-hubs-rp-remove/3-uninstall.png)](media/event-hubs-rp-remove/3-uninstall.png#lightbox)

   [![Odebírají se centra událostí 4.](media/event-hubs-rp-remove/4-uninstall.png)](media/event-hubs-rp-remove/4-uninstall.png#lightbox)

   > [!IMPORTANT]
   > Před opětovnou instalací Event Hubs je nutné počkat alespoň 10 minut, než se Event Hubs úspěšně odebrala. Důvodem je skutečnost, že stále můžou běžet aktivity vyčištění, což může být v konfliktu s jakoukoli novou instalací.

## <a name="delete-event-hubs-packages"></a>Odstranit balíčky Event Hubs

Tuto možnost použijte, pokud po odinstalování Event Hubs chcete odebrat i balíčky používané k instalaci Event Hubs. 

## <a name="next-steps"></a>Další kroky

Pokud ho chcete znovu nainstalovat, vraťte se do článku [instalace Event Hubs poskytovatele prostředků](event-hubs-rp-install.md) .