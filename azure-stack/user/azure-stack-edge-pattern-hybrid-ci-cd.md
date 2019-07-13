---
title: Vzor DevOps pro inteligentních hraničních zařízení pomocí služby Azure Stack | Dokumentace Microsoftu
description: Další informace o DevOps vzor pro inteligentních hraničních zařízení pomocí služby Azure Stack
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
ms.openlocfilehash: 76437cd37733984d3230d4c40ccc82c7e6ede2b9
ms.sourcegitcommit: 51ec68b5e6dbf437aaca19a9f35ba07d2c402892
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2019
ms.locfileid: "67856384"
---
# <a name="devops-pattern"></a>Vzor DevOps

Kódu z jednoho místa a nasazení do více cílů v vývoj, testování a produkční prostředí, které mohou být v místním datovém centru, privátních cloudů nebo ve veřejném cloudu.

## <a name="context-and-problem"></a>Kontext a problém

Kontinuity nasazení aplikací, zabezpečení a spolehlivost jsou zásadní pro organizace a důležité pro vývojové týmy.

Aplikace často vyžadují refaktorovaný kód ke spuštění v každý cílové prostředí. To znamená, že aplikace není zcela přenosný. Musí být aktualizována, testovat a ověřit, protože prochází přes každé prostředí. Například kód napsaný v prostředí pro vývoj musíte pak být přepsané téma, které fungují v testovacím prostředí a přepsán při nakonec jsou v produkčním prostředí. Kromě toho tento kód je konkrétně vázán na hostitele. Tím se zvyšuje náklady a složitost spojené s udržováním vaší aplikace. Každá verze aplikace je vázán na každé prostředí. Zvýšení složitosti a duplikace zvyšuje riziko zabezpečení a kvalitě kódu. Kromě toho kód se nedají znovu nasadit snadno při odebrání hostitele obnovení se nezdařilo nebo nasazení další hostitele pro zpracování zvýšení poptávky.

## <a name="solution"></a>Řešení

Vzor DevOps umožňuje vytváření, testování a nasazení aplikace, která běží na několika cloudy. Tento model sjednotí postupů průběžné integrace a průběžného doručování. Kód je s využitím průběžné integrace, vytvořená a testovat pokaždé, když člen týmu potvrdí změny do správy verzí. Průběžné doručování automatizuje každý krok z buildu do produkčního prostředí. Tyto procesy dohromady, vytvořit proces vydávání verzí, který podporuje nasazení v různých prostředích. S tímto modelem stačí můžete návrh kódu a pak nasadit stejný kód do místního prostředí, jiný privátní cloudy a veřejných cloudech. Rozdíly v prostředí vyžadovat změnu do konfiguračního souboru, nikoli změny kódu.

![Vzor DevOps](media/azure-stack-edge-pattern-hybrid-ci-cd/hybrid-ci-cd.png)

Konzistentní sadu nástrojů pro vývoj napříč místními, privátního cloudu a veřejných cloudových prostředích můžete implementovat postup průběžné integrace a průběžného doručování. Aplikace a službám nasazeným pomocí modelu DevOps jsou zaměnitelné a můžete spustit v některém z těchto umístění s využitím místních a veřejným cloudem funkcí a možností.

Použití DevOps kanál pro vydávání verzí vám pomůže:

-   Zahájení nového sestavení založené na potvrzení změn kódu do jednoho úložiště.

-   Automaticky nasaďte nově vytvořený kód do veřejného cloudu pro testování přijetí u zákazníků.

-   Automaticky nasaďte do privátního cloudu, jakmile se váš kód prošel testováním.

## <a name="issues-and-considerations"></a>Problémy a důležité informace

Vzor DevOps je určený k zachování konzistence napříč nasazení bez ohledu na cílové prostředí. Funkce se však liší v cloudu a místních prostředích. Zvažte použití těchto zdrojů:

-   Jsou funkce, koncové body, služeb a dalších prostředků ve vašem nasazení k dispozici v umístění cílů nasazení?

-   Jsou artefakty konfigurace uložené v umístění, které jsou dostupné v cloudu?

-   Bude parametrů nasazení fungovat ve všech cílových prostředí?

-   Jsou k dispozici vlastnosti specifických pro prostředky ve všech cloudech cílové?

Další informace najdete v tématu [šablon vývoj Azure Resource Manageru pro cloud konzistence](https://docs.microsoft.com/azure/azure-resource-manager/templates-cloud-consistency).

Kromě toho zvažte následující skutečnosti při rozhodování o tom, jak tento model implementovat:

### <a name="scalability-considerations"></a>Aspekty zabezpečení

Nasazení automatizační systémy jsou klíče řídicí bod ve vzorech DevOps. Implementace se může lišit. Výběr správné velikosti serveru závisí na velikosti očekávaného zatížení. Virtuální počítače dražší škálování než kontejnery. Pokud chcete ke škálování použít kontejnery, musí ale být kontejnery využité při procesu sestavování.

### <a name="availability-considerations"></a>Aspekty dostupnosti

Dostupnost v kontextu DevPattern znamená, že možnost obnovit libovolné informace o stavu přidružené k pracovního postupu, jako je například výsledky testu, závislostí kódu nebo jiné artefakty. Při vyhodnocování požadavků na dostupnost vezměte v úvahu dvě běžné metriky:

-   Cíl času obnovení (RTO) určuje, jak dlouho vydržíte bez systému.

-   Cíl bodu obnovení (RPO) určuje, kolik dat si můžete dovolit ztratit, pokud přerušení ve službě ovlivňuje systém.

V praxi znamenají RTO a RPO redundanci a zálohování. Na globální cloud Azure, dostupnost není otázkou obnovení hardwaru –, který je součástí Azure, ale spíš zajištění, že udržujete stav vašich systémů DevOps. Ve službě Azure Stack obnovení hardwaru může být potřeba.

Hlavní je potřeba zamyslet při návrhu systému, použít pro automatizaci nasazení se řízení přístupu a správné správy práv potřebných k nasazení služeb do cloudových prostředích. Jaká práva je třeba vytvořit, odstranit nebo upravit nasazení? Jedna sada práv je většinou nutné k vytvoření skupiny prostředků v Azure a druhý k nasazení služeb do skupiny prostředků.

### <a name="manageability-considerations"></a>Aspekty správy

Návrh libovolný systém založena na vzoru DevOps nutné vzít v úvahu automatizace, protokolování a upozorňování pro každou službu v celém portfoliu. Použití sdílených služeb nebo aplikací team a sledovat zásady zabezpečení a zásad správného řízení také.

Nasazení v produkčním prostředí a vývojová/testovací prostředí v samostatných skupin prostředků v Azure nebo Azure Stack. Potom můžete monitorovat prostředky jednotlivých prostředí a seskupit fakturační náklady podle skupin prostředků. Prostředky můžete také odstranit jako sadu, což je užitečné pro testovací nasazení.

## <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít

Použijte tento model:

-   Můžete vyvíjet kód v jednom prostředí, která vyhovuje potřebám vašich vývojářů a nasazení do konkrétního prostředí do vašeho řešení, ve kterém může být obtížné rozvíjet nový kód.

-   Můžete použít kód a nástroje, které vaši vývojáři chtěli nám za předpokladu, budou mít možnost si podle postupu průběžné integrace a průběžné doručování vzoru DevOps.

Tento model se nedoporučuje:

-   Pokud nelze automatizovat infrastrukturu a zřizování prostředků, konfigurace, identity a zabezpečení úloh.

-   Pokud týmy nemají přístup ke cloudovým prostředkům hybridní implementace průběžné integrace a průběžného vývoje (CI/CD) přístup.

## <a name="example"></a>Příklad

Zjistěte, jak nasadit aplikaci do Azure a využitím kanálu průběžné integrace a doručování (CI/CD) hybridní službě Azure Stack.

[Nasazení aplikací do Azure a Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-pipeline)

## <a name="next-steps"></a>Další postup

Další informace o [vzory návrhu v hybridního cloudu pro Azure Stack](azure-stack-edge-pattern-overview.md)
