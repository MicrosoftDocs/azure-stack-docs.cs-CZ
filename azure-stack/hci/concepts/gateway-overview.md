---
title: Přehled brány RAS v Azure Stack HCI
description: V tomto tématu se dozvíte o bráně RAS pro softwarově definované sítě v Azure Stack HCI.
author: khdownie
ms.author: v-kedow
ms.topic: overview
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 10/8/2020
ms.openlocfilehash: a7f05f7e07960e83681a13c92f4653b0993668de
ms.sourcegitcommit: 1621f2748b2059fd47ccacd48595a597c44ee63f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2020
ms.locfileid: "91858889"
---
# <a name="what-is-ras-gateway-for-software-defined-networking"></a>Co je brána služby RAS pro softwarově definované sítě?

> Platí pro Azure Stack HCI, verze 20H2; Windows Server 2019

Brána služby Remote Access Service (RAS) je směrovač podporující protokol BGP (software-based Border Gateway Protocol), který je určený pro poskytovatele cloudových služeb (CSP) a podniky hostující virtuální sítě s více klienty pomocí virtualizace sítě Hyper-V (HNV). Bránu RAS můžete použít ke směrování síťového provozu mezi virtuální sítí a jinou sítí, a to buď místně, nebo vzdáleně.

Brána RAS vyžaduje [síťový adaptér](network-controller-overview.md), který provádí nasazení fondů bran, nakonfiguruje připojení klientů na každé bráně a přepne síťový provoz do pohotovostní brány v případě selhání brány.

  > [!NOTE]
  > Víceklientská architektura je schopnost cloudové infrastruktury podporovat úlohy virtuálních počítačů (VM) více tenantů, vzájemně je izolovat, zatímco všechny úlohy běží na stejné infrastruktuře. Několik úloh jednoho klienta je možné vzájemně propojit a spravovat vzdáleně, ale tyto systémy nevytvářejí propojení s úlohami ostatních klientů, ani ostatní klienti tyto úlohy nemůžou vzdáleně spravovat.

## <a name="ras-gateway-features"></a>Funkce služby RAS Gateway

Služba RAS Gateway nabízí řadu funkcí pro virtuální privátní síť (VPN), tunelové propojení, předávání a dynamické směrování.

### <a name="site-to-site-ipsec-vpn"></a>Síť Site-to-site IPsec VPN

Tato funkce brány RAS umožňuje propojit dvě sítě na různých fyzických místech napříč internetem pomocí připojení virtuální privátní sítě (VPN) typu Site-to-Site (S2S). Jedná se o šifrované připojení pomocí protokolu IKEv2 VPN.

Pro zprostředkovatele CSP, kteří hostují mnoho tenantů ve svém datacentru, poskytuje brána RAS řešení víceklientské brány, které umožňuje klientům přístup k prostředkům a jejich správě přes připojení VPN typu Site-to-site ze vzdálených lokalit. Brána RAS umožňuje tok provozu mezi virtuálními prostředky v datovém centru a jejich fyzickou sítí.

### <a name="site-to-site-gre-tunnels"></a>Tunely GRE typu Site-to-site

Tunely založené na protokolu GRE (Generic Routing Encapsulation) umožňují připojení mezi virtuálními a externími sítěmi klientů. Vzhledem k tomu, že protokol GRE je odlehčený a podpora protokolu GRE je dostupná na většině síťových zařízení, je ideální volbou pro tunelování, u kterého není nutné šifrovat data.

Podpora GRE v tunelech S2S řeší problém přesměrování mezi klientskými virtuálními sítěmi a externími sítěmi klientů pomocí víceklientské brány.

### <a name="layer-3-forwarding"></a>Přesměrování vrstvy 3

Přesměrování vrstvy 3 (L3) umožňuje propojení mezi fyzickou infrastrukturou v datovém centru a virtualizované infrastruktury v cloudu virtualizace sítě Hyper-V. Pomocí připojení pro předávání L3 se virtuální počítače klientské sítě můžou připojit k fyzické síti přes bránu SDN, která je už nakonfigurovaná v prostředí softwarově definované sítě (SDN). V takovém případě brána SDN funguje jako směrovač mezi virtualizované sítě a fyzickou sítí.

### <a name="dynamic-routing-with-bgp"></a>Dynamické směrování s protokolem BGP

Protokol BGP omezuje potřebu ruční konfiguraci směrování ve směrovačích, protože se jedná o dynamický směrovací protokol, který automaticky zjišťuje směrování mezi lokalitami propojenými pomocí připojení VPN typu site-to-site. Pokud má vaše organizace více lokalit, které jsou připojené pomocí směrovačů s povoleným protokolem BGP, jako je třeba brána RAS, umožňuje směrovačům automaticky vypočítat a používat platné trasy v případě přerušení nebo selhání sítě.

Reflektor trasy protokolu BGP zahrnutý v bráně RAS nabízí alternativu k topologii celé sítě protokolu BGP, která je nutná pro synchronizaci směrování mezi směrovači. Další informace najdete v tématu [co je reflektor směrování](route-reflector-overview.md) .

## <a name="how-ras-gateway-works"></a>Jak funguje brána služby RAS

Brána RAS směruje síťový provoz mezi fyzickou sítí a síťovými prostředky virtuálních počítačů bez ohledu na jejich umístění. Síťový provoz můžete směrovat ve stejném fyzickém umístění nebo v mnoha různých umístěních.

Bránu RAS můžete nasadit v fondech s vysokou dostupností, které používají více funkcí najednou. Fondy brány obsahují více instancí brány RAS pro zajištění vysoké dostupnosti a převzetí služeb při selhání.

Fond brány můžete snadno škálovat směrem nahoru nebo dolů přidáním nebo odebráním virtuálních počítačů brány ve fondu. Odebrání nebo přidání bran neruší služby poskytované fondem. Můžete také přidat a odebrat celé fondy bran. Další informace najdete v tématu [Vysoká dostupnost brány RAS](/windows-server/networking/sdn/technologies/network-function-virtualization/ras-gateway-high-availability).

Každý fond bran poskytuje redundanci M + N. To znamená, že "počet virtuálních počítačů aktivních bran se zálohuje" N "v pohotovostním počtu virtuálních počítačů brány. Redundance M + N zajišťuje větší flexibilitu při určování úrovně spolehlivosti, kterou požadujete při nasazení brány RAS.

Jednu veřejnou IP adresu můžete přiřadit ke všem fondům nebo k podmnožině fondů. Tím se výrazně sníží počet veřejných IP adres, které musíte použít, protože je možné mít všechny klienty připojení ke cloudu na jedné IP adrese.

## <a name="next-steps"></a>Další kroky

Související informace najdete v tématu také:

- [Architektura nasazení brány služby RAS](/windows-server/networking/sdn/technologies/network-function-virtualization/ras-gateway-deployment-architecture)
- [Přehled síťového adaptéru](network-controller-overview.md)
- [Plánování nasazení síťového adaptéru](network-controller.md)
- [SDN ve Azure Stack HCI](software-defined-networking.md)