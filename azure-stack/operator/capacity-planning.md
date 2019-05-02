---
title: Plánování kapacity pro Azure Stack | Dokumentace Microsoftu
description: Další informace o plánování kapacity pro nasazení Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/29/2019
ms.author: jeffgilb
ms.reviewer: prchint
ms.lastreviewed: 03/29/2019
ms.openlocfilehash: 1a5bde916779961f9cc4c099b57324b8ddbd3bda
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2019
ms.locfileid: "64984489"
---
# <a name="azure-stack-capacity-planning"></a>Plánování kapacity v Azure stacku
Při vyhodnocování řešení s Azure Stack, existují možnosti konfigurace hardwaru, které mají přímý vliv na celkovou kapacitu cloudu Azure Stack. Jedná se o classic volby procesoru, paměti hustota, konfiguraci úložiště a celkové řešení škálování nebo počet serverů. Na rozdíl od tradiční virtualizaci řešení jednoduché aritmetické operace těchto komponent k určení využitelné kapacity se nevztahuje. Prvním důvodem je, že Azure Stack je navržený pro hostování součásti infrastruktury nebo správu v rámci vlastním řešením. Druhý důvodem je, že některé z kapacity řešení nebylo vyhrazeno podporu odolnost; aktualizace softwaru řešení tak, aby se minimalizovalo přerušení úlohy klientů.

> [!IMPORTANT]
> Informace o plánování kapacity zadaná a související tabulce, jsou určeny pouze jako vodítko pro vám pomůže zajistit plánování služby Azure Stack a rozhodnutí týkajících se konfigurace. Nejsou určeny k sloužit jako náhradu pro zkoumání a analýzy. 

## <a name="compute-and-storage-capacity-planning"></a>Výpočty a plánování kapacity úložiště
Řešení s Azure Stack je vytvořený jako cluster konvergovaný na hyper-compute, sítě a úložiště. To umožňuje efektivní použití nebo sdílení všechny kapacity hardwaru v clusteru, uvedené jako jednotka škálování pro Azure Stack, způsobem, který poskytuje dostupnost a škálovatelnost. Veškerý software infrastruktury hostována v rámci sady virtuálních počítačů (VM) a sdílí stejné fyzické servery jako virtuální počítače klientů. Spravuje všechny virtuální počítače jsou pak jednotka škálování technologie clusteringu Windows Server a jednotlivých instancí technologie Hyper-V. Tento přístup zjednodušuje pořízení a správu řešení Azure Stack a zajišťuje pro přesun a škálovatelnost všech služeb (tenantů a infrastruktury) v této jednotce škálování.

Jenom fyzický prostředek, který není nadměrně zřízený v řešení služby Azure Stack je paměť serveru. Další prostředky, procesoru jader, šířky pásma sítě a kapacitu úložiště, bude overprovisioned k co nejlíp využít dostupné prostředky. Při výpočtu dostupnou kapacitu pro řešení, je fyzický server paměti hlavního přispěvatele. Využití dalších prostředků je pak pochopení poměr zřizuje, který je možné a jaká bude akceptovatelná podle předpokládané úlohy.

Přibližně 30 virtuálních počítačů se používají k hostování infrastruktura Azure stacku a celkem, využívat přibližně 230 GB paměti a 140 virtuálních jader. Důvody pro tento počet virtuálních počítačů je oddělení potřebné služby podle zabezpečení, škálovatelnost, údržby a používání dílčích oprav požadavky splňují. Tato struktura interní služba umožní pro budoucí zavlečení nových služeb infrastruktury, jako jsou vyvíjeny.

Umístění pro podporu automatickou aktualizaci všech fyzických serverů a softwarových komponent infrastruktury, nebo opravy a aktualizace, infrastruktury a uživatelský virtuální počítač nespotřebuje všechny paměťové prostředky v jednotce škálování. Množství z celkové paměti ve všech serverech jednotky škálování nepřidělené podporu řešení požadavky odolnosti proti chybám. Například při aktualizaci image Windows serveru fyzický server, virtuální počítače hostované na serveru se přesouvají jinde v rámci Škálovací jednotky během aktualizace Image Windows serveru na server. Po dokončení aktualizace se server restartuje a vrátí do úlohy údržby. Cílem pro opravy a aktualizace řešení pro Azure Stack je vyhnout se nutnosti zastavit hostované virtuální počítače. V uživatele, aby splnila tento cíl, je úplné minimálně jeden server kapacita paměti volné umožňující přesun virtuálních počítačů v jednotce škálování. Toto umístění a přesunu se vztahuje na virtuální počítače infrastruktury i virtuální počítače vytvořené ve jménu uživatele nebo tenanta služby Azure Stack řešení. Výsledky implementace konečné jsou takové, že velikost paměti, které jsou vyhrazené pro podporu potřebné přesun virtuálního počítače může být mnohem víc než jeden server kapacity z důvodu umístění výzvy při virtuální počítače mají různé požadavky na paměť. Další režii v kategorii využití paměti je, že instance serveru Windows, samotného. Instance základní operační systém pro každý server spotřebuje paměti pro operační systém a jeho virtuální stránky tabulek spolu s paměti používané při správě každý hostovaný virtuální počítač technologie Hyper-V.

Dostupná paměť je určeno kapacitu virtuálních počítačů. Virtuálních jader na fyzické procesory poměrech exemplify jak hustotu virtuálních počítačů se změní dostupné kapacity procesoru, pokud toto řešení je vytvořený pomocí větší počet fyzických jader (zvolili jiný procesoru). Totéž platí sady kapacitou úložiště a kapacity úložiště mezipaměti.

Vysvětlení pouze pro účely jsou výše uvedené příklady hustotu virtuálních počítačů. Existuje další složitosti ve výpočtech v technické podpory. Kontakt s Microsoftem nebo partnera řešení je potřeba ještě lépe pochopili možností plánování kapacity a kapacity výsledný, k dispozici.

Zbývající část Tato část popisuje požadavky na nasazení služby Azure Stack pro výpočetní prostředky a úložiště. Tyto informace slouží k získání základní principy, jaké součásti jsou povinné a jejich hodnoty minimální konfigurace. Další informace také poskytuje k popisu konfigurace řešení s ohledem na dostupnou kapacitu a potenciální omezení z hlediska schopností tenanta kapacitu a výkon systému.

> [!NOTE]
> Plánování požadavků na kapacitu pro sítě jsou minimální, jak je možné konfigurovat pouze velikost veřejných virtuálních IP adres. Informace o tom, jak přidat další veřejné IP adresy do služby Azure Stack najdete v části [přidání veřejné IP adresy](azure-stack-add-ips.md).


## <a name="next-steps"></a>Další postup
[COMPUTE plánování kapacity](capacity-planning-compute.md)
