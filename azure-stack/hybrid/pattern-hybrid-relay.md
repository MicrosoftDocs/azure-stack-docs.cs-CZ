---
title: Vzor hybridního přenosu v Azure a centra Azure Stack
description: Pomocí vzoru hybridního přenosu v Azure a centra Azure Stack se připojte k hraničním prostředkům chráněným branami firewall.
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: abb4f2b779afb7450c04b98d1d6b02d824446b09
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "80891004"
---
# <a name="hybrid-relay-pattern"></a>Vzor hybridního přenosu

Naučte se, jak se připojit k hraničním prostředkům nebo zařízením chráněným branami firewall pomocí modelu hybridního přenosu a Azure Service Bus Relay.

## <a name="context-and-problem"></a>Kontext a problém

Hraniční zařízení jsou často za podnikovou bránou firewall nebo zařízením NAT. I když jsou zabezpečené, nemusí být schopni komunikovat s veřejným cloudem nebo hraničními zařízeními v jiných podnikových sítích. Může být nutné zajistit zabezpečený způsob vystavení určitých portů a funkcí uživatelům ve veřejném cloudu.

## <a name="solution"></a>Řešení

Vzor hybridního přenosu používá Azure Service Bus Relay k vytvoření tunelu WebSockets mezi dvěma koncovými body, které nemůžou přímo komunikovat. Zařízení, která nejsou místní, ale potřebují se připojit k místnímu koncovému bodu, se připojí ke koncovému bodu ve veřejném cloudu. Tento koncový bod přesměruje provoz v předdefinovaných trasách přes zabezpečený kanál. Koncový bod v místním prostředí obdrží provoz a směruje ho do správného cíle.

![Architektura řešení vzorů hybridního přenosu](media/pattern-hybrid-relay/solution-architecture.png)

Tady je postup, jak funguje vzor hybridního přenosu:

1. Zařízení se připojí k virtuálnímu počítači (VM) v Azure na předdefinovaném portu.
2. Provoz se přenáší do Service Bus Relay v Azure.
3. Virtuální počítač na rozbočovači Azure Stack, který už navázal dlouhodobé připojení k Service Bus Relay, přijme provoz a předá ho k cíli.
4. Místní služba nebo koncový bod zpracovává požadavek.

## <a name="components"></a>Komponenty

Toto řešení používá následující komponenty:

| Vrstva | Součást | Popis |
|----------|-----------|-------------|
| Azure | Virtuální počítač Azure | Virtuální počítač Azure poskytuje veřejně přístupný koncový bod pro místní prostředek. |
| | Azure Service Bus Relay | [Azure Service Bus Relay](/azure/service-bus-relay/) poskytuje infrastrukturu pro zachovávání tunelu a připojení mezi virtuálním počítačem Azure a virtuálním počítačem centra Azure Stack.|
| Centrum Azure Stack | Compute | Virtuální počítač centra Azure Stack poskytuje na straně serveru tunelové propojení hybridního přenosu. |
| | Storage | Cluster modulu AKS nasazený do centra Azure Stack poskytuje škálovatelný a odolný modul pro spuštění kontejneru Face API.|

## <a name="issues-and-considerations"></a>Problémy a důležité informace

Při rozhodování, jak implementovat toto řešení, vezměte v úvahu následující body:

### <a name="scalability"></a>Škálovatelnost

Tento model umožňuje pouze mapování portů 1:1 na straně klienta a serveru. Pokud je například port 80 tunelování pro jednu službu na koncovém bodu Azure, nelze ji použít pro jinou službu. Mapování portů by mělo být naplánováno odpovídajícím způsobem. Přenos Service Bus a virtuální počítače by měly být vhodně škálované na zpracování provozu.

### <a name="availability"></a>Dostupnost

Tato tunelová propojení a připojení nejsou redundantní. Abyste zajistili vysokou dostupnost, možná budete chtít implementovat kód kontroly chyb. Další možností je mít fond Service Bus virtuálních počítačů připojených k předávání za nástroj pro vyrovnávání zatížení.

### <a name="manageability"></a>Možnosti správy

Toto řešení může zahrnovat mnoho zařízení a umístění, což by mohlo mít nepraktický. Služby IoT v Azure můžou automaticky přenést nová umístění a zařízení do režimu online a udržovat je v aktuálním stavu.

### <a name="security"></a>Zabezpečení

Tento model, jak ukazuje, umožňuje neomezený přístup k portu na interním zařízení z Edge. Zvažte přidání mechanismu ověřování ke službě na interním zařízení nebo před koncový bod hybridního přenosu.

## <a name="next-steps"></a>Další kroky

Další informace o tématech zavedených v tomto článku:

- Tento model používá Azure Service Bus Relay. Další informace najdete v dokumentaci ke službě [Azure Service Bus Relay](/azure/service-bus-relay/).
- Další informace o osvědčených postupech najdete v tématu [aspekty návrhu hybridní aplikace](overview-app-design-considerations.md) a Získejte odpovědi na další otázky.
- Další informace o celém portfoliu produktů a řešení najdete v [Azure Stack rodině produktů a řešení](/azure-stack) .

Až budete připraveni otestovat příklad řešení, pokračujte v [Průvodci nasazením řešení hybridního přenosu](https://aka.ms/hybridrelaydeployment). Průvodce nasazením poskytuje podrobné pokyny pro nasazení a testování jeho komponent.