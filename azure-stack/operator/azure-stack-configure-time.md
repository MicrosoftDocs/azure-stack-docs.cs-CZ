---
title: Konfigurace časového serveru pro centrum Azure Stack | Microsoft Docs
description: Přečtěte si, jak nakonfigurovat časový server pro centrum Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/10/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 10/10/2019
ms.openlocfilehash: bc139b43c2914f14cbe3bf998c20137e7a07dd25
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75882738"
---
# <a name="configure-the-time-server-for-azure-stack-hub"></a>Konfigurace časového serveru pro centrum Azure Stack

Pomocí privilegovaného koncového bodu (PEP) můžete aktualizovat časový server v centru Azure Stack. Použijte název hostitele, který se přeloží na dvě nebo víc IP adres serveru NTP.

Azure Stack hub používá protokol NTP (Network Time Protocol) pro připojení k časovým serverům v Internetu. Servery NTP poskytují přesný systémový čas. Čas se používá v rámci fyzických síťových přepínačů centra Azure Stack, hostitele životního cyklu, služby infrastruktury a virtuálních počítačů. Pokud se hodiny nesynchronizují, Azure Stack centrum může docházet k vážným problémům se sítí a ověřováním. Soubory protokolu, dokumenty a jiné soubory mohou být vytvořeny s nesprávnými časovými razítky.

Pro Azure Stack centra pro synchronizaci času je vyžadován jeden časový server (NTP). Když nasadíte centrum Azure Stack, zadáte adresu serveru NTP. Čas je kritická služba infrastruktury Datacenter. Pokud se služba změní, budete muset čas aktualizovat.

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
    > Tato procedura neaktualizuje časový server na fyzických přepínačích

    ```PowerShell
    Set-AzsTimeSource -TimeServer NEWTIMESERVERIP -resync
    ```

4. Zkontrolujte výstup příkazu pro všechny chyby.


## <a name="next-steps"></a>Další kroky

[Zobrazit sestavu připravenosti](azure-stack-validation-report.md)  
[Obecné pokyny k integraci centra Azure Stack](azure-stack-datacenter-integration.md)  
