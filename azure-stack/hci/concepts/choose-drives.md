---
title: Výběr jednotek pro Azure Stack HCI
description: Postup výběru jednotek pro Prostory úložiště s přímým přístupem v Azure Stack HCI
author: khdownie
ms.author: v-kedow
ms.topic: article
ms.date: 03/06/2020
ms.openlocfilehash: ee51dc973c26335cfb6c75de991508a6063e0993
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "80806680"
---
# <a name="choosing-drives-for-azure-stack-hci"></a>Výběr jednotek pro Azure Stack HCI

>Platí pro: Windows Server 2019

V tomto tématu najdete pokyny k výběru jednotek [prostory úložiště s přímým přístupem](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) pro splnění požadavků na výkon a kapacitu pro Azure Stack HCI.

## <a name="drive-types"></a>Typy jednotek

Prostory úložiště s přímým přístupem aktuálně funguje se třemi typy jednotek:

|||
|----------------------|--------------------------|
|![NVMe](media/choose-drives/NVMe-100-px.png)|**NVMe** (nestálá paměť Express) odkazuje na jednotky Solid-State, které přímo sedí na sběrnici PCIe. Obecné faktory pro formuláře jsou 2,5 "U. 2, PCIe Add-in-Card (AIC) a M. 2. NVMe nabízí vyšší propustnost vstupně-výstupních operací za sekundu s nižší latencí než jakýkoli jiný typ jednotky, který dnes podporujeme.|
|![SSD](media/choose-drives/SSD-100-px.png)|**SSD** odkazuje na jednotky SSD, které se připojují prostřednictvím konvenčního SATA nebo SAS.|
|![HDD](media/choose-drives/HDD-100-px.png)|**HDD** odkazuje na rotační a magnetické jednotky pevného disku, které nabízejí obrovské kapacity úložiště.|

## <a name="built-in-cache"></a>Integrovaná mezipaměť

Prostory úložiště s přímým přístupem funkce obsahuje integrovanou mezipaměť na straně serveru. Je to velká, Trvalá mezipaměť pro čtení a zápis v reálném čase. V nasazeních s více typy jednotek je automaticky nakonfigurováno pro použití všech jednotek typu "nejrychlejší". Zbývající jednotky se použijí k ukládání.

Další informace najdete [v části Principy mezipaměti v prostory úložiště s přímým přístupem](/windows-server/storage/storage-spaces/understand-the-cache).

## <a name="option-1--maximizing-performance"></a>Možnost 1 – maximalizace výkonu

Pokud chcete dosáhnout předvídatelné a jednotné latence v milisekundách mezi náhodným čtením a zápisem do jakýchkoli dat, nebo pokud chcete dosáhnout extrémně vysokého počtu vstupně-výstupních operací (provedli jsme [více než 6 000 000](https://www.youtube.com/watch?v=0LviCzsudGY&t=28m)!) nebo [propustnost v/v,](https://www.youtube.com/watch?v=-LK2ViRGbWs&t=16m50s)měli byste přejít "all-Flash".

Existují v současné době tři způsoby, jak to provést:

![Vše – možnosti nasazení – bliknutí](media/choose-drives/All-Flash-Deployment-Possibilities.png)

1. **Všechny NVMe.** Použití všech NVMe poskytuje neodpovídající výkon, včetně nejpředvídatelné nízké latence. Pokud jsou všechny jednotky stejného modelu, neexistuje mezipaměť. Můžete také kombinovat vyšší životnost a nižší dlouhodobé modely NVMe a nakonfigurovat původní mezipaměť na zápisy do mezipaměti pro druhé ([vyžaduje nastavení](/windows-server/storage/storage-spaces/understand-the-cache#manual-configuration)).

2. **NVMe + SSD.** Při použití NVMe spolu s SSD bude NVMe automaticky zapisovat zápisy do mezipaměti SSD. To umožňuje zápisy do mezipamětí a jejich vyřazení z více fází, jak je potřeba, abyste snížili opotřebení SSD. Tato funkce poskytuje NVMe jako vlastnosti zápisu, zatímco čtení se zpracovává přímo z Ssdy také.

3. **Všechny jednotky SSD.** Stejně jako u All-NVMe neexistuje mezipaměť, pokud jsou všechny vaše jednotky stejný model. Pokud kombinujete modely s vyšší životností a nižší dlouhodobé nároky, můžete nakonfigurovat původní pro zápis do mezipaměti pro druhý ([vyžaduje nastavení](/windows-server/storage/storage-spaces/understand-the-cache#manual-configuration)).

   >[!NOTE]
   > Výhodou použití All-NVMe nebo All-SSD bez mezipaměti je to, že získáte použitelnou kapacitu úložiště z každé jednotky. Při ukládání do mezipaměti není vydaná žádná kapacita, což může být v menším měřítku opravné.

## <a name="option-2--balancing-performance-and-capacity"></a>Možnost 2 – vyvážení výkonu a kapacity

U prostředí s nejrůznějšími aplikacemi a úlohami, které jsou s přísnými nároky na výkon a ostatními, které vyžadují značnou kapacitu úložiště, byste měli začít "hybrid" pomocí NVMe nebo SSD caching pro větší HDD.

![Hybridní nasazení – možnosti](media/choose-drives/Hybrid-Deployment-Possibilities.png)

1. **NVMe + HDD**. Jednotky NVMe urychlují čtení a zápisy ukládáním do mezipaměti. Čtení do mezipaměti umožňuje HDD Zaměřte se na zápisy. Zápisy do mezipaměti absorbují shluky a umožňují zápisy do COALESCE a mají být v případě potřeby v uměle serializovaném stavu, který maximalizuje vstupně-výstupní operace HDD a propustnost vstupně-výstupních operací. To poskytuje NVMeelné charakteristiky pro zápis a často i nedávno čtená data, a to i v NVMe, jako jsou vlastnosti čtení.

2. **SSD + HDD**. Podobně jako u výše uvedeného bude SSD zrychlit čtení a zápisy ukládáním do mezipaměti. To poskytuje charakteristiky pro zápis na disku SSD a charakteristiky pro čtení z disku SSD pro často nebo nedávno načtená data.

    Existuje jedna další, spíše neexotická možnost: pro použití jednotek *všech tří* typů.

3. **NVMe + SSD + HDD.** S jednotkami všech tří typů NVMe jednotky mezipaměť pro SSD a HDD. Opravou je, že můžete vytvářet svazky na SSD a svazky na HDD, vedle sebe ve stejném clusteru, a to vše urychlené NVMe. Bývalé jsou přesně stejné jako v nasazení "all-Flash" a druhá je přesně stejná jako v nasazeních "hybrid" popsaných výše. Tato funkce se koncepčně podobá dvěma fondům s převážně nezávislou správou kapacity, cykly selhání a oprav a tak dále.

   >[!IMPORTANT]
   > K umístění většiny úloh citlivých na výkon na všech bliknutích doporučujeme použít vrstvu SSD.

## <a name="option-3--maximizing-capacity"></a>Možnost 3 – Maximalizace kapacity

Pro úlohy, které vyžadují rozsáhlou kapacitu a zápis, jako je archivace, cíle zálohování, datové sklady nebo "studené" úložiště, byste měli zkombinovat několik SSD pro ukládání do mezipaměti s mnoha většími Hddy pro kapacitu.

![Možnosti nasazení pro maximalizaci kapacity](media/choose-drives/maximizing-capacity.png)

1. **SSD + HDD**. SSD zapíše do mezipaměti čtení a zápisy, aby absorbují shluky a poskytovala výkon zápisu z disku SSD s použitím optimalizovaného zrušení přípravy později k HDD.

>[!IMPORTANT]
>Konfigurace pouze s HDD není podporována. Nedoporučuje se SSD ukládání do mezipaměti s vysokou životností do nízké životnosti SSD.

## <a name="sizing-considerations"></a>Důležité informace o velikosti

### <a name="cache"></a>Mezipaměť

Každý server musí mít alespoň dvě jednotky mezipaměti (minimální vyžaduje se pro redundanci). Doporučujeme, abyste si počet kapacitních jednotek vynásobí násobkem počtu jednotek mezipaměti. Pokud máte například 4 jednotky mezipaměti, budete mít jednotnější výkon s 8 jednotkami kapacity (1:2 poměr) než 7 nebo 9.

Mezipaměť by měla být upravena tak, aby vyhovovala pracovní sadě vašich aplikací a úloh, to znamená všechna data, která jsou v daném okamžiku aktivně čtena a zapsána. Není potřeba žádné požadavky na velikost mezipaměti nad rámec těchto požadavků. U nasazení s HDD je spravedlivým počátečním místem 10 procent kapacity – například pokud každý server má 4 × 4 TB HDD = 16 TB kapacity, pak 2 × 800 GB SSD = 1,6 TB mezipaměti na jeden server. U nasazení ve všech počítačích, zejména s velmi [vysokou životností](https://blogs.technet.microsoft.com/filecab/2017/08/11/understanding-dwpd-tbw/) SSD, může být spravedlivé začít přiblížit k 5 procentům kapacity – například pokud každý server má 24 × 1,2 TB SSD = 28,8 TB kapacity, pak 2 x 750 GB NVMe = 1,5 TB mezipaměti na jeden server. Jednotky mezipaměti můžete kdykoli přidat nebo odebrat později, abyste je mohli upravit.

### <a name="general"></a>Obecné

Doporučujeme omezit celkovou kapacitu úložiště na jeden server na přibližně 400 terabajtů (TB). Čím větší kapacita úložiště na server, tím déle bude doba potřebná k opětovné synchronizaci dat po výpadku nebo restartování, například při použití aktualizací softwaru. Aktuální maximální velikost na jeden fond úložiště je 4 řádu petabajtů (PB) (4 000 TB) pro Windows Server 2019.

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu také:

- [Přehled Azure Stack HCI](../overview.md)
- [Pochopení mezipaměti v Azure Stack HCI](cache.md)
- [Prostory úložiště s přímým přístupem požadavky na hardware](/windows-server/storage/storage-spaces/storage-spaces-direct-hardware-requirements)
- [Plánování svazků v Azure Stack HCI](plan-volumes.md)
- [Odolnost proti chybám a efektivita úložiště](fault-tolerance.md)