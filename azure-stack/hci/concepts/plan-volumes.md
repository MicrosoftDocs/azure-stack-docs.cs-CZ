---
title: Plánování svazků v Azure Stack HCI
description: Jak naplánovat svazky úložiště v Azure Stack HCL.
author: khdownie
ms.author: v-kedow
ms.topic: article
ms.date: 03/06/2020
ms.openlocfilehash: c6410e4f0d60138ce773f7f0abfae1a5c1850bd2
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "79095012"
---
# <a name="planning-volumes-in-storage-spaces-direct"></a>Plánování svazků v Prostory úložiště s přímým přístupem

> Platí pro: Windows Server 2019

Toto téma obsahuje informace o tom, jak naplánovat svazky v Prostory úložiště s přímým přístupem tak, aby splňovaly požadavky na výkon a kapacitu vašich úloh, včetně volby jejich systému souborů, typu odolnosti a velikosti.

## <a name="review-what-are-volumes"></a>Kontrola: co jsou svazky

Na svazcích se ukládají soubory, které vaše úlohy potřebují, například soubory VHD nebo VHDX pro virtuální počítače s technologií Hyper-V. Svazky spojují jednotky ve fondu úložiště, aby zavedly odolnost proti chybám, škálovatelnost a výkonnostní výhody Prostory úložiště s přímým přístupem.

   >[!NOTE]
   > V celé dokumentaci pro Prostory úložiště s přímým přístupem používáme pojem "Volume", který společně odkazuje na svazek a virtuální disk, včetně funkcí poskytovaných jinými integrovanými funkcemi Windows, jako jsou sdílené svazky clusteru (CSV) a ReFS. Porozumění těmto rozdílům na úrovni implementace není nutné k naplánování a nasazení Prostory úložiště s přímým přístupem úspěšné.

![Co jsou svazky](media/plan-volumes/what-are-volumes.png)

Všechny svazky jsou přístupné pro všechny servery v clusteru ve stejnou dobu. Po vytvoření se zobrazí v **C:\ClusterStorage\\ ** na všech serverech.

![CSV – složka – snímek obrazovky](media/plan-volumes/csv-folder-screenshot.png)

## <a name="choosing-how-many-volumes-to-create"></a>Volba počtu svazků, které se mají vytvořit

Doporučujeme, abyste v clusteru provedli počet svazků násobkem počtu serverů. Pokud máte třeba 4 servery, budete mít jednotnější výkon se 4 celkovými objemy než 3 nebo 5. Díky tomu může cluster distribuovat svazek "vlastnictví" (jeden server zpracovává orchestraci metadat pro každý svazek) rovnoměrně mezi servery.

Pro Windows Server 2019 doporučujeme omezit celkový počet svazků na 64 svazků na cluster.

## <a name="choosing-the-filesystem"></a>Výběr systému souborů

Pro Prostory úložiště s přímým přístupem doporučujeme použít nový [odolný systém souborů (ReFS)](/en-us/windows-server/storage/refs/refs-overview) . ReFS je založený na účelu systému souborů Premier pro virtualizaci a nabízí spoustu výhod, včetně výrazné akcelerace výkonu a integrované ochrany před poškozením dat. Podporuje téměř všechny klíčové funkce systému souborů NTFS, včetně odstranění duplicitních dat ve Windows serveru verze 1709 a novějších. Podrobnosti najdete v [tabulce porovnání funkcí](/windows-server/storage/refs/refs-overview#feature-comparison) ReFS.

Pokud vaše úloha vyžaduje funkci, kterou odkazy ještě nepodporují, můžete místo toho použít systém souborů NTFS.

   > [!TIP]
   > Svazky s různými systémy souborů můžou existovat ve stejném clusteru.

## <a name="choosing-the-resiliency-type"></a>Výběr typu odolnosti

Svazky v Prostory úložiště s přímým přístupem poskytují odolnost proti problémům s hardwarem, jako jsou například selhání jednotky nebo serveru a umožňují nepřetržitou dostupnost během údržby serveru, jako jsou například aktualizace softwaru.

   > [!NOTE]
   > Jaké typy odolnosti si můžete vybrat nezávisle na tom, jaké typy jednotek máte.

### <a name="with-two-servers"></a>Se dvěma servery

Se dvěma servery v clusteru můžete použít obousměrné zrcadlení. Pokud používáte Windows Server 2019, můžete také použít vnořenou odolnost.

Obousměrné zrcadlení uchovává dvě kopie všech dat, jednu kopii na jednotkách na každém serveru. Efektivita úložiště je 50%; Pokud chcete zapsat 1 TB dat, budete potřebovat minimálně 2 TB kapacity fyzického úložiště ve fondu úložiště. Obousměrné zrcadlení může bezpečně tolerovat jednu selhání hardwaru v čase (jeden server nebo jednotka).

![dvoucestné zrcadlení](media/plan-volumes/two-way-mirror.png)

Vnořená odolnost (k dispozici pouze v systému Windows Server 2019) zajišťuje odolnost dat mezi servery s obousměrným zrcadlením a přidává odolnost na serveru s obousměrným zrcadlením nebo paritou podporující zrcadlení. Vnořování zajišťuje odolnost dat i v případě, že je jeden server restartován nebo není k dispozici. Jeho efektivita úložiště je 25 procent s vnořeným obousměrným zrcadlením a přibližně 35-40 procent pro paritní zrcadlením. Vnořená odolnost může bezpečně tolerovat dvě chyby hardwaru (dvě jednotky nebo server a jednotku na zbývajícím serveru). Kvůli této přidané odolnosti dat doporučujeme používat pro produkční nasazení clusterů se dvěma servery vnořenou odolnost, pokud používáte Windows Server 2019. Další informace najdete v tématu [vnořená odolnost](/windows-server/storage/storage-spaces/nested-resiliency).

![Vnořená zrcadlení – akcelerovaná parita](media/plan-volumes/nested-mirror-accelerated-parity.png)

### <a name="with-three-servers"></a>Se třemi servery

U tří serverů byste měli použít trojrozměrné zrcadlení pro lepší odolnost proti chybám a výkon. Trojrozměrné zrcadlení udržuje tři kopie všech dat, jednu kopii na jednotkách na každém serveru. Efektivita úložiště je 33,3% – pro zápis 1 TB dat potřebujete minimálně 3 TB kapacity fyzického úložiště ve fondu úložiště. Třícestný zrcadlení může bezpečně tolerovat [alespoň dva problémy s hardwarem (na disku nebo na serveru)](/windows-server/storage/storage-spaces/storage-spaces-fault-tolerance#examples). Pokud 2 uzly nebudou k dispozici, fond úložiště ztratí kvorum, protože 2/3 disků není k dispozici a virtuální disky nebudou přístupné. Uzel ale může být mimo provoz a jeden nebo víc disků na jiném uzlu se může zdařit a virtuální disky zůstanou online. Pokud například restartujete jeden server, když dojde k výpadku jiné jednotky nebo serveru, všechna data zůstanou bezpečná a nepřetržitě dostupná.

![Třícestný zrcadlový svazek](media/plan-volumes/three-way-mirror.png)

### <a name="with-four-or-more-servers"></a>Se čtyřmi nebo více servery

Se čtyřmi nebo více servery si můžete zvolit pro každý svazek, jestli se má použít trojrozměrné zrcadlení, duální parita (často označované jako "mazání kódu"), nebo můžete kombinaci dvou s paritou podporující zrcadlení.

Duální parita poskytuje stejnou odolnost proti chybám jako trojrozměrné zrcadlení, ale s lepší efektivitou úložiště. Se čtyřmi servery je efektivita úložiště 50,0%; Pokud chcete uložit 2 TB dat, budete potřebovat 4 TB kapacity fyzického úložiště ve fondu úložiště. Zvyšuje se tím 66,7% efektivita úložiště s sedmi servery a pokračuje až do 80,0 procenta efektivity úložiště. Kompromisy jsou tím, že kódování parity je náročné na výpočetní výkon, což může omezit jeho výkon.

![Duální parita](media/plan-volumes/dual-parity.png)

Typ odolnosti, který se použije, závisí na potřebách vaší úlohy. Tady je tabulka, která shrnuje, které úlohy jsou vhodné pro každý typ odolnosti, a také efektivitu výkonu a úložiště každého typu odolnosti.

| Typ odolnosti | Efektivita kapacity | Rychlost | Úkoly |
| ------------------- | ----------------------  | --------- | ------------- |
| **Zrcadlení**         | ![Efektivita úložiště zobrazující 33%](media/plan-volumes/3-way-mirror-storage-efficiency.png)<br>Třícestný zrcadlový svazek: 33% <br>Dvoucestné zrcadlení: 50%     |![Výkon ukazující 100%](media/plan-volumes/three-way-mirror-perf.png)<br> Nejvyšší výkon  | Virtualizované úlohy<br> Databáze<br>Další úlohy s vysokým výkonem |
| **Zrcadlení – urychlené parity** |![Efektivita úložiště znázorňující přibližně 50%](media/plan-volumes/mirror-accelerated-parity-storage-efficiency.png)<br> Závisí na poměru zrcadlení a parity. | ![Výkon znázorňující přibližně 20%](media/plan-volumes/mirror-accelerated-parity-perf.png)<br>Mnohem pomalejší než Zrcadlová, ale až dvakrát jako rychlá jako duální parita<br> Nejlepší pro velké sekvenční zápisy a čtení | Archivace a zálohování<br> Infrastruktura virtualizovaných ploch     |
| **Duální parita**               | ![Efektivita úložiště znázorňující přibližně 80%](media/plan-volumes/dual-parity-storage-efficiency.png)<br>4 servery: 50% <br>16 serverů: až 80% | ![Výkon ukazující přibližně na 10%](media/plan-volumes/dual-parity-perf.png)<br>Nejvyšší latence v/v & využití procesoru při zápisu<br> Nejlepší pro velké sekvenční zápisy a čtení | Archivace a zálohování<br> Infrastruktura virtualizovaných ploch  |

#### <a name="when-performance-matters-most"></a>V případě nejdůležitějších aspektů výkonu

Úlohy, které mají přísné požadavky na latenci nebo které potřebují velké množství smíšených vstupně-výstupních operací, například SQL Server databází nebo virtuálních počítačů s technologií Hyper-V s vysokým výkonem, by měly být spuštěny na svazcích, které k maximalizaci výkonu používají zrcadlení

   >[!TIP]
   > Zrcadlení je rychlejší než jakýkoli jiný typ odolnosti. Pro téměř všechny naše příklady výkonu používáme zrcadlení.

#### <a name="when-capacity-matters-most"></a>Co nejvíc kapacity

Úlohy, které zapisují zřídka, například datové sklady nebo "studené" úložiště, by měly běžet na svazcích, které používají duální paritu k maximalizaci efektivity úložiště. Některé další úlohy, jako jsou tradiční souborové servery, Infrastruktura virtuálních klientských počítačů (VDI) nebo jiné, které nevytvářejí spoustu rychlých vstupně-výstupních operací s náhodným vstupem a/nebo nevyžadují nejlepší výkon, můžou ve vašem uvážení taky využívat duální paritu. Parita nevyhnutelně zvyšuje využití procesoru a latence v/v, zejména zápisy, ve srovnání s zrcadlením.

#### <a name="when-data-is-written-in-bulk"></a>Při hromadném zápisu dat

Úlohy, které zapisují velký počet sekvenčních průchodů, jako jsou například archivace nebo cíle zálohování, mají jinou možnost: jeden svazek může kombinovat zrcadlení a duální paritu. Zapisuje první pozemky do zrcadlené části a postupně se přesunou do paritní části později. Tím se zrychluje ingestování a snížení využití prostředků, když se dostanou Velká zápisy, což umožňuje kódování parity náročné na výpočetní výkon při delší dobu. Při změně velikosti částí zvažte, že množství zápisů, které se provedou najednou (například jedna denní záloha), by mělo být v rámci zrcadlové části vhodné. Pokud například obdržíte 100 GB jednou denně, zvažte použití zrcadlení pro 150 GB až 200 GB a duální paritě pro zbytek.

Výsledná efektivita úložiště závisí na zvolených proportech. Příklady najdete v [této ukázce](https://www.youtube.com/watch?v=-LK2ViRGbWs&t=36m55s) .

   > [!TIP]
   > Pokud obdržíte prudký pokles výkonu zápisu zablokuje prostřednictvím ingestování dat, může to znamenat, že zrcadlová část není dostatečně velká nebo že parita s možností zrcadlení není pro váš případ použití vhodná. Pokud například výkon zápisu klesá z 400 MB/s na 40 MB/s, zvažte rozšíření zrcadlové části nebo přepnutí na Třícestný zrcadlový svazek.

### <a name="about-deployments-with-nvme-ssd-and-hdd"></a>O nasazeních pomocí NVMe, SSD a HDD

V nasazení se dvěma typy jednotek poskytují rychlejší jednotky ukládání do mezipaměti, zatímco pomalejší jednotky poskytují kapacitu. K tomu dochází automaticky – další informace najdete v tématu [Principy mezipaměti v prostory úložiště s přímým přístupem](/windows-server/storage/storage-spaces/understand-the-cache). V takových nasazeních se všechny svazky nakonec nacházejí na stejném typu jednotek – kapacitní jednotky.

V nasazeních se všemi třemi typy jednotek poskytuje mezipaměť pouze nejrychlejší jednotky (NVMe) a zajišťují tak kapacitu dvěma typy jednotek (SSD a HDD). U každého svazku můžete zvolit, zda se nachází výhradně na úrovni SSD, výhradně na úrovni HDD, nebo na to, jestli se tyto dva rozsahy nacházejí.

   > [!IMPORTANT]
   > K umístění většiny úloh citlivých na výkon na všech bliknutích doporučujeme použít vrstvu SSD.

## <a name="choosing-the-size-of-volumes"></a>Výběr velikosti svazků

V systému Windows Server 2019 doporučujeme omezit velikost každého svazku na 64 TB.

   > [!TIP]
   > Pokud používáte řešení zálohování, které spoléhá na službu Stínová kopie svazku (VSS) a poskytovatele softwaru Volsnap, jak je běžné pro úlohy souborového serveru – omezení velikosti svazku na 10 TB zvýší výkon a spolehlivost. Řešení pro zálohování, která používají novější rozšíření Hyper-V RCT API a/nebo nativní rozhraní API pro zálohování SQL, fungují až do 32 TB a i později.

### <a name="footprint"></a>Nároky

Velikost svazku odkazuje na jeho použitelnou kapacitu, na množství dat, která může ukládat. K tomu slouží parametr **-Size** rutiny **New-Volume** a pak se při spuštění rutiny **Get-Volume** zobrazí ve vlastnosti **Size** .

Velikost se liší od kapacity svazku *, což*je celková kapacita fyzického úložiště, kterou zabírá fond úložiště. Nároky závisí na typu odolnosti. Například svazky, které používají trojrozměrné zrcadlení, mají třikrát větší nároky na velikost.

Nároky na vaše svazky se musí vejít do fondu úložiště.

![velikost – oproti](media/plan-volumes/size-versus-footprint.png)

### <a name="reserve-capacity"></a>Rezervní kapacita

Když se v nepřidělené kapacitě fondu úložiště zachová nějaká kapacita, připraví se na ně místo na disku, aby se zvýšila bezpečnost a výkon dat. Pokud je k dispozici dostatečná kapacita, okamžitá a místní oprava může obnovit svazky do plné odolnosti, dokonce i před tím, než se neúspěšné jednotky nahradí. K tomu dojde automaticky.

Doporučujeme, abyste zavedli ekvivalent jedné jednotky kapacity na server, až 4 jednotky. Můžete si vyhradit Další informace podle vašeho uvážení, ale toto minimální doporučení zaručuje okamžitou a místní opravu, může po selhání jakékoli jednotky být úspěšné.

![rezervační](media/plan-volumes/reserve.png)

Například pokud máte 2 servery a používáte 1 TB kapacitních jednotek, nastavte vyhrazené množství 2 × 1 = 2 TB fondu jako rezerva. Pokud máte 3 servery a 1 TB kapacitních jednotek, nastavte rezervovat 3 x 1 = 3 TB. Pokud máte minimálně 4 servery a kapacitu na 1 TB, nastavte rezervu na 4 x 1 = 4 TB.

   >[!NOTE]
   > V clusterech s jednotkami všech tří typů (NVMe + SSD + HDD) doporučujeme rezervovat ekvivalent jedné jednotky SSD Plus jeden pevný disk na server, a to až na 4 jednotky každého.

## <a name="example-capacity-planning"></a>Příklad: plánování kapacity

Zvažte 1 4-serverový cluster. Každý server má pro kapacitu několik jednotek mezipaměti a 16 až 2 TB jednotek.

```
4 servers x 16 drives each x 2 TB each = 128 TB
```

Od tohoto 128 TB ve fondu úložiště jsme nastavili nevyužité čtyři jednotky nebo 8 TB, aby k nim na pracovišti docházelo bez nespěcháte, aby se jednotky po selhání nahradily. Tím se ve fondu vytvoří 120 TB fyzické kapacity úložiště, se kterou můžeme vytvářet svazky.

```
128 TB – (4 x 2 TB) = 120 TB
```

Předpokládejme, že naše nasazení potřebuje hostovat některé vysoce aktivní virtuální počítače Hyper-V, ale máme spoustu studeného úložiště – staré soubory a zálohy musíme zachovat. Vzhledem k tomu, že máme čtyři servery, vytvoříme čtyři svazky.

Pojďme virtuální počítače umístit na první dva svazky, *Volume1* a *Volume2*. Pro zajištění odolnosti proti chybám vybíráme jako systém souborů ReFS (pro rychlejší vytváření a kontrolní body) a trojrozměrné zrcadlení. Podíváme se do chladírny na dalších dvou svazcích, na svazcích *3* a na *svazcích 4*. Jako systém souborů zvolíme NTFS (pro odstranění duplicitních dat) a duální parita odolnosti proti chybám, aby bylo možné maximalizovat kapacitu.

Nepotřebujeme, aby měly všechny svazky stejnou velikost, ale pro jednoduchost, ale můžeme je udělat na všech 12 TB.

*Volume1* a *Volume2* budou mít 12 tb × 33,3% 36 efektivitu kapacity fyzického úložiště.

*Volume3* a *Volume4* budou mít 12 TB × 50,0% efektivitu = 24 TB kapacity fyzického úložiště.

```
36 TB + 36 TB + 24 TB + 24 TB = 120 TB
```

Čtyři svazky přesně vyhovují kapacitě fyzického úložiště, která je k dispozici ve vašem fondu. Ideální!

![příklad](media/plan-volumes/example.png)

   >[!TIP]
   > Nemusíte vytvářet všechny svazky hned. Svazky můžete kdykoli roztáhnout nebo vytvořit nové svazky později.

V zájmu jednoduchosti tento příklad používá desítkové jednotky (Base-10) v průběhu, což znamená 1 TB = 1 000 000 000 000 bajtů. Množství úložišť v systému Windows se ale zobrazuje v binárních jednotkách (2). Například každá 2 TB jednotka by se zobrazila jako 1,82 TiB ve Windows. Podobně se fond úložiště 128 TB zobrazí jako 116,41 TiB. To se očekává.

## <a name="usage"></a>Využití

Viz [vytváření svazků v Azure Stack HCI](../manage/create-volumes.md).

## <a name="next-steps"></a>Další kroky

Další informace najdete v tématu také:

- [Přehled Azure Stack HCI](../overview.md)
- [Výběr jednotek pro Prostory úložiště s přímým přístupem](choose-drives.md)
- [Odolnost proti chybám a efektivita úložiště](fault-tolerance.md)