---
title: Vzory hybridního cloudu pomocí služby Azure Stack | Dokumentace Microsoftu
description: Přehled vzorů pro hybridní cloud a inteligentních hraničních zařízení pomocí služby Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 06/11/2019
ms.openlocfilehash: e390e43b02d93690c1e233279d52d03665414bf7
ms.sourcegitcommit: 51ec68b5e6dbf437aaca19a9f35ba07d2c402892
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2019
ms.locfileid: "67856406"
---
# <a name="hybrid-cloud-design-patterns-for-azure-stack"></a>Vzory návrhu v hybridního cloudu pro Azure Stack

Microsoft Azure je jediný konsistentní hybridní cloud. To vám umožní znova využít vaše investice do vývoje a umožňuje aplikacím, které se týkají global Azure, suverénních cloudech Azure a Azure Stack, která je rozšířením Azure ve vašem datovém centru. Aplikace, které jsou rozmístěny cloudy jsou také označovány jako *hybridní aplikace*.

[ *Příručka aplikační architektura v Azure* ](https://docs.microsoft.com/azure/architecture/guide) popisuje strukturovaný přístup k návrhu aplikací, které jsou škálovatelné, odolné a vysoce dostupné. Jaké jsou požadavky popsané v [ *příručka aplikační architektura v Azure* ](https://docs.microsoft.com/azure/architecture/guide) stejně platí pro aplikace, které jsou navržené pro jeden cloud a pro aplikace, které jsou rozmístěny cloudy.

Tento článek argumentech [ *pilířů kvality softwaru* ](https://docs.microsoft.com/azure/architecture/guide/pillars) popsané v [ *aplikace Azure* ](https://docs.microsoft.com/azure/architecture/guide/) [ *Průvodce architekturou*,](https://docs.microsoft.com/azure/architecture/guide/) zaměření konkrétně na návrh hybridní aplikace. Kromě toho můžeme přidat *umístění* pilíř jako hybridní aplikace, které nejsou výhradně pro jeden cloud nebo jednu v místním datovém centru.

Hybridní scénáře značně lišit s prostředky, které jsou k dispozici pro vývoj a zahrnují aspekty, jako je například Geografie, zabezpečení, přístup k Internetu a další důležité informace. Přestože tento průvodce nemůže provést výčet konkrétní aspekty, může poskytnout některé klíče pokyny a osvědčené postupy pro se měli řídit. Navrhování, konfiguraci, nasazování a údržbu hybridní architektury aplikace úspěšně zahrnuje mnoho aspekty návrhu, které nemusí být známé ze své podstaty vám.

Tento dokument má za cíl agregovat možné dotazy, které mohou nastat při implementaci hybridní aplikace a obsahuje důležité informace (tyto pilíře) a osvědčené postupy pro práci s nimi. Které splňuje tyto otázky během fáze návrhu, vyhnete problémy, které by mohly způsobit v produkčním prostředí.

V podstatě jedná se o otázek, na které je potřeba zvážit před vytvořením hybridní aplikace. Abyste mohli začít, musíte provést následující kroky:

-   Identifikujte a zhodnotit součásti aplikace.

-   Vyhodnocení součásti aplikace proti pilíře.

## <a name="evaluate-the-application-components"></a>Vyhodnocení součásti aplikace

Každou komponentu aplikace má vlastní konkrétní roli v rámci větší aplikace a byste měli zkontrolovat, se všechny aspekty návrhu. Požadavky na jednotlivé komponenty a funkce by měla být mapována na tyto aspekty, které pomůžou určit architekturu aplikace.

Zkoumání architektury vaší aplikace a určení, čeho se skládá rozložit aplikace do její složek. Součásti mohou zahrnovat také další aplikace, které vaše aplikace komunikuje s. Po určení komponenty, vyhodnoťte určené hybridní operací podle jejich vlastností, jako je následující:

-   Jaký je účel součásti?

-   Co jsou vzájemné závislosti mezi komponentami?

Například aplikace může mít front-endem a back-end definovány jako dvě součásti. V hybridním scénáři představuje front-endu probíhá jeden cloud a back-end je v jiném. Aplikace

poskytuje komunikační kanály mezi front-endem a uživatelem a také mezi front-endu a back endu.

Součásti aplikace je definován tak, že mnoho formulářů a scénáře. Nejdůležitější úlohy je identifikace je a jejich cloudových nebo místních umístění.

V tabulce 1 jsou uvedeny běžné součásti aplikace zahrnout ve vašem inventáři softwaru.

### <a name="table-1-common-app-components"></a>Tabulka 1. Běžné součásti aplikace


| **Komponenta** | **Doprovodný materiál pro hybridní aplikaci** |
| ---- | ---- |
| Připojení klientů | Aplikace (na všech zařízeních) mají přístup k uživatelům různými způsoby, od jeden vstupní bod, včetně následujících:<br>– Model klient server, který vyžaduje, aby uživatel měl klient nainstalovány pro práci s aplikací. Aplikace založené na serveru, která je přístupná z webového prohlížeče.<br>– Klientská připojení můžete zahrnout oznámení, když připojení bylo přerušeno nebo výstrahy, když vám za něj jim umožňují uplatnit. |
| Ověřování  | Ověřování může být uživatelé připojení k aplikaci, nebo mezi jednotlivými komponentami připojení na jiný. |
| API  | Můžete vývojářům poskytuje programový přístup k vaší aplikaci pomocí sady rozhraní API a knihovny tříd a poskytuje připojení rozhraní podle standardů aplikace Internet. Rozhraní API můžete použít také k je aplikace rozložená do nezávisle na sobě provozní logické jednotky. |
| Služby  | Můžete použít stručné službami a zajistit tak funkce pro aplikaci. Služba může být modul, na které aplikace poběží. |
| Fronty | Fronty slouží k uspořádání stav životní cykly a stavů součástí vaší aplikace. Tyto fronty může poskytnout zasílání zpráv, oznámení a ukládání do vyrovnávací paměti funkce odběratelská stranám. |
| Úložiště dat | Aplikace může být Bezstavová nebo stavová. Stavové aplikace potřebujete úložiště dat, které mohou být splněny řadu formátů a svazky. |
| Ukládání dat do mezipaměti  | Komponenta ukládání do mezipaměti dat v návrhu můžete strategicky řešit problémy s latencí a hrají roli při aktivaci shlukování cloudu. |
| Přijímání dat | Data lze odeslat do aplikace v mnoha způsoby, od uživatel odeslal hodnoty do webového formuláře do průběžně velkého objemu dat toku. |
| Zpracování dat | Vaše úlohy zpracování dat (například sestavy, analýzy, exporty služby batch a transformace dat) můžete být zpracována ve zdrojovém kódu nebo na samostatná komponenta pomocí kopie dat se sníženou zátěží. |

## <a name="assess-app-components-for-pillars"></a>Vyhodnocení komponenty aplikace pro pilíře

Pro každou komponentu vyhodnoťte její vlastnosti pro každého ze čtyř pilířů. Při hodnocení jednotlivé komponenty se všemi pilíře, může stanou známými otázek, na které jste nemusí být považována za vás, které by ovlivnily návrhu hybridní aplikace. Reagovat na tyto aspekty může přidání hodnoty do optimalizace vaší aplikace. Tabulka 2 obsahuje popis každého ze čtyř pilířů totiž souvisí s hybridní aplikace.

### <a name="table-2-pillars"></a>Tabulka 2. Pilíře

| **Pillar** | **Popis** |
| ----------- | --------------------------------------------------------- |
| umístění  | Strategické umístění součásti v hybridních aplikacích. |
| Škálovatelnost  | Schopnost systému zvládnout zvýšenou zátěž |
| Dostupnost  | Podíl času, který hybridní aplikace je funkční a v provozu. |
| Odolnost | Možnost pro hybridní aplikace pro obnovení. |
| Možnosti správy | Provozní procesy, které udržují systém spuštěný v produkčním prostředí. |
| Zabezpečení | Ochrana hybridních aplikací a dat před hrozbami. |

## <a name="placement"></a>umístění

Hybridní aplikace má ze své podstaty potřeba umístění, například pro datového centra.

Umístění je důležitá úloha umístění součásti tak, aby poskytovala nejlépe služby hybridní aplikace. Podle definice hybridní aplikace zahrnují umístění, například z místního do cloudu a mezi různých cloudů. Součásti aplikace lze umístit na cloudy dvěma způsoby:

-   **Svislé hybridních aplikací**  
    Součásti aplikace jsou distribuované mezi umístěními. Jednotlivých komponent může mít více instancí, které se nachází pouze v jednom umístění.

-   **Vodorovné hybridních aplikací**  
    Součásti aplikace jsou distribuované mezi umístěními. Jednotlivých komponent může mít více instancí pokrývající víc umístění.
    
    Některé součásti lze vědět, jejich umístění, zatímco jiné nemají žádnou znalost jejich umístění a umístění. Tato virtuousness lze nastavit pomocí abstraktní vrstvu. Tato vrstva s architektura moderní aplikace, jako jsou mikroslužby, můžete definovat, jak aplikace obsloužena umístění součásti aplikace, které jsou nasazeny na uzly napříč cloudy.

### <a name="placement-checklist"></a>Umístění kontrolního seznamu

**Ověřte požadované umístění.** Ujistěte se, že aplikace nebo některý z jeho součástí jsou nezbytné k provozu v, nebo vyžadovat certifikaci pro konkrétní cloud. To může zahrnovat požadavky na suverenitu ve vaší společnosti nebo podle zákona. Určují také, pokud všechny operace v místním jsou požadovány pro konkrétní umístění nebo národní prostředí.

**Ověření připojení k závislosti.** Připojení k závislosti mezi komponentami vaší může diktovat požadované umístění a dalších faktorů. Při umisťování komponenty, určete optimální připojením a zabezpečením pro komunikaci mezi nimi. Možnosti jsou [ *VPN*,](https://docs.microsoft.com/azure/vpn-gateway/) [ *ExpressRoute*,](https://docs.microsoft.com/azure/expressroute/) a [ *Hybrid Connections*.](https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections)

**Vyhodnocení funkce platformy.** Pro každou komponentu aplikace najdete v článku Pokud požadovaný prostředek zprostředkovatele pro součást aplikace je dostupná v cloudu a šířku pásma zvládne očekávané propustnosti a požadavky na latenci.

**Plán pro přenositelnost.** Použití moderních aplikačních architektur, jako jsou kontejnery nebo mikroslužbami, plánování přesunu operace a zabránit závislosti služby.

**Určete požadavky na suverenitu dat.** Hybridní aplikace jsou zaměřené pro naplnění izolace dat, jako například v místním datovém centru. Zkontrolujte umístění zdroje pro optimalizaci úspěch pro naplnění tohoto požadavku.

**Plán pro latenci.** Operace mezi cloudu můžete zavést fyzická vzdálenost mezi součástmi aplikace. Zjistit požadavky s ohledem všechny latence.

**Ovládací prvek přenosové toky.** Zpracování využití ve špičce a odpovídající a zabezpečenou komunikaci pro osobní identifikovatelné údaje data při přístupu front-endu ve veřejném cloudu.

## <a name="scalability"></a>Škálovatelnost

Škálovatelnost je schopnost systému zvládnout zvýšenou zátěž na aplikace, které můžete jako další faktory a vynutí se liší v čase, vliv na velikost cílové skupiny kromě velikost a oboru aplikace.

Základní informace o tomto pillar, naleznete v tématu [ *škálovatelnost* ](https://docs.microsoft.com/azure/architecture/guide/pillars#scalability) v pilířů kvality softwaru.

Horizontální škálování přístup pro hybridní aplikace umožňuje přidání dalších instancí podle potřeby a potom je zakázat náročném období naopak.

V hybridních scénářích horizontální navýšení kapacity pro jednotlivé součásti nutné ho pečlivě zvážit při komponenty jsou rozděleny mezi cloudy. Škálování jedné části aplikace může vyžadovat škálování jiného. Například pokud číslo zvyšuje připojení klienta, ale aplikace webové služby nejsou škálované vhodným způsobem, zatížení na databázi může saturate aplikace.

Některé součásti aplikací můžete horizontálně navýšit kapacitu lineárně, zatímco jiné mají škálování závislostí a může být omezená co rozšíření je možné škálovat. Například tunelového připojení sítě VPN poskytují hybridní možnosti připojení pro umístění součásti aplikace platí omezení šířky pásma a čekací doba, kterou je možné škálovat na. Jak jsou součásti aplikace škálovat, aby tyto požadavky splněny?

### <a name="scalability-checklist"></a>Kontrolní seznam ke škálovatelnosti

**Ověření prahové hodnoty měřítka.** Pro zpracování různých závislostí ve vaší aplikaci, určíte tak rozsah, do které aplikace součástí jiné cloudy můžete škálovat nezávisle na sobě, současně stále splnit požadavky na spuštění aplikace. Hybridní aplikace často potřebují škálovat na konkrétní oblasti v aplikaci pro zpracování funkce, když pracuje a má vliv na zbytek aplikace. Vyšší než počet front-endových instancí například může vyžadovat škálování back endu.

**Definujte plány škálování.** Většina aplikací má vytíženosti, takže je potřeba započítat jejich špičky do plány pro koordinaci optimálního škálování.

**Používejte centralizované monitorování systému.** Možnosti monitorování platformy může poskytnout automatické škálování, ale hybridní aplikace potřebovat centralizovaného monitorování systému, který agreguje stav systému a zatížení. Centralizované monitorování systému, může iniciovat škálování prostředků na jednom místě a škálování v závislosti na prostředku do jiného umístění. Kromě toho centrální monitorování systému můžete sledovat, což cloudů automatické škálování prostředků a cloudy, které nejsou.

**Využijte možnosti automatického škálování (jako je k dispozici).** Pokud funkce automatického škálování část vaší architektury, implementovat automatického škálování tak, že nastavíte mezní hodnoty, které definují, když je potřeba škálovat nahoru, out, dolů nebo v součásti aplikace. Příklad automatického škálování je připojení klienta, který je došlo ke ztrátě schopnosti v jeden cloud ke zpracování větší kapacitu, ale způsobí, že ostatní závislosti aplikace, rozloženy různých cloudů se také dá škálovat. Možnosti automatického škálování těchto závislých komponent musí zjišťuje.

Pokud automatické škálování není k dispozici, zvažte implementaci skripty a další prostředky, abyste ruční škálování aktivuje prahovými hodnotami v centralizované monitorování systému.

**Určení očekávané zatížení podle umístění.** Hybridní aplikace, které zpracovávají požadavky klientů může primárně využívají na jednom místě. Pokud zatížení požadavky klienta překročí prahovou hodnotu,

je možné přidat další zdroje informací k distribuci zatížení příchozích požadavků do jiného umístění. Ujistěte se, že připojení klientů může zpracovávat větší zatížení a také určit všechny automatické postupy pro připojení klientů pro zpracování zátěže.

## <a name="availability"></a>Dostupnost

Dostupnost je čas, který je systém funkční a v provozu. Dostupnost se měří jako procento doby provozu. Dostupnost mohou snížit chyby aplikace, problémy s infrastrukturou nebo zátěž systému.

Základní informace o tomto pillar, naleznete v tématu [ *dostupnosti* ](https://docs.microsoft.com/azure/architecture/guide/pillars#availability) v pilířů kvality softwaru.

### <a name="availability-checklist"></a>Kontrolní seznam k dostupnosti

**Zajistit redundanci pro připojení.** Hybridní aplikace vyžadují připojení mezi cloudy, které aplikace se pak rozdělí mezi. Můžete vybrat, technologie pro hybridní připojení, proto navíc do zvoleného primární technologii ve jiné technologie poskytnout redundanci automatické převzetí služeb při selhání by selhat jako primární technologii ve.

**Klasifikace domén selhání.** Odolné aplikace vyžadují více domén selhání. Chyby domény pomáhají izolovat bod selhání, třeba když jeden pevný disk selže v místním prostředí, pokud top-of-rack switch ocitne mimo provoz nebo celé datové centrum nedostupný. V hybridní aplikace umístění dají považovat za doménu selhání. Další požadavky na dostupnost tím více musíte vyhodnotit, jak by měly být klasifikované doménu selhání jedné.

**Klasifikace upgradovacích domén.** Upgradovací domény se používají k zajištění, že instance součásti aplikace jsou k dispozici, zatímco ostatní instance stejné komponenty jsou probíhá údržba aktualizace nebo upgrady funkcí. Stejně jako u domén selhání, může být klasifikované podle jejich umístění upgradovacích domén mezi umístěními. Musíte určit, pokud jako součást aplikace zvládne neupgradují na jednom místě v předtím, než dojde k upgradu v jiném umístění nebo pokud potřebujete další konfigurace domény. Na jednom místě, samotný může mít více upgradovacích domén.

**Sledování instancí a dostupnost.** Může být k dispozici prostřednictvím služby Vyrovnávání zatížení a synchronní replikaci dat součásti aplikace s vysokou dostupností. Musíte určit, kolik instancí může být offline, než dojde k přerušení služby.

**Implementujte automatické opravování chyb.** V případě, že problém způsobuje, že přerušení dostupnosti aplikace, zjišťování monitorování systému zahájit samoopravitelné činnosti do aplikace, jako je vyprazdňování chybné instance a jejího opakovaného nasazení. Pravděpodobně tomu je potřeba řešení centrální monitorování, integrovaná s hybridní průběžnou integraci a kanál průběžného doručování (CI/CD). Aplikace je integrované do monitorování systému můžete identifikovat problémy, které by mohly vyžadovat opakované nasazení součásti aplikace. Monitorovací systém může také aktivovat hybridní CI/CD a opětovné nasazení součásti aplikace a potenciálně všechny ostatní závislé součásti ve stejné nebo jiné umístění.

**Udržujte smlouvy o úrovni služeb (SLA).** Dostupnost se žádné smlouvy k zajištění možnosti připojení ke službám a aplikace, které mají s vašimi zákazníky. Každé umístění, která využívá hybridní aplikace může mít svůj vlastní smlouvu SLA. Tyto různé smlouvy o úrovni služeb můžou ovlivnit celkovou úroveň smlouvy SLA hybridní aplikace.

## <a name="resiliency"></a>Odolnost

Odolnost je schopnost pro hybridní aplikace a systém zotavit z chyb a nadále fungovat. Cílem odolnosti proti chybám je obnovení plně funkčního stavu aplikace co nejdříve po selhání. Strategie odolnosti proti chybám patří řešení, jako je zálohování, replikace a zotavení po havárii.

Základní informace o tomto pillar, naleznete v tématu [ *odolnost proti chybám* ](https://docs.microsoft.com/azure/architecture/guide/pillars#resiliency) v pilířů kvality softwaru.

### <a name="resiliency-checklist"></a>Kontrolní seznam k odolnosti

**Odhalte závislosti zotavení po havárii.** Zotavení po havárii v jeden cloud může vyžadovat změny součásti aplikace v jiném cloudu. Pokud při selhání jedné nebo víc součástí z jednoho cloudu do jiného umístění, ve stejném cloudu nebo do jiného cloudu závislé součásti nutné rozpoznat tyto změny. To zahrnuje také připojení závislosti. Odolnost proti chybám vyžaduje plán obnovení plně otestované aplikace pro jednotlivé cloudy.

**Vytvořte tok obnovení.** Při návrhu toku efektivní obnovení byla vyhodnocena součásti aplikace pro jejich schopnost přizpůsobit vyrovnávacích pamětí, opakovaných pokusů, opakování se nezdařilo přenos dat a v případě potřeby vrátit k jiné služby nebo pracovního postupu. Musíte určit, jaký mechanismus zálohování, co zahrnuje jeho obnovení a jak často je testována. Měli byste taky určit frekvenci přírůstkových i úplných záloh.

**Otestujte částečné obnovy.** Částečné obnovení pro součást aplikace může poskytovat uživatelům jistotu, že všechny není k dispozici. Tato část plánu zajistil, že částečného obnovení nemá žádné vedlejší účinky, jako je zálohování a obnovení služby, která interaguje s aplikací řádně vypnout, před provedením zálohy.

**Určení účastenství zotavení po havárii a přiřaďte odpovědnost.** Plán obnovení, měl by popisovat, kdo a jaké role můžete iniciovat akcemi zálohování a obnovení kromě co může zálohovat a obnovit.

**Porovnejte samoopravení prahové hodnoty s zotavení po havárii.** Určení možností samoopravení aplikace pro zahájení automatického obnovení a dobu potřebnou pro aplikace self – opravy, aby bylo považováno za selhání nebo úspěchu. Určení prahových hodnot pro jednotlivé cloudy.

**Ověřte dostupnost, odolnost proti chybám funkce.** Zjistíte dostupnost, odolnost proti chybám funkcí a možností pro každé umístění. Pokud umístění neposkytuje požadované funkce, vezměte v úvahu integrace tohoto umístění do centralizované služby, která poskytuje funkce odolnosti proti chybám.

**Určení výpadky.** Určení očekávaný výpadek kvůli údržbě pro aplikaci jako celek, jako součásti aplikace.

**Řešení potíží s procedurami dokumentu.** Definujte postupy pro řešení potíží pro opakované nasazení prostředků a součásti aplikace.

## <a name="manageability"></a>Možnosti správy

Jaké jsou požadavky na způsob správy hybridních aplikací jsou velmi důležité při návrhu architektury. Dobře spravované hybridní aplikace slouží jako infrastruktura jako kód, který umožňuje integraci kódu konzistentní aplikací ve společné vývojového kanálu. Implementací konzistentní celý systém a jednotlivé testování změny v infrastruktuře, zajistíte integrované nasazovací Pokud změny projdou testy, což jim umožní sloučit do zdrojového kódu.

Základní informace o tomto pillar, naleznete v tématu [ *správy a DevOps* ](https://docs.microsoft.com/azure/architecture/guide/pillars#management-and-devops) v pilířů kvality softwaru.

### <a name="manageability-checklist"></a>Kontrolní seznam možností správy

**Implementace monitorování.** Pomocí systému centralizované monitorování aplikace, kterou součásti rozložit napříč cloudy poskytují souhrnné zobrazení jejich stavu a výkonu. Tento systém zahrnuje monitorování součásti aplikace i funkce související platformy.

Určete části aplikace, které vyžadují monitorování.

**Souřadnice zásady.** Každé umístění, která zahrnuje hybridní aplikace může mít své vlastní zásady, které pokrývá povolené typy prostředků, pojmenování konvence, značek a dalších kritérií.

**Definice a používání rolí.** Jako správce databáze budete muset určit oprávnění požadovaná pro různé osoby (například vlastníka aplikace, správce databáze a koncový uživatel), které potřebují přístup k prostředkům aplikace. Tato oprávnění je potřeba nakonfigurovat s prostředky a uvnitř aplikace. Systém řízení přístupu na základě role můžete tato oprávnění nastavit na prostředky aplikace. Tato přístupová práva jsou náročné, když všechny prostředky nasazené v jedné cloudové ale vyžadují ještě další pozornost při prostředky jsou rozděleny mezi cloudy. Oprávnění na prostředky v jeden cloud se nevztahují na prostředky v jiném cloudu.

**Pomocí kanálů CI/CD.** Průběžná integrace a kanálu kontinuální vývoj (CI/CD) můžete zadat konzistentního procesu pro vytváření a nasazování aplikací, které jsou rozmístěny napříč cloudy a zajištění kvality pro jejich aplikací a infrastruktury. Tento kanál umožňuje infrastruktury a aplikací, které má být testována na jeden cloud a nasazeny v jiném cloudu. Kanál i umožňuje nasadit některé součásti vaší hybridní aplikace pro jeden cloud a další součásti do jiného cloudu, v podstatě tvoří základ pro hybridní nasazení aplikace. CI/CD systému je velmi důležité pro zpracování, které součásti aplikace závislosti mezi sebou mít během instalace, jako jsou například webové aplikace potřebují připojovací řetězec k databázi.

**Správa životního cyklu.** Protože prostředky hybridní aplikace může mít rozsah umístění, možnosti správy životního cyklu jednotlivých jednoho umístění musí být agregován do jedné životního cyklu správy jednotky. Zvažte, jak se vytvářejí, aktualizované a odstraněné.

**Prozkoumejte strategie pro řešení potíží.** Řešení potíží s hybridní aplikace zahrnuje další součásti aplikace než stejnou aplikaci, na kterém běží v jeden cloud. Kromě připojení mezi cloudy aplikace běží na dvou platforem místo jednoho. Důležité úlohy v řešení potíží s hybridní aplikace je prozkoumat agregované stavu a výkonu monitorování součásti aplikace.

## <a name="security"></a>Zabezpečení

Zabezpečení je jedním z primárních důležité informace o všech cloudových aplikací a stane se ještě důležitější pro hybridní cloudové aplikace.

Základní informace o tomto pillar, naleznete v tématu [ *zabezpečení* ](https://docs.microsoft.com/azure/architecture/guide/pillars#security) v pilířů kvality softwaru.

### <a name="security-checklist"></a>Kontrolní seznam zabezpečení

**Předpokládej chybu zabezpečení.** Pokud dojde k ohrožení jedné části aplikace, ujistěte se, že na místě, chcete-li minimalizovat šíření porušení zabezpečení, nejen v rámci stejného umístění, ale také mezi umístěními řešení.

**Monitorování povolený přístup k síti.** Určení zásad síťového přístupu pro aplikace, jako je například pouze z konkrétní podsítě přístup k aplikaci a povolit pouze minimální portů a protokolů mezi komponenty potřebné pro správnou funkci aplikace.

**Využívejte robustní ověřování.** Robustní ověřování schématu je velmi důležité pro zabezpečení aplikace. Zvažte použití poskytovatele federované identity, který poskytuje možnosti jednotného přihlašování a používají jeden nebo více z následujících schémat: uživatelské jméno a heslo přihlášení, veřejné a soukromé klíče, dvojúrovňového nebo vícefaktorové ověřování a důvěryhodné zabezpečení skupiny. Určení odpovídající prostředky pro ukládání citlivých dat a dalších tajných kódů aplikace ověřování kromě typů certifikátů a jejich požadavky.

**Použijte šifrování.** Identifikace oblastí, které aplikace používat šifrování, jako je pro komunikaci klienta nebo úložiště dat a přístup.

**Použijte zabezpečené kanály.** Zabezpečený kanál mezi cloudy je velmi důležité pro zajištění kontroly zabezpečení a ověřování, ochrana v reálném čase, karantény a dalším službám v cloudu.

**Definice a používání rolí.** Implementace rolí pro přístup k jedné identity a konfigurace prostředků napříč cloudy. Určete požadavky na řízení přístupu na základě role pro aplikaci a její prostředky platformy.

**Audit systému.** Systém monitorování může přihlásit a agregovat data z komponent aplikace a operace související cloudové platformy.

## <a name="summary"></a>Souhrn

Tento článek obsahuje seznam položek, které jsou důležité vzít v úvahu při vytváření a návrhu hybridní aplikace. Revize tyto pilíře před nasazením aplikace zabrání narazíte na tyto otázky v produkční výpadky a potenciálně by bylo potřeba návštěvě návrhu.

Může to vypadat podobně jako časově náročný úkol předem, ale snadno získat návratnosti investice do návrhu vaší aplikace založená na tyto pilíře.

## <a name="next-steps"></a>Další postup

Další informace naleznete v následujících materiálech:

-   [Hybridní cloud](https://azure.microsoft.com/overview/hybrid-cloud/)
-   [Hybridní cloudové aplikace](https://azure.microsoft.com/solutions/hybrid-cloud-app/)
-   [Vývoj šablon Azure Resource Manageru pro konzistenci cloudu](http://aka.ms/consistency)