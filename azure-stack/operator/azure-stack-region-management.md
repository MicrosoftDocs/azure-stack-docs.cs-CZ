---
title: Správa oblastí v Azure Stack | Microsoft Docs
description: Přehled správy oblastí v Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: e94775d5-d473-4c03-9f4e-ae2eada67c6c
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/13/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 11/27/2018
ms.openlocfilehash: da80b144987e1dd90e45e83bb7be4ed581db143e
ms.sourcegitcommit: aefcf9c61bd8089a0aaa569af7643e5e15f4947c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68991764"
---
# <a name="region-management-in-azure-stack"></a>Správa oblastí v Azure Stack

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Azure Stack používá koncept *oblastí*, které jsou logickými entitami sestávající z hardwarových prostředků tvořících Azure Stack infrastrukturu. V části Správa oblastí můžete najít všechny prostředky, které jsou potřeba k úspěšnému provozování infrastruktury Azure Stack.

Jediné nasazení integrovaného systému (označované jako *cloud Azure Stack*) tvoří jednu oblast. Každý Azure Stack Development Kit (ASDK) má jednu oblast s názvem **Local**. Pokud nasadíte druhý Azure Stack integrovaný systém nebo nastavíte jinou instanci ASDK na samostatném hardwaru, bude tento Azure Stack Cloud jinou oblastí.

## <a name="information-available-through-the-region-management-tile"></a>Informace dostupné prostřednictvím dlaždice Správa oblastí

Azure Stack má na dlaždici **Správa oblastí** k dispozici sadu možností správy oblastí. Tato dlaždice je k dispozici pro operátor Azure Stack na výchozím řídicím panelu na portálu pro správu. Prostřednictvím této dlaždice můžete monitorovat a aktualizovat svou Azure Stackovou oblast a její součásti, které jsou specifické pro oblast.

![Dlaždice správy oblastí](media/azure-stack-region-management/image1.png)

Pokud kliknete na oblast v dlaždici **Správa oblastí** , můžete získat přístup k následujícím informacím:

[![Popis podoken v okně Správa oblastí](media/azure-stack-region-management/regionssm.png "Okno správy oblastí")](media/azure-stack-region-management/regions.png#lightbox)

1. **Nabídka prostředků**: Přístup ke konkrétním oblastem správy infrastruktury a zobrazení a Správa uživatelských prostředků, jako jsou účty úložiště a virtuální sítě.

2. **Výstrahy**: Vypíše výstrahy na úrovni systému a poskytuje podrobnosti o jednotlivých výstrahách.

3. **Aktualizace**: Zobrazit aktuální verzi infrastruktury Azure Stack, dostupné aktualizace a historii aktualizací. Můžete také aktualizovat integrovaný systém.

4. **Poskytovatelé prostředků**: Spravujte uživatelské funkce nabízené součástmi, které jsou nutné ke spuštění Azure Stack. Každý poskytovatel prostředků se dodává s prostředím pro správu. Toto prostředí může obsahovat výstrahy pro konkrétního poskytovatele, metriky a další možnosti správy, které jsou specifické pro poskytovatele prostředků.

5. **Role infrastruktury**: Komponenty nezbytné ke spuštění Azure Stack. V seznamu se zobrazí jenom role infrastruktury, na které se hlásí výstrahy. Výběrem role můžete zobrazit výstrahy spojené s rolí a instancemi rolí, na kterých je tato role spuštěná.

6. **Vlastnosti**: Stav registrace a podrobnosti vašeho prostředí v okně Správa oblastí. Stav lze **zaregistrovat**, **není**zaregistrováno nebo **vypršela jeho platnost**. Pokud je zaregistrované, zobrazí se také ID předplatného Azure, které jste použili k registraci Azure Stack, spolu se skupinou prostředků registrace a názvem.

## <a name="next-steps"></a>Další kroky

- [Monitorování stavu a upozornění ve službě Azure Stack](azure-stack-monitor-health.md)
- [Správa aktualizací ve službě Azure Stack](azure-stack-updates.md)
