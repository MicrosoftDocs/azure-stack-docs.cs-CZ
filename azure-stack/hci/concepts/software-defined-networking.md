---
title: Softwarově definované sítě (SDN) v Azure Stack HCI
description: Přehled témat SDN, která se vztahují na funkce v Azure Stack HCI
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 09/24/2020
ms.openlocfilehash: 34ddaf17a9c90373d76a9b0ce490e2e60a1e221d
ms.sourcegitcommit: 034e61836038ca75199a0180337257189601cd12
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91230474"
---
# <a name="sdn-in-azure-stack-hci"></a>SDN ve Azure Stack HCI

> Platí pro Azure Stack HCI, verze 20H2; Windows Server 2019

Softwarově definované sítě (SDN) poskytují způsob, jak centrálně konfigurovat a spravovat sítě a síťové služby, jako je přepínání, směrování a vyrovnávání zatížení ve vašem datovém centru. Pomocí SDN můžete dynamicky vytvářet, zabezpečovat a připojovat síť, aby splňovala požadavky vašich aplikací na vývoj. Provozování celosvětově škálovatelných Datacenter pro služby, jako je Microsoft Azure, což efektivně provádí desítky tisíců síťových změn každý den, je možné jenom z důvodu SDN.

Prvky virtuální sítě, jako je [virtuální přepínač Hyper-v](/windows-server/virtualization/hyper-v-virtual-switch/hyper-v-virtual-switch), [virtualizace sítě Hyper-v](/windows-server/networking/sdn/technologies/hyper-v-network-virtualization/hyper-v-network-virtualization), [Vyrovnávání zatížení softwaru](/windows-server/networking/sdn/technologies/network-function-virtualization/software-load-balancing-for-sdn)a [Brána služby RAS](/windows-server/networking/sdn/technologies/network-function-virtualization/ras-gateway-for-sdn) , jsou navržené tak, aby byly integrálními prvky vaší infrastruktury Sdn. Můžete také použít stávající zařízení kompatibilní s technologií SDN a dosáhnout tak hlubší integrace mezi úlohami spuštěnými ve virtuálních sítích a fyzickou sítí.

Existují tři hlavní komponenty SDN na Azure Stack HCI a můžete zvolit, který chcete nasadit: síťový adaptér, Load Balancer softwaru a bránu.

## <a name="network-controller"></a>Síťový adaptér

[Síťový adaptér](/windows-server/networking/sdn/technologies/Software-Defined-Networking-Technologies#network-controller) poskytuje centralizovaný, programovatelný bod automatizace pro správu, konfiguraci, monitorování a odstraňování potíží infrastruktury virtuální sítě ve vašem datovém centru. Je to vysoce škálovatelná role serveru, která používá Service Fabric k zajištění vysoké dostupnosti. Síťový adaptér musí být nasazený na svých vlastních vyhrazených virtuálních počítačích.

Nasazení síťového adaptéru umožňuje následující funkce:

- Vytváření a správa virtuálních sítí a podsítí. Připojte virtuální počítače k virtuálním podsítím.
- Konfigurace a Správa mikrosegmentace pro virtuální počítače připojené k virtuální síti nebo k tradiční síti založené na síti VLAN.
- Připojte virtuální zařízení k virtuálním sítím.
- Nakonfigurujte zásady QoS (Quality of Service) pro virtuální počítače připojené k virtuální síti nebo k tradiční síti založené na síti VLAN.

Po vytvoření clusteru Azure Stack HCI doporučujeme [nasadit síťový adaptér pomocí prostředí PowerShell](../deploy/network-controller-powershell.md) .

## <a name="software-load-balancing"></a>Vyrovnávání zatížení softwaru

[Vyrovnávání zatížení softwaru](/windows-server/networking/sdn/technologies/network-function-virtualization/software-load-balancing-for-sdn) (SLB) se dá použít k rovnoměrné distribuci síťového provozu zákazníka mezi více virtuálních počítačů. Umožňuje více serverům hostovat stejnou úlohu a poskytovat vysokou dostupnost a škálovatelnost. SLB používá [Border Gateway Protocol](/windows-server/remote/remote-access/bgp/border-gateway-protocol-bgp) k inzerování virtuálních IP adres do fyzické sítě.

## <a name="gateway"></a>brána

Brány se používají ke směrování síťového provozu mezi virtuální sítí a jinou sítí, a to buď místně, nebo vzdáleně. Brány můžete použít k těmto akcím:

- Vytvořte zabezpečená připojení Site-to-site IPsec mezi virtuálními sítěmi SDN a externími zákaznickými sítěmi přes Internet.
- Vytvořte připojení GRE (Generic Routing Encapsulation) mezi virtuálními sítěmi SDN a externími sítěmi. Rozdíl mezi připojeními mezi lokalitami a propojeními GRE je takový, že se nejedná o šifrované připojení. Další informace o scénářích připojení GRE najdete [v tématu tunelování GRE ve Windows serveru](/windows-server/remote/remote-access/ras-gateway/gre-tunneling-windows-server).
- Vytvořte připojení vrstvy 3 mezi virtuálními sítěmi SDN a externími sítěmi. V takovém případě brána SDN jednoduše funguje jako směrovač mezi vaší virtuální sítí a externí sítí.

Brány používají [Border Gateway Protocol](/windows-server/remote/remote-access/bgp/border-gateway-protocol-bgp) k inzerování koncových bodů GRE a k vytváření připojení Point-to-Point. Nasazení SDN vytvoří výchozí fond bran, který podporuje všechny typy připojení. V rámci tohoto fondu můžete zadat, kolik bran je v pohotovostním režimu vyhrazené pro případ, že se aktivní brána nezdařila.

## <a name="next-steps"></a>Další kroky

Související informace najdete v tématu také:

- [Plánování infrastruktury softwarově definované sítě](plan-software-defined-networking-infrastructure.md)
- [SDN v systému Windows Server – přehled](/windows-server/networking/sdn/software-defined-networking)
- [Nasazení infrastruktury softwarově definovaných sítí pomocí skriptů](/windows-server/networking/sdn/deploy/deploy-a-software-defined-network-infrastructure-using-scripts)
