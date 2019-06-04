---
title: Plánování výpočetní kapacity Azure Stack | Dokumentace Microsoftu
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
ms.openlocfilehash: e549413798ffc3c06c95bfbcf50ab4929ffeaf63
ms.sourcegitcommit: 80775f5c5235147ae730dfc7e896675a9a79cdbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/03/2019
ms.locfileid: "66461022"
---
# <a name="azure-stack-compute"></a>Výpočetní prostředky Azure Stack

[Velikosti virtuálních počítačů](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes) podporované ve službě Azure Stack, jsou podmnožinou, které jsou podporovány v Azure. Azure má omezení prostředků podél mnoho vektorů, aby overconsumption prostředků (server místní a úrovni služeb). Bez uložení některá omezení na prostředky spotřebované klienty, sníží tenanta prostředí při jiných tenantů overconsume prostředky. Pro sítě odchozího přenosu dat z virtuálního počítače existují omezení šířky pásma na místě ve službě Azure Stack, odpovídající omezení Azure. Pro prostředky úložiště limity vstupně-výstupních operací úložiště je implementovaná v Azure stacku, aby se zabránilo základní overconsumption prostředků tenantů pro přístup k úložišti.

>[!IMPORTANT]
>[Azure Stack Capacity Planner](https://aka.ms/azstackcapacityplanner) vezměte v úvahu nebo zaručí výkon vstupně-výstupních operací.

## <a name="vm-placement"></a>Umístění virtuálního počítače

Ve službě Azure Stack umístění virtuálního počítače klienta se provádí automaticky modulem umístění napříč dostupných hostitelů. Pouze dvě okolnosti při umísťování virtuálních počítačů se, zda je dostatek paměti na hostiteli pro tento typ virtuálního počítače a virtuální počítače jsou součástí [dostupnosti](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) nebo jsou [škálovací sady virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).  

Abyste dosáhli vysoké dostupnosti systému produkčního prostředí více virtuálních počítačů ve službě Azure Stack, jsou virtuální počítače umístěné ve skupině dostupnosti, který se šíří mezi více domén selhání. Doména selhání ve skupině dostupnosti je definován jako jeden uzel v jednotce škálování. Azure Stack podporuje s dostupnosti s délkou maximálně tři domény selhání pro zajištění konzistence s Azure. Virtuální počítače umístěné ve skupině dostupnosti budou fyzicky izolované od sebe navzájem tím, že rozprostírá co nejrovnoměrněji rozložené přes víc domén selhání, to znamená hostitelů Azure Stack. Pokud dojde k selhání hardwaru, virtuálních počítačů z neúspěšných doména bude být restartování v jiných doménách selhání, ale pokud je to možné udržovat v samostatných doménách selhání z jiných virtuálních počítačů ve stejné sadě dostupnosti. Když hostitel přejde do režimu online, virtuálních počítačů bude možné znovu vyrovnána udržet vysokou dostupnost.  

Škálovací sady virtuálních počítačů pomocí sad dostupnosti na zadní ukončit a ujistěte se, že instance každého virtuálního počítače škálovací sady je umístěn v různých chybových domény. To znamená, že používají samostatné infrastruktury uzly služby Azure Stack. Například ve 4 uzly systémech pro Azure Stack mohou existovat situace, ve kterém se nezdaří počet 3 instancí škálovací sady virtuálních počítačů při vytváření z důvodu nedostatku kapacity 4 uzly umístit 3 instancí škálovací sady virtuálních počítačů na 3 samostatných uzlech služby Azure Stack. Kromě toho můžete uzlů Azure Stack vyplněné nahoru na různých úrovních před pokusu o umístění. 

Azure Stack není over-pass-the potvrdit paměť. Útoky over-pass-the potvrzení počet fyzických jader je však povoleno. Protože umístění algoritmy nevypadají v existujících virtuálních a fyzických jader over-pass-the zřizování poměr jako faktor, každý hostitel může mít různý poměr. Jako Microsoft neposkytujeme pokyny na fyzický virtuální jádrům kvůli kolísání úloh a požadavky na úroveň služby. 

## <a name="azure-stack-memory"></a>Azure Stack paměti 

Azure Stack je navržena pro zachovat virtuální počítače spuštěné, které po úspěšném zřízení. Například pokud je hostitel v režimu offline z důvodu selhání hardwaru, Azure Stack se pokusí restartování tohoto virtuálního počítače na jiného hostitele. Druhý příklad je opravy a aktualizace softwaru Azure Stack. Pokud je potřeba restartování fyzického hostitele, je proveden pokus o přesunutí virtuálních počítačů, spouštění na tomto hostiteli na jiného hostitele k dispozici v řešení.   

Tato Správa virtuálních počítačů nebo přesun jde dosáhnout jenom pokud existuje vyhrazené paměti kapacitu umožňující dojde k restartování nebo migrace. Část paměti celkový počet hostitelů je rezervována a není k dispozici pro umístění virtuálního počítače tenanta. 

Můžete zkontrolovat výsečový graf v portálu pro správu, který zobrazuje volné a využité paměti v Azure stacku. Následující diagram znázorňuje kapacita fyzické paměti na jednotce škálování Azure Stack ve službě Azure Stack:

![Kapacita fyzické paměti](media/azure-stack-capacity-planning/physical-memory-capacity.png)

Využitá paměť se skládá z několika komponent. Následující komponenty využití paměti v části Použití výsečového grafu.  

- Používání hostitelský operační systém nebo rezervy – to je paměti používané podle operačního systému (OS) na hostitele, tabulky stránky virtuální paměti, procesy, které jsou spuštěny na hostitelském operačním systému a prostorů s přímým přístupem mezipaměti. 
- Služby infrastruktury – jedná se o infrastrukturu virtuálních počítačů, které tvoří Azure Stack. Od verze 1902 verzi služby Azure Stack, to znamená 31 virtuálních počítačů, které zabírají 242 GB + (4 GB × počet uzlů). Tato struktura interní služba umožní pro budoucí zavlečení nových služeb infrastruktury, jako jsou vyvíjeny.
- Odolnost proti chybám rezervy – Azure Stack si vyhrazuje část paměti umožňující tenanta dostupnosti při selhání jednoho hostitele, a také opravy a aktualizace umožňující úspěšné migrace za provozu virtuálních počítačů. 
- Virtuální počítače tenanta – jedná se o klientské virtuální počítače vytvořené uživateli Azure stacku. Kromě spouštění virtuálních počítačů, paměť je využívána všechny virtuální počítače, které jste dostali v prostředcích infrastruktury. To znamená, že virtuální počítače v **vytváření** nebo **neúspěšné** stavu nebo virtuální počítače vypnout v hostovaném počítači, bude využití paměti. Virtuální počítače, které bylo uvolněno pomocí zastavení navrácena možnost však nebude využívat paměti ze služby Azure Stack. 

Nejlepší způsob, jak porozumět využití paměti na portálu je použít [Azure Stack Capacity Planner](https://aka.ms/azstackcapacityplanner) zobrazení dopadů různé úlohy. Následující výpočtu je stejný jako ten používá plánovač.

Tento výpočet je výsledkem celkovou a dostupnou paměť, která lze použít pro umístění virtuálního počítače tenanta. Tato kapacita paměti je podkladové jednotka škálování služby Azure Stack. 


  Paměť k dispozici pro umístění virtuálního počítače = celková paměť hostitele – odolnost proti chybám rezervy - využité spuštěním klientské virtuální počítače – Azure Stack infrastruktury režii <sup>1</sup>

  Odolnost proti chybám rezervy = H + R * ((N-1) * H) + V * (N-2)

> Kde:
> - H = velikost paměti na jeden server
> - N = velikost z jednotky škálování (počet serverů)
> - R = operační systém rezervy pro operační systém režie, což je v tomto vzorci.15<sup>2</sup>
> - V = největší virtuální počítač v jednotce škálování

  <sup>1</sup> zatížení infrastruktury azure Stack = 242 GB + (4 GB × počet uzlů). Přibližně 31 virtuálních počítačů se používají k hostování infrastruktura Azure stacku a celkem, využívat přibližně 242 GB + (4 GB × počet uzlů) paměti a 146 virtuálních jader. Důvody pro tento počet virtuálních počítačů je oddělení potřebné služby podle zabezpečení, škálovatelnost, údržby a používání dílčích oprav požadavky splňují. Tato struktura interní služba umožní pro budoucí zavlečení nových služeb infrastruktury, jako jsou vyvíjeny. 

  <sup>2</sup> rezerva operačního systému pro režii = 15 % (. 15) paměti uzlu. Hodnotu rezervy operačního systému je odhad a budou lišit v závislosti na kapacita fyzické paměti serveru a režijní náklady na obecné operačního systému.


Hodnota V největší virtuální počítač v jednotce škálování dynamicky podle největší tenanta velikost paměti virtuálního počítače. Největší hodnota virtuálního počítače může být například, 7 GB nebo 112 GB nebo jakékoli jiné podporovanou velikost virtuálního počítače paměti v řešení služby Azure Stack. Změna největší virtuálního počítače v prostředcích infrastruktury Azure stacku způsobí zvýšení odolnosti proti chybám rezervy kromě nárůst objemu paměti Virtuálního počítače. 

> [!NOTE]
> Plánování požadavků na kapacitu pro sítě jsou minimální, jak je možné konfigurovat pouze velikost veřejných virtuálních IP adres. Informace o tom, jak přidat další veřejné IP adresy do služby Azure Stack najdete v tématu [přidání veřejné IP adresy](azure-stack-add-ips.md).

## <a name="next-steps"></a>Další postup
Další informace o [úložiště služby Azure Stack](azure-stack-capacity-planning-storage.md)