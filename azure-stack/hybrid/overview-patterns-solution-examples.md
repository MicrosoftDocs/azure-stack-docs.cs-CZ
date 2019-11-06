---
title: Hybridní vzory a příklady řešení pro Azure a Azure Stack
description: Přehled hybridních vzorů a příkladů řešení, které jsou užitečné pro učení a sestavování hybridních řešení v Azure a Azure Stack.
author: BryanLa
ms.service: azure-stack
ms.topic: overview
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 134171c85f91801f83965d376f79d09f6e8e3ac8
ms.sourcegitcommit: 5c92a669007ab4aaffe4484f1d8836a40340dde1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73638725"
---
# <a name="hybrid-patterns-and-solution-examples-for-azure-and-azure-stack"></a>Hybridní vzory a příklady řešení pro Azure a Azure Stack

Microsoft poskytuje produkty a řešení Azure a Azure Stack jako jeden konzistentní ekosystém Azure. Microsoft Azure Stack rodina je rozšířením Azure. 

## <a name="the-hybrid-cloud-and-hybrid-apps"></a>Hybridní cloud a hybridní aplikace

Azure Stack přináší flexibilitu cloud computingu do místního prostředí a na hraničních zařízeních tím, že umožňuje *hybridní cloud*. Centrum Azure Stack, Azure Stack HCI a Azure Stack Edge rozšíří Azure z cloudu do datacenter v svrchovaných pobočkách, poboček, polí a mimo ně. Díky této různorodé sadě funkcí můžete:

- Používejte kód a spouštějte nativní cloudové aplikace konzistentně napříč Azure a místními prostředími.
- Spouštějte tradiční virtualizované úlohy s volitelnými připojeními ke službám Azure.
- Přeneste data do cloudu nebo je Zachovejte v datacentrech dat, abyste zachovali dodržování předpisů.
- Spusťte hardwarově akcelerované strojové učení, kontejnery nebo virtualizované úlohy, a to vše na inteligentních hraničních zařízeních.

Aplikace, které zahrnují cloudy, se také označují jako *hybridní aplikace*. V Azure můžete vytvářet hybridní cloudové aplikace a nasazovat je do připojeného nebo odpojeného datacentra, které najdete kdekoli.

Hybridní scénáře aplikací se výrazně liší u prostředků, které jsou k dispozici pro vývoj. Také zahrnují hlediska geografického zabezpečení, přístupu k Internetu a dalších. I když zde popsané vzorce a řešení nemusí řešit všechny požadavky, poskytují pokyny a příklady pro prozkoumání a opakované použití při implementaci hybridních řešení.

## <a name="design-patterns"></a>Způsoby návrhu

Vzory návrhu odvrácené na základě možností opakovaného návrhu, od reálných scénářů a zkušeností zákazníků. Vzor je abstraktní a umožňuje, aby byl použitelný pro různé typy scénářů nebo vertikální obory. Každý vzor dokumentuje kontext a problém a poskytuje přehled o příkladech řešení. Příklad řešení je určen jako možná implementace vzoru.

Existují dva typy článků o vzorcích:

- Jeden vzor: poskytuje pokyny k návrhu pro jediný scénář pro obecné účely.
- Multi-Pattern: poskytuje pokyny pro návrh, kde se používá aplikace s více vzorci. To se často vyžaduje pro řešení složitějších scénářů nebo problémů specifických pro konkrétní obor.

## <a name="solution-deployment-guides"></a>Průvodci nasazením řešení

Podrobný průvodce nasazením vám pomůže při nasazení příkladu řešení. Průvodce může také odkazovat na ukázku kódu, který je uložen v [úložišti ukázek řešení](https://github.com/Azure-Samples/azure-intelligent-edge-patterns)GitHub. 

## <a name="next-steps"></a>Další kroky

- Podívejte se na [Azure Stack rodinu produktů a řešení](/azure-stack), abyste se dozvěděli víc o celém portfoliu produktů a řešení.
- Další informace o jednotlivých tématech najdete v částech "vzory" a "Průvodci nasazením řešení".
- Přečtěte si o [faktorech návrhu hybridní aplikace](overview-app-design-considerations.md) a prozkoumejte pilíře kvality softwaru pro navrhování, nasazování a provoz hybridních aplikací.
- [Nastavte vývojové prostředí na Azure Stack](../user/azure-stack-dev-start.md) a [nasaďte svoji první aplikaci](../user/azure-stack-dev-start-deploy-app.md) na Azure Stack.
