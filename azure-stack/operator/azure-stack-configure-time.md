---
title: Konfigurace časového serveru v centru Azure Stack
description: Přečtěte si, jak nakonfigurovat časový server v centru Azure Stack.
author: PatAltimore
ms.topic: article
ms.date: 2/19/2020
ms.author: patricka
ms.reviewer: thoroet
ms.lastreviewed: 10/10/2019
ms.openlocfilehash: e50bb123e7f52c17e5d180d0dd33cc84669882ab
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97871408"
---
# <a name="configure-the-time-server-for-azure-stack-hub"></a>Konfigurace časového serveru pro centrum Azure Stack

Pomocí privilegovaného koncového bodu (PEP) můžete aktualizovat časový server v centru Azure Stack. Použijte název hostitele, který se přeloží na dvě nebo víc IP adres serveru NTP (Network Time Protocol).

Azure Stack hub používá NTP pro připojení k časovým serverům v Internetu. Servery NTP poskytují přesný systémový čas. Čas se používá v rámci fyzických síťových přepínačů centra Azure Stack, hostitele životního cyklu, služby infrastruktury a virtuálních počítačů. Pokud se hodiny nesynchronizují, Azure Stack centrum může docházet k vážným problémům se sítí a ověřováním. Soubory protokolu, dokumenty a jiné soubory mohou být vytvořeny s nesprávnými časovými razítky.

Pro Azure Stack centra pro synchronizaci času je vyžadován jeden časový server (NTP). Když nasadíte centrum Azure Stack, zadáte adresu serveru NTP. Čas je kritická služba infrastruktury Datacenter. Pokud se služba změní, je nutné čas aktualizovat.

> [!NOTE]
> Centrum Azure Stack podporuje synchronizaci s časem jenom s jedním časovým serverem (NTP). Nemůžete poskytnout více NTPs pro centrum Azure Stack k synchronizaci času s.

## <a name="configure-time"></a>Konfigurovat čas

1. [Připojte se k PEP](azure-stack-privileged-endpoint.md).
    > [!Note]  
    > K odemknutí privilegovaného koncového bodu není nutné otevřít lístek podpory.

2. Spusťte následující příkaz a zkontrolujte aktuální nakonfigurovaný server NTP:

    ```PowerShell
    Get-AzsTimeSource
    ```

3. Spuštěním následujícího příkazu aktualizujte centrum Azure Stack tak, aby používalo nový server NTP, a okamžitě synchronizujte čas.

    > [!Note]  
    > Tato procedura neaktualizuje časový server na fyzických přepínačích. Pokud Váš časový server není serverem NTP se systémem Windows, je nutné přidat příznak `0x8` .

    ```PowerShell
    Set-AzsTimeSource -TimeServer NEWTIMESERVERIP -resync
    ```

    Pro jiné servery než pro časové servery založené na systému Windows:

    ```PowerShell
    Set-AzsTimeSource -TimeServer "NEWTIMESERVERIP,0x8" -resync
    ```

4. Zkontrolujte výstup příkazu pro všechny chyby.


## <a name="next-steps"></a>Další kroky

[Zobrazit sestavu připravenosti](azure-stack-validation-report.md)  
[Obecné pokyny k integraci centra Azure Stack](azure-stack-datacenter-integration.md)  
