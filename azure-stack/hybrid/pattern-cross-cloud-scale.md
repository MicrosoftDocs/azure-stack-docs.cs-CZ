---
title: Vzor škálování mezi cloudy v Azure Stackovém centru
description: Naučte se vytvářet škálovatelné aplikace pro více cloudů v Azure a centra Azure Stack.
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: a830f96e97c347cbbcc09a1b17f4836ecb6eb3e6
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "80891023"
---
# <a name="cross-cloud-scaling-pattern"></a>Vzor škálování mezi cloudy

Automatické přidání prostředků do existující aplikace, aby se přizpůsobilo zvýšení zátěže.

## <a name="context-and-problem"></a>Kontext a problém

Vaše aplikace nemůže zvýšit kapacitu pro splnění neočekávaného zvýšení poptávky. Výsledkem tohoto nedostatku je to, že uživatelé nedosáhnou aplikace během špičky využití. Aplikace může obsluhovat pevný počet uživatelů.

Globální podniky vyžadují zabezpečené, spolehlivé a dostupné cloudové aplikace. Schůzka roste na vyžádání a používání správné infrastruktury pro podporu této poptávky je kritická. Firmy se bojovat na rovnováhu mezi náklady a údržbou pomocí zabezpečení, úložiště a dostupnosti obchodních dat v reálném čase.

Možná nebudete moct aplikaci spustit ve veřejném cloudu. Nemusí ale být hospodářsky proveditelné, aby společnost udržovala kapacitu potřebnou v místním prostředí, aby mohla zpracovávat špičky v poptávce pro aplikaci. V tomto modelu můžete použít pružnost veřejného cloudu s vaším místním řešením.

## <a name="solution"></a>Řešení

Vzor škálování mezi cloudy rozšiřuje aplikaci umístěnou v místním cloudu s využitím veřejných cloudových prostředků. Tento model se aktivuje zvýšením nebo snížením poptávky a v tomto pořadí přidá nebo odebere prostředky v cloudu. Tyto prostředky poskytují redundanci, rychlou dostupnost a geograficky vyhovující směrování.

![Vzor škálování mezi cloudy](media/pattern-cross-cloud-scale/cross-cloud-scaling.png)

> [!NOTE]
> Tento model se vztahuje pouze na bezstavové komponenty vaší aplikace.

## <a name="components"></a>Komponenty

Vzor škálování mezi cloudy se skládá z následujících součástí.

### <a name="outside-the-cloud"></a>Mimo Cloud

#### <a name="traffic-manager"></a>Traffic Manager

V diagramu se nachází mimo skupinu veřejných cloudů, ale musela by koordinovat provoz v místním datacentru i ve veřejném cloudu. Nástroj pro vyrovnávání zatížení poskytuje vysokou dostupnost pro aplikace monitorováním koncových bodů a zajištěním přerozdělení převzetí služeb při selhání.

#### <a name="domain-name-system-dns"></a>DNS (Domain Name System)

Název domény systému nebo DNS zodpovídá za překlad (nebo překladu) názvu webu nebo služby na jeho IP adresu.

### <a name="cloud"></a>Cloud

#### <a name="hosted-build-server"></a>Hostovaný sestavovací Server

Prostředí pro hostování vašeho kanálu sestavení.

#### <a name="app-resources"></a>Prostředky aplikace

Prostředky aplikace musí umožňovat horizontální navýšení kapacity a horizontální navýšení kapacity, jako jsou sady škálování a kontejnery pro virtuální počítače.

#### <a name="custom-domain-name"></a>Vlastní název domény

Pro požadavky směrování glob použít vlastní název domény.

#### <a name="public-ip-addresses"></a>Veřejné IP adresy

Veřejné IP adresy se používají ke směrování příchozího provozu prostřednictvím Traffic Manageru do koncového bodu prostředků cloudové aplikace.  

### <a name="local-cloud"></a>Místní Cloud

#### <a name="hosted-build-server"></a>Hostovaný sestavovací Server

Prostředí pro hostování vašeho kanálu sestavení.

#### <a name="app-resources"></a>Prostředky aplikace

Prostředky aplikace potřebují možnost horizontálního navýšení kapacity a horizontálního navýšení kapacity, jako jsou například sady škálování virtuálních počítačů a kontejnery.

#### <a name="custom-domain-name"></a>Vlastní název domény

Pro požadavky směrování glob použít vlastní název domény.

#### <a name="public-ip-addresses"></a>Veřejné IP adresy

Veřejné IP adresy se používají ke směrování příchozího provozu prostřednictvím Traffic Manageru do koncového bodu prostředků cloudové aplikace.

## <a name="issues-and-considerations"></a>Problémy a důležité informace

Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:

### <a name="scalability"></a>Škálovatelnost

Klíčovou součástí škálování mezi cloudy je schopnost doručovat škálování na vyžádání. Škálování musí probíhat mezi veřejnou a místní cloudovou infrastrukturou a poskytovat konzistentní a spolehlivé služby na vyžádání.

### <a name="availability"></a>Dostupnost

Zajistěte, aby lokálně nasazené aplikace byly nakonfigurované pro vysokou dostupnost prostřednictvím místní konfigurace hardwaru a nasazení softwaru.

### <a name="manageability"></a>Možnosti správy

Vzor křížového cloudu zajišťuje bezproblémové řízení a známé rozhraní mezi prostředími.

## <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít

Použijte tento model:

- Když potřebujete zvýšit kapacitu vaší aplikace s neočekávanými požadavky nebo pravidelnými požadavky na vyžádání.
- Pokud nechcete investovat do prostředků, které se budou používat jenom během špičky. Platíte za to, co využijete.

Tento model se nedoporučuje, pokud:

- Vaše řešení vyžaduje, aby se uživatelé připojovali přes Internet.
- Vaše firma má místní předpisy, které vyžadují, aby původní připojení pocházelo z volání na pracovišti.
- Vaše síť bude mít normální kritická místa, která by omezila výkon škálování.
- Vaše prostředí je odpojené od Internetu a nemůže se připojit k veřejnému cloudu.

## <a name="next-steps"></a>Další kroky

Další informace o tématech zavedených v tomto článku:

- Další informace o tom, jak tento nástroj pro vyrovnávání zatížení využívající službu DNS funguje, najdete v tématu [Přehled Azure Traffic Manager](/azure/traffic-manager/traffic-manager-overview) .
- Další informace o osvědčených postupech a získání odpovědí na jakékoli další otázky najdete v tématu [aspekty návrhu hybridní aplikace](overview-app-design-considerations.md) .
- Další informace o celém portfoliu produktů a řešení najdete v [Azure Stack rodině produktů a řešení](/azure-stack) .

Až budete připraveni otestovat příklad řešení, pokračujte v [Průvodci nasazením řešení škálování mezi cloudy](solution-deployment-guide-cross-cloud-scaling.md). Průvodce nasazením poskytuje podrobné pokyny pro nasazení a testování jeho komponent. Naučíte se, jak vytvořit řešení pro různé cloudy, které umožní ručně aktivovaný proces přepnutí z hostované webové aplikace Azure Stack hub do hostované webové aplikace Azure. Naučíte se také, jak používat automatické škálování prostřednictvím Traffic Manageru, což zajišťuje flexibilní a škálovatelný cloudový nástroj při zatížení.
