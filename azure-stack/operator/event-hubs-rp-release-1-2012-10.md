---
title: Event Hubs ve zprávách k vydání verze centra Azure Stack 1.2012.1.0
description: Přečtěte si o 1.2012.1.0 vydání pro Event Hubs v centru Azure Stack, včetně oprav chyb, funkcí a způsobu instalace aktualizace.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: article
ms.date: 02/15/2021
ms.reviewer: kalkeea
ms.lastreviewed: 02/15/2021
ms.openlocfilehash: ef28447b87c0647b65c996112c5739bf7c6caccf
ms.sourcegitcommit: 34babe5abf1bceee718011b5c5c25f75e1b03b0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100563494"
---
# <a name="event-hubs-on-azure-stack-hub-1201210-release-notes"></a>Event Hubs ve zprávách k vydání verze centra Azure Stack 1.2012.1.0

Tyto poznámky k verzi popisují vylepšení a opravy v Event Hubs v centru Azure Stack Center verze 1.2012.1.0 a jakékoli známé problémy. 

[!INCLUDE [Azure Stack Hub update reminder](../includes/event-hubs-hub-update-banner.md)]

## <a name="updates-in-this-release"></a>Aktualizace v této verzi

Tato verze zahrnuje následující aktualizace:

- Pro vývojáře Azure Portal SDK se teď podporuje portál verze 5.0.303.7361.
- Vylepšení interního protokolování pro Event Hubs clustery.

## <a name="issues-fixed-in-this-release"></a>Chyby opravené v této verzi

Tato verze zahrnuje následující opravy:

- Oprava pořadí upgradu pro clustery Event Hubs pro vyřešení problému s upgradem.
- V případě, že clustery byly ve stavu "upgrade" nebo "upgrade se nezdařilo", nebyly spuštěny clustery stavů a zálohování pro Event Hubs clustery. V této verzi byl problém vyřešen.
- Opravila se chyba způsobující, že záznamy o využití obsahují špatné množství. Místo jader byly vygenerovány jednotky kapacity (CU). V minulosti by cluster 1CU ve hodinovém používání zobrazoval 1 jádro. Uživatelům se teď v průběhu hodinového používání zobrazí správné množství 10 jader pro cluster 1 CU.

## <a name="known-issues"></a>Známé problémy 

Pro tuto verzi neexistují žádné známé problémy.

## <a name="next-steps"></a>Další kroky

- Pokud chcete získat další informace, začněte [Event Hubs v článku Přehled operátora centra Azure Stack](event-hubs-rp-overview.md).

