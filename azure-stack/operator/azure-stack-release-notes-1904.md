---
title: Zpráva k vydání verze Azure Stack 1904 | Microsoft Docs
description: Seznamte se s aktualizací 1904 pro Azure Stack integrovaných systémů, včetně nových, známých problémů a místa stažení aktualizace a archivované poznámky k verzi Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 05/30/2019
ms.openlocfilehash: 71ad5812472534ae4a7e3dc34feabf7a1a02faa0
ms.sourcegitcommit: f6ea6daddb92cbf458f9824cd2f8e7e1bda9688e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2019
ms.locfileid: "68494050"
---
# <a name="azure-stack-1904-update"></a>Aktualizace Azure Stack 1904

*Platí pro: Azure Stack integrovaných systémů*

Tento článek popisuje obsah balíčku aktualizace 1904. Tato aktualizace obsahuje novinky a opravy pro tuto verzi Azure Stack. Tento článek obsahuje následující informace:

- [Popis novinek, vylepšení, oprav a aktualizací zabezpečení](azure-stack-release-notes-1904.md#whats-in-this-update)
- [Plánování aktualizací](azure-stack-release-notes-1904.md#update-planning)

> [!IMPORTANT]  
> Tento balíček aktualizace je pouze pro integrované systémy Azure Stack. Tento balíček aktualizace nevztahují na Azure Stack Development Kit.

## <a name="archived-release-notes"></a>Archivované poznámky k verzi

[Starší verze Azure Stack poznámky k verzi najdete v Galerii TechNet](http://aka.ms/azsarchivedrelnotes). Tato Archivovaná zpráva k vydání verze je poskytována pouze pro referenční účely a neznamená podporu těchto verzí. Pokud potřebujete další pomoc, obraťte se na službu zákaznické podpory společnosti Microsoft.

## <a name="build-reference"></a>Referenční informace o buildu

Číslo buildu aktualizace Azure Stack 1904 je **1.1904.0.36**.

### <a name="update-type"></a>Typ aktualizace

Typ sestavení aktualizace Azure Stack 1904 je **Express**. Další informace o typech sestavení aktualizace naleznete v článku [Správa aktualizací v Azure Stack](azure-stack-updates.md) . Očekávaný čas potřebný k dokončení aktualizace 1904 je přibližně 16 hodin, ale přesné časy se mohou lišit. Tato přibližná doba běhu je specifická pro aktualizaci 1904 a neměla by se porovnávat s jinými aktualizacemi Azure Stack.

## <a name="whats-in-this-update"></a>Co je v této aktualizaci

<!-- The current theme (if any) of this release. -->

<!-- What's new, also net new experiences and features. -->

### <a name="improvements"></a>Vylepšen

<!-- Changes and product improvements with tangible customer-facing value. -->

- V 1904 byly provedeny významné vylepšení zásobníku softwarově definované sítě (SDN). Tato vylepšení zvyšují celkovou údržbu a spolehlivost zásobníku SDN v Azure Stack.

- Přidání oznámení na portál pro správu, pokud aktuálně přihlášený uživatel nemá potřebná oprávnění, což umožňuje správné načtení řídicího panelu. Obsahuje taky odkaz na dokumentaci, která vysvětluje, které účty mají příslušná oprávnění, v závislosti na zprostředkovateli identity použitého během nasazování.

- Bylo přidáno vylepšení odolnosti a provozu virtuálních počítačů, které řeší scénář, ve kterém všechny virtuální počítače přecházejí do stavu offline, pokud svazek úložiště obsahující konfigurační soubory virtuálních počítačů přejde do režimu offline.

<!-- 1901,2,3 related hotfix -->
- Přidala se optimalizace počtu virtuálních počítačů, které se současně vyhusty, a umístili jste limit na využitou šířku pásma, aby se virtuální počítač výpadky nebo blackouts v případě vysokého zatížení sítě. Tato změna zvyšuje dobu provozu virtuálního počítače při aktualizaci systému.

<!-- 1901,2,3 related hotfix -->
- Vylepšené omezování prostředků v případě, že systém pracuje se škálováním na ochranu proti interním procesům vyčerpání prostředků platformy, což vede k neúspěšným operacím na portálu.

- Vylepšené možnosti filtrování umožňují operátorům použít více filtrů současně. Ve sloupci **název** můžete řadit pouze v novém uživatelském rozhraní.

- Vylepšení procesu odstraňování nabídek, plánů, kvót a předplatných. Z portálu pro správu teď můžete úspěšně odstranit nabídky, kvóty, plány a odběry, pokud objekt, který chcete odstranit, nemá žádné závislosti. Další informace najdete v [tomto článku](azure-stack-delete-offer.md).  

<!-- this applies to bug 3725384 and bug #4225643 -->
- Vylepšený objem zpráv syslog filtrováním zbytečných událostí a zadáním konfiguračního parametru pro výběr požadované úrovně závažnosti pro předávané zprávy. Další informace o tom, jak nakonfigurovat úroveň závažnosti, najdete v tématu [Azure Stack Integration Datacenter – předávání SYSLOG](azure-stack-integrate-security.md).

<!--this applied to Bug 1473487 -->
- Do rutiny **Get-AzureStackLog** se přidala nová funkce, která zahrnuje další parametr, `-OutputSASUri`. Nyní můžete shromažďovat protokoly Azure Stack z vašeho prostředí a ukládat je do zadaného Azure Storage kontejneru objektů BLOB. Další informace najdete v tématu [diagnostika Azure Stack](azure-stack-configure-on-demand-diagnostic-log-collection.md#examples).

- Do skupiny **test-AzureStack** `UpdateReadiness` bylo přidáno nové vrácení paměti, které kontroluje, jestli je v zásobníku k dispozici dostatek paměti, aby se aktualizace úspěšně dokončila.

<!-- Bug/Task 4311058 -->
- Vylepšení **testu – AzureStack** pro vyhodnocení stavu Service Fabric.

<!-- feature: 2976966 -->
- Vylepšení aktualizací hardwaru, což zkracuje dobu potřebnou k dokončení aktualizace firmwaru pro jednotku 2-4 hodin. Modul Update dynamicky určuje, které části aktualizace je třeba provést, na základě obsahu v balíčku.

<!-- Feature 3906611 -->
- Přidání předkontrol robustních operací, aby se předešlo operacím instance role rušivé infrastruktury, které mají vliv na dostupnost.

<!-- Feature 3780326 -->
- Vylepšení plánu akcí zálohování infrastruktury idempotence.

<!--Bug/Task 3139609 -->
- Vylepšení shromažďování protokolů Azure Stack. Tato vylepšení omezují dobu potřebnou k načtení sady protokolů. Rutina [Get-AzureStackLog](azure-stack-configure-on-demand-diagnostic-log-collection.md#run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system) už navíc negeneruje výchozí protokoly pro roli OEM. Je nutné spustit rutinu [Invoke-AzureStackOnDemandLog](azure-stack-configure-on-demand-diagnostic-log-collection.md#invoke-azurestackondemandlog) a zadat roli pro načtení protokolů OEM. Další informace najdete v tématu [diagnostika Azure Stack](azure-stack-configure-on-demand-diagnostic-log-collection.md#run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system).

- Azure Stack teď sleduje adresu URL federačních dat poskytnutých pro integraci Datacenter se službou AD FS. To zlepšuje spolehlivost při střídání tajnosti instance nebo farmy služby AD FS.

### <a name="changes"></a>Změny

<!-- Feature 3906611 -->
- Odebrali jsme možnost pro operátory Azure Stack pro vypnutí instancí rolí infrastruktury na portálu pro správu. Funkce restartování zajišťuje před restartováním instance role infrastruktury čistý pokus o vypnutí. Pro pokročilé scénáře zůstanou dostupné funkce rozhraní API a prostředí PowerShell.

<!-- Feature ## 4199257 -->
- K dispozici je nové prostředí pro správu Marketplace, které obsahuje samostatné obrazovky pro image a poskytovatele prostředků na webu Marketplace. V současné době je okno **poskytovatelé prostředků** teď prázdné, ale v budoucích verzích se nové nabídky služby PaaS zobrazí a budou se spravovat v okně **poskytovatelé prostředků** .

<!-- Feature ## 4199257 -->
- Změny v možnosti aktualizace na portálu operátora. Pro aktualizace poskytovatele prostředků je k dispozici nová mřížka. Možnost aktualizovat poskytovatele prostředků zatím není k dispozici.

<!-- Task ## 3748423  -->
- Změny v prostředí instalace aktualizace na portálu operátora. Aby Azure Stack operátory reagovaly vhodně na problém s aktualizací, portál teď poskytuje konkrétnější doporučení na základě stavu jednotky škálování, jak je odvozeno automaticky spuštěním **test-AzureStack** a analýzou výsledků. Na základě výsledku bude pracovníkovi informovat, aby provedl jednu ze dvou akcí:

  - Na portálu se zobrazí upozornění "tiché", které čte "Poslední aktualizace vyžaduje pozornost. Microsoft doporučuje otevřít žádost o služby během normální pracovní doby. V rámci procesu aktualizace se provede test-AzureStack a na základě výstupu vygenerujeme nejvhodnější výstrahu. V tomto případě test-AzureStack proběhl úspěšně. "

  - Na portálu se zobrazí "pevná" kritická výstraha, která čte "Poslední aktualizace se nezdařila. Společnost Microsoft doporučuje otevřít žádost o služby co nejdříve. V rámci procesu aktualizace se provede test-AzureStack a na základě výstupu vygenerujeme nejvhodnější výstrahu. V tomto případě se nepovedlo také test-AzureStack. "

- Aktualizace agenta Azure Linux verze 2.2.38.0. Tato podpora umožňuje zákazníkům udržovat konzistentní image Linux mezi Azure a Azure Stack.

- Změny v protokolech aktualizace na portálu operátora. Požadavky na načtení úspěšných protokolů aktualizací již nejsou k dispozici. Protokoly aktualizací, které se nezdařily, protože jsou pro diagnostiku k dispozici, jsou stále k dispozici ke stažení.

### <a name="fixes"></a>Opravy

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- Opravili jsme problém, kdy se konfigurace syslogu nezachovala prostřednictvím cyklu aktualizace, což způsobí ztrátu konfigurace klienta syslog a zastavení zpráv syslogu. Konfigurace syslogu je teď zachovaná.

- Opravili jsme problém v CRP, který blokoval zrušení přidělení virtuálních počítačů. Dřív, pokud virtuální počítač obsahoval více velkých spravovaných disků, může se stát, že zrušení přidělení virtuálního počítače selhalo s chybou vypršení časového limitu.

- Opravili jsme problém s modulem Windows Defender, který má vliv na přístup k úložišti jednotek škálování.

- Opravili jsme problém s uživatelským portálem, ve kterém se nepovedlo načíst okno zásad přístupu pro účty BLOB Storage.

- Opravili jsme problém na portále správce i uživatele, ve kterém se zobrazila chybná oznámení o globálních Azure Portal.

- Opravili jsme problém s uživatelským portálem, ve kterém se vybere dlaždice pro **zpětnou vazbu** , která způsobila otevření prázdné karty prohlížeče.

- Opravili jsme problém portálu, ve kterém se mění statická IP adresa pro konfiguraci protokolu IP, která je vázaná na síťový adaptér připojený k instanci virtuálního počítače, což způsobilo, že se zobrazí chybová zpráva.

- Opravili jsme problém s uživatelským portálem, ve kterém se snaží **připojit síťové rozhraní** k EXISTUJÍCÍmu virtuálnímu počítači prostřednictvím okna **sítě** , což způsobilo selhání operace s chybovou zprávou.

- Opravili jsme problém, kdy Azure Stack nepodporovala připojení více než 4 síťových rozhraní k instanci virtuálního počítače.

- Opravili jsme problém portálu, ve kterém přidáte příchozí pravidlo zabezpečení a vyberete jako zdroj **značku služby** , zobrazí se několik možností, které nejsou k dispozici pro Azure Stack.

- Opravili jsme problém, kdy skupiny zabezpečení sítě (skupin zabezpečení sítě) nefungovaly v Azure Stack stejným způsobem jako globální Azure.

- Opravili jsme problém se správou Marketplace, který skryje všechny stažené produkty, pokud vyprší platnost registrace nebo se odebere.

- Opravili jsme problém, kdy se v prostředí PowerShell vystavil příkaz **set-AzureRmVirtualNetworkGatewayConnection** pro existující připojení brány virtuální sítě, protože se nakonfigurovali chybová zpráva **neplatný sdílený klíč..** .

- Opravili jsme problém, který způsobil, že zprostředkovatel síťových prostředků (NRP) není synchronizovaný se síťovým adaptérem, což vede k vyžádání duplicitních prostředků. V některých případech to vedlo k tomu, že nadřazená prostředky zůstala v chybovém stavu.

- Opravili jsme problém, kdy uživatel, kterému byla přiřazena role přispěvatele k předplatnému, ale explicitně neudělil oprávnění ke čtení, vygenerovala se chyba, která se přečte **... Klientsomelogonaccount@domain.coms ID objektu {GUID} nemá autorizaci k provedení akce...** při pokusu o uložení změny prostředku.

- Opravili jsme problém, ve kterém byla obrazovka správy Marketplace prázdná, pokud byl k nahrání imagí použit nástroj pro syndikaci offline, přičemž některé z nich postrádá identifikátor URI ikony.

- Opravili jsme problém, který brání tomu, aby se ze správy Marketplace odstranily produkty, které se nepovedlo stáhnout.

### <a name="security-updates"></a>Aktualizace zabezpečení

Tato aktualizace Azure Stack nezahrnuje aktualizace zabezpečení pro základní operační systém, který hostuje Azure Stack. Informace najdete v tématu [Azure Stack aktualizace zabezpečení.](azure-stack-release-notes-security-updates-1904.md)

## <a name="update-planning"></a>Plánování aktualizací

Před použitím této aktualizace se ujistěte, že si provedete následující informace:

- [Známé problémy](azure-stack-release-notes-known-issues-1904.md)
- [Aktualizace zabezpečení](azure-stack-release-notes-security-updates-1904.md)
- [Kontrolní seznam aktivit před a po instalaci aktualizace](azure-stack-release-notes-checklist.md)

> [!NOTE]
> Pro plánování úloh a změnu velikosti se ujistěte, že používáte nejnovější verzi nástroje [Azure Stack Capacity Planner](https://aka.ms/azstackcapacityplanner) Tool. Nejnovější verze obsahuje opravy chyb a poskytuje nové funkce, které jsou vydány s každou aktualizací Azure Stack.

## <a name="download-the-update"></a>Stáhnout aktualizaci.

Balíček aktualizace Azure Stack 1904 můžete stáhnout ze [stránky pro stažení Azure Stack](https://aka.ms/azurestackupdatedownload).

## <a name="hotfixes"></a>Opravy hotfix

Azure Stack pravidelně vydává opravy hotfix. Před aktualizací Azure Stack na 1904 nezapomeňte nainstalovat nejnovější Azure Stack opravu hotfix pro 1903.

Azure Stack oprav hotfix se týkají pouze Azure Stack integrovaných systémů. Nepokoušejte se instalovat opravy hotfix na ASDK.

### <a name="before-applying-the-1904-update"></a>Před použitím aktualizace 1904

Vydání verze 1904 Azure Stack se musí použít ve verzi 1903 s následujícími opravami hotfix:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack opravy hotfix 1.1903.2.39](https://support.microsoft.com/help/4500638)

### <a name="after-successfully-applying-the-1904-update"></a>Po úspěšné instalaci aktualizace 1904

Po instalaci této aktualizace nainstalujte všechny příslušné opravy hotfix. Další informace najdete v našich [zásadách obsluhy](azure-stack-servicing-policy.md).

- [Azure Stack opravy hotfix 1.1904.4.45](https://support.microsoft.com/help/4505688)

## <a name="automatic-update-notifications"></a>Oznámení automatických aktualizací

Zákazníci se systémy, které mají přístup k Internetu z sítě infrastruktury, uvidí na portálu operátora zprávu **aktualizace k dispozici** . Systémy bez přístupu k Internetu můžou stáhnout a naimportovat soubor. zip s odpovídající příponou. XML.

> [!TIP]  
> Přihlaste se k odběru následujících informačních kanálů *RSS* nebo *Atom* , abyste zachovali Azure Stack opravy hotfix:
>
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Počtu](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

## <a name="next-steps"></a>Další postup

- Přehled správy aktualizací ve službě Azure Stack najdete v tématu [správy aktualizací ve službě Azure Stack přehled](azure-stack-updates.md).  
- Další informace o tom, jak použít aktualizace pomocí služby Azure Stack najdete v tématu [použití aktualizací ve službě Azure Stack](azure-stack-apply-updates.md).
- Zásady údržby pro integrované systémy Azure Stack, a co musíte udělat, aby byl váš systém v podporovaném stavu najdete v tématu [Azure Stack zásady obsluhy](azure-stack-servicing-policy.md).  
- Privilegované koncový bod (období) použít ke sledování a obnovit aktualizace, najdete v článku [monitorování aktualizací ve službě Azure Stack pomocí privilegovaných koncového bodu](azure-stack-monitor-update.md).  

