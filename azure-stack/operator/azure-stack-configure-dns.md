---
title: Aktualizace služby DNS pro přeposílání v centru Azure Stack
description: Naučte se aktualizovat službu DNS pro přeposílání v centru Azure Stack.
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: inhenkel
ms.reviewer: thoroet
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: 6d5138868b07e2a3d552cd154976352266941760
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77704467"
---
# <a name="update-the-dns-forwarder-in-azure-stack-hub"></a>Aktualizace služby DNS pro přeposílání v centru Azure Stack

Aby mohla infrastruktura centra Azure Stack přeložit externí názvy, musí mít aspoň jeden dostupný dosažitelný server DNS. Pro nasazení centra Azure Stack musí být k dispozici služba DNS pro přeposílání. Tento vstup se používá pro interní servery DNS centra Azure Stack jako službu pro směrování a umožňuje překlad externích IP adres pro služby, jako je ověřování, Správa Marketplace nebo využití.

DNS je kritická služba infrastruktury Datacenter, která se může změnit, a pokud k tomu dojde, Azure Stack centrum se musí aktualizovat.

Tento článek popisuje použití privilegovaného koncového bodu (PEP) k aktualizaci služby DNS pro přeposílání v centru Azure Stack. Doporučuje se používat dvě IP adresy spolehlivého serveru DNS.

1. Připojte se k [privilegovanému koncovému bodu](azure-stack-privileged-endpoint.md). Všimněte si, že není nutné odemknout privilegovaný koncový bod otevřením lístku podpory.

2. Spuštěním následujícího příkazu zkontrolujte aktuálně nakonfigurované servery DNS pro přeposílání. Alternativně můžete použít také vlastnosti oblasti portálu pro správu:

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
