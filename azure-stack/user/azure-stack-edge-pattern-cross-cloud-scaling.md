---
title: Vzor škálování cloudu pro inteligentních hraničních zařízení pomocí služby Azure Stack | Dokumentace Microsoftu
description: Další informace o škálování cloudu vzor pro inteligentních hraničních zařízení pomocí služby Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 06/11/2019
ms.openlocfilehash: 2b97be7ecfffe83e560d32fbaac6480c8c17bb88
ms.sourcegitcommit: 51ec68b5e6dbf437aaca19a9f35ba07d2c402892
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2019
ms.locfileid: "67856373"
---
# <a name="cross-cloud-scaling-pattern"></a>Vzor škálování cloudu

Automaticky přidáte prostředky do existující aplikace tak, aby vyhovovaly nárůst zatížení.

## <a name="context-and-problem"></a>Kontext a problém

Aplikaci nelze zvýšení kapacity pro splnění neočekávané zvýšení poptávky. Výsledkem je uživatelé nedosáhne aplikace během špičky využití. Aplikace může obsluhovat pevným počtem uživatelů.

Globální podniky, které vyžadují zabezpečené, spolehlivé a dostupné cloudovým aplikacím. Schůzky kvůli současnému nárůstu poptávky, a pomocí správné infrastruktury pro podporu vyžadující je velmi důležité. Společností bojuje s vyvážit náklady a údržba obchodních dat zabezpečení, úložiště a dostupnosti v reálném čase.

Nebudete moci spustit vaši aplikaci ve veřejném cloudu. Ale nemusí být ekonomicky vhodná pro firmy a udržovat kapacita požadovaná pro zpracování accurately špiček v poptávce aplikace v jejich v místním prostředí. Pomocí tohoto modelu můžou využít pružnosti veřejného cloudu s místním řešením.

## <a name="solution"></a>Řešení

Vzor škálování cloudu rozšiřuje možnosti aplikace umístěné v místní cloud s veřejným cloudovým prostředkům. Vzor se aktivuje při zvýšení nebo snížení poptávky a v uvedeném pořadí přidá nebo odstraní prostředky v cloudu. Tyto prostředky poskytnout redundanci, rychlé dostupnost a CLS geografické směrování.

![Vzor škálování cloudu](media/azure-stack-edge-pattern-cross-cloud-scaling/cross-cloud-scaling.png)

> Poznámka:  
> Tento model platí jenom pro bezstavové aplikace.

Vzor škálování cloudu se skládá z následujících součástí.

**Traffic Manager**  
** V diagramu tento soubor je umístěn mimo skupinu veřejného cloudu, ale by třeba mohla koordinovat provoz v místním datovém centru a veřejným cloudem. Nástroje pro vyrovnávání a zajišťuje vysokou dostupnost aplikace tak monitorování koncových bodů a poskytuje redistribution převzetí služeb při selhání v případě potřeby.

**Domain Name System (DNS)**  
Domain Name System nebo DNS, je zodpovědný za převod (nebo řešení) názvu webu nebo služby k její IP adrese.

### <a name="cloud"></a>Cloud

**Hosted Build Server**  
Prostředí pro hostování vašeho kanálu sestavení.

**Prostředky aplikace**  
Prostředky aplikace potřeba mít možnost pro horizontální snížení kapacity a horizontální navýšení kapacity, jako je například ScaleSets virtuálních počítačů a kontejnerů.

**Custom Domain Name**  
Použití vlastního názvu domény pro glob směrování požadavků.

**Veřejné IP adresy**  
Veřejné IP adresy se používají ke směrování příchozího provozu pomocí traffic Manageru na koncový bod aplikace prostředky veřejného cloudu.  

### <a name="local-cloud"></a>Místní cloud

**Hosted Build Server**  
Prostředí pro hostování vašeho kanálu sestavení.

**Prostředky aplikace**  
Prostředky aplikace potřeba mít možnost pro horizontální snížení kapacity a horizontální navýšení kapacity, jako je například ScaleSets virtuálních počítačů a kontejnerů.

**Custom Domain Name**  
Použití vlastního názvu domény pro glob směrování požadavků.

**Veřejné IP adresy**  
Veřejné IP adresy se používají ke směrování příchozího provozu pomocí traffic Manageru na koncový bod aplikace prostředky veřejného cloudu. 

## <a name="issues-and-considerations"></a>Problémy a důležité informace


Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:

### <a name="scalability-considerations"></a>Aspekty zabezpečení

Klíčovou součástí škálování cloudu je schopnost poskytovat škálování na vyžádání mezi veřejné a místní cloudové infrastruktury, prokázání konzistentní a spolehlivé služby podle poptávky.

### <a name="availability-considerations"></a>Aspekty dostupnosti

Zajištění místně nasazených aplikací jsou nakonfigurované pro vysokou dostupnost v místním hardwaru konfigurací a nasazování softwaru.

### <a name="manageability-considerations"></a>Aspekty správy

Vzor cloudu zajišťuje známých rozhraní mezi prostředími a Bezproblémová Správa.

#### <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít

Použijte tento model:

-   Když potřebujete zvýšit kapacitu vaší aplikace s neočekávané požadavky nebo pravidelné požadavky v poptávce.

-   Pokud nechcete investovat do prostředků, které se používají pouze během špiček. Plaťte jenom za to, co používáte.

Tento model se nedoporučuje, pokud:

-   Řešení vyžaduje, aby uživatelé připojení přes internet.

-   Váš podnik má místní předpisy, které vyžadují, aby původní připojení pocházet z volání rozhraní místní.

-   Síti prostředí regulární problémová místa, které by omezit výkon škálování.

-   Prostředí není připojený k Internetu a nemá přístup ve veřejném cloudu.

## <a name="example"></a>Příklad

Zjistěte, jak různé cloudové řešení, které poskytují ručně aktivované proces pro přechod z Azure Stack hostované webové aplikace do Azure hostované webové aplikace s automatickým Škálováním pomocí traffic Manageru, zajišťuje flexibilní a škálovatelná Cloudová nástroj při zátěži.

[Vytvoření řešení škálování cloudu s Azure](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-cloud-burst)

## <a name="next-steps"></a>Další postup

Další informace o [vzory návrhu v hybridního cloudu pro Azure Stack](azure-stack-edge-pattern-overview.md)
