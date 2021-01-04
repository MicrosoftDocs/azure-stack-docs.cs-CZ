---
title: Přehled infrastruktury úložiště Azure Stack hub
titleSuffix: Azure Stack
description: Naučte se spravovat infrastrukturu úložiště pro centrum Azure Stack.
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: inhenkel
ms.lastreviewed: 5/5/2020
ms.reviewer: jiaha
ms.custom: contperf-fy20q4
ms.openlocfilehash: 8633e31ac60dfb091997657b02b633d24d39b29d
ms.sourcegitcommit: e13f27291bab236aac5d8b05401056961e9cc1e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2020
ms.locfileid: "97697603"
---
# <a name="azure-stack-hub-storage-infrastructure-overview"></a>Přehled infrastruktury úložiště Azure Stack hub

Tento článek poskytuje Azure Stack koncepty infrastruktury úložiště centra. Obsahuje informace o jednotkách a svazcích a způsobu jejich použití v centru Azure Stack.

## <a name="drives"></a>Drives

### <a name="drive-types"></a>Typy jednotek

Azure Stack partneři integrovaných systémů pro rozbočovače nabízejí řadu variant řešení, včetně široké škály flexibility úložiště. Můžete vybrat až **dva** typy jednotek ze tří podporovaných typů jednotek:

1. NVMe (non-volatile paměť Express)
1. SATA/SAS SSD (Solid-State Drive)
1. HDD (pevný disk).

### <a name="performance-vs-capacity"></a>Výkon vs – kapacita

Azure Stack hub používá pro clustering s podporou převzetí služeb při selhání Windows serveru Prostory úložiště s přímým přístupem (S2D). Tato kombinace poskytuje výkonné, škálovatelné a odolné služby úložiště.

Nasazení Azure Stack můžou maximalizovat výkon úložiště nebo vyvážit výkon a kapacitu.

Prostory úložiště s přímým přístupem používá mezipaměť k maximalizaci výkonu úložiště.

### <a name="how-drive-types-are-used"></a>Jak se používají typy jednotek

Když má zařízení centra Azure Stack jeden typ jednotky, pro kapacitu se použijí všechny jednotky.

Pokud existují dva typy jednotek, Prostory úložiště s přímým přístupem automaticky používá všechny jednotky typu "nejrychlejší" (NVMe &gt; SSD &gt; HDD) pro ukládání do mezipaměti. Zbývající jednotky se použijí k ukládání.

### <a name="all-flash-or-hybrid"></a>Vše – bliknutí nebo hybridní

Jednotky mohou být seskupeny do nasazení "all-Flash" nebo "hybrid".

Nasazení all-Flash se zaměřuje na maximalizaci výkonu úložiště a nezahrnují rotaci HDD.

![Diagram, který obsahuje seskupení nasazení all-Flash.](media/azure-stack-storage-infrastructure-overview/image1.png)


Hybridní nasazení mají za cíl vyvážit výkon a kapacitu nebo maximalizovat kapacitu a zahrnout rotační Hddy.

![Diagram, který zobrazuje seskupení hybridního nasazení.](media/azure-stack-storage-infrastructure-overview/image2.png)

### <a name="caching-behavior"></a>Chování při ukládání do mezipaměti

Chování mezipaměti je určeno automaticky na základě typů jednotek. Při ukládání do mezipaměti pro SSD (například ukládání do mezipaměti NVMe pro SSD) se do mezipaměti ukládají pouze zápisy. Tím se snižuje kapacita jednotek kapacity, což snižuje počet kumulovaných přenosů na kapacitní jednotky a prodlouží jejich dobu života.

Čtení nejsou uložená v mezipaměti. Nejsou ukládány do mezipaměti, protože čtení nemají významně vliv na životnost blesku a protože SSD univerzálně nabízí nízkou latenci čtení.

Při ukládání do mezipaměti pro HDD (jako je například ukládání do mezipaměti SSD pro HDD) jsou čtení i zápisy ukládány do mezipaměti, aby se zajistila latence s podobným rozhraním Flash (často/~ 10x) pro obojí.

![Diagram, který zobrazuje Azure Stack chování při ukládání do mezipaměti úložiště centra.](media/azure-stack-storage-infrastructure-overview/image3.svg)

K dispozici je konfigurace úložiště, kterou můžete vyhledat v Azure Stack partnerovi OEM ( https://azure.microsoft.com/overview/azure-stack/partners/) pro podrobnou specifikaci).

> [!NOTE]
> Zařízení centra Azure Stack se dá doručit v hybridním nasazení s jednotkami HDD i SSD (nebo NVMe). Jednotky rychlejšího typu se ale používají jako jednotky mezipaměti a všechny zbývající jednotky se jako fond používají jako jednotky kapacity. Data tenanta (objekty blob, tabulky, fronty a disky) se umístí na jednotky kapacity. Zřizování prémiových disků nebo výběr typu účtu Premium Storage nezaručí, že se objekty přidělují na jednotky SSD nebo NVMe.

## <a name="volumes"></a>Svazky

*Služba úložiště* rozdělí dostupné úložiště na samostatné svazky, které jsou přiděleny k uchování dat systému a klienta. Svazky spojují jednotky ve fondu úložiště, aby poskytovaly odolnost proti chybám, škálovatelnost a výkonnostní výhody Prostory úložiště s přímým přístupem.

![Diagram, který zobrazuje Azure Stack oddíly služby úložiště centra](media/azure-stack-storage-infrastructure-overview/image4.svg)

### <a name="volume-types"></a>Typy svazků

Existují tři typy svazků, které jsou vytvořené ve fondu úložiště Azure Stack hub:

1. Na svazcích **infrastruktury** se soubory hostují v Azure Stack virtuálních počítačích infrastruktury centra a v základních službách.
1. Dočasné svazky **virtuálního počítače** hostují dočasné disky připojené k virtuálním počítačům tenanta a tato data jsou uložená na těchto discích.
1. **Úložiště objektů** : svazky hostují objekty blob, tabulky, fronty a disky virtuálních počítačů, které jsou v úložišti.

### <a name="volumes-in-a-multi-node-deployment"></a>Svazky v nasazení s více uzly

V nasazení s více uzly jsou k dispozici tři svazky infrastruktury.

Počet dočasnou svazky virtuálního počítače a svazky úložiště objektů se rovná počtu uzlů v nasazení centra Azure Stack:

- Při nasazení se čtyřmi uzly jsou k dispozici čtyři stejné dočasné svazky virtuálních počítačů a čtyři stejné svazky úložiště objektů.

- Pokud do clusteru přidáte nový uzel, bude pro oba typy vytvořen nový svazek.

- Počet svazků zůstává stejný i v případě, že uzel nefunguje nebo je odebraný.

> [!NOTE]
> Pokud používáte [Azure Stack Development Kit (ASDK)](../asdk/index.yml), existuje jeden svazek s více [sdílenými](azure-stack-manage-storage-shares.md)složkami.

### <a name="fault-tolerance-and-mirroring"></a>Odolnost proti chybám a zrcadlení

Svazky v Prostory úložiště s přímým přístupem poskytují odolnost proti problémům s hardwarem, jako je například selhání disku nebo serveru. Umožňují nepřetržitou dostupnost v rámci údržby serveru, jako jsou aktualizace softwaru.

Zrcadlení zajišťuje odolnost proti chybám tím, že udržuje více kopií všech dat. Způsob, jakým jsou data rozložená a umístěná, jsou netriviální, ale veškerá data uložená pomocí zrcadlení se v celém rozsahu zapisují několikrát. Každá kopie je zapsána na jiný fyzický hardware (různé jednotky na různých serverech), u kterých se předpokládá nezávisle na selhání. 

Nasazení centra Azure Stack používá k zajištění odolnosti dat trojrozměrné zrcadlení. Třícestný zrcadlení může bezpečně tolerovat alespoň dva problémy s hardwarem (na disku nebo na serveru). Pokud například restartujete jeden server, když dojde k výpadku jiné jednotky nebo serveru, všechna data zůstanou bezpečná a nepřetržitě dostupná.

Tři kopie dat tenanta se zapisují na různé servery, kde se nacházejí v mezipaměti:

![Diagram, který ukazuje, jak jsou tři kopie dat tenanta zapisovány na různé servery.](media/azure-stack-storage-infrastructure-overview/image5.png)

## <a name="next-step"></a>Další krok

[Správa kapacity úložiště](azure-stack-manage-storage-shares.md) 
