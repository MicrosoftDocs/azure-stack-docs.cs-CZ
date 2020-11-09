---
title: Seznámení se sítěmi MDC (modulární Datacenter).
description: Přečtěte si o sítích pro zařízení MDC.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: conceptual
ms.date: 12/31/2019
ms.lastreviewed: 12/31/2019
ms.openlocfilehash: 4aec8b6dde194590d0bc5cb00f42869462fc365e
ms.sourcegitcommit: ce864e1d86ad05a03fe896721dea8f0cce92085f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2020
ms.locfileid: "94383543"
---
# <a name="modular-data-center-mdc-network-introduction"></a>Úvod do modulárního datového centra (MDC)

## <a name="package-content"></a>Obsah balíčků

Řešení se dodává s dalšími síťovými zařízeními, která jsou odkazována níže. Toto vybavení slouží k připojení kontejneru k síti.

Potvrďte Optický přepínač, který odpovídá vašemu prostředí.

| Množství | Typ | Model |
|-----|---------------|-------|
| 12 | 12x QSFP-40G | UVEDENO |
| 12 | 12x SFP + 10 GB | UVEDENO |
| 12 | 12x SFP 1G | UVEDENO |
| 12 | 12x QSFP-40G | LR |
| 12 | 12x SFP + 10 GB | LR |
| 12 | 12x SFP 1G | LR |

## <a name="network-design-overview"></a>Přehled návrhu sítě

### <a name="physical-network-design"></a>Návrh fyzické sítě

Řešení MDC vyžaduje odolnou a vysokou dostupnou fyzickou infrastrukturu pro podporu jeho provozu a služeb. Odchozí připojení z paměti k přepínačům ohraničení jsou omezená na SFP28a SFP + nebo na více než 1 GB, 10 GB nebo 40 GB.

Následující diagram představuje náš doporučený návrh pro MDC:

![Doporučený návrh fyzické sítě](media/network-introduction/physical-network-design.png)

### <a name="logical-network-design"></a>Návrh logické sítě

Návrh logické sítě představuje abstrakci fyzické síťové infrastruktury. Slouží k organizování a zjednodušení přiřazení sítě pro hostitele, virtuální počítače a služby. V rámci vytváření logických sítí se vytvoří síťové lokality, které definují:
- virtuální místní sítě (VLAN)
- Podsítě IP
- Páry podsítě protokolu IP/sítě VLAN

Všechny, které jsou spojeny s logickou sítí v každém fyzickém umístění.

Následující tabulka uvádí logické sítě a přidružené rozsahy podsítí IPv4, které je nutné naplánovat:

| **Logická síť** | **Popis** | **Velikost** |
|---------------------|-----------------|----------|
| Veřejná virtuální IP adresa (VIP) | MDC používá celkem 31 adres z této sítě. Osm veřejných IP adres se používá pro malou sadu MDC služeb a zbývající jsou používány virtuálními počítači klienta. Pokud plánujete použít App Service a poskytovatele prostředků SQL, použijí se 7 dalších adres. Zbývajících 15 IP adres se rezervuje pro budoucí služby Azure. | /26 (62 hostitelů)-/22 (1022 hostitelů) <br><Br>Doporučené =/24 (254 hostitelů) |
| Přepnout infrastrukturu | IP adresy Point-to-Point pro účely směrování, rozhraní pro správu vyhrazených přepínačů a adresy zpětné smyčky přiřazené přepínači. | za 26 |
| Infrastruktura | Slouží k komunikaci s interními komponentami MDC. | za 24 |
| Privátní | Používá se pro síť úložiště, privátní virtuální IP adresy, kontejnery infrastruktury a další interní funkce. | /20 |
| Řadič pro správu základní desky (BMC) | Slouží ke komunikaci s řadiči pro správu základní desky na fyzických hostitelích. | za 26 |
| Isilon | Slouží ke komunikaci s úložištěm Isilon. | 1x/25 mandát 1x/25 BMC (Správa) |

### <a name="network-infrastructure"></a>Síťová infrastruktura

Síťová infrastruktura pro MDC se skládá z několika logických sítí, které jsou nakonfigurované na přepínačích. Následující diagram znázorňuje tyto logické sítě a způsob jejich integrace s přepínači "rozvaděče", řadič pro správu základní desky a hraniční (zákaznická síť).

Diagram logické sítě MDC:

![Návrh logické sítě](media/network-introduction/logical-network-design.png)

#### <a name="bmc-network"></a>Síť řadiče pro správu základní desky

Tato síť je vyhrazena pro připojení všech řadičů pro správu základní desky (označovaných také jako BMC nebo procesory služeb) k síti pro správu. Mezi příklady patří: iDRAC, MOP, iBMC a tak dále. Ke komunikaci s jakýmkoli uzlem BMC se používá jenom jeden účet řadiče pro správu základní desky. Je-li k dispozici, je hostitel životního cyklu hardwaru (HLH) umístěn v této síti a může poskytovat software pro správu a údržbu hardwaru určený výrobcem OEM.

HLH také hostuje virtuální počítač nasazení (DVM). DVM se používá během nasazování MDC a po dokončení nasazení se odebere. DVM vyžaduje přístup k internetu ve scénářích propojeného nasazení, aby bylo možné testovat, ověřovat a přistupovat k několika komponentám. Tyto součásti můžou být uvnitř firemní sítě i mimo ni (například NTP, DNS a Azure). Další informace o požadavcích na připojení najdete v části věnované překladu adres (NAT) v tématu Integrace brány MDC firewall.

#### <a name="private-network"></a>Privátní síť

Síť/20 (IP adresy hostitele 4096) je privátní pro MDC oblast. Nerozšíří se za zařízení přepínače ohraničení oblasti MDC. Tato síť je rozdělená do několika podsítí, například:

- **Síť úložiště** : a/25 (128 IP adres), která se používá k podpoře použití prostorových přímých a přenosů úložiště protokolu SMB (Server Message Block) a migrace za provozu virtuálních počítačů.
- Interní virtuální IP síť: A/25 síť vyhrazenou pouze pro interní VIP pro nástroj pro vyrovnávání zatížení softwaru.
- **Síť kontejneru** : a/23 (512 IP adres), které jsou vyhrazené jenom pro interní přenosy mezi kontejnery, na kterých běží služby infrastruktury

Změnila se velikost privátní sítě/20 (4096 IP adres) privátního ADRESního prostoru. Tato síť je soukromá pro MDC systém. Netrasuje se nad rámec hraničních zařízení systému MDC a je možné ho znovu použít v několika MDC systémech. I když je síť soukromá pro MDC, nesmí se překrývat s ostatními sítěmi v datacentru. Pokyny k privátnímu adresnímu prostoru IP adres vám doporučujeme postupovat podle dokumentu RFC 1918.

Privátní IP adresa/20 je rozdělená do několika sítí, která umožňuje, aby se MDC systémová infrastruktura spouštěla na kontejnerech v budoucích verzích. Podrobnosti najdete v poznámkách k verzi 1910. Tato nová privátní IP adresa umožňuje průběžné úsilí snížit před nasazením požadovaný adresní prostor IP adres.

#### <a name="mdc-infrastructure-network"></a>Síť infrastruktury MDC

Síť/24 je vyhrazená pro interní součásti MDC, aby mohly komunikovat a vyměňovat data mezi sebou. Tuto podsíť je možné směrovat externě z řešení MDC do vašeho datacentra. V této podsíti **nedoporučujeme** používat veřejné nebo internetové IP adresy směrování. Tato síť se inzeruje na hranici, ale většina IP adres je chráněná pomocí seznamů Access Control (ACL). IP adresy povolené pro přístup jsou v malém rozsahu, který má stejnou velikost až do/27 sítě. Hostitelské služby s IP adresami, jako jsou privilegované koncové body (PEP) a zálohování MDC.

#### <a name="public-vip-network"></a>Síť veřejných virtuálních IP adres

Veřejná VIP síť je přiřazena k síťovému adaptéru v MDC. Nejedná se o logickou síť na přepínači. SLB používá fond adres a přiřazuje sítě/32 pro zatížení klientů. V tabulce směrování přepínače se tyto/32 IP adresy inzerují jako dostupná trasa přes Border Gateway Protocol (BGP). Tato síť obsahuje veřejné adresy, které jsou externě přístupné. Infrastruktura MDC si vyhrazuje prvních 31 adres z této veřejné sítě VIP, ale zbytek je využíván virtuálními počítači klienta. Velikost sítě v této podsíti může být v rozsahu od minimálně/26 (64 hostitelů) až do maximálního počtu/22 (1022 hostitelů). Doporučujeme, abyste naplánovali síť/24.

#### <a name="switch-infrastructure-network"></a>Přepnout síť infrastruktury

Síť/26 je podsíť, která obsahuje směrování podsítě IP adres Point-to-Point/30 (dvou hostitelských IP adres) a zpětné smyčky. Jedná se o vyhrazené podsítě/32 pro správu integrovaných přepínačů a ID směrovače protokolu BGP. Tento rozsah IP adres musí být směrovatelné mimo řešení MDC do vašeho datacentra. IP adresy můžou být privátní nebo veřejné.

#### <a name="switch-management-network"></a>Přepnout síť pro správu

Síť/29 (šest hostitelských IP adres) je vyhrazená pro připojení portů pro správu přepínačů. Tato síť umožňuje přístup mimo pásmo pro nasazení, správu a řešení potíží. Počítá se ze sítě infrastruktury přepínače uvedené výše.

#### <a name="isilon-network"></a>Isilon síť

K dispozici jsou dvě/25 sítí, jeden, který se nachází na přepínači pro použití rozhraní příkazového řadiče pro správu základní desky a jeden/25.

## <a name="dns-design-overview"></a>Přehled návrhu DNS

Pokud chcete získat přístup k koncovým bodům MDC ( *portál* , *adminportal* , *Management* , *adminmanagement* ) mimo MDC, musíte integrovat služby DNS MDC se servery DNS, které hostují zóny DNS, které chcete používat v MDC.

### <a name="mdc-dns-namespace"></a>Obor názvů DNS MDC

Když nasadíte MDC, budete muset poskytnout nějaké důležité informace týkající se DNS.

| **Pole** | **Popis** | **Příklad** |
|-----------|-----------------|-------------|
| Oblast | Zeměpisné umístění vašeho nasazení MDC. | *Atlantiku* |
| Název externí domény | Název zóny, kterou chcete použít pro nasazení MDC. | *cloud.fabrikam.com* |
| Interní název domény | Název interní zóny, která se používá pro služby infrastruktury v MDC. Je to integrovaná a soukromá adresářová služba (není dostupná z vnějšku nasazení MDC). | *azurestack. Local* |
| Servery DNS pro přeposílání | Servery DNS, které se používají k přeposílání dotazů DNS, zón DNS a záznamů hostovaných mimo MDC, a to buď na podnikovém intranetu nebo na veřejném Internetu. Po nasazení můžete hodnotu služby DNS resílat upravit pomocí rutiny **set-AzSDnsForwarder** . | |
| Předpona názvů (volitelné) | Předpona názvů, kterou chcete, aby názvy počítačů v instancích rolí infrastruktury MDC měly. Pokud není zadaný, použije se výchozí hodnota *AZS*. | *azs* |

Plně kvalifikovaný název domény (FQDN) vašeho nasazení MDC a koncových bodů je kombinací parametru region a parametru názvu externí domény. Pomocí hodnot z příkladů v předchozí tabulce bude plně kvalifikovaný název domény pro toto nasazení MDC: *East.Cloud.fabrikam.com* .

Například příklady některých koncových bodů tohoto nasazení by vypadaly jako následující adresy URL:

- `https://portal.east.cloud.fabrikam.com`
- `https://adminportal.east.cloud.fabrikam.com`

Pokud chcete tento příklad oboru názvů DNS pro nasazení MDC použít, vyžadují se tyto podmínky:

- Zóna fabrikam.com je registrovaná s doménovým registrátorem, interním podnikovým serverem DNS nebo obojím. Registrace závisí na požadavcích na překlad názvů.

- Podřízená doména cloud.fabrikam.com existuje v zóně fabrikam.com.

- Servery DNS, které hostují zóny fabrikam.com a cloud.fabrikam.com, jsou dostupné z nasazení MDC.

Chcete-li přeložit názvy DNS pro koncové body a instance MDC mimo MDC, je nutné integrovat servery DNS. Včetně serverů, které jsou hostiteli externí zóny DNS pro MDC, se servery DNS, které hostují nadřazenou zónu, kterou chcete použít.

#### <a name="dns-name-labels"></a>Popisky názvů DNS

MDC podporuje přidání popisku názvu DNS k veřejné IP adrese, aby bylo možné překlad názvů pro veřejné IP adresy. Popisky DNS představují pohodlný způsob, jak uživatelům oslovit aplikace a služby hostované v MDC podle názvu. Popisek názvu DNS používá mírně odlišný obor názvů než koncové body infrastruktury. V následujícím ukázkovém oboru názvů bude obor názvů pro popisky názvů DNS: *\* . East.cloudapp.Cloud.fabrikam.com*. 

Pokud tenant určí pole **MyApp** v poli popisek názvu DNS prostředku veřejné IP adresy, vytvoří záznam a pro MyApp v zóně **East.cloudapp.Cloud.fabrikam.com** na externím serveru DNS MDC. Výsledný plně kvalifikovaný název domény by byl: *MyApp.East.cloudapp.Cloud.fabrikam.com*. 

Pokud chcete tuto funkci využít a použít tento obor názvů, musíte integrovat servery DNS. Včetně serverů, které jsou hostiteli externí zóny DNS pro MDC, a serverů DNS, které hostují nadřazenou zónu, kterou chcete použít taky. Tento obor názvů je jiný než ten, který se používá pro koncové body služby MDC, takže musíte vytvořit další pravidlo delegování nebo podmíněného předávání.

Další informace o tom, jak popisek názvu DNS funguje, najdete v tématu použití DNS v MDC.

### <a name="resolution-and-delegation"></a>Překládání a delegování

Existují dva typy serverů DNS:

- Autoritativní server DNS hostí zóny DNS. Odpovídá pouze na dotazy DNS pro záznamy v těchto zónách.

- Rekurzivní server DNS nehostuje zóny DNS. Odpovídá na všechny dotazy DNS voláním autoritativních serverů DNS, které shromáždí potřebná data.

MDC zahrnuje autoritativní i rekurzivní servery DNS. Rekurzivní servery se používají k překladu názvů všeho s výjimkou interní privátní zóny a externí veřejné zóny DNS pro nasazení MDC.

### <a name="resolving-external-dns-names-from-mdc"></a>Překlad externích názvů DNS z MDC

Chcete-li přeložit názvy DNS pro koncové body mimo MDC (například: www.bing.com), je nutné poskytnout servery DNS pro MDC k přeposílání požadavků DNS, pro které není MDC autoritativní. V listu nasazení (v poli pro přeposílání DNS) se vyžadují servery DNS, na které MDC požadavky na přeposílání. Pro odolnost proti chybám zadejte v tomto poli aspoň dva servery. Bez těchto hodnot se nasazení MDC nezdařilo. Po nasazení můžete hodnoty DNS pro přeposílání upravit pomocí rutiny **set-AzSDnsForwarder** .

## <a name="firewall-design-overview"></a>Přehled návrhu brány firewall

Doporučuje se použít pro usnadnění zabezpečení MDC zařízení brány firewall. Brány firewall můžou přispět k obraně proti akcím, jako jsou například distribuované útoky s cílem odepření služeb (DDOS), zjišťování vniknutí a kontrola obsahu. Můžou se ale taky stát kritickými body pro služby Azure Storage, jako jsou objekty blob, tabulky a fronty.

Pokud se používá režim odpojeného nasazení, je nutné publikovat AD FS koncový bod. Další informace najdete v článku věnovaném identitě pro integraci Datacenter.

Koncové body pro Azure Resource Manager (správce), portál pro správu a Key Vault (správce) nemusí nutně vyžadovat externí publikování. Například jako poskytovatel služeb můžete omezit plochu pro útok tím, že pouze spravujete MDC zevnitř vaší sítě, nikoli z Internetu.

V případě podnikových organizací může být externí síť stávající podnikovou sítí. V tomto scénáři musíte publikovat koncové body pro provozování MDC z podnikové sítě.

### <a name="network-address-translation"></a>Překlad síťových adres

Překlad síťových adres (NAT) je doporučená metoda, která umožňuje virtuálnímu počítači pro nasazení (DVM) získat přístup k externím prostředkům během nasazování. Také pro virtuální počítače ERCS (Emergency Recovery Console) nebo privilegovaného koncového bodu (PEP) během registrace a odstraňování potíží.

Překlad adres (NAT) může být také alternativou k veřejným IP adresám na externí nebo veřejné VIP. To se ale nedoporučuje, protože to omezuje činnost koncového uživatele tenanta a zvyšuje složitost. Jednou z možností je jeden pro překlad adres (NAT), který stále vyžaduje jednu veřejnou IP adresu pro uživatele ve fondu. Další možností je celá řada pro překlad adres (NAT), která vyžaduje pravidlo překladu adres (NAT) na uživatelskou VIP pro všechny porty, které uživatel může použít.

K downsides používání protokolu NAT pro veřejné virtuální IP adresy patří:

- Režie při správě pravidel brány firewall, protože uživatelé řídí jejich vlastní koncové body a pravidla publikování v zásobníku softwarově definované sítě (SDN). Aby uživatelé mohli své virtuální IP adresy publikovat a aktualizovat seznam portů, musí se obrátit na operátor MDC. 
- I když použití překladu adres (NAT) omezuje činnost koncového uživatele, poskytuje operátorovi úplnou kontrolu nad požadavky publikování.
- V případě hybridních cloudových scénářů s Azure zvažte, že Azure nepodporuje nastavování tunelu VPN pro koncový bod pomocí překladu adres (NAT).

### <a name="ssl-interception"></a>Zachycení SSL

V současné době doporučujeme zakázat jakékoli zachycení SSL (například snižování zátěže) u všech MDC přenosů. Pokud je podpora v budoucích aktualizacích podporovaná, poskytnou se pokyny, jak povolit zachycení SSL pro MDC.

### <a name="edge-deployment-firewall-scenario"></a>Scénář brány firewall nasazení Edge

V nasazení Edge je MDC nasazen přímo za hraničním směrovačem nebo bránou firewall. V těchto scénářích se podporuje, aby brána firewall byla nad hranicí (scénář 1), kde podporuje konfigurace brány firewall aktivní-aktivní i aktivní – pasivní. Může také fungovat jako hraniční zařízení (scénář 2), kde podporuje pouze konfiguraci brány firewall aktivní-aktivní. Scénář 2 se pro převzetí služeb při selhání spoléhá na ECMP (EQUAL-cost multi-Path) s protokolem BGP nebo statickým směrováním.

Veřejné IP adresy určené pro veřejný fond VIP z externí sítě v době nasazení. Z bezpečnostních důvodů se veřejné směrovatelné **IP adresy** nedoporučují v žádné jiné síti ve scénáři Edge. Tento scénář umožňuje uživateli vyzkoušet si plně řízené cloudové prostředí jako ve veřejném cloudu, jako je Azure.

 ![Scénář brány firewall pro MDC Edge](media/network-introduction/edge-firewall-scenario-60.png) 

### <a name="enterprise-intranet-or-perimeter-network-firewall-scenario"></a>Scénář pro bránu firewall Enterprise intranet nebo hraniční sítě

V podnikovém intranetu nebo hraničním nasazení je MDC nasazený v bráně firewall s více zónami nebo mezi hraniční branou firewall a interní bránou firewall podnikové sítě. Provoz se pak distribuuje mezi zabezpečenou, hraniční sítí (nebo DMZ) a nezabezpečenými zónami, jak je popsáno níže:

- **Zabezpečená zóna** : interní síť, která používá interní nebo firemní IP adresy, které se používají. Zabezpečenou síť lze rozdělit. Může mít internetový odchozí přístup prostřednictvím NAT brány firewall. Obvykle je k dispozici ve vašem datovém centru prostřednictvím interní sítě. Všechny sítě MDC by se měly nacházet v zabezpečené zóně, s výjimkou veřejného fondu VIP externí sítě.
- **Hraniční zóna** : hraniční síť je obvykle nasazená na externí nebo internetové aplikace, jako jsou webové servery. Bránu firewall obvykle monitoruje, aby nedocházelo k útokům, jako je DDoS a vniknutí (hacker), a zároveň přitom povoluje zadaný příchozí provoz z Internetu. V zóně DMZ by se měl nacházet jenom MDC fond virtuálních IP adres externí sítě. 
- **Nezabezpečená zóna** : externí síť, Internet. Nasazení MDC v nezabezpečené **zóně se** nedoporučuje.

![Scénář brány firewall hraniční sítě](media/network-introduction/perimeter-network-firewall-scenario-50.png) 

## <a name="vpn-design-overview"></a>Přehled návrhu sítě VPN

I když je síť VPN konceptem uživatele, existují některé důležité důležité informace, které vlastník a operátor řešení potřebují znát.

Než budete moct poslat síťový provoz mezi virtuální sítí Azure a vaší místní lokalitou, musíte pro svoji virtuální síť vytvořit bránu virtuální sítě (VPN).

Brána VPN je typem brány virtuální sítě, která odesílá šifrovaný síťový provoz přes veřejné spojení. Brány VPN můžete použít k bezpečnému posílání provozu mezi virtuální sítí v MDC a virtuální sítí v Azure. Mezi virtuální sítí a jinou sítí, která je připojená k zařízení VPN, můžete také bezpečně odesílat přenosy.

Při vytváření brány virtuální sítě musíte určit typ brány, který chcete vytvořit. MDC podporuje jeden typ brány virtuální sítě: typ sítě VPN.

Každá virtuální síť může mít dvě brány virtuální sítě, ale každého typu jenom jednu. V závislosti na nastavení, které zvolíte, můžete k jedné bráně VPN vytvořit několik připojení. Příkladem tohoto typu instalace je konfigurace připojení s více lokalitami.

Než vytvoříte a nakonfigurujete brány VPN pro MDC, přečtěte si téma požadavky na MDC Networking. Zjistíte, jak se konfigurace pro MDC liší od Azure.

V Azure se propustnost šířky pásma pro SKLADOVOU položku brány VPN, kterou zvolíte, musí rozdělit mezi všechna připojení, která jsou připojená k bráně. V MDC se ale hodnota šířky pásma pro SKU brány VPN použije u každého prostředku připojení, který je připojený k bráně. Zde je příklad: 

- V Azure může základní propustnost služby VPN Gateway vyhovět přibližně 100 MB/s agregované propustnosti. Pokud vytvoříte dvě připojení k této bráně VPN a jedno připojení používá 50 MB/s šířky pásma, pak je k dispozici 50 MB/s pro druhé připojení.

- V MDC se každé připojení k SKU služby VPN Gateway úrovně Basic přiděluje 100 MB/s propustnost.

### <a name="vpn-types"></a>Typy sítě VPN

Když vytvoříte bránu virtuální sítě pro konfiguraci brány sítě VPN, musíte zadat typ sítě VPN. Typ sítě VPN, který zvolíte, závisí na topologii připojení, kterou chcete vytvořit. Typ sítě VPN může také záviset na hardwaru, který používáte. Konfigurace S2S vyžadují zařízení VPN. Některá zařízení VPN podporují jenom určitý typ sítě VPN.

>[!IMPORTANT]
> V současné době MDC podporuje pouze typ sítě VPN založené na trasách. Pokud vaše zařízení podporuje jenom sítě VPN založené na zásadách, pak připojení k těmto zařízením z MDC nejsou podporovaná. V tuto chvíli nepodporuje MDC používání selektorů přenosu na základě zásad pro brány založené na trasách, protože vlastní konfigurace zásad IPSec/IKE se nepodporují. 

- **PolicyBased** : sítě VPN založené na zásadách šifrují a směrují pakety prostřednictvím tunelů IPsec v závislosti na zásadách IPSec. Zásady se konfigurují s kombinacemi předpon adres mezi vaší místní sítí a virtuální sítí MDC. Zásada nebo selektor provozu je obvykle seznam přístupu v konfiguraci zařízení VPN. **PolicyBased** se podporuje v Azure, ale ne v MDC. 
- **RouteBased** : sítě VPN založené na směrování používají trasy, které jsou nakonfigurované v tabulce předávání IP nebo směrovací tabulky. Směruje pakety přímé na odpovídající rozhraní tunelového propojení. Rozhraní tunelového propojení potom šifrují nebo dešifrují pakety směřující do tunelových propojení nebo z nich. Zásady nebo selektor provozu pro sítě VPN **RouteBased** jsou nakonfigurovány jako libovolné (nebo používají zástupné karty). Ve výchozím nastavení se nedají změnit. Hodnota pro typ VPN **RouteBased** je **RouteBased**.

### <a name="configuring-a-vpn-gateway"></a>Konfigurace služby VPN Gateway

Připojení k bráně VPN Gateway závisí na několika prostředcích, které jsou nakonfigurovány se specifickým nastavením. Většinu těchto prostředků je možné nakonfigurovat samostatně, ale v některých případech musí být nakonfigurovány v určitém pořadí.

#### <a name="settings"></a>Nastavení

Nastavení, které zvolíte pro každý prostředek, je důležité pro vytvoření úspěšného připojení.

Tento článek vám pomůže pochopit tyto informace:

- Typy bran, typy sítí VPN a typy připojení.
- Podsítě brány, místní síťové brány a další nastavení prostředků, která si možná budete chtít zvážit.

#### <a name="connection-topology-diagrams"></a>Diagramy topologie připojení

K dispozici jsou různé konfigurace pro připojení brány VPN. Určete, která konfigurace nejlépe vyhovuje vašim potřebám. V následujících částech si můžete zobrazit informace a diagramy topologie o následujících připojeních k bráně VPN Gateway:

- dostupný model nasazení,
- dostupné konfigurační nástroje,
- odkazy na příslušný článek, pokud existuje.

Diagramy a popisy v následujících částech vám pomůžou vybrat topologii připojení, která bude odpovídat vašim požadavkům. Diagramy znázorňují hlavní topologie standardních hodnot, ale je možné vytvořit složitější konfigurace pomocí diagramů jako průvodce.

#### <a name="site-to-site-and-multi-site-ipsecike-vpn-tunnel"></a>Síť typu Site-to-site a Multi-Site (tunel VPN IPsec/IKE)

##### <a name="site-to-site"></a>Připojení typu site-to-site

Připojení brány VPN typu Site-to-Site (S2S) je připojení přes tunelové připojení VPN pomocí protokolu IPsec/IKE (IKEv2). Tento typ připojení vyžaduje zařízení VPN, které je umístěné místně a má přiřazenou veřejnou IP adresu. Toto zařízení se nemůže nacházet za překladem adres (NAT). Připojení S2S můžete použít pro konfigurace mezi různými místy a pro hybridní konfigurace.

##### <a name="multi-site"></a>Připojení typu multi-site (pro více lokalit)

Připojení k více lokalitám je varianta připojení typu Site-to-site. Z brány virtuální sítě vytvoříte několik připojení VPN, obvykle pro připojení k několika místním lokalitám. Při práci s více připojeními je nutné použít typ sítě VPN založený na trasách (označovaný jako dynamická brána při práci s klasickým virtuální sítě). Vzhledem k tomu, že virtuální síť může mít jenom jednu bránu virtuální sítě, všechna připojení prostřednictvím brány sdílejí dostupnou šířku pásma.

### <a name="gateway-skus"></a>Skladové položky brány

Když vytvoříte bránu virtuální sítě pro MDC, zadáte SKU brány, které chcete použít. Podporovány jsou následující SKU brány VPN:

- Basic
- Standard
- High Performance

Vyberete-li vyšší SKU brány, přidělíte k bráně více procesorů a šířku pásma sítě. V důsledku toho může Brána podporovat vyšší propustnost sítě pro virtuální síť.

MDC nepodporuje SKLADOVOU položku brány pro ultra Performance, která se používá výhradně se službou Express Route. Při výběru SKU Vezměte v úvahu následující:

- MDC nepodporuje brány založené na zásadách.
- Protokol BGP není podporován pro základní SKU.
- ExpressRoute – existující konfigurace brány VPN Gateway nejsou v MDC podporované.

#### <a name="gateway-availability"></a>Dostupnost brány

Scénáře s vysokou dostupností se dají konfigurovat jenom na skladové jednotce připojení High-Performance brány. Na rozdíl od Azure, který poskytuje dostupnost prostřednictvím aktivní/aktivní i aktivní/pasivní konfigurace, MDC podporuje pouze konfiguraci aktivní/pasivní.

#### <a name="failover"></a>Převzetí služeb při selhání

Existují tři virtuální počítače infrastruktury víceklientské brány v MDC. Dva z těchto virtuálních počítačů jsou v aktivním režimu a třetí je v redundantním režimu. Aktivní virtuální počítače umožňují vytváření připojení k síti VPN a redundantní virtuální počítač akceptuje připojení VPN jenom v případě, že dojde k převzetí služeb při selhání. Pokud virtuální počítač aktivní brány přestane být dostupný, připojení VPN se po krátké době (několik sekund) převezme k redundantnímu virtuálnímu počítači po krátkou dobu (několik sekund) ztráty připojení.

### <a name="estimated-aggregate-throughput-by-sku"></a>Odhadovaná agregovaná propustnost podle typů SKU

Následující tabulka ukazuje typy brány a odhadovanou agregovanou propustnost pomocí SKU brány:

| | **Propustnost brány sítě VPN (1)** | **Maximální počet tunelových propojení IPsec brány sítě VPN (2)** |
|-|--------------------------------|---------------------------------------|
| Základní SKU (3) | 100 Mb/s | 20 |
| Standardní SKU | 100 Mb/s | 20 |
| SKU High-Performance | 200 Mb/s | 10 |

Poznámky tabulky

(1) – propustnost sítě VPN není zaručená propustnost pro připojení mezi různými místy přes Internet. Je to maximální možné měření propustnosti.  
(2) – maximální počet tunelů je celkové nasazení na MDC pro všechna předplatná.  
(3) – pro základní SKU není podporováno směrování protokolu BGP.

>[!IMPORTANT]
> Mezi dvěma nasazeními MDC se dá vytvořit jenom jedno připojení typu Site-to-Site VPN. Důvodem je omezení platformy, která umožňuje jenom jedno připojení VPN ke stejné IP adrese. Vzhledem k tomu, že MDC využívá víceklientské brány, která používá jednu veřejnou IP adresu pro všechny brány VPN v systému MDC, může být mezi dvěma MDC systémy jenom jedno připojení VPN. 
>
> Toto omezení platí i pro připojení více než jednoho připojení VPN typu Site-to-site k libovolné bráně VPN, která používá jednu IP adresu. MDC neumožňuje vytvoření více než jednoho prostředku brány místní sítě, který používá stejnou IP adresu.

### <a name="ipsecike-parameters"></a>Parametry protokolu IPsec/IKE

Když nastavíte připojení VPN v MDC, musíte nakonfigurovat připojení na obou koncích. Pokud konfigurujete připojení VPN mezi MDC a hardwarovým zařízením, můžete si toto zařízení vyžádat o další nastavení. Například přepínač nebo směrovač, který funguje jako brána sítě VPN.

Na rozdíl od Azure, který podporuje více nabídek jako iniciátor i respondér, podporuje MDC ve výchozím nastavení jenom jednu nabídku. Pokud pro práci se zařízením VPN potřebujete použít jiné nastavení protokolu IPSec/IKE, máte k dispozici více nastavení pro ruční konfiguraci připojení.

#### <a name="ike-phase-1-main-mode-parameters"></a>Parametry protokolu IKE fáze 1 (hlavní režim)

| **Vlastnost** | **Hodnota** |
|--------------|-----------|
| Verze IKE | IKEv2 |
| Skupina Diffie-Hellman | ECP384 |
| Metoda ověřování | Předsdílený klíč |
| Algoritmy šifrování a hash | AES256, SHA384 |
| Životnost SA (čas) | 28 800 sekund |

#### <a name="ike-phase-2-quick-mode-parameters"></a>Parametry protokolu IKE fáze 2 (rychlý režim)

| **Vlastnost** | **Hodnota** |
|--------------|-----------|
| Verze IKE | IKEv2 |
| Šifrování & algoritmy hash (šifrování) | GCMAES256 |
| Šifrování šifrovacích & algoritmů hash (ověřování) | GCMAES256 |
| Životnost SA (čas) | 27 000 sekund |
| Životnost SA (kilobajty) | 33 553 408 |
| Metoda Perfect Forward Secrecy (PFS) | ECP384 |
| Detekce mrtvých partnerských zařízení | Podporováno |

### <a name="configure-custom-ipsecike-connection-policies"></a>Konfigurace vlastních zásad připojení IPSec/IKE

Protokol IPsec a IKE standard podporuje široké spektrum kryptografických algoritmů v různých kombinacích. Pokud chcete zjistit, které parametry jsou v MDC podporované pro splnění požadavků na dodržování předpisů nebo požadavky na zabezpečení, přečtěte si téma parametry IPsec/IKE.

Tento článek poskytuje pokyny, jak vytvořit a nakonfigurovat zásadu IPsec/IKE a použít ji pro nové nebo existující připojení.

#### <a name="considerations"></a>Požadavky

Při používání těchto zásad Vezměte v vědomí následující důležité informace:

- Zásady IPsec/IKE fungují jenom na SKU brány Standard a HighPerformance (na základě tras).
- Pro jedno připojení můžete zadat pouze jednu kombinaci zásad.
- Je nutné zadat všechny algoritmy a parametry pro protokol IKE (hlavní režim) i pro protokol IPsec (rychlý režim). Částečná specifikace zásad není povolená.
- Pokud chcete zajistit, aby se zásady na místních zařízeních VPN podporovaly, kontaktujte specifikace dodavatele zařízení VPN. Připojení Site-to-site nelze vytvořit, pokud jsou zásady nekompatibilní.

#### <a name="workflow-to-create-and-set-ipsecike-policy"></a>Pracovní postup vytvoření a nastavení zásad IPsec/IKE

Tato část popisuje pracovní postup nutný k vytvoření a aktualizaci zásad IPsec/IKE na připojení VPN typu Site-to-site:

1. Vytvořte virtuální síť a bránu VPN.
2. Vytvořte bránu místní sítě pro připojení mezi různými místy.
3. Vytvořte zásadu IPsec/IKE s vybranými algoritmy a parametry.
4. Vytvořte připojení IPSec pomocí zásad IPsec/IKE.
5. Přidat nebo aktualizovat nebo odebrat zásady IPsec/IKE pro existující připojení.

#### <a name="supported-cryptographic-algorithms-and-key-strengths"></a>Podporované kryptografické algoritmy a síly klíčů

Následující tabulka uvádí podporované kryptografické algoritmy a síly klíče, které můžou zákazníci MDC konfigurovat:

| **IPsec/IKEv2** | **Možnosti** |
|-----------------|-------------|
| Šifrování protokolem IKEv2 | AES256, AES192, AES128, DES3, DES |
| Integrita protokolu IKEv2 | SHA384, SHA256, SHA1, MD5 |
| Skupina DH | ECP384, ECP256, DHGroup14, DHGroup2048, DHGroup2, DHGroup1, None |
| Šifrování protokolem IPsec | GCMAES256, GCMAES192, GCMAES128, AES256, AES192, AES128, DES3, DES, Žádné |
| Integrita protokolu IPsec | GCMASE256, GCMAES192, GCMAES128, SHA256, SHA1, MD5 |
| Skupina PFS | PFS24, ECP384, ECP256, PFS2048, PFS2, PFS1, Žádná |
| Doba života přidružení zabezpečení v rychlém režimu | (Volitelné: použijí se výchozí hodnoty, pokud není zadaný) |
| | Sekundy (Integer; min. 300/výchozí 27 000 sekund) |
| | Kilobajty (Integer; min. 1024/výchozí 102 400 000 KB) |
| Selektor provozu | V MDC se nepodporují selektory provozu na základě zásad. |

Konfigurace vašeho místního zařízení VPN musí odpovídat zásadám brány Azure VPN Gateway nebo musí obsahovat následující algoritmy a parametry, které zadáte v zásadách IPsec/IKE Azure:

 - Šifrovací algoritmus IKE (hlavní režim/fáze 1).
 - Algoritmus integrity protokolu IKE (hlavní režim/fáze 1).
 - Skupina DH (hlavní režim/fáze 1)
 - Šifrovací algoritmus IPsec (rychlý režim/fáze 2).
 - Algoritmus integrity protokolu IPsec (rychlý režim/fáze 2).
 - Skupina PFS (rychlý režim/fáze 2).
 - Životnosti přidružení zabezpečení jsou pouze místní specifikace, nemusí se shodovat.

Pokud se pro šifrovací algoritmus IPsec používá GCMAES, musíte vybrat stejný algoritmus GCMAES a délku klíče pro integritu protokolu IPsec. Například: použití GCMAES128 pro obojí.

V tabulce:

 - IKEv2 odpovídá hlavnímu režimu nebo fázi 1.
 - Protokol IPsec odpovídá rychlému režimu nebo fázi 2.
 - Skupina DH určuje Diffie-Hellmen skupinu použitou v hlavním režimu nebo ve fázi 1.
 - Skupina PFS určuje Diffie-Hellmen skupinu použitou v rychlém režimu nebo ve fázi 2.
- Životnost SA hlavního režimu IKEv2 je opravena na 28 800 sekund v bránách VPN MDC.

Následující tabulka obsahuje seznam odpovídajících skupin Diffie-Hellman podporovaných vlastními zásadami:

| **Skupina Diffie-Hellman** | **DHGroup** | **PFSGroup** | **Délka klíče** |
|--------------------------|-------------|--------------|----------------|
| 1 | DHGroup1 | PFS1 | 768bitová skupina MODP |
| 2 | DHGroup2 | PFS2 | 1024bitová skupina MODP |
| 14 | DHGroup14 | PFS2048 | 2048bitová skupina MODP |
| | DHGroup2048 | | |
| 19 | ECP256 | ECP256 | 256bitová skupina ECP |
| 20 | ECP384 | ECP384 | 384bitová skupina ECP |
| 24 | DHGroup24 | PFS24 | 2048bitová skupina MODP |

### <a name="connect-mdc-to-azure-using-azure-expressroute"></a>Připojení MDC k Azure pomocí Azure ExpressRoute

#### <a name="overview-assumptions-and-prerequisites"></a>Přehled, předpoklady a předpoklady

Azure ExpressRoute umožňuje rozšiřování místních sítí do cloudu Microsoftu. Použijete privátní připojení, které poskytuje poskytovatel připojení. ExpressRoute není připojením VPN přes veřejný Internet.

Další informace o Azure ExpressRoute najdete v tématu Přehled ExpressRoute.

#### <a name="assumptions"></a>Předpoklady

V tomto článku se předpokládá, že:

- Máte praktické znalosti Azure.
- Máte základní znalosti o MDC.
- Máte základní znalosti o sítích.

#### <a name="prerequisites"></a>Požadavky

Pokud chcete připojit MDC a Azure pomocí ExpressRoute, musíte splnit následující požadavky:

- Zřízený okruh ExpressRoute prostřednictvím poskytovatele připojení.
- Předplatné Azure pro vytvoření okruhu ExpressRoute a virtuální sítě v Azure.
- Směrovač, který musí:
  - Podpora připojení VPN typu Site-to-site mezi rozhraním LAN a Azure Stack bránou pro více tenantů.
  - Podpora vytváření více VRFs (virtuální směrování a předávání), pokud je ve vašem nasazení MDC více než jeden tenant.

- Směrovač, který má:
  - Port sítě WAN připojený k okruhu ExpressRoute.
  - Port LAN připojený k MDC bráně pro více tenantů.

#### <a name="expressroute-network-architecture"></a>Architektura sítě ExpressRoute

Následující obrázek znázorňuje prostředí MDC a Azure po dokončení nastavení ExpressRoute pomocí příkladů v tomto článku:

 ![Architektura sítě ExpressRoute](media/network-introduction/expressroute-network-architecture-60.png) 

