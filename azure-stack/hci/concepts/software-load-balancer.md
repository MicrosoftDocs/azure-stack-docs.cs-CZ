---
title: Software Load Balancer (SLB) pro SDN v Azure Stack HCI
description: V tomto tématu se dozvíte o Load Balancer softwaru pro softwarově definované sítě v Azure Stack HCI.
author: khdownie
ms.author: v-kedow
ms.topic: overview
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 10/8/2020
ms.openlocfilehash: 35cf6d997a7200b762b8da052e5e30038f288bdc
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2021
ms.locfileid: "98254989"
---
# <a name="what-is-software-load-balancer-slb-for-sdn"></a>Co je software Load Balancer \( SLB \) pro Sdn?

> Platí pro: Azure Stack HCI, verze 20H2; Windows Server 2019

Poskytovatelé cloudových služeb (CSP) a podniky, které nasazují [software definované sítě (SDN) v Azure Stack HCI](software-defined-networking.md) , můžou k rovnoměrné distribuci síťového provozu klientů a klientů tenanta mezi prostředky virtuální sítě použít software Load BALANCER (SLB). SLB umožňuje více serverům hostovat stejnou úlohu, což zajišťuje vysokou dostupnost a škálovatelnost.

Load Balancer softwaru obsahuje následující funkce:

- Služby Vyrovnávání zatížení vrstvy 4 (L4) pro přenosy dat pro sever/jih a na východ a na západ TCP/UDP.

- Vyrovnávání zatížení veřejných sítí a interních síťových přenosů.

- Podpora dynamických IP adres (DIP) u virtuálních místních sítí (VLAN) a ve virtuálních sítích, které vytvoříte pomocí virtualizace sítě Hyper-V.

- Podpora sondy stavu.

- Připravené na škálování v cloudu, včetně možností škálování na více instancí a možností škálování pro multiplexor a hostitelské agenty.

- Sjednocený hraniční Server s více klienty bez problémů integrovaných s technologiemi SDN, jako je brána RAS, firewall Datacenter a reflektor trasy.

Další informace najdete v tématu [funkce Load Balancer softwaru](#bkmk_features) v tomto tématu.

> [!NOTE]
> Víceklientská architektura pro sítě VLAN není síťovým adaptérem podporována. Můžete ale použít sítě VLAN s SLB pro úlohy spravované poskytovateli služeb, jako je například infrastruktura Datacenter a webové servery s vysokou hustotou.

Pomocí Load Balancer softwaru můžete škálovat možnosti vyrovnávání zatížení pomocí virtuálních počítačů (VM) služby SLB na stejných výpočetních serverech Hyper-V, které používáte pro jiné úlohy virtuálních počítačů. Z tohoto důvodu podporuje vyrovnávání zatížení softwaru rychlé vytváření a mazání koncových bodů vyrovnávání zatížení, které jsou požadovány pro operace CSP. Vyrovnávání zatížení softwaru navíc podporuje desítky gigabajtů na cluster, poskytuje jednoduchý zřizovací model a umožňuje snadné horizontální navýšení a navýšení kapacity.

## <a name="how-software-load-balancer-works"></a>Jak funguje Load Balancer softwaru

Software Load Balancer funguje tak, že namapuje virtuální IP adresy (VIP) na DIP, které jsou součástí sady prostředků cloudové služby v datacentru.

VIP jsou jedna IP adresa, která poskytuje veřejný přístup k fondu virtuálních počítačů s vyrovnáváním zatížení. Například VIP jsou IP adresy, které jsou zpřístupněny na internetu, aby se klienti a klienti klientů mohli připojovat k prostředkům tenanta v cloudovém datovém centru.

DIP jsou IP adresy virtuálních počítačů ve fondu s vyrovnáváním zatížení za virtuální IP adresou. K prostředkům tenanta se přiřadí v rámci cloudové infrastruktury DIP.

Virtuální IP adresy se nacházejí v multiplexu SLB (MUX).  MUX se skládá z jednoho nebo více virtuálních počítačů.  Síťový adaptér poskytuje každé MUX každé virtuální IP adrese a každé MUX používá Border Gateway Protocol (BGP) k inzerování každé virtuální IP adresy směrovačům ve fyzické síti jako trasu/32.  Protokol BGP umožňuje směrovačům fyzické sítě:

- Přečtěte si, že virtuální IP adresa je k dispozici na všech MUX, i když se MUX nacházejí v různých podsítích v síti vrstvy 3.

- Rozšíří zatížení každé virtuální IP adresy napříč všemi dostupnými MUX pomocí směrování ECMP (EQUAL cost multi-Path).

- Automaticky zjistí selhání nebo odebrání MUX a zastaví odesílání provozu do neúspěšného přenosu MUX.

- Rozprostře zatížení z neúspěšných nebo odebraných instancí MUX v rámci zdravého Muxu.

Když se veřejné přenosy dostanou z Internetu, služba SLB MUX prověřuje přenos, který obsahuje VIP jako cíl, a namapuje a přepíše provoz tak, aby se dostal na jednotlivé DIP. Pro příchozí síťový provoz se tato transakce provádí v procesu se dvěma kroky, který je rozdělen mezi virtuální počítače MUX a hostitele technologie Hyper-V, kde je umístěn cílový DIP:

1. Vyrovnávání zatížení – MUX používá VIP k výběru DIP, zapouzdřuje paket a přepošle provoz na hostitele Hyper-V, kde se nachází DIP.

2. Překlad síťových adres (NAT) – Hostitel Hyper-V odebírá zapouzdření z paketu, překládá virtuální IP adresu na DIP, přemapuje porty a předává paket na virtuální počítač DIP.

MUX ví, jak namapovat VIP na správné DIP kvůli zásadám vyrovnávání zatížení, které definujete pomocí síťového adaptéru. Mezi tato pravidla patří protokol, port front-end, back-end port a distribuční algoritmus (5, 3 nebo 2 řazené kolekce členů).

Když virtuální počítače klienta reagují a odesílají odchozí síťové přenosy zpátky do internetových nebo vzdálených tenantů, protože služba NAT provádí hostitele Hyper-V, přeskočí zatížení MUX a přímo do hraničního směrovače z hostitele Hyper-V. Tento proces obejití instancí MUX se nazývá přímé vrácení serveru (DSR).

A po navázání počátečního toku síťových přenosů příchozí síťový provoz obchází všechny instance SLB MUX zcela.

Na následujícím obrázku klientský počítač provede dotaz DNS na IP adresu webu služby SharePoint společnosti, v tomto případě fiktivní společnost s názvem contoso. Dojde k následujícímu procesu:

1. Server DNS vrátí VIP 107.105.47.60 klientovi.

2. Klient odešle požadavek HTTP na virtuální IP adresu.

3. Fyzická síť má k dispozici více cest, aby bylo možné dosáhnout virtuální IP adresy nacházející se v rozhraních MUX.  Každý směrovač na cestě používá ECMP k výběru dalšího segmentu cesty, dokud požadavek nepřijde na MUX.

4. MUX, který obdrží požadavek, ověří nakonfigurované zásady a zjistí, že ve virtuální síti jsou k dispozici dvě vyhrazené IP adresy, 10.10.10.5 a 10.10.20.5, které zpracovávají požadavek na VIP 107.105.47.60.

5. MUX vybere DIP 10.10.10.5 a zapouzdřuje pakety pomocí VXLAN, aby ji mohl odeslat do hostitele, který obsahuje DIP, pomocí fyzické síťové adresy hostitele.

6. Hostitel přijme zapouzdřený paket a zkontroluje ho. Odstraní zapouzdření a přepíše paket tak, aby byl místo virtuální IP adresy 10.10.10.5 místo VIP a pak odeslal provoz na DIP virtuální počítač.

7. Požadavek dosáhne webu služby SharePoint společnosti Contoso v serverové farmě 2. Server vygeneruje odpověď a pošle ji klientovi pomocí vlastní IP adresy jako zdroje.

8. Hostitel zachycuje odchozí paket ve virtuálním přepínači, který pamatuje, že klient, nyní cíl, vytvořil původní požadavek na virtuální IP adresu.  Hostitel přepíše zdroj paketu jako virtuální IP adresu, aby klient nevidí adresu DIP.

9. Hostitel přepošle paket přímo do výchozí brány pro fyzickou síť, která používá svou standardní směrovací tabulku pro přeposílání paketu klientovi, který obdrží odpověď.

:::image type="content" source="media/software-load-balancing/slb-process.jpg" alt-text="Proces vyrovnávání zatížení softwaru" border="false":::

### <a name="load-balancing-internal-datacenter-traffic"></a>Vyrovnávání zatížení interního datového centra

Při vyrovnávání zatížení sítě interního datového centra, například mezi prostředky tenanta, které běží na různých serverech a jsou členy stejné virtuální sítě, je virtuální přepínač Hyper-V, ke kterému jsou připojené virtuální počítače, provádět NAT.

V případě interního vyrovnávání zatížení se první požadavek pošle a zpracovává pomocí MUX, který vybere odpovídající DIP a pak přesměruje provoz do DIP. Od tohoto okamžiku předem zavedený tok přenosů obchází MUX a přejde přímo z virtuálního počítače na virtuální počítač.

### <a name="health-probes"></a>Sondy stavu

Software Load Balancer zahrnuje sondy stavu pro ověření stavu síťové infrastruktury, včetně následujících:

- Test TCP na port

- Test HTTP na port a adresu URL

Na rozdíl od tradičních zařízení pro vyrovnávání zatížení, kde sonda vzchází na zařízení a přenáší do sítě DIP, vychází sonda SLB v hostiteli, kde se nachází DIP, a přímo z agenta hostitele SLB do DIP, dále distribuuje práci mezi hostiteli.

## <a name="software-load-balancer-infrastructure"></a><a name="bkmk_infrastructure"></a>Infrastruktura Load Balancer softwaru
Než budete moct nakonfigurovat Load Balancer softwaru, musíte nejdřív nasadit síťový adaptér a jeden nebo víc virtuálních počítačů SLB MUX.

Kromě toho musíte nakonfigurovat Azure Stack hostitele HCI s virtuálním přepínačem Hyper-V s povoleným SDN a ujistit se, že je agent hostitele SLB spuštěný. Směrovače, které obsluhují hostitele, musí podporovat směrování ECMP a Border Gateway Protocol (BGP) a musí být nakonfigurované tak, aby přijímaly požadavky na partnerský vztah protokolu BGP z MUX SLB.

Následující obrázek poskytuje přehled infrastruktury SLB.

:::image type="content" source="media/software-load-balancing/slb-overview.png" alt-text="Infrastruktura Load Balancer softwaru" border="false":::

Následující části poskytují další informace o těchto prvcích infrastruktury Load Balancer softwaru.

### <a name="network-controller"></a>Síťový adaptér
Síťový adaptér je hostitelem Správce služby SLB a provádí následující akce pro Load Balancer softwaru:

- Procesy SLB, které se přidávají prostřednictvím rozhraní Northbound API z centra pro správu Windows, nástroje System Center, Windows PowerShell nebo jiné aplikace pro správu sítě.

- Vypočítá zásadu pro distribuci Azure Stack hostitelé HCI a SLB MUX.

- Poskytuje stav infrastruktury Load Balancer softwaru.

K instalaci a konfiguraci síťového adaptéru a jiné infrastruktury SLB můžete použít Centrum pro správu Windows nebo Windows PowerShell.

### <a name="slb-mux"></a>SLB MUX
SLB MUX zpracovává příchozí síťový provoz a mapuje VIP na DIP a pak přepošle provoz na správnou DIP. Každé MUX používá protokol BGP také k publikování tras VIP k hraničním směrovačům. Keep Alive protokolu BGP upozorní MUX, když MUX selže, což umožňuje aktivnímu MUX opětovné distribuci zatížení v případě selhání MUX. To v podstatě poskytuje vyrovnávání zatížení pro nástroje pro vyrovnávání zatížení.

### <a name="slb-host-agent"></a>Agent hostitele SLB
Když nasadíte Load Balancer softwaru, musíte použít Centrum pro správu Windows, System Center, Windows PowerShell nebo jinou aplikaci pro správu k nasazení agenta hostitele SLB na každý hostitelský server.

Agent hostitele SLB čeká na aktualizace zásad SLB ze síťového adaptéru. Kromě toho hostitelský agent konfiguruje pravidla pro SLB do virtuálních přepínačů Hyper-V s povolenou technologií SDN nakonfigurovaných v místním počítači.

### <a name="sdn-enabled-hyper-v-virtual-switch"></a>Virtuální přepínač Hyper-V s povoleným SDN
Aby byl virtuální přepínač kompatibilní s SLB, musí být ve virtuálním přepínači povolený rozšíření VFP (Virtual Filtering Platform). To je prováděno automaticky pomocí skriptů PowerShell nasazení SDN, Průvodce nasazením centra pro správu systému Windows a nasazení System Center Virtual Machine Manager (SCVMM).

Informace o povolení VFP na virtuálních přepínačích najdete v tématu příkazy Windows PowerShellu [Get-VMSystemSwitchExtension](/powershell/module/hyper-v/get-vmsystemswitchextension?view=win10-ps) a [Enable-VMSwitchExtension](/powershell/module/hyper-v/enable-vmswitchextension?f=255&MSPPError=-2147217396&view=win10-ps).

Virtuální přepínač technologie Hyper-V s povoleným SDN provádí následující akce pro SLB:

- Zpracuje cestu dat pro SLB.

- Přijímá příchozí síťový provoz od MUX.

- Obchází MUX pro odchozí síťový provoz a odesílá ho do směrovače pomocí DSR.

### <a name="bgp-router"></a>Směrovač protokolu BGP
Směrovač protokolu BGP provádí následující akce Load Balancer softwaru:

- Směruje příchozí provoz do MUX pomocí ECMP.

- U odchozího síťového provozu používá trasu poskytnutou hostitelem.

- Naslouchá aktualizacím VIP pro virtuální IP adresy z SLB MUX.

- Odebere SLB MUX z rotace SLB, pokud se Keep Alive nezdařila.

## <a name="software-load-balancer-features"></a><a name="bkmk_features"></a>Funkce Load Balancer softwaru
Následující části popisují některé funkce a možnosti Load Balancer softwaru.

### <a name="core-functionality"></a>Základní funkce

- SLB poskytuje služby Vyrovnávání zatížení vrstvy 4 pro přenosy v oblasti sever/jih a na východ a západ TCP/UDP.

- SLB můžete použít v síti založené na virtualizaci sítě Hyper-V.

- Můžete použít SLB se sítí VLAN pro virtuální počítače, které jsou připojené k virtuálnímu přepínači Hyper-V s podporou SDN.

- Jedna instance SLB může zpracovávat více tenantů.

- SLB a DIP podporují zpáteční cestu s nízkou latencí, která je implementovaná přes DSR.

- SLB Functions, když používáte také rozhraní SR-IOV (Switch Embedded Teaming) nebo single root Input/Output Virtualization (SR-IOV).

- SLB zahrnuje podporu Internet Protocol verze 6 (IPv6) a verze 4 (IPv4).

- Pro scénáře brány typu Site-to-site poskytuje SLB funkce překladu adres (NAT), která umožňuje všem připojením typu Site-to-site využívat jednu veřejnou IP adresu.

### <a name="scale-and-performance"></a>Škálování a výkon

- Připravené na škálování v cloudu, včetně možností škálování na více instancí a škálování pro MUX a agenty hostitele.

- Jeden aktivní modul síťového adaptéru nástroje SLB Manager může podporovat osm instancí MUX.

### <a name="high-availability"></a>Vysoká dostupnost

- Službu SLB můžete nasadit do více než dvou uzlů v konfiguraci aktivní/aktivní.

- MUX je možné přidat a odebrat z fondu instancí MUX, aniž by to ovlivnilo službu SLB. Tím se zachová dostupnost SLB při opravách jednotlivých MUX.

- Jednotlivé instance MUX mají dobu provozu 99 procent.

- Data monitorování stavu jsou k dispozici pro entity správy.

## <a name="next-steps"></a>Další kroky

Související informace najdete v tématu také:

- [Přehled síťového adaptéru](network-controller-overview.md)
- [Nasazení síťového adaptéru pomocí Windows PowerShellu](https://github.com/microsoft/SDN/tree/master/SDNExpress/scripts)
- [SDN ve Azure Stack HCI](software-defined-networking.md)