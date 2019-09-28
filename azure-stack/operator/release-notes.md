---
title: Poznámky k verzi Azure Stack | Microsoft Docs
description: Seznamte se s aktualizacemi Azure Stack integrovaných systémů, včetně co je nového a kde stáhnout aktualizaci.
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
ms.date: 09/27/2019
ms.author: sethm
ms.reviewer: prchint
ms.lastreviewed: 08/30/2019
ms.openlocfilehash: 62440c61ee637a0092b1e956b08ba0a9c94d8bab
ms.sourcegitcommit: c2ea4ffb42563c26faaf2993ba7b484bcb6d5cb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71342935"
---
# <a name="azure-stack-updates-release-notes"></a>Aktualizace Azure Stack: poznámky k verzi

*Platí pro: Azure Stack integrovaných systémů*

Tento článek popisuje obsah balíčků aktualizací Azure Stack. Tato aktualizace obsahuje novinky a opravy pro tuto verzi Azure Stack.

Pokud chcete získat přístup k poznámkám k verzi pro jinou verzi, použijte rozevírací nabídku selektor verzí nad obsahem vlevo.

::: moniker range=">=azs-1905"
> [!IMPORTANT]  
> Tento balíček aktualizace je pouze pro integrované systémy Azure Stack. Tento balíček aktualizace nevztahují na Azure Stack Development Kit.
::: moniker-end
::: moniker range="<azs-1905"
> [!IMPORTANT]  
> Pokud je vaše instance Azure Stack za více než dvěma aktualizacemi, je považována za nedodržující předpisy. Aby bylo možné [získat podporu, musíte aktualizovat aspoň minimální podporovanou verzi](azure-stack-servicing-policy.md#keep-your-system-under-support). 
::: moniker-end

## <a name="update-planning"></a>Plánování aktualizací

Před použitím této aktualizace se ujistěte, že si provedete následující informace:

- [Známé problémy](known-issues.md)
- [Aktualizace zabezpečení](release-notes-security-updates.md)
- [Kontrolní seznam aktivit před a po instalaci aktualizace](release-notes-checklist.md)

Nápovědu k řešení potíží s aktualizacemi a procesu aktualizace najdete v tématu [řešení potíží se opravami a aktualizacemi pro Azure Stack](azure-stack-updates-troubleshoot.md).

<!---------------------------------------------------------->
<!------------------- SUPPORTED VERSIONS ------------------->
<!---------------------------------------------------------->
::: moniker range="azs-1908"
## <a name="1908-build-reference"></a>1908 – odkaz na sestavení

Číslo buildu aktualizace Azure Stack 1908 je **1.1908.4.33**.

### <a name="update-type-1908"></a>Typ aktualizace

V 1908 se základní operační systém, na kterém Azure Stack běžet, aktualizoval na Windows Server 2019. To umožňuje základní vylepšení základních funkcí a také možnost přinášet další možnosti Azure Stack v blízké budoucnosti.

Typ sestavení aktualizace Azure Stack 1908 je **plný**. V důsledku toho má aktualizace 1908 delší dobu běhu než expresní aktualizace jako 1906 a 1907. Přesné moduly runtime pro úplné aktualizace obvykle závisí na počtu uzlů, které vaše instance Azure Stack obsahuje, na kapacitě používané v systému podle úloh klientů, na síťovém připojení vašeho systému (Pokud je připojené k Internetu) a na vašem systémovém hardwaru. rozšířeného. Aktualizace 1908 měla v našem interním testování následující očekávané moduly runtime: 4 uzly – 42 hodin, 8 uzlů – 50 hodin, 12 uzlů – 60 hodin, 16 uzlů – 70 hodiny. Aktualizační moduly, které trvají déle než tyto očekávané hodnoty, nejsou Neběžné a nevyžadují akci Azure Stack operátory, pokud aktualizace neproběhne úspěšně.

Další informace o typech sestavení aktualizací najdete v tématu [Správa aktualizací v Azure Stack](azure-stack-updates.md).

- Přesné běhové moduly pro aktualizaci jsou obvykle závislé na kapacitě používané systémem v rámci úloh klientů, připojení k systémové síti (Pokud je připojeno k Internetu) a hardwarové konfiguraci systému.
- Běhové moduly, které trvají déle než obvykle, nejsou běžné a nevyžadují akci Azure Stack operátory, pokud se aktualizace nezdařila.
- Tato přibližná doba běhu je specifická pro aktualizaci 1908 a neměla by se porovnávat s jinými aktualizacemi Azure Stack.

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new-1908"></a>Co je nového

<!-- What's new, also net new experiences and features. -->

- Pro 1908 si všimněte, že základní operační systém, na kterém Azure Stack běžet, byl aktualizován na Windows Server 2019. To umožňuje základní vylepšení základních funkcí a také možnost přinášet další možnosti Azure Stack v blízké budoucnosti.
- Všechny součásti infrastruktury Azure Stack nyní fungují v režimu FIPS 140-2.
- Azure Stack operátory teď můžou odebrat uživatelská data portálu. Další informace najdete v tématu [vymazání uživatelských dat portálu z Azure Stack](azure-stack-portal-clear.md).

### <a name="improvements-1908"></a>Vylepšen

<!-- Changes and product improvements with tangible customer-facing value. -->
- Vylepšení v oblasti šifrování uložených dat Azure Stack pro zachování tajných kódů do hardwaru Trusted Platform Module (TPM) fyzických uzlů.

### <a name="changes-1908"></a>Provedeny

- Poskytovatelé hardwaru budou vycházet z balíčku rozšíření OEM 2,1 nebo novějšího ve stejnou dobu jako verze Azure Stack 1908. Balíček rozšíření OEM 2,1 nebo novější je předpokladem pro Azure Stack verze 1908. Další informace o tom, jak stáhnout balíček rozšíření OEM 2,1 nebo novější, získáte od poskytovatele hardwaru vašeho systému a v článku věnovaném [aktualizacím OEM](azure-stack-update-oem.md#oem-contact-information) .  

### <a name="fixes-1908"></a>Řeší

- Opravili jsme problém s kompatibilitou s budoucími Azure Stack aktualizacemi OEM a problémem s nasazením virtuálního počítače pomocí uživatelských imagí zákazníka. Tento problém byl nalezen v 1907 a opraven v opravě hotfix [KB4517473](https://support.microsoft.com/en-us/help/4517473/azure-stack-hotfix-1-1907-12-44)  
- Opravili jsme problém s aktualizací firmwaru OEM a opravili jsme chybnou diagnostiku v test-AzureStack pro Fabric Ring stav. Tento problém byl nalezen v 1907 a opraven v opravě hotfix [KB4515310](https://support.microsoft.com/en-us/help/4515310/azure-stack-hotfix-1-1907-7-35)
- Opravili jsme problém s procesem aktualizace firmwaru OEM. Tento problém byl nalezen v 1907 a opraven v opravě hotfix [KB4515650](https://support.microsoft.com/en-us/help/4515650/azure-stack-hotfix-1-1907-8-37)


<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

## <a name="security-updates-1908"></a>Aktualizace zabezpečení

Informace o aktualizacích zabezpečení v této aktualizaci Azure Stack najdete v tématu [Azure Stack aktualizace zabezpečení](release-notes-security-updates.md).

## <a name="download-the-update-1908"></a>Stáhnout aktualizaci

Balíček aktualizace Azure Stack 1908 můžete stáhnout ze [stránky pro stažení Azure Stack](https://aka.ms/azurestackupdatedownload).

## <a name="hotfixes-1908"></a>Opravy hotfix

Azure Stack pravidelně vydává opravy hotfix. Před aktualizací Azure Stack na 1908 nezapomeňte nainstalovat nejnovější Azure Stack opravu hotfix pro 1907.

Azure Stack oprav hotfix se týkají pouze Azure Stack integrovaných systémů. Nepokoušejte se instalovat opravy hotfix na ASDK.

### <a name="prerequisites-before-applying-the-1908-update"></a>Požadavky: Před použitím aktualizace 1908

Vydání verze 1908 Azure Stack se musí použít ve verzi 1907 s následujícími opravami hotfix:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack opravy hotfix 1.1907.17.54](https://support.microsoft.com/help/4523826)

Aktualizace Azure Stack 1908 vyžaduje **Azure Stack OEM verze 2,1 nebo novější** od poskytovatele hardwaru vašeho systému. Aktualizace OEM zahrnují aktualizace ovladačů a firmwaru pro váš Azure Stack systémový hardware. Další informace o použití aktualizací OEM naleznete v tématu [apply Azure Stack Updates Original Equipment Updates](azure-stack-update-oem.md) .

### <a name="after-successfully-applying-the-1908-update"></a>Po úspěšné instalaci aktualizace 1908

Po instalaci této aktualizace nainstalujte všechny příslušné opravy hotfix. Další informace najdete v našich [zásadách obsluhy](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- Pro 1908 není k dispozici žádná oprava hotfix.
::: moniker-end

::: moniker range="azs-1907"
## <a name="1907-build-reference"></a>1907 – odkaz na sestavení

Číslo buildu aktualizace Azure Stack 1907 je **1.1907.0.20**.

### <a name="update-type-1907"></a>Typ aktualizace

Typ sestavení aktualizace Azure Stack 1907 je **Express**. Další informace o typech sestavení aktualizace naleznete v článku [Správa aktualizací v Azure Stack](azure-stack-updates.md) . Na základě interního testování je očekávaný čas potřebný k dokončení aktualizace 1907 přibližně 13 hodin.

- Přesné běhové moduly pro aktualizaci jsou obvykle závislé na kapacitě používané systémem v rámci úloh klientů, připojení k systémové síti (Pokud je připojeno k Internetu) a hardwarové konfiguraci systému.
- Běhové moduly, které trvají déle než obvykle, nejsou běžné a nevyžadují akci Azure Stack operátory, pokud se aktualizace nezdařila.
- Tato přibližná doba běhu je specifická pro aktualizaci 1907 a neměla by se porovnávat s jinými aktualizacemi Azure Stack.

## <a name="whats-in-this-update-1907"></a>Co je v této aktualizaci

<!-- The current theme (if any) of this release. -->

### <a name="whats-new-1907"></a>Co je nového

<!-- What's new, also net new experiences and features. -->

- Verze služby pro diagnostiku protokolu Azure Stack pro obecné dostupnosti pro usnadnění a zlepšení shromažďování protokolů diagnostiky. Služba Azure Stack Diagnostic log Collection nabízí zjednodušený způsob shromažďování a sdílení diagnostických protokolů pomocí služeb zákaznické podpory Microsoftu (CSS). Tato služba Shromažďování protokolů diagnostiky poskytuje nové prostředí pro uživatele na portálu Azure Stack správce, které umožňuje operátorům nastavit automatické nahrávání diagnostických protokolů do objektu BLOB úložiště při vyvolání určitých kritických výstrah nebo provedení stejných operace na vyžádání Další informace naleznete v článku [shromažďování diagnostických protokolů](azure-stack-diagnostic-log-collection-overview.md) .

- Verze ověřování infrastruktury Azure Stack sítě v rámci obecné dostupnosti jako součást nástroje pro ověření Azure Stack **test-AzureStack**. Azure Stack síťová infrastruktura bude součástí **AzureStack testu**, aby bylo možné zjistit, jestli dojde k selhání v síťové infrastruktuře Azure Stack. Test kontroluje konektivitu síťové infrastruktury tím, že obchází Azure Stack softwarově definované sítě. Ukazuje připojení z veřejné virtuální IP adresy k nakonfigurovaným serverům DNS pro přeposílání, serverům NTP a koncovým bodům identity. Kromě toho kontroluje připojení k Azure při použití Azure AD jako zprostředkovatele identity nebo federovaného serveru při používání služby AD FS. Další informace najdete v článku [Nástroj pro ověření Azure Stack](azure-stack-diagnostic-test.md) .

- Přidání interního postupu pro rotaci tajných kódů pro otočení vnitřních certifikátů protokolu SQL TLS, jak je potřeba při aktualizaci systému.

### <a name="improvements-1907"></a>Vylepšen

<!-- Changes and product improvements with tangible customer-facing value. -->

- Okno Azure Stack aktualizace nyní zobrazuje čas **dokončení posledního kroku** pro aktivní aktualizace. To lze zobrazit tak, že přejdete do okna aktualizace a kliknete na běžící aktualizaci. **Poslední krok** je pak k dispozici v části **Podrobnosti o spuštění aktualizace** .

- Vylepšení akcí operátoru **Start-AzureStack** a **stop-AzureStack** . Čas spuštění Azure Stack se snížil průměrem 50%. Čas pro vypnutí Azure Stack byl snížen o průměr 30%. Průměrná doba spouštění a vypínání zůstane stejná jako počet uzlů, které se zvyšují v jednotce škálování.

- Vylepšené zpracování chyb pro nástroj na odpojeném webu Marketplace. Pokud se stahování při použití **Export-AzSOfflineMarketplaceItem**nezdaří nebo částečně proběhne úspěšně, zobrazí se podrobná chybová zpráva s dalšími podrobnostmi o chybách a krocích při zmírňování.

- Vylepšili jsme výkon při vytváření spravovaných disků z velkého objektu BLOB nebo snímku stránky. Dřív při vytváření velkého disku aktivoval časový limit.  

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/127669774/home -->
- Vylepšená kontroly stavu virtuálních disků před vypnutím uzlu, aby nedocházelo k neočekávanému odpojení virtuálního disku.

- Vylepšené úložiště vnitřních protokolů pro operace správy. Výsledkem je vyšší výkon a spolehlivost během operací správy tím, že minimalizují spotřebu paměti a úložiště interních procesů protokolů. Můžete si také všimnout lepších časů načítání stránky v okně aktualizace na portálu pro správu. V rámci tohoto vylepšení už nebudou v systému k dispozici protokoly aktualizací starší než 6 měsíců. Pokud pro tyto aktualizace požadujete protokoly, nezapomeňte [si stáhnout Shrnutí](azure-stack-apply-updates.md) pro všechny aktualizace, které jsou starší než 6 měsíců před provedením aktualizace 1907.

### <a name="changes-1907"></a>Provedeny

- Azure Stack verze 1907 obsahuje varovnou výstrahu, která dává operátorům pokyn, aby před aktualizací na verzi 1908 aktualizovala balíček OEM svého systému na verzi 2,1 nebo novější. Další informace o tom, jak použít Azure Stack aktualizace OEM, najdete v části [použití Azure Stack původní aktualizace výrobce zařízení](azure-stack-update-oem.md).

- Bylo přidáno nové odchozí pravidlo (HTTPS) pro povolení komunikace pro Azure Stack službu shromažďování protokolů diagnostiky. Další informace najdete v tématu [Azure Stack integrace Datacenter – publikování koncových bodů](azure-stack-integrate-endpoints.md#ports-and-urls-outbound).

- Služba zálohování infrastruktury nyní odstraní částečně nahrané zálohy, pokud umístění externího úložiště vyčerpá kapacitu.

- Zálohy infrastruktury již nezahrnují zálohování dat služby Domain Services. To platí jenom pro systémy, které používají Azure Active Directory jako zprostředkovatele identity.

- Nyní Ověřujeme, že se obrázek ingestuje do okna **COMPUTE-> bitové kopie virtuálního počítače** , který je typu objekt blob stránky.

### <a name="fixes-1907"></a>Řeší

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

## <a name="security-updates-1907"></a>Aktualizace zabezpečení

Informace o aktualizacích zabezpečení v této aktualizaci Azure Stack najdete v tématu [Azure Stack aktualizace zabezpečení](release-notes-security-updates.md).

## <a name="update-planning-1907"></a>Plánování aktualizací

Před použitím této aktualizace se ujistěte, že si provedete následující informace:

- [Známé problémy](known-issues.md)
- [Aktualizace zabezpečení](release-notes-security-updates.md)
- [Kontrolní seznam aktivit před a po instalaci aktualizace](release-notes-checklist.md)

## <a name="download-the-update-1907"></a>Stáhnout aktualizaci

Balíček aktualizace Azure Stack 1907 můžete stáhnout ze [stránky pro stažení Azure Stack](https://aka.ms/azurestackupdatedownload).

## <a name="hotfixes-1907"></a>Opravy hotfix

Azure Stack pravidelně vydává opravy hotfix. Před aktualizací Azure Stack na 1907 nezapomeňte nainstalovat nejnovější Azure Stack opravu hotfix pro 1906.

Azure Stack oprav hotfix se týkají pouze Azure Stack integrovaných systémů. Nepokoušejte se instalovat opravy hotfix na ASDK.

### <a name="before-applying-the-1907-update"></a>Před použitím aktualizace 1907

Vydání verze 1907 Azure Stack se musí použít ve verzi 1906 s následujícími opravami hotfix:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack opravy hotfix 1.1906.13.56](https://support.microsoft.com/help/4520375)

### <a name="after-successfully-applying-the-1907-update"></a>Po úspěšné instalaci aktualizace 1907

Po instalaci této aktualizace nainstalujte všechny příslušné opravy hotfix. Další informace najdete v našich [zásadách obsluhy](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack opravy hotfix 1.1907.17.54](https://support.microsoft.com/help/4523826)
::: moniker-end

::: moniker range="azs-1906"
## <a name="1906-build-reference"></a>1906 – odkaz na sestavení

Číslo buildu aktualizace Azure Stack 1906 je **1.1906.0.30**.

### <a name="update-type-1906"></a>Typ aktualizace

Typ sestavení aktualizace Azure Stack 1906 je **Express**. Další informace o typech sestavení aktualizace naleznete v článku [Správa aktualizací v Azure Stack](azure-stack-updates.md) . Očekávaný čas potřebný k dokončení aktualizace 1906 je přibližně 10 hodin, bez ohledu na počet fyzických uzlů v prostředí Azure Stack. Přesné běhové moduly pro aktualizace budou obvykle záviset na kapacitě používané v systému podle zatížení klientů, připojení k systémové síti (Pokud je připojeno k Internetu) a specifikacemi hardwaru systému. Běhové moduly trvající déle než očekávaná hodnota nejsou běžné a nevyžadují akci Azure Stack operátory, pokud se aktualizace nezdařila. Tato přibližná doba běhu je specifická pro aktualizaci 1906 a neměla by se porovnávat s jinými aktualizacemi Azure Stack.

## <a name="whats-in-this-update-1906"></a>Co je v této aktualizaci

<!-- The current theme (if any) of this release. -->

<!-- What's new, also net new experiences and features. -->

- Do privilegovaného koncového bodu (PEP) se přidala rutina **set-TLSPolicy** , která vynutí TLS 1,2 u všech koncových bodů. Další informace najdete v tématu [Azure Stack kontroly zabezpečení](azure-stack-security-configuration.md).

- Do privilegovaného koncového bodu (PEP) se přidala rutina **Get-TLSPolicy** , která načte použité zásady TLS. Další informace najdete v tématu [Azure Stack kontroly zabezpečení](azure-stack-security-configuration.md).

- Přidání interního postupu pro rotaci tajných kódů pro otočení vnitřních certifikátů TLS podle požadavků při aktualizaci systému.

- Přidání ochrany, která zabraňuje vypršení platnosti interních tajných kódů tím, že vynucuje interní rotaci tajných kódů pro případ, že kritická výstraha na vypršení platnosti Tato činnost by se neměla spoléhat jako na běžný operační postup. Rotace tajných kódů by měla být naplánována během časového období údržby. Další informace najdete v tématu [Azure Stack rotaci tajných klíčů](azure-stack-rotate-secrets.md).

- Visual Studio Code se teď podporuje s nasazením Azure Stack pomocí AD FS.

### <a name="improvements-1906"></a>Vylepšen

<!-- Changes and product improvements with tangible customer-facing value. -->

- Rutina **Get-GraphApplication** v privilegovaném koncovém bodu teď zobrazuje kryptografický otisk aktuálně používaného certifikátu. To zlepšuje správu certifikátů pro instanční objekty, když Azure Stack nasadíte pomocí AD FS.

- Byla přidána nová pravidla monitorování stavu, která ověřují dostupnost služby AD Graph a AD FS, včetně možnosti vyvolat výstrahy.

- Vylepšení spolehlivosti poskytovatele prostředků zálohování, když se služba infrastruktura zálohování přesune na jinou instanci.

- Optimalizace výkonu pro postup při rotaci externích tajných klíčů, který poskytuje jednotnou dobu provádění, která usnadňuje plánování časového období údržby.

- Rutina **test-AzureStack** nyní hlásí interní tajné klíče, jejichž platnost brzy vyprší (kritické výstrahy).

- V rámci privilegovaného koncového bodu je k dispozici nový parametr pro rutinu **Register-CustomAdfs** , která umožňuje přeskočí kontrolu seznamu odvolaných certifikátů při konfiguraci důvěryhodnosti federace pro AD FS.

- Vydání 1906 přináší lepší přehled o průběhu aktualizace, takže můžete mít jistotu, že se aktualizace nepozastavuje. Výsledkem je zvýšení celkového počtu kroků aktualizace zobrazených u operátorů v okně **aktualizace** . Můžete si také všimnout dalších kroků aktualizace, které probíhají paralelně než v předchozích aktualizacích.

#### <a name="networking-updates-1906"></a>Aktualizace sítě

- Byla aktualizována doba zapůjčení nastavená v respondéru DHCP, aby byla konzistentní s Azure.

- Vylepšené sazby opakování pro poskytovatele prostředků ve scénáři selhání nasazení prostředků.

- Odebrali jsme možnost **Standard** SKU z nástroje pro vyrovnávání zatížení i z veřejné IP adresy, protože se v tuto chvíli nepodporuje.

### <a name="changes-1906"></a>Provedeny

- Vytvoření prostředí účtu úložiště je teď konzistentní s Azure.

- Aktivační události změněné výstrahy pro vypršení platnosti interních tajných kódů:
  - Upozornění na upozornění se teď zvyšují 90 dní před vypršením platnosti tajných kódů.
  - Kritické výstrahy jsou nyní vyvolány 30 dní před vypršením platnosti tajných kódů.

- Aktualizované řetězce v poskytovateli prostředků zálohování infrastruktury pro konzistentní terminologii.

### <a name="fixes-1906"></a>Řeší

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- Opravili jsme problém, kdy změna velikosti virtuálního počítače spravovaného disku selhala s **chybou vnitřní operace**.

- Opravili jsme problém, kdy vytvoření image uživatele, která selhala, umístí službu, která spravuje image, je ve špatném stavu. Tím se odstraní neúspěšné image a vytváření nových imagí. Tato oprava je také opravena v 1905.

- Aktivní výstrahy týkající se vypršení platnosti interních tajných kódů se teď po úspěšném provedení interního přestupnosti automaticky zavřou.

- Opravili jsme problém, ve kterém doba aktualizace na kartě Historie aktualizace ořízne první číslici, pokud byla aktualizace spuštěná déle než 99 hodin.

- Okno **aktualizace** obsahuje možnost **obnovení** pro neúspěšné aktualizace.

- V portálu pro správu a uživatele byly vyřešeny problémy v Marketplace, ve kterých bylo rozšíření Docker nesprávně vráceno ze služby Search, ale nebylo možné provést žádnou další akci, protože není k dispozici v Azure Stack.

- Opravili jsme problém v uživatelském rozhraní nasazení šablony, které neplní parametry, pokud název šablony začíná podtržítkem _.

- Opravili jsme problém, kdy prostředí pro vytváření sady škálování virtuálních počítačů poskytuje CentOS 7,2 jako možnost nasazení. CentOS 7,2 není na Azure Stack k dispozici. Jako naši možnost pro nasazení teď poskytujeme CentOS 7,5.

- V okně **Virtual Machine Scale Sets** teď můžete odebrat sadu škálování.

## <a name="security-updates-1906"></a>Aktualizace zabezpečení

Informace o aktualizacích zabezpečení v této aktualizaci Azure Stack najdete v tématu [Azure Stack aktualizace zabezpečení](release-notes-security-updates.md).

## <a name="update-planning-1906"></a>Plánování aktualizací

Před použitím této aktualizace se ujistěte, že si provedete následující informace:

- [Známé problémy](known-issues.md)
- [Aktualizace zabezpečení](release-notes-security-updates.md)
- [Kontrolní seznam aktivit před a po instalaci aktualizace](release-notes-checklist.md)

## <a name="download-the-update-1906"></a>Stáhnout aktualizaci

Balíček aktualizace Azure Stack 1906 můžete stáhnout ze [stránky pro stažení Azure Stack](https://aka.ms/azurestackupdatedownload).

## <a name="hotfixes-1906"></a>Opravy hotfix

Azure Stack pravidelně vydává opravy hotfix. Před aktualizací Azure Stack na 1906 nezapomeňte nainstalovat nejnovější Azure Stack opravu hotfix pro 1905. Po aktualizaci nainstalujte všechny [dostupné opravy hotfix pro 1906](#after-successfully-applying-the-1906-update).

Azure Stack oprav hotfix se týkají pouze Azure Stack integrovaných systémů. Nepokoušejte se instalovat opravy hotfix na ASDK.

### <a name="before-applying-the-1906-update"></a>Před použitím aktualizace 1906

Vydání verze 1906 Azure Stack se musí použít ve verzi 1905 s následujícími opravami hotfix:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack opravy hotfix 1.1905.3.48](https://support.microsoft.com/help/4510078)

### <a name="after-successfully-applying-the-1906-update"></a>Po úspěšné instalaci aktualizace 1906

Po instalaci této aktualizace nainstalujte všechny příslušné opravy hotfix. Další informace najdete v našich [zásadách obsluhy](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack opravy hotfix 1.1906.13.56](https://support.microsoft.com/help/4520375)
::: moniker-end

::: moniker range="azs-1905"
## <a name="1905-build-reference"></a>1905 – odkaz na sestavení

Číslo buildu aktualizace Azure Stack 1905 je **1.1905.0.40**.

### <a name="update-type-1905"></a>Typ aktualizace

Typ sestavení aktualizace Azure Stack 1905 je **plný**. V důsledku toho má aktualizace 1905 delší dobu běhu než expresní aktualizace jako 1903 a 1904. Přesné moduly runtime pro úplné aktualizace obvykle závisí na počtu uzlů, které vaše instance Azure Stack obsahuje, na kapacitě používané v systému podle úloh klientů, na síťovém připojení vašeho systému (Pokud je připojené k Internetu) a na vašem systémovém hardwaru. rozšířeného. Aktualizace 1905 měla v našem interním testování následující očekávané moduly runtime: 4 uzly – 35 hodin, 8 uzlů – 45 hodin, 12 uzlů – 55 hodin, 16 uzlů – 70 hodiny. 1905 za běhu trvající delší dobu než tyto očekávané hodnoty nejsou běžné a nevyžadují akci Azure Stack operátory, pokud se aktualizace nezdařila. Další informace o typech sestavení aktualizací najdete v tématu [Správa aktualizací v Azure Stack](azure-stack-updates.md).

## <a name="whats-in-this-update-1905"></a>Co je v této aktualizaci

<!-- The current theme (if any) of this release. -->

<!-- What's new, also net new experiences and features. -->

- V této aktualizaci může aktualizační stroj v Azure Stack aktualizovat firmware uzlů jednotek škálování. To vyžaduje kompatibilní balíček aktualizace od hardwarových partnerů. Obraťte se na svého hardwarového partnera, který vám umožní získat podrobnosti o dostupnosti.

- Windows Server 2019 je teď podporovaný a dostupný pro syndikátování prostřednictvím Azure Stack Marketplace.
V této aktualizaci se teď dá úspěšně aktivovat Windows Server 2019 na hostiteli 2016.

- Nové [rozšíření Visual Studio Code účtu Azure](../user/azure-stack-dev-start-vscode-azure.md) umožňuje vývojářům cílit Azure Stack přihlášením a zobrazením předplatných a také mnoha dalších služeb. Rozšíření účtu Azure funguje na obou prostředích Azure Active Directory (Azure AD) i AD FS a vyžaduje jenom malou změnu v Visual Studio Code nastavení uživatele. Aby bylo možné spustit v tomto prostředí, Visual Studio Code vyžaduje, aby byl objekt služby udělen oprávnění. Provedete to tak, že naimportujete skript identity a v Azure Stack spustíte rutiny určené pro [více tenantů](../operator/azure-stack-enable-multitenancy.md). To vyžaduje aktualizaci domovského adresáře a registraci adresáře klienta hosta pro každý adresář. Po aktualizaci na 1905 nebo novější se zobrazí výstraha, aby se aktualizoval tenant domovského adresáře, pro který je zahrnutý Visual Studio Code instančního objektu. 

### <a name="improvements-1905"></a>Vylepšen

<!-- Changes and product improvements with tangible customer-facing value. -->
- V rámci vynucování TLS 1,2 na Azure Stack se následující rozšíření aktualizovala na tyto verze:

  - microsoft.customscriptextension-arm-1.9.3
  - microsoft.iaasdiagnostics-1.12.2.2
  - microsoft.antimalware-windows-arm-1.5.5.9
  - microsoft.dsc-arm-2.77.0.0
  - microsoft.vmaccessforlinux-1.5.2

  Stáhněte si prosím tyto verze rozšíření hned, aby nová nasazení rozšíření nebyla úspěšná, když se TLS 1,2 vynutil v budoucí verzi. Vždy nastavte **autoUpgradeMinorVersion = true** , aby se automaticky prováděla aktualizace dílčí verze na rozšíření (například 1,8 až 1,9).

- Nový **Přehled Nápověda a podpora** na portálu Azure Stack usnadňuje operátorům kontrolovat jejich možnosti podpory, získat odbornou pomoc a získat další informace o Azure Stack. V případě integrovaných systémů bude při vytváření žádosti o podporu předem vybraná služba Azure Stack. Důrazně doporučujeme, aby zákazníci používali toto prostředí k odeslání lístků místo použití globálních Azure Portal. Další informace najdete v tématu [Azure Stack nápovědě a podpoře](azure-stack-help-and-support-overview.md).

- Při připojování více adresářů Azure Active Directory (prostřednictvím [tohoto procesu](azure-stack-enable-multitenancy.md)) je možné opomíjení znovu spustit skript, když dojde k určitým aktualizacím, nebo když změny v autorizaci instančního objektu služby Azure AD způsobují chybějící práva. To může způsobit různé problémy, od blokovaného přístupu k určitým funkcím až po více diskrétních selhání, u kterých je obtížné sledovat původní problém. Chcete-li tomu zabránit, 1905 zavádí novou funkci, která kontroluje tato oprávnění a vytvoří výstrahu, když budou zjištěny určité problémy s konfigurací. Toto ověření se spouští každou hodinu a zobrazuje nápravné akce potřebné k vyřešení problému. Výstraha se zavře, jakmile jsou všichni klienti v dobrém stavu.

- Zlepšená spolehlivost operací zálohování infrastruktury během převzetí služeb při selhání.

- K dispozici je nová verze [modulu plug-in Azure Stack Nagios](azure-stack-integrate-monitor.md#integrate-with-nagios) , která pro ověřování používá [knihovny Azure Active Directory Authentication](/azure/active-directory/develop/active-directory-authentication-libraries) Library (ADAL). Modul plug-in teď podporuje taky nasazení služby Azure AD a Active Directory Federation Services (AD FS) (AD FS) Azure Stack. Další informace najdete na webu [Exchange plugin Nagios](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details) .

- Nový hybridní profil **2019-03-01 –** byl vydán hybrid, který podporuje všechny nejnovější funkce v Azure Stack. Azure PowerShell i rozhraní příkazového řádku Azure podporují **hybridní profil 2019-03-01** . Sady SDK pro .NET, Ruby, Node. js, přejít a Python obsahují publikované balíčky podporující profil **2019-03-01-Hybrid** . Příslušná dokumentace a některé ukázky byly aktualizovány, aby odrážely změny.

- [Sada SDK pro Node. js](https://www.npmjs.com/search?q=2019-03-01-hybrid) teď podporuje profily rozhraní API. Jsou publikovány balíčky podporující profil **2019-03-01-Hybrid** .

- 1905 Azure Stack aktualizace přináší dvě nové role infrastruktury pro zlepšení spolehlivosti a podpory platforem:

  - **Kanál infrastruktury**: V budoucnu bude kanál infrastruktury hostovat verze stávajících rolí infrastruktury (například XRP), které aktuálně vyžadují vlastní určené virtuální počítače infrastruktury. Tím se Vylepšete spolehlivost platformy a sníží se počet virtuálních počítačů infrastruktury, které Azure Stack vyžaduje. Tím se následně omezí celková spotřeba prostředků Azure Stack rolí infrastruktury v budoucnu.
  - **Aktualizační kanál podpory**: V budoucnu se ke zpracování lepších scénářů podpory pro zákazníky použije aktualizační kanál podpory.  

  Kromě toho jsme přidali další instanci virtuálního počítače řadiče domény, aby se zlepšila dostupnost této role.

  Tyto změny zvyšují spotřebu prostředků Azure Stack infrastruktury následujícími způsoby:
  
    | SKU Azure Stack | Zvýšení výpočetní spotřeby | Zvýšení spotřeby paměti |
    | -- | -- | -- |
    |4 uzly|22 vCPU|28 GB|
    |8 uzlů|38 vCPU|44 GB|
    |12 uzlů|54 vCPU|60 GB|
    |16 uzlů|70 vCPU|76 GB|
  
### <a name="changes-1905"></a>Provedeny

- Aby bylo možné zvýšit spolehlivost a dostupnost během plánovaných a neplánovaných scénářů údržby, Azure Stack přidá další instanci role infrastruktury pro doménové služby.

- Během této aktualizace během operace opravit a přidat uzel se ověří hardware, aby se zajistila homogenní uzly jednotek škálování v rámci jednotky škálování.

- Pokud se naplánované zálohy nedaří dokončit a dojde k překročení definované doby uchovávání, kontroler zálohování infrastruktury zajistí, že se zachová aspoň jedna úspěšná záloha. 

### <a name="fixes-1905"></a>Řeší

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- Opravili jsme problém, ve kterém se zobrazilo upozornění **agenta COMPUTE Host** po restartování uzlu v jednotce škálování.

- Opravili jsme problémy se správou Marketplace na portálu pro správu, který ukázal nesprávné výsledky při použití filtrů, a v filtru vydavatele se zobrazily duplicitní názvy vydavatelů. Také vylepšení výkonu pro rychlejší zobrazení výsledků.

- Opravili jsme problém v okně dostupná záloha, které uvádí nové dostupné zálohování, než se dokončí odeslání do umístění externího úložiště. Po úspěšném nahrání do umístění úložiště se teď dostupná záloha zobrazí v seznamu. 

<!-- ICM: 114819337; Task: 4408136 -->
- Opravili jsme problém při načítání obnovovacích klíčů během operace zálohování. 

<!-- Bug: 4525587 -->
- Oprava potíží s aktualizací OEM, která zobrazuje verzi jako nedefinované, na portálu operátora

### <a name="security-updates-1905"></a>Aktualizace zabezpečení

Informace o aktualizacích zabezpečení v této aktualizaci Azure Stack najdete v tématu [Azure Stack aktualizace zabezpečení](release-notes-security-updates.md).

## <a name="update-planning-1905"></a>Plánování aktualizací

Před použitím této aktualizace se ujistěte, že si provedete následující informace:

- [Známé problémy](known-issues.md)
- [Aktualizace zabezpečení](release-notes-security-updates.md)
- [Kontrolní seznam aktivit před a po instalaci aktualizace](release-notes-checklist.md)

## <a name="download-the-update-1905"></a>Stáhnout aktualizaci

Balíček aktualizace Azure Stack 1905 můžete stáhnout ze [stránky pro stažení Azure Stack](https://aka.ms/azurestackupdatedownload). Při použití nástroje pro stahovací program se ujistěte, že používáte nejnovější verzi a nikoli kopii uloženou v mezipaměti z adresáře pro stahování.

## <a name="hotfixes-1905"></a>Opravy hotfix

Azure Stack pravidelně vydává opravy hotfix. Před aktualizací Azure Stack na 1905 nezapomeňte nainstalovat nejnovější Azure Stack opravu hotfix pro 1904.

Azure Stack oprav hotfix se týkají pouze Azure Stack integrovaných systémů. Nepokoušejte se instalovat opravy hotfix na ASDK.

### <a name="before-applying-the-1905-update"></a>Před použitím aktualizace 1905

Vydání verze 1905 Azure Stack se musí použít ve verzi 1904 s následujícími opravami hotfix:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack opravy hotfix 1.1904.4.45](https://support.microsoft.com/help/4505688)

### <a name="after-successfully-applying-the-1905-update"></a>Po úspěšné instalaci aktualizace 1905

Po instalaci této aktualizace nainstalujte všechny příslušné opravy hotfix. Další informace najdete v našich [zásadách obsluhy](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack opravy hotfix 1.1905.3.48](https://support.microsoft.com/help/4510078)
::: moniker-end

::: moniker range=">=azs-1905"
## <a name="automatic-update-notifications"></a>Oznámení automatických aktualizací

Systémy, které mají přístup k Internetu ze sítě infrastruktury, uvidí na portálu operátora zprávu **k aktualizaci k dispozici** . Systémy bez přístupu k Internetu můžou stáhnout a naimportovat soubor. zip s odpovídající příponou. XML.

> [!TIP]  
> Přihlaste se k odběru následujících informačních kanálů *RSS* nebo *Atom* , abyste zachovali Azure Stack opravy hotfix:
>
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Počtu](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

## <a name="archive"></a>Archiv

Pokud chcete získat přístup k archivovaným dokumentům k verzi pro starší verzi, použijte rozevírací nabídku selektor verzí nad obsahem vlevo a vyberte verzi, kterou chcete zobrazit.

## <a name="next-steps"></a>Další kroky

- Přehled správy aktualizací ve službě Azure Stack najdete v tématu [správy aktualizací ve službě Azure Stack přehled](azure-stack-updates.md).  
- Další informace o tom, jak použít aktualizace pomocí služby Azure Stack najdete v tématu [použití aktualizací ve službě Azure Stack](azure-stack-apply-updates.md).
- Zásady údržby pro integrované systémy Azure Stack, a co musíte udělat, aby byl váš systém v podporovaném stavu najdete v tématu [Azure Stack zásady obsluhy](azure-stack-servicing-policy.md).  
- Privilegované koncový bod (období) použít ke sledování a obnovit aktualizace, najdete v článku [monitorování aktualizací ve službě Azure Stack pomocí privilegovaných koncového bodu](azure-stack-monitor-update.md).
::: moniker-end

<!------------------------------------------------------------>
<!------------------- UNSUPPORTED VERSIONS ------------------->
<!------------------------------------------------------------>
::: moniker range="azs-1904"
## <a name="1904-archived-release-notes"></a>1904 archivované poznámky k verzi
::: moniker-end
::: moniker range="azs-1903"
## <a name="1903-archived-release-notes"></a>1903 archivované poznámky k verzi
::: moniker-end
::: moniker range="azs-1902"
## <a name="1902-archived-release-notes"></a>1902 archivované poznámky k verzi
::: moniker-end
::: moniker range="azs-1901"
## <a name="1901-archived-release-notes"></a>1901 archivované poznámky k verzi
::: moniker-end
::: moniker range="azs-1811"
## <a name="1811-archived-release-notes"></a>1811 archivované poznámky k verzi
::: moniker-end
::: moniker range="azs-1809"
## <a name="1809-archived-release-notes"></a>1809 archivované poznámky k verzi
::: moniker-end
::: moniker range="azs-1808"
## <a name="1808-archived-release-notes"></a>1808 archivované poznámky k verzi
::: moniker-end
::: moniker range="azs-1807"
## <a name="1807-archived-release-notes"></a>1807 archivované poznámky k verzi
::: moniker-end
::: moniker range="azs-1805"
## <a name="1805-archived-release-notes"></a>1805 archivované poznámky k verzi
::: moniker-end
::: moniker range="azs-1804"
## <a name="1804-archived-release-notes"></a>1804 archivované poznámky k verzi
::: moniker-end
::: moniker range="azs-1803"
## <a name="1803-archived-release-notes"></a>1803 archivované poznámky k verzi
::: moniker-end
::: moniker range="azs-1802"
## <a name="1802-archived-release-notes"></a>1802 archivované poznámky k verzi
::: moniker-end

::: moniker range="<azs-1905"
Do Galerie TechNet můžete získat přístup ke [starším verzím Azure Stack](https://aka.ms/azsarchivedrelnotes). Tyto archivované dokumenty jsou k dispozici pouze pro referenční účely a neznamenají podporu těchto verzí. Informace o podpoře Azure Stack najdete v tématu [zásady pro obsluhu Azure Stack](azure-stack-servicing-policy.md). Pokud potřebujete další pomoc, obraťte se na službu zákaznické podpory společnosti Microsoft.
::: moniker-end


