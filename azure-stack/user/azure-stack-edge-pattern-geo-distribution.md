---
title: DevOps vzor pro inteligentní Edge s Azure Stack | Microsoft Docs
description: Přečtěte si o DevOps modelu inteligentního okraje pomocí Azure Stack.
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
ms.date: 10/10/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 06/11/2019
ms.openlocfilehash: deab520045d50acefb03691b9b127f99676061a0
ms.sourcegitcommit: a6d47164c13f651c54ea0986d825e637e1f77018
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72277714"
---
# <a name="geo-distributed-pattern"></a>Geograficky distribuovaný model

Poskytněte koncové body aplikace v několika oblastech. Směrování přenosů uživatelů na základě umístění a požadavků na dodržování předpisů.

## <a name="context-and-problem"></a>Kontext a problém

Organizace, které mají rozsáhlou škálu geografických oblastí, se snaží bezpečně a přesně distribuovat a umožňují přístup k datům a současně zajišťují požadavky na zabezpečení, dodržování předpisů a výkon na uživatele, umístění a zařízení přes hranice.

## <a name="solution"></a>Řešení

Model směrování geografického provozu Azure Stack nebo geograficky distribuované aplikace umožňuje směrovat provoz na konkrétní koncové body na základě různých metrik. Vytvoření Traffic Manager s využitím geograficky směrování a konfigurace koncového bodu směruje provoz do koncových bodů na základě regionálních požadavků, podnikových a mezinárodních předpisů a datových potřeb.

![Geograficky distribuovaný model](media/azure-stack-edge-pattern-geo-distribution/geo-distribution.png)

**Traffic Manager**  
V tomto diagramu se nachází mimo veřejný cloud, ale musí být schopný koordinovat provoz v místním datovém centru i ve veřejném cloudu. Nástroj pro vyrovnávání zatížení směruje provoz do geografických umístění.

**DNS (Domain Name System)**  
Název domény systému nebo DNS zodpovídá za překlad (nebo překladu) názvu webu nebo služby na jeho IP adresu.

### <a name="public-cloud"></a>Veřejný cloud

**Koncový bod cloudu**  
Veřejné IP adresy slouží ke směrování příchozího provozu prostřednictvím Traffic Manageru do koncového bodu prostředků aplikace veřejné cloudy.  

### <a name="local-clouds"></a>Místní cloudy

**Místní koncový bod**  
Veřejné IP adresy slouží ke směrování příchozího provozu prostřednictvím Traffic Manageru do koncového bodu prostředků aplikace veřejné cloudy.

## <a name="issues-and-considerations"></a>Problémy a důležité aspekty

Při rozhodování, jak tento model implementovat, byste měli vzít v úvahu následující body:

### <a name="scalability-considerations"></a>Požadavky na škálovatelnost

Tento model zpracovává směr geografického provozu, nikoli škálování pro zvýšení provozu. Tento model však můžete zkombinovat s jinými řešeními Azure a místními řešeními. Tento model se dá použít například se vzorem škálování mezi cloudy.

### <a name="availability-considerations"></a>Požadavky na dostupnost

Zajistěte, aby lokálně nasazené aplikace byly nakonfigurované pro vysokou dostupnost prostřednictvím místní konfigurace hardwaru a nasazení softwaru.

### <a name="manageability-considerations"></a>Důvody pro spravovatelnost

Vzor zajišťuje bezproblémové řízení a známé rozhraní mezi prostředími.

## <a name="when-to-use-this-pattern"></a>Kdy použít tento model

-   Moje organizace má mezinárodní pobočky vyžadující vlastní regionální zásady zabezpečení a distribuce.

-   Každá z kanceláří mojí organizace si vyžádá data o zaměstnancích, firmách a obchodních přístavech a vyžaduje, aby se na základě místních předpisů a časového pásma vycházely

-   Vysoce škálovatelné požadavky můžou být splněné horizontálním škálováním aplikací, přičemž v jedné oblasti se vytvoří víc nasazení aplikace, stejně jako v různých oblastech, aby se mohly zvládnout extrémní požadavky na zatížení.

-   Aplikace musí být vysoce dostupné a reagovat na požadavky klientů i v případě výpadku jedné oblasti.

## <a name="example"></a>Příklad

Naučte se směrovat provoz do konkrétních koncových bodů na základě různých metrik pomocí vzoru geograficky distribuovaných aplikací. Když vytvoříte profil Traffic Manager s využitím geografického směrování a konfigurace koncového bodu, zajistíte směrování informací na koncové body na základě regionálních požadavků, podnikových a mezinárodních předpisů a vašich datových potřeb.

[Vytvořte geograficky distribuované řešení aplikace pomocí Azure a Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-geo-distributed)

## <a name="next-steps"></a>Další kroky

Další informace o [vzorech návrhu hybridního cloudu pro Azure Stack](azure-stack-edge-pattern-overview.md)
