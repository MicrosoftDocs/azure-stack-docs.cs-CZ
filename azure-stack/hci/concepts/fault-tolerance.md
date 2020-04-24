---
title: Odolnost proti chybám a efektivita úložiště v Azure Stack HCI
description: Diskuze o možnostech odolnosti v Prostory úložiště s přímým přístupem včetně zrcadlení a parity.
author: khdownie
ms.author: v-kedow
ms.topic: article
ms.date: 02/28/2020
ms.openlocfilehash: 9ace3960b4c54461a4153c4997694e6d17ee4fd1
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "79025697"
---
# <a name="fault-tolerance-and-storage-efficiency-in-azure-stack-hci"></a>Odolnost proti chybám a efektivita úložiště v Azure Stack HCI

>Platí pro: Windows Server 2019

Toto téma představuje možnosti odolnosti dostupné v Prostory úložiště s přímým přístupem a popisuje požadavky na škálování, efektivitu úložiště a obecné výhody a kompromisy každého z nich. Nabízí také některé pokyny k používání, které vám pomůžou začít, a odkazuje na některé skvělé dokumenty, blogy a další obsah, kde se můžete dozvědět víc.

Pokud jste již obeznámeni s prostory úložiště, můžete přeskočit na část [Souhrn](#summary) .

## <a name="overview"></a>Přehled

Ve srdce jsou prostory úložiště o zajištění odolnosti proti chybám, které se často označují jako "odolnost", pro vaše data. Jeho implementace je podobná RAID, kromě distribuovaných napříč servery a implementována v softwaru.

Stejně jako u RAID existuje několik různých způsobů, jak si prostory úložiště můžou dělat různé kompromisy mezi odolností proti chybám, efektivitou úložiště a složitostí výpočtů. Mezi ně patří například "zrcadlení" a "parita", druhé, někdy označované jako "mazání kódu".

## <a name="mirroring"></a>Zrcadlení

Zrcadlení zajišťuje odolnost proti chybám tím, že udržuje více kopií všech dat. To je velmi podobné jako RAID-1. Způsob, jakým jsou data rozložená a umístěná, jsou netriviální (Další informace najdete v [tomto blogovém příspěvku](https://blogs.technet.microsoft.com/filecab/2016/11/21/deep-dive-pool-in-spaces-direct/) ), ale v plném rozsahu platí, že všechna data uložená pomocí zrcadlení se zapisují v celých případech několikrát. Každá kopie je zapsána na jiný fyzický hardware (různé jednotky na různých serverech), u kterých se předpokládá nezávisle na selhání.

Prostory úložiště nabízí dva typy zrcadlení – "obousměrný" a "třícestný".

### <a name="two-way-mirror"></a>Dvoucestný zrcadlový svazek

Obousměrné zrcadlení zapisuje dvě kopie všeho. Efektivita úložiště je 50% – pro zápis 1 TB dat potřebujete alespoň 2 TB kapacity fyzického úložiště. Podobně potřebujete alespoň dvě [domény selhání hardwaru](/windows-server/failover-clustering/fault-domains) – s prostory úložiště s přímým přístupem, což znamená dva servery.

![dvoucestné zrcadlení](media/fault-tolerance/two-way-mirror-180px.png)

   >[!WARNING]
   > Pokud máte více než dva servery, doporučujeme místo toho použít trojrozměrné zrcadlení.

### <a name="three-way-mirror"></a>Třícestný zrcadlový svazek

Třícestný zrcadlení zapisuje tři kopie všeho. Efektivita úložiště je 33,3% – pro zápis 1 TB dat potřebujete aspoň 3 TB kapacity fyzického úložiště. Podobně potřebujete alespoň tři domény selhání hardwaru – s Prostory úložiště s přímým přístupem, které znamenají tři servery.

Třícestný zrcadlení může bezpečně tolerovat alespoň [dva problémy s hardwarem (na disku nebo na serveru)](#examples). Pokud například restartujete jeden server, když dojde k výpadku jiné jednotky nebo serveru, všechna data zůstanou bezpečná a nepřetržitě dostupná.

![Třícestný zrcadlový svazek](media/fault-tolerance/three-way-mirror-180px.png)

## <a name="parity"></a>Parity

Kódování parity, často označované jako "mazání kódu", zajišťuje odolnost proti chybám pomocí bitových aritmetických výpočtů, což může mít [výjimečně komplikované](https://www.microsoft.com/research/wp-content/uploads/2016/02/LRC12-cheng20webpage.pdf). Způsob, jak to funguje, je méně zřejmý než zrcadlení a existuje mnoho skvělých online prostředků (například Tento průvodce Dummies třetí strany [pro mazání kódu](http://smahesh.com/blog/2012/07/01/dummies-guide-to-erasure-coding/)), který vám pomůže získat nápad. Je nám stačit, když je lepší efektivita úložiště, aniž by došlo k narušení odolnosti proti chybám.

Prostory úložiště nabízí dva typy parity – "jednoduchá" parita a duální parita, druhá používá pokročilou techniku nazvanou "místní rekonstrukce kódu" při větších škálováních.

> [!IMPORTANT]
> Pro většinu úloh citlivých na výkon doporučujeme použití zrcadlení. Další informace o tom, jak vyrovnávat výkon a kapacitu v závislosti na vašich úlohách, najdete v tématu [plánování svazků](/windows-server/storage/storage-spaces/plan-volumes#choosing-the-resiliency-type).

### <a name="single-parity"></a>Jednoduchá parita
Jednoduchá parita uchovává pouze jeden logický paritový symbol, který poskytuje odolnost proti chybám proti jednomu selhání současně. Nejlépe se podobá RAID-5. Chcete-li použít jednoduchou paritu, potřebujete alespoň tři domény selhání hardwaru – pomocí Prostory úložiště s přímým přístupem, který znamená tři servery. Vzhledem k tomu, že třícestný zrcadlení poskytuje větší odolnost proti chybám ve stejném měřítku, nebudeme používat jednoduchou paritu. Ale pokud si ho nebudete používat a plně se podporuje, je tam.

   >[!WARNING]
   > Nemůžeme používat jednoduchou paritu, protože může bezpečně tolerovat jenom jedno selhání hardwaru: Pokud provádíte restartování jednoho serveru, když dojde k výpadku jiné jednotky nebo serveru, dojde k výpadku. Pokud máte jenom tři servery, doporučujeme použít třícestný způsob zrcadlení. Pokud máte čtyři nebo více, přečtěte si další část.

### <a name="dual-parity"></a>Duální parita

Duální parita implementuje Reed-Šalamounovy chyby – opravují se kódy, které udržují dva bitové paritní symboly, čímž zajišťuje stejnou odolnost proti chybám jako třícestný zrcadlení (tj. až dvě selhání najednou), ale s lepší efektivitou úložiště. Nejlépe se podobá RAID-6. Chcete-li použít duální paritu, potřebujete alespoň čtyři domény selhání hardwaru – s Prostory úložiště s přímým přístupem, což znamená čtyři servery. V této škále je efektivita úložiště 50% – Pokud chcete ukládat 2 TB dat, budete potřebovat 4 TB kapacity fyzického úložiště.

![Duální parita](media/fault-tolerance/dual-parity-180px.png)

Efektivita úložiště s duální paritou zvyšuje více domén selhání hardwaru, od 50% do 80 procent. Například při sedmi (s Prostory úložiště s přímým přístupem, což znamená sedm serverů) se efektivita blíží 66,7 procent – pro uložení 4 TB dat, potřebujete pouze 6 TB kapacity fyzického úložiště.

![Dual-parita – celá](media/fault-tolerance/dual-parity-wide-180px.png)

V části [Shrnutí](#summary) najdete efektivitu kódů dvojí a místní rekonstrukce v každé škále.

### <a name="local-reconstruction-codes"></a>Místní kódy rekonstrukce

Prostory úložiště zavádí pokročilou techniku vyvinutou Microsoft Research s názvem "místní rekonstrukce codes" nebo LRC. Ve velkém měřítku používá duální parita LRC k rozdělení kódování/dekódování do několika menších skupin, aby se snížila režie potřebná k zápisu nebo obnovení při selhání.

S pevnými disky (HDD) je velikost skupiny čtyři symboly; u jednotek SSD (Solid-State Drive) je velikost skupiny šest symbolů. Například toto rozložení vypadá jako u pevných disků a 12 domén selhání hardwaru (tzn. 12 serverů) – existují dvě skupiny čtyř datových symbolů. Dosahuje 72,7 procent efektivity úložiště.

![místní rekonstrukce-kódy](media/fault-tolerance/local-reconstruction-codes-180px.png)

Doporučujeme, abyste tento Názorný postup eminentlyi, a to díky tomu, [jak místní kódy rekonstrukce zpracovávají různé scénáře selhání a proč jsou z důvodu jejich odvolání](https://blogs.technet.microsoft.com/filecab/2016/09/06/volume-resiliency-and-efficiency-in-storage-spaces-direct/)na naší hodně [Claus Joergensen](https://twitter.com/clausjor).

## <a name="mirror-accelerated-parity"></a>Zrcadlení – urychlené parity

Prostory úložiště s přímým přístupem svazek může být součástí zrcadlení a parity částí. Zapisuje první pozemky do zrcadlené části a postupně se přesunou do paritní části později. To efektivně [používá zrcadlení ke zrychlení mazání kódu](https://blogs.technet.microsoft.com/filecab/2016/09/06/volume-resiliency-and-efficiency-in-storage-spaces-direct/).

Ke smíchání trojrozměrného zrcadlení a duální parity potřebujete alespoň čtyři domény selhání, což znamená čtyři servery.

Efektivita úložiště parity podporující zrcadlení je mezi tím, co byste se dostali z použití všech zrcadlení nebo všech parit, a závisí na tom, jaké proporce zvolíte. Například ukázka na značce 37-minut v této prezentaci ukazuje různé směsi, které [dosahují 46 procent, 54 procent 65 a% efektivitu](https://www.youtube.com/watch?v=-LK2ViRGbWs&t=36m55s) s 12 servery.

> [!IMPORTANT]
> Pro většinu úloh citlivých na výkon doporučujeme použití zrcadlení. Další informace o tom, jak vyrovnávat výkon a kapacitu v závislosti na vašich úlohách, najdete v tématu [plánování svazků](/windows-server/storage/storage-spaces/plan-volumes#choosing-the-resiliency-type).

## <a name="summary"></a><a name="summary"></a>Souhrn

Tato část shrnuje typy odolnosti dostupné v Prostory úložiště s přímým přístupem, minimální požadavky na škálování pro použití každého typu, počet chyb, které každý typ může tolerovat, a odpovídající efektivitu úložiště.

### <a name="resiliency-types"></a>Typy odolnosti

|    Odolnost          |    Odolnost proti chybám       |    Efektivita úložiště      |
|------------------------|----------------------------|----------------------------|
|    Dvoucestný zrcadlový svazek      |    1                       |    50,0%                   |
|    Třícestný zrcadlový svazek    |    2                       |    33,3%                   |
|    Duální parita         |    2                       |    50,0%-80,0%           |
|    Smíšené               |    2                       |    33,3%-80,0%           |

### <a name="minimum-scale-requirements"></a>Minimální požadavky na škálování

|    Odolnost          |    Minimální požadované domény selhání   |
|------------------------|-------------------------------------|
|    Dvoucestný zrcadlový svazek      |    2                                |
|    Třícestný zrcadlový svazek    |    3                                |
|    Duální parita         |    4                                |
|    Smíšené               |    4                                |

   >[!TIP]
   > Pokud nepoužíváte odolnost [skříně nebo stojanu](/windows-server/failover-clustering/fault-domains), počet domén selhání odkazuje na počet serverů. Počet jednotek na každém serveru nemá vliv na typy odolné proti chybám, které můžete použít, pokud splňujete minimální požadavky na Prostory úložiště s přímým přístupem.

### <a name="dual-parity-efficiency-for-hybrid-deployments"></a>Duální efektivita parity pro hybridní nasazení

Tato tabulka ukazuje efektivitu úložiště pro duální paritu a kódy místní rekonstrukce v každé škále pro hybridní nasazení, které obsahují jak jednotky pevného disku (HDD), tak jednotky SSD (Solid-State Drive).

|    Domény selhání      |    Rozložení           |    Efektivita   |
|-----------------------|---------------------|-----------------|
|    2                  |    –                |    –            |
|    3                  |    –                |    –            |
|    4                  |    RS 2 + 2           |    50,0%        |
|    5                  |    RS 2 + 2           |    50,0%        |
|    6                  |    RS 2 + 2           |    50,0%        |
|    7                  |    RS 4 + 2           |    66,7%        |
|    8                  |    RS 4 + 2           |    66,7%        |
|    9                  |    RS 4 + 2           |    66,7%        |
|    10                 |    RS 4 + 2           |    66,7%        |
|    11                 |    RS 4 + 2           |    66,7%        |
|    12                 |    LRC (8, 2, 1)    |    72,7%        |
|    13                 |    LRC (8, 2, 1)    |    72,7%        |
|    14                 |    LRC (8, 2, 1)    |    72,7%        |
|    15                 |    LRC (8, 2, 1)    |    72,7%        |
|    16                 |    LRC (8, 2, 1)    |    72,7%        |

### <a name="dual-parity-efficiency-for-all-flash-deployments"></a>Dual paritní efektivita pro nasazení ve všech bliknutích

Tato tabulka ukazuje efektivitu úložiště pro duální paritu a kódy místní rekonstrukce v každé škále pro všechna nasazení v rámci technologie Flash, která obsahují pouze jednotky SSD (Solid-State Drive). Rozložení parity může používat větší velikosti skupin a dosáhnout lepší efektivity úložiště v konfiguraci all-Flash.

|    Domény selhání      |    Rozložení           |    Efektivita   |
|-----------------------|---------------------|-----------------|
|    2                  |    –                |    –            |
|    3                  |    –                |    –            |
|    4                  |    RS 2 + 2           |    50,0%        |
|    5                  |    RS 2 + 2           |    50,0%        |
|    6                  |    RS 2 + 2           |    50,0%        |
|    7                  |    RS 4 + 2           |    66,7%        |
|    8                  |    RS 4 + 2           |    66,7%        |
|    9                  |    RS 6 + 2           |    75,0%        |
|    10                 |    RS 6 + 2           |    75,0%        |
|    11                 |    RS 6 + 2           |    75,0%        |
|    12                 |    RS 6 + 2           |    75,0%        |
|    13                 |    RS 6 + 2           |    75,0%        |
|    14                 |    RS 6 + 2           |    75,0%        |
|    15                 |    RS 6 + 2           |    75,0%        |
|    16                 |    LRC (12, 2, 1)   |    80,0%        |

## <a name="examples"></a><a name="examples"></a>Příklady

Pokud nemáte pouze dva servery, doporučujeme používat trojrozměrné zrcadlení a/nebo duální paritu, protože nabízejí lepší odolnost proti chybám. Konkrétně zajistí, aby všechna data zůstala bezpečná a nepřetržitě přístupná i v případě, že dvě domény selhání – s Prostory úložiště s přímým přístupem, což znamená dva servery, které jsou ovlivněné souběžnými chybami.

### <a name="examples-where-everything-stays-online"></a>Příklady, kde všechno stále online

Tyto šest příkladů znázorňují, jaké tři možnosti zrcadlení a/nebo duální parity **můžou** tolerovat.

- **1.** jedna jednotka se ztratila (zahrnuje jednotky mezipaměti).
- **2.** jeden server se ztratil.

![odolnost proti chybám – příklady-1-a-2](media/fault-tolerance/Fault-Tolerance-Example-12.png)

- **3.** jeden server a jedna jednotka se ztratily
- **4.** dvě jednotky se ztratily na různých serverech.

![odolnost proti chybám – příklady – 3 a-4](media/fault-tolerance/Fault-Tolerance-Example-34.png)

- **5.** bylo ztraceno více než dvou jednotek, pokud jsou ovlivněny maximálně dva servery.
- **6.** dva servery se ztratily.

![odolnost proti chybám – příklady-5-a-6](media/fault-tolerance/Fault-Tolerance-Example-56.png)

... v každém případě budou všechny svazky zůstat online. (Ujistěte se, že cluster udržuje kvorum.)

### <a name="examples-where-everything-goes-offline"></a>Příklady, kdy všechno přejde do režimu offline

V průběhu své životnosti můžou prostory úložiště tolerovat libovolný počet selhání, protože po každém z nich se po každém z nich obnovuje na plnou odolnost, a to kvůli dostatečné době. U většiny dvou domén selhání může být v kterémkoli okamžiku chyba bezpečně ovlivněna selháními. Tady jsou příklady toho, jak se třemi zrcadleními a/nebo s duální paritou **nemůžou** tolerovat.

- **7.** jednotky se ztratily na třech nebo více serverech najednou.
- **8.** tři nebo více serverů se ztratily najednou.

![odolnost proti chybám – příklady – 7 a-8](media/fault-tolerance/Fault-Tolerance-Example-78.png)

## <a name="usage"></a>Využití

Podívejte [se na vytváření svazků v prostory úložiště s přímým přístupem](/windows-server/storage/storage-spaces/create-volumes).

## <a name="next-steps"></a>Další kroky

Další informace o tématech uvedených v tomto článku najdete v následujících tématech:

- [Mazání kódu v Azure pomocí Microsoft Research](https://www.microsoft.com/research/publication/erasure-coding-in-windows-azure-storage/)
- [Místní kódy rekonstrukce a urychlení paritních svazků](https://blogs.technet.microsoft.com/filecab/2016/09/06/volume-resiliency-and-efficiency-in-storage-spaces-direct/)
- [Svazky v rozhraní API pro správu úložiště](https://blogs.technet.microsoft.com/filecab/2016/08/29/deep-dive-volumes-in-spaces-direct/)
- [Ukázka efektivity úložiště na webu Microsoft Ignite 2016](https://www.youtube.com/watch?v=-LK2ViRGbWs&t=36m55s)
- [VERZE kalkulačky pro Prostory úložiště s přímým přístupem](https://aka.ms/s2dcalc)
