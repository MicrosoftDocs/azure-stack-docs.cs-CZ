---
title: Správa oblastí v centru Azure Stack
titleSuffix: Azure Stack Hub
description: Přehled správy oblastí v centru Azure Stack.
author: sethmanheim
ms.topic: article
ms.date: 12/15/2020
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 11/27/2019
ms.openlocfilehash: 86e449c9a825c80fa230d94948281c67e4756a25
ms.sourcegitcommit: a53ea4a28e715c80a99fa89e9d364bc4556558de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97577019"
---
# <a name="region-management-in-azure-stack-hub"></a>Správa oblastí v centru Azure Stack

Azure Stack hub používá koncept *oblastí*, což jsou logické entity skládající se z hardwarových prostředků tvořících infrastrukturu centra Azure Stack. V části Správa oblastí můžete najít všechny prostředky, které jsou potřeba k úspěšnému provozování infrastruktury centra Azure Stack.

Jediné nasazení integrovaného systému (označované jako *cloudové centrum Azure Stack*) tvoří jednu oblast. Každý Azure Stack Development Kit (ASDK) má jednu oblast s názvem **Local**. Pokud nasadíte druhý integrovaný systém Azure Stack hub nebo nastavíte jinou instanci ASDK na samostatný hardware, bude tento cloud Azure Stack hub jinou oblastí.

## <a name="information-available-through-the-region-management-tile"></a>Informace dostupné prostřednictvím dlaždice Správa oblastí

Centrum Azure Stack má na dlaždici **Správa oblastí** k dispozici sadu možností správy oblastí. Tato dlaždice je k dispozici pro operátor centra Azure Stack na výchozím řídicím panelu na portálu pro správu. Na této obrazovce můžete monitorovat a aktualizovat oblast centra Azure Stack a její součásti, které jsou specifické pro oblast.

![Dlaždice správy oblastí na portálu správce centra Azure Stack](media/azure-stack-region-management/image1.png)

Pokud vyberete oblast na dlaždici **Správa oblastí** , můžete získat přístup k následujícím informacím:

[![Popis podoken v okně Správa oblastí na portálu pro správu centra Azure Stack](media/azure-stack-region-management/regionssm.png "Okno Správa oblastí na portálu pro správu centra Azure Stack")](media/azure-stack-region-management/regions.png#lightbox)

1. **Nabídka prostředků**: přístup k různým oblastem správy infrastruktury a zobrazení a Správa uživatelských prostředků, jako jsou účty úložiště a virtuální sítě.

2. **Výstrahy**: vypíše výstrahy na úrovni systému a poskytne podrobnosti o jednotlivých výstrahách.

3. **Aktualizace**: zobrazit aktuální verzi infrastruktury centra Azure Stack, dostupné aktualizace a historii aktualizací. Můžete také aktualizovat integrovaný systém.

4. **Poskytovatelé prostředků**: Spravujte uživatelské funkce nabízené součástmi, které jsou potřebné ke spuštění centra Azure Stack. Každý poskytovatel prostředků se dodává s prostředím pro správu. Toto prostředí může obsahovat výstrahy pro konkrétního poskytovatele, metriky a další možnosti správy, které jsou specifické pro poskytovatele prostředků.

5. **Role infrastruktury**: komponenty nezbytné ke spuštění centra Azure Stack. V seznamu se zobrazí jenom role infrastruktury, na které se hlásí výstrahy. Výběrem role můžete zobrazit výstrahy spojené s rolí a instancemi rolí, na kterých je tato role spuštěná.

6. **Vlastnosti**: stav registrace a podrobnosti vašeho prostředí v okně Správa oblastí. Stav lze **zaregistrovat**, **není zaregistrováno** nebo **vypršela jeho platnost**. Pokud je zaregistrované, zobrazí se také ID předplatného Azure, které jste použili k registraci centra Azure Stack, spolu se skupinou prostředků registrace a názvem.

## <a name="next-steps"></a>Další kroky

- [Monitorování stavu a upozornění ve službě Azure Stack Hub](azure-stack-monitor-health.md)
- [Správa aktualizací ve službě Azure Stack Hub](azure-stack-updates.md)
