---
title: Aktualizace služby DNS pro přeposílání v Azure Stack | Microsoft Docs
description: Naučte se aktualizovat službu DNS pro přeposílání v Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: sethm
ms.reviewer: thoroet
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: 22e49f28dee6b4aa97b9e84cf52950dd678450e4
ms.sourcegitcommit: cefba8d6a93efaedff303d3c605b02bd28996c5d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74308199"
---
# <a name="update-the-dns-forwarder-in-azure-stack"></a>Aktualizace služby DNS pro přeposílání v Azure Stack

Aby mohla infrastruktura Azure Stack přeložit externí názvy, musí mít aspoň jeden dosažitelný server DNS. Pro nasazení Azure Stack musí být k dispozici předávací služba DNS. Tento vstup se používá pro Azure Stack interní servery DNS jako servery pro přeposílání a umožňuje překlad externích IP adres pro služby, jako je ověřování, Správa Marketplace nebo využití.

DNS je kritická služba infrastruktury Datacenter, která se může změnit, a pokud k tomu dojde, Azure Stack nutné aktualizovat.

Tento článek popisuje použití privilegovaného koncového bodu (PEP) k aktualizaci služby DNS pro přeposílání v Azure Stack. Doporučuje se používat dvě IP adresy spolehlivého serveru DNS.

1. Připojte se k [privilegovanému koncovému bodu](azure-stack-privileged-endpoint.md). Všimněte si, že není nutné odemknout privilegovaný koncový bod otevřením lístku podpory.

2. Spuštěním následujícího příkazu zkontrolujte aktuálně nakonfigurované servery DNS pro přeposílání. Alternativně můžete použít také vlastnosti oblasti portálu pro správu:

   ```powershell
   Get-AzsDnsForwarder
   ```

3. Spuštěním následujícího příkazu aktualizujte Azure Stack pro použití nového serveru DNS pro přeposílání:

   ```powershell
    Set-AzsDnsForwarder -IPAddress "IPAddress 1","IPAddress 2"
   ```

4. Zkontrolujte výstup příkazu pro všechny chyby.

## <a name="next-steps"></a>Další kroky

[Integrace brány firewall](azure-stack-firewall.md)
