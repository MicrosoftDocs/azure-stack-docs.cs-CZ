---
title: Aktualizace služby DNS pro přeposílání v centru Azure Stack
description: Naučte se aktualizovat službu DNS pro přeposílání v centru Azure Stack.
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: inhenkel
ms.reviewer: thoroet
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: ede13e98893fcf30c0e1640d65271554410f659a
ms.sourcegitcommit: 70c344b3c9c63f8c12867b2cdfdd1794fcc518dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/05/2020
ms.locfileid: "82836083"
---
# <a name="update-the-dns-forwarder-in-azure-stack-hub"></a>Aktualizace služby DNS pro přeposílání v centru Azure Stack

Aby mohla infrastruktura centra Azure Stack přeložit externí názvy, musí mít aspoň jeden dostupný dosažitelný server DNS. Pro nasazení centra Azure Stack musí být k dispozici služba DNS pro přeposílání. Tento vstup se používá pro interní servery DNS centra Azure Stack jako službu pro dodávání a umožňuje překlad externích IP adres pro služby, jako je ověřování, Správa Marketplace nebo využití.

DNS je kritická služba infrastruktury Datacenter, která se může změnit. V takovém případě musí být centrum Azure Stack aktualizované.

Tento článek popisuje použití privilegovaného koncového bodu (PEP) k aktualizaci služby DNS pro přeposílání v centru Azure Stack. Doporučuje se používat dvě IP adresy spolehlivého serveru DNS.

## <a name="steps-to-update-the-dns-forwarder"></a>Postup aktualizace služby DNS pro přeposílání

1. Připojte se k [privilegovanému koncovému bodu](azure-stack-privileged-endpoint.md). Otevřením lístku podpory není nutné odemknout privilegovaný koncový bod.

2. Spuštěním následujícího příkazu zkontrolujte aktuálně nakonfigurované servery DNS pro přeposílání. Alternativně můžete také použít vlastnosti oblasti portálu pro správu:

   ```powershell
   Get-AzsDnsForwarder
   ```

3. Spuštěním následujícího příkazu aktualizujte centrum Azure Stack tak, aby používalo nové služby DNS pro přeposílání:

   ```powershell
    Set-AzsDnsForwarder -IPAddress "IPAddress 1","IPAddress 2"
   ```

4. Zkontrolujte výstup příkazu pro všechny chyby.

## <a name="next-steps"></a>Další kroky

[Integrace brány firewall](azure-stack-firewall.md)
