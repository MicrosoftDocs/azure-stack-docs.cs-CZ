---
title: Vzor DevOps pro inteligentních hraničních zařízení pomocí služby Azure Stack | Dokumentace Microsoftu
description: Další informace o DevOps vzor pro inteligentních hraničních zařízení pomocí služby Azure Stack.
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
ms.openlocfilehash: 07e0c3f0ca52c7079b879050ab36bf6a5f166f92
ms.sourcegitcommit: 51ec68b5e6dbf437aaca19a9f35ba07d2c402892
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2019
ms.locfileid: "67856362"
---
# <a name="geo-distributed-pattern"></a>Geograficky distribuované vzor

Zadejte koncové body aplikace napříč několika oblastmi. Směrovat uživatelský provoz podle umístění a dodržování předpisů.

## <a name="context-and-problem"></a>Kontext a problém

Organizace s rozsáhlý zeměpisných oblastech snažit bezpečně a přesně distribuovat a povolit přístup k datům přitom zajistit požadované úrovně zabezpečení, dodržování předpisů a výkon jednotlivých uživatelů, umístění a zařízení napříč ohraničení.

## <a name="solution"></a>Řešení

Vzor směrování geografické provoz služby Azure Stack nebo Geo-Distributed aplikace umožňuje přenos přesměrováni na konkrétní koncové body na základě různých metrik. Vytváření Traffic Manageru s geografické založené na směrování a koncový bod trasy konfigurace provozu do koncových bodů na základě místní požadavky, podnikové a mezinárodní nařízení a data potřebám.

![Geograficky distribuované vzor](media/azure-stack-edge-pattern-geo-distribution/geo-distribution.png)

**Traffic Manager**  
V diagramu tento soubor je umístěn mimo veřejného cloudu, ale by třeba mohla koordinovat provoz v místním datovém centru a veřejným cloudem. Nástroje pro vyrovnávání směruje přenosy do geografických umístěních.

**Domain Name System (DNS)**  
Domain Name System nebo DNS, je zodpovědný za převod (nebo řešení) názvu webu nebo služby k její IP adrese.

### <a name="public-cloud"></a>Veřejný cloud

**Cloud Endpoint**  
Veřejné IP adresy se používají ke směrování příchozího provozu pomocí traffic Manageru na koncový bod aplikace prostředky veřejného cloudu.  

### <a name="local-clouds"></a>Místní cloudy

**Místní koncový bod**  
Veřejné IP adresy se používají ke směrování příchozího provozu pomocí traffic Manageru na koncový bod aplikace prostředky veřejného cloudu.

## <a name="issues-and-considerations"></a>Problémy a důležité informace

Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:

### <a name="scalability-considerations"></a>Aspekty zabezpečení

Vzor zpracovává zeměpisné provoz směrování, spíše než škálování, abychom splnili zvýšení provozu. Tento model lze však kombinovat s jinými řešeními pro Azure a místní. Například je možné tento model se vzorem škálování cloudu.

### <a name="availability-considerations"></a>Aspekty dostupnosti

Zajištění místně nasazených aplikací jsou nakonfigurované pro vysokou dostupnost v místním hardwaru konfigurací a nasazování softwaru.

### <a name="manageability-considerations"></a>Aspekty správy

Vzor zajišťuje Bezproblémová Správa a známému rozhraní mezi prostředími.

## <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít

-   V naší organizaci je mezinárodní vyžadující vlastní místní zabezpečení a distribuci zásad větví.

-   Každé z poboček mojí organizace si vyžádá zaměstnance, firmy a data, zařízení, která vyžadují reporting aktivita na místní předpisy a časovém pásmu.

-   Vodorovně horizontální navýšení kapacity aplikace s více nasazení aplikací pro zpracování požadavků na extrémní zatížení prováděných v rámci jedné oblasti i napříč oblastmi, mohou být splněny požadavky na vysokou škálovatelností.

-   Aplikace musí být vysoce dostupná a reagovat na žádosti klientů i v případě výpadku jedné oblasti.

## <a name="example"></a>Příklad

Zjistěte, jak směrovat provoz na konkrétní koncové body na základě různých metrik pomocí vzoru geograficky distribuované aplikace. Vytváření Traffic Manageru profil s geografické konfigurace založená na směrování a koncového bodu zajišťuje informace se směruje do koncových bodů na základě místní požadavky, podnikové a mezinárodní nařízení a dat, které potřebujete.

[Vytvoření řešení geograficky distribuované aplikace s využitím Azure a Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-geo-distributed)

## <a name="next-steps"></a>Další postup

Další informace o [vzory návrhu v hybridního cloudu pro Azure Stack](azure-stack-edge-pattern-overview.md)
