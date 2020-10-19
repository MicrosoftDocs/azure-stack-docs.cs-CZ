---
title: Přehled plánování kapacity pro centrum Azure Stack | Microsoft Docs
description: Přečtěte si o plánování kapacity pro Azure Stack nasazení centra.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/10/2020
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 01/10/2020
ms.openlocfilehash: a608a64de28104513cb708ef350eb4db672f5f65
ms.sourcegitcommit: e4e2cc6a68f02c3e856f58ca5ee51b3313c7ff8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92183046"
---
# <a name="overview-of-azure-stack-hub-capacity-planning"></a>Přehled plánování kapacity centra Azure Stack

Když vyhodnocujete řešení centra Azure Stack, zvažte volby konfigurace hardwaru, které mají přímý vliv na celkovou kapacitu cloudu Azure Stack hub. 

Například je třeba provést volby týkající se CPU, hustoty paměti, konfigurace úložiště a celkového rozsahu řešení nebo počtu serverů. Na rozdíl od tradičního řešení virtualizace neplatí jednoduché aritmetické operace s určením použitelné kapacity. Centrum Azure Stack je postavené na hostování infrastruktury nebo součástí správy v rámci samotného řešení. Část kapacity řešení je také vyhrazena pro zajištění odolnosti a aktualizace softwaru tohoto řešení způsobem, který minimalizuje přerušení zatížení klientů. 

> [!IMPORTANT]
> Tyto informace o plánování kapacity a [Capacity Planner centra Azure Stack](https://aka.ms/azstackcapacityplanner) jsou výchozím bodem pro rozhodování Azure Stack plánování a konfigurace centra. Tyto informace nemají sloužit jako náhrada za vaše vlastní šetření a analýzu. Společnost Microsoft neposkytuje žádné reprezentace ani záruky, vyjádřené ani odvozené, s ohledem na informace, které jsou zde k dispozici.
 
Řešení centra Azure Stack je postavené jako předem sblížený cluster výpočetních a úložných prostředků. Konvergence umožňuje sdílení kapacity hardwaru v clusteru, označované jako *jednotka škálování*. V Azure Stackovém centru poskytuje jednotka škálování dostupnost a škálovatelnost prostředků. Jednotka škálování se skládá ze sady Azure Stack hub serverů, které se označují jako *hostitelé*. Software infrastruktury je hostovaný v rámci sady virtuálních počítačů (VM) a sdílí stejné fyzické servery jako virtuální počítače klientů. Všechny virtuální počítače centra Azure Stack se pak spravují pomocí technologií Windows Server Clustering jednotky škálování a jednotlivých instancí Hyper-V. 

Jednotka škálování zjednodušuje Azure Stack centrum pro získávání a správu. Jednotka škálování také umožňuje přesun a škálovatelnost všech služeb (klientů a infrastruktury) v rámci centra Azure Stack. 

Následující témata obsahují další podrobnosti o jednotlivých součástech:

- [Výpočetní prostředí centra Azure Stack](../operator/azure-stack-capacity-planning-compute.md)
- [Azure Stack úložiště centra](../operator/azure-stack-capacity-planning-storage.md)
- [Capacity Planner centra Azure Stack](azure-stack-capacity-planner.md)

