---
title: Model geografické distribuované aplikace v centru Azure Stack
description: Přečtěte si o modelu geograficky distribuovaných aplikací pro inteligentní Edge pomocí Azure a centra Azure Stack.
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod2019
ms.openlocfilehash: 1f6243927390c7a520c2607c722664b2d31fc07f
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "80891027"
---
# <a name="geo-distributed-app-pattern"></a>Model geograficky distribuované aplikace

Naučte se poskytovat koncové body aplikací napříč několika oblastmi a směrovat provoz uživatelů na základě umístění a požadavků na dodržování předpisů.

## <a name="context-and-problem"></a>Kontext a problém

Organizace, které mají rozsáhlou škálu geografických oblastí, se snaží bezpečně a přesně distribuovat a umožňují přístup k datům a současně zajišťují požadavky na zabezpečení, dodržování předpisů a výkon na uživatele, umístění a zařízení přes hranice.

## <a name="solution"></a>Řešení

Model směrování geografického provozu Azure Stack centra nebo geografické distribuované aplikace umožňuje směrovat provoz na konkrétní koncové body na základě různých metrik. Vytvoření Traffic Manager s využitím geograficky směrování a konfigurace koncového bodu směruje provoz do koncových bodů na základě regionálních požadavků, podnikových a mezinárodních předpisů a datových potřeb.

![Geograficky distribuovaný model](media/pattern-geo-distributed/geo-distribution.png)

## <a name="components"></a>Komponenty

### <a name="outside-the-cloud"></a>Mimo Cloud

#### <a name="traffic-manager"></a>Traffic Manager

V diagramu se Traffic Manager nachází mimo veřejný cloud, ale musí být schopný koordinovat provoz v místním datacentru i ve veřejném cloudu. Nástroj pro vyrovnávání zatížení směruje provoz do geografických umístění.

#### <a name="domain-name-system-dns"></a>DNS (Domain Name System)

Název domény systému nebo DNS zodpovídá za překlad (nebo překladu) názvu webu nebo služby na jeho IP adresu.

### <a name="public-cloud"></a>Veřejný cloud

#### <a name="cloud-endpoint"></a>Koncový bod cloudu

Veřejné IP adresy se používají ke směrování příchozího provozu prostřednictvím Traffic Manageru do koncového bodu prostředků cloudové aplikace.  

### <a name="local-clouds"></a>Místní cloudy

#### <a name="local-endpoint"></a>Místní koncový bod

Veřejné IP adresy se používají ke směrování příchozího provozu prostřednictvím Traffic Manageru do koncového bodu prostředků cloudové aplikace.

## <a name="issues-and-considerations"></a>Problémy a důležité informace

Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:

### <a name="scalability"></a>Škálovatelnost

Tento model zpracovává směr geografického provozu, nikoli škálování pro zvýšení provozu. Tento model však můžete zkombinovat s jinými řešeními Azure a místními řešeními. Tento model se dá použít například se vzorem škálování mezi cloudy.

### <a name="availability"></a>Dostupnost

Zajistěte, aby lokálně nasazené aplikace byly nakonfigurované pro vysokou dostupnost prostřednictvím místní konfigurace hardwaru a nasazení softwaru.

### <a name="manageability"></a>Možnosti správy

Vzor zajišťuje bezproblémové řízení a známé rozhraní mezi prostředími.

## <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít

- Moje organizace má mezinárodní pobočky vyžadující vlastní regionální zásady zabezpečení a distribuce.
- Každá z kanceláří mojí organizace si vyžádá data o zaměstnancích, firmách a obchodních přístavech a vyžaduje, aby se na základě místních předpisů a časového pásma vycházely
- Vysoce škálovatelné požadavky můžou být splněné horizontálním škálováním aplikací, přičemž v jedné oblasti a v různých oblastech se provádí nasazení s více aplikacemi, aby se mohly zvládnout extrémní požadavky na zatížení.
- Aplikace musí být vysoce dostupné a reagovat na požadavky klientů i v případě výpadků v jedné oblasti.

## <a name="next-steps"></a>Další kroky

Další informace o tématech zavedených v tomto článku:

- Další informace o tom, jak tento nástroj pro vyrovnávání zatížení využívající službu DNS funguje, najdete v tématu [Přehled Azure Traffic Manager](/azure/traffic-manager/traffic-manager-overview) .
- Další informace o osvědčených postupech a získání odpovědí na případné další otázky najdete v tématu [aspekty návrhu hybridní aplikace](overview-app-design-considerations.md) .
- Další informace o celém portfoliu produktů a řešení najdete v [Azure Stack rodině produktů a řešení](/azure-stack) .

Až budete připraveni otestovat příklad řešení, pokračujte pomocí [Průvodce nasazením geograficky distribuovaných aplikací](solution-deployment-guide-geo-distributed.md). Průvodce nasazením poskytuje podrobné pokyny pro nasazení a testování jeho komponent. Naučíte se, jak směrovat provoz do konkrétních koncových bodů na základě různých metrik pomocí vzoru geograficky distribuované aplikace. Když vytvoříte profil Traffic Manager s využitím geografického směrování a konfigurace koncového bodu, zajistíte směrování informací na koncové body na základě regionálních požadavků, podnikových a mezinárodních předpisů a vašich datových potřeb.
