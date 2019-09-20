---
title: Azure Stack COMPUTE pro plánování kapacity | Microsoft Docs
description: Přečtěte si o plánování kapacity pro Azure Stack nasazení.
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
ms.date: 07/16/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 06/13/2019
ms.openlocfilehash: 17136cbe86029f0ea776d8dc8860ff96c82c756e
ms.sourcegitcommit: ae79b8bea670ea854ed00e9998d45f6b47fc8347
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2019
ms.locfileid: "71142594"
---
# <a name="azure-stack-compute"></a>Azure Stack COMPUTE

[Velikosti virtuálních počítačů](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes) podporované v Azure Stack jsou podmnožinou těch, které jsou podporované v Azure. Azure ukládá omezení prostředků spolu s mnoha vektory, aby nedocházelo k přečerpání prostředků (místní server a úroveň služeb). Aniž byste museli zabírat některá omezení pro využití tenanta, prostředí klienta bude mít za následek, že budou prostředky využívaly i jiní klienti. U odchozích síťových přenosů z virtuálního počítače je na Azure Stack, která odpovídají omezením Azure, zavedena zakončení šířky pásma. U prostředků úložiště v Azure Stack se limity IOPS úložiště nevyhnout základní spotřebě prostředků od klientů po přístup k úložišti.

>[!IMPORTANT]
>[Capacity Planner Azure Stack](https://aka.ms/azstackcapacityplanner) nebere v úvahu ani nezaručuje výkon IOPS.

## <a name="vm-placement"></a>Umístění virtuálního počítače

Modul umístění Azure Stack umísťuje virtuální počítače klientů mezi dostupné hostitele.

Při umísťování virtuálních počítačů používá Azure Stack dvě předpoklady. Jeden je dostatek paměti na hostiteli pro daný typ virtuálního počítače. Jsou to virtuální počítače, které jsou součástí skupiny [dostupnosti](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) , nebo se jedná o služby [Virtual Machine Scale Sets](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).

Pokud chcete dosáhnout vysoké dostupnosti produkčního prostředí pro více virtuálních počítačů v Azure Stack, virtuální počítače se umístí do skupiny dostupnosti, která je rozšíří napříč více doménami selhání. Doména selhání ve skupině dostupnosti je definována jako jeden uzel v jednotce škálování. Azure Stack podporuje pro zajištění konzistence s Azure skupinu dostupnosti s maximálně třemi doménami selhání. Virtuální počítače, které jsou umístěné ve skupině dostupnosti, se fyzicky izolují od sebe navzájem, jak je rozšíříte tak, jak je to možné, do více domén selhání, to znamená Azure Stack hostitelů. Pokud dojde k selhání hardwaru, virtuální počítače z neúspěšné domény selhání se restartují v jiných doménách selhání, ale pokud je to možné, udržují se v samostatných doménách selhání z ostatních virtuálních počítačů ve stejné skupině dostupnosti. Když se hostitel vrátí zpátky do online režimu, virtuální počítače se znovu vyrovnávají, aby se zachovala vysoká dostupnost.  

Virtual Machine Scale Sets používá v back-endu skupiny dostupnosti a zajišťují, aby se všechny instance sady škálování virtuálních počítačů nastavily v jiné doméně selhání. To znamená, že používají samostatné uzly infrastruktury Azure Stack. Například ve čtyřech Azure Stack systému se může jednat o situaci, kdy sada škálování virtuálních počítačů se třemi instancemi selže při vytváření z důvodu nedostatku kapacity 4 uzly k umístění tří instancí sady škálování virtuálních počítačů na tři samostatné Azure Stack uzly. . Kromě toho je možné uzly Azure Stack vyplnit na různých úrovních před vyzkoušením umístění. 

Azure Stack nepřesný zápis paměti. Nepovolený počet fyzických jader je ale povolený. 

Vzhledem k tomu, že algoritmy umístění se nevztahují na stávající virtuální až fyzické poměry při zřizování jako faktor, může mít každý hostitel jiný poměr. Microsoft neposkytuje pokyny k poměru fyzického a základního jádra z důvodu variací úloh a požadavků na úroveň služeb. 

## <a name="consideration-for-total-number-of-vms"></a>Zvážení celkového počtu virtuálních počítačů 

Existují nové aspekty pro přesné plánování Azure Stack kapacity. S aktualizací 1901 (a všemi aktualizacemi, které se dokončí), teď existuje omezení celkového počtu virtuálních počítačů, které se dají vytvořit. Tento limit má být dočasný, aby nedošlo k nestabilitě řešení. Zdroj problému se stabilitou, s větším počtem virtuálních počítačů, se zabývá, ale konkrétní časová osa pro nápravu se ještě nezjistila. Nyní je limit počtu 60 virtuálních počítačů na jeden server s celkovým limitem řešení 700. Například limit 8 serverových Azure Stackch virtuálních počítačů by byl 480 (8 × 60). Pro řešení od 12 do 16 Azure Stack serveru by byl limit 700. Toto omezení se vytvořilo, takže se na razítku zachovávají všechny požadavky na výpočetní kapacitu, jako je třeba rezerva odolnosti a virtuální procesor a fyzický poměr, který by měl obsluha na razítku udržovat. Další informace najdete v nové verzi plánovače kapacity. 

V případě, že se dosáhlo limitu škálování virtuálního počítače, vrátí se následující chybové kódy jako výsledek: VMsPerScaleUnitLimitExceeded, VMsPerScaleUnitNodeLimitExceeded.

## <a name="considerations-for-deallocation"></a>Požadavky na zrušení přidělení

Když je virtuální počítač ve stavu _zrušeno přidělení_ , prostředky paměti se nepoužívají. To umožňuje umístit ostatní virtuální počítače do systému. 

Pokud se znovu spustí uvolněný virtuální počítač, využití paměti nebo přidělení se považuje za nový virtuální počítač umístěný do systému a spotřebovává se dostupná paměť. 

Pokud není dostupná žádná paměť, virtuální počítač se nespustí.

## <a name="azure-stack-memory"></a>Azure Stack paměti 

Azure Stack je navržený tak, aby byly virtuální počítače se systémem, které se úspěšně zřídily. Pokud je například hostitel offline z důvodu selhání hardwaru, Azure Stack se pokusí tento virtuální počítač restartovat na jiném hostiteli. Druhým příkladem je oprava a aktualizace softwaru Azure Stack. Pokud je nutné restartovat fyzického hostitele, je proveden pokus o přesunutí virtuálních počítačů spuštěných na tomto hostiteli do jiného dostupného hostitele v řešení.   

Tuto správu nebo pohyb virtuálních počítačů je možné dosáhnout, jenom pokud je kapacita rezervované paměti, která umožňuje restartování nebo migraci. Část celkové paměti hostitele je vyhrazena a není k dispozici pro umístění virtuálního počítače tenanta. 

Výsečový graf můžete zkontrolovat na portálu pro správu, který zobrazuje volnou a využitou paměť v Azure Stack. Následující diagram znázorňuje kapacitu fyzické paměti na Azure Stack jednotce škálování v Azure Stack:

![Kapacita fyzické paměti](media/azure-stack-capacity-planning/physical-memory-capacity.png)

Použitá paměť se skládá z několika součástí. Následující komponenty využívají paměť v části použití výsečového grafu:  

 -  Využití nebo rezervace operačního systému hostitele – Toto je paměť využívaná operačním systémem (OS) na hostiteli, tabulkách stránky virtuální paměti, procesy, které jsou spuštěny v hostitelském operačním systému, a s mezipamětí mezipaměti s přímým přístupem. Vzhledem k tomu, že tato hodnota závisí na paměti používané různými procesy technologie Hyper-V běžícími na hostiteli, může to kolísat.
 - Služby infrastruktury – jedná se o virtuální počítače infrastruktury, které tvoří Azure Stack. Od verze 1904 Release Azure Stack to zahrnuje ~ 31 virtuálních počítačů, které zabírají 242 GB + (4 GB × počet uzlů) paměti. Využití paměti komponent služby infrastruktury se může při práci se změnou škálovatelnosti a odolnosti pro naši službu infrastruktury změnit.
 - Rezerva odolnosti – Azure Stack vyhrazuje část paměti, která umožňuje dostupnost tenanta během jednoho selhání hostitele a také během opravy a aktualizace, aby bylo umožněno úspěšné migrace virtuálních počítačů za provozu.
 - Virtuální počítače klienta – jedná se o klientské virtuální počítače vytvořené Azure Stack uživateli. Navíc k běžícím virtuálním počítačům je paměť spotřebovaná všemi virtuálními počítači, které se proložily v prostředcích infrastruktury. To znamená, že virtuální počítače ve stavu "vytvoření" nebo "selhání" nebo virtuální počítače, které se vypnou z hosta, budou spotřebovávat paměť. Virtuální počítače, které se nastavily pomocí možnosti zastavit navráceno z portálu nebo PowerShellu nebo CLI, ale nebudou spotřebovávat paměť z Azure Stack.
 - RPs doplňků – virtuální počítače nasazené pro doplněk RPs, jako je SQL, MySQL, App Service atd.


Nejlepším způsobem, jak pochopit využití paměti na portálu, je použití [Capacity Planner Azure Stack](https://aka.ms/azstackcapacityplanner) k zobrazení dopadu různých úloh. Následující výpočet je stejný jako ten, který používá Planner.

Výsledkem tohoto výpočtu je celková dostupná paměť, která se dá použít pro umístění virtuálního počítače tenanta. Tato kapacita paměti je za celou Azure Stack jednotku škálování. 


  Dostupná paměť pro umístění virtuálního počítače = celková paměť hostitele – Rezervovaná paměť – paměť využitá běžícími virtuálními počítači klienta – Azure Stack režijních nákladů na infrastrukturu <sup>1</sup>

  Rezerva odolnosti = H + R * (N-1) * H) + V * (N-2)

> Kde:
> - H = velikost paměti s jedním serverem
> - N = velikost jednotky škálování (počet serverů)
> - R = rezerva operačního systému pro režijní náklady na operační systém, který je .15 v tomto vzorci<sup>2</sup>
> - V = největší virtuální počítač v jednotce škálování

  <sup>1</sup> Azure Stack režie infrastruktury = 242 GB + (4 GB x # uzlů). K hostování infrastruktury Azure Stack se používá přibližně 31 virtuálních počítačů a v celkovém počtu se spotřebují přibližně 242 GB + (4 GB x # z uzlů) paměti a 146 virtuálních jader. Pro tento počet virtuálních počítačů je nutné, aby splňovaly potřebné oddělení služeb pro splnění požadavků na zabezpečení, škálovatelnost, údržbu a opravy. Tato interní struktura služby umožňuje budoucí zavádění nových služeb infrastruktury, když jsou vyvíjené. 

  <sup>2</sup> rezerva operačního systému pro režii = 15% (. 15) paměti uzlu. Hodnota rezervovaného operačního systému je odhad a bude se lišit v závislosti na kapacitě fyzické paměti serveru a celkové režii operačního systému.


Hodnota V, největší virtuální počítač v jednotce škálování, je dynamicky založená na největší velikosti paměti virtuálního počítače tenanta. Například největší hodnota virtuálního počítače může být 7 GB nebo 112 GB nebo jakákoli jiná podporovaná velikost paměti virtuálního počítače v řešení Azure Stack. Změna největšího virtuálního počítače v Azure Stack prostředcích infrastruktury způsobí navýšení rezervy na odolnost proti zvýšení velikosti paměti samotného virtuálního počítače. 

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**OTÁZKA**: Můj tenant nasadil nový virtuální počítač, jak dlouho bude trvat, než se v grafu schopností na portálu pro správu zobrazí zbývající kapacita?

**A**: Okno kapacity se aktualizuje každých 15 minut, proto prosím Vezměte v úvahu.

**OTÁZKA**: Počet nasazených virtuálních počítačů v mém Azure Stack se nezměnil, ale kapacita se pohybuje. Proč?

**A**: Dostupná paměť pro umístění virtuálního počítače má několik závislostí, jedna z nich je rezerva hostitelského operačního systému. Tato hodnota závisí na paměti používané různými procesy technologie Hyper-V spuštěné na hostiteli, což není konstantní hodnota.

**OTÁZKA**: Jaký stav mají virtuální počítače tenanta pro využívání paměti?

v: Navíc k běžícím virtuálním počítačům je paměť spotřebovaná všemi virtuálními počítači, které se proložily v prostředcích infrastruktury. To znamená, že virtuální počítače, které jsou v rámci "vytváření", "neúspěšné" nebo virtuální počítače vycházející z hosta, na rozdíl od zrušení přidělení z portálu/PowerShell/CLI budou spotřebovávat paměť.

**OTÁZKA**: Mám čtyři Azure Stack hostitele. Můj tenant má 3 virtuální počítače, které každý z nich spotřebovává 56 GB RAM (D5_v2). U jednoho z virtuálních počítačů se změní velikost na 112 GB RAM (D14_v2) a k dispozici je generování sestav paměti na řídicím panelu s výsledkem špičky 168 GB v okně kapacity. Následná změna velikosti dalších dvou D5_v2 virtuálních počítačů na D14_v2, výsledkem je pouze 56 GB paměti RAM. Proč to jde?

**A**: Dostupná paměť je funkce rezervy odolnosti udržované Azure Stack. Rezerva odolnosti je funkce největší velikosti virtuálního počítače na Azure Stack razítku. Nejdřív je největší virtuální počítač na razítku 56 GB paměti. Když se změnila velikost virtuálního počítače, největší virtuální počítač na razítku se stal 112 GB paměti, což nejen zvýšilo paměť využitou virtuálním počítačem tenanta, ale také zvýšila rezervu odolnosti. Výsledkem bylo zvýšení 56 GB (56 GB až 112 GB zvýšení paměti virtuálního počítače klienta) + 112 GB zvýšení odolnosti paměti. Když se změnila velikost dalších virtuálních počítačů, největší velikost virtuálního počítače zůstala 112 GB virtuálního počítače, a proto se nemusela zvýšit žádná rezerva v důsledku odolnosti proti chybám. Zvýšení spotřeby paměti bylo pouze zvýšení paměti virtuálního počítače klienta (56 GB). 


> [!NOTE]
> Požadavky na plánování kapacity pro sítě jsou minimální, protože je konfigurovatelná jenom velikost veřejné virtuální IP adresy. Informace o tom, jak přidat další Veřejné IP adresy pro Azure Stack, najdete v tématu [přidání veřejné IP adresy](azure-stack-add-ips.md).

## <a name="next-steps"></a>Další kroky
Další informace o [Azure Stack Storage](azure-stack-capacity-planning-storage.md)
