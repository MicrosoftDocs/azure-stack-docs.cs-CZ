---
title: Transparentní proxy server pro integrované systémy Azure Stack hub
description: Přehled transparentní vlastnosti v systémech integrovaných na rozbočovači Azure Stack.
author: PatAltimore
ms.topic: conceptual
ms.date: 02/24/2021
ms.author: patricka
ms.reviewer: sranthar
ms.lastreviewed: 01/25/2021
ms.openlocfilehash: a7fc47edf63a83e1ee05c46b03d8533787b1983c
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "101840690"
---
# <a name="transparent-proxy-for-azure-stack-hub"></a>Transparentní proxy server pro centrum Azure Stack

Transparentní proxy server (označovaný také jako zachycení, vložený nebo vynucený proxy server) zachycuje normální komunikaci v síťové vrstvě bez nutnosti speciální konfigurace klienta. Klienti nemusí vědět o existenci proxy serveru.

Pokud vaše datacentrum vyžaduje, aby všechny přenosy používaly proxy, nakonfigurujete transparentní proxy server tak, aby zpracovával veškerou komunikaci podle zásad tím, že odděluje přenos mezi zónami v síti.

## <a name="traffic-types"></a>Typy provozu

Odchozí přenosy z centra Azure Stack se kategorizují jako přenosy dat klientů nebo infrastruktury.

Klienti vygenerují přenosy klientů prostřednictvím virtuálních počítačů, nástrojů pro vyrovnávání zatížení, bran sítě VPN, služby App Services atd.

Provoz infrastruktury se generuje z prvního `/27` rozsahu veřejného virtuálního IP fondu přiřazeného ke službám infrastruktury, jako je identita, oprava a aktualizace, metriky využití, syndikace Marketplace, registrace, shromažďování protokolů, program Windows Defender atd. Provoz z těchto služeb se směruje do [koncových bodů Azure](azure-stack-integrate-endpoints.md#ports-and-urls-outbound). Azure nepřijímá provoz změněný proxy serverem nebo zachyceným provozem protokolu TLS/SSL. Z tohoto důvodu Azure Stack centrum nepodporuje konfiguraci nativního proxy serveru.

Při konfiguraci transparentního proxy serveru můžete zvolit odesílání všech odchozích přenosů dat nebo jenom provozu infrastruktury prostřednictvím proxy serveru.

## <a name="partner-integration"></a>Integrace partnerských řešení

Společnost Microsoft spolupracuje s předními dodavateli proxy v oboru za účelem ověření scénářů použití centra Azure Stack s transparentní konfigurací proxy serveru. Následující diagram je příklad Azure Stack konfigurace sítě centra s proxy HA. Externí proxy zařízení musí být umístěna na sever od hraničních zařízení.

![Diagram sítě se serverem proxy před hraničními zařízeními](./media/azure-stack-transparent-proxy/proxy.svg)

 Kromě toho musí být hraniční zařízení nakonfigurovaná tak, aby směrovala provoz z centra Azure Stack jedním z následujících způsobů:
- Směrování veškerého odchozího provozu z centra Azure Stack do proxy zařízení
- Směrování všech odchozích přenosů z prvního `/27` rozsahu fondu IP adres centra Azure Stack do proxy zařízení prostřednictvím směrování založeného na zásadách.  

Konfiguraci vzorku ohraničení najdete v části [příklad konfigurace ohraničení](#example-border-configuration) v tomto článku.

Projděte si následující dokumenty pro ověřování transparentních proxy konfigurací pomocí centra Azure Stack:

- [Konfigurace transparentního proxy brány zabezpečení kontrolního bodu](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk171559)
- [Konfigurace transparentního proxy serveru Sophos XG firewallu](https://community.sophos.com/xg-firewall/f/recommended-reads/124106/xg-firewall-integration-with-azure-stack-hub)
- [Integrace Citrix ADC, Citrix Secure Web Gateway pomocí centra Azure Stack](https://www.citrix.com/blogs/2021/02/19/integrating-citrix-adc-citrix-secure-web-gateway-with-azure-stack-hub/)

Ve scénářích, kdy se při průchodu explicitního proxy serveru vyžaduje odchozí přenos z centra Azure Stack, poskytuje zařízení s funkcí Sophos a Checkpoint funkci s duálním režimem, která umožňuje konkrétní rozsah provozu prostřednictvím transparentního režimu, zatímco jiné rozsahy je možné nakonfigurovat tak, aby prošly explicitním režimem. Pomocí této funkce je možné nakonfigurovat tato proxy zařízení tak, aby se prostřednictvím transparentního proxy serveru odesílaly jenom přenosy infrastruktury, zatímco veškerý provoz klienta se odesílá prostřednictvím explicitního režimu.

> [!IMPORTANT]
> Zachycení provozu SSL není podporované a může při přístupu k koncovým bodům způsobit selhání služby. Maximální podporovaný časový limit pro komunikaci s koncovými body vyžadovanými pro identitu je 60 s se 3 opakovanými pokusy. Další informace najdete v tématu [Integrace brány firewall centra Azure Stack](azure-stack-firewall.md#ssl-interception).

## <a name="example-border-configuration"></a>Příklad konfigurace ohraničení

Řešení je založené na směrování na základě zásad (PBR), které používá sadu kritérií, která implementuje seznam řízení přístupu (ACL). Seznam ACL zařadí provoz, který je směrován na IP adresu dalšího směrování zařízení proxy, implementovaných na mapě tras, místo normálního směrování, které je založené pouze na cílové IP adrese. Konkrétní síťový provoz infrastruktury pro porty 80 a 443 je směrován z hraničních zařízení do transparentního nasazení proxy serveru. Transparentní proxy server dělá filtrování adres URL a *žádný povolený* provoz je vyřazený.

Následující konfigurační ukázka je určena pro Cisco Nexus 9508 šasi.

V tomto scénáři jsou zdrojové sítě infrastruktury, které vyžadují přístup k Internetu, následující:

- Veřejná VIP – první/27
- Síť infrastruktury – posledních/27
- Síť BMC – posledních/27

V tomto scénáři jsou v těchto scénářích doručovány postupy směrování na základě zásad:

| Síť | Rozsah IP adres | Zpracování PBR pro příjem podsítí
|---------|----------|-------------------------------
| Veřejný virtuální IP fond | Prvních/27 172.21.107.0/27 | 172.21.107.0/27 = 172.21.107.1 na 172.21.107.30
| Síť infrastruktury | Posledních/27 172.21.7.0/24 | 172.21.7.224/27 = 172.21.7.225 na 172.21.7.254
| Síť řadiče pro správu základní desky | Posledních/27 10.60.32.128/26 | 10.60.32.160/27 = 10.60.32.161 na 10.60.32.190

### <a name="configure-border-device"></a>Konfigurace hraničního zařízení

Povolte PBR zadáním `feature pbr` příkazu. 

```
****************************************************************************
PBR Configuration for Cisco Nexus 9508 Chassis
PBR Enivronment configured to use VRF08
The test rack has is a 4-node Azure Stack stamp with 2x TOR switches and 1x BMC switch. Each TOR switch 
has a single uplink to the Nexus 9508 chassis using BGP for routing. In this example the test rack 
is in it's own VRF (VRF08)
****************************************************************************
!
feature pbr
!

<Create VLANs that the proxy devices will use for inside and outside connectivity>

!
VLAN 801
name PBR_Proxy_VRF08_Inside
VLAN 802
name PBR_Proxy_VRF08_Outside
!
interface vlan 801
description PBR_Proxy_VRF08_Inside
no shutdown
mtu 9216
vrf member VRF08
no ip redirects
ip address 10.60.3.1/29
!
interface vlan 802
description PBR_Proxy_VRF08_Outside
no shutdown
mtu 9216
vrf member VRF08
no ip redirects
ip address 10.60.3.33/28
!
!
ip access-list PERMITTED_TO_PROXY_ENV1
100 permit tcp 172.21.107.0/27 any eq www
110 permit tcp 172.21.107.0/27 any eq 443
120 permit tcp 172.21.7.224/27 any eq www
130 permit tcp 172.21.7.224/27 any eq 443
140 permit tcp 10.60.32.160/27 any eq www
150 permit tcp 10.60.32.160/27 any eq 443
!
!
route-map TRAFFIC_TO_PROXY_ENV1 pbr-statistics
route-map TRAFFIC_TO_PROXY_ENV1 permit 10
  match ip address PERMITTED_TO_PROXY_ENV1
  set ip next-hop 10.60.3.34 10.60.3.35
!
!
interface Ethernet1/1
  description DownLink to TOR-1:TeGig1/0/47
  mtu 9100
  logging event port link-status
  vrf member VRF08
  ip address 192.168.32.193/30
  ip policy route-map TRAFFIC_TO_PROXY_ENV1
  no shutdown
!
interface Ethernet2/1
  description DownLink to TOR-2:TeGig1/0/48
  mtu 9100
  logging event port link-status
  vrf member VRF08
  ip address 192.168.32.205/30
  ip policy route-map TRAFFIC_TO_PROXY_ENV1
  no shutdown
!

<Interface configuration for inside/outside connections to proxy devices. In this example there are 2 firewalls>

!
interface Ethernet1/41
  description management interface for Firewall-1
  switchport
  switchport access vlan 801
  no shutdown
!
interface Ethernet1/42
  description Proxy interface for Firewall-1
  switchport
  switchport access vlan 802
  no shutdown
!
interface Ethernet2/41
  description management interface for Firewall-2
  switchport
  switchport access vlan 801
  no shutdown
!
interface Ethernet2/42
  description Proxy interface for Firewall-2
  switchport
  switchport access vlan 802
  no shutdown
!

<BGP network statements for VLAN 801-802 subnets and neighbor statements for R023171A-TOR-1/R023171A-TOR-2> 

!
router bgp 65000
!
vrf VRF08
address-family ipv4 unicast
network 10.60.3.0/29
network 10.60.3.32/28
!
neighbor 192.168.32.194
  remote-as 65001
  description LinkTo 65001:R023171A-TOR-1:TeGig1/0/47
  address-family ipv4 unicast
    maximum-prefix 12000 warning-only
neighbor 192.168.32.206
  remote-as 65001
  description LinkTo 65001:R023171A-TOR-2:TeGig1/0/48
  address-family ipv4 unicast
    maximum-prefix 12000 warning-only
!
!
```

Vytvořte nový seznam ACL, který bude sloužit k identifikaci provozu, který bude mít za zpracování PBR. Tento provoz je webový provoz (port HTTP 80 a port HTTPS 443) z hostitelů nebo podsítí v testovacím stojanu, který získává službu proxy, jak je podrobně popsáno v tomto příkladu. Například název seznamu ACL je **PERMITTED_TO_PROXY_ENV1**.

```
ip access-list PERMITTED_TO_PROXY_ENV1
100 permit tcp 172.21.107.0/27 any eq www <<HTTP traffic from CL04 Public Admin VIPs leaving test rack>>
110 permit tcp 172.21.107.0/27 any eq 443 <<HTTPS traffic from CL04 Public Admin VIPs leaving test rack>>
120 permit tcp 172.21.7.224/27 any eq www <<HTTP traffic from CL04 INF-pub-adm leaving test rack>>
130 permit tcp 172.21.7.224/27 any eq 443 <<HTTPS traffic from CL04 INF-pub-adm leaving test rack>>
140 permit tcp 10.60.32.160/27 any eq www <<HTTP traffic from DVM and HLH leaving test rack>>
150 permit tcp 10.60.32.160/27 any eq 443 <<HTTPS traffic from DVM and HLH leaving test rack>>
```

Základem funkce PBR je implementace mapy tras **TRAFFIC_TO_PROXY_ENV1** . Přidá se možnost **PBR-STATISTICS** , která umožní zobrazit statistické údaje o shodě zásad a ověřit tak počet paketů, které dělají a nezíská předávání PBR. Pořadí mapy tras 10 povoluje zpracování PBR pro přenos seznamu ACL pro řízení přístupu **PERMITTED_TO_PROXY_ENV1** kritéria. Tento provoz se přesměruje na IP adresy dalšího směrování `10.60.3.34` a `10.60.3.35` , které jsou VIP pro primární a sekundární proxy zařízení v naší ukázkové konfiguraci.

```
!
route-map TRAFFIC_TO_PROXY_ENV1 pbr-statistics
route-map TRAFFIC_TO_PROXY_ENV1 permit 10
  match ip address PERMITTED_TO_PROXY_ENV1
  set ip next-hop 10.60.3.34 10.60.3.35
```

Seznamy řízení přístupu (ACL) se používají jako kritéria shody pro mapu **TRAFFIC_TO_PROXY_ENV1** tras. Když se provoz shoduje se seznamem ACL **PERMITTED_TO_PROXY_ENV1** , PBR přepíše normální směrovací tabulku a místo toho přesměruje provoz na uvedené IP adresy dalšího směrování.
 
Zásada **TRAFFIC_TO_PROXY_ENV1** PBR se aplikuje na provoz, který vstoupí do hraničního zařízení z hostitelů CL04 a veřejných virtuálních IP adres a z HLH a DVM v testovacím stojanu.

## <a name="next-steps"></a>Další kroky

Další informace o integraci brány firewall najdete v tématu [Integrace brány firewall centra Azure Stack](azure-stack-firewall.md).
