---
title: Přehled plánování kapacity pro Azure Stack | Dokumentace Microsoftu
description: Další informace o plánování kapacity pro nasazení Azure Stack.
services: azure-stack
documentationcenter: ''
author: prchint
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 05/31/2019
ms.openlocfilehash: 0d2610d2082973f4ab255027cc299b5858660c58
ms.sourcegitcommit: 80775f5c5235147ae730dfc7e896675a9a79cdbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/03/2019
ms.locfileid: "66460989"
---
# <a name="overview-of-azure-stack-capacity-planning"></a>Přehled plánování kapacity služby Azure Stack

Při vyhodnocování řešení s Azure Stack, existují možnosti konfigurace hardwaru, které mají přímý vliv na celkovou kapacitu cloudu Azure Stack. Jedná se o classic volby procesoru, paměti hustota, konfiguraci úložiště a celkové řešení škálování nebo počet serverů. Na rozdíl od tradiční virtualizaci řešení jednoduché aritmetické operace těchto komponent k určení využitelné kapacity se nevztahuje. Prvním důvodem je, že Azure Stack je navržený pro hostování součásti infrastruktury nebo správu v rámci vlastním řešením. Druhý důvodem je, že některé z kapacity řešení nebylo vyhrazeno podporu odolnost proti chybám, aktualizace softwaru řešení tak, aby se minimalizovalo přerušení úlohy klientů. 

> [!IMPORTANT]
> Tato kapacita informace o plánování a [Azure Stack Capacity Planner](https://aka.ms/azstackcapacityplanner) jsou výchozím bodem pro rozhodnutí o plánování a konfigurace Azure Stack. Ale nemají sloužit jako náhradu pro zkoumání a analýzy. Společnost Microsoft neposkytuje žádná vyjádření či záruky, vyjádřené nebo předpokládané, s ohledem na uvedené informace.
 
Řešení s Azure Stack je vytvořený jako cluster konvergovaný na hyper-výpočetního výkonu a úložiště. Konvergence umožňuje sdílení kapacitu hardwaru v clusteru, uvedené jako *jednotka škálování*. Jednotky škálování ve službě Azure Stack, poskytuje dostupnost a škálovatelnost prostředků. Jednotka škálování se skládá ze sady Azure Stack servery, označuje jako *hostitele*. Software infrastruktury hostována v rámci sady virtuálních počítačů a sdílí stejné fyzické servery jako virtuální počítače klientů. Všechny virtuální počítače Azure Stack spravuje pak jednotka škálování technologie clusteringu Windows Server a jednotlivých instancí technologie Hyper-V. Jednotka škálování zjednodušuje pořízení a správu služby Azure Stack. Jednotka škálování umožňuje také pro přesun a škálovatelnost všech služeb (tenantů a infrastruktury) ve službě Azure Stack. 

Další podrobnosti o jednotlivých součástech naleznete v následujících tématech:

- [Výpočetní prostředky Azure Stack](azure-stack-capacity-planning-compute.md)
- [Úložiště Azure Stack](azure-stack-capacity-planning-storage.md)
- [Capacity Planner služby Azure Stack](azure-stack-capacity-planner.md)
