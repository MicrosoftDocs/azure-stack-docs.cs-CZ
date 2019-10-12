---
title: Vzor DevOps pro inteligentní Edge s Azure Stack | Microsoft Docs
description: Přečtěte si o modelu DevOps pro inteligentní Edge pomocí Azure Stack
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
ms.openlocfilehash: 04eff0f095f14d88443fc4b221799e63f523c82c
ms.sourcegitcommit: a6d47164c13f651c54ea0986d825e637e1f77018
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72277003"
---
# <a name="devops-pattern"></a>Vzor DevOps

Kód z jednoho umístění a nasazení na více cílů ve vývojových, testovacích a produkčních prostředích, která můžou být v místním datacentru, privátních cloudech nebo ve veřejném cloudu.

## <a name="context-and-problem"></a>Kontext a problém

Kontinuita nasazení aplikace, zabezpečení a spolehlivost jsou zásadní pro organizace a kritické pro vývojové týmy.

Aplikace často vyžadují refaktoring kódu ke spuštění v každém cílovém prostředí. To znamená, že aplikace není zcela přenosná. Je nutné ho aktualizovat, otestovat a ověřit při pohybu přes každé prostředí. Například kód napsaný ve vývojovém prostředí musí být přepsán, aby fungoval v testovacím prostředí, a přepsat, když je nakonec v produkčním prostředí. Kromě toho je tento kód konkrétně svázán s hostitelem. Tím se zvýší náklady a složitost údržby aplikace. Každá verze aplikace je vázaná na každé prostředí. Zvýšení složitosti a duplicit zvyšuje riziko zabezpečení a kvality kódu. Kromě toho kód nejde snadno znovu nasadit, když odeberete rutiny obnovit neúspěšné hostitele nebo nasadíte další hostitele, aby se podařilo zvýšit nároky na poptávku.

## <a name="solution"></a>Řešení

Vzor DevOps vám umožňuje sestavovat, testovat a nasazovat aplikace, které běží na více cloudech. Tento vzor jednotek vydává postupy průběžné integrace a průběžného doručování. S průběžnou integrací je kód sestaven a testován pokaždé, když člen týmu potvrdí změnu ve správě verzí. Průběžné doručování automatizuje každý krok z buildu do produkčního prostředí. Tyto procesy společně vytvoří proces vydání, který podporuje nasazení napříč různými prostředími. Pomocí tohoto modelu můžete vytvořit koncept kódu a pak nasadit stejný kód do místního prostředí, různých privátních cloudů a veřejných cloudů. Rozdíly v prostředí vyžadují změnu konfiguračního souboru, nikoli změny kódu.

![Vzor DevOps](media/azure-stack-edge-pattern-hybrid-ci-cd/hybrid-ci-cd.png)

Díky konzistentní sadě vývojářských nástrojů v místních, privátních a veřejných cloudových prostředích můžete implementovat postupy průběžné integrace a průběžného doručování. Aplikace a služby nasazené pomocí modelu DevOps jsou zaměnitelné a můžou běžet v libovolném z těchto umístění, a to s využitím místních a veřejných cloudových funkcí a možností.

Použití kanálu pro vydávání verzí DevOps vám pomůže:

-   Iniciuje nové sestavení založené na potvrzeních kódu do jediného úložiště.

-   K automatickému nasazení nově vytvořeného kódu ve veřejném cloudu pro testování přijetí uživateli.

-   Automatické nasazení do privátního cloudu, jakmile váš kód prošel testováním.

## <a name="issues-and-considerations"></a>Problémy a důležité aspekty

Vzor DevOps je určený k zajištění konzistence napříč nasazeními bez ohledu na cílové prostředí. Možnosti se ale liší v cloudových i místních prostředích. Vezměte v úvahu následující skutečnosti:

-   Jsou funkce, koncové body, služby a další prostředky v nasazení dostupné v cílových umístěních nasazení?

-   Jsou artefakty konfigurace uložené v umístěních, která jsou přístupná napříč cloudy?

-   Budou parametry nasazení fungovat ve všech cílových prostředích?

-   Jsou vlastnosti specifické pro prostředky dostupné ve všech cílových cloudech?

Další informace najdete v tématu [vývoj šablon Azure Resource Manager pro cloudovou konzistenci](https://docs.microsoft.com/azure/azure-resource-manager/templates-cloud-consistency).

Při rozhodování, jak tento model implementovat, zvažte také následující body:

### <a name="scalability-considerations"></a>Požadavky na škálovatelnost

Systémy automatizace nasazení jsou klíčovým řídicím bodem ve vzorech DevOps. Implementace se můžou lišit. Výběr správné velikosti serveru závisí na velikosti očekávaného zatížení. Větší náklady na virtuální počítače se škálují od kontejnerů. Chcete-li však použít kontejnery pro škálování, proces sestavení musí být spuštěn s kontejnery.

### <a name="availability-considerations"></a>Požadavky na dostupnost

Dostupnost v kontextu DevPattern znamená, že je možné obnovit všechny informace o stavu přidružené k vašemu pracovnímu postupu, jako jsou například výsledky testů, závislosti kódu nebo jiné artefakty. Pokud chcete vyhodnotit své požadavky na dostupnost, zvažte dvě běžné metriky:

-   Plánovaná doba obnovení (RTO) určuje, jak dlouho můžete bez systému přejít.

-   Cíl bodu obnovení (RPO) indikuje, kolik dat se může hodit, pokud má dopad na systém vliv přerušení služby.

V praxi RTO a RPO implikuje redundanci a zálohování. V globálním cloudu Azure není dostupnost v otázce obnovení hardwaru, která je součástí Azure – ale ale zajišťuje, abyste si zachovali stav svých DevOps systémů. V Azure Stack může být zváženo obnovení hardwaru.

Dalším důležitým aspektem při navrhování systému používaného pro automatizaci nasazení je řízení přístupu a správná Správa práv potřebných k nasazení služeb do cloudových prostředí. Jaká práva jsou nutná k vytváření, odstraňování a úpravám nasazení? Například jedna sada práv je obvykle nutná k vytvoření skupiny prostředků v Azure a další pro nasazení služeb ve skupině prostředků.

### <a name="manageability-considerations"></a>Důvody pro spravovatelnost

Návrh jakéhokoli systému, který je založený na vzorech DevOps, musí vzít v úvahu automatizaci, protokolování a upozorňování na každou službu v rámci portfolia. Používejte taky sdílené služby, aplikační tým nebo obojí a sledujte taky zásady zabezpečení a zásady správného řízení.

Nasaďte produkční prostředí a vývojové a testovací prostředí do samostatných skupin prostředků v Azure nebo Azure Stack. Pak můžete monitorovat prostředky jednotlivých prostředí a účtovat fakturační náklady podle skupin prostředků. Prostředky můžete také odstranit jako sadu, což je užitečné pro testovací nasazení.

## <a name="when-to-use-this-pattern"></a>Kdy použít tento model

Použít tento model:

-   Můžete vyvíjet kód v jednom prostředí, které vyhovuje potřebám vašich vývojářů a nasazovat do prostředí specifického pro vaše řešení, kde může být obtížné vyvíjet nový kód.

-   Můžete použít kód a nástroje, které vašim vývojářům rádi chce, pokud budou moci postupovat podle procesu průběžné integrace a průběžného doručování ve vzoru DevOps.

Tento model se nedoporučuje:

-   Pokud nemůžete automatizovat infrastrukturu, zřizování prostředků, konfigurace, identity a zabezpečení úloh.

-   Pokud týmy nemají přístup k hybridním cloudovým prostředkům a implementují přístup pro průběžnou integraci/dopředné vývojové prostředí (CI/CD).

## <a name="example"></a>Příklad

Naučte se, jak nasadit aplikaci do Azure a Azure Stack pomocí kanálu hybridního průběžné integrace nebo průběžného doručování (CI/CD).

[Nasazení aplikací do Azure a Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-pipeline)

## <a name="next-steps"></a>Další kroky

Další informace o [vzorech návrhu hybridního cloudu pro Azure Stack](azure-stack-edge-pattern-overview.md)
