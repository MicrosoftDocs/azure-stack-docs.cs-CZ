---
title: Monitorování stavu a výstrah v centru Azure Stack | Microsoft Docs
description: Naučte se monitorovat stav a výstrahy v centru Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/2/2019
ms.author: mabrigg
ms.lastreviewed: 01/18/2019
ms.openlocfilehash: d539ea7c2be9cbd124ce3286adda2eff8cd1a7ca
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75882296"
---
# <a name="monitor-health-and-alerts-in-azure-stack-hub"></a>Monitorování stavu a výstrah v centru Azure Stack

Centrum Azure Stack zahrnuje možnosti monitorování infrastruktury, které vám pomůžou zobrazit stav a výstrahy pro oblast centra Azure Stack. Dlaždice **Správa oblastí** obsahuje seznam všech nasazených oblastí centra Azure Stack. Ve výchozím nastavení je připnuté na portálu pro správu pro výchozí předplatné poskytovatele. Tato dlaždice zobrazuje počet aktivních výstrah kritických a varovných upozornění pro jednotlivé oblasti. Tato dlaždice je vaším vstupním bodem do funkce stavu a výstrahy centra Azure Stack.

![Dlaždice správy oblastí na portálu správce centra Azure Stack](media/azure-stack-monitor-health/image1.png)

## <a name="understand-health-in-azure-stack-hub"></a>Vysvětlení stavu v centru Azure Stack

Poskytovatel prostředků stavu spravuje stav a výstrahy. Komponenty infrastruktury centra Azure Stack se registrují u poskytovatele prostředků stavu během nasazování a konfigurace centra Azure Stack. Tato registrace umožňuje zobrazit stav a výstrahy pro jednotlivé komponenty. Stav v centru Azure Stack je jednoduchý koncept. Pokud existují výstrahy pro registrovanou instanci komponenty, stav této součásti odráží nejhorší závažnost aktivní výstrahy: upozornění nebo kritická.

## <a name="alert-severity-definition"></a>Definice závažnosti výstrahy

Centrum Azure Stack vyvolává výstrahy jenom se dvěma závažnostmi: **Upozornění** a **kritické**.

- **Upozornění**  
  Operátor může výstražné upozornění naplánovaným způsobem vyřešit. Výstraha obvykle nemá vliv na uživatelské úlohy.

- **Kritické**  
  Operátor by měl vyřešit kritickou výstrahu s naléhavostí. Tyto výstrahy označují problémy, které aktuálně ovlivňují nebo brzy ovlivní Azure Stack uživatelů centra.


## <a name="view-and-manage-component-health-state"></a>Zobrazení a Správa stavu součásti

Stav komponent můžete zobrazit na portálu pro správu a pomocí REST API a PowerShellu.

Chcete-li zobrazit stav na portálu, klikněte na oblast, kterou chcete zobrazit v dlaždici **Správa oblastí** . Můžete zobrazit stav rolí infrastruktury a poskytovatelů prostředků.

![Seznam rolí infrastruktury](media/azure-stack-monitor-health/image2.png)

Kliknutím na poskytovatel prostředků nebo na roli infrastruktury zobrazíte podrobnější informace.

> [!WARNING]  
> Pokud kliknete na roli infrastruktury a pak na instanci role, existují možnosti **spuštění**, **restartování**nebo **vypnutí**. Tyto akce nepoužívejte při použití aktualizací pro integrovaný systém. Nepoužívejte také tyto možnosti v prostředí Azure Stack Development Kit (ASDK). Tyto možnosti jsou určené jenom pro prostředí s integrovanými systémy, kde je na jednu roli infrastruktury víc instancí role. Restartování instance role (obzvláště AzS-Xrp01) v ASDK způsobuje nestabilitu systému. Pokud potřebujete pomoc s řešením potíží, vystavte svůj problém do [fóra centra Azure Stack](https://aka.ms/azurestackforum).
>

## <a name="view-alerts"></a>Zobrazení upozornění

Seznam aktivních výstrah pro každou oblast centra Azure Stack je k dispozici přímo v okně **Správa oblastí** . První dlaždice ve výchozí konfiguraci je dlaždice **výstrahy** , která zobrazuje souhrn kritických výstrah a upozornění pro oblast. Dlaždici výstrahy můžete připnout jako jakoukoli jinou dlaždici v tomto okně na řídicí panel pro rychlý přístup.

![Dlaždice výstrahy, která zobrazuje upozornění na portálu pro správu Azure Stack hub](media/azure-stack-monitor-health/image3.png)

 Chcete-li zobrazit seznam všech aktivních výstrah pro danou oblast, vyberte horní část dlaždice **výstrahy** . Chcete-li zobrazit filtrovaný seznam výstrah (kritická nebo upozornění), vyberte buď položku **kritického** nebo **varovného** řádku v rámci dlaždice.

Okno **výstrahy** podporuje možnost filtrovat stav (aktivní nebo Uzavřeno) a závažnost (kritická nebo varovná). Ve výchozím zobrazení se zobrazí všechny aktivní výstrahy. Všechny uzavřené výstrahy se ze systému po sedmi dnech odeberou.

>[!Note]
>Pokud výstraha zůstane aktivní, ale během dne nebyla aktualizována, můžete spustit rutinu [test-AzureStack](azure-stack-diagnostic-test.md) a Zavřít výstrahu, pokud nejsou hlášeny žádné problémy.

![Podokno filtru pro filtrování podle kritického nebo varovného stavu na portálu pro správu centra Azure Stack](media/azure-stack-monitor-health/alert-view.png)

Akce **Zobrazit rozhraní API** zobrazuje REST API, která byla použita k vygenerování zobrazení seznamu. Tato akce poskytuje rychlý způsob, jak se seznámit se syntaxí REST API, kterou můžete použít k dotazování výstrah. Toto rozhraní API můžete použít ve službě Automation nebo pro integraci s vaším stávajícím řešením pro monitorování, generování sestav a vytváření lístků Datacenter.

Kliknutím na konkrétní výstrahu můžete zobrazit podrobnosti výstrahy. V podrobnostech výstrahy se zobrazují všechna pole, která jsou spojená s výstrahou, a umožňují rychlou navigaci na ovlivněnou součást a zdroj výstrahy. Například následující výstraha nastane, pokud jedna z instancí role infrastruktury přejde do režimu offline nebo není přístupná.  

![Okno Podrobnosti výstrahy na portálu pro správu centra Azure Stack](media/azure-stack-monitor-health/alert-detail.png)

## <a name="repair-alerts"></a>Opravit výstrahy

V některých upozorněních můžete vybrat možnost **opravit** .

Je-li tento příkaz vybrán, provede akce **opravit** kroky specifické pro výstrahu a pokuste se problém vyřešit. Po výběru je stav akce **opravit** k dispozici jako oznámení na portálu.

![Probíhá akce opravy výstrahy.](media/azure-stack-monitor-health/repair-in-progress.png)

Akce **opravit** bude hlásit úspěšné dokončení nebo neúspěšné dokončení akce v okně oznámení na portálu.  Pokud se akce opravy u výstrahy nezdařila, můžete znovu spustit akci **opravy** z podrobností výstrahy. Pokud se akce opravy úspěšně dokončí **, neprovádějte znovu** akci **opravit** .

![Akce opravy byla úspěšně dokončena.](media/azure-stack-monitor-health/repair-completed.png)

Po opětovném návratu instance role infrastruktury do režimu online se tato výstraha automaticky zavře. Mnohé, ale ne všechny výstrahy, se automaticky zavřou, když se vyřeší příslušný problém. Výstrahy, které poskytují tlačítko akce opravy, se automaticky zavřou, pokud Azure Stack centrum problém vyřeší. Pro všechny ostatní výstrahy vyberte **Zavřít výstrahu** po provedení nápravných kroků. Pokud se problém nevyřeší, Azure Stack centrum vygeneruje novou výstrahu. Pokud tento problém vyřešíte, výstraha zůstane zavřená a nevyžaduje žádné další kroky.

## <a name="next-steps"></a>Další kroky

[Správa aktualizací v centru Azure Stack](azure-stack-updates.md)

[Správa oblastí v centru Azure Stack](azure-stack-region-management.md)
