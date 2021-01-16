---
title: Požadavky na síť hostitele pro Azure Stack HCI
description: Seznamte se s požadavky na síť hostitele pro Azure Stack HCI
author: v-dasis
ms.topic: how-to
ms.date: 11/25/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 3e31852e554c85ffab18aacaa336a007a97874f2
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2021
ms.locfileid: "98255295"
---
# <a name="host-network-requirements-for-azure-stack-hci"></a>Požadavky na síť hostitele pro Azure Stack HCI

> Platí pro Azure Stack HCI, verze 20H2

Toto téma popisuje Azure Stack a požadavky na síťové sítě a požadavky na hostitele HCI. Informace o architekturách datového centra a fyzických připojeních mezi servery najdete v tématu [požadavky na fyzickou síť](physical-network-requirements.md).

## <a name="network-traffic-types"></a>Typy síťových přenosů

Azure Stack může být síťový provoz HCI klasifikován podle zamýšleného účelu:

- **Výpočetní provoz** – provoz pocházející z nebo určený pro virtuální počítač (VM)
- **Provoz úložiště** pro prostory úložiště S přímým přístupem (S2D) pomocí protokolu SMB (Server Message Block)
- **Provoz správy** – provoz je důležitý pro správce správy clusterů, jako je například služba Active Directory, Vzdálená plocha, centrum pro správu systému Windows a prostředí Windows PowerShell.

## <a name="selecting-a-network-adapter"></a>Výběr síťového adaptéru

Pro Azure Stack HCI vyžadujeme, abyste vybrali síťový adaptér, který dosáhl certifikace Windows serveru Software-Defined Datacenter (SDDC) s využitím dodatečné kvalifikace Standard nebo Premium (AQ). Tyto adaptéry podporují nejvíce pokročilých funkcí platformy a prošly tím nejvyšším testováním našimi hardwarovými partnery. Tato úroveň kontroly obvykle vede k omezení potíží s kvalitou hardwaru a ovladačů.

Můžete určit adaptér, který má AQ úrovně Standard nebo Premium, a to tak, že zkontrolujete položku [katalogu Windows serveru](https://www.windowsservercatalog.com/) pro příslušný adaptér a příslušnou verzi operačního systému. Níže je uveden příklad zápisu pro Premium AQ:

:::image type="content" source="media/plan-networking/windows-certified.png" alt-text="Certifikovaný systém Windows" lightbox="media/plan-networking/windows-certified.png":::

## <a name="overview-of-key-network-adapter-capabilities"></a>Přehled schopností síťových adaptérů pro klíč

Důležité možnosti síťového adaptéru, které využívají Azure Stack HCI, zahrnují:

- Dynamická vícenásobná fronta virtuálních počítačů (dynamická VMMQ nebo d. VMMQ)
- Přímý přístup do paměti vzdáleného počítače (RDMA)
- Host RDMA
- Switch Embedded Teaming (SET)

### <a name="dynamic-vmmq"></a>Dynamické VMMQ

Všechny síťové adaptéry s podporou Premium AQ podporují Dynamic VMMQ. Dynamické VMMQ vyžaduje použití seskupování vloženého přepínače.

**Použitelné typy provozu**: COMPUTE

**Vyžadované certifikace**: Premium

Dynamická VMMQ je inteligentní technologie pro přijímání na straně příjmu, která se vytváří na základě jejich předchůdců Fronta pro virtuální počítače (VMQ) (VMQ), virtuálního škálování na straně příjmu (vRSS) a VMMQ, aby poskytoval tři primární vylepšení:

- Optimalizuje efektivitu hostitele pomocí PROCESORových jader.
- Automatické ladění zpracování síťového provozu na jádra procesoru, čímž umožníte virtuálním počítačům naplnit a udržovat očekávanou propustnost
- Umožňuje úlohám "shlukování" získat očekávaný objem provozu.

Další informace o dynamických VMMQ najdete v blogovém příspěvku [syntetických zrychlení](https://techcommunity.microsoft.com/t5/networking-blog/synthetic-accelerations-in-a-nutshell-windows-server-2019/ba-p/653976).

### <a name="rdma"></a>RDMA

RDMA je snižování zátěže síťového zásobníku na síťový adaptér, který umožňuje provoz úložiště protokolu SMB obejít operační systém pro zpracování.

RDMA umožňuje sítě s vysokou propustností a nízkou latencí při používání minimálních prostředků procesoru hostitele. Tyto prostředky hostitelského procesoru pak můžete použít ke spouštění dalších virtuálních počítačů nebo kontejnerů.

**Použitelné typy provozu**: úložiště hostitele

**Vyžadované certifikace**: Standard

Všechny adaptéry s podporou Standard nebo Premium AQ podporují RDMA (přímý přístup do paměti vzdáleného počítače). RDMA je doporučený výběr nasazení pro úlohy úložiště v Azure Stack HCI a může být volitelně povolen pro úlohy úložiště (pomocí protokolu SMB) pro virtuální počítače. Další informace najdete v části **Host RDMA pro hosty** .

Azure Stack HCI podporuje RDMA buď pomocí implementace protokolu iWARP nebo RoCE.

> [!IMPORTANT]
> Adaptéry RDMA fungují jenom s ostatními adaptéry RDMA, které implementují stejný protokol RDMA (iWARP nebo RoCE).

Ne všechny síťové adaptéry od dodavatelů podporují RDMA. V následující tabulce jsou uvedeni dodavatelé (v abecedním pořadí), které nabízejí certifikované adaptéry RDMA. V tomto seznamu ale nejsou k dispozici dodavatelé hardwaru, kteří také podporují RDMA. Ověřte podporu RDMA v [katalogu Windows serveru](https://www.windowsservercatalog.com/) .

> [!NOTE]
> InfiniBand (IB) není u Azure Stack HCL podporováno.

|Dodavatel síťových adaptérů|iWARP|RoCE|
|----|----|----|
|Broadcom|Ne|Ano|
|Chelsio|Ano|Ne|
|RSS|Ano|Ano (některé modely)|
|PERC (QLogic/Cavium)|Ano|Ano|
|NVIDIA (Mellanox)|Ne|Ano|

> [!NOTE]
> Ne všechny adaptéry od dodavatelů podporují RDMA. Ověřte prosím podporu RDMA u konkrétního dodavatele síťových karet.

Pokud chcete získat další informace o nasazení RDMA, Stáhněte si dokument Word z [úložiště GitHub SDN](https://github.com/Microsoft/SDN/blob/master/Diagnostics/S2D%20WS2016_ConvergedNIC_Configuration.docx).

#### <a name="internet-wide-area-rdma-protocol-iwarp"></a>Protokol Internet iWARP (Internet WAN Area RDMA)

iWARP používá protokol TCP (Transmission Control Protocol) a je možné ho volitelně rozšířit pomocí řízení toku založeného na prioritách DCB (PFC) a služby Enhanced Transmission Service (ETS).

Doporučujeme použít iWARP, pokud:

- Máte málo nebo žádné síťové prostředí nebo se Uncomfortable Správa síťových přepínačů.
- Neřídíte přepínačům pro mandát.
- Řešení nebudete spravovat po nasazení.
- Nasazení už máte pomocí iWARP.
- Nejste si jistí, kterou možnost zvolit

#### <a name="rdma-over-converged-ethernet-roce"></a>RDMA přes sblíženou síť Ethernet (RoCE)

RoCE používá protokol UDP (User Datagram Protocol) a vyžaduje, aby se zajistila spolehlivost přemostění datového centra PFC a ETS.

Doporučujeme použít RoCE, pokud:

- V datovém centru už máte nasazení pomocí RoCE.
- Máte zkušenosti s požadavky vaší fyzické sítě

### <a name="guest-rdma"></a>Host RDMA

Host RDMA umožňuje úlohám SMB pro virtuální počítače získat stejné výhody použití RDMA na hostitelích.

**Použitelné typy provozu**: COMPUTE

**Vyžadované certifikace**: Premium

 Primární výhody použití hosta RDMA jsou:

- Snižování zátěže procesoru pro síťové rozhraní pro zpracování síťových přenosů
- Extrémně nízká latence
- Vysoká propustnost

Pokud potřebujete další informace, včetně toho, jak nasadit hosta RDMA, Stáhněte si dokument Word z [úložiště GitHub SDN](https://github.com/Microsoft/SDN/blob/master/Diagnostics/S2D%20WS2016_ConvergedNIC_Configuration.docx).

### <a name="switch-embedded-teaming-set"></a>Switch Embedded Teaming (SET)

Switch Embedded Teaming (SET) je softwarově založená technologie pro týmovou práci, která je zahrnutá v operačním systému Windows Server od Windows serveru 2016. SADA není závislá na typu používaných síťových adaptérů.

**Použitelné typy provozu**: výpočty, úložiště a Správa

**Vyžadované certifikace:** žádné (integrované v operačním systému)

SADA je jediná technologie seskupování podporovaná Azure Stackmi HCI. Vyrovnávání zatížení/převzetí služeb při selhání (LBFO) je jiná technologie seskupování běžně používaná se systémem Windows Server, ale není podporována Azure Stack HCL. Další informace o LBFO v Azure Stack HCI najdete v blogovém příspěvku [týmu v tématu Azure Stack HCL](https://techcommunity.microsoft.com/t5/networking-blog/teaming-in-azure-stack-hci/ba-p/1070642) . Tato sada funguje dobře s výpočetními, úložnými a řídicími přenosy dat.

NASTAVENÍ je důležité pro Azure Stack HCI, protože se jedná o jedinou technologii seskupování, která umožňuje:

- Seskupování adaptérů RDMA (v případě potřeby)
- Host RDMA
- Dynamické VMMQ
- Další funkce klíče Azure Stack HCI (viz [seskupování v Azure Stack HCI](https://techcommunity.microsoft.com/t5/networking-blog/teaming-in-azure-stack-hci/ba-p/1070642))

SADA poskytuje další funkce nad LBFO, včetně vylepšení kvality a výkonu. Aby to bylo možné, sada vyžaduje použití symetrických (stejných) adaptérů – seskupování asymetrických adaptérů není podporováno. Mezi symetrické síťové adaptéry patří:

- Značka (dodavatel)
- Model (verze)
- rychlost (propustnost)
- konfigurace

Nejjednodušší způsob, jak zjistit, jestli jsou adaptéry symetrické, je, pokud jsou tyto rychlosti stejné a popisy rozhraní se shodují. Můžou se odchýlit jenom v číslicích uvedených v popisu. Pomocí [`Get-NetAdapterAdvancedProperty`](/powershell/module/netadapter/get-netadapteradvancedproperty) rutiny ověřte, že nahlášená konfigurace obsahuje seznam stejných hodnot vlastností.

V následující tabulce najdete příklad popisů rozhraní, které se odchylují jenom od číslic (#):

|Název|Popis rozhraní|Rychlost propojení|
|----|----|----|
|NIC1|#1 síťového adaptéru|25 GB/s|
|NIC2|#2 síťového adaptéru|25 GB/s|
|NIC3|#3 síťového adaptéru|25 GB/s|
|NIC4|#4 síťového adaptéru|25 GB/s|

### <a name="rdma-traffic-considerations"></a>Požadavky RDMA na provoz

Pokud implementujete přemostění Datacenter (DCB), musíte zajistit, aby se konfigurace PFC a ETS správně implementovala napříč všemi síťovými porty, včetně síťových přepínačů. DCB se vyžaduje pro RoCE a volitelné pro iWARP.

Podrobné informace o tom, jak nasadit RDMA, najdete v dokumentu aplikace Word z [úložiště GitHub SDN](https://github.com/Microsoft/SDN/blob/master/Diagnostics/S2D%20WS2016_ConvergedNIC_Configuration.docx).

Implementace Azure Stack HCI založené na RoCE vyžadují konfiguraci tří tříd přenosů PFC, včetně výchozí třídy provozu v rámci prostředků infrastruktury a všech hostitelů:

#### <a name="cluster-traffic-class"></a>Třída přenosů clusteru

Tato třída provozu zajišťuje dostatečnou šířku pásma rezervovanou pro prezenční signály clusteru:

- Požadováno: Ano
- PFC povoleno: ne
- Doporučená priorita provozu: priorita 7
- Doporučená rezervace šířky pásma:
    - 10 GbE nebo nižší sítě RDMA = 2%
    - 25 GbE nebo vyšší sítě RDMA = 1%

#### <a name="rdma-traffic-class"></a>Třída přenosů RDMA

Tato třída provozu zajišťuje dostatečnou šířku pásma vyhrazenou pro komunikaci s bezeztrátovou RDA pomocí funkce SMB Direct:

- Požadováno: Ano
- PFC povoleno: Ano
- Doporučená priorita provozu: Priorita 3 nebo 4
- Doporučená rezervace šířky pásma: 50%

#### <a name="default-traffic-class"></a>Výchozí třída provozu

Tato třída provozu přenáší veškerý provoz, který není definovaný v clusteru nebo třídách provozu RDMA, včetně provozu virtuálních počítačů a provozu správy:

- Požadováno: ve výchozím nastavení (na hostiteli není nutná žádná konfigurace)
- Řízení toku (PFC) povoleno: ne
- Doporučená třída provozu: ve výchozím nastavení (Priorita 0)
- Doporučená rezervace šířky pásma: ve výchozím nastavení se nepožaduje konfigurace hostitele.

## <a name="storage-traffic-models"></a>Modely provozu úložiště

SMB poskytuje spoustu výhod jako protokol úložiště pro Azure Stack HCI včetně protokolu SMB vícekanálový. I když je protokol SMB vícekanálový pro toto téma mimo rozsah, je důležité pochopit, zda je přenos multiplexní všemi možnými propojeními, které může používat funkce SMB vícekanálový.

> [!NOTE]
>K oddělení provozu úložiště v Azure Stack HCI doporučujeme použít několik podsítí a sítí VLAN.

Vezměte v úvahu následující příklad clusteru se čtyřmi uzly. Každý server má dva porty úložiště (levou a pravou stranu). Vzhledem k tomu, že každý adaptér je ve stejné podsíti a síti VLAN, bude se v případě protokolu SMB ve všech dostupných odkazech šířit připojení. Proto port vlevo na prvním serveru (192.168.1.1) provede připojení k portu na levé straně na druhém serveru (192.168.1.2). Port na pravé straně na prvním serveru (192.168.1.12) se připojí k portu na druhém serveru na pravé straně. Podobná připojení jsou navázána na třetí a čtvrté servery.

Tím se ale vytvoří nepotřebná připojení a dojde k zahlcení mezičlánek (skupina agregace s více šasi nebo MC), která spojuje přepínače regálu (s označením XS). Podívejte se na následující diagram:

:::image type="content" source="media/plan-networking/four-node-cluster-1.png" alt-text="stejná podsíť clusteru se čtyřmi uzly" lightbox="media/plan-networking/four-node-cluster-1.png":::

Doporučený postup je použít pro každou sadu adaptérů samostatné podsítě a sítě VLAN. V následujícím diagramu teď porty na pravé straně používají podsíť virtuální počítače. x/24 a VLAN2. To umožňuje, aby přenosy na levé straně zůstaly v TOR1 a provoz na portech na pravé straně zůstal v TOR2. Podívejte se na následující diagram:

:::image type="content" source="media/plan-networking/four-node-cluster-2.png" alt-text="cluster se čtyřmi uzly různé podsítě" lightbox="media/plan-networking/four-node-cluster-2.png":::

## <a name="traffic-bandwidth-allocation"></a>Přidělení šířky pásma provozu

Následující tabulka ukazuje příklad přidělení šířky pásma různých typů provozu pomocí běžných rychlostí adaptéru v Azure Stack HCI. Všimněte si, že toto je příklad sblíženého řešení, ve kterém jsou všechny typy provozu (výpočty, úložiště a správa) spouštěny přes stejné fyzické adaptéry a jsou seskupeny pomocí SET.

Vzhledem k tomu, že tento případ použití představuje nejvíce omezení, představuje dobrý směrný plán. S ohledem na permutace pro počet adaptérů a rychlostí je však třeba vzít v úvahu příklad, nikoli požadavek na podporu.

Pro tento příklad jsou provedeny následující předpoklady:

- Existují dva adaptéry na tým.
- Provoz vrstvy úložiště (SBL), sdílený svazek clusteru (CSV) a technologie Hyper-V (Migrace za provozu):

    - Použít stejné fyzické adaptéry
    - Použití protokolu SMB
- Protokol SMB má přidělenou 50% šířku pásma pomocí přemostění datového centra.
    - SBL/CSV je přenos s nejvyšší prioritou a přijímá 70% rezervace šířky pásma protokolu SMB a:
    - Migrace za provozu (LM) je omezené pomocí `Set-SMBBandwidthLimit` rutiny a přijímá 29% zbývající šířky pásma.
        - Pokud je dostupná šířka pásma pro Migrace za provozu >= 5 GB/s a síťové adaptéry podporují, použijte RDMA. K tomu použijte následující rutinu:

            ```Powershell
            Set-VMHost VirtualMachineMigrationPerformanceOption SMB
            ```

        - Pokud je dostupná šířka pásma pro Migrace za provozu < 5 GB/s, použijte kompresi k omezení nedostupnosti časů. K tomu použijte následující rutinu:

            ```Powershell
            Set-VMHost -VirtualMachineMigrationPerformanceOption Compression
            ```

 - Pokud používáte RDMA s Migrace za provozu, ujistěte se, že Migrace za provozu provoz nemůže spotřebovat celou šířku pásma přidělenou třídě provozu RDMA pomocí limitu šířky pásma protokolu SMB. Buďte opatrní, protože tato rutina přebírá položku v bajtech za sekundu (b/s), zatímco síťové adaptéry jsou uvedené v bitech za sekundu (b/s). Pomocí následující rutiny nastavte limit šířky pásma 6 GB/s, například:

    ```Powershell
    Set-SMBBandwidthLimit -Category LiveMigration -BytesPerSecond 750MB
    ```

    > [!NOTE]
    >750 MB/s v tomto příkladu je rovno 6 GB/s.

Tady je ukázková tabulka přidělení šířky pásma:

|Rychlost síťové karty|Šířka pásma týmu|Rezervace šířky pásma protokolu SMB * *|SBL/CSV%|Šířka pásma SBL/CSV|Migrace za provozu%|Maximální šířka pásma Migrace za provozu|Signály|Šířka pásma prezenčního signálu|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|10 Gb/s|20 GB/s|10 Gb/s|70 %|7 GB/s|*|*|2 %|200 Mb/s|
|25 GB/s|50 GB/s|25 GB/s|70 %|17,5 GB/s|29|7,25 GB/s|1 %|250 MB/s|
|40 Gb/s|80 GB/s|40 Gb/s|70 %|28 GB/s|29|11,6 GB/s|1 %|400 MB/s|
|50 GB/s|100 Gb/s|50 GB/s|70 %|35 GB/s|29|14,5 GB/s|1 %|500 Mb/s|
|100 Gb/s|200 Gb/s|100 Gb/s|70 %|70 GB/s|29|29 GB/s|1 %|1 Gb/s|
|200 Gb/s|400 GB/s|200 Gb/s|70 %|140 GB/s|29|58 GB/s|1 %|2 Gb/s|

* – Použijte kompresi místo RDMA, protože přidělení šířky pásma pro Migrace za provozu provoz je <5 GB/s.

* *-50% je příkladem rezervace šířky pásma pro tento příklad.

## <a name="stretched-cluster-considerations"></a>Požadavky na roztažené clustery

Roztažené clustery poskytují zotavení po havárii, které pokrývá více datových center. V nejjednodušším tvaru síť s clustery HCI roztaženého Azure Stack vypadá takto:

:::image type="content" source="media/plan-networking/stretched-cluster.png" alt-text="Roztažený cluster" lightbox="media/plan-networking/stretched-cluster.png":::

Roztažené clustery mají následující požadavky a charakteristiky:

- RDMA je omezený jenom na jednu lokalitu a není podporovaný napříč různými lokalitami nebo podsítěmi.
- Servery ve stejné lokalitě se musí nacházet ve stejném stojanu a hranici vrstvy 2.
- Komunikace mezi lokalitami přes hranici vrstvy 3; roztažené topologie vrstvy 2 se nepodporují.

- Pokud lokalita používá RDMA pro své adaptéry úložiště, musí být tyto adaptéry v samostatné podsíti a síti VLAN, které nemůžou směrovat mezi lokalitami. Tím zabráníte tomu, aby replika úložiště používala RDMA napříč lokalitami.
- Adaptéry používané pro komunikaci mezi lokalitami:

    - Může být fyzický nebo virtuální (hostitelský vNIC). Pokud je virtuální, musíte zřídit jednu vNIC ve vlastní podsíti a síti VLAN na fyzickou síťovou kartu.
    - Musí být ve své vlastní podsíti a síti VLAN, které mohou směrovat mezi lokalitami.
    - RDMA musí být zakázán pomocí `Disable-NetAdapterRDMA` rutiny. Doporučujeme, abyste repliku úložiště výslovně využívali pro použití určitých rozhraní pomocí `Set-SRNetworkConstraint` rutiny.
    - Musí splňovat všechny další požadavky na repliku úložiště.
-   V případě převzetí služeb při selhání do jiné lokality je nutné zajistit, aby byla k dispozici dostatečná šířka pásma pro spuštění úloh v druhé lokalitě. Bezpečnou možností je zřídit lokality na 50% dostupné kapacity. Nejedná se o tvrdý požadavek, pokud můžete při převzetí služeb při selhání tolerovat nižší výkon.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o síťových přepínačích a požadavcích na fyzickou síť. Viz [požadavky na fyzickou síť](physical-network-requirements.md).
- Vyštětcte se základy clusteringu s podporou převzetí služeb při selhání. Informace najdete v tématu [Základy práce s clustery](https://techcommunity.microsoft.com/t5/failover-clustering/failover-clustering-networking-basics-and-fundamentals/ba-p/1706005?s=09)
- Zapnout štětce při použití SET Viz [přímý přístup do paměti vzdáleného počítače (RDMA) a Switch Embedded Teaming (set)](/windows-server/virtualization/hyper-v-virtual-switch/rdma-and-switch-embedded-teaming) .
- Informace k nasazení najdete v tématu [Vytvoření clusteru pomocí centra pro správu Windows](../deploy/create-cluster.md) .
- Informace k nasazení najdete v tématu [Vytvoření clusteru pomocí Windows PowerShellu](../deploy/create-cluster-powershell.md) .