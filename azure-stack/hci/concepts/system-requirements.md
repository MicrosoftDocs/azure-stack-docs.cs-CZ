---
title: Požadavky na systém pro Azure Stack HCI
description: Jak zvolit servery, úložiště a síťové součásti pro Azure Stack HCI.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 10/29/2020
ms.openlocfilehash: 2d4aebd0609dff744f4c8f6ae9113fba1b7b0ba9
ms.sourcegitcommit: 26901a61a44390bc9b7804c22018c213036e680d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2020
ms.locfileid: "96354152"
---
# <a name="system-requirements-for-azure-stack-hci"></a>Požadavky na systém pro Azure Stack HCI

> Platí pro: Azure Stack HCI, verze 20H2

V tomto tématu můžete vyhodnotit požadavky na systém pro servery, úložiště a síť pro Azure Stack HCI.

## <a name="server-requirements"></a>Požadavky na server

Standardní Azure Stack cluster HCI vyžaduje minimálně dva servery a maximálně 16 serverů. clustery se ale dají kombinovat pomocí sad clusterů a vytvořit tak platformu HCI se stovkami uzlů.

U různých typů Azure Stackch prostředí HCI mějte na paměti následující:

- Roztažené clustery vyžadují nasazení serverů ve dvou různých lokalitách. Lokality můžou být v různých zemích, různých městech, různých podlahách nebo různých místnostech. Roztažené clustery vyžadují minimálně 4 servery (2 na jeden web) a maximálně 16 serverů (8 na lokalitu).

- Doporučuje se, aby všechny servery byly stejné jako výrobce a model, s využitím 64 Nehalem, technologie AMD EPYC nebo novějších kompatibilních procesorů s překladem adres druhé úrovně (SLAT). Druhá generace škálovatelného procesoru Intel Xeon je nutná k podpoře trvalé paměti Intel Optane DC. Procesory musí být aspoň 1,4 GHz a kompatibilní se sadou instrukcí x64.

- Ujistěte se, že jsou servery vybavené aspoň 32 GB paměti RAM na uzel, aby se vešly serverové operační systémy, virtuální počítače a další aplikace nebo úlohy. Kromě toho povolte pro Prostory úložiště s přímým přístupem metadat 4 GB paměti RAM na terabajtu (TB) kapacity jednotky mezipaměti na každém serveru.

- Ověřte, že je v systému BIOS nebo rozhraní UEFI zapnutá podpora virtualizace:
    - Hardwarovou podporu virtualizace. To je dostupné v procesorech, které zahrnují možnost virtualizace, konkrétně procesory s technologií Intel Virtualization Technology (Intel VT) nebo AMD Virtualization (AMD-V).
    - K dispozici a povolená musí být funkce hardwarem vynuceného zabránění spuštění dat (DEP). Pro systémy Intel se jedná o bit XD (spuštění zakazující bit). Pro systémy AMD se jedná o bit NX (bez bitu spouštění).

- Můžete použít jakékoli spouštěcí zařízení podporované systémem Windows Server, které [teď zahrnuje SATADOM](https://cloudblogs.microsoft.com/windowsserver/2017/08/30/announcing-support-for-satadom-boot-drives-in-windows-server-2016/). Zrcadlový svazek RAID 1 **není vyžadován, ale je podporován** pro spuštění. Doporučuje se minimální velikost 200 GB.

- Další požadavky na konkrétní funkce pro technologii Hyper-V najdete v tématu [požadavky na systém pro Hyper-v v systému Windows Server](/windows-server/virtualization/hyper-v/system-requirements-for-hyper-v-on-windows).

## <a name="storage-requirements"></a>Požadavky na úložiště

Azure Stack HCI funguje s přímými připojenými jednotkami SATA, SAS, NVMe nebo trvalé paměti, které jsou fyzicky připojené pouze k jednomu serveru.

Nejlepších výsledků dosáhnete, když zadržíte následující:

- Každý server v clusteru musí mít stejné typy jednotek a stejné číslo každého typu. Doporučuje se také (ale není nutné), aby jednotky měly stejnou velikost a model. Jednotky můžou být interní na serveru nebo v externí skříni, která je připojená jenom k jednomu serveru. Další informace najdete v tématu věnovaném [hlediskům symetrie jednotek](drive-symmetry-considerations.md).

- Každý server v clusteru by měl mít vyhrazené svazky pro protokoly s úložištěm protokolů aspoň tak rychle jako úložiště dat. Roztažené clustery vyžadují aspoň dva svazky: jednu pro replikovaná data a jednu pro data protokolu.

- Pro mapování a identifikaci slotu se vyžadují služby skříně SCSI (SES). Každá externí skříň musí představovat jedinečný identifikátor (jedinečné ID). 

   > [!IMPORTANT]
   > **NEpodporováno:** Řadiče RAID nebo úložiště SAN (technologie Fibre Channel, iSCSI, FCoE), sdílené skříně SAS připojené k více serverům nebo libovolná forma vstupně-výstupní operace s více cestami (MPIO), kde jsou jednotky přístupné pomocí více cest. Karty hostitelského adaptéru (HBA) musí implementovat jednoduchý předávací režim.

## <a name="networking-requirements"></a>Požadavky na síť

Cluster Azure Stack HCI vyžaduje spolehlivé síťové připojení s vysokou šířkou pásma a nízkou latencí mezi jednotlivými uzly serveru.

- Ověřte, zda je k dispozici alespoň jeden síťový adaptér, který je vyhrazen pro správu clusteru.
- Ověřte, že fyzické přepínače ve vaší síti jsou nakonfigurované tak, aby umožňovaly provoz na všech sítích VLAN, které budete používat.

Mezi uzly serveru probíhá více typů komunikace:

- Komunikace s clustery (spojení uzlů, aktualizace clusteru, aktualizace registru)
- Prezenční signály clusteru
- Přesměrované přenosy sdílený svazek clusteru (CSV)
- Provoz migrace za provozu pro virtuální počítače

V Prostory úložiště s přímým přístupem existuje další síťový provoz, který je potřeba vzít v úvahu:

- Vrstva sběrnice úložiště (SBL) – rozsahy nebo data rozprostře mezi uzly.
- Stav – monitorování a správa objektů (uzly, jednotky, síťové karty, Clusterová služba)

V případě roztaženého clusteru je mezi lokalitami předávány i další přenosy repliky úložiště. Provoz vrstvy úložiště (SBL) a sdílený svazek clusteru (CSV) mezi lokalitami nepřekračuje, jenom mezi uzly serveru v každé lokalitě.

Informace o požadavcích a požadavcích sítě hostitele najdete v tématu [požadavky na síť hostitele](host-network-requirements.md).

## <a name="software-defined-networking-sdn-requirements"></a>Požadavky na software definované sítě (SDN)

Když vytvoříte cluster Azure Stack HCI pomocí centra pro správu systému Windows, máte možnost nasadit síťový adaptér, který umožní softwarově definované sítě (SDN). Pokud máte v úmyslu použít SDN na Azure Stack HCI:

- Zajistěte, aby na hostitelských serverech bylo aspoň 50-100 GB volného místa pro vytvoření virtuálních počítačů síťového adaptéru.

- Aby bylo možné vytvořit virtuální počítače síťového adaptéru, je nutné zkopírovat virtuální pevný disk (VHD) Azure Stackho operačního systému HCI do prvního uzlu v clusteru. Virtuální pevný disk můžete připravit pomocí [nástroje Sysprep](/windows-hardware/manufacture/desktop/sysprep-process-overview) nebo spuštěním skriptu [Convert-WindowsImage](https://gallery.technet.microsoft.com/scriptcenter/Convert-WindowsImageps1-0fe23a8f) k převedení souboru. ISO na disk VHD.

Další informace o přípravě na použití SDN v Azure Stack HCI najdete v tématu [plánování infrastruktury softwarově definované sítě](plan-software-defined-networking-infrastructure.md) a [Plánování nasazení síťového adaptéru](../concepts/network-controller.md).

   > [!NOTE]
   > SDN není v roztaženém clusteru (více lokalit) podporován.

### <a name="sdn-hardware-requirements"></a>Požadavky na hardware SDN

Tato část poskytuje síťové požadavky na síťový hardware pro síťové karty a fyzické přepínače při plánování prostředí SDN.

#### <a name="network-interface-cards-nics"></a>Karty síťového rozhraní (nic)

Síťové adaptéry, které používáte ve svých hostitelích Hyper-V a hostitelích úložiště, vyžadují konkrétní možnosti, abyste dosáhli nejlepšího výkonu.

Přímý přístup do paměti vzdáleného počítače (RDMA) je technika obejít jádra, která umožňuje přenos velkých objemů dat bez použití hostitelského procesoru, který uvolňuje CPU k provedení jiné práce. Přepínač Embedded Teaming (SET) je alternativním řešením pro seskupování síťových adaptérů, které můžete použít v prostředích, která zahrnují technologii Hyper-V a sadu SDN. SADA integruje některé funkce seskupování síťových adaptérů do virtuálního přepínače Hyper-V.

Další informace najdete v tématech [přímý přístup do paměti vzdáleného počítače (RDMA) a Switch Embedded Teaming (set)](/windows-server/virtualization/hyper-v-virtual-switch/rdma-and-switch-embedded-teaming).

Aby bylo možné přihlédnout k režii v provozu virtuální sítě tenanta způsobené hlavičkou zapouzdření VXLAN nebo NVGRE, musí být maximální přenosová jednotka (MTU) síťové infrastruktury vrstvy 2 (přepínače a hostitelé) nastavená na hodnotu větší nebo rovna 1674 bajtů, \( včetně hlaviček sítě Ethernet vrstvy 2 \) .

Síťové adaptéry, které podporují nové klíčové slovo *EncapOverhead* Advanced Adapter, nastaví jednotku MTU automaticky prostřednictvím agenta hostitele síťového adaptéru. Síťové adaptéry, které nepodporují nové klíčové slovo *EncapOverhead* , musí ručně nastavit velikost jednotky MTU na každém fyzickém hostiteli *JumboPacket* pomocí \( klíčového slova JumboPacket nebo ekvivalentního \) .

#### <a name="switches-and-routers"></a>Přepínače a směrovače

Při výběru fyzického přepínače a směrovače pro prostředí SDN se ujistěte, že podporuje následující sadu funkcí:
- Vyžaduje se nastavení switchport MTU. \(\)
- MTU nastavená na >= 1674 bajtů \( včetně L2-Ethernet záhlaví\)
- \(Vyžadovány protokoly L3\)
- Směrování EQUAL-cost multi-Path (ECMP)
- \( \) \- Ecmp založené na RFC IETF RFC 4271

Implementace by měly podporovat příkazy musí být v následujících standardech IETF:
- RFC 2545: [rozšíření protokolu BGP-4 pro IPv6 Inter-Domain směrování](https://tools.ietf.org/html/rfc2545)
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

### <a name="domain-requirements"></a>Požadavky na doménu

Pro Azure Stack HCL nejsou k dispozici žádné zvláštní požadavky na úroveň funkčnosti domény pro váš řadič domény, který je stále podporován. Doporučujeme, abyste funkci Koš služby Active Directory zapnuli jako obecný osvědčený postup, pokud jste to ještě neudělali. Další informace najdete v tématu [přehled Active Directory Domain Services](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview).

## <a name="maximum-supported-hardware-specifications"></a>Maximální podporované specifikace hardwaru

Azure Stack nasazení HCI, která překračují následující specifikace, se nepodporují:

| Prostředek                     | Maximum |
| ---------------------------- | --------|
| Fyzické servery na cluster | 16      |
| Virtuální počítače na hostitele                 | 1 024   |
| Počet disků na virtuální počítač (SCSI)          | 256     |
| Úložiště na cluster          | 4 PB    |
| Úložiště na server           | 400 TB  |
| Logické procesory na hostitele  | 512     |
| RAM na hostitele                 | 24 TB   |
| RAM na virtuální počítač                   | 12 TB (virtuální počítač 2. generace) nebo 1 TB (generace 1)|
| Virtuální procesory na hostitele  | 2 048   |
| Virtuální procesory na virtuální počítač    | 240 (virtuální počítač 2. generace) nebo 64 (generace 1)|

## <a name="next-steps"></a>Další kroky

Související informace najdete v tématu také:

- [Zvolit jednotky](choose-drives.md)
- [Prostory úložiště s přímým přístupem požadavky na hardware](/windows-server/storage/storage-spaces/storage-spaces-direct-hardware-requirements)
