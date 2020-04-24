---
title: Požadavky na návrh hybridní aplikace v Azure a centru Azure Stack
description: Přečtěte si o požadavcích na návrh při sestavování hybridních aplikací pro inteligentní Cloud a inteligentní hraniční zařízení, včetně umístění, škálovatelnosti, dostupnosti a odolnosti.
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 8f37967534452c43788cbe0c40490ee4021ca5f0
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "80812329"
---
# <a name="hybrid-app-design-considerations"></a>Požadavky na návrh hybridní aplikace

Microsoft Azure je jediným konzistentním hybridním cloudem. Umožňuje znovu použít své investice do vývoje a povolit aplikace, které můžou zahrnovat globální Azure, cloudové cloudy Azure a Azure Stack, což je rozšíření Azure ve vašem datovém centru. Aplikace, které zahrnují cloudy, se také označují jako *hybridní aplikace*.

[*Průvodce architekturou aplikací Azure*](https://docs.microsoft.com/azure/architecture/guide) popisuje strukturovaný přístup k návrhu aplikací, které jsou škálovatelné, odolné a vysoce dostupné. Požadavky popsané v [*příručce aplikační architektura Azure*](https://docs.microsoft.com/azure/architecture/guide) se vztahují i na aplikace, které jsou navržené pro jeden Cloud a pro aplikace, které využívají cloudy.

Tento článek rozšiřuje [*pilíře kvality softwaru*](https://docs.microsoft.com/azure/architecture/guide/pillars) popsané v [ *Průvodci architekturou*](https://docs.microsoft.com/azure/architecture/guide/) [*aplikací Azure*](https://docs.microsoft.com/azure/architecture/guide/) a zaměřuje se konkrétně na návrh hybridních aplikací. Kromě toho přidáme pilíře *umístění* , protože hybridní aplikace nejsou výhradně v jednom cloudu nebo v jednom místním datovém centru.

Hybridní scénáře se značně liší u prostředků, které jsou k dispozici pro vývoj, a zahrnují okolnosti, jako je zeměpisná, zabezpečení, přístup k Internetu a další požadavky. I když v této příručce nejde vytvořit výčet konkrétních důležitých informací, může vám poskytnout několik klíčových pokynů a osvědčených postupů, které můžete sledovat. Úspěšné navrhování, konfigurace, nasazení a údržba architektury hybridních aplikací zahrnuje mnoho hledisek návrhu, které vám nemusí být pro vás podstatou známo.

Tento dokument se zaměřuje na agregaci možných otázek, které mohou nastat při implementaci hybridních aplikací, a poskytuje aspekty (Tyto pilíře) a osvědčené postupy, jak s nimi pracovat. Po vyřešení těchto otázek během fáze návrhu se vyhnete problémům, které by mohly způsobovat v produkčním prostředí.

V podstatě se jedná o otázky, které je potřeba vzít v úvahu před vytvořením hybridní aplikace. Chcete-li začít, je třeba provést následující akce:

- Identifikujte a vyhodnoťte součásti aplikace.
- Vyhodnotit komponenty aplikace na pilířích.

## <a name="evaluate-the-app-components"></a>Vyhodnotit součásti aplikace

Každá součást aplikace má svou vlastní konkrétní roli v rámci větší aplikace a měla by být prověřena všemi důležitými hledisky návrhu. Požadavky a funkce každé součásti by měly být namapovány na tyto otázky, které vám pomůžou určit architekturu aplikace.

Rozložíte svou aplikaci na své součásti tím, že prostudujte architekturu vaší aplikace a určíte, co se skládá z. Součástí můžou být i další aplikace, se kterými vaše aplikace komunikuje. Jak identifikujete komponenty, vyhodnoťte své zamýšlené hybridní operace podle jejich vlastností, a to tak, že požádáte o tyto otázky:

- Jaký je účel komponenty?
- Jaké jsou vzájemné závislosti mezi součástmi?

Například aplikace může mít front-end a back-end definované jako dvě součásti. V hybridním scénáři je front-end v jednom cloudu a back-end je v druhém. Aplikace poskytuje komunikační kanály mezi front-end a uživatelem a také mezi front-end a back-end.

Komponenta aplikace je definována mnoha formuláři a scénáři. Nejdůležitější úlohy je identifikují a jejich cloudové nebo místní umístění.

Společné součásti aplikací, které se mají zahrnout do inventáře, jsou uvedené v tabulce 1.

### <a name="table-1-common-app-components"></a>Tabulka 1. Společné součásti aplikací

| **Komponenta** | **Doprovodné materiály k hybridní aplikaci** |
| ---- | ---- |
| Připojení klientů | Vaše aplikace (na jakémkoli zařízení) má přístup k uživatelům různými způsoby z jednoho vstupního bodu, a to včetně následujících způsobů:<br>– Model klient-server, který vyžaduje, aby uživatel měl nainstalovaného klienta pro práci s aplikací. Aplikace založená na serveru, ke které se přistupoval z prohlížeče.<br>-Klientská připojení můžou zahrnovat oznámení v případě, že dojde k přerušení připojení nebo upozornění, když můžou platit poplatky za roaming. |
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
| Umístění  | Strategické umístění komponent v hybridních aplikacích. |
| Škálovatelnost  | Schopnost systému zvládnout zvýšenou zátěž |
| Dostupnost  | Poměr doby, po kterou je hybridní aplikace funkční a funguje. |
| Odolnost | Možnost obnovení hybridní aplikace. |
| Možnosti správy | Provozní procesy, které udržují systém spuštěný v produkčním prostředí. |
| Zabezpečení | Ochrana hybridních aplikací a dat před hrozbami |

## <a name="placement"></a>Umístění

Hybridní aplikace má v podstatě nějaký aspekt umístění, třeba pro datové centrum.

Umístění je důležitým úkolem umístění komponent, aby mohli nejlépe vystavovat hybridní aplikaci. Podle definice hybridní aplikace rozdělují umístění, například z místního prostředí do cloudu a mezi různými cloudy. Komponenty aplikace můžete umístit na cloudy dvěma způsoby:

- **Vertikální hybridní aplikace**  
    Komponenty aplikace jsou distribuovány napříč umístěními. Každá jednotlivá součást může mít více instancí, které se nachází pouze v jednom umístění.

- **Horizontální hybridní aplikace**  
    Komponenty aplikace jsou distribuovány napříč umístěními. Každá jednotlivá součást může mít více instancí, které jsou rozloženy do více umístění.

    Některé součásti si můžou uvědomit o jejich umístění, zatímco jiné nemají žádné znalosti o jejich umístění a umístění. Tento virtuousness je možné dosáhnout pomocí abstraktní vrstvy. Tato vrstva s moderní aplikační architekturou, jako jsou mikroslužby, může definovat způsob, jakým se aplikace obsluhuje umístěním součástí aplikace, které fungují na uzlech napříč cloudy.

### <a name="placement-checklist"></a>Kontrolní seznam umístění

**Ověřte požadovaná umístění.** Ujistěte se, že aplikace nebo některá její součást je nutná k provozu v nebo vyžadovat certifikaci pro konkrétní Cloud. To může zahrnovat požadavky na svrchovanost z vaší společnosti nebo podle zákona. Také určete, zda jsou některé místní operace požadovány pro konkrétní umístění nebo národní prostředí.

**Zjišťuje závislosti připojení.** Požadovaná umístění a další faktory mohou určovat závislosti připojení mezi vašimi komponentami. Když umístíte komponenty, určete optimální konektivitu a zabezpečení pro komunikaci mezi nimi. Mezi možnosti patří [ *VPN*,](https://docs.microsoft.com/azure/vpn-gateway/) [ *ExpressRoute*](https://docs.microsoft.com/azure/expressroute/) a [ *Hybrid Connections*.](https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections)

**Vyhodnoťte možnosti platformy.** U každé součásti aplikace Zjistěte, jestli je požadovaný poskytovatel prostředků pro součást aplikace dostupný v cloudu a jestli šířka pásma může vyhovovat očekávané propustnosti a požadavkům na latenci.

**Plánování přenositelnosti.** Použijte moderní architektury aplikací, jako jsou kontejnery nebo mikroslužby, k naplánování operací přesunutí a zabránění závislostem služeb.

**Určete požadavky na suverenitu dat.** Hybridní aplikace jsou zaměřené na izolaci dat, jako je například v místním datovém centru. Projděte si umístění vašich prostředků a optimalizujte úspěšnost tohoto požadavku.

**Plánování latence.** Mezi součásti aplikace může mezi jednotlivými cloudovou operací zavádět fyzickou vzdálenost. Zjistíte, že požadavky budou vyhovovat libovolné latenci.

**Řízení toků provozu.** Využívání špičkové služby a příslušné a zabezpečené komunikace pro osobní údaje údajů, které jsou přístupné pro front-end ve veřejném cloudu.

## <a name="scalability"></a>Škálovatelnost

Škálovatelnost je schopnost systému zvládnout zvýšené zatížení aplikace, které se může v průběhu času měnit, protože jiné faktory a síly ovlivňují velikost cílové skupiny, a to i podle velikosti a rozsahu aplikace.

Základní diskusi k tomuto pilíři najdete v tématu [*škálovatelnost*](https://docs.microsoft.com/azure/architecture/guide/pillars#scalability) v pěti pilířích kvality architektury.

Horizontální přístup k škálování pro hybridní aplikace umožňuje přidat další instance pro splnění požadavků a pak je v tichých intervalech zakázat.

V hybridních scénářích je potřeba při rozšiřování jednotlivých komponent mezi cloudy zvážit další aspekty. Škálování jedné části aplikace může vyžadovat jiné škálování. Například pokud se počet připojení klientů zvyšuje, ale webové služby aplikace nebudou škálovat správně, zatížení databáze může sytost aplikace navýšit.

Některé součásti aplikace se můžou škálovat lineárně, zatímco ostatní mají závislosti na škálování a můžou být omezené na to, jaký rozsah si dokáže škálovat. Například tunel VPN, který poskytuje hybridní připojení pro umístění součástí aplikace, má omezení šířky pásma a latence, na kterou lze škálovat. Jak se škálují komponenty aplikace, aby se zajistilo splnění těchto požadavků?

### <a name="scalability-checklist"></a>Kontrolní seznam ke škálovatelnosti

**Zjišťuje prahové hodnoty škálování.** Pokud chcete zpracovat různé závislosti ve vaší aplikaci, určete rozsah, ve kterém se součásti aplikace v různých cloudech můžou nezávisle škálovat, a přitom splní požadavky na spuštění aplikace. Hybridní aplikace často potřebují škálovat konkrétní oblasti v aplikaci tak, aby se při interakci s funkcí pracovaly a ovlivněny zbývající část aplikace. Například překročení počtu front-endové instancí může vyžadovat škálování back-endu.

**Definujte plány škálování.** Většina aplikací má zaneprázdněné doby, takže potřebujete agregovat dobu špičky v plánech, abyste mohli koordinovat optimální škálování.

**Použijte centralizovaný systém monitorování.** Funkce monitorování platforem můžou poskytovat automatické škálování, ale hybridní aplikace potřebují centralizovaný monitorovací systém, který agreguje stav systému a zatížení. Centralizovaný monitorovací systém může iniciovat škálování prostředku v jednom umístění a škálování v závislosti na prostředku v jiném umístění. Kromě toho může centrální monitorovací systém sledovat, které cloudy mají prostředky automatického škálování a které ne.

**Využijte možnosti automatického škálování (jako k dispozici).** Pokud jsou možnosti automatického škálování součástí vaší architektury, implementujete automatické škálování nastavením prahových hodnot, které definují, kdy musí být součást aplikace rozdělená nahoru, dolů, dolů nebo v. Příkladem automatického škálování je připojení klienta, které se automaticky škáluje v jednom cloudu tak, aby se zvýšila větší kapacita, ale v různých cloudech se také rozšířily i další závislosti aplikace. Funkce automatického škálování těchto závislých komponent musí být zjišťovány.

Pokud není k dispozici automatické škálování, zvažte implementaci skriptů a dalších prostředků pro zajištění ručního škálování aktivovaného mezními hodnotami v centralizovaném monitorovacím systému.

**Určete očekávané zatížení podle umístění.** Hybridní aplikace, které zpracovávají požadavky klientů, se můžou primárně spoléhat na jedno místo. Pokud zatížení požadavků klientů překročí prahovou hodnotu, mohou být další prostředky přidány v jiném umístění pro distribuci zatížení příchozích požadavků. Ujistěte se, že připojení klientů mohou zpracovávat zvýšené zátěže a také určit automatické postupy pro připojení klientů pro zpracování zatížení.

## <a name="availability"></a>Dostupnost

Dostupnost je čas, kdy je systém funkční a funguje. Dostupnost se měří jako procento doby provozu. Všechny chyby aplikace, problémy s infrastrukturou a zatížení systému můžou snížit dostupnost.

Základní diskusi k tomuto pilíři najdete v tématu [*dostupnost*](/azure/architecture/framework/) v pěti pilířích kvality architektury.

### <a name="availability-checklist"></a>Kontrolní seznam k dostupnosti

**Poskytněte redundanci připojení.** Hybridní aplikace vyžadují připojení mezi cloudy, mezi kterými se aplikace rozprostře. Máte možnost zvolit si technologie hybridního připojení, takže kromě vaší primární technologie můžete použít jinou technologii k zajištění redundance s automatizovanými možnostmi převzetí služeb při selhání, pokud primární technologie selže.

**Klasifikace domén selhání.** Aplikace odolné proti chybám vyžadují více domén selhání. Domény selhání umožňují izolovat bod selhání, například pokud dojde k selhání jednoho pevného disku v místním prostředí, pokud se nejedná o přepínač nejvyšší úrovně, nebo pokud celé datové centrum není k dispozici. V hybridní aplikaci může být umístění klasifikované jako doména selhání. Čím víc je požadavků na dostupnost, budete potřebovat vyhodnotit, jak by měla být klasifikována jedna doména selhání.

**Klasifikujte domény upgradu.** Domény upgradu slouží k zajištění dostupnosti instancí součástí aplikace, zatímco jiné instance stejné komponenty se účtují s aktualizacemi nebo upgrady funkcí. Stejně jako u domén selhání mohou být upgradované domény klasifikovány podle jejich umístění napříč umístěními. Musíte určit, jestli se komponenta aplikace může pojmout při upgradu na jednom umístění, než se upgraduje v jiném umístění, nebo jestli se vyžadují jiné konfigurace domény. Jedno samotné umístění může mít více domén upgradu.

**Sledujte instance a dostupnost.** Komponenty aplikace s vysokou dostupností můžou být dostupné prostřednictvím vyrovnávání zatížení a synchronní replikace dat. Před přerušením služby musíte určit, kolik instancí může být offline.

**Implementujte samočinně.** V případě, že problém způsobuje přerušení dostupnosti aplikace, detekce systému monitorování by mohla iniciovat aktivity samočinného retušování aplikace, jako je vyprazdňování neúspěšné instance a její opětovné nasazení. Nejpravděpodobnější příčinou je, že je k dispozici řešení centrálního monitorování, které je integrováno do kanálu hybridní průběžné integrace a průběžného doručování (CI/CD). Aplikace je integrovaná do monitorovacího systému, aby identifikovala problémy, které by mohly vyžadovat opětovné nasazení součásti aplikace. Monitorovací systém může také aktivovat hybridní CI/CD pro opětovné nasazení komponenty aplikace a potenciálně další závislé součásti ve stejném nebo jiném umístění.

**Udržujte smlouvy o úrovni služeb (SLA).** Dostupnost je zásadní pro jakékoli smlouvy, aby se zachovalo připojení ke službám a aplikacím, které máte ve svých zákaznících. Každé umístění, na kterém vaše hybridní aplikace spoléhá, může mít svou vlastní smlouvu SLA. Tyto různé SLA můžou mít vliv na celkovou smlouvu SLA vaší hybridní aplikace.

## <a name="resiliency"></a>Odolnost

Odolnost proti chybám je schopnost hybridní aplikace a systému obnovit selhání a nadále fungovat. Cílem odolnosti proti chybám je vrácení aplikace do plně funkčního stavu po selhání. Strategie odolnosti proti chybám zahrnují řešení, jako je zálohování, replikace a zotavení po havárii.

Základní diskusi k tomuto pilíři najdete v tématu [*odolnost*](https://docs.microsoft.com/azure/architecture/guide/pillars#resiliency) proti chybám v pěti pilířích vynikající architektury.

### <a name="resiliency-checklist"></a>Kontrolní seznam k odolnosti

**Odhalte závislosti zotavení po havárii.** Zotavení po havárii v jednom cloudu může vyžadovat změny součástí aplikace v jiném cloudu. Pokud je jedna nebo víc komponent z jednoho cloudu převzetí služeb při selhání do jiného umístění, buď ve stejném cloudu, nebo v jiném cloudu, je potřeba, abyste tyto změny věděli i na závislých součástech. To zahrnuje i závislosti připojení. Odolnost proti chybám vyžaduje plně testovaný plán pro obnovení aplikací pro každý Cloud.

**Navažte tok obnovení.** Účinný návrh toku obnovení vyhodnotil součásti aplikace, aby mohla přizpůsobit vyrovnávací paměti, opakování, Opakování neúspěšného přenosu dat a v případě potřeby se vrátí do jiné služby nebo pracovního postupu. Musíte určit, jaký záložní mechanismus se má použít, jak postup obnovení zahrnuje a jak často se testuje. Měli byste také určit četnost přírůstkových i úplných záloh.

**Test částečné obnovy.** Částečná obnova v části aplikace může poskytovat reassurance uživatelům, kteří nejsou k dispozici. Tato část plánu by měla zajistit, že částečné obnovení nemá žádné vedlejší účinky, jako je například služba zálohování a obnovení, která komunikuje s aplikací, aby ji před provedením zálohování řádně ukončila.

**Určete návod pro zotavení po havárii a přiřaďte zodpovědnost.** Plán obnovení by měl obsahovat informace o tom, kdo a jaké role můžou zahájit akce zálohování a obnovení kromě toho, co se dá zálohovat a obnovit.

**Porovnejte prahové hodnoty pro samočinné retušování pomocí zotavení po havárii.** Určete možnosti automatického retušování aplikace pro automatické zahájení obnovení a dobu potřebnou k automatickému retušování aplikace, aby byla považována za selhání nebo úspěšnost. Určete prahové hodnoty pro každý Cloud.

**Ověřte dostupnost funkcí odolnosti proti chybám.** Určete dostupnost funkcí a schopností odolnosti proti chybám pro každé umístění. Pokud umístění neposkytuje požadované možnosti, zvažte integraci tohoto umístění do centralizované služby, která poskytuje funkce odolnosti.

**Určete prostoje.** Určete očekávané výpadky z důvodu údržby aplikace jako celku a jako součásti aplikace.

**Postupy řešení potíží v dokumentu.** Definujte postupy řešení potíží pro opětovné nasazení prostředků a součástí aplikací.

## <a name="manageability"></a>Možnosti správy

Důležité informace týkající se správy hybridních aplikací jsou důležité při návrhu vaší architektury. Dobře spravovaná hybridní aplikace poskytuje infrastrukturu jako kód, který umožňuje integraci konzistentního kódu aplikace ve společném vývojovém kanálu. Implementací konzistentního systému a individuálního testování změn infrastruktury můžete zajistit integrované nasazení, pokud změny projde testy, což umožňuje jejich sloučení do zdrojového kódu.

Základní diskusi k tomuto pilíři najdete v tématu [*DevOps*](/azure/architecture/framework/#devops) v pěti pilířích špičkové architektury.

### <a name="manageability-checklist"></a>Kontrolní seznam spravovatelnosti

**Implementujte monitorování.** Pomocí centralizovaného monitorovacího systému komponent aplikace rozšíříte napříč cloudy, abyste zajistili agregované zobrazení jejich stavu a výkonu. Tento systém zahrnuje monitorování součástí aplikace i souvisejících funkcí platformy.

Určete části aplikace, které vyžadují monitorování.

**Zásady souřadnic.** Každé umístění, které hybridní aplikace pokrývá, může mít vlastní zásady, které pokrývají povolené typy prostředků, konvence pojmenování, značky a další kritéria.

**Definujte a používejte role.** Jako správce databáze potřebujete určit oprávnění požadovaná pro různé osoby (například vlastníka aplikace, správce databáze a koncového uživatele), které potřebují přístup k prostředkům aplikace. Tato oprávnění je nutné nakonfigurovat u prostředků a uvnitř aplikace. Systém řízení přístupu na základě role (RBAC) umožňuje nastavit tato oprávnění pro prostředky aplikace. Tato přístupová práva jsou náročná na to, že jsou všechny prostředky nasazeny v jednom cloudu, ale vyžadují ještě větší pozornost, pokud jsou prostředky rozloženy mezi cloudy. Oprávnění k prostředkům nastaveným v jednom cloudu se nevztahují na prostředky nastavené v jiném cloudu.

**Použijte kanály CI/CD.** Kanál průběžné integrace a průběžného vývoje (CI/CD) může poskytovat konzistentní proces pro vytváření a nasazování aplikací, které jsou napříč cloudy, a zajištění kvality pro svou infrastrukturu a aplikaci. Tento kanál umožňuje testování infrastruktury a aplikace na jednom cloudu a nasazení na jiném cloudu. Kanál dokonce umožňuje nasadit určité komponenty hybridní aplikace do jednoho cloudu a dalších součástí do jiného cloudu, v podstatě tak, aby bylo základem nasazení hybridní aplikace. Systém CI/CD je velmi důležitý pro zpracování komponent aplikace závislosti v průběhu instalace, jako je třeba webová aplikace, která vyžaduje připojovací řetězec k databázi.

**Spravujte životní cyklus.** Vzhledem k tomu, že prostředky hybridní aplikace můžou zahrnovat umístění, musí být každá funkce správy životního cyklu jednoho umístění agregovaná do jedné jednotky pro správu životního cyklu. Vezměte v úvahu, jak se vytvářejí, aktualizují a odstraňují.

**Projděte si strategie odstraňování potíží.** Řešení potíží s hybridní aplikací zahrnuje více součástí aplikace než stejná aplikace, která běží v jednom cloudu. Kromě připojení mezi cloudy je aplikace spuštěná na dvou platformách, nikoli na jednom. Důležitou úlohou při řešení potíží s hybridními aplikacemi je prozkoumávat agregované monitorování stavu a sledování výkonu komponent aplikace.

## <a name="security"></a>Zabezpečení

Zabezpečení je jedním z hlavních předpokladů pro jakoukoli cloudovou aplikaci a je ještě důležitější pro hybridní cloudové aplikace.

Základní diskusi k tomuto pilíři najdete v tématu [*zabezpečení*](https://docs.microsoft.com/azure/architecture/guide/pillars#security) v pěti pilířích kvality architektury.

### <a name="security-checklist"></a>Kontrolní seznam zabezpečení

**Předpokládejte, že došlo k porušení.** Pokud dojde k ohrožení jedné části aplikace, zajistěte, aby byla k dispozici řešení pro minimalizaci rozšíření porušení, nikoli jenom ve stejném umístění, ale i v různých umístěních.

**Monitorovat povolený přístup k síti.** Určete zásady přístupu k síti pro aplikaci, jako je jenom přístup k aplikaci z konkrétní podsítě, a povolte jenom minimální porty a protokoly mezi součástmi, které aplikace potřebuje k tomu, aby správně fungovala.

**Využívají robustní ověřování.** Robustní schéma ověřování je důležité pro zabezpečení vaší aplikace. Zvažte použití federovaného poskytovatele identity, který poskytuje možnosti jednotného přihlašování a používá jedno nebo několik následujících schémat: uživatelské jméno a přihlášení k heslům, veřejné a privátní klíče, dvojúrovňové nebo vícefaktorové ověřování a důvěryhodné skupiny zabezpečení. Kromě typů certifikátů a jejich požadavků určete vhodné prostředky pro ukládání citlivých dat a dalších tajných kódů pro ověřování aplikací.

**Použijte šifrování.** Určete, které oblasti aplikace používají šifrování, například pro úložiště dat nebo komunikaci klientů a přístup.

**Používejte zabezpečené kanály.** Zabezpečený kanál napříč cloudy je důležitý pro poskytování kontrol zabezpečení a ověřování, ochrany v reálném čase, karantény a dalších služeb napříč cloudy.

**Definujte a používejte role.** Implementujte role pro konfigurace prostředků a přístup s jednou identitou napříč cloudy. Určete požadavky na řízení přístupu na základě role (RBAC) pro aplikaci a její prostředky platformy.

**Auditovat systém.** Sledování systému může protokolovat a agregovat data z komponent aplikace i souvisejících operací cloudové platformy.

## <a name="summary"></a>Souhrn

Tento článek poskytuje kontrolní seznam položek, které je důležité zvážit během vytváření a navrhování hybridních aplikací. Před tím, než aplikaci nasadíte, si Projděte tyto pilíře, abyste se nemuseli spouštět na tyto otázky v produkčních výpadkech a případně vyžadovali, abyste přenavštívili návrh.

Může se zdát jako časově náročný úkol předem, ale pokud navrhujete svou aplikaci na základě těchto pilířů, snadno získáte návratnost investic.

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících materiálech:

- [Hybridní cloud](https://azure.microsoft.com/overview/hybrid-cloud/)
- [Hybridní cloudové aplikace](https://azure.microsoft.com/solutions/hybrid-cloud-app/)
- [Vývoj šablon Azure Resource Manageru pro konzistenci cloudu](https://aka.ms/consistency)