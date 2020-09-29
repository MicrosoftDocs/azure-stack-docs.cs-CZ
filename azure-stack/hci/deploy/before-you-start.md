---
title: Před nasazením Azure Stack HCL
description: Příprava na nasazení Azure Stack HCI.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 09/24/2020
ms.openlocfilehash: d4dc446f5d58f25ba6183cf4415b5f4e2d34df9a
ms.sourcegitcommit: 034e61836038ca75199a0180337257189601cd12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91230457"
---
# <a name="before-you-deploy-azure-stack-hci"></a>Před nasazením Azure Stack HCL

> Platí pro: Azure Stack HCI, verze 20H2

V této příručce se dozvíte, jak:

- Určete, jestli hardware splňuje základní požadavky pro standardní (jeden web) nebo roztažené (dvě lokality) Azure Stackch clusterech HCI.
- Ujistěte se, že nepřekračujete maximální podporované specifikace hardwaru.
- Shromáždění požadovaných informací pro úspěšné nasazení
- Instalace centra pro správu Windows na počítač nebo na server pro správu

Informace o požadavcích na Azure Stack HCL pro službu Azure Kubernetes najdete v tématu [AKS požadavky na Azure Stack HCL](../../aks-hci/overview.md#what-you-need-to-get-started).

## <a name="determine-hardware-requirements"></a>Určení hardwarových požadavků

Společnost Microsoft doporučuje, abyste od našich partnerů nakoupili ověřené Azure Stack hardwarového a softwarového řešení HCI. Tato řešení jsou navržená, sestavená a ověřená v rámci naší referenční architektury za účelem zajištění kompatibility a spolehlivosti, takže můžete rychle začít pracovat. Ověřte, že systémy, součásti, zařízení a ovladače, které používáte, jsou systémy Windows Server 2019 s certifikací na základě katalogu Windows serveru. Ověřená řešení najdete na webu [řešení Azure Stack HCL](https://azure.microsoft.com/overview/azure-stack/hci) .

### <a name="server-requirements"></a>Požadavky na server

- Standardní Azure Stack cluster HCI vyžaduje minimálně 2 servery a maximálně 16 serverů. clustery se ale dají kombinovat pomocí sad clusterů a vytvořit tak platformu HCI se stovkami uzlů.
- Roztažené clustery vyžadují nasazení serverů ve dvou různých lokalitách. Lokality můžou být v různých zemích, různých městech, různých podlahách nebo různých místnostech. Roztažené clustery vyžadují minimálně 4 servery (2 na jeden web) a maximálně 16 serverů (8 na lokalitu).
- Doporučuje se, aby všechny servery byly stejné jako výrobce a model, s využitím 64 Nehalem, technologie AMD EPYC nebo novějších kompatibilních procesorů s překladem adres druhé úrovně (SLAT). Pro podporu trvalé paměti Intel Optane DC se vyžaduje druhá generace škálovatelný procesor Intel Xeon. Procesory musí být aspoň 1,4 GHz a kompatibilní se sadou instrukcí x64.
- Ujistěte se, že jsou servery vybavené aspoň 32 GB paměti RAM na uzel, aby se vešly serverové operační systémy, virtuální počítače a další aplikace nebo úlohy. Kromě toho povolte pro Prostory úložiště s přímým přístupem metadat 4 GB paměti RAM na terabajtu (TB) kapacity jednotky mezipaměti na každém serveru.
- Ověřte, že je v systému BIOS nebo rozhraní UEFI zapnutá podpora virtualizace:
    - Hardwarovou podporu virtualizace. To je dostupné v procesorech, které zahrnují možnost virtualizace, konkrétně procesory s technologií Intel Virtualization Technology (Intel VT) nebo AMD Virtualization (AMD-V).
    - K dispozici a povolená musí být funkce hardwarem vynuceného zabránění spuštění dat (DEP). Pro systémy Intel se jedná o bit XD (spuštění zakazující bit). Pro systémy AMD se jedná o bit NX (bez bitu spouštění).
- Můžete použít jakékoli spouštěcí zařízení podporované systémem Windows Server, které [teď zahrnuje SATADOM](https://cloudblogs.microsoft.com/windowsserver/2017/08/30/announcing-support-for-satadom-boot-drives-in-windows-server-2016/). Zrcadlový svazek RAID 1 **není vyžadován, ale je podporován** pro spuštění. Doporučuje se minimální velikost 200 GB.
- Další požadavky na konkrétní funkce pro technologii Hyper-V najdete v tématu [požadavky na systém pro Hyper-v v systému Windows Server](/windows-server/virtualization/hyper-v/system-requirements-for-hyper-v-on-windows).

### <a name="networking-requirements"></a>Požadavky na síť

Cluster Azure Stack HCI vyžaduje spolehlivé síťové připojení s vysokou šířkou pásma a nízkou latencí mezi jednotlivými uzly serveru. Ověřte následující:

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

### <a name="software-defined-networking-requirements"></a>Požadavky na softwarově definované sítě

Když vytvoříte cluster Azure Stack HCI pomocí centra pro správu systému Windows, máte možnost nasadit síťový adaptér, který umožní softwarově definované sítě (SDN). Pokud máte v úmyslu použít SDN na Azure Stack HCI:

- Zajistěte, aby na hostitelských serverech bylo aspoň 50-100 GB volného místa pro vytvoření virtuálních počítačů síťového adaptéru.

- Aby bylo možné vytvořit virtuální počítače síťového adaptéru, je nutné zkopírovat virtuální pevný disk (VHD) Azure Stackho operačního systému HCI do prvního uzlu v clusteru. Virtuální pevný disk můžete připravit pomocí [nástroje Sysprep](/windows-hardware/manufacture/desktop/sysprep-process-overview) nebo spuštěním skriptu [Convert-WindowsImage](https://gallery.technet.microsoft.com/scriptcenter/Convert-WindowsImageps1-0fe23a8f) k převedení souboru. ISO na disk VHD.

Další informace o přípravě na použití SDN v Azure Stack HCI najdete v tématu [plánování infrastruktury softwarově definované sítě](../concepts/plan-software-defined-networking-infrastructure.md) a [Plánování nasazení síťového adaptéru](../concepts/network-controller.md).

### <a name="domain-requirements"></a>Požadavky na doménu

Pro Azure Stack HCL nejsou k dispozici žádné zvláštní požadavky na úroveň funkčnosti domény pro váš řadič domény, který je stále podporován. Doporučujeme, abyste funkci Koš služby Active Directory zapnuli jako obecný osvědčený postup, pokud jste to ještě neudělali.

### <a name="interconnect-requirements-between-nodes"></a>Požadavky propojení mezi uzly

Tato část popisuje specifické požadavky sítě mezi uzly serveru v lokalitě, která se nazývá propojení. Je možné použít přepínací uzel nebo vzájemně propojené uzly, které jsou podporované:

- **Přepnuto:** Uzly serveru se nejčastěji připojují přes sítě Ethernet, které používají síťové přepínače. Přepínače musí být správně nakonfigurovány pro zpracování šířky pásma a typu sítě. Pokud používáte RDMA, který implementuje protokol RoCE, je důležitá konfigurace síťového zařízení a přepínače.
- Bez **přepínání:** Uzly serveru je taky možné propojit pomocí přímých připojení Ethernet bez přepínače. V takovém případě musí mít každý uzel serveru přímé spojení s každým jiným uzlem clusteru ve stejné lokalitě.

#### <a name="interconnects-for-2-3-node-clusters"></a>Propojení clusterů uzlů 2-3

Jedná se o *minimální* požadavky propojení pro clustery s jedním webem, které mají dva nebo tři uzly. Platí pro každý uzel serveru:

- Jedna nebo víc síťových adaptérů s 1 GB, které se mají použít pro funkce správy
- Jedna nebo více 10 GB (nebo rychlejších) síťových adaptérů pro provoz úložiště a úloh
- Pro zajištění redundance a výkonu se doporučuje dvě nebo víc síťových připojení mezi jednotlivými uzly.

#### <a name="interconnects-for-4-node-and-greater-clusters"></a>Propojení pro clustery se čtyřmi a více uzly

Toto jsou *minimální* požadavky na propojení pro clustery se čtyřmi nebo více uzly a pro vysoce výkonné clustery. Platí pro každý uzel serveru:

- Jedna nebo víc síťových adaptérů s 1 GB, které se mají použít pro funkce správy
- Jedna nebo více 25 GB (nebo rychlejší) síťových adaptérů pro provoz úložiště a úloh. Pro zajištění redundance a výkonu doporučujeme dvě nebo víc síťových připojení.
- Síťové karty, které podporují přímý přístup do paměti vzdáleného počítače (RDMA): iWARP (doporučeno) nebo RoCE.

### <a name="site-to-site-requirements-stretched-cluster"></a>Požadavky na lokalitu (roztažené clustery)

Při připojování mezi lokalitami pro roztažené clustery se stále používají požadavky na propojení v rámci každé lokality a mají další repliky úložiště a požadavky migrace Hyper-V za provozu, které je potřeba vzít v úvahu:

- Aspoň jedno připojení RDMA nebo Ethernet/TCP mezi lokalitami pro synchronní replikaci. Upřednostňuje se připojení RDMA od 25 GB.
- Síť mezi lokalitami s dostatečnou šířkou pásma, která bude obsahovat vaše vstupně-výstupní úlohy zápisu, a průměrnou latenci 5ms odezvy nebo nižší pro synchronní replikaci. Asynchronní replikace nemá doporučení pro latenci.
- Pokud používáte jedno připojení mezi lokalitami, nastavte omezení šířky pásma protokolu SMB pro repliku úložiště pomocí PowerShellu. Další informace najdete v tématu [set-SmbBandwidthLimit](/powershell/module/smbshare/set-smbbandwidthlimit).
- Pokud používáte více připojení mezi lokalitami, oddělte provoz mezi připojeními. Například vložte provoz repliky úložiště do samostatné sítě, než je migrace Hyper-V za provozu pomocí PowerShellu. Další informace najdete v tématu [set-SRNetworkConstraint](/powershell/module/storagereplica/set-srnetworkconstraint).

### <a name="network-port-requirements"></a>Požadavky na síťový port

Ujistěte se, že jsou mezi všemi uzly serveru v lokalitě i mezi lokalitami (pro roztažené clustery) otevřené správné síťové porty. Budete potřebovat patřičná pravidla brány firewall a směrovačů, aby bylo možné v obousměrném přenosu přes protokol ICMP, SMB (port 445 a port 5445 pro SMB Direct) a WS-MAN (port 5985) mezi všemi servery v clusteru.

Při použití Průvodce vytvořením clusteru v centru pro správu systému Windows k vytvoření clusteru se v průvodci automaticky otevře příslušné porty brány firewall na každém serveru v clusteru pro clustering s podporou převzetí služeb při selhání, Hyper-V a repliku úložiště. Pokud na každém serveru používáte jinou bránu firewall softwaru, otevřete následující porty:

#### <a name="failover-clustering-ports"></a>Porty clusteringu s podporou převzetí služeb při selhání

- ICMPv4 a ICMPv6
- Port TCP 445
- Dynamické porty RPC
- Port TCP 135
- Port TCP 137
- Port TCP 3343
- Port UDP 3343

#### <a name="hyper-v-ports"></a>Porty Hyper-V

- Port TCP 135
- Port TCP 80 (připojení HTTP)
- Port TCP 443 (připojení HTTPS)
- Port TCP 6600
- Port TCP 2179
- Dynamické porty RPC
- Mapovač koncových bodů Endpoint Mapper RPC
- Port TCP 445

#### <a name="storage-replica-ports-stretched-cluster"></a>Porty repliky úložiště (roztažené clustery)

- Port TCP 445
- TCP 5445 (při použití iWarp RDMA)
- Port TCP 5985
- ICMPv4 a ICMPv6 (při použití test-SRTopology)

Můžou se vyžadovat další porty, které nejsou uvedené výše. Toto jsou porty pro základní funkce rozhraní Azure Stack HCI.

### <a name="storage-requirements"></a>Požadavky na úložiště

- Azure Stack HCI funguje s přímými připojenými jednotkami SATA, SAS, NVMe nebo trvalé paměti, které jsou fyzicky připojené pouze k jednomu serveru.
- Každý server v clusteru musí mít stejné typy jednotek a stejné číslo každého typu. Doporučuje se také (ale není nutné), aby jednotky měly stejnou velikost a model. Jednotky můžou být interní na serveru nebo v externí skříni, která je připojená jenom k jednomu serveru. Další informace najdete v tématu věnovaném [hlediskům symetrie jednotek](../concepts/drive-symmetry-considerations.md).
- Každý server v clusteru by měl mít vyhrazené svazky pro protokoly s úložištěm protokolů aspoň tak rychle jako úložiště dat. Roztažené clustery vyžadují aspoň dva svazky: jednu pro replikovaná data a jednu pro data protokolu.
- Pro mapování a identifikaci slotu se vyžadují služby skříně SCSI (SES). Každá externí skříň musí představovat jedinečný identifikátor (jedinečné ID). **NEpodporováno:** Řadiče RAID nebo úložiště SAN (technologie Fibre Channel, iSCSI, FCoE), sdílené skříně SAS připojené k více serverům nebo libovolná forma vstupně-výstupní operace s více cestami (MPIO), kde jsou jednotky přístupné pomocí více cest. Karty hostitelského adaptéru (HBA) musí implementovat jednoduchý předávací režim.
- Další nápovědu najdete v tématu [Volba jednotek](../concepts/choose-drives.md) nebo [prostory úložiště s přímým přístupem hardwarové požadavky](/windows-server/storage/storage-spaces/storage-spaces-direct-hardware-requirements).
- Dostupné možnosti pro svazky a odolnost najdete v tématu [plánování svazků v tématu prostory úložiště s přímým přístupem](/windows-server/storage/storage-spaces/plan-volumes#choosing-the-resiliency-type) .
- Pokud nasazujete i virtuální počítače a virtualizované úložiště, přečtěte si téma [použití prostory úložiště s přímým přístupem v clusterech hostovaných virtuálních počítačů](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm).

## <a name="review-maximum-supported-hardware-specifications"></a>Kontrola maximálního počtu podporovaných specifikací hardwaru

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

## <a name="gather-information"></a>Shromažďování informací

Při přípravě na nasazení shromážděte následující podrobnosti o vašem prostředí:

- **Názvy serverů:** Seznamte se s zásadami pro pojmenovávání počítačů, souborů, cest a dalších prostředků ve vaší organizaci. Budete muset zřídit několik serverů, z nichž každý má jedinečné názvy.
- **Název domény:** Seznamte se se zásadami vaší organizace pro pojmenovávání domén a připojení k doméně. Připojíte servery k doméně a budete muset zadat název domény.
- **Statické IP adresy:** Azure Stack HCI vyžaduje statické IP adresy pro provoz úložiště a zatížení a nepodporuje přiřazování dynamických IP adres prostřednictvím protokolu DHCP pro tuto vysokorychlostní síť. Protokol DHCP můžete použít pro síťový adaptér pro správu, pokud nepoužíváte dva v týmu. v takovém případě je nutné použít statické IP adresy. Informace o IP adrese, kterou byste měli použít pro každý server v clusteru, získáte od správce sítě.
- **Sítě RDMA:** Existují dva typy protokolů RDMA: iWarp a RoCE. Všimněte si, že jeden ze síťových adaptérů používá, a pokud RoCE, poznamenejte si také verzi (v1 nebo v2). V případě RoCE si také poznamenejte model přepínače horního stojanu.
- **ID sítě VLAN:** Poznamenejte si ID sítě VLAN, které se má použít pro síťové adaptéry na serverech (pokud nějaké existují). To by mělo být možné získat od správce sítě.
- **Názvy webů:** Pro roztažené clustery se pro zotavení po havárii používají dvě lokality. Můžete nastavit lokality pomocí Active Directory Domain Services, nebo Průvodce vytvořením clusteru může automaticky nastavit pro vás. Informace o nastavování lokalit vám poskytne správce domény. Další informace najdete v tématu [přehled Active Directory Domain Services](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview).

## <a name="install-windows-admin-center"></a>Nainstalovat centrum pro správu Windows

Centrum pro správu Windows je místně nasazená aplikace založená na prohlížeči pro správu Azure Stack HCI. Nejjednodušší způsob, jak [nainstalovat centrum pro správu Windows](/windows-server/manage/windows-admin-center/deploy/install) , je na místním počítači pro správu (desktopový režim), ale můžete ho taky nainstalovat na server (režim služby).

Pokud nainstalujete centrum pro správu Windows na server, úlohy vyžadující CredSSP, jako je vytvoření clusteru a instalace aktualizací a rozšíření, vyžadují použití účtu, který je členem skupiny Správci brány na serveru centra pro správu systému Windows. Další informace najdete v prvním dvou částech [Konfigurace uživatelských Access Control a oprávnění](/windows-server/manage/windows-admin-center/configure/user-access-control#gateway-access-role-definitions).

## <a name="next-steps"></a>Další kroky

V dalším článku se dozvíte, jak nasadit operační systém Azure Stack HCI.
> [!div class="nextstepaction"]
> [Nasazení operačního systému Azure Stack HCI](operating-system.md)
