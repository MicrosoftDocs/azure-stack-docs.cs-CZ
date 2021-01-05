---
title: Virtuální počítače GPU v Azure Stack | Microsoft Docs
description: Referenční informace o výpočetním procesoru GPU v Azure Stack.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/02/2020
ms.author: patricka
ms.reviewer: kivenkat
ms.lastreviewed: 01/02/2020
ms.openlocfilehash: 2b941c47b9c9662998b8d55bc6878e935969d1a4
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97872530"
---
# <a name="gpu-vms-on-azure-stack"></a>Virtuální počítače GPU na Azure Stack 

*Platí pro: Azure Stack integrovaných systémů* 

Toto téma popisuje, jak spravovat virtuální počítače GPU v centru Azure Stack.


## <a name="partitioned-gpu-vm-size"></a>Velikost virtuálního počítače s děleným procesorem GPU 

Virtuální počítače řady NVv4-Series využívají GPU technologie [AMD Radeon Instinct MI25](https://www.amd.com/en/products/professional-graphics/instinct-mi25) . Pomocí centra Azure Stack řady NVv4-Series Představujeme virtuální počítače s využitím částečných procesorů GPU. Tato velikost se dá použít pro akcelerované grafické aplikace GPU a virtuální plochy. Virtuální počítače s NVv4 v současné době podporují jenom hostovaný operační systém Windows. 

| Velikost | Virtuální procesory | Paměť: GiB | Dočasné úložiště (SSD): GiB | GPU | Paměť GPU: GiB | Max. datových disků | Maximální počet síťových karet | 
| --- | --- | --- | --- | --- | --- | --- | --- |   
| Standard_NV4as_v4 |4 |14 |88 | 1/8 | 2 | 4 | 2 | 

## <a name="patch-and-update-fru-behavior-of-vms"></a>Oprava a aktualizace, chování funkce FRU virtuálních počítačů 

Virtuální počítače GPU budou podléhat výpadkům během operací, jako je třeba patch and Update (PnU), a také nahrazení hardwaru (FRU) centra Azure Stack. Následující tabulka přechází do stavu virtuálního počítače, jak se zjistila během těchto aktivit, a také ruční akci, kterou může uživatel udělat, aby tyto virtuální počítače znovu vyúčtovaly tyto operace. 

| Operace | PnU – expresní aktualizace | PnU – úplná aktualizace, OEM Update | JEDNOTKA | 
| --- | --- | --- | --- | 
| Stav virtuálního počítače  | Během a po aktualizaci bez operace ručního spuštění není k dispozici. | Během aktualizace není k dispozici. Dostupná aktualizace po aktualizaci s ruční operací | Během aktualizace není k dispozici. Dostupná aktualizace po aktualizaci s ruční operací| 
| Ruční operace | Pokud je potřeba virtuální počítač zpřístupnit během aktualizace, pokud jsou dostupné oddíly GPU, můžete virtuální počítač restartovat z portálu kliknutím na tlačítko restartovat. Virtuální počítač je potřeba restartovat po aktualizaci z portálu pomocí tlačítka "restartovat". | Virtuální počítač nejde během aktualizace zpřístupnit. Po dokončení aktualizace je potřeba virtuální počítač zastavit a zrušit jeho přidělení pomocí tlačítka zastavit a spustit zálohování pomocí tlačítka Spustit. | Virtuální počítač nejde během aktualizace zpřístupnit. Po dokončení aktualizace je potřeba virtuální počítač zastavit a zrušit jeho přidělení pomocí tlačítka zastavit a spustit zálohování pomocí tlačítka Spustit.| 

## <a name="guest-driver-installation"></a>Instalace ovladače hosta 

[Tento](https://docs.microsoft.com/azure/virtual-machines/windows/n-series-amd-driver-setup) dokument překročí nastavení ovladače hosta AMD v rámci virtuálního počítače s povolenou technologií NVv4 GPU – P s postupem, jak ověřit instalaci ovladače. 

## <a name="next-steps"></a>Další kroky 

[Funkce Azure Stack virtuálního počítače](azure-stack-vm-considerations.md) 
