---
title: Plánování infrastruktury softwarově definované sítě
description: Toto téma poskytuje informace o tom, jak naplánovat nasazení infrastruktury pro softwarově definovanou síť (SDN).
manager: grcusanz
ms.topic: conceptual
ms.assetid: ea7e53c8-11ec-410b-b287-897c7aaafb13
ms.author: anpaul
author: AnirbanPaul
ms.date: 09/11/2020
ms.openlocfilehash: 7d7eeaec5f82e08cf33a307f429389f03e712987
ms.sourcegitcommit: a845ae0d3794b5d845b2ae712baa7e38f3011a7b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2020
ms.locfileid: "90045522"
---
# <a name="plan-a-software-defined-network-infrastructure"></a>Plánování infrastruktury softwarově definované sítě

>Platí pro: Azure Stack HCI, verze 20H2; Windows Server 2019, Windows Server (půlroční kanál), Windows Server 2016

Seznamte se s plánováním nasazení infrastruktury softwarově definované sítě (SDN), včetně požadavků na hardware a software. Toto téma zahrnuje požadavky na plánování pro fyzickou a logickou konfiguraci sítě, směrování, brány, síťový hardware a další. Zahrnuje také požadavky na rozšíření infrastruktury SDN a používání postupného nasazení.

## <a name="prerequisites"></a>Požadavky
Pro infrastrukturu SDN je k dispozici několik hardwarových a softwarových požadavků, včetně:
- **Skupiny zabezpečení a registrace dynamického serveru DNS**. Vaše datacentrum musíte připravit pro nasazení síťového adaptéru, což vyžaduje sadu virtuálních počítačů. Než budete moct nasadit síťový adaptér, musíte nakonfigurovat skupiny zabezpečení a dynamickou registraci DNS.

    Další informace o nasazení síťového adaptéru pro vaše datové centrum najdete v tématu [požadavky pro nasazení síťového adaptéru](/windows-server/networking/sdn/plan/installation-and-preparation-requirements-for-deploying-network-controller).

- **Fyzická síť**. K nakonfigurování virtuálních místních sítí (VLAN), směrování a Border Gateway Protocol (BGP) potřebujete přístup k fyzickým síťovým zařízením. Toto téma poskytuje pokyny pro konfiguraci ručního přepínání a také možnosti pro použití partnerského vztahu protokolu BGP u přepínačů a směrovačů vrstvy 3 nebo virtuálního počítače serveru Směrování a vzdálený přístup (RRAS).

- **Fyzické hostitele COMPUTE**. Tito hostitelé používají technologii Hyper-V a jsou vyžadovány pro hostování infrastruktury SDN a virtuálních počítačů klientů. Konkrétní síťový hardware je na těchto hostitelích vyžadován pro dosažení co nejlepších výsledků, jak je popsáno v části [síťový hardware](#network-hardware) .

## <a name="physical-and-logical-network-configuration"></a>Konfigurace fyzické a logické sítě
Každý fyzický výpočetní hostitel vyžaduje připojení k síti přes jeden nebo více síťových adaptérů připojených k portu fyzického přepínače. [Síť VLAN](https://en.wikipedia.org/wiki/Virtual_LAN) úrovně 2 podporuje sítě rozdělené do několika segmentů logické sítě.

>[!TIP]
>Pro logické sítě v buď režimu přístupu, nebo bez příznaku použijte VLAN 0.

>[!IMPORTANT]
>Softwarově definované sítě Windows Server 2016 podporují adresování IPv4 pro Underlay a překrytí. Protokol IPv6 není podporován. Windows Server 2019 podporuje adresování IPv4 i IPv6.

### <a name="logical-networks"></a>Logical networks
Tato část popisuje požadavky na plánování infrastruktury SDN pro logickou síť zprostředkovatele pro logickou síť správy a virtualizaci sítě Hyper-V (HNV). Obsahuje podrobné informace o zřizování dalších logických sítí pro používání bran a Load Balancer softwaru (SLB) a ukázkové síťové topologie.

#### <a name="management-and-hnv-provider"></a>Správa a poskytovatel HNV
Všichni fyzický výpočetní hostitelé musí mít přístup k logické síti pro správu a logické síti poskytovatele HNV. Pro účely plánování IP adres musí mít každý fyzický výpočetní hostitel alespoň jednu IP adresu přiřazenou z logické sítě pro správu. Síťový adaptér vyžaduje vyhrazenou IP adresu z této sítě, aby sloužila jako IP adresa pro překládání stavu (REST).

Síť poskytovatele HNV slouží jako podkladová fyzická síť pro přenos klientů v oblasti východ/západ (interní interní), sever – jih (externí interní) provoz klienta a informace o partnerském vztahu protokolu BGP s fyzickou sítí.

Server DHCP může automaticky přiřadit IP adresy pro síť pro správu, nebo můžete přiřadit statické IP adresy ručně. Zásobník SDN automaticky přiřazuje IP adresy pro logickou síť poskytovatele HNV pro jednotlivé hostitele Hyper-V z fondu IP adres. Síťový adaptér určuje a spravuje fond IP adres.

>[!NOTE]
>Síťový adaptér přiřadí IP adresu poskytovatele HNV fyzickému hostiteli COMPUTE až poté, co agent hostitele síťového adaptéru obdrží zásady sítě pro konkrétní virtuální počítač tenanta.

| Pokud...                                                    | Pak...                                               |
| :------------------------------------------------------- | :---------------------------------------------------- |
| Logické sítě používají sítě VLAN,                          | fyzický výpočetní hostitel se musí připojit k portu přepínacího přepínače, který má přístup k sítím VLAN. Je důležité si uvědomit, že fyzické síťové adaptéry na hostiteli počítače nesmí mít aktivované žádné filtrování sítě VLAN.|
| Používáte seskupování se zapnutými a integrovanými členy (SET) a máte několik členů týmu síťového rozhraní (NIC), jako jsou například síťové adaptéry.| všechny členy týmu síťových adaptérů pro tohoto konkrétního hostitele musíte připojit ke stejné doméně všesměrového vysílání vrstvy 2.|
| Fyzický výpočetní hostitel používá další virtuální počítače infrastruktury, například síťový adaptér, SLB/multiplexor (MUX) nebo bránu. | Ujistěte se, že logická síť pro správu má pro každý hostovaný virtuální počítač dostatek IP adres. Ujistěte se také, že logická síť poskytovatele HNV má dostatečná IP adresa pro přidělení ke každému virtuálnímu počítači služby SLB/MUX a infrastruktury brány. I když je vyhrazená IP adresa spravovaná síťovým adaptérem, může se stát, že při nedostupnosti vyhradí novou IP adresu, může dojít k duplicitním IP adresám ve vaší síti.|

Informace o virtualizaci sítě Hyper-V (HNV), kterou můžete použít k virtualizaci sítí v nasazení Microsoft SDN, najdete v tématu [virtualizace sítě Hyper-v](/windows-server/networking/sdn/technologies/hyper-v-network-virtualization/hyper-v-network-virtualization).

#### <a name="gateways-and-the-software-load-balancer-slb"></a>Brány a Load Balancer softwaru (SLB)
Musíte vytvořit a zřídit další logické sítě, které budou používat brány a SLB. Nezapomeňte získat správné předpony IP adres, identifikátory sítě VLAN a IP adresy brány pro tyto sítě.

|                                |                     |
| :----------------------------- | :------------------ |
| **Logická síť veřejných virtuálních IP adres** | Veřejná virtuální síť IP (VIP) musí používat předpony podsítě protokolu IP, které jsou směrovatelné mimo cloudové prostředí (obvykle Internet směrovatelný). Jedná se o front-endové IP adresy, které externí klienti používají pro přístup k prostředkům ve virtuálních sítích, včetně front-endové VIP pro bránu site-to-site. K této síti nemusíte přiřazovat síť VLAN. |
| **Logická síť privátních virtuálních IP adres** | Logická síť privátních virtuálních IP adres není potřeba směrovat mimo Cloud. Důvodem je to, že ho používají jenom virtuální IP adresy, které jsou dostupné z interních cloudových klientů, jako jsou třeba soukromé služby. K této síti nemusíte přiřazovat síť VLAN. |
| **Logická síť GRE VIP** | Síť VIP (Generic Routing Encapsulation) je podsíť, která existuje výhradně k definování VIP. Virtuální IP adresy jsou přiřazené k virtuálním počítačům brány, které běží na vašich prostředcích infrastruktury SDN pro typ připojení Site-to-Site (S2S) GRE. Tuto síť nemusíte předem konfigurovat ve vašich fyzických přepínačích nebo směrovačích nebo k ní přiřadit síť VLAN. |

#### <a name="sample-network-topology"></a>Ukázková síťová topologie
Změňte si ukázkové předpony podsítě protokolu IP a identifikátory sítě VLAN pro vaše prostředí.

| Název sítě | Podsíť | Maska | ID sítě VLAN v režimu trunk | brána | Rezervace (příklady) |
| :----------------------- | :------------ | :------- | :---------------------------- | :-------------- | :------------------------------------------- |
| Správa              | 10.184.108.0 |    24   |          7                   | 10.184.108.1   | 10.184.108.1 – směrovač<br> 10.184.108.4 – síťový adaptér<br> 10.184.108.10 – výpočetní hostitel 1<br> 10.184.108.11 – výpočetní hostitel 2<br> 10.184.108. x – výpočetní hostitel X |
| Poskytovatel HNV             |  10.10.56.0  |    23    |          11                |  10.10.56.1    | 10.10.56.1 – směrovač<br> 10.10.56.2 – SLB/MUX1<br> 10.10.56.5 - Gateway1 |
| Veřejná virtuální IP adresa               |  41.40.40.0  |    27    |          Není k dispozici                |  41.40.40.1    | 41.40.40.1 – směrovač<br> 41.40.40.3-IPSec S2S VPN VIP |
| Privátní VIP              |  20.20.20.0  |    27    |          Není k dispozici                |  20.20.20.1    | 20.20.20.1 – výchozí GS (směrovač) |
| GRE VIP                  |  31.30.30.0  |    24    |          Není k dispozici                |  31.30.30.1    | 31.30.30.1 – výchozí GS |

## <a name="routing-infrastructure"></a>Infrastruktura směrování
Informace o směrování, \( jako je například další směrování \) pro podsítě VIP, jsou inzerovány pomocí bran služby SLB/MUX a serveru vzdáleného přístupu (RAS) do fyzické sítě pomocí interního partnerského vztahu protokolu BGP. Logické sítě VIP nemají přiřazenou síť VLAN a nejsou předem nakonfigurované v přepínači vrstvy 2 (například přepínač Top-of-rack).

Musíte vytvořit partnerský uzel protokolu BGP ve směrovači, který používá infrastruktura SDN pro příjem tras pro logické sítě VIP inzerované branami SLB/MUX a RAS. Partnerský vztah protokolu BGP se musí nacházet pouze jedním ze způsobů (od služby SLB/MUX nebo brány RAS k externímu partnerskému uzlu protokolu BGP). Nad první vrstvou směrování můžete použít statické trasy nebo jiný protokol dynamického směrování, jako je například Open nejkratší cesta First (OSPF). Jak už bylo uvedeno výše, předpona podsítě protokolu IP pro logické sítě VIP musí být směrovatelné z fyzické sítě na externí partnerský uzel protokolu BGP.

Partnerský vztah protokolu BGP je obvykle nakonfigurovaný ve spravovaném přepínači nebo směrovači jako součást síťové infrastruktury. Partnerský uzel protokolu BGP se dá nakonfigurovat taky na Windows serveru s rolí RAS nainstalovanou v režimu pouze směrování. Partnerský uzel směrovače protokolu BGP v síťové infrastruktuře musí být nakonfigurovaný tak, aby používal svoje vlastní autonomní systémová čísla (ASN) a umožňoval partnerský vztah z ASN, který je přiřazený k komponentám SDN, \( SLB/MUX a branám RAS \) .

Musíte získat následující informace od fyzického směrovače nebo od správce sítě po kontrolu tohoto směrovače:
- ASN směrovače
- IP adresa směrovače

>[!NOTE]
>4 bajty čísla ASN nejsou podporovány rozhraním SLB/MUX. Do SLB/MUX a směrovače, ke kterému se připojujete, musíte přidělit čísla ASN se dvěma bajty. Ve svém prostředí můžete použít čísla ASN se čtyřmi bajty kdekoli.

Vy nebo váš správce sítě musíte nakonfigurovat partnera směrovače protokolu BGP tak, aby přijímal připojení z ASN a IP adresy nebo adresy podsítě logické sítě poskytovatele HNV, kterou používá vaše brána služby RAS a SLB MUX.

Další informace najdete v tématu [Border Gateway Protocol (BGP)](/windows-server/remote/remote-access/bgp/border-gateway-protocol-bgp).

## <a name="default-gateways"></a>Výchozí brány
Počítače nakonfigurované pro připojení k více sítím, jako jsou fyzické hostitele, SLB/MUX a virtuální počítače brány, musí mít nakonfigurovanou jenom jednu výchozí bránu. Pro hostitele a virtuální počítače infrastruktury použijte následující výchozí brány:
- U hostitelů Hyper-V použijte jako výchozí bránu síť pro správu.
- Pro virtuální počítače síťového adaptéru použijte jako výchozí bránu síť pro správu.
- U virtuálních počítačů SLB/MUX použijte jako výchozí bránu síť pro správu.
- Pro virtuální počítače brány použijte jako výchozí bránu síť poskytovatele HNV. Toto nastavení by mělo být nastaveno na front-endové kartě virtuálních počítačů brány.

## <a name="network-hardware"></a>Síťový hardware
Tato část uvádí požadavky na nasazení síťového hardwaru pro síťové adaptéry a fyzické přepínače.

### <a name="network-interface-cards-nics"></a>Karty síťového rozhraní
Síťové adaptéry, které používáte ve svých hostitelích Hyper-V a hostitelích úložiště, vyžadují konkrétní možnosti, abyste dosáhli nejlepšího výkonu.

Přímý přístup do paměti vzdáleného počítače (RDMA) je technika obejít jádra, která umožňuje přenos velkých objemů dat bez použití hostitelského procesoru, který uvolňuje CPU k provedení jiné práce. Přepínač Embedded Teaming (SET) je alternativním řešením pro seskupování síťových adaptérů, které můžete použít v prostředích, která zahrnují technologii Hyper-V a sadu SDN. SADA integruje některé funkce seskupování síťových adaptérů do virtuálního přepínače Hyper-V.

Další informace najdete v tématech [přímý přístup do paměti vzdáleného počítače (RDMA) a Switch Embedded Teaming (set)](/windows-server/virtualization/hyper-v-virtual-switch/rdma-and-switch-embedded-teaming).

Aby bylo možné přihlédnout k režii v provozu virtuální sítě tenanta způsobené hlavičkou zapouzdření VXLAN nebo NVGRE, musí být maximální přenosová jednotka (MTU) síťové infrastruktury vrstvy 2 (přepínače a hostitelé) nastavená na hodnotu větší nebo rovna 1674 bajtů, \( včetně hlaviček sítě Ethernet vrstvy 2 \) .

Síťové adaptéry, které podporují nové klíčové slovo *EncapOverhead* Advanced Adapter, nastaví jednotku MTU automaticky prostřednictvím agenta hostitele síťového adaptéru. Síťové adaptéry, které nepodporují nové klíčové slovo *EncapOverhead* , musí ručně nastavit velikost jednotky MTU na každém fyzickém hostiteli *JumboPacket* pomocí \( klíčového slova JumboPacket nebo ekvivalentního \) .

### <a name="switches"></a>Přepínače
Při výběru fyzického přepínače a směrovače pro vaše prostředí se ujistěte, že podporuje následující sadu funkcí:
- Vyžaduje se nastavení switchport MTU. \(\)
- MTU nastavená na >= 1674 bajtů \( včetně hlavičky L2-Ethernet\)
- \(Vyžadovány protokoly L3\)
- Směrování EQUAL-cost multi-Path (ECMP)
- \( \) \- Ecmp založené na RFC IETF RFC 4271

Implementace by měly podporovat příkazy musí být v následujících standardech IETF:
- RFC 2545: [rozšíření protokolu BGP-4 pro směrování mezi doménami IPv6](https://tools.ietf.org/html/rfc2545)
- RFC 4760: [rozšíření protokolu BGP-4 s více protokoly](https://tools.ietf.org/html/rfc4760)
- RFC 4893: [Podpora protokolu BGP se čtyřmi oktety jako číselného prostoru](https://tools.ietf.org/html/rfc4893)
- RFC 4456: [reflexe trasy protokolu BGP: alternativa k internímu protokolu BGP pro celou síť (IBGP)](https://tools.ietf.org/html/rfc4456)
- RFC 4724: [mechanismus úspěšného restartování pro protokol BGP](https://tools.ietf.org/html/rfc4724)

Jsou vyžadovány následující protokoly označování:
- SÍŤ VLAN – izolace různých typů provozu
- 802.1 q – šachta

Ovládací prvek odkaz poskytují tyto položky:
- PFC kvality služeb \( QoS se \) \( vyžaduje jenom v případě, že používáte roce.\)
- Vylepšený výběr provozu \( 802.1 QAZ\)
- Řízení toku na základě priority (PFC) \( 802.1 p/Q a 802.1 QBB\)

Následující položky poskytují dostupnost a redundanci:
- Dostupnost přepínače (povinné)
- K provádění funkcí brány je nutný vysoce dostupný směrovač. Můžete to poskytnout pomocí switch\router a technologií, jako je nebo VRRP (Virtual router redundance Protocol).

### <a name="switch-configuration-examples"></a>Příklady konfigurace přepínače
Aby bylo možné konfigurovat fyzický přepínač nebo směrovač, je k dispozici sada ukázkových konfiguračních souborů pro nejrůznější modely přepínačů a dodavatelů v [úložišti Microsoft SDN GitHub](https://github.com/microsoft/SDN/tree/master/SwitchConfigExamples). K dispozici jsou podrobné příkazy Readme a testované rozhraní příkazového řádku (CLI) pro konkrétní přepínače.

## <a name="compute"></a>Compute
Všichni hostitelé Hyper-V musí mít nainstalovaný příslušný operační systém, povolit Hyper-V a používat externí virtuální přepínač Hyper-V s aspoň jedním fyzickým adaptérem připojeným k logické síti pro správu. Hostitel musí být dosažitelný prostřednictvím IP adresy pro správu přiřazené k hostiteli pro správu vNIC.

Můžete použít libovolný typ úložiště, který je kompatibilní s technologií Hyper-V, Shared nebo Local.

> [!TIP]
> Je vhodné použít stejný název pro všechny virtuální přepínače, ale není povinný. Pokud plánujete použít skripty k nasazení, podívejte se do komentáře přidruženého k `vSwitchName` proměnné v souboru config.psd1.

### <a name="host-compute-requirements"></a>Požadavky na výpočetní výkon hostitele
Níže jsou uvedeny minimální požadavky na hardware a software pro čtyři fyzické hostitele používané v ukázkovém nasazení.

Hostitel|Požadavky na hardware|Požadavky na software|
--------|-------------------------|-------------------------
|Fyzický Hostitel Hyper-V|4jádrový procesor 2,66 GHz<br> 32 GB paměti RAM<br> 300 GB místa na disku<br> fyzický síťový adaptér 1 GB/s (nebo rychlejší)|Operační systém: jak je definováno v<br> v části "platí pro" na začátku tohoto tématu.<br> Nainstalovaná role Hyper-V|

### <a name="sdn-infrastructure-vm-role-requirements"></a>Požadavky role virtuálních počítačů infrastruktury SDN
Níže jsou uvedeny požadavky na role virtuálních počítačů.

Role|požadavky na vCPU|Požadavky na paměť|Požadavky na disk|
--------|-----------------------------|-----------------------|--------------------------
|Síťový adaptér (tři uzly)|4 vCPU|minimálně 4 GB<br> (doporučeno 8 GB)|75 GB pro jednotku operačního systému
|SLB/MUX (tři uzly)|8 vCPU|doporučuje se 8 GB|75 GB pro jednotku operačního systému
|Brána RAS<br> (jeden fond tří uzlů<br> brány, dvě aktivní, jedna pasivní)|8 vCPU|doporučuje se 8 GB|75 GB pro jednotku operačního systému
|Směrovač protokolu BGP brány RAS<br> pro partnerský vztah SLB/MUX<br> (případně použijte přepínač "<br> jako směrovač protokolu BGP)|2 vCPU|2 GB|75 GB pro jednotku operačního systému|

Pokud pro nasazení používáte nástroj System Center-Virtual Machine Manager (VMM), vyžadují se pro VMM a jinou infrastrukturu mimo SDN další prostředky virtuálních počítačů infrastruktury. Další informace najdete v tématu [požadavky na systém pro System Center Virtual Machine Manager](https://docs.microsoft.com/system-center/vmm/system-requirements?view=sc-vmm-2019&preserve-view=true).

## <a name="extending-your-infrastructure"></a>Rozšíření vaší infrastruktury
Požadavky na velikost a prostředky pro vaši infrastrukturu závisí na virtuálních počítačích úloh klientů, které chcete hostovat. Požadavky na procesor, paměť a disk pro virtuální počítače infrastruktury (například: síťový adaptér, SLB, brána atd.) jsou definované v předchozí tabulce. Můžete přidat další virtuální počítače infrastruktury pro škálování podle potřeby. Všechny virtuální počítače klientů, které běží na hostitelích Hyper-V, ale mají vlastní požadavky na procesor, paměť a disk, které je potřeba vzít v úvahu.

Když se virtuální počítače zatížení spouštějí na fyzických hostitelích Hyper-V s využitím příliš velkého počtu prostředků, můžete svou infrastrukturu rozšíříte tak, že přidáte další fyzické hostitele. K vytvoření nových prostředků serveru prostřednictvím síťového adaptéru můžete použít buď centrum pro správu Windows, VMM, nebo skripty PowerShellu. Metoda, která se má použít, závisí na počátečním nasazení infrastruktury. Pokud potřebujete přidat další IP adresy pro síť poskytovatele HNV, můžete vytvořit nové logické podsítě (s odpovídajícími fondy IP adres), které můžou hostitelé používat.

## <a name="phased-deployment"></a>Dvoufázové nasazení
Na základě vašich požadavků možná budete muset nasadit podmnožinu infrastruktury SDN. Například pokud chcete, aby se v datacentru spouštěly jenom úlohy zákazníka, a externí komunikace se nevyžaduje, můžete nasadit síťový adaptér a přeskočit nasazení SLB/MUX a virtuálních počítačů brány. Následující článek popisuje požadavky na infrastrukturu síťových funkcí pro dvoufázové nasazení infrastruktury SDN.

Příznak|Požadavky nasazení|Požadavky sítě|
--------|-------------------------|-------------------------
|Správa logické sítě<br> Seznamy řízení přístupu (ACL) (pro síť využívající síť VLAN)<br> QoS (Quality of Service) (pro sítě založené na síti VLAN)<br>|Síťový adaptér|Žádné|
|Virtuální sítě<br> Směrování definované uživatelem<br> Seznamy řízení přístupu (pro virtuální síť)<br> Šifrované podsítě<br> QoS (pro virtuální sítě)<br> Partnerský vztah virtuální sítě|Síťový adaptér|HNV PA – síť VLAN, podsíť, směrovač|
|Příchozí/odchozí NAT<br> Vyrovnávání zatížení|Síťový adaptér<br> SLB/MUX|Protokol BGP v HNV PA – síť<br> Privátní a veřejné podsítě VIP|
|Připojení brány GRE|Síťový adaptér<br> brána|Protokol BGP v HNV PA – síť<br> Podsíť GRE VIP|
|Připojení brány IPSec|Síťový adaptér<br> SLB/MUX<br> brána|Protokol BGP v HNV PA – síť<br> Podsíť veřejné VIP|
|Připojení brány L3|Síťový adaptér<br> brána|SÍŤ VLAN klienta, podsíť, směrovač<br> Protokol BGP v klientské síti VLAN je nepovinný.|

## <a name="next-steps"></a>Další kroky
Související informace najdete v tématu také:
- [Požadavky pro nasazení síťového adaptéru](/windows-server/networking/sdn/plan/installation-and-preparation-requirements-for-deploying-network-controller)
- [SDN ve Azure Stack HCI](https://docs.microsoft.com/azure-stack/hci/concepts/software-defined-networking)