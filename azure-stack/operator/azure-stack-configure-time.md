---
title: Nakonfigurovat časový server pro Azure Stack | Microsoft Docs
description: Přečtěte si, jak nakonfigurovat časový server pro Azure Stack.
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
ms.openlocfilehash: cc432538715c1c990a9efe6473b33303deb78734
ms.sourcegitcommit: a6d47164c13f651c54ea0986d825e637e1f77018
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72280531"
---
# <a name="configure-the-time-server-for-azure-stack"></a>Konfigurace časového serveru pro Azure Stack

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*  

Pomocí privilegovaného koncového bodu (PEP) můžete aktualizovat časový server v Azure Stack. Použijte název hostitele, který se přeloží na dvě nebo víc IP adres serveru NTP.

Azure Stack používá protokol NTP (Network Time Protocol) pro připojení k časovým serverům v Internetu. Servery NTP poskytují přesný systémový čas. Čas se používá v rámci fyzických síťových přepínačů Azure Stack, hostitele životního cyklu, služby infrastruktury a virtuálních počítačů. Pokud hodiny nejsou synchronizované, může Azure Stack dojít k vážným problémům se sítí a ověřováním. Soubory protokolu, dokumenty a jiné soubory mohou být vytvořeny s nesprávnými časovými razítky.

Azure Stack k synchronizaci času je nutný alespoň jeden časový server (NTP). Když nasadíte Azure Stack, zadáte adresu serveru NTP. Čas je kritická služba infrastruktury Datacenter. Pokud se služba změní, budete muset čas aktualizovat.

## <a name="configure-time"></a>Konfigurovat čas

1. [Připojte se k PEP](azure-stack-privileged-endpoint.md). 
    > [!Note]  
    > K odemknutí privilegovaného koncového bodu není nutné otevřít lístek podpory.

2. Spusťte následující příkaz a zkontrolujte aktuální nakonfigurovaný server NTP:

    ```PowerShell
    Get-AzsTimeSource
    ```

3. Spuštěním následujícího příkazu aktualizujte Azure Stack na použití nového serveru NTP a okamžitě synchronizujte čas.

    > [!Note]  
    > Tato procedura neaktualizuje časový server na fyzických přepínačích

    ```PowerShell
    Set-AzsTimeSource -TimeServer NEWTIMESERVERIP -resync
    ```

4. Zkontrolujte výstup příkazu pro všechny chyby.


## <a name="next-steps"></a>Další kroky

[Zobrazit sestavu připravenosti](azure-stack-validation-report.md)  
[Obecné pokyny k integraci Azure Stack](azure-stack-datacenter-integration.md)  
