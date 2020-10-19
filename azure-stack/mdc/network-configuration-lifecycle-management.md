---
title: MDC Správa životního cyklu konfigurace sítě
description: Přečtěte si o správě životního cyklu konfigurace sítě pro zařízení MDC.
author: justinha
ms.author: justinha
ms.service: azure-stack
ms.topic: conceptual
ms.date: 01/02/2020
ms.lastreviewed: 01/02/2020
ms.openlocfilehash: 78e4a98a606b100e42d4beb465874d4d0648c971
ms.sourcegitcommit: e4e2cc6a68f02c3e856f58ca5ee51b3313c7ff8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92182961"
---
# <a name="configuration-lifecycle-management"></a>Správa životního cyklu konfigurace

Toto téma popisuje správu životního cyklu pro konfiguraci sítě.

## <a name="update-configuration"></a>Aktualizovat konfiguraci


Metodologie aktualizace konfigurací přepínače v poli. To platí pro všechny a všechny typy aktualizací konfigurace. Kvůli mnoha neznámým proměnným mimo náš ovládací prvek, jako jsou ruční konfigurace uplatňované zákazníky nebo výrobcem OEM, se jedná o ruční proces s více kroky. V tuto chvíli není nijak zajištěna doba provozu a aktualizace by se měly provádět během časového období údržby.

** \# 1** . Vytvořte zálohu aktuálních konfiguračních souborů pro přepínače edg1, EDGE2, tor1, tor2 a BMC. Zkopírujte tyto soubory mimo přepínač.

** \# 2** . Poznamenejte si číslo sestavení sady nástrojů existující konfigurace.
Všechny konfigurace by měly mít tuto zprávu v hlavičce **motd** . Vyhledejte *"BUILDNUMBER"*

** \# 3 –** obnovování počátečních konfigurací pomocí stejné verze sady nástrojů z kroku \# odst.

** \# 4 –** načtěte konfigurace z kroku \# 3 na příslušné přepínače.
Důvodem je *, aby se* konfigurace vygenerovaná našimi nástroji procházela prostřednictvím přepínače pro získání standardních hodnot. To může výrobce OEM provést na samostatném hardwaru, jako je třeba v laboratoři výrobce OEM nebo v lokalitě s zákazníkem.

** \# 5** . Vytvořte zálohu konfiguračního souboru z kroku \# 4 a zkopírujte ho do vzdáleného umístění.

** \# 6 –** pomocí nástroje diff podle vašeho výběru Porovnejte zálohu aktuální konfigurace z kroku \# 1 až po zálohu konfigurace standardních hodnot z kroku \# 5. Vytvořte si poznámku nebo kopii všech rozdílů, které by měly být přeneseny do upgradované konfigurace přepínače na každé zařízení.

** \# 7** . Spusťte novou sadu nástrojů, která bude generovat aktualizované konfigurace přepínače.

** \# 8** . sloučení rozdílů z kroku 6 do nových konfigurací přepínačů.

** \# 9 –** načte nové konfigurace do odpovídajících přepínačů a spustí příkazy po ověření poskytované ve výstupním adresáři z našich nástrojů.

** \# 10 –** uložení konfigurací.

## <a name="ntp"></a>NTP

Pomocí privilegovaného koncového bodu (PEP) můžete aktualizovat časový server v Azure Stack. Použijte název hostitele, který se přeloží na dvě nebo víc IP adres serveru NTP.

Azure Stack používá protokol NTP (Network Time Protocol) pro připojení k časovým serverům v Internetu. Servery NTP poskytují přesný systémový čas. Čas se používá v rámci fyzických síťových přepínačů Azure Stack, hostitele životního cyklu, služby infrastruktury a virtuálních počítačů. Pokud hodiny nejsou synchronizované, může Azure Stack dojít k vážným problémům se sítí a ověřováním. Soubory protokolu, dokumenty a jiné soubory mohou být vytvořeny s nesprávnými časovými razítky.

Pro Azure Stack k synchronizaci času je vyžadován jeden časový server (NTP).
Když nasadíte Azure Stack, zadáte adresu serveru NTP. Čas je kritická služba infrastruktury Datacenter. Pokud se služba změní, budete muset čas aktualizovat.

Azure Stack podporuje synchronizaci času s pouze jedním časovým serverem (NTP). Nelze poskytnout více NTPs pro Azure Stack k synchronizaci času s. Doporučuje se nastavit položku DNS, která se překládá na více serverů NTP. 


### <a name="update-ntp-post-deployment"></a>Aktualizace po nasazení NTP

1.  Připojte se k privilegovanému koncovému bodu (PEP). K odemknutí privilegovaného koncového bodu není nutné otevírat lístek podpory. 

2.  Spusťte následující příkaz a zkontrolujte aktuální nakonfigurovaný server NTP:

    ```powershell
    Get-AzsTimeSource
    ```

3.  Spuštěním následujícího příkazu aktualizujte Azure Stack na použití nového serveru NTP a okamžitě synchronizujte čas.

    ```powershell
    Set-AzsTimeSource -Timeserver NEWTIMESERVER -resync
    ```

    >[!NOTE] 
    >Tato procedura neaktualizuje časový server na fyzických přepínačích. 


## <a name="dns"></a>DNS

V této části se dozvíte, jak aktualizovat servery DNS pro překládání na externí názvy.

### <a name="update-the-dns-forwarder-in-azure-stack"></a>Aktualizace služby DNS pro přeposílání v Azure Stack

Aby mohla infrastruktura Azure Stack přeložit externí názvy, musí mít aspoň jeden dosažitelný server DNS. Pro nasazení Azure Stack musí být k dispozici předávací služba DNS. Tento vstup se používá pro Azure Stack interní servery DNS jako servery pro přeposílání a umožňuje překlad externích IP adres pro služby, jako je ověřování, Správa Marketplace nebo využití.

DNS je kritická služba infrastruktury Datacenter, která se může změnit, a pokud k tomu dojde, Azure Stack nutné aktualizovat.

Tento článek popisuje použití privilegovaného koncového bodu (PEP) k aktualizaci služby DNS pro přeposílání v Azure Stack. Doporučuje se používat dvě IP adresy spolehlivého serveru DNS.

1.  Připojte se k privilegovanému koncovému bodu. Všimněte si, že není nutné odemknout privilegovaný koncový bod otevřením lístku podpory.

2.  Spuštěním následujícího příkazu zkontrolujte aktuálně nakonfigurované servery DNS pro přeposílání. Alternativně můžete použít také vlastnosti oblasti portálu pro správu:

    ```powershell
    Get-AzsDnsForwarder 
    ```

3.  Spuštěním následujícího příkazu aktualizujte Azure Stack pro použití nového serveru DNS pro přeposílání:

    ```powershell
    Set-AzsDnsForwarder -IPAddress "IPAddress 1", "IPAddress 2"
    ``` |

4.  Review the output of the command for any errors.
