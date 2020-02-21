---
title: Přehled plánování kapacity
titleSuffix: Azure Stack Hub
description: Přečtěte si o plánování kapacity pro Azure Stack nasazení centra.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 05/31/2019
ms.author: inhenkel
ms.reviewer: prchint
ms.lastreviewed: 05/31/2019
ms.openlocfilehash: 343f9a691cb37966c4a5b8cfc9932488749d967f
ms.sourcegitcommit: 97806b43314d306e0ddb15847c86be2c92ae001e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2020
ms.locfileid: "77510091"
---
# <a name="capacity-planning-for-azure-stack-hub-overview"></a>Přehled plánování kapacity pro Azure Stack centra

Když vyhodnocujete řešení centra Azure Stack, zvažte volby konfigurace hardwaru, které mají přímý vliv na celkovou kapacitu cloudu Azure Stack hub.

Například je třeba provést volby týkající se CPU, hustoty paměti, konfigurace úložiště a celkového rozsahu řešení nebo počtu serverů. Na rozdíl od tradičního řešení virtualizace neplatí jednoduché aritmetické operace s určením použitelné kapacity. Centrum Azure Stack je postavené na hostování infrastruktury nebo součástí správy v rámci samotného řešení. Některá kapacita řešení je také vyhrazena pro zajištění odolnosti proti chybám. Odolnost proti chybám je definovaná jako aktualizace softwaru řešení způsobem, aby se minimalizovalo přerušení zatížení klientů.

> [!IMPORTANT]
> Tyto informace o plánování kapacity a [Capacity Planner centra Azure Stack](https://aka.ms/azstackcapacityplanner) jsou výchozím bodem pro rozhodování Azure Stack plánování a konfigurace centra. Tyto informace nemají sloužit jako náhrada za vaše vlastní šetření a analýzu. Společnost Microsoft neposkytuje žádné reprezentace ani záruky, vyjádřené ani odvozené, s ohledem na informace, které jsou zde k dispozici.

Řešení centra Azure Stack je postavené jako předem sblížený cluster výpočetních a úložných prostředků. Konvergence umožňuje sdílení kapacity hardwaru v clusteru, označované jako *jednotka škálování*. V Azure Stackovém centru poskytuje jednotka škálování dostupnost a škálovatelnost prostředků. Jednotka škálování se skládá ze sady Azure Stack hub serverů, které se označují jako *hostitelé*. Software infrastruktury je hostovaný v rámci sady virtuálních počítačů (VM) a sdílí stejné fyzické servery jako virtuální počítače klientů. Všechny virtuální počítače centra Azure Stack se pak spravují pomocí technologií Windows Server Clustering jednotky škálování a jednotlivých instancí Hyper-V.

Jednotka škálování zjednodušuje nabývání a správu centra Azure Stack. Jednotka škálování také umožňuje přesun a škálovatelnost všech služeb (klientů a infrastruktury) v rámci centra Azure Stack.

Následující témata obsahují další podrobnosti o jednotlivých součástech:

- [Výpočetní prostředí centra Azure Stack](azure-stack-capacity-planning-compute.md)
- [Azure Stack úložiště centra](azure-stack-capacity-planning-storage.md)
- [Capacity Planner centra Azure Stack](azure-stack-capacity-planner.md)
