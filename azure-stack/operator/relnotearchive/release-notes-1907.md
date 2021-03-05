---
title: Zpráva k vydání verze Azure Stack 1907 | Microsoft Docs
description: Seznamte se s aktualizacemi Azure Stack integrovaných systémů 1907
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
ms.date: 10/29/2019
ms.author: sethm
ms.reviewer: prchint
ms.lastreviewed: 10/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 5ac273ef07747ffa0f5f32e681afd25ecc60bad6
ms.sourcegitcommit: f194f9ca4297864500e62d8658674a0625b29d1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102187210"
---
# <a name="azure-stack-updates-1907-release-notes"></a>Azure Stack aktualizace: zpráva k vydání verze 1907

Tento článek popisuje obsah balíčků aktualizací Azure Stack. Tato aktualizace obsahuje novinky a opravy pro tuto verzi Azure Stack.

Pokud chcete získat přístup k poznámkám k verzi pro jinou verzi, použijte rozevírací nabídku selektor verzí nad obsahem vlevo.

> [!IMPORTANT]  
> Tento balíček aktualizace je určen pouze pro Azure Stack integrovaných systémů. Nepoužívejte tento balíček aktualizace na Azure Stack Development Kit.

> [!IMPORTANT]  
> Pokud je vaše instance Azure Stack za více než dvěma aktualizacemi, je považována za nedodržující předpisy. Aby bylo možné [získat podporu, musíte aktualizovat aspoň minimální podporovanou verzi](../azure-stack-servicing-policy.md#keep-your-system-under-support).

## <a name="update-planning"></a>Plánování aktualizací

Před použitím této aktualizace se ujistěte, že si provedete následující informace:

- [Známé problémy](known-issues-1907.md)
- [Aktualizace zabezpečení](../release-notes-security-updates.md)
- [Kontrolní seznam aktivit před a po instalaci aktualizace](../release-notes-checklist.md)

Nápovědu k řešení potíží s aktualizacemi a procesu aktualizace najdete v tématu [řešení potíží se opravami a aktualizacemi pro Azure Stack](../azure-stack-troubleshooting.md).

## <a name="1907-build-reference"></a>1907 – odkaz na sestavení

Číslo buildu aktualizace Azure Stack 1907 je **1.1907.0.20**.

### <a name="update-type"></a>Typ aktualizace

Typ sestavení aktualizace Azure Stack 1907 je **Express**. Další informace o typech sestavení aktualizace naleznete v článku [Správa aktualizací v Azure Stack](../azure-stack-updates.md) . Na základě interního testování je očekávaný čas potřebný k dokončení aktualizace 1907 přibližně 13 hodin.

- Přesné běhové moduly pro aktualizaci jsou obvykle závislé na kapacitě používané systémem v rámci úloh klientů, připojení k systémové síti (Pokud je připojeno k Internetu) a hardwarové konfiguraci systému.
- Běhové moduly, které trvají déle než obvykle, nejsou běžné a nevyžadují akci Azure Stack operátory, pokud se aktualizace nezdařila.
- Tato přibližná doba běhu je specifická pro aktualizaci 1907 a neměla by se porovnávat s jinými aktualizacemi Azure Stack.

## <a name="whats-in-this-update"></a>Co je v této aktualizaci

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Novinky

<!-- What's new, also net new experiences and features. -->

- Verze služby pro diagnostiku protokolu Azure Stack pro obecné dostupnosti pro usnadnění a zlepšení shromažďování protokolů diagnostiky. Služba Azure Stack Diagnostic log Collection nabízí zjednodušený způsob shromažďování a sdílení diagnostických protokolů pomocí služeb zákaznické podpory Microsoftu (CSS). Tato služba Shromažďování protokolů diagnostiky poskytuje nové prostředí uživatele na portálu Azure Stack správce, které umožňuje operátorům nastavit automatické nahrávání diagnostických protokolů do objektu BLOB úložiště, když jsou vyvolány určité kritické výstrahy, nebo provést stejnou operaci na vyžádání. Další informace naleznete v článku [shromažďování diagnostických protokolů](../diagnostic-log-collection.md) .

- Verze ověřování infrastruktury Azure Stack sítě v rámci obecné dostupnosti jako součást nástroje pro ověření Azure Stack **test-AzureStack**. Azure Stack síťová infrastruktura bude součástí **AzureStack testu**, aby bylo možné zjistit, jestli dojde k selhání v síťové infrastruktuře Azure Stack. Test kontroluje konektivitu síťové infrastruktury tím, že obchází Azure Stack softwarově definované sítě. Ukazuje připojení z veřejné virtuální IP adresy k nakonfigurovaným serverům DNS pro přeposílání, serverům NTP a koncovým bodům identity. Kromě toho kontroluje připojení k Azure při použití Azure AD jako zprostředkovatele identity nebo federovaného serveru při používání služby AD FS. Další informace najdete v článku [Nástroj pro ověření Azure Stack](../azure-stack-diagnostic-test.md) .

- Přidání interního postupu pro rotaci tajných kódů pro otočení vnitřních certifikátů protokolu SQL TLS, jak je potřeba při aktualizaci systému.

### <a name="improvements"></a>Vylepšen

<!-- Changes and product improvements with tangible customer-facing value. -->

- Okno Azure Stack aktualizace nyní zobrazuje čas **dokončení posledního kroku** pro aktivní aktualizace. To lze zobrazit tak, že přejdete do okna aktualizace a kliknete na běžící aktualizaci. **Poslední krok** je pak k dispozici v části **Podrobnosti o spuštění aktualizace** .

- Vylepšení akcí operátoru **Start-AzureStack** a **stop-AzureStack** . Čas spuštění Azure Stack se snížil průměrem 50%. Čas pro vypnutí Azure Stack byl snížen o průměr 30%. Průměrná doba spouštění a vypínání zůstane stejná jako počet uzlů, které se zvyšují v jednotce škálování.

- Vylepšené zpracování chyb pro nástroj na odpojeném webu Marketplace. Pokud se stahování při použití **Export-AzSOfflineMarketplaceItem** nezdaří nebo částečně proběhne úspěšně, zobrazí se podrobná chybová zpráva s dalšími podrobnostmi o chybách a krocích při zmírňování.

- Vylepšili jsme výkon při vytváření spravovaných disků z velkého objektu BLOB nebo snímku stránky. Dřív při vytváření velkého disku aktivoval časový limit.  

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/127669774/home -->
- Vylepšená kontroly stavu virtuálních disků před vypnutím uzlu, aby nedocházelo k neočekávanému odpojení virtuálního disku.

- Vylepšené úložiště vnitřních protokolů pro operace správy. Výsledkem je vyšší výkon a spolehlivost během operací správy tím, že minimalizují spotřebu paměti a úložiště interních procesů protokolů. Můžete si také všimnout lepších časů načítání stránky v okně aktualizace na portálu pro správu. V rámci tohoto vylepšení už nebudou v systému k dispozici protokoly aktualizací starší než 6 měsíců. Pokud pro tyto aktualizace požadujete protokoly, nezapomeňte [si stáhnout Shrnutí](../azure-stack-apply-updates.md) pro všechny aktualizace, které jsou starší než 6 měsíců před provedením aktualizace 1907.

### <a name="changes"></a>Změny

- Azure Stack verze 1907 obsahuje varovnou výstrahu, která dává operátorům pokyn, aby před aktualizací na verzi 1908 aktualizovala balíček OEM svého systému na verzi 2,1 nebo novější. Další informace o tom, jak použít Azure Stack aktualizace OEM, najdete v části [použití Azure Stack původní aktualizace výrobce zařízení](../azure-stack-update-oem.md).

- Bylo přidáno nové odchozí pravidlo (HTTPS) pro povolení komunikace pro Azure Stack službu shromažďování protokolů diagnostiky. Další informace najdete v tématu [Azure Stack integrace Datacenter – publikování koncových bodů](../azure-stack-integrate-endpoints.md#ports-and-urls-outbound).

- Služba zálohování infrastruktury nyní odstraní částečně nahrané zálohy, pokud umístění externího úložiště vyčerpá kapacitu.

- Zálohy infrastruktury již nezahrnují zálohování dat služby Domain Services. To platí jenom pro systémy, které používají Azure Active Directory jako zprostředkovatele identity.

- Nyní Ověřujeme, že se obrázek ingestuje do okna **COMPUTE-> bitové kopie virtuálního počítače** , který je typu objekt blob stránky.

### <a name="fixes"></a>Opravy

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->
- Opravili jsme problém, kdy byl Vydavatel, nabídka a SKU ošetřen v Správce prostředků šabloně s rozlišením malých a velkých písmen: image nebyla načtena pro nasazení, pokud by parametry Image byly stejného případu jako u vydavatele, nabídky a SKU.

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/129536438/home -->
- Opravili jsme problém se selháním zálohování s chybovou zprávou **PartialSucceeded** z důvodu vypršení časových limitů při zálohování metadat služby úložiště.  

- Opravili jsme problém, kdy výsledkem odstranění předplatných uživatelů byly osamocené prostředky.

- Opravili jsme problém, kdy pole Popis nebyl při vytváření nabídky uložené.

- Opravili jsme problém, kdy uživatel s oprávněním **jen pro čtení** mohl vytvořit, upravit a odstranit prostředky. Nyní může uživatel vytvořit prostředky pouze v případě, že je přiřazeno oprávnění **Přispěvatel** . 

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/127772311/home -->
- Opravili jsme problém, kdy se aktualizace nezdařila z důvodu souboru DLL uzamčeného hostitelem zprostředkovatele rozhraní WMI.

- Opravili jsme problém ve službě aktualizace, který znemožňuje zobrazení dostupných aktualizací na dlaždici aktualizace nebo poskytovateli prostředků. Tento problém byl nalezen v 1906 a opraven v [KB4511282](https://support.microsoft.com/help/4511282/)oprav hotfix.

- Opravili jsme problém, který by mohl způsobit selhání aktualizací z důvodu nesprávné konfigurace roviny správy, která je v pořádku. Tento problém byl nalezen v 1906 a opraven v [KB4512794](https://support.microsoft.com/help/4512794/)oprav hotfix.

- Opravili jsme problém, který uživatelům zabránil v dokončení nasazení imagí od jiných výrobců z webu Marketplace. Tento problém byl nalezen v 1906 a opraven v [KB4511259](https://support.microsoft.com/help/4511259/)oprav hotfix.

- Opravili jsme problém, který by mohl způsobit selhání vytváření virtuálních počítačů ze spravovaných imagí z důvodu chyby služby správce uživatelských imagí. Tento problém byl nalezen v 1906 a opraven v opravě hotfix [KB4512794](https://support.microsoft.com/help/4512794/)

- Opravili jsme problém, kdy se operace CRUD virtuálních počítačů nepovedly kvůli neobnovování mezipaměti služby App Gateway. Tento problém byl nalezen v 1906 a opraven v opravě hotfix [KB4513119](https://support.microsoft.com/en-us/help/4513119/)

- Opravili jsme problém poskytovatele prostředků stavu, který ovlivnil dostupnost oblasti a oken výstrah na portálu pro správu. Tento problém byl nalezen v 1906 a opraven v [KB4512794](https://support.microsoft.com/help/4512794)oprav hotfix.

## <a name="security-updates"></a>Aktualizace zabezpečení

Informace o aktualizacích zabezpečení v této aktualizaci Azure Stack najdete v tématu [Azure Stack aktualizace zabezpečení](../release-notes-security-updates.md).

## <a name="download-the-update"></a>Stáhnout aktualizaci

Balíček aktualizace Azure Stack 1907 můžete stáhnout ze [stránky pro stažení Azure Stack](https://aka.ms/azurestackupdatedownload).

## <a name="hotfixes"></a>Opravy hotfix

Azure Stack pravidelně vydává opravy hotfix. Před aktualizací Azure Stack na 1907 nezapomeňte nainstalovat nejnovější Azure Stack opravu hotfix pro 1906.

Azure Stack oprav hotfix se týkají pouze Azure Stack integrovaných systémů. Nepokoušejte se instalovat opravy hotfix na ASDK.

### <a name="before-applying-the-1907-update"></a>Před použitím aktualizace 1907

Vydání verze 1907 Azure Stack se musí použít ve verzi 1906 s následujícími opravami hotfix:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack opravy hotfix 1.1906.15.60](https://support.microsoft.com/help/4524559)

### <a name="after-successfully-applying-the-1907-update"></a>Po úspěšné instalaci aktualizace 1907

Po instalaci této aktualizace nainstalujte všechny příslušné opravy hotfix. Další informace najdete v našich [zásadách obsluhy](../azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack opravy hotfix 1.1907.17.54](https://support.microsoft.com/help/4523826)