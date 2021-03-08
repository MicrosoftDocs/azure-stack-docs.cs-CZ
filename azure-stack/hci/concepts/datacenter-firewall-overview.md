---
title: Přehled brány firewall Datacenter v Azure Stack HCI a Windows Server
description: V tomto tématu se dozvíte, jak používat bránu firewall datového centra v Azure Stack HCI a Windows Server.
author: khdownie
ms.author: v-kedow
ms.topic: overview
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 03/05/2021
ms.openlocfilehash: 3761f0867995fe784987a76e1b04194dfae2b56b
ms.sourcegitcommit: 192701cd691d0f13ae65f19627bb8ae7fc81bbc9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2021
ms.locfileid: "102451281"
---
# <a name="what-is-datacenter-firewall"></a>Co je datacentrum firewall?

> Platí pro: Azure Stack HCI, verze 20H2; Windows Server 2019; Windows Server 2016

Firewall Datacenter je síťová vrstva, 5 – n-tice (protokol, čísla zdrojového a cílového portu, zdrojová a cílová IP adresa), stavová, brána firewall pro více tenantů softwarově definované sítě (SDN). Brána firewall Datacenter chrání datové toky východní i západní a Jižní sítě napříč síťovou vrstvou virtuálních sítí a tradičními sítěmi VLAN.

## <a name="how-datacenter-firewall-works"></a>Jak funguje firewall Datacenter

Povolíte a nakonfigurujete bránu firewall Datacenter tím, že vytvoříte seznamy řízení přístupu (ACL), které se aplikují na podsíť nebo síťové rozhraní. Zásady brány firewall se vystavují na portu virtuálních počítačů každého klientského virtuálního počítače. Zásady se přidávají prostřednictvím portálu tenanta a [síťový adaptér](network-controller-overview.md) je distribuuje na všechny příslušné hostitele.

Správci klientů mohou instalovat a konfigurovat zásady brány firewall, aby lépe chránily své sítě před nežádoucím provozem pocházejících z internetových a intranetových sítí.

:::image type="content" source="media/datacenter-firewall/multitenant-firewall-overview.png" alt-text="Firewall Datacenter v zásobníku sítě" border="false":::

Správce poskytovatele služeb nebo správce tenanta může spravovat zásady brány firewall Datacenter prostřednictvím síťového adaptéru a rozhraní Northbound API. Můžete taky nakonfigurovat a spravovat zásady brány firewall Datacenter pomocí centra pro správu Windows.

## <a name="advantages-for-cloud-service-providers"></a>Výhody pro poskytovatele cloudových služeb

Brána firewall Datacenter nabízí pro CSP následující výhody:

- Vysoce škálovatelné, spravovatelné a rozšiřitelné řešení brány firewall založené na softwaru, které se dá nabídnout klientům

- Volnost přesunu virtuálních počítačů tenantů do různých výpočetních hostitelů bez přerušení zásad brány firewall klienta

    - Nasazeno jako brána firewall agenta hostitele portu virtuálního přepínače

    - Virtuální počítače tenanta získávají zásady přiřazené k bráně firewall hostitele virtuálních přepínačů.

    - Pravidla brány firewall jsou nakonfigurovaná na každém portu virtuálních počítačů bez ohledu na samotného hostitele, na kterém běží virtuální počítač.

- Nabízí ochranu pro virtuální počítače tenanta nezávisle na hostovaném operačním systému klienta.

## <a name="advantages-for-tenants"></a>Výhody pro klienty

Brána firewall Datacenter nabízí pro klienty tyto výhody:

- Možnost definovat pravidla brány firewall pro usnadnění ochrany internetových a interních úloh v sítích

- Možnost definovat pravidla brány firewall pro lepší ochranu provozu mezi virtuálními počítači ve stejné podsíti vrstvy 2 (L2) a také mezi virtuálními počítači v různých podsítích L2

- Možnost definovat pravidla brány firewall pro lepší ochranu a izolaci síťového provozu mezi místními sítěmi klienta a jejich virtuálními sítěmi u poskytovatele služeb

- Možnost použít zásady brány firewall na tradiční sítě VLAN a na překryvných virtuálních sítích

## <a name="next-steps"></a>Další kroky

Související informace najdete v tématu také:

- [Použití brány firewall Datacenter ke konfiguraci seznamů ACL pomocí centra pro správu Windows](../manage/use-datacenter-firewall-windows-admin-center.md)
- [Použití brány firewall Datacenter ke konfiguraci seznamů ACL pomocí PowerShellu](../manage/use-datacenter-firewall-powershell.md)
- [SDN v Azure Stack HCI a Windows Server](software-defined-networking.md)