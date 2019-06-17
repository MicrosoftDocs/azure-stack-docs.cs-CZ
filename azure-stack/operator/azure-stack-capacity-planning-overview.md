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
ms.openlocfilehash: 335e92fcdb2eadd8bebfbfd3fb2e3b31edd00734
ms.sourcegitcommit: b79a6ec12641d258b9f199da0a35365898ae55ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67131342"
---
# <a name="overview-of-azure-stack-capacity-planning"></a>Přehled plánování kapacity služby Azure Stack

Když vyhodnocujete vaše řešení s Azure Stack, vezměte v úvahu možnosti konfigurace hardwaru, které mají přímý vliv na celkové kapacity cloudu služby Azure Stack. 

Například musíte rozhodovat o procesoru, paměti hustota, konfiguraci úložiště a celkové řešení škálování nebo počet serverů. Na rozdíl od tradiční virtualizaci řešení jednoduché aritmetické operace těchto komponent k určení využitelné kapacity neplatí. Azure Stack je vytvořená tak být hostitelem komponent infrastruktury nebo správu v rámci vlastním řešením. Navíc některé kapacitu tohoto řešení je vyhrazený pro podporu odolnost proti chybám, aktualizace softwaru řešení tak, aby se minimalizovalo přerušení úlohy klientů. 

> [!IMPORTANT]
> Tato kapacita informace o plánování a [Azure Stack Capacity Planner](https://aka.ms/azstackcapacityplanner) jsou výchozím bodem pro rozhodnutí o plánování a konfigurace Azure Stack. Tyto informace nemají sloužit jako náhradu pro zkoumání a analýzy. Společnost Microsoft neposkytuje žádná vyjádření či záruky, vyjádřené nebo předpokládané, s ohledem na uvedené informace.
 
Řešení s Azure Stack je vytvořený jako hyperkonvergovaného clusteru výpočetních operací a úložiště. Konvergence umožňuje sdílení kapacitu hardwaru v clusteru, uvedené jako *jednotka škálování*. Jednotky škálování ve službě Azure Stack, poskytuje dostupnost a škálovatelnost prostředků. Jednotka škálování se skládá ze sady Azure Stack servery, označuje jako *hostitele*. Software infrastruktury hostována v rámci sady virtuálních počítačů (VM) a sdílí stejné fyzické servery jako virtuální počítače klientů. Všechny virtuální počítače Azure Stack spravuje pak jednotka škálování technologie clusteringu Windows Server a jednotlivých instancí technologie Hyper-V. 

Jednotka škálování zjednodušuje pořízení a správu služby Azure Stack. Jednotka škálování umožňuje také pro přesun a škálovatelnost všech služeb (tenantů a infrastruktury) ve službě Azure Stack. 

Další podrobnosti o jednotlivých součástech naleznete v následujících tématech:

- [Výpočetní prostředky Azure Stack](azure-stack-capacity-planning-compute.md)
- [Úložiště Azure Stack](azure-stack-capacity-planning-storage.md)
- [Capacity Planner služby Azure Stack](azure-stack-capacity-planner.md)
