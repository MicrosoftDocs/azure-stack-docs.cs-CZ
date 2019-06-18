---
title: Zpráva k vydání verze Azure Stack 1904 | Dokumentace Microsoftu
description: Další informace o aktualizaci 1904 pro integrované systémy Azure Stack, včetně novinek, známé problémy a kde lze stáhnout aktualizace a archivované zpráva k vydání verze Azure Stack.
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
ms.openlocfilehash: d3a50288464b81104ddd1dd032f93128fde43eae
ms.sourcegitcommit: 593d40bccf1b2957a763017a8a2d7043f8d8315c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2019
ms.locfileid: "67152510"
---
# <a name="azure-stack-1904-update"></a>Aktualizace služby Azure Stack 1904

*Platí pro: Integrované systémy Azure Stack*

Tento článek popisuje obsah balíčku 1904 aktualizace. Obsahuje novinky vylepšení a oprav pro tuto verzi služby Azure Stack. Tento článek obsahuje následující informace:

- [Popis co je nového, zabezpečení, vylepšení a oprav aktualizací](azure-stack-release-notes-1904.md#whats-in-this-update)
- [Plánování aktualizace](azure-stack-release-notes-1904.md#update-planning)

> [!IMPORTANT]  
> Tento balíček aktualizace je pouze pro integrované systémy Azure Stack. Tento balíček aktualizace nevztahují na Azure Stack Development Kit.

## <a name="archived-release-notes"></a>Archivované poznámky k verzi

Můžete zobrazit [starší verze služby Azure Stack poznámky v Galerii TechNet](http://aka.ms/azsarchivedrelnotes). Tyto archivované verze poznámky jsou k dispozici pouze pro referenční účely a není určeno, podporu pro tyto verze. Potřebujete další pomoc obraťte se na službu zákaznické podpory Microsoftu.

## <a name="build-reference"></a>Referenční informace o buildu

Číslo sestavení aktualizace Azure Stack 1904 je **1.1904.0.36**.

### <a name="update-type"></a>Typ aktualizace

Typ sestavení update Azure Stack 1904 je **Express**. Další informace o aktualizaci typy sestavení, najdete v článku [správy aktualizací ve službě Azure Stack](azure-stack-updates.md) článku. Očekávaná doba potřebná pro aktualizace 1904 dokončete je asi 16 hodin, ale přesné časy se může lišit. Tento modul runtime aproximace je specifická pro 1904 aktualizace a by neměl ve srovnání s jinými aktualizacemi Azure Stack.

## <a name="whats-in-this-update"></a>Co je v této aktualizaci

<!-- The current theme (if any) of this release. -->

<!-- What's new, also net new experiences and features. -->

### <a name="improvements"></a>Vylepšení

<!-- Changes and product improvements with tangible customer-facing value. -->

- Zásobník softwarově definované sítě (SDN) byla v 1904 významná vylepšení. Tato vylepšení zvýšit celkový údržby a spolehlivost komponent SDN ve službě Azure Stack.

- Přidat oznámení na portálu pro správce, pokud je aktuálně přihlášený uživatel nemá potřebná oprávnění, umožňující řídicí panel načíst správně. Také obsahuje odkaz na dokumentaci, která popisuje účty, které má požadovaná oprávnění, v závislosti na poskytovateli identity používá při nasazení.

- Přidání vylepšení do virtuálního počítače odolnost a dostupnost, která řeší tento scénář, ve kterém jsou všechny virtuální počítače přejdou do režimu offline, pokud svazek úložiště, který obsahuje konfigurační soubory virtuálního počítače přejde do režimu offline.

<!-- 1901,2,3 related hotfix -->
- Přidání optimalizace počtu instancí virtuálních počítačů současně evakuovat a umístí limitu na adresu od virtuálního počítače nebo blackouts, pokud síť není v případě velkého zatížení využité šířky pásma. Tato změna zvyšuje době provozu virtuálního počítače při aktualizaci systému.

<!-- 1901,2,3 related hotfix -->
- Vylepšené prostředků omezování, když systém běží ve velkém měřítku pro ochranu před vyčerpáním prostředků platformy, výsledkem je neúspěšné operace portálu interních procesů.

- Vylepšené možnosti filtrování povolit operátory použít několik filtrů ve stejnou dobu. Můžete řadit pouze na **název** sloupce v nové uživatelské rozhraní.

- Vylepšení procesu odstranění nabídky, plány, kvót a předplatných. Nyní můžete úspěšně odstranit nabídek, kvót, plány a předplatné z portálu správce Pokud objekt, který chcete odstranit nemá žádné závislosti. Další informace najdete v [tomto článku](azure-stack-delete-offer.md).  

<!-- this applies to bug 3725384 and bug #4225643 -->
- Vylepšili jsme svazku zprávy syslog odfiltrováním zbytečných události a poskytnutím konfiguračního parametru a vyberte úroveň závažnosti požadované pro přesměrované zprávy. Další informace o tom, jak nakonfigurovat úroveň závažnosti, naleznete v tématu [integrace datových center Azure Stack – předávání syslog](azure-stack-integrate-security.md).

<!--this applied to Bug 1473487 -->
- Přidali novou funkci pro **Get-AzureStackLog** rutiny začleněním další parametr, `-OutputSASUri`. Teď můžete shromažďovat protokoly služby Azure Stack z vašeho prostředí a jejich uložení v zadaném kontejneru objektů blob v Azure Storage. Další informace najdete v tématu [diagnostiky Azure Stack](azure-stack-diagnostics.md#examples).

- Přidá novou kontrolu paměti v **testovací AzureStack** `UpdateReadiness` skupinu, která zkontroluje, zda máte k dispozici dostatek paměti na zásobníku pro aktualizace se nepodaří.

<!-- Bug/Task 4311058 -->
- Vylepšení **testovací AzureStack** za vaše rozhodnutí vyzkoušet stavu Service Fabric.

<!-- feature: 2976966 -->
- Vylepšení aktualizace hardwaru, což snižuje čas potřebný k dokončení firmwaru jednotky aktualizovat na 2 – 4 hodiny. Modul aktualizace dynamicky zjistí, které části aktualizace budete muset provést, na základě obsahu v balíčku.

<!-- Feature 3906611 -->
- Přidání robustního operace prechecks zabránit rušivé infrastrukturu role instance operací, které ovlivňují dostupnost.

<!-- Feature 3780326 -->
- Vylepšení idempotence infrastruktura zálohování akčního plánu.

<!--Bug/Task 3139609 -->
- Vylepšení pro shromažďování protokolů služby Azure Stack. Tato vylepšení zkracují čas potřebný k načtení sady protokolů. Navíc [Get-AzureStackLog](azure-stack-diagnostics.md#run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system) rutina už generuje výchozí protokoly pro výrobce OEM roli. Je třeba spustit [Invoke-AzureStackOnDemandLog](azure-stack-diagnostics.md#invoke-azurestackondemandlog) rutiny, určení roli pro načtení protokolů výrobce OEM. Další informace najdete v tématu [diagnostiky Azure Stack](azure-stack-diagnostics.md#run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system).

- Azure Stack nyní sleduje adresa URL federačních data k dispozici pro integraci datacentra v rámci služby AD FS. To zvyšuje spolehlivost během tajných kódů otočení instance služby AD FS zákazníka nebo farmy.

### <a name="changes"></a>Změny

<!-- Feature 3906611 -->
- Odebrali možnost operátorům Azure stacku pro vypnutí infrastruktury instance rolí na portálu pro správce. Funkce restartování zajistí pokus čistého vypnutí před restartování instance role infrastruktury. Pro pokročilé scénáře funkce prostředí PowerShell a rozhraní API zůstane k dispozici.

<!-- Feature ## 4199257 -->
- Nové tržiště prostředí pro správu, pomocí samostatné obrazovky pro Image z Marketplace a zprostředkovatele prostředků není k dispozici. Prozatím **poskytovatele prostředků** okna je prázdný, ale zobrazí se v budoucích verzích nové PaaS nabídek služeb a spravovat **poskytovatelů prostředků** okno.

<!-- Feature ## 4199257 -->
- Změny aktualizace prostředí na portálu pro operátor. Existuje Nová mřížka aktualizací zprostředkovatele prostředků. Možnost aktualizovat poskytovatele prostředků ještě není k dispozici.

<!-- Task ## 3748423  -->
- Změny prostředí instalace aktualizace na portálu pro operátor. Ke službě Azure Stack operátory správně reagovat na chybu aktualizace portálu teď poskytuje konkrétnější doporučení v závislosti na stavu jednotka škálování odvozené automaticky spuštěním **testovací AzureStack** a analýza kódu výsledky. Na základě výsledku, bude informovat operátora provést jednu z následujících akcí:

  - "Text soft" výstražné upozornění se zobrazí na portálu, který čte "poslední aktualizace potřebám pozornost. Společnost Microsoft doporučuje, otevřete žádost o službu běžné pracovní době. Jako součást procesu aktualizace AzureStack testu je prováděno a založené na výstupu se vygeneruje výstraha nejvhodnější. V tomto případě Test AzureStack předána."

  - "Pevné" Kritická výstraha se zobrazí na portálu, který čte "poslední aktualizace se nezdařila. Společnost Microsoft doporučuje, otevřete žádost o službu co nejdříve. Jako součást procesu aktualizace AzureStack testu je prováděno a založené na výstupu se vygeneruje výstraha nejvhodnější. V tomto případě Test AzureStack také se nezdařilo."

- Aktualizace agenta Azure Linux verze 2.2.38.0. Tato podpora umožňuje zákazníkům udržovat konzistentní Linuxové Image mezi Azure a Azure Stack.

- Změny aktualizace protokoly portálu operátor. Žádosti o získání protokolů úspěšná aktualizace už nejsou k dispozici. Protokoly aktualizace se nezdařila, protože jsou užitečné pro diagnostiku, jsou stále k dispozici ke stažení.

### <a name="fixes"></a>Opravy

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- Opravili jsme problém, ve kterém nebyla zachována konfiguraci syslogu prostřednictvím cyklu aktualizace způsobí klienta syslog přijít o své konfiguraci a zprávy syslog zastavit předávaná. Konfigurace Syslog je nyní zachovány.

- Opravili jsme problém v CRP. která zablokuje zrušení přidělení virtuálních počítačů. Dříve Pokud virtuální počítač obsahuje více velké spravované disky, rušení přidělení virtuálního počítače mohlo selhat s chybou vypršení časového limitu.

- Oprava potíží s modulem Windows Defender vliv na přístup k jednotce škálování úložiště.

- Oprava uživatelů portálu potíží, ve kterém se v okně zásady přístupu pro účty úložiště blob nepodařilo načíst.

- Opravili jsme problém v portálech pro správce i uživatele, ve kterých byly zobrazeny chybné oznámení o globální webu Azure portal.

- Opravili chybu portálu při výběru, který **zpětnou vazbu** dlaždice způsobila kartě prázdný prohlížeče otevřete.

- Opravili jsme problém v které změna statickou IP adresu pro konfiguraci IP, která byla vázána k síti způsobila adaptér připojený k instanci virtuálního počítače, který se zobrazí chybová zpráva na portálu.

- Oprava potíží portálu uživatele v které pokusem o **připojit síťové rozhraní** k existujícímu virtuálnímu počítači prostřednictvím **sítě** okno způsobila operace se nezdaří s chybovou zprávu.

- Opravili jsme problém, ve kterém Azure Stack nepodporuje připojení více než 4 síťových rozhraní (NIC) k instanci virtuálního počítače.

- Oprava portálu potíží přidat příchozí pravidlo zabezpečení a výběr **značka služby** jako zdroje a zobrazí několik možností, které nejsou k dispozici pro Azure Stack.

- Byl opraven problém, do které skupiny zabezpečení sítě (Nsg) nebyla úspěšná ve službě Azure Stack stejným způsobem jako globální Azure.

- Opravili jsme problém v Marketplace správu, který skryje všechny stažené produktů, pokud vyprší platnost registrace nebo je odebrat.

- Opravili jsme problém, v které vydání **Set-AzureRmVirtualNetworkGatewayConnection** příkazu v Powershellu na existující připojení brány virtuální sítě selhalo s chybovou zprávou **neplatný sdílený klíč nakonfigurovaný ...** .

- Opravili jsme problém, který způsobil poskytovatele prostředků sítě (NRP) bude synchronizován s funkcí síťový adaptér, což vede k duplicitní prostředky, které jsou požadovány. V některých případech to výsledkem opuštění nadřazený prostředek v chybovém stavu.

- Opravili jsme problém, ve kterém Pokud uživatel, který byl přiřadit roli přispěvatele k předplatnému, ale nebyl přidělen explicitní oprávnění ke čtení, byla generována chyba, kterou si přečíst **... Klient "somelogonaccount@domain.com" s objektem ID {GUID} nemá oprávnění k provedení akce...**  při pokusu o uložení změny k prostředku.

- Opravili jsme problém, ve kterém byla prázdná obrazovka správu webu marketplace, pokud nástroj v režimu offline syndikace byl použit k nahrávání obrázků a některý z nich chybí ikona URI(s).

- Opravili jsme chybu, která zabránila produktů, které se nepovedlo stáhnout odstranit v marketplace správu.

### <a name="security-updates"></a>Aktualizace zabezpečení

Tato aktualizace služby Azure Stack nezahrnuje aktualizace zabezpečení pro příslušný operační systém, který je hostitelem služby Azure Stack. Informace najdete v tématu [aktualizace zabezpečení služby Azure Stack.](azure-stack-release-notes-security-updates-1904.md)

## <a name="update-planning"></a>Plánování aktualizace

Před instalací této aktualizace, ujistěte se, že ke kontrole následující informace:

- [Známé problémy](azure-stack-release-notes-known-issues-1904.md)
- [Aktualizace zabezpečení](azure-stack-release-notes-security-updates-1904.md)
- [Kontrolní seznam před a po instalaci aktualizace aktivity](azure-stack-release-notes-checklist.md)

> [!NOTE]
> Ujistěte se, že používáte nejnovější verzi [Azure Stack Capacity Planner](https://aka.ms/azstackcapacityplanner) nástroj k provedení úlohy plánování a velikosti. Nejnovější verze obsahuje opravy chyb a nabízí nové funkce, které se vydávají s každou aktualizaci Azure Stack.

## <a name="download-the-update"></a>Stáhnout aktualizaci.

Můžete stáhnout aktualizace balíčku Azure Stack 1904 z [stránce pro stažení služby Azure Stack](https://aka.ms/azurestackupdatedownload).

## <a name="hotfixes"></a>Opravy hotfix

Azure Stack vydává opravy hotfix v pravidelných intervalech. Nezapomeňte nainstalovat nejnovější opravy hotfix Azure Stack pro 1903 před aktualizací služby Azure Stack na 1904.

Azure Stack opravy hotfix platí pouze pro integrované systémy Azure Stack; Nepokoušejte se nainstalovat ASDK opravy hotfix.

### <a name="before-applying-the-1904-update"></a>Před použitím 1904 aktualizace

1904 vydání sady Azure Stack se musí použít na verzi 1903 s následující opravy hotfix:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack opravu hotfix 1.1903.2.39](https://support.microsoft.com/help/4500638)

### <a name="after-successfully-applying-the-1904-update"></a>Po úspěšném použití 1904 aktualizace

Po instalaci této aktualizace nainstalujte všechny příslušné opravy hotfix. Další informace najdete v tématu naše [zásady obsluhy](azure-stack-servicing-policy.md).

- [Azure Stack opravu hotfix 1.1904.4.45](https://support.microsoft.com/help/4505688)

## <a name="automatic-update-notifications"></a>Upozornění na automatické aktualizace

Zákazníkům se systémy, které můžete přístup k Internetu z infrastruktury sítě se zobrazí **k dispozici je aktualizace** zpráva na portálu pro operátor. Systémy bez připojení k Internetu, můžete stáhnout a naimportovat soubor .zip s odpovídající soubor XML.

> [!TIP]  
> Předplatit následující *RSS* nebo *Atom* kanály, držet krok s Azure Stack opravy hotfix:
>
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

## <a name="next-steps"></a>Další postup

- Přehled správy aktualizací ve službě Azure Stack najdete v tématu [správy aktualizací ve službě Azure Stack přehled](azure-stack-updates.md).  
- Další informace o tom, jak použít aktualizace pomocí služby Azure Stack najdete v tématu [použití aktualizací ve službě Azure Stack](azure-stack-apply-updates.md).
- Zásady údržby pro integrované systémy Azure Stack, a co musíte udělat, aby byl váš systém v podporovaném stavu najdete v tématu [Azure Stack zásady obsluhy](azure-stack-servicing-policy.md).  
- Privilegované koncový bod (období) použít ke sledování a obnovit aktualizace, najdete v článku [monitorování aktualizací ve službě Azure Stack pomocí privilegovaných koncového bodu](azure-stack-monitor-update.md).  

