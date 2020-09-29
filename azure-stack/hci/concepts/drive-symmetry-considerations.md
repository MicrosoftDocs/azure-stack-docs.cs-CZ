---
title: Požadavky na symetrii na disku pro Azure Stack HCI
description: Toto téma vysvětluje omezení symetrie jednotek a poskytuje příklady podporovaných a nepodporovaných konfigurací.
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 09/01/2020
ms.openlocfilehash: 545a0b90ad938a172a184748780974ba7403f19f
ms.sourcegitcommit: 4af79f4fa2598d57c81e994192c10f8c6be5a445
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89742396"
---
# <a name="drive-symmetry-considerations-for-azure-stack-hci"></a>Požadavky na symetrii na disku pro Azure Stack HCI

> Platí pro: Azure Stack HCI, verze 20H2; Windows Server 2019

Azure Stack HCI funguje nejlépe, když má každý server přesně stejné jednotky.

Ve skutečnosti to znamená, že to není vždycky praktické, protože Azure Stack HCI je navržená tak, aby se spouštěla po rocích a aby se mohla škálovat podle potřeb vaší organizace. V současné době si můžete koupit spacious 3 TB pevných disků; v příštím roce nemusí být možné najít jednotky, které jsou malé. Proto je očekáváno a podporováno určité množství kombinací a porovnání. Pamatujte však, že více symetrie je vždy lepší.

Toto téma vysvětluje omezení a poskytuje příklady podporovaných a nepodporovaných konfigurací.

## <a name="constraints"></a>Omezení

### <a name="type"></a>Typ

Všechny servery by měly mít stejné [typy jednotek](choose-drives.md#drive-types).

Například pokud má jeden server NVMe, musí mít *všechny* NVMe.

### <a name="number"></a>Číslo

Všechny servery musí mít stejný počet jednotek každého typu.

Pokud má například jeden server šest SSD, měly by mít *všechny* šest SSD.

   > [!NOTE]
   > Je v pořádku, kolik jednotek se v průběhu selhání nebo při přidávání nebo odebírání jednotek bude dočasně lišit.

### <a name="model"></a>Modelování

Pokud je to možné, doporučujeme použít jednotky stejného modelu a verze firmwaru. Pokud nemůžete, pečlivě vyberte jednotky, které jsou co nejblíže. Nemůžeme si vymíchat jednotky stejného typu s ostrými různou charakteristikou výkonu nebo životnosti (Pokud jedna z nich není mezipaměť a druhá kapacita), protože Azure Stack HCI distribuuje v/v v/v v závislosti na modelu i nestejnou diskriminaci.

   > [!NOTE]
   > Je v pořádku, aby se shodoval s podobnými jednotkami SATA a SAS.

### <a name="size"></a>Velikost

Pokud je to možné, doporučujeme použít jednotky se stejnými velikostmi. Použití kapacitních jednotek různých velikostí může mít za následek nepoužitelnou kapacitu a používání diskových jednotek mezipaměti s různými velikostmi nemusí zlepšit výkon mezipaměti. Podrobnosti najdete v následující části.

   > [!WARNING]
   > Různé velikosti kapacitních jednotek na serverech můžou mít za následek navedenou kapacitu.

## <a name="understand-capacity-imbalance"></a>Pochopení: nevyrovnanost kapacity

Azure Stack HCI je robustní pro kapacitu nerovnováhy mezi jednotkami a mezi servery. I když je nerovnováha závažná, vše bude i nadále fungovat. V závislosti na několika faktorech ale nemusí být kapacita dostupná na každém serveru použitelná.

Chcete-li zjistit, proč k tomu dochází, zvažte následující zjednodušené ilustrace. Každé barevné pole představuje jednu kopii zrcadlených dat. Například pole označená jako, a a jsou tři kopie stejných dat. Aby bylo možné akceptovat odolnost proti chybám serveru, *musí* být tyto kopie uložené na různých serverech.

### <a name="stranded-capacity"></a>Navýšení kapacity

Jak je vykresleno, server 1 (10 TB) a Server 2 (10 TB) jsou zaplněny. Server 3 má větší jednotky, takže jeho celková kapacita je větší (15 TB). Pro uložení více trojrozměrného zrcadlového data na serveru 3 ale budete potřebovat kopie na serveru 1 a na serveru 2, které jsou již zaplněny. Zbývající kapacitu 5 TB na serveru 3 nelze použít – jedná se o *"" "* .

![Třícestný zrcadlový svazek, tři servery, geografická kapacita](media/drive-symmetry-considerations/Size-Asymmetry-3N-Stranded.png)

### <a name="optimal-placement"></a>Optimální umístění

V opačném případě se čtyřmi servery o kapacitě 10 TB, 10 TB, 10 TB a 15 TB a odolné odolnosti proti chybám *je* možné přidružit kopie způsobem, který používá veškerou dostupnou kapacitu, jak je vykreslený. Kdykoli je to možné, přidělování Prostory úložiště s přímým přístupem vyhledá a použije optimální umístění, takže zůstane bez jeho celkové kapacity.

![Třícestný zrcadlový svazek, čtyři servery, žádná geografická kapacita](media/drive-symmetry-considerations/Size-Asymmetry-4N-No-Stranded.png)

Počet serverů, odolnost, závažnost nerovnováhy kapacity a další faktory ovlivňují, zda existuje oblast s určením kapacity. **Nejefektivnějším obecným pravidlem je předpokládat, že je zaručena použitelnost dostupné kapacity na každém serveru.**

## <a name="understand-cache-imbalance"></a>Pochopení: nerovnováha mezipaměti

Azure Stack HCI je robustní pro ukládání nerovnováhy do mezipaměti napříč jednotkami a mezi servery. I když je nerovnováha závažná, vše bude i nadále fungovat. Kromě toho Azure Stack rozhraní HCI vždycky používá veškerou dostupnou mezipaměť na plný.

Použití jednotek mezipaměti různých velikostí ale nedokáže vylepšit výkon mezipaměti jednotně nebo prediktivním způsobem, což může zlepšit výkon jenom u vazeb v/v za [jednotku](cache.md#server-side-architecture) s většími jednotkami mezipaměti. Azure Stack HCI distribuuje vstupně-výstupní operace rovnoměrně napříč vazbami a nevede k diskriminaci na základě poměru mezipaměti na kapacitu.

![Nerovnováha mezipaměti](media/drive-symmetry-considerations/Cache-Asymmetry.png)

   > [!TIP]
   > Další informace o vazbách mezipaměti najdete v tématu [Principy mezipaměti](cache.md) .

## <a name="example-configurations"></a>Příklady konfigurací

Tady jsou některé podporované a nepodporované konfigurace:

### <a name="image-typeicon-sourcemediadrive-symmetry-considerationssupportedpng-borderfalse-supported-different-models-between-servers"></a>:::image type="icon" source="media/drive-symmetry-considerations/supported.png" border="false"::: Podporováno: různé modely mezi servery

První dva servery používají NVMe model "X", ale třetí server používá model NVMe "Z", který je velmi podobný.

| Server 1                    | Server 2                    | Server 3                    |
|-----------------------------|-----------------------------|-----------------------------|
| 2 x NVMe Model X (mezipaměť)    | 2 x NVMe Model X (mezipaměť)    | 2 x NVMe model Z (mezipaměť)    |
| 10 x SSD model Y (kapacita) | 10 x SSD model Y (kapacita) | 10 x SSD model Y (kapacita) |

To je podporováno.

### <a name="image-typeicon-sourcemediadrive-symmetry-considerationssupportedpng-borderfalse-supported-different-models-within-server"></a>:::image type="icon" source="media/drive-symmetry-considerations/supported.png" border="false"::: Podporováno: různé modely v rámci serveru

Každý server používá jinou kombinaci modelů HDD "Y" a "Z", které jsou velmi podobné. Každý server má 10 Hard HDD.

| Server 1                   | Server 2                   | Server 3                   |
|----------------------------|----------------------------|----------------------------|
| 2 x SSD Model X (mezipaměť)    | 2 x SSD Model X (mezipaměť)    | 2 x SSD Model X (mezipaměť)    |
| 7 × model HDD Y (kapacita) | 5 × model HDD Y (kapacita) | 1 x model HDD Y (kapacita) |
| 3 x HDD modelu Z (kapacita) | 5 x HDD modelu Z (kapacita) | 9 x HDD modelu Z (kapacita) |

To je podporováno.

### <a name="image-typeicon-sourcemediadrive-symmetry-considerationssupportedpng-borderfalse-supported-different-sizes-across-servers"></a>:::image type="icon" source="media/drive-symmetry-considerations/supported.png" border="false"::: Podporováno: různé velikosti mezi servery

První dva servery používají disk o 4 TB, ale třetí server používá velmi podobný disk s 6 TB.

| Server 1                | Server 2                | Server 3                |
|-------------------------|-------------------------|-------------------------|
| 2 × 800 GB NVMe (mezipaměť) | 2 × 800 GB NVMe (mezipaměť) | 2 × 800 GB NVMe (mezipaměť) |
| 4 × 4 TB HDD (kapacita) | 4 × 4 TB HDD (kapacita) | 4 × 6 TB HDD (kapacita) |

Tato funkce je podporována, i když bude mít za následek navedenou kapacitu.

### <a name="image-typeicon-sourcemediadrive-symmetry-considerationssupportedpng-borderfalse-supported-different-sizes-within-server"></a>:::image type="icon" source="media/drive-symmetry-considerations/supported.png" border="false"::: Podporováno: různé velikosti v rámci serveru

Každý server používá jinou kombinaci 1,2 TB a velmi podobných 1,6 TB SSD. Každý server má 4 celkem SSD.

| Server 1                 | Server 2                 | Server 3                 |
|--------------------------|--------------------------|--------------------------|
| 3 x 1,2 TB SSD (mezipaměť)   | 2 x 1,2 TB SSD (mezipaměť)   | 4 x 1,2 TB SSD (mezipaměť)   |
| 1 x 1,6 TB SSD (mezipaměť)   | 2 x 1,6 TB SSD (mezipaměť)   | -                        |
| 20 × 4 TB HDD (kapacita) | 20 × 4 TB HDD (kapacita) | 20 × 4 TB HDD (kapacita) |

To je podporováno.

### <a name="image-typeicon-sourcemediadrive-symmetry-considerationsunsupportedpng-borderfalse-not-supported-different-types-of-drives-across-servers"></a>:::image type="icon" source="media/drive-symmetry-considerations/unsupported.png" border="false"::: Nepodporováno: různé typy jednotek mezi servery

Server 1 NVMe, ale ostatní ne.

| Server 1            | Server 2            | Server 3            |
|---------------------|---------------------|---------------------|
| 6 x NVMe (mezipaměť)    | -                   | -                   |
| -                   | 6 x SSD (mezipaměť)     | 6 x SSD (mezipaměť)     |
| 18 × HDD (kapacita) | 18 × HDD (kapacita) | 18 × HDD (kapacita) |

Toto není podporováno. Typy jednotek by měly být na každém serveru stejné.

### <a name="image-typeicon-sourcemediadrive-symmetry-considerationsunsupportedpng-borderfalse-not-supported-different-number-of-each-type-across-servers"></a>:::image type="icon" source="media/drive-symmetry-considerations/unsupported.png" border="false"::: Nepodporováno: různý počet jednotlivých typů mezi servery

Server 3 má více jednotek než ostatní.

| Server 1            | Server 2            | Server 3            |
|---------------------|---------------------|---------------------|
| 2 x NVMe (mezipaměť)    | 2 x NVMe (mezipaměť)    | 4 x NVMe (mezipaměť)    |
| 10 × HDD (kapacita) | 10 × HDD (kapacita) | 20 × HDD (kapacita) |

Toto není podporováno. Počet jednotek každého typu by měl být stejný na každém serveru.

### <a name="image-typeicon-sourcemediadrive-symmetry-considerationsunsupportedpng-borderfalse-not-supported-only-hdd-drives"></a>:::image type="icon" source="media/drive-symmetry-considerations/unsupported.png" border="false"::: Nepodporováno: pouze jednotky HDD

Všechny servery mají připojené jenom jednotky HDD.

|Server 1|Server 2|Server 3|
|-|-|-|
|18 × HDD (kapacita) |18 × HDD (kapacita)|18 × HDD (kapacita)|

Toto není podporováno. Musíte přidat minimálně dvě jednotky mezipaměti (NvME nebo SSD) připojené ke každému ze serverů.

## <a name="summary"></a>Shrnutí

Aby rekapitulace každý server v clusteru měl mít stejné typy jednotek a stejný počet každého typu. V souladu s výše uvedenými požadavky podporuje modely jednotek a velikosti jednotek podle potřeby.

| Jedinečn | Stav |
|--|--|
| Stejné typy jednotek na každém serveru | **Požadováno** |
| Stejný počet každého typu na každém serveru | **Požadováno** |
| Stejné modely jednotek na každém serveru | Doporučeno |
| Stejné velikosti jednotek na každém serveru | Doporučeno |

## <a name="next-steps"></a>Další kroky

Související informace najdete v tématu také:

- [Před nasazením Azure Stack HCL](../deploy/before-you-start.md)
- [Zvolit jednotky](choose-drives.md)
