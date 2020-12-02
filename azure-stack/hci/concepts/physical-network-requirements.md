---
title: Požadavky na fyzickou síť pro Azure Stack HCI
description: Seznamte se s požadavky na fyzickou síť pro Azure Stack HCI
author: v-dasis
ms.topic: how-to
ms.date: 11/30/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 7452437d7f760a688c2403cb4def735b2daaa105
ms.sourcegitcommit: a7a2ac1b9be926134826dce03e348154fd212bc9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96443293"
---
# <a name="physical-network-requirements-for-azure-stack-hci"></a>Požadavky na fyzickou síť pro Azure Stack HCI

> Platí pro Azure Stack HCI, verze 20H2

Toto téma popisuje fyzické (Fabric) požadavky na síť a požadavky pro Azure Stack HCI, zejména pro síťové přepínače.

> [!NOTE]
> Požadavky na budoucí Azure Stack verze HCI se mohou změnit.

## <a name="network-switches-for-azure-stack-hci"></a>Síťové přepínače pro Azure Stack HCI

Microsoft testuje Azure Stack HCL do standardů a protokolů, které jsou uvedené v části **požadavky na síťový přepínač** níže. Zatímco společnost Microsoft neosvědčuje síťové přepínače, spolupracuje s dodavateli s cílem identifikovat zařízení, která podporují Azure Stack požadavky na HCI.

Tyto požadavky jsou publikovány také v [zásadách a zásadách programu pro kompatibilitu hardwaru systému Windows](https://docs.microsoft.com/windows-hardware/design/compatibility/whcp-specifications-policies).  Vyberte možnost **Stáhnout specifikace a zásady, verze 1809**, otevřete soubor zip, otevřete **WHCP-Components-Peripherals-Specification-1809.pdf** a pak se podívejte na oddíl **Device. Network. Switch. SDDC** .

> [!IMPORTANT]
> I když další síťové přepínače využívající technologie a protokoly, které tady nejsou uvedené, můžou fungovat, Microsoft nemůže zaručit, že budou fungovat s Azure Stack HCI a nemusí být schopné pomáhat při řešení problémů, ke kterým dochází.

Při nákupu síťových přepínačů se obraťte na dodavatele přepínače a zajistěte, aby zařízení splňovala všechny Azure Stack požadavky na rozhraní HCI. Následující dodavatelé (v abecedním pořadí) potvrdili, že jejich přepínače podporují Azure Stack požadavky na rozhraní HCI:

|Dodavatel|10 GbE|25 GbE|100 GbE|
|-----|-----|-----|-----|
|Firm|[Řada S41xx](https://www.dell.com/learn/us/en/45/shared-content~data-sheets~en/documents~dell-emc-networking-s4100-series-spec-sheet.pdf)|[Řada S52xx](https://www.delltechnologies.com/resources/en-us/asset/data-sheets/products/networking/dell_emc_networking-s5200_on_spec_sheet.pdf)|[Řada S52xx](https://www.delltechnologies.com/resources/en-us/asset/data-sheets/products/networking/dell_emc_networking-s5200_on_spec_sheet.pdf)|
|Lenovo|[G8272](https://lenovopress.com/tips1267-lenovo-rackswitch-g8272), [NE1032](https://lenovopress.com/lp0605-thinksystem-ne1032-rackswitch)|[NE2572](https://lenovopress.com/lp0608-lenovo-thinksystem-ne2572-rackswitch) |[NE10032](https://lenovopress.com/lp0609-lenovo-thinksystem-ne10032-rackswitch) |

> [!IMPORTANT]
> Microsoft tento seznam aktualizuje, protože jsme informovali o změnách od dodavatelů síťových přepínačů.

Pokud váš přepínač není zahrnutý, obraťte se na dodavatele přepínače, aby se zajistilo, že model přepínače a přepínač verze operačního systému podporují požadavky v následující části.

## <a name="network-switch-requirements"></a>Požadavky na síťový přepínač

V této části jsou uvedené oborové standardy, které jsou povinné pro síťové přepínače používané ve všech Azure Stackch nasazeních HCI. Tyto standardy vám pomůžou zajistit spolehlivou komunikaci mezi uzly v Azure Stack nasazeních clusteru HCI.

> [!NOTE]
> Síťové adaptéry používané pro výpočetní prostředky, úložiště a provoz pro správu vyžadují Ethernet. Další informace najdete v tématu [požadavky na síť hostitele](host-network-requirements.md).

Tady jsou povinné standardy IEEE a specifikace IEEE:

### <a name="standard-ieee-8021q"></a>Standard: IEEE 802.1 Q

Přepínače sítě Ethernet musí splňovat specifikaci IEEE 802.1 Q, která definuje sítě VLAN. SÍTĚ VLAN jsou vyžadovány pro několik aspektů Azure Stack HCI a jsou požadovány ve všech scénářích.

### <a name="standard-ieee-8021qbb"></a>Standard: IEEE 802.1 QBB

Přepínače sítě Ethernet musí splňovat specifikaci IEEE 802.1 QBB, která definuje prioritní řízení toku (PFC). PFC se vyžaduje v případě, že se používá přemostění Datacenter (DCB). Vzhledem k tomu, že DCB se dá použít ve scénářích RoCE a iWARP RDMA, ve všech scénářích se vyžaduje 802.1 QBB. Bez downgrade možností přepínače nebo rychlosti portů se vyžadují minimálně tři priority třídy Service (CoS). Aspoň jedna z těchto tříd přenosu musí poskytovat bezztrátovou komunikaci.

### <a name="standard-ieee-8021qaz"></a>Standard: IEEE 802.1 QAZ

Přepínače sítě Ethernet musí splňovat specifikaci IEEE 802.1 Qaz, která definuje vylepšený výběr přenosu (ETS). ETS je vyžadován, kde se používá DCB. Vzhledem k tomu, že DCB se dá použít ve scénářích RoCE a iWARP RDMA, ve všech scénářích se vyžaduje 802.1 QAZ. Vyžaduje se minimálně tři prioritní priority bez downgradu možností přepnutí nebo rychlosti portu.

> [!NOTE]
> Infrastruktura sblížená pomocí technologie Hyper-v má vysokou závislost na East-West komunikaci vrstvy 2 v rámci stejného stojanu, a proto vyžaduje ETS. Společnost Microsoft netestuje Azure Stack HCL s použitím rozlišeného bodu kódování (DSCP). 

### <a name="standard-ieee-8021ab"></a>Standard: IEEE 802.1 AB

Přepínače sítě Ethernet musí splňovat specifikaci IEEE 802.1 AB definující Protokol LLDP (Link Layer Discovery Protocol). LLDP se vyžaduje pro Azure Stack HCI a podporuje seskupování pro zjišťování konfigurace přepínače.

Konfigurace hodnot typu LLDP-Length-Values (TLVs) musí být dynamicky povolena. Tyto přepínače nesmí vyžadovat další konfiguraci nad rámec povolení konkrétního TLV. Například povolení 802,1 podtyp 3 by mělo automaticky inzerovat všechny sítě VLAN, které jsou dostupné na portech přepínače.

### <a name="custom-tlv-requirements"></a>Vlastní požadavky TLV

LLDP umožňuje organizacím definovat a kódovat vlastní TLVs. Ty se nazývají organizace specifické pro TLVs. Všechny organizace specifické pro TLVs začínají s hodnotou typu LLDP TLV typu 127. Následující tabulka uvádí, které z organizačního vlastního typu TLV (TLV Type 127) jsou požadovány:

|Vyžaduje se verze|Organizace|Typ TLV|
|-----|-----|-----|-----|
|20H2 a novější|IEEE 802,1|Název sítě VLAN (podtyp = 3)|
|20H2 a novější|IEEE 802,3|Maximální velikost rámce (podtyp = 4)|

## <a name="network-traffic-and-architecture"></a>Síťový provoz a architektura

Tato část je převážně pro správce sítě.

Azure Stack HCI může fungovat v různých architekturách datového centra, včetně dvou vrstev (hřbet-list) a 3 vrstvy (Core-Aggregator-Access). V této části najdete další informace o konceptech z Spine-Leaf topologie, které se běžně používají s úlohami v infrastruktuře založené na technologii Hyper-v, jako je Azure Stack HCI.

## <a name="network-models"></a>Síťové modely

Síťový provoz může klasifikovat jeho směr. Tradiční prostředí sítě SAN (Storage Area Network) jsou silně North-South, kde přenos toků z výpočetní vrstvy do vrstvy úložiště napříč hranicí vrstvy 3 (IP). Probíhající infrastruktura je složitější East-West, kde v rámci hranice vrstvy 2 (VLAN) zůstane značná část provozu.

> [!IMPORTANT]
>Důrazně doporučujeme, aby všechny uzly clusteru v lokalitě byly fyzicky umístěné ve stejném racku a připojily se ke stejným přepínačům rozhraní příkazového stojanu.

### <a name="north-south-traffic-for-azure-stack-hci"></a>North-South provoz pro Azure Stack HCI

North-South provoz má následující vlastnosti:

- Provoz se přesměruje z přepnutí do páteře nebo z hřbetu na přepínač pro mandát.
- Provoz opouští fyzický Stojan nebo překračuje hranici vrstvy 3 (IP).
- Zahrnuje správu (PowerShell, centrum pro správu Windows), výpočetní výkon (virtuální počítač) a provoz roztaženého clusteru mezi lokalitami.
- Používá pro připojení k fyzické síti přepínač sítě Ethernet

### <a name="east-west-traffic-for-azure-stack-hci"></a>East-West provoz pro Azure Stack HCI

East-West provoz má následující vlastnosti:

- Provoz zůstává v rámci přepínačů mandátu a hranice vrstvy 2 (VLAN).
- Zahrnuje provoz úložiště nebo Migrace za provozu provozu mezi uzly ve stejném clusteru a (Pokud používáte roztažené clustery) v rámci stejné lokality.
- Může používat přepínač sítě Ethernet (přepnutý) nebo přímé (bez přepínání), jak je popsáno v následujících dvou částech.

## <a name="using-switches"></a>Používání přepínačů

North-South provoz vyžaduje použití přepínačů. Kromě použití přepínače sítě Ethernet, který podporuje požadované protokoly pro Azure Stack HCI, je nejdůležitějším aspektem správné určení velikosti prostředků infrastruktury sítě.

Je důležité pochopit "neblokované" šířku pásma prostředků infrastruktury, kterou můžou vaše přepínače sítě Ethernet podporovat a které minimalizujete (nebo pokud možno eliminovat) přeplacení sítě.

Společné body zahlcení a přebytek, jako je například [Skupina agregace více skříní](https://en.wikipedia.org/wiki/Multi-chassis_link_aggregation_group) , která se používá pro redundanci cest, lze odstranit pomocí správného použití podsítí a sítí VLAN. Viz také [požadavky na síť hostitele](host-network-requirements.md).

Spolupracujte s dodavatelem sítě nebo týmem podpory sítě, abyste zajistili správnou velikost síťových přepínačů pro úlohy, které chcete spouštět.

## <a name="using-switchless"></a>Použití bez přepínání

Azure Stack HCI podporuje bezplatných připojení pro East-West provoz u všech velikostí clusterů, pokud každý uzel v clusteru má redundantní připojení ke všem uzlům v clusteru. Toto se nazývá připojení "s úplnými oky".

> [!NOTE]
>Výhody nasazení bez přepínání se v důsledku počtu požadovaných síťových adaptérů zmenšují s clustery, které jsou větší než tři uzly.

### <a name="advantages-of-switchless-connections"></a>Výhody připojení bez přepínání

- Pro East-West provoz není nutný žádný přepínač pro zakoupení. Pro North-South provoz je vyžadován přepínač. To může vést ke snížení nákladů na nižší kapitálové výdaje (CAPEX), ale závisí na počtu uzlů v clusteru.
- Vzhledem k tomu, že není k dispozici žádný přepínač, konfigurace je omezena na hostitele, což může snížit možný počet potřebných kroků konfigurace. Tato hodnota se zmenšuje, když se zvětší velikost clusteru.

### <a name="disadvantages-of-switchless-connections"></a>Nevýhody nepřepínačů připojení

- Jak roste počet uzlů v clusteru, můžou náklady na síťové adaptéry překročit náklady na používání síťových přepínačů.
- Pro schémata IP a adresování podsítí se vyžaduje další plánování.
- Poskytuje jenom přístup k místnímu úložišti. V případě provozu virtuálního počítače, provozu správy a dalšího provozu, který vyžaduje North-South přístup, nelze použít tyto adaptéry.
- Obecně se neškáluje stejně jako clustery se třemi uzly.

## <a name="next-steps"></a>Další kroky

- Seznamte se s požadavky na síťový adaptér a hostitele. Viz [požadavky na síť hostitele](host-network-requirements.md).
- Vyštětcte se základy clusteringu s podporou převzetí služeb při selhání. Seznamte se se [Základy sítí pro převzetí služeb při selhání](https://techcommunity.microsoft.com/t5/failover-clustering/.failover-clustering-networking-basics-and-fundamentals/ba-p/1706005?s=09).
- Zapnout štětce při použití SET Podívejte [se na téma přímý přístup do paměti vzdáleného počítače (RDMA) a Switch Embedded Teaming (set)](https://docs.microsoft.com/windows-server/virtualization/.hyper-v-virtual-switch/rdma-and-switch-embedded-teaming).
- Nasazení najdete v tématu [Vytvoření clusteru pomocí centra pro správu systému Windows](https://docs.microsoft.com/azure-stack/hci/deploy/create-cluster).
- Informace k nasazení najdete v tématu [Vytvoření clusteru pomocí Windows PowerShellu](https://docs.microsoft.com/azure-stack/hci/deploy/create-cluster-powershell).