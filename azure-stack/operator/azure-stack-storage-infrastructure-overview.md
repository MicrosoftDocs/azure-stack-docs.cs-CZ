---
title: Přehled infrastruktury úložiště Azure Stack hub
titleSuffix: Azure Stack
description: Naučte se spravovat infrastrukturu úložiště pro centrum Azure Stack.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 5/5/2020
ms.author: inhenkel
ms.lastreviewed: 5/5/2020
ms.reviewer: jiaha
ms.openlocfilehash: de6da5b42a88d2c1f9689fe8c43d898bc7a0bf5e
ms.sourcegitcommit: c263a86d371192e8ef2b80ced2ee0a791398cfb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2020
ms.locfileid: "82848162"
---
# <a name="azure-stack-hub-storage-infrastructure-overview"></a>Přehled infrastruktury úložiště Azure Stack hub

Tento článek poskytuje přehled Azure Stack infrastruktury úložiště centra.

## <a name="understand-drives-and-volumes"></a>Principy jednotek a svazků

### <a name="drives"></a>Drives

Služba Azure Stack hub využívá software Windows serveru a umožňuje kombinovat možnosti úložiště s kombinací Prostory úložiště s přímým přístupem (S2D) a clusteringu s podporou převzetí služeb při selhání Windows serveru. Tato kombinace poskytuje výkonné, škálovatelné a odolné služby úložiště.

Azure Stack partneři integrovaných systémů pro rozbočovače nabízejí řadu variant řešení, včetně široké škály flexibility úložiště. V současné době můžete vybrat až dva typy jednotek ze tří podporovaných typů jednotek: NVMe (Non Volatile Memory Express), SATA/SAS SSD (Solid-State Drive), HDD (pevný disk). 

Prostory úložiště s přímým přístupem poskytuje mezipaměť pro maximalizaci výkonu úložiště. V zařízení centra Azure Stack s jedním typem jednotky (tj. NVMe nebo SSD) se pro kapacitu používají všechny jednotky. Pokud existují dva typy jednotek, Prostory úložiště s přímým přístupem automaticky používá všechny jednotky typu "nejrychlejší" (NVMe &gt; SSD &gt; HDD) pro ukládání do mezipaměti. Zbývající jednotky se použijí k ukládání. Jednotky mohou být seskupeny do nasazení "all-Flash" nebo "hybrid":

![Azure Stack infrastruktura úložiště centra](media/azure-stack-storage-infrastructure-overview/image1.png)

Nasazení all-Flash se zaměřuje na maximalizaci výkonu úložiště a nezahrnují rotaci HDD.

![Azure Stack infrastruktura úložiště centra](media/azure-stack-storage-infrastructure-overview/image2.png)

Hybridní nasazení mají za cíl vyvážit výkon a kapacitu nebo maximalizovat kapacitu a zahrnout rotační Hddy.

Chování mezipaměti je určeno automaticky na základě typů jednotek, které jsou ukládány do mezipaměti pro. Při ukládání do mezipaměti pro SSD (například ukládání do mezipaměti NVMe pro SSD) se do mezipaměti ukládají pouze zápisy. Tím se snižuje kapacita jednotek kapacity, což snižuje počet kumulovaných přenosů na kapacitní jednotky a prodlouží jejich dobu života. Do té doby se čtení neukládá do mezipaměti. Nejsou ukládány do mezipaměti, protože čtení nemají významně vliv na životnost blesku a protože SSD univerzálně nabízí nízkou latenci čtení. Při ukládání do mezipaměti pro HDD (jako je například ukládání do mezipaměti SSD pro HDD) jsou čtení i zápisy ukládány do mezipaměti, aby se zajistila latence s podobným rozhraním Flash (často/~ 10x) pro obojí.

![Azure Stack infrastruktura úložiště centra](media/azure-stack-storage-infrastructure-overview/image3.png)

K dispozici je konfigurace úložiště, kterou můžete vyhledat v Azure Stack partnerovi OEMhttps://azure.microsoft.com/overview/azure-stack/partners/) (pro podrobnou specifikaci).

> [!Note]  
> Zařízení centra Azure Stack lze doručovat v hybridním nasazení s jednotkami HDD i SSD (nebo NVMe). Jednotky rychlejšího typu se ale používají jako jednotky mezipaměti a všechny zbývající jednotky se jako fond používají jako jednotky kapacity. Data tenanta (objekty blob, tabulky, fronty a disky) se umístí na jednotky kapacity. Zřizování prémiových disků nebo výběr typu účtu Premium Storage nezaručí, že se objekty přidělují na jednotky SSD nebo NVMe.

### <a name="volumes"></a>Svazky

*Služba úložiště* rozdělí dostupné úložiště na samostatné svazky, které jsou přiděleny k uchování dat systému a klienta. Svazky spojují jednotky ve fondu úložiště, aby poskytovaly odolnost proti chybám, škálovatelnost a výkonnostní výhody Prostory úložiště s přímým přístupem.

![Azure Stack infrastruktura úložiště centra](media/azure-stack-storage-infrastructure-overview/image4.png)

Existují tři typy svazků, které jsou vytvořené ve fondu úložiště Azure Stack hub:

- Infrastruktura: hostitelské soubory používané Azure Stackmi virtuálními počítači infrastruktury centra a základními službami.

- Dočasné virtuální počítače: hostovat dočasné disky připojené k virtuálním počítačům tenanta a tato data jsou uložená na těchto discích.

- Úložiště objektů: objekty blob, tabulky, fronty a disky virtuálních počítačů obsluhy hostitelských dat klienta.

V nasazení s více uzly vidíte tři svazky infrastruktury, zatímco počet dočasnou svazky virtuálního počítače a svazky úložiště objektů se rovná počtu uzlů v nasazení centra Azure Stack:

- Při nasazení se čtyřmi uzly jsou k dispozici čtyři stejné dočasné svazky virtuálních počítačů a čtyři stejné svazky úložiště objektů.

- Pokud do clusteru přidáte nový uzel, bude pro oba typy vytvořen nový svazek.

- Počet svazků zůstává stejný i v případě, že uzel nefunguje nebo je odebraný.

- Pokud používáte Azure Stack Development Kit, existuje jeden svazek s více sdílenými složkami.

Svazky v Prostory úložiště s přímým přístupem poskytují odolnost proti problémům s hardwarem, jako je například selhání disku nebo serveru. Zároveň umožňují nepřetržitou dostupnost v rámci údržby serveru, jako jsou aktualizace softwaru. Nasazení centra Azure Stack používá k zajištění odolnosti dat trojrozměrné zrcadlení. Tři kopie dat tenanta se zapisují na různé servery, kde se nacházejí v mezipaměti:

![Azure Stack infrastruktura úložiště centra](media/azure-stack-storage-infrastructure-overview/image5.png)

Zrcadlení zajišťuje odolnost proti chybám tím, že udržuje více kopií všech dat. Způsob, jakým jsou data rozložená a umístěná, jsou netriviální, ale mají pravdu, že všechna data uložená pomocí zrcadlení se napíší několikrát. Každá kopie je zapsána na jiný fyzický hardware (různé jednotky na různých serverech), u kterých se předpokládá nezávisle na selhání. Třícestný zrcadlení může bezpečně tolerovat alespoň dva problémy s hardwarem (na disku nebo na serveru). Pokud například restartujete jeden server, když dojde k výpadku jiné jednotky nebo serveru, všechna data zůstanou bezpečná a nepřetržitě dostupná.

## <a name="next-step"></a>Další krok

[Správa kapacity úložiště](azure-stack-manage-storage-shares.md) 
