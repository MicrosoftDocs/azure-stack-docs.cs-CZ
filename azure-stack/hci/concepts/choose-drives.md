---
title: Zvolit jednotky pro Azure Stack HCI
description: Postup výběru jednotek pro Azure Stack HCI
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 09/01/2020
ms.openlocfilehash: e5069745a1ada76f37a9dea78eeeafa18a481cff
ms.sourcegitcommit: 296c95cad20ed62bdad0d27f1f5246bfc1c81d5e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93064646"
---
# <a name="choose-drives-for-azure-stack-hci"></a>Zvolit jednotky pro Azure Stack HCI

> Platí pro: Azure Stack HCI, verze 20H2; Windows Server 2019

V tomto tématu najdete pokyny k výběru jednotek pro splnění požadavků na výkon a kapacitu pro Azure Stack HCI.

## <a name="drive-types"></a>Typy jednotek

Azure Stack HCL aktuálně pracuje se čtyřmi typy jednotek:

| Typ jednotky | Description |
|----------------------|--------------------------|
|![PMem](media/choose-drives/pmem-100px.png)|**PMem** odkazuje na trvalou paměť, což je nový typ úložiště s nízkou latencí a vysokým výkonem.|
|![NVMe](media/choose-drives/NVMe-100-px.png)|**NVMe** (nestálá paměť Express) odkazuje na jednotky Solid-State, které přímo sedí na sběrnici PCIe. Obecné faktory pro formuláře jsou 2,5 "U. 2, PCIe Add-in-Card (AIC) a M. 2. NVMe nabízí vyšší propustnost vstupně-výstupních operací za sekundu s nižší latencí než jakýkoli jiný typ disku, který podporujeme dnes s výjimkou PMem.|
|![SSD](media/choose-drives/SSD-100-px.png)|**SSD** odkazuje na jednotky SSD, které se připojují prostřednictvím konvenčního SATA nebo SAS.|
|![HDD](media/choose-drives/HDD-100-px.png)|**HDD** odkazuje na rotační a magnetické jednotky pevného disku, které nabízejí obrovské kapacity úložiště.|

## <a name="built-in-cache"></a>Integrovaná mezipaměť

Azure Stack HCI obsahuje integrovanou mezipaměť na straně serveru. Je to velká, Trvalá mezipaměť pro čtení a zápis v reálném čase. V nasazeních s více typy jednotek je automaticky nakonfigurováno pro použití všech jednotek typu "nejrychlejší". Zbývající jednotky se použijí k ukládání.

Další informace najdete [v části Principy mezipaměti v Azure Stack HCI](cache.md).

## <a name="option-1--maximizing-performance"></a>Možnost 1 – maximalizace výkonu

Pokud chcete dosáhnout předvídatelné a jednotné latence v milisekundách mezi náhodným čtením a zápisem do libovolných dat, nebo pokud chcete dosáhnout extrémně vysokého počtu vstupně-výstupních operací (bylo provedeno [více než 13 000 000](https://techcommunity.microsoft.com/t5/storage-at-microsoft/the-new-hci-industry-record-13-7-million-iops-with-windows/ba-p/428314)!) nebo propustnost v/500 v, měli byste jít o "vše-bliknutí".

To lze provést několika způsoby:

![Diagram zobrazuje možnosti nasazení, včetně všech N V M e pro kapacitu, N V M e pro mezipaměť s s s D pro kapacitu a všechny S D pro kapacitu.](media/choose-drives/All-Flash-Deployment-Possibilities.png)

1. **Všechny NVMe.** Použití všech NVMe poskytuje neodpovídající výkon, včetně nejpředvídatelné nízké latence. Pokud jsou všechny jednotky stejného modelu, neexistuje mezipaměť. Můžete také kombinovat vyšší životnost a nižší dlouhodobé modely NVMe a nakonfigurovat původní mezipaměť na zápisy do mezipaměti pro druhé ([vyžaduje nastavení](/windows-server/storage/storage-spaces/understand-the-cache#manual-configuration)).

2. **NVMe + SSD.** Při použití NVMe spolu s SSD bude NVMe automaticky zapisovat zápisy do mezipaměti SSD. To umožňuje zápisy do mezipamětí a jejich vyřazení z více fází, jak je potřeba, abyste snížili opotřebení SSD. Tato funkce poskytuje NVMe jako vlastnosti zápisu, zatímco čtení se zpracovává přímo z Ssdy také.

3. **Všechny jednotky SSD.** Stejně jako u All-NVMe neexistuje mezipaměť, pokud jsou všechny vaše jednotky stejný model. Pokud kombinujete modely s vyšší životností a nižší dlouhodobé nároky, můžete nakonfigurovat původní pro zápis do mezipaměti pro druhý ([vyžaduje nastavení](/windows-server/storage/storage-spaces/understand-the-cache#manual-configuration)).

   >[!NOTE]
   > Výhodou použití All-NVMe nebo All-SSD bez mezipaměti je to, že získáte použitelnou kapacitu úložiště z každé jednotky. Při ukládání do mezipaměti není vydaná žádná kapacita, což může být v menším měřítku opravné.

## <a name="option-2--balancing-performance-and-capacity"></a>Možnost 2 – vyvážení výkonu a kapacity

U prostředí s nejrůznějšími aplikacemi a úlohami, které jsou s přísnými nároky na výkon a ostatními, které vyžadují značnou kapacitu úložiště, byste měli začít "hybrid" pomocí NVMe nebo SSD caching pro větší HDD.

![Diagram zobrazuje možnosti nasazení, včetně N V M e pro mezipaměť s H D d pro kapacitu, s D pro mezipaměť s H D d pro kapacitu, a N V M E pro mezipaměť se smíšenou příponou D a H + D pro kapacitu.](media/choose-drives/Hybrid-Deployment-Possibilities.png)

1. **NVMe + HDD** . Jednotky NVMe urychlují čtení a zápisy ukládáním do mezipaměti. Čtení do mezipaměti umožňuje HDD Zaměřte se na zápisy. Zápisy do mezipaměti absorbují shluky a umožňují zápisy do COALESCE a mají být v případě potřeby v uměle serializovaném stavu, který maximalizuje vstupně-výstupní operace HDD a propustnost vstupně-výstupních operací. To poskytuje NVMeelné charakteristiky pro zápis a často i nedávno čtená data, a to i v NVMe, jako jsou vlastnosti čtení.

2. **SSD + HDD** . Podobně jako u výše uvedeného bude SSD zrychlit čtení a zápisy ukládáním do mezipaměti. To poskytuje charakteristiky pro zápis na disku SSD a charakteristiky pro čtení z disku SSD pro často nebo nedávno načtená data.

    Existuje jedna další, spíše neexotická možnost: pro použití jednotek *všech tří* typů.

3. **NVMe + SSD + HDD.** S jednotkami všech tří typů NVMe jednotky mezipaměť pro SSD a HDD. Opravou je, že můžete vytvářet svazky na SSD a svazky na HDD vedle sebe ve stejném clusteru, a to vše urychlené NVMe. Bývalé jsou přesně stejné jako v nasazení "all-Flash" a druhá je přesně stejná jako v nasazeních "hybrid" popsaných výše. Tato funkce se koncepčně podobá dvěma fondům s převážně nezávislou správou kapacity, cykly selhání a oprav a tak dále.

   >[!IMPORTANT]
   > K umístění většiny úloh citlivých na výkon na všech bliknutích doporučujeme použít vrstvu SSD.

## <a name="option-3--maximizing-capacity"></a>Možnost 3 – Maximalizace kapacity

Pro úlohy, které vyžadují rozsáhlou kapacitu a zápis, jako je archivace, cíle zálohování, datové sklady nebo "studené" úložiště, byste měli zkombinovat několik SSD pro ukládání do mezipaměti s mnoha většími Hddy pro kapacitu.

![Možnosti nasazení pro maximalizaci kapacity](media/choose-drives/maximizing-capacity.png)

1. **SSD + HDD** . SSD zapíše do mezipaměti čtení a zápisy, aby absorbují shluky a poskytovala výkon zápisu z disku SSD s použitím optimalizovaného zrušení přípravy později k HDD.

>[!IMPORTANT]
>Konfigurace pouze s HDD není podporována. Nedoporučuje se SSD ukládání do mezipaměti s vysokou životností do nízké životnosti SSD.

## <a name="sizing-considerations"></a>Důležité informace o velikosti

### <a name="cache"></a>Mezipaměť

Každý server musí mít alespoň dvě jednotky mezipaměti (minimální vyžaduje se pro redundanci). Doporučujeme, abyste si počet kapacitních jednotek vynásobí násobkem počtu jednotek mezipaměti. Pokud máte například 4 jednotky mezipaměti, budete mít jednotnější výkon s 8 jednotkami kapacity (1:2 poměr) než 7 nebo 9.

Mezipaměť by měla být upravena tak, aby vyhovovala pracovní sadě vašich aplikací a úloh, to znamená všechna data, která jsou v daném okamžiku aktivně čtena a zapsána. Není potřeba žádné požadavky na velikost mezipaměti nad rámec těchto požadavků. U nasazení s HDD je spravedlivým počátečním místem 10 procent kapacity – například pokud každý server má 4 × 4 TB HDD = 16 TB kapacity, pak 2 × 800 GB SSD = 1,6 TB mezipaměti na jeden server. U nasazení ve všech počítačích, zejména s velmi [vysokou životností](https://techcommunity.microsoft.com/t5/storage-at-microsoft/understanding-ssd-endurance-drive-writes-per-day-dwpd-terabytes/ba-p/426024) SSD, může být spravedlivé začít přiblížit k 5 procentům kapacity – například pokud každý server má 24 × 1,2 TB SSD = 28,8 TB kapacity, pak 2 x 750 GB NVMe = 1,5 TB mezipaměti na jeden server. Jednotky mezipaměti můžete kdykoli přidat nebo odebrat později, abyste je mohli upravit.

### <a name="general"></a>Obecné

Doporučujeme omezit celkovou kapacitu úložiště na jeden server na přibližně 400 terabajtů (TB). Čím větší kapacita úložiště na server, tím déle bude doba potřebná k opětovné synchronizaci dat po výpadku nebo restartování, například při použití aktualizací softwaru. Aktuální maximální velikost na jeden fond úložiště je 4 petabajty (PB) (4 000 TB) pro Windows Server 2019.

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu také:

- [Pochopení mezipaměti](cache.md)
- [Požadavky na systém](system-requirements.md)
- [Požadavky na symetrii jednotky](drive-symmetry-considerations.md)
- [Plánování svazků](plan-volumes.md)
- [Odolnost proti chybám a efektivita úložiště](fault-tolerance.md)
- [Pochopení a nasazení trvalé paměti](/windows-server/storage/storage-spaces/deploy-pmem)
