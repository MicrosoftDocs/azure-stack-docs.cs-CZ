---
title: MDC Správa životního cyklu konfigurace sítě
description: Přečtěte si o správě životního cyklu konfigurace sítě pro modulární datacentrum Azure.
author: justinha
ms.author: justinha
ms.service: azure-stack
ms.topic: conceptual
ms.date: 01/02/2020
ms.lastreviewed: 01/02/2020
ms.openlocfilehash: b88511f47064f0ac55571c81924cca13eb65e9f6
ms.sourcegitcommit: 25b234330df4e753ed2dd480c208ec88cd90234c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2020
ms.locfileid: "94924346"
---
# <a name="configuration-lifecycle-management"></a>Správa životního cyklu konfigurace

Tento článek popisuje správu životního cyklu konfigurace sítě pro modulární datacentrum Azure.

## <a name="update-configuration"></a>Aktualizovat konfiguraci

Tato metodika aktualizace konfigurací přepínačů v poli se vztahuje na všechny a všechny typy aktualizací konfigurace. Z důvodu mnoha neznámých proměnných mimo náš ovládací prvek, jako jsou ruční konfigurace uplatňované zákazníky nebo výrobcem OEM, má tento ruční proces více kroků. V tuto chvíli není nijak zajištěna doba provozu. Aktualizace by se měly provádět během časového období údržby.

1. Vytvořte zálohu aktuálních konfiguračních souborů pro přepínače EDGE1, EDGE2, tor1, tor2 a BMC. Zkopírujte tyto soubory mimo přepínač.

1. Poznamenejte si číslo sestavení sady nástrojů existující konfigurace. Všechny konfigurace by měly mít toto číslo v hlavičce **motd** . Vyhledejte **BUILDNUMBER**.

1. Obnovte počáteční konfiguraci pomocí stejné verze sady nástrojů z kroku 2.

1. Načtěte konfigurace z kroku 3 na příslušné přepínače. Bodem této akce je *promytí* konfigurace vygenerované našimi nástroji prostřednictvím přepínače pro získání směrného plánu. Tuto akci může provést výrobce OEM na samostatném hardwaru, jako je třeba v laboratoři výrobce OEM nebo v lokalitě s zákazníkem.

1. Vytvořte zálohu konfiguračního souboru z kroku 4. Zkopírujte ho do vzdáleného umístění.

1. Pomocí nástroje diff podle vašeho výběru Porovnejte zálohu aktuální konfigurace z kroku 1 až po zálohu konfigurace standardních hodnot z kroku 5. Udělejte si poznámku nebo kopii všech rozdílů, které by se měly přenést do upgradované konfigurace přepínače na každé zařízení.

1. Spusťte novou sadu nástrojů, která bude generovat aktualizované konfigurace přepínače.

1. Sloučí rozdíly z kroku 6 do nových konfigurací přepínače.

1. Načtěte nové konfigurace do odpovídajících přepínačů. Spusťte příkazy po ověření poskytované ve výstupním adresáři z našich nástrojů.

1. Uložte konfigurace.

## <a name="ntp"></a>NTP

Pomocí privilegovaného koncového bodu (PEP) můžete aktualizovat časový server v Azure Stack. Použijte název hostitele, který se přeloží na dvě nebo víc IP adres serveru NTP (Network Time Protocol).

Azure Stack používá NTP k připojení k časovým serverům na internetu. Servery NTP poskytují přesný systémový čas. Čas se používá v rámci fyzických síťových přepínačů Azure Stack, hostitele životního cyklu, služby infrastruktury a virtuálních počítačů. Pokud hodiny nejsou synchronizované, Azure Stack se může setkat s vážnými problémy se sítí a ověřováním. Soubory protokolu, dokumenty a jiné soubory mohou být vytvořeny s nesprávnými časovými razítky.

Pro Azure Stack k synchronizaci času je vyžadován jeden časový server (NTP). Když nasadíte Azure Stack, zadáte adresu serveru NTP. Čas je kritická služba infrastruktury Datacenter. Pokud se služba změní, budete muset čas aktualizovat.

Azure Stack podporuje synchronizaci času s pouze jedním časovým serverem (NTP). Pro Azure Stack k synchronizaci času nelze zadat více NTPs. Doporučujeme nastavit položku DNS (Domain Name System), která se překládá na více serverů NTP.

### <a name="update-ntp-post-deployment"></a>Aktualizace po nasazení NTP

1. Připojte se k PEP. K odemknutí nemusíte otevírat lístek podpory.

1. Spusťte následující příkaz a zkontrolujte aktuální nakonfigurovaný server NTP.

    ```powershell
    Get-AzsTimeSource
    ```

1. Spuštěním následujícího příkazu aktualizujte Azure Stack na použití nového serveru NTP a okamžitě synchronizujte čas.

    ```powershell
    Set-AzsTimeSource -Timeserver NEWTIMESERVER -resync
    ```

>[!NOTE]
>Tato procedura neaktualizuje časový server na fyzických přepínačích.

## <a name="dns"></a>DNS

V této části se dozvíte, jak aktualizovat servery DNS pro překládání na externí názvy.

### <a name="update-the-dns-forwarder-in-azure-stack"></a>Aktualizace služby DNS pro přeposílání v Azure Stack

Aby mohla infrastruktura Azure Stack přeložit externí názvy, musí mít aspoň jeden dosažitelný server DNS. Pro nasazení Azure Stack musí být k dispozici předávací služba DNS. Tento vstup se používá pro Azure Stack interní servery DNS jako server pro přeposílání a umožňuje překlad externích IP adres pro služby, jako je ověřování, Správa Marketplace nebo využití.

DNS je kritická služba infrastruktury Datacenter, která se může změnit. Pokud se změní, Azure Stack nutné aktualizovat.

Tento článek popisuje použití nástroje PEP k aktualizaci služby DNS pro přeposílání v Azure Stack. Doporučujeme používat dvě IP adresy spolehlivého serveru DNS.

1. Připojte se k PEP. Nemusíte odemykat PEP otevřením lístku podpory.

1. Spuštěním následujícího příkazu zkontrolujte aktuálně nakonfigurované servery DNS pro přeposílání. Alternativně můžete použít také vlastnosti oblasti portálu pro správu.

    ```powershell
    Get-AzsDnsForwarder 
    ```

1. Spuštěním následujícího příkazu aktualizujte Azure Stack pro použití nového serveru DNS pro přeposílání.

    ```powershell
    Set-AzsDnsForwarder -IPAddress "IPAddress 1", "IPAddress 2"
    ```

1. Zkontrolujte výstup příkazu pro všechny chyby.
