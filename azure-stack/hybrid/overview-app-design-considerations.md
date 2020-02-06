---
title: Požadavky na návrh hybridní aplikace při sestavování v Azure a Azure Stack
description: Co je třeba zvážit při vytváření hybridní aplikace pro inteligentní Cloud a inteligentní hraniční zařízení.
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 72a0fd215363911457271f65d636be37e3388c83
ms.sourcegitcommit: 74ce7c12a93d47315d70427b02bcacbd3b44f854
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2020
ms.locfileid: "77037161"
---
# <a name="hybrid-application-design-considerations"></a>Požadavky na návrh hybridní aplikace 

Microsoft Azure je jediným konzistentním hybridním cloudem. Umožňuje znovu použít vaše investice do vývoje a umožňuje aplikacím, které můžou zahrnovat globální Azure, cloudové cloudy Azure a Azure Stack, což je rozšíření Azure ve vašem datovém centru. Aplikace, které zahrnují cloudy, se také označují jako *hybridní aplikace*.

[*Průvodce architekturou aplikací Azure*](https://docs.microsoft.com/azure/architecture/guide) popisuje strukturovaný přístup k návrhu aplikací, které jsou škálovatelné, odolné a vysoce dostupné. Požadavky popsané v [*příručce aplikační architektura Azure*](https://docs.microsoft.com/azure/architecture/guide) se vztahují i na aplikace, které jsou navržené pro jeden Cloud a pro aplikace, které využívají cloudy.

Tento článek rozšiřuje [*pilíře kvality softwaru*](https://docs.microsoft.com/azure/architecture/guide/pillars) popsané v [ *Průvodci architekturou*](https://docs.microsoft.com/azure/architecture/guide/) [*aplikací Azure*](https://docs.microsoft.com/azure/architecture/guide/) a zaměřuje se konkrétně na návrh hybridních aplikací. Kromě toho přidáme pilíře *umístění* , protože hybridní aplikace nejsou výhradně v jednom cloudu nebo v jednom místním datovém centru.

Hybridní scénáře se značně liší u prostředků, které jsou k dispozici pro vývoj, a zahrnují okolnosti, jako je zeměpisná, zabezpečení, přístup k Internetu a další požadavky. I když v této příručce nejde vytvořit výčet konkrétních důležitých hodnot, může vám poskytnout několik klíčových pokynů a osvědčených postupů, které můžete sledovat. Úspěšné navrhování, konfigurace, nasazení a údržba hybridní aplikační architektury zahrnuje mnoho hledisek návrhu, které vám nemusí být pro vás podstatou známo.

Tento dokument se zaměřuje na agregaci možných otázek, které mohou nastat při implementaci hybridních aplikací, a poskytuje aspekty (Tyto pilíře) a osvědčené postupy, jak s nimi pracovat. Po vyřešení těchto otázek během fáze návrhu se vyhnete problémům, které by mohly způsobovat v produkčním prostředí.

V podstatě se jedná o otázky, které je třeba vzít v úvahu před vytvořením hybridní aplikace. Chcete-li začít, je třeba provést následující akce:

-   Identifikujte a vyhodnoťte součásti aplikace.

-   Vyhodnotit součásti aplikace na pilířích.

## <a name="evaluate-the-application-components"></a>Vyhodnotit součásti aplikace

Každá součást aplikace má svou vlastní konkrétní roli v rámci větší aplikace a měla by být přezkoumána se všemi důležitými hledisky návrhu. Požadavky a funkce každé součásti by měly být namapovány na tyto otázky, které vám pomůžou určit architekturu aplikace.

Rozložíte svou aplikaci na své komponenty pomocí zkoumání architektury aplikace a určení toho, co se skládá z. Součástí můžou být i další aplikace, se kterými vaše aplikace komunikuje. Jak identifikujete komponenty, vyhodnoťte zamýšlené hybridní operace podle jejich vlastností, jako jsou následující:

-   Jaký je účel komponenty?

-   Jaké jsou vzájemné závislosti mezi součástmi?

Například aplikace může mít front-end a back-end definované jako dvě součásti. V hybridním scénáři je front-end v jednom cloudu a back-end je v druhé. Aplikace

poskytuje komunikační kanály mezi front-end a uživatelem a také mezi front-end a back-endu.

Komponenta aplikace je definována mnoha formuláři a scénáři. Nejdůležitější úlohy je identifikují a jejich cloudové nebo místní umístění.

Společné součásti aplikace, které se mají zahrnout do inventáře, jsou uvedené v tabulce 1.

### <a name="table-1-common-app-components"></a>Tabulka 1. Společné součásti aplikací


| **Komponenta** | **Doprovodné materiály k hybridní aplikaci** |
| ---- | ---- |
| Připojení klientů | Vaše aplikace (na jakémkoli zařízení) má přístup k uživatelům různými způsoby, od jediného vstupního bodu, včetně následujících:<br>– Model klient-server, který vyžaduje, aby uživatel měl nainstalovaného klienta nástroje pro práci s aplikací. Serverová aplikace, ke které se přistupoval z webového prohlížeče.<br>-Klientská připojení můžou zahrnovat oznámení v případě, že dojde k přerušení připojení nebo upozornění, když můžou platit poplatky za roaming. |
| Ověřování  | Ověřování může být vyžadováno pro uživatele, který se připojuje k aplikaci, nebo z jedné součásti, která se připojuje k jiné. |
| Rozhraní API  | Vývojářům můžete poskytnout programový přístup k vaší aplikaci pomocí sad rozhraní API a knihoven tříd a poskytovat rozhraní připojení na základě standardů sítě Internet. Rozhraní API můžete použít také k rozloží aplikace na nezávisle provozní logické jednotky. |
| Služby  | Pro poskytování funkcí aplikace můžete využívat stručné služby. Služba může být modul, na kterém je aplikace spuštěná. |
| Fronty | Fronty můžete použít k uspořádání stavu životního cyklu a stavů komponent aplikace. Tyto fronty můžou poskytovat možnosti zasílání zpráv, oznámení a ukládání do vyrovnávací paměti pro odběr stran. |
| Úložiště dat | Aplikace může být Bezstavová nebo stavová. Stavové aplikace potřebují úložiště dat, které může být splněno řadou formátů a svazků. |
| Ukládání dat do mezipaměti  | Komponenta pro ukládání dat do mezipaměti v návrhu může navrhovat problémy s latencí při řešení potíží a hrát roli při aktivaci shlukování cloudu. |
| Přijímání dat | Data je možné odeslat do aplikace mnoha různými způsoby, od uživatelem zaslaných hodnot ve webovém formuláři až po nepřetržitý tok dat s velkým objemem. |
| Zpracování dat | Vaše úlohy zpracování dat (například sestavy, analýzy, dávkové exporty a transformace dat) je možné zpracovat buď ve zdroji, nebo v samostatné komponentě pomocí kopie dat. |

## <a name="assess-app-components-for-pillars"></a>Posouzení součástí aplikace pro pilíře

Pro každou komponentu vyhodnoťte své charakteristiky pro každý pilíř. Když vyhodnocujete jednotlivé komponenty se všemi pilíři, může se vám stát, že vám budou vědět, že se vám bude týkat návrh hybridní aplikace. Vyjednání s těmito hledisky by mohla do optimalizace aplikace přidat hodnotu. Tabulka 2 poskytuje popis každého pilíře v souvislosti s hybridními aplikacemi.

### <a name="table-2-pillars"></a>Tabulka 2. Pilíře

| **Pilíř** | **Popis** |
| ----------- | --------------------------------------------------------- |
| umístění  | Strategické umístění komponent v hybridních aplikacích. |
| Škálovatelnost  | Schopnost systému zvládnout zvýšenou zátěž |
| Dostupnost  | Poměr doby, po kterou je hybridní aplikace funkční a funguje. |
| Odolnost | Schopnost hybridní aplikace obnovit. |
| Možnosti správy | Provozní procesy, které udržují systém spuštěný v produkčním prostředí. |
| Zabezpečení | Ochrana hybridních aplikací a dat před hrozbami. |

## <a name="placement"></a>umístění

Hybridní aplikace má v podstatě aspekt umístění, například pro datové centrum.

Umístění je důležitým úkolem umístění komponent, aby mohli nejlépe vystavit hybridní aplikaci. Podle definice hybridní aplikace rozdělují umístění, například z místního prostředí do cloudu a mezi různými cloudy. Komponenty aplikace můžete umístit do cloudů dvěma způsoby:

-   **Vertikální hybridní aplikace**  
    Komponenty aplikace jsou distribuovány napříč umístěními. Každá jednotlivá součást může mít více instancí, které se nachází pouze v jednom umístění.

-   **Horizontální hybridní aplikace**  
    Komponenty aplikace jsou distribuovány napříč umístěními. Každá jednotlivá součást může mít více instancí, které jsou rozloženy do více umístění.
    
    Některé součásti si mohou být vědomy jejich polohy, i když jiné nemají žádné znalosti o jejich umístění a umístění. Tento virtuousness je možné dosáhnout pomocí abstraktní vrstvy. Tato vrstva s moderní aplikační architekturou, jako jsou mikroslužby, může definovat způsob, jakým se aplikace obsluhuje umístěním komponent aplikace pracujících na uzlech napříč cloudy.

### <a name="placement-checklist"></a>Kontrolní seznam umístění

**Ověřte požadovaná umístění.** Ujistěte se, že aplikace nebo kterákoli z jeho součástí jsou nutné k provozu v nebo vyžadují certifikaci pro konkrétní Cloud. To může zahrnovat požadavky na svrchovanost z vaší společnosti nebo podle zákona. Také určete, zda jsou některé místní operace požadovány pro konkrétní umístění nebo národní prostředí.

**Zjišťuje závislosti připojení.** Požadovaná umístění a další faktory mohou určovat závislosti připojení mezi vašimi komponentami. Když umístíte komponenty, určete optimální konektivitu a zabezpečení pro komunikaci mezi nimi. Mezi možnosti patří [ *VPN*,](https://docs.microsoft.com/azure/vpn-gateway/) [ *ExpressRoute*](https://docs.microsoft.com/azure/expressroute/) a [ *Hybrid Connections*.](https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections)

**Vyhodnoťte možnosti platformy.** U každé součásti aplikace zkontrolujte, jestli je požadovaný poskytovatel prostředků pro komponentu aplikace dostupný v cloudu a jestli šířka pásma může vyhovovat očekávané propustnosti a požadavkům na latenci.

**Plánování přenositelnosti.** Použijte moderní aplikační architektury, jako jsou kontejnery nebo mikroslužby, k naplánování operací přesunutí a zabránění závislostem služeb.

**Určete požadavky na suverenitu dat.** Hybridní aplikace jsou zaměřené na izolaci dat, jako je například v místním datovém centru. Projděte si umístění vašich prostředků a optimalizujte úspěšnost tohoto požadavku.

**Plánování latence.** Mezi součásti aplikace může mezi různými cloudovou operací dovádět fyzickou vzdálenost. Zjistíte, že požadavky budou vyhovovat libovolné latenci.

**Řízení toků provozu.** Využívání špičkové služby a příslušné a zabezpečené komunikace pro osobní údaje údajů, které jsou přístupné pro front-end ve veřejném cloudu.

## <a name="scalability"></a>Škálovatelnost

Škálovatelnost je schopnost systému zvládnout zvýšené zatížení aplikace, které se může v průběhu času v různých faktorech a síly měnit i v případě velikosti a rozsahu aplikace.

Základní diskusi k tomuto pilíři najdete v tématu [*škálovatelnost*](https://docs.microsoft.com/azure/architecture/guide/pillars#scalability) v pilířích kvality softwaru.

Horizontální přístup k škálování pro hybridní aplikace umožňuje přidat další instance pro splnění požadavků a pak je v tichých intervalech zakázat.

V hybridních scénářích je potřeba při rozšiřování jednotlivých komponent mezi cloudy zvážit další aspekty. Škálování jedné části aplikace může vyžadovat jiné škálování. Například pokud se počet připojení klientů zvyšuje, ale webové služby aplikace nebudou škálovat správně, zatížení databáze může sytost aplikace navýšit.

Některé součásti aplikace se můžou škálovat lineárně, zatímco ostatní mají závislosti na škálování a můžou být omezené na to, co rozšiřují, aby se mohly škálovat. Například tunel VPN, který poskytuje hybridní připojení pro umístění součástí aplikace, má omezení šířky pásma a latence, na kterou lze škálovat. Jak se škálují komponenty aplikace, aby se zajistilo splnění těchto požadavků?

### <a name="scalability-checklist"></a>Kontrolní seznam ke škálovatelnosti

**Zjišťuje prahové hodnoty škálování.** Chcete-li zpracovat různé závislosti ve vaší aplikaci, určete rozsah, ve kterém lze součásti aplikace v různých cloudech škálovat nezávisle na sobě, a přitom ještě splnění požadavků na spuštění aplikace. Hybridní aplikace často potřebují škálovat konkrétní oblasti v aplikaci tak, aby zpracovávala funkci při jejich interakci a vliv na zbytek aplikace. Například překročení počtu front-end instancí může vyžadovat škálování back-endu.

**Definujte plány škálování.** Většina aplikací má zaneprázdněné doby, takže potřebujete agregovat dobu špičky v plánech, abyste mohli koordinovat optimální škálování.

**Použijte centralizovaný systém monitorování.** Funkce monitorování platforem můžou poskytovat automatické škálování, ale hybridní aplikace potřebují centralizovaný monitorovací systém, který agreguje stav systému a zatížení. Centralizovaný monitorovací systém může iniciovat změnu velikosti prostředku v jednom umístění a škálování v závislosti na prostředku v jiném umístění. Kromě toho může centrální monitorovací systém sledovat, které cloudy mají prostředky automatického škálování a které ne.

**Využijte možnosti automatického škálování (jako k dispozici).** Pokud jsou možnosti automatického škálování součástí vaší architektury, implementujete automatické škálování nastavením prahových hodnot, které definují, kdy musí být součást aplikace rozdělena nahoru, dolů, dolů nebo v. Příkladem automatického škálování je připojení klienta, které se automaticky škáluje v jednom cloudu tak, aby zpracovávala větší kapacitu, ale způsobí, že se v různých cloudech změní také jiné závislosti aplikace. Funkce automatického škálování těchto závislých komponent musí být zjišťovány.

Pokud automatické škálování není k dispozici, zvažte implementaci skriptů a dalších prostředků pro zajištění ručního škálování aktivovaného mezními hodnotami v centralizovaném monitorovacím systému.

**Určete očekávané zatížení podle umístění.** Hybridní aplikace, které zpracovávají požadavky klientů, se můžou primárně spoléhat na jedno místo. Pokud zatížení klientských požadavků překročí prahovou hodnotu,

Další prostředky lze přidat do jiného umístění pro distribuci zatížení příchozích požadavků. Ujistěte se, že připojení klientů mohou zpracovávat zvýšené zátěže a také určit automatické postupy pro připojení klientů pro zpracování zatížení.

## <a name="availability"></a>Dostupnost

Dostupnost je čas, kdy je systém funkční a funguje. Dostupnost se měří jako procento doby provozu. Dostupnost mohou snížit chyby aplikace, problémy s infrastrukturou nebo zátěž systému.

Základní diskusi k tomuto pilíři najdete v tématu [*dostupnost*](/azure/architecture/framework/) v pilířích kvality softwaru.

### <a name="availability-checklist"></a>Kontrolní seznam k dostupnosti

**Poskytněte redundanci připojení.** Hybridní aplikace vyžadují připojení mezi cloudy, mezi kterými je aplikace rozložená. Máte možnost zvolit si technologie hybridního připojení, takže kromě vaší primární technologie můžete použít jinou technologii k zajištění redundance s automatizovanými možnostmi převzetí služeb při selhání, pokud primární technologie selže.

**Klasifikace domén selhání.** Aplikace odolné proti chybám vyžadují více domén selhání. Domény selhání umožňují izolovat bod selhání, například pokud dojde k selhání jednoho pevného disku v místním prostředí, pokud se nejedná o přepínač nejvyšší úrovně, nebo pokud celé datové centrum není k dispozici. V hybridní aplikaci může být umístění klasifikované jako doména selhání. Čím víc je požadavků na dostupnost, budete potřebovat vyhodnotit, jak by měla být klasifikována jedna doména selhání.

**Klasifikujte domény upgradu.** Domény upgradu slouží k zajištění, že jsou k dispozici instance komponent aplikace, zatímco jiné instance stejné komponenty jsou zpracovávány pomocí aktualizací nebo upgradů funkcí. Stejně jako u domén selhání mohou být upgradované domény klasifikovány podle jejich umístění napříč umístěními. Musíte určit, jestli se komponenta aplikace může pojmout při upgradu na jednom umístění, než se upgraduje v jiném umístění, nebo pokud se vyžadují jiné konfigurace domény. Jedno samotné umístění může mít více domén upgradu.

**Sledujte instance a dostupnost.** Vysoce dostupné komponenty aplikace můžou být dostupné prostřednictvím vyrovnávání zatížení a synchronní replikace dat. Před přerušením služby musíte určit, kolik instancí může být offline.

**Implementujte samočinně.** V případě, že problém způsobuje přerušení dostupnosti aplikace, detekce systému monitorování by mohla iniciovat aktivity samočinného retušování aplikace, jako je vyprázdnění neúspěšné instance a její opětovné nasazení. Nejpravděpodobnější příčinou je, že je k dispozici řešení centrálního monitorování, které je integrováno do kanálu hybridní průběžné integrace a průběžného doručování (CI/CD). Aplikace je integrovaná do monitorovacího systému k identifikaci problémů, které by mohly vyžadovat opětovné nasazení součásti aplikace. Monitorovací systém může také aktivovat hybridní CI/CD a znovu nasadit komponentu aplikace a potenciálně další závislé komponenty ve stejném nebo jiném umístění.

**Udržujte smlouvy o úrovni služeb (SLA).** Dostupnost je zásadní pro jakékoli smlouvy, aby se zachovalo připojení ke službám a aplikacím, které máte ve svých zákaznících. Každé umístění, se kterým se vaše hybridní aplikace spoléhá, může mít svou vlastní smlouvu SLA. Tyto různé SLA můžou mít vliv na celkovou smlouvu SLA vaší hybridní aplikace.

## <a name="resiliency"></a>Odolnost

Odolnost proti chybám je schopnost hybridní aplikace a systému obnovit selhání a nadále fungovat. Cílem odolnosti proti chybám je obnovení plně funkčního stavu aplikace co nejdříve po selhání. Strategie odolnosti proti chybám zahrnují řešení, jako je zálohování, replikace a zotavení po havárii.

Základní diskusi k tomuto pilíři najdete v tématu [*odolnost*](https://docs.microsoft.com/azure/architecture/guide/pillars#resiliency) proti chybám v pilířích kvality softwaru.

### <a name="resiliency-checklist"></a>Kontrolní seznam k odolnosti

**Odhalte závislosti zotavení po havárii.** Zotavení po havárii v jednom cloudu může vyžadovat změny součástí aplikace v jiném cloudu. Pokud dojde k převzetí služeb při selhání jedné nebo více komponent z jednoho cloudu do jiného umístění, buď v rámci stejného cloudu, nebo do jiného cloudu, je nutné, aby se tyto změny dozvěděly na závislých součástech. To zahrnuje i závislosti připojení. Odolnost proti chybám vyžaduje plně testovaný plán obnovení aplikace pro každý Cloud.

**Navažte tok obnovení.** Účinný návrh toku obnovení vyhodnotil součásti aplikace, aby mohla přizpůsobit vyrovnávací paměti, opakované pokusy, Opakování neúspěšného přenosu dat a v případě potřeby přejít zpět na jinou službu nebo pracovní postup. Musíte určit, jaký záložní mechanismus se má použít, jak postup obnovení zahrnuje a jak často se testuje. Měli byste také určit četnost přírůstkových i úplných záloh.

**Test částečné obnovy.** Částečná obnova v části aplikace může poskytovat zajištění pro uživatele, kteří nejsou k dispozici. Tato část plánu by měla zajistit, že částečné obnovení nemá žádné vedlejší účinky, jako je například služba zálohování a obnovení, která komunikuje s aplikací, aby se před provedením zálohování řádně ukončila.

**Určete návod pro zotavení po havárii a přiřaďte zodpovědnost.** Plán obnovení by měl obsahovat informace o tom, kdo a jaké role můžou zahájit akce zálohování a obnovení kromě toho, co se dá zálohovat a obnovit.

**Porovnejte prahové hodnoty pro samočinné retušování pomocí zotavení po havárii.** Určete možnosti automatického retušování aplikace pro automatické zahájení obnovení a dobu potřebnou k automatickému retušování aplikace, aby byla považována za selhání nebo úspěšnost. Určete prahové hodnoty pro každý Cloud.

**Ověřte dostupnost funkcí odolnosti proti chybám.** Určete dostupnost funkcí a schopností odolnosti proti chybám pro každé umístění. Pokud umístění neposkytuje požadované možnosti, zvažte integraci tohoto umístění do centralizované služby, která poskytuje funkce odolnosti.

**Určete prostoje.** Určete očekávané výpadky z důvodu údržby aplikace jako celku a jako součásti aplikace.

**Postupy řešení potíží v dokumentu.** Definujte postupy řešení potíží pro opětovné nasazení prostředků a součástí aplikací.

## <a name="manageability"></a>Možnosti správy

Důležité informace týkající se správy hybridních aplikací jsou důležité při návrhu vaší architektury. Dobře spravovaná hybridní aplikace poskytuje infrastrukturu jako kód, který umožňuje integraci konzistentního kódu aplikace ve společném vývojovém kanálu. Implementací konzistentního systému a individuálního testování změn infrastruktury můžete zajistit integrované nasazení, pokud změny projde testy, což umožňuje jejich sloučení do zdrojového kódu.

Základní diskusi k tomuto pilíři najdete v tématu [*DevOps*](/azure/architecture/framework/#devops) v pilířích kvality softwaru.

### <a name="manageability-checklist"></a>Kontrolní seznam spravovatelnosti

**Implementujte monitorování.** Pomocí centralizovaného monitorovacího systému komponent, které jsou rozloženy mezi cloudy, můžete poskytnout agregované zobrazení jejich stavu a výkonu. Tento systém zahrnuje monitorování součástí aplikace i souvisejících funkcí platformy.

Určete části aplikace, které vyžadují monitorování.

**Zásady souřadnic.** Každé umístění, ve kterém hybridní aplikace pokrývá, může mít vlastní zásady, které zahrnují povolené typy prostředků, zásady pojmenování, značky a další kritéria.

**Definujte a používejte role.** Jako správce databáze potřebujete určit oprávnění požadovaná pro různé osoby (například vlastníka aplikace, správce databáze a koncového uživatele), které potřebují přístup k prostředkům aplikace. Tato oprávnění je nutné nakonfigurovat u prostředků a v rámci aplikace. Systém řízení přístupu na základě role (RBAC) umožňuje nastavit tato oprávnění pro prostředky aplikace. Tato přístupová práva jsou náročná na to, že jsou všechny prostředky nasazeny v jednom cloudu, ale vyžadují ještě větší pozornost, pokud jsou prostředky rozloženy mezi cloudy. Oprávnění k prostředkům nastaveným v jednom cloudu se nevztahují na prostředky nastavené v jiném cloudu.

**Použijte kanály CI/CD.** Kanál průběžné integrace a průběžného vývoje (CI/CD) může poskytovat konzistentní proces pro vytváření a nasazování aplikací, které jsou rozloženy mezi cloudy, a zajištění kvality pro svou infrastrukturu a aplikaci. Tento kanál umožňuje testovat infrastrukturu a aplikace na jednom cloudu a nasadit na jiném cloudu. Kanál dokonce umožňuje nasadit určité komponenty hybridní aplikace do jednoho cloudu a dalších součástí do jiného cloudu, v podstatě tak, že základem pro nasazení hybridních aplikací. Systém CI/CD je velmi důležitý pro zpracování komponent aplikace závislosti v průběhu instalace, jako je třeba webová aplikace, která vyžaduje připojovací řetězec k databázi.

**Spravujte životní cyklus.** Vzhledem k tomu, že prostředky hybridní aplikace můžou zahrnovat umístění, musí být každá funkce správy životního cyklu jednoho umístění agregovaná do jedné jednotky pro správu životního cyklu. Vezměte v úvahu, jak se vytvářejí, aktualizují a odstraňují.

**Projděte si strategie odstraňování potíží.** Řešení potíží s hybridní aplikací zahrnuje více součástí aplikace než stejná aplikace, která běží v jednom cloudu. Kromě připojení mezi cloudy je aplikace spuštěna na dvou platformách, nikoli na jednom. Důležitou úlohou při řešení potíží s hybridními aplikacemi je prozkoumávat agregované monitorování stavu a sledování výkonu komponent aplikace.

## <a name="security"></a>Zabezpečení

Zabezpečení je jedním z hlavních předpokladů pro jakoukoli cloudovou aplikaci a je ještě důležitější pro hybridní cloudové aplikace.

Základní diskusi k tomuto pilíři najdete v tématu [*zabezpečení*](https://docs.microsoft.com/azure/architecture/guide/pillars#security) v pilířích kvality softwaru.

### <a name="security-checklist"></a>Kontrolní seznam zabezpečení

**Předpokládejte, že došlo k porušení.** Pokud dojde k ohrožení jedné součásti aplikace, zajistěte, aby byla k dispozici řešení pro minimalizaci rozšíření porušení, nikoli pouze ve stejném umístění, ale také ve všech umístěních.

**Monitorovat povolený přístup k síti.** Určete zásady přístupu k síti pro aplikaci, například jenom přístup k aplikaci z konkrétní podsítě, a povolte jenom minimální porty a protokoly mezi součástmi, které aplikace potřebuje k tomu, aby aplikace správně fungovala.

**Využívají robustní ověřování.** Robustní schéma ověřování je důležité pro zabezpečení vaší aplikace. Zvažte použití důvěryhodného poskytovatele identity, který poskytuje možnosti jednotného přihlašování a používá jedno nebo několik následujících schémat: uživatelské jméno a přihlášení k heslům, veřejné a privátní klíče, dvojúrovňové nebo vícefaktorové ověřování a důvěryhodné zabezpečení. skupiny. Kromě typů certifikátů a jejich požadavků určete vhodné prostředky pro ukládání citlivých dat a dalších tajných kódů pro ověřování aplikací.

**Použijte šifrování.** Určete, které oblasti aplikace používají šifrování, například pro úložiště dat nebo komunikaci klientů a přístup.

**Používejte zabezpečené kanály.** Zabezpečený kanál napříč cloudy je důležitý pro poskytování kontrol zabezpečení a ověřování, ochrany v reálném čase, karantény a dalších služeb napříč cloudy.

**Definujte a používejte role.** Implementujte role pro konfigurace prostředků a přístup s jednou identitou napříč cloudy. Určete požadavky na řízení přístupu na základě role (RBAC) pro aplikaci a její prostředky platformy.

**Auditovat systém.** Sledování systému může protokolovat a agregovat data z komponent aplikace i souvisejících operací cloudové platformy.

## <a name="summary"></a>Souhrn

Tento článek poskytuje kontrolní seznam položek, které je důležité zvážit při vytváření a navrhování vašich hybridních aplikací. Před načtením těchto pilířů před nasazením aplikace zabráníte v jejich spuštění na tyto otázky v případě výpadků v produkčním prostředí a případně budete vyžadovat, abyste si návrh znovu navštívili.

Může se zdát jako časově náročná úloha předem, ale pokud aplikaci navrhujete na základě těchto pilířů, snadno získáte návratnost investic.

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících zdrojích:

-   [Hybridní cloud](https://azure.microsoft.com/overview/hybrid-cloud/)
-   [Hybridní cloudové aplikace](https://azure.microsoft.com/solutions/hybrid-cloud-app/)
-   [Vývoj šablon Azure Resource Manageru pro konzistenci cloudu](https://aka.ms/consistency)