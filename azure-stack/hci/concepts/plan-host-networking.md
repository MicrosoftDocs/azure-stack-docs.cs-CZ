---
title: Plánování sítě hostitele pro Azure Stack HCI
description: Naučte se plánovat sítě hostitele pro Azure Stack clustery HCI
author: v-dasis
ms.topic: how-to
ms.date: 11/06/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: e9a03fa7518c6a450204cdbdb40483b593b1867b
ms.sourcegitcommit: ce864e1d86ad05a03fe896721dea8f0cce92085f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/09/2020
ms.locfileid: "94383471"
---
# <a name="plan-host-networking-for-azure-stack-hci"></a>Plánování sítě hostitele pro Azure Stack HCI

> Platí pro Azure Stack HCI, verze 20H2

Toto téma popisuje požadavky a požadavky na plánování sítě hostitele v prostředích clusteru bez roztažení a roztažení Azure Stack HCI.

## <a name="traffic-types-supported"></a>Podporované typy provozu

Azure Stack HCI používá protokol SMB (Server Message Block). SMB on Azure Stack HCI podporuje následující typy přenosů:

- Vrstva sběrnice úložiště (SBL) – používá Prostory úložiště s přímým přístupem; provoz s nejvyšší prioritou
- Sdílené svazky clusteru
- Migrace za provozu (LM)
- Replika úložiště (SR) – používá se v roztaženém clusteru
- Sdílené složky souborů (FS) – tradiční a Scale-Out souborový server služby FS (SOFS)
- Prezenční signál clusteru (s)
- Komunikace s clustery (spojení uzlů, aktualizace clusteru, aktualizace registru)

Provoz SMB může přenášet do těchto protokolů:

- Protokol TCP (Transport Control Protocol) – používá se mezi lokalitami
- Přímý přístup do paměti vzdáleného počítače (RDMA)

## <a name="traffic-bandwidth-allocation"></a>Přidělení šířky pásma provozu

Následující tabulka uvádí přidělení šířky pásma pro různé typy provozu, kde:

- Všechny jednotky jsou v GB/s.
- Hodnoty platí jak pro roztažené, tak i bez roztažené clustery.
- Provoz SMB získá 50% celkového přidělení šířky pásma.
- Provoz sběrnice nebo sdílený svazek clusteru (SBL/CSV) sběrnice úložiště získá 70% zbývající 50% přidělení
- Přenos Migrace za provozu (LM) dostane 15% zbývajícího přidělení 50%.
- Provoz repliky úložiště (SR) získá 14% zbývajícího přidělení 50%.
- Přenos prezenčního signálu (nezatíženého signálu) získá 1% zbývajícího přidělení 50%.
- * = by měl používat kompresi místo RDMA, pokud je přidělení šířky pásma pro přenosy LM <5 GB/s

|Rychlost síťové karty|Šířka pásma týmu|Rezervace SMB 50%|SBL/CSV%|Šířka pásma SBL/CSV|HASH|Šířka pásma LM|UVEDENO |Šířka pásma pro rozpoznávání řeči|Nejenom%|Šířka pásma|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|10|20|10|70 %|7|14% *|1,4 *|14 %|1.4|2 %|0,2|
|25|50|25|70 %|17,5|15% *|3,75 *|14 %|3,5|1 %|0,25|
|40|80| 40|70 %|28|15 %|6|14 %|5,6|1 %|0,4|
|50|100|50|70 %|35|15 %|7,5|14 %|7|1 %|0,5|
|100|200|100|70 %|70|15 %|15|14 %|14|1 %|1|
|200|400|200|70 %|140|15 %|30|14 %|28|1 %|2|

## <a name="rdma-considerations"></a>Požadavky RDMA

Přímý přístup do paměti vzdáleného počítače (RDMA) je přímý přístup do paměti z paměti jednoho počítače do jiného bez nutnosti použít operační systém počítače. To umožňuje sítě s vysokou propustností a nízkou latencí při minimalizaci využití procesoru, což je zvláště užitečné v clusterech.

Veškerý provoz RDMA hostitele využívá funkci SMB Direct. SMB Direct je přenos SMB 3,0 odeslaný přes RDMA a je multiplexější prostřednictvím portu 445. Aby provoz RDMA zůstal kompatibilní se většinou současných a budoucích fyzických přepínačů na trhu, musí se použít minimálně dvě PFC (TCs) s povoleným škálováním na základě priority.

Protokol iWARP (Internet WAN Area RDMA Protocol) spouští RDMA přes protokol TCP, zatímco RDMA přes sblíženou síť Ethernet (RoCE) zabraňuje použití TCP, ale vyžaduje jak síťové adaptéry, tak fyzické přepínače, které ji podporují. Sblížené síťové požadavky pro RDMA přes RoCE najdete v [Průvodci nasazením Windows serveru 2016 a 2019 RDMA](https://github.com/Microsoft/SDN/blob/master/Diagnostics/S2D%20WS2016_ConvergedNIC_Configuration.docx).

Služba RDMA je ve výchozím nastavení povolená pro všechny přenosy na východ a západ mezi uzly clusteru v lokalitě ve stejné podsíti. RDMA je zakázané a nepodporuje se u Severově roztaženého provozu clusteru mezi lokalitami v různých podsítích.

Požadavky na RDMA pro Azure Stack HCI:

- Veškerý provoz mezi podsítěmi a mezi lokalitami (roztažené clustery) musí používat rozhraní WinSock TCP. Zprostředkující směrování sítě je mimo zobrazení a kontrolu nad Azure Stack HCI.
- RDMA mezi podsítěmi a mezi lokalitami (roztažené clustery) se nepodporuje. Použití odchozích připojení a více síťových zařízení znamená více bodů selhání, kde se může stát nestabilní a nepodporují se.
- Pro provoz repliky úložiště pro roztažené clustery nejsou potřeba žádné další virtuální síťové karty. Pro účely řešení potíží ale může být užitečné, aby přenosy mezi lokalitami a různými podsítěmi byly oddělené od provozu v oblasti východ až na síť RDMA. Pokud funkce SMB Direct nemůže být nativně vypnutá mezi lokalitami a mezi jednotlivými toky, pak:
    - Pro repliku úložiště by se měl zřídit minimálně jeden další virtuální síťové adaptéry.
    - Virtuální síťové adaptéry repliky úložiště musí mít zakázaný přístup RDMA pomocí rutiny [disabled-NetAdapterRDMA](https://docs.microsoft.com/powershell/module/netadapter/disable-netadapterrdma) prostředí PowerShell, protože se jedná o definiční nasazení mezi lokalitami a mezi podsítěmi.
    - Nativní adaptéry RDMA by vyžadovaly virtuální přepínač a virtuální síťové adaptéry pro podporu repliky úložiště, aby splňovaly výše uvedené požadavky na lokalitu a podsíť.
    - Požadavky na šířku pásma RDMA mezi lokalitami vyžadují znalost procentuálních hodnot šířky pásma na typ provozu, jak je popsáno v části **přidělení šířky pásma přenosu** . Tím se zajistí, že se pro provoz v oblasti Východ a západ (mezi uzly) můžou použít vhodné rezervace a omezení šířky pásma.
- Provoz repliky úložiště Migrace za provozu a úložiště musí být omezený na šířku pásma SMB, jinak by mohl spotřebovat veškerou šířku pásma a omezují provoz úložiště s vysokou prioritou. Další informace najdete v tématu rutiny PowerShellu [set-SmbBandwidthLimit](https://docs.microsoft.com/powershell/module/smbshare/set-smbbandwidthlimit) a [set-SRNetworkConstraint](https://docs.microsoft.com/powershell/module/storagereplica/set-srnetworkconstraint) .

> [!NOTE]
> Při použití rutiny je potřeba převést bity na bajty `Set-SmbBandwidthLimit` .

## <a name="node-interconnect-requirements"></a>Požadavky propojení uzlů

Tato část popisuje specifické požadavky sítě mezi uzly serveru v lokalitě, která se nazývá propojení. Je možné použít přepínací uzel nebo vzájemně propojené uzly, které jsou podporované:

- **Přepnuto:** Uzly serveru se nejčastěji připojují přes sítě Ethernet, které používají síťové přepínače. Přepínače musí být správně nakonfigurovány pro zpracování šířky pásma a typu sítě. Pokud používáte RDMA, který implementuje protokol RoCE, je důležitá konfigurace síťového zařízení a přepínače.
- Bez **přepínání:** Uzly serveru je taky možné propojit pomocí přímých připojení Ethernet bez přepínače. V takovém případě musí mít každý uzel serveru přímé spojení s každým jiným uzlem clusteru ve stejné lokalitě.

### <a name="interconnects-for-2-3-node-clusters"></a>Propojení clusterů uzlů 2-3

Jedná se o *minimální* požadavky propojení pro clustery s jedním webem, které mají dva nebo tři uzly. Platí pro každý uzel serveru:

- Jedna nebo víc síťových adaptérů s 1 GB, které se mají použít pro funkce správy
- Jedna nebo více 10 GB (nebo rychlejších) síťových adaptérů pro provoz úložiště a úloh
- Pro zajištění redundance a výkonu se doporučuje dvě nebo víc síťových připojení mezi jednotlivými uzly.

### <a name="interconnects-for-4-node-and-greater-clusters"></a>Propojení pro clustery se čtyřmi a více uzly

Toto jsou *minimální* požadavky na propojení pro clustery se čtyřmi nebo více uzly a pro vysoce výkonné clustery. Platí pro každý uzel serveru:

- Jedna nebo víc síťových adaptérů s 1 GB, které se mají použít pro funkce správy
- Jedna nebo více 25 GB (nebo rychlejší) síťových adaptérů pro provoz úložiště a úloh. Pro zajištění redundance a výkonu doporučujeme dvě nebo víc síťových připojení.
- Síťové karty, které podporují přímý přístup do paměti vzdáleného počítače (RDMA): iWARP (doporučeno) nebo RoCE.

### <a name="site-to-site-requirements-stretched-cluster"></a>Požadavky na lokalitu (roztažené clustery)

Při připojování mezi lokalitami pro roztažené clustery se stále používají požadavky na propojení v rámci každé lokality a mají další repliky úložiště a požadavky migrace Hyper-V za provozu, které je potřeba vzít v úvahu:

- K synchronní replikaci mají minimálně 1 1 GB RDMA nebo Ethernet/TCP připojení mezi lokalitami. Upřednostňuje se 25 GB připojení.
- Síť mezi lokalitami s dostatečnou šířkou pásma, která bude obsahovat vaše vstupně-výstupní úlohy zápisu, a průměrnou latenci 5ms odezvy nebo nižší pro synchronní replikaci. Asynchronní replikace nemá doporučení pro latenci.
- Pokud používáte jedno připojení mezi lokalitami, nastavte omezení šířky pásma protokolu SMB pro repliku úložiště pomocí PowerShellu. Další informace najdete v tématu [set-SmbBandwidthLimit](/powershell/module/smbshare/set-smbbandwidthlimit).
- Pokud používáte více připojení mezi lokalitami, oddělte provoz mezi připojeními. Například vložte provoz repliky úložiště do samostatné sítě, než je migrace Hyper-V za provozu pomocí PowerShellu. Další informace najdete v tématu [set-SRNetworkConstraint](/powershell/module/storagereplica/set-srnetworkconstraint).

## <a name="network-port-requirements"></a>Požadavky na síťový port

Ujistěte se, že jsou mezi všemi uzly serveru v lokalitě i mezi lokalitami (pro roztažené clustery) otevřené správné síťové porty. Budete potřebovat patřičná pravidla brány firewall a směrovačů, aby bylo možné v obousměrném přenosu přes protokol ICMP, SMB (port 445 a port 5445 pro SMB Direct) a WS-MAN (port 5985) mezi všemi servery v clusteru.

Při použití Průvodce vytvořením clusteru v centru pro správu systému Windows k vytvoření clusteru se v průvodci automaticky otevře příslušné porty brány firewall na každém serveru v clusteru pro clustering s podporou převzetí služeb při selhání, Hyper-V a repliku úložiště. Pokud na každém serveru používáte jinou bránu firewall, otevřete následující porty:

### <a name="failover-clustering-ports"></a>Porty clusteringu s podporou převzetí služeb při selhání

- ICMPv4 a ICMPv6
- Port TCP 445
- Dynamické porty RPC
- Port TCP 135
- Port TCP 137
- Port TCP 3343
- Port UDP 3343

### <a name="hyper-v-ports"></a>Porty Hyper-V

- Port TCP 135
- Port TCP 80 (připojení HTTP)
- Port TCP 443 (připojení HTTPS)
- Port TCP 6600
- Port TCP 2179
- Dynamické porty RPC
- Mapovač koncových bodů Endpoint Mapper RPC
- Port TCP 445

### <a name="storage-replica-ports-stretched-cluster"></a>Porty repliky úložiště (roztažené clustery)

- Port TCP 445
- TCP 5445 (při použití iWarp RDMA)
- Port TCP 5985
- ICMPv4 a ICMPv6 (při použití `Test-SRTopology` rutiny prostředí PowerShell)

Můžou se vyžadovat další porty, které nejsou uvedené výše. Toto jsou porty pro základní funkce rozhraní Azure Stack HCI.

## <a name="network-switch-requirements"></a>Požadavky na síťový přepínač

V této části jsou definovány požadavky na fyzické přepínače používané při Azure Stack HCI. Tyto požadavky uvádějí oborové specifikace, organizační standardy a protokoly, které jsou povinné pro všechna Azure Stack nasazení rozhraní HCI. Není-li uvedeno jinak, je vyžadována nejnovější aktivní (nenahrazená) verze Standard.

Tyto požadavky vám pomůžou zajistit spolehlivou komunikaci mezi uzly v Azure Stack nasazeních clusteru HCI. Spolehlivá komunikace mezi uzly je kritická. Aby byla zajištěna potřebná úroveň spolehlivosti pro Azure Stack HCI, je nutné, aby byly přepínače:

- Dodržování příslušných specifikací, standardů a protokolů v oboru
- Poskytněte přehled o tom, které specifikace, standardy a protokoly podporuje přepínač.
- Zadejte informace o tom, které funkce jsou povolené.

Ujistěte se, že požádáte dodavatele přepínače, pokud váš přepínač podporuje následující:

#### <a name="standard-ieee-8021q"></a>Standard: IEEE 802.1 Q

Přepínače sítě Ethernet musí splňovat specifikaci IEEE 802.1 Q, která definuje sítě VLAN. SÍTĚ VLAN jsou vyžadovány pro několik aspektů Azure Stack HCI a jsou požadovány ve všech scénářích.

#### <a name="standard-ieee-8021qbb"></a>Standard: IEEE 802.1 QBB

Přepínače sítě Ethernet musí splňovat specifikaci IEEE 802.1 QBB, která definuje prioritní řízení toku (PFC). PFC se vyžaduje v případě, že se používá přemostění Datacenter (DCB). Vzhledem k tomu, že DCB se dá použít ve scénářích RoCE a iWARP RDMA, ve všech scénářích se vyžaduje 802.1 QBB. Bez downgradů možností přepínače nebo rychlosti portů se vyžadují minimálně tři priority třídy Service (CoS).

#### <a name="standard-ieee-8021qaz"></a>Standard: IEEE 802.1 QAZ

Přepínače sítě Ethernet musí splňovat specifikaci IEEE 802.1 Qaz, která definuje vylepšený výběr přenosu (ETS). ETS je vyžadován, kde se používá DCB. Vzhledem k tomu, že DCB se dá použít ve scénářích RoCE a iWARP RDMA, ve všech scénářích se vyžaduje 802.1 QAZ. Vyžaduje se minimálně tři prioritní priority bez downgradu možností přepnutí nebo rychlosti portu.

#### <a name="standard-ieee-8021ab"></a>Standard: IEEE 802.1 AB

Přepínače sítě Ethernet musí splňovat specifikaci IEEE 802.1 AB definující Protokol LLDP (Link Layer Discovery Protocol). LLDP se vyžaduje pro zjišťování konfigurace přepínače v systému Windows. Konfigurace hodnot typu LLDP-Length-Values (TLVs) musí být dynamicky povolena. Tyto přepínače nesmí vyžadovat další konfiguraci.

Například povolení 802,1 podtyp 3 by mělo automaticky inzerovat všechny sítě VLAN, které jsou dostupné na portech přepínače.

#### <a name="tlv-requirements"></a>Požadavky TLV

LLDP umožňuje organizacím definovat a kódovat vlastní TLVs. Ty se nazývají organizace specifické pro TLVs. Všechny organizace specifické pro TLVs začínají s hodnotou typu LLDP TLV typu 127. Následující tabulka uvádí, které z organizačních specifických typů TLV (TLV Type 127) jsou povinné a které jsou volitelné:

|Stav|Organizace|Typ TLV|
|-|-|-|
|Vyžadováno|IEEE 802,1|Název sítě VLAN (podtyp = 3)|
|Vyžadováno|IEEE 802,3|Maximální velikost rámce (podtyp = 4)|
|Volitelné|IEEE 802,1|ID VLAN portu (podtyp = 1)|
|Volitelné|IEEE 802,1|IDENTIFIKÁTOR sítě VLAN portu a protokolu (podtyp = 2)|
|Volitelné|IEEE 802,1|Agregace propojení (podtyp = 7)|
|Volitelné|IEEE 802,1|Oznámení o zahlcení (podtyp = 8)|
|Volitelné|IEEE 802,1|Konfigurace ETS (podtyp = 9)|
|Volitelné|IEEE 802,1|Doporučení ETS (podtyp = A)|
|Volitelné|IEEE 802,1|Konfigurace PFC (podtyp = B)|
|Volitelné|IEEE 802,1|EVB (podtyp = D)|
|Volitelné|IEEE 802,3|Agregace propojení (podtyp = 3)|

> [!NOTE]
> Některé z uvedených volitelných funkcí můžou být v budoucnu nutné.

## <a name="example-cluster-network-design"></a>Příklad návrhu sítě s clustery

Následující diagram znázorňuje standardní konfiguraci (bez roztaženého) clusteru se dvěma clustery ve stejné podsíti a stejné lokalitě. Uzly serveru spolu komunikují ve stejném clusteru pomocí redundantních síťových adaptérů připojených k duálním přepínačům rozhraní příkazového stojanu. Komunikace mezi clustery prochází přes duální síťová hřbetová zařízení.

:::image type="content" source="media/plan-host-networking/rack-topology-non-stretched-cluster.png" alt-text="Cluster bez roztažení" lightbox="media/plan-host-networking/rack-topology-non-stretched-cluster.png":::

## <a name="example-stretched-cluster-network-design"></a>Ukázkový návrh roztažené sítě clusteru

Následující diagramy znázorňují roztaženou konfiguraci clusteru s jedním clusterem se serverovými uzly umístěnými v různých lokalitách a podsítích (čtyři uzly na lokalitu). Uzly serveru spolu navzájem komunikují ve stejném clusteru pomocí redundantních síťových adaptérů připojených k přepínačům pro připojení s duálním připojením. Komunikace mezi lokalitami prochází přes duální směrovače pomocí repliky úložiště pro převzetí služeb při selhání.

:::image type="content" source="media/plan-host-networking/rack-topology-stretched-cluster.png" alt-text="Roztažený cluster" lightbox="media/plan-host-networking/rack-topology-stretched-cluster.png":::

### <a name="stretched-cluster-node-networking-option-1"></a>Síť roztaženého uzlu clusteru – možnost sítě 1

Následující diagram znázorňuje roztažené clustery, které používají přepínač s vloženým seskupením (nastaveno) k řízení toku, Migrace za provozu a provoz repliky úložiště mezi lokalitami na stejném vNIC. Použijte rutiny prostředí PowerShell [set-SmbBandwidthLimit](https://docs.microsoft.com/powershell/module/smbshare/set-smbbandwidthlimit) a [set-SRNetworkConstraint](https://docs.microsoft.com/powershell/module/storagereplica/set-srnetworkconstraint) k omezení šířky pásma migrace za provozu a provozu repliky úložiště. 

Pamatujte, že protokol TCP se používá pro přenosy mezi lokalitami, zatímco RDMA se používá pro provoz úložiště v rámci lokality Migrace za provozu.

:::image type="content" source="media/plan-host-networking/stretched-cluster-option-1.png" alt-text="Síť roztaženého uzlu clusteru – možnost sítě 1" lightbox="media/plan-host-networking/stretched-cluster-option-1.png":::

### <a name="stretched-cluster-node-networking-option-2"></a>Síť roztaženého uzlu clusteru – možnost sítě 2

Následující diagram znázorňuje pokročilejší konfiguraci roztaženého clusteru, který používá [vícekanálový protokol SMB](https://docs.microsoft.com/azure-stack/hci/manage/manage-smb-multichannel) pro přenos replik úložiště mezi lokalitami a vyhrazený adaptér pro provoz správy clusteru. Použijte rutiny prostředí PowerShell [set-SmbBandwidthLimit](https://docs.microsoft.com/powershell/module/smbshare/set-smbbandwidthlimit) a [set-SRNetworkConstraint](https://docs.microsoft.com/powershell/module/storagereplica/set-srnetworkconstraint) k omezení šířky pásma migrace za provozu a provozu repliky úložiště.

Pamatujte, že protokol TCP se používá pro přenosy mezi lokalitami, zatímco RDMA se používá pro provoz úložiště v rámci lokality.

:::image type="content" source="media/plan-host-networking/stretched-cluster-option-2.png" alt-text="Síť roztaženého uzlu clusteru – možnost sítě 2" lightbox="media/plan-host-networking/stretched-cluster-option-2.png":::


## <a name="next-steps"></a>Další kroky

- Vyštětcte se základy clusteringu s podporou převzetí služeb při selhání. Informace najdete v tématu [Základy práce s clustery](https://techcommunity.microsoft.com/t5/failover-clustering/failover-clustering-networking-basics-and-fundamentals/ba-p/1706005?s=09)
- Zapnout štětce při použití SET Viz [přímý přístup do paměti vzdáleného počítače (RDMA) a Switch Embedded Teaming (set)](https://docs.microsoft.com/windows-server/virtualization/hyper-v-virtual-switch/rdma-and-switch-embedded-teaming) .
- Informace k nasazení najdete v tématu [Vytvoření clusteru pomocí centra pro správu Windows](https://docs.microsoft.com/azure-stack/hci/deploy/create-cluster) .
- Informace k nasazení najdete v tématu [Vytvoření clusteru pomocí Windows PowerShellu](https://docs.microsoft.com/azure-stack/hci/deploy/create-cluster-powershell) .