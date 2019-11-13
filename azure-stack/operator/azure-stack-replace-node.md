---
title: Nahrazení uzlu jednotky škálování v Azure Stack integrovaném systému | Microsoft Docs
description: Přečtěte si, jak nahradit uzel fyzické jednotky škálování na Azure Stack integrovaném systému.
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
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 0ac0a75e0986642020567ea554a4500cc051fd01
ms.sourcegitcommit: 102ef41963b5d2d91336c84f2d6af3fdf2ce11c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73955310"
---
# <a name="replace-a-scale-unit-node-on-an-azure-stack-integrated-system"></a>Nahrazení uzlu jednotky škálování v Azure Stack integrovaném systému

*Platí pro: Azure Stack integrovaných systémů*

Tento článek popisuje obecný proces nahrazení fyzického počítače (také označovaného jako uzel jednotky škálování) na Azure Stack integrovaný systém. Skutečný postup nahrazení uzlu jednotek škálování se bude lišit v závislosti na dodavateli hardwaru OEM (Original Equipment Manufacturer). Konkrétní podrobný postup pro váš systém najdete v dokumentaci k jednotce nahraditelné v terénu od vašeho dodavatele.

> [!CAUTION]  
> Úroveň firmwaru je zásadní pro úspěch operace popsané v tomto článku. Chybějící tento krok může vést k nestabilitě systému, poklesu výkonu, vláknům zabezpečení nebo zabránit automatizaci Azure Stack k nasazení operačního systému. Při nahrazování hardwaru vždy projděte dokumentaci k vašemu hardwarovému partnerovi, aby se zajistilo, že aplikovaný firmware odpovídá verzi OEM zobrazené na [portálu Azure Stack pro správu](azure-stack-updates.md).<br>
Další informace a odkazy na dokumentaci k partnerům najdete v tématu [Výměna hardwarové komponenty](azure-stack-replace-component.md).

Následující vývojový diagram znázorňuje proces obecného procesu FRU k nahrazení celého uzlu jednotky škálování.

![Vývojový diagram pro proces nahrazení uzlu](media/azure-stack-replace-node/replacenodeflow.png)

\* Tato akce se nemusí vyžadovat na základě fyzické podmínky hardwaru.

> [!Note]  
> Pokud operace vypnutí selže, doporučuje se použít operaci vyprázdnění následovanou operací zastavení. Další podrobnosti najdete v části dostupné operace uzlu.  

## <a name="review-alert-information"></a>Kontrola informací o výstrahách

Pokud je uzel jednotky škálování mimo provoz, obdržíte následující kritické výstrahy:

- Uzel není připojený k síťovému adaptéru
- Nedostupný uzel pro umístění virtuálního počítače
- Uzel jednotky škálování je offline

![Seznam výstrah pro jednotku škálování mimo provoz](media/azure-stack-replace-node/nodedownalerts.png)

Pokud otevřete **uzel jednotka škálování je offline** výstraha, popis výstrahy obsahuje uzel jednotka škálování, který je nepřístupný. Další výstrahy můžete také získat v řešení pro monitorování specifické pro výrobce OEM, které běží na hostiteli životního cyklu hardwaru.

![Podrobnosti výstrahy na offline uzlu](media/azure-stack-replace-node/nodeoffline.png)

## <a name="scale-unit-node-replacement-process"></a>Proces nahrazení uzlu jednotky škálování

Následující kroky jsou k dispozici v podobě vysokého přehledu o procesu nahrazení uzlu jednotky škálování. Podrobné pokyny, které jsou specifické pro váš systém, najdete v dokumentaci k prostředí FRU dodavatele hardwaru OEM. Neprovádějte tyto kroky bez odkazování na dokumentaci poskytovanou výrobcem OEM.

1. K bezproblémovému vypnutí uzlu jednotky škálování použijte akci **vypnutí** . Tato akce se nemusí vyžadovat na základě fyzické podmínky hardwaru. 

2. V nepravděpodobném případě se akce vypnutí nezdařila, pomocí akce [vyprázdnění](azure-stack-node-actions.md#drain) umístěte uzel jednotky škálování do režimu údržby. Tato akce se nemusí vyžadovat na základě fyzické podmínky hardwaru.

   > [!NOTE]  
   > V každém případě je možné zakázat a vypnout pouze jeden uzel ve stejnou dobu, aniž by došlo k přerušení S2D (Prostory úložiště s přímým přístupem).

3. Po dokončení uzlu jednotky škálování v režimu údržby použijte akci [zastavit](azure-stack-node-actions.md#stop) . Tato akce se nemusí vyžadovat na základě fyzické podmínky hardwaru.

   > [!NOTE]  
   > V nepravděpodobném případě akce vypnutí nefunguje, místo toho použijte webové rozhraní řadiče pro správu základní desky (BMC).

4. Nahraďte fyzický počítač. Obvykle to dělá dodavatel hardwaru OEM.
5. Pomocí akce [opravit](azure-stack-node-actions.md#repair) přidejte do jednotky škálování nový fyzický počítač.
6. Pomocí privilegovaného koncového bodu [Ověřte stav opravy virtuálního disku](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair-using-the-privileged-endpoint). S novými datovými jednotkami může úplná úloha opravy úložiště trvat několik hodin v závislosti na zatížení systému a spotřebovaném prostoru.
7. Po dokončení akce opravy ověřte, zda byly všechny aktivní výstrahy automaticky uzavřeny.

## <a name="next-steps"></a>Další kroky

- Informace o nahrazení fyzického disku v době, kdy je systém zapnutý, najdete v tématu [Výměna disku](azure-stack-replace-disk.md). 
- Informace o nahrazení hardwarové součásti, která vyžaduje vypnutí systému, najdete v tématu [Výměna hardwarové komponenty](azure-stack-replace-component.md).
