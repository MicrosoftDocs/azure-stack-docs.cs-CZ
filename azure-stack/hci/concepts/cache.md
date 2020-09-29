---
title: Principy mezipaměti fondu úložiště v Azure Stack HCL
description: Způsob ukládání do mezipaměti pro čtení a zápis funguje v Prostory úložiště s přímým přístupem a Azure Stack HCI.
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 09/04/2020
ms.openlocfilehash: 9a15b953ffe2229d7f92bea998392b8570f481de
ms.sourcegitcommit: 4af79f4fa2598d57c81e994192c10f8c6be5a445
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89742527"
---
# <a name="understanding-the-storage-pool-cache-in-azure-stack-hci"></a>Principy mezipaměti fondu úložiště v Azure Stack HCL

> Platí pro: Azure Stack HCI, verze 20H2; Windows Server 2019

Azure Stack HCI obsahuje integrovanou mezipaměť na straně serveru pro zajištění maximálního výkonu úložiště. Je to velká, Trvalá mezipaměť pro čtení *a* zápis v reálném čase. Mezipaměť se konfiguruje automaticky při nasazení Azure Stack HCL. Ve většině případů se nevyžaduje žádná ruční Správa. Způsob fungování mezipaměti závisí na typech jednotek, které jsou k dispozici.

Následující video se dohlíží na Další informace o tom, jak funguje ukládání do mezipaměti pro Prostory úložiště s přímým přístupem, základní virtualizační technologie úložiště na Azure Stack HCI a také další informace o návrhu.

<strong>Prostory úložiště s přímým přístupem požadavky na návrh</strong><br>(20 minut)<br>
<iframe src="https://channel9.msdn.com/Blogs/windowsserver/Design-Considerations-for-Storage-Spaces-Direct/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="drive-types-and-deployment-options"></a>Typy jednotek a možnosti nasazení

Azure Stack HCL aktuálně pracuje se čtyřmi typy jednotek:

| Typ jednotky | Popis |
|----------------------|--------------------------|
|![PMem](media/choose-drives/pmem-100px.png)|**PMem** odkazuje na trvalou paměť, což je nový typ úložiště s nízkou latencí a vysokým výkonem.|
|![NVMe](media/choose-drives/NVMe-100-px.png)|**NVMe** (nestálá paměť Express) odkazuje na jednotky Solid-State, které přímo sedí na sběrnici PCIe. Obecné faktory pro formuláře jsou 2,5 "U. 2, PCIe Add-in-Card (AIC) a M. 2. NVMe nabízí vyšší propustnost vstupně-výstupních operací za sekundu s nižší latencí než jakýkoli jiný typ disku, který podporujeme dnes s výjimkou PMem.|
|![SSD](media/choose-drives/SSD-100-px.png)|**SSD** odkazuje na jednotky SSD, které se připojují prostřednictvím konvenčního SATA nebo SAS.|
|![HDD](media/choose-drives/HDD-100-px.png)|**HDD** odkazuje na rotační a magnetické jednotky pevného disku, které nabízejí obrovské kapacity úložiště.|

Můžou se kombinovat různými způsoby, které jsme seskupují do dvou kategorií: "all-Flash" a "hybrid".

### <a name="all-flash-deployment-possibilities"></a>Možnosti nasazení all-Flash

Nasazení all-Flash se zaměřuje na maximalizaci výkonu úložiště a nezahrnují jednotky pevného disku (HDD).

![Diagram zobrazuje všechna nasazení v prostředí Flash, včetně N V M e pro kapacitu, N V M e pro mezipaměť s s s D pro kapacitu a s S D pro kapacitu.](media/cache/All-Flash-Deployment-Possibilities.png)

### <a name="hybrid-deployment-possibilities"></a>Možnosti hybridního nasazení

Hybridní nasazení mají za cíl vyvážit výkon a kapacitu nebo maximalizovat kapacitu a zahrnout rotační jednotky pevného disku (HDD).

![Diagram zobrazuje hybridní nasazení, včetně N V M e pro mezipaměť s H D pro kapacitu, s. s D pro mezipaměť s H D d pro kapacitu a N V M e pro mezipaměť s h d d + s D pro kapacitu.](media/cache/Hybrid-Deployment-Possibilities.png)

## <a name="cache-drives-are-selected-automatically"></a>Automaticky se vyberou jednotky mezipaměti.

V nasazeních s více typy jednotek Azure Stack HCI automaticky používá všechny jednotky "nejrychlejší" typ pro ukládání do mezipaměti. Zbývající jednotky se použijí k ukládání.

Typ "nejrychlejší" je určen podle následující hierarchie.

![Diagram znázorňuje typy disků uspořádané rychleji na pomalejší v pořadí N V M e, S D, neoznačený disk představující H D d.](media/cache/Drive-Type-Hierarchy.png)

Pokud máte například NVMe a SSD, bude NVMe ukládat do mezipaměti pro SSD.

Pokud máte SSD a HDD, bude SSD ukládat do mezipaměti pro HDD.

   >[!NOTE]
   > Jednotky mezipaměti nepřispívají k použitelné kapacitě úložiště. Všechna data uložená v mezipaměti se také ukládají jinde, nebo se nacházejí v nepatrných fázích. To znamená, že celková volná kapacita úložiště vašeho nasazení je součtem pouze jednotek kapacity.

Pokud jsou všechny jednotky stejného typu, není žádná mezipaměť nakonfigurovaná automaticky. Máte možnost ručně nakonfigurovat jednotky s vyšší životností pro ukládání do mezipaměti pro jednotky s nižší životností stejného typu – viz oddíl [Ruční konfigurace](#manual-configuration) , kde se dozvíte, jak.

   >[!TIP]
   > Ve všech nasazeních NVMe nebo All-SSD, zejména ve velmi velkém měřítku, které nemají žádné jednotky "spotřebované" v mezipaměti, může smysluplně zlepšit efektivitu úložiště.

## <a name="cache-behavior-is-set-automatically"></a>Chování mezipaměti se nastaví automaticky.

Chování mezipaměti je určeno automaticky na základě typů jednotek, které jsou ukládány do mezipaměti pro. Při ukládání do mezipaměti pro jednotky SSD (například NVMe caching pro SSD) se do mezipaměti ukládají pouze zápisy. Při ukládání do mezipaměti pro jednotky pevného disku (například ukládání do mezipaměti SSD pro HDD) jsou čtení i zápisy ukládány do mezipaměti.

![Diagram, který porovnává ukládání do mezipaměti pro všechny-Flash, kde jsou zápisy do mezipaměti a čtení nejsou, s hybridem, kde jsou čtení i zápisy ukládány do mezipaměti.](media/cache/Cache-Read-Write-Behavior.png)

### <a name="write-only-caching-for-all-flash-deployments"></a>Ukládání do mezipaměti jen pro zápis pro nasazení ve všech bliknutích

Při ukládání do mezipaměti pro jednotky SSD (NVMe nebo SSD) se do mezipaměti ukládají pouze zápisy. Tím se snižuje kapacita jednotek kapacit, protože mnoho zápisů a opakovaných zápisů se může v mezipaměti považovat za nepotřebnou dobu, čímž se zkrátí kumulativní provoz na kapacitní jednotky a prodlouží se jejich životnost. Z tohoto důvodu doporučujeme pro mezipaměť vybrat [vyšší životnost a jednotky optimalizované pro zápis](http://whatis.techtarget.com/definition/DWPD-device-drive-writes-per-day) . Kapacitní jednotky mohou rozumně snížit životnost zápisu.

Vzhledem k tomu, že čtení nijak významně neovlivňuje životnost blesku a protože jednotky SSD (Solid-State Drive) všeobecně nabízejí nízkou latenci čtení, čtení nejsou uloženy v mezipaměti: jsou obsluhovány přímo z kapacitních jednotek (s výjimkou případů, kdy byla data dříve vytvořena). Díky tomu může být mezipaměť výhradně vyhrazena pro zápis a maximalizace jejich efektivity.

Výsledkem je, že se vlastnosti zápisu, jako je latence zápisu, načtou jednotkami mezipaměti, zatímco charakteristiky čtení jsou vydávány jednotkami kapacity. Obě jsou konzistentní, předvídatelné a uniformní.

### <a name="readwrite-caching-for-hybrid-deployments"></a>Mezipaměť pro čtení a zápis pro hybridní nasazení

Při ukládání do mezipaměti pro jednotky pevného disku (HDD) jsou čtení *i* zápisy ukládány do mezipaměti, aby se zajistila latence s podobným rozhraním Flash (často ~ 10x lepší) pro obě. Mezipaměť pro čtení ukládá poslední a často načtená data pro rychlý přístup a minimalizuje náhodný provoz do HDD. (Vzhledem k prodlevám při hledání a rotaci je doba latence a ztráty způsobená náhodným přístupem k disku velká.) Zápisy jsou ukládány do mezipaměti za účelem zvýšení zátěže a stejně jako dříve, pro sloučení zápisů a opětovného zápisu a minimalizaci kumulativního provozu na kapacitní jednotky.

Azure Stack HCI implementuje algoritmus, který rozpustí zápisy před rozbalením, aby emuluje vzor vstupně-výstupní operace s diskem, který se jeví jako sekvenční, i když je skutečná vstupně-výstupní operace přicházející z úlohy (například virtuální počítače) náhodná. Tím se maximalizuje IOPS a propustnost do HDD.

### <a name="caching-in-deployments-with-nvme-ssd-and-hdd"></a>Ukládání do mezipaměti v nasazeních pomocí NVMe, SSD a HDD

Pokud jsou přítomny jednotky všech tří typů, NVMe jednotky poskytují ukládání do mezipaměti pro SSD a HDD. Chování je popsané výše: pouze zápisy jsou ukládány do mezipaměti pro SSD a čtení i zápisy jsou ukládány do mezipaměti pro HDD. Zatížení mezipaměti pro HDD se rovnoměrně rozděluje mezi jednotky mezipaměti.

## <a name="summary"></a>Shrnutí

Tato tabulka shrnuje, které jednotky se používají pro ukládání do mezipaměti, které se používají pro kapacitu, a to, co je chování ukládání do mezipaměti pro jednotlivé možnosti nasazení.

| Nasazení     | Jednotky mezipaměti                        | Jednotky kapacity | Chování mezipaměti (výchozí)  |
| -------------- | ----------------------------------- | --------------- | ------------------------- |
| Všechny NVMe         | Žádné (volitelné: ruční konfigurace) | NVMe            | Pouze pro zápis (Pokud je nakonfigurován)  |
| Všechny jednotky SSD          | Žádné (volitelné: ruční konfigurace) | SSD             | Pouze pro zápis (Pokud je nakonfigurován)  |
| NVMe + SSD       | NVMe                                | SSD             | Jen pro zápis                  |
| NVMe + HDD       | NVMe                                | HDD             | Čtení + zápis                |
| SSD + HDD        | SSD                                 | HDD             | Čtení + zápis                |
| NVMe + SSD + HDD | NVMe                                | SSD + HDD       | Čtení + zápis pro pevný disk, pouze pro zápis pro SSD  |

## <a name="server-side-architecture"></a>Architektura na straně serveru

Mezipaměť je implementována na úrovni jednotky: jednotlivé jednotky mezipaměti v jednom serveru jsou vázány na jednu nebo mnoho jednotek kapacity na jednom serveru.

Vzhledem k tomu, že mezipaměť je pod zbývající částí zásobníku úložiště definovaného softwarem Windows, nemá ani žádné povědomí o konceptech, jako jsou prostory úložiště nebo odolnost proti chybám. Můžete si ho představit jako vytvoření "hybrid" (část Flash, část disku), která se pak zobrazí v systému Windows. Stejně jako u skutečné hybridní jednotky je pohyb mezi horkou a studenou daty mezi rychlejší a pomalejší částí fyzického média skoro neviditelný i mimo něj.

Vzhledem k tom, že odolnost v Azure Stack HCI je alespoň na úrovni serveru (to znamená, že kopie dat se vždycky zapisují na různé servery. maximálně jedna kopie na server), data v mezipaměti těží ze stejné odolnosti jako data, která nejsou v mezipaměti.

![Diagram představuje tři servery, které jsou spojeny pomocí trojrozměrného zrcadlení ve vrstvě prostoru úložiště, který přistupuje k vrstvě mezipaměti N V M e, které mají přístup k neoznačeným jednotkám kapacity.](media/cache/Cache-Server-Side-Architecture.png)

Například při použití trojrozměrného zrcadlení se tři kopie dat zapisují na různé servery, kde se nacházejí v mezipaměti. Bez ohledu na to, zda jsou později v nezpracované fázi nebo ne, budou vždy existovat tři kopie.

## <a name="drive-bindings-are-dynamic"></a>Vazby jednotky jsou dynamické.

Vazba mezi mezipamětí a úložnými jednotkami může mít libovolný poměr, od 1:1 do 1:12 nebo po. Přizpůsobuje se dynamicky při každém přidání nebo odebrání jednotek, například při vertikálním navýšení nebo po selhání. To znamená, že kdykoli budete chtít, můžete přidat jednotky mezipaměti nebo jednotky kapacity samostatně.

![Animovaný diagram zobrazuje dvě jednotky mezipaměti N V M V, dynamicky namapovány na první čtyři, tedy na šest a pak na osm kapacitních jednotek.](media/cache/Dynamic-Binding.gif)

Pro symetrii doporučujeme, abyste si vynásobení počtu kapacitních jednotek a několika jednotek mezipaměti. Pokud máte například 4 jednotky mezipaměti, budete mít více i výkon s 8 jednotkami kapacity (1:2) než 7 nebo 9.

## <a name="handling-cache-drive-failures"></a>Zpracování selhání jednotky mezipaměti

Pokud se jednotka mezipaměti nezdařila, všechny zápisy, které ještě nebyly odstraněny, budou ztraceny *na místní server*, což znamená, že existují pouze na ostatních kopiích (na jiných serverech). Stejně jako u jakékoli jiné chyby jednotky úložiště můžou prostory úložiště a automaticky obnovit pomocí konzultací s pozůstalými kopiemi.

Pro krátkou dobu se na diskové jednotky, které byly vázány na jednotku ztracených mezipamětí, zobrazí stav není v pořádku. Jakmile dojde k opětovné vazbě mezipaměti (automatické) a oprava dat se dokončila (automaticky), bude se zobrazovat jako v pořádku.

Tento scénář vyžaduje, aby na jeden server bylo nutné zadat minimálně dvě jednotky mezipaměti, aby se zajistil výkon.

![Animovaný diagram znázorňuje dvě jednotky mezipaměti S S D namapované na šest kapacitních jednotek, dokud jedna jednotka mezipaměti neprojde, což způsobí, že všechny šesti jednotky budou namapovány na zbývající jednotku mezipaměti.](media/cache/Handling-Failure.gif)

Pak můžete jednotku mezipaměti nahradit stejně jako jakoukoli jinou náhradu jednotky.

   >[!NOTE]
   > Možná budete muset vypnout, abyste bezpečně nahradili NVMe, který je doplňkovou kartou (AIC) nebo M. 2 Form Factor.

## <a name="relationship-to-other-caches"></a>Vztah k jiným mezipamětem

V zásobníku úložiště definovaném softwarem Windows je několik dalších nesouvisejících mezipamětí. Příklady zahrnují mezipaměť se zpětným zápisem pro prostory úložiště a mezipaměť pro čtení v paměti sdílený svazek clusteru (CSV).

Při Azure Stack HCI by se mezipaměť se zpětným zápisem do mezipaměti úložiště neměla měnit z výchozího chování. Například parametry, jako je například **-WriteCacheSize** v rutině **New-Volume** , by neměly být používány.

Můžete použít mezipaměť sdíleného svazku clusteru, nebo ne – to je až na sebe. Ve výchozím nastavení je ve Azure Stack HCI, ale nekoliduje s mezipamětí popsanou v tomto tématu. V některých scénářích může poskytovat cenné výhody výkonu. Další informace najdete v tématu [použití mezipaměti pro čtení v paměti sdíleného svazku clusteru s Azure Stack HCL](../manage/use-csv-cache.md).

## <a name="manual-configuration"></a>Ruční konfigurace

U většiny nasazení není nutná ruční konfigurace. V případě potřeby si přečtěte následující oddíly.

Pokud po instalaci potřebujete změnit model zařízení mezipaměti, upravte dokument podpory součástí Health Service, jak je popsáno v [Health Service přehledu](/windows-server/failover-clustering/health-service-overview#supported-components-document).

### <a name="specify-cache-drive-model"></a>Zadat model jednotky mezipaměti

V nasazeních, kde jsou všechny jednotky stejného typu, jako jsou všechna nasazení NVMe nebo All-SSD, není nakonfigurovaná žádná mezipaměť, protože systém Windows nemůže rozlišovat vlastnosti, jako je například dlouhodobá doba zápisu, a to automaticky mezi jednotky stejného typu.

Chcete-li použít jednotky s vyšší životností pro ukládání do mezipaměti pro jednotky s nižší životností stejného typu, můžete určit, který model jednotky bude použit s parametrem **-CacheDeviceModel** rutiny **Enable-ClusterS2D** . Všechny jednotky tohoto modelu budou použity pro ukládání do mezipaměti.

   >[!TIP]
   > Ujistěte se, že odpovídáte řetězci modelu přesně tak, jak se zobrazuje ve výstupu **Get-fyzický disk**.

####  <a name="example"></a>Příklad

Nejdřív Získejte seznam fyzických disků:

```PowerShell
Get-PhysicalDisk | Group Model -NoElement
```

Tady je příklad výstupu:

```
Count Name
----- ----
    8 FABRIKAM NVME-1710
   16 CONTOSO NVME-1520
```

Potom zadejte následující příkaz, který určuje model zařízení mezipaměti:

```PowerShell
Enable-ClusterS2D -CacheDeviceModel "FABRIKAM NVME-1710"
```

Můžete ověřit, jestli se jednotky, které jste chtěli použít pro ukládání do mezipaměti, spuštěním příkazu **Get-fyzický disk** v PowerShellu a ověřením, že jeho vlastnost **použití** říká **"Deník"**.

### <a name="manual-deployment-possibilities"></a>Možnosti ručního nasazení

Ruční konfigurace umožňuje následující možnosti nasazení:

![Diagram zobrazuje možnosti nasazení, včetně N V M e pro mezipaměť i kapacitu, S S D pro mezipaměť i kapacitu, a s D pro kapacitu cache a Mixed S S d a H d d pro kapacitu.](media/cache/Exotic-Deployment-Possibilities.png)

### <a name="set-cache-behavior"></a>Nastavení chování mezipaměti

Je možné přepsat výchozí chování mezipaměti. Například můžete nastavit, aby čtení mezipaměti bylo i v případě nasazení typu all-Flash. Nemůžeme změnit chování, pokud neurčíte, že výchozí hodnota nevyhovuje vašim úlohám.

Chcete-li toto chování přepsat, použijte rutinu **set-ClusterStorageSpacesDirect** a parametry **CacheModeSSD** a **-CacheModeHDD** . Parametr **CacheModeSSD** nastaví chování mezipaměti při ukládání do mezipaměti pro jednotky Solid-State. Parametr **CacheModeHDD** nastaví chování mezipaměti při ukládání do mezipaměti pro jednotky pevného disku.

K ověření, zda je chování nastaveno, lze použít **příkaz Get-ClusterStorageSpacesDirect** .

#### <a name="example"></a>Příklad

Nejprve získejte nastavení Prostory úložiště s přímým přístupem:

```PowerShell
Get-ClusterStorageSpacesDirect
```

Tady je příklad výstupu:

```
CacheModeHDD : ReadWrite
CacheModeSSD : WriteOnly
```

Pak proveďte tyto akce:

```PowerShell
Set-ClusterStorageSpacesDirect -CacheModeSSD ReadWrite

Get-ClusterS2D
```

Tady je příklad výstupu:

```
CacheModeHDD : ReadWrite
CacheModeSSD : ReadWrite
```

## <a name="sizing-the-cache"></a>Změna velikosti mezipaměti

Mezipaměť by měla být upravena tak, aby vyhovovala pracovní sadě (data, která jsou v daném čase aktivně čtena nebo zapsána) vašich aplikací a úloh.

To je zvlášť důležité v hybridních nasazeních s pevnými disky. Pokud aktivní pracovní sada překročí velikost mezipaměti, nebo pokud aktivní pracovní sada přesáhne příliš rychlou rychlost, Neúspěšné přístupy do mezipaměti pro čtení se budou muset prodloužit a neubližujeme celkový výkon.

Pomocí integrovaného nástroje sledování výkonu (PerfMon.exe) v systému Windows můžete zkontrolovat četnost neúspěšných přístupů do mezipaměti. Konkrétně můžete porovnat počet **neúspěšných přístupů do mezipaměti** z čítače **hybridního disku úložiště clusteru** , který je nastavený na celkový počet IOPS čtení vašeho nasazení. Každý "hybridní disk" odpovídá jedné jednotce kapacity.

Například 2 jednotky mezipaměti vázané na 4 jednotky kapacity mají za následek 4 instance objektů hybridního disku na jeden server.

![Výkon – monitorování](media/cache/PerfMon.png)

Neexistuje žádné univerzální pravidlo, ale v případě, že v příliš mnoha čteních chybí mezipaměť, může být nedostatečné a měli byste zvážit přidání jednotek mezipaměti pro rozšíření mezipaměti. V případě potřeby můžete přidat jednotky mezipaměti nebo jednotky kapacity nezávisle na sobě.

## <a name="next-steps"></a>Další kroky

Další informace o úložišti najdete v článku:

- [Odolnost proti chybám a efektivita úložiště](fault-tolerance.md)
- [Kvorum clusteru a fondu](quorum.md)
