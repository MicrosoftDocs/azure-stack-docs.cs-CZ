---
title: Použití DISKSPD k testování výkonu úložiště úloh
description: V tomto tématu najdete pokyny k použití DISKSPD k testování výkonu úložiště úloh.
author: jasonnyi
ms.author: jasonyi
ms.topic: how-to
ms.date: 11/17/2020
ms.openlocfilehash: 8d8a78d0a5faaa3c041e17c3c38f208132f19834
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2021
ms.locfileid: "98254785"
---
# <a name="use-diskspd-to-test-workload-storage-performance"></a>Použití DISKSPD k testování výkonu úložiště úloh

>Platí pro: Azure Stack HCI, verze 20H2; Windows Server 2019

V tomto tématu najdete pokyny k použití DISKSPD k testování výkonu úložiště úloh. Máte nastavený cluster Azure Stack HCI, který je připravený k použití. Skvělé, ale jak zjistíte, jestli jste dostali přislíbenou metriku výkonu, ať už jde o latenci, propustnost nebo IOPS? To je, když možná budete chtít zapnout DISKSPD. Po přečtení tohoto tématu budete vědět, jak spustit DISKSPD, pochopit podmnožinu parametrů, interpretovat výstup a získat obecné informace o proměnných, které ovlivňují výkon úložiště úloh.

## <a name="what-is-diskspd"></a>Co je DISKSPD?
DISKSPD je vstupně-výstupní generování, nástroj příkazového řádku pro mikrosrovnávací testy. Skvělé, takže všechny tyto výrazy znamenají? Důvodem může být kdokoli, kdo nastavuje cluster Azure Stack HCI nebo fyzický server. Může se jednat o nastavení hostitelského prostředí pro web nebo pro zaměstnance spustit virtuální plochy. Bez ohledu na to, jaký je skutečný případ použití, pravděpodobně budete chtít simulovat test ještě předtím, než nasadíte svoji vlastní aplikaci. Testování aplikace ve skutečném scénáři je však často obtížné – to je místo, kde DISKSPD přichází.

DISKSPD je nástroj, který můžete přizpůsobit a vytvořit si tak vlastní syntetické úlohy a před nasazením otestovat svoji aplikaci. V takovém případě se jedná o nástroj, který vám poskytne volnost při konfiguraci a úpravách parametrů pro vytvoření konkrétního scénáře, který se podobá skutečnému zatížení. DISKSPD vám může poskytnout nakoukněte na to, co váš systém podporuje před nasazením. V jádru DISKSPD jednoduše vydává spoustu operací čtení a zápisu.

Nyní víte, co DISKSPD je, ale když byste ji měli použít? DISKSPD má obtížný čas, který emuluje složitá zatížení. DISKSPD je ale skvělé v případě, že vaše zatížení není přesně přibližné kopírováním souborů s jedním vláknem a potřebujete jednoduchý nástroj, který vytváří přijatelné výsledky směrného plánu.

## <a name="quick-start-install-and-run-diskspd"></a>Rychlý Start: instalace a spuštění DISKSPD
Bez dalších objektů ADO můžeme začít:

1. V počítači pro správu otevřete PowerShell jako správce a připojte se k cílovému počítači, který chcete testovat pomocí DISKSPD, a potom zadejte následující příkaz a stiskněte klávesu ENTER.

     ```powershell
     Enter-PSSession -ComputerName <TARGET_COMPUTER_NAME>
    ```

    V tomto příkladu používáme virtuální počítač (VM) s názvem "Uzel1".

1. Chcete-li stáhnout nástroj DISKSPD, zadejte následující příkazy a stiskněte klávesu ENTER:

     ```powershell
     $client = new-object System.Net.WebClient
    ```

     ```powershell
     $client.DownloadFile("https://github.com/Microsoft/diskspd/releases/download/v2.0.21a/DiskSpd-2.0.21a.zip","<ENTER_PATH>\DiskSpd-2.0.21a.zip")
    ```

1. K extrahování staženého souboru použijte následující příkaz:

     ```powershell
     Expand-Archive -LiteralPath <ENTERPATH>\DiskSpd-2.0.21a.zip -DestinationPath C:\DISKSPD
    ```

1. Změňte adresář na adresář DISKSPD a vyhledejte příslušný spustitelný soubor pro operační systém Windows, na kterém je cílový počítač spuštěn.

    V tomto příkladu používáme verzi amd64.

    > [!NOTE]
    > Nástroj DISKSPD si také můžete stáhnout přímo z [úložiště GitHub](https://github.com/microsoft/diskspd) , které obsahuje open source kód, a stránku wikiwebu, která podrobně popisuje všechny parametry a specifikace. V úložišti v části **vydané verze** vyberte odkaz pro automatické stažení souboru ZIP.

    V souboru ZIP se zobrazí tři podsložky: amd64 (64 – 32bitové systémy), x86 (32 systémy) a ARM64 (systémy ARM). Tyto možnosti umožňují spustit nástroj v každé verzi klienta nebo serveru Windows.

    :::image type="content" source="media/diskspd/download-directory.png" alt-text="Adresář pro stažení souboru DISKSPD. zip." lightbox="media/diskspd/download-directory.png":::

1. Spusťte DISKSPD pomocí následujícího příkazu PowerShellu. Nahraďte vše uvnitř hranatých závorek, včetně složených závorek s příslušným nastavením.

    ```powershell
     .\[INSERT_DISKSPD_PATH] [INSERT_SET_OF_PARAMETERS] [INSERT_CSV_PATH_FOR_TEST_FILE] > [INSERT_OUTPUT_FILE.txt]
    ```

    Tady je příklad příkazu, který můžete spustit:

    ```powershell
     .\diskspd -t2 -o32 -b4k -r4k -w0 -d120 -Sh -D -L -c5G C:\ClusterStorage\test01\targetfile\IO.dat > test01.txt
    ```

    > [!NOTE]
    > Pokud nemáte testovací soubor, použijte parametr **-c** k jeho vytvoření. Pokud použijete tento parametr, nezapomeňte při definování cesty použít název testovacího souboru. Například: [INSERT_CSV_PATH_FOR_TEST_FILE] = C:\ClusterStorage\CSV01\IO.dat. V příkladu příkazu je IO. dat název testovacího souboru a test01.txt je název výstupního souboru DISKSPD.

## <a name="specify-key-parameters"></a>Zadat parametry klíče
Dobře, které byly jednoduché? Bohužel k tomu máme víc. Pojďme si vybalit, co máme. Nejdřív existuje několik parametrů, se kterými se můžete Tinker, a může získat konkrétní. Použili jsme ale následující sadu parametrů standardních hodnot:

> [!NOTE]
> V parametrech DISKSPD se rozlišují velká a malá písmena.

**-T2**: označuje počet vláken na cílovou/testovací soubor. Toto číslo je často založené na počtu jader procesoru. V tomto případě se k navýšení jádra procesoru použila dvě vlákna.

**-o32**: označuje počet nezpracovaných vstupně-výstupních požadavků na cíl na vlákno. Tento postup se označuje také jako hloubka fronty a v tomto případě se 32 použil k navýšení procesoru.

**-b4K**: označuje velikost bloku v bajtech, KiB, MIB nebo GIB. V tomto případě se velikost bloku 4K použila k simulaci náhodných vstupně-výstupních testů.

**-r4K**: označuje náhodné vstupně-výstupní operace zarovnané na zadanou velikost v bajtech, KiB, MIB, GIB nebo blocích (přepíše parametr **-s** ). Obvyklá velikost 4K Byte byla použita pro správné zarovnání s velikostí bloku.

**-W0**: Určuje procento operací, které jsou požadavky na zápis (-W0 je ekvivalentní 100% čtení). V tomto případě byly pro účely jednoduchého testu použity zápisy o velikosti 0%.

**-D120**: Určuje dobu trvání testu, včetně chlazení nebo zahřívání. Výchozí hodnota je 10 sekund, ale pro všechny závažné úlohy doporučujeme používat aspoň 60 sekund. V tomto případě se 120 sekund použil k minimalizaci všech odlehlého množství.

**-SUW**: zakáže ukládání softwaru a hardwarového zápisu do mezipaměti (ekvivalent **-SH**).

**-D**: zachycuje statistiku IOPS, jako je směrodatná odchylka, v intervalech milisekund (na vlákno, podle cíle).

**-L**: měří statistiku latence.

**-C5G**: nastavuje ukázkovou velikost souboru použitou v testu. Dá se nastavit v bajtech, KiB, MiB, GiB nebo blocích. V tomto případě se použil cílový soubor o velikosti 5 GB.

Úplný seznam parametrů najdete v [úložišti GitHubu](https://github.com/Microsoft/diskspd/wiki/Command-line-and-parameters).

## <a name="understand-the-environment"></a>Pochopení prostředí
Výkon je vysoce závislý na vašem prostředí. Co je to naše prostředí? Naše specifikace zahrnuje Azure Stack cluster HCI s fondem úložiště a Prostory úložiště s přímým přístupem (S2D). Přesněji řečeno, existuje pět virtuálních počítačů: DC, Uzel1, Uzel2, Uzel3 a Node pro správu. Samotný cluster je cluster se třemi uzly, který má trojrozměrnou zrcadlovou strukturu odolnosti. Proto jsou zachovány tři kopie dat. Každý uzel v clusteru je Standard_B2ms virtuální počítač s maximálním limitem IOPS 1920. V rámci každého uzlu jsou k dispozici čtyři jednotky SSD úrovně Premium P30 s maximálním limitem IOPS 5000. Nakonec má každá jednotka SSD 1 TB paměti.

Vygenerujete testovací soubor v rámci sjednoceného oboru názvů, který poskytuje sdílený svazek clusteru (CSV) (C:\ClusteredStorage) pro použití celého fondu jednotek.

>[!NOTE]
> Ukázkové *prostředí nemá technologii* Hyper-V nebo vnořenou virtualizační strukturu.

Jak vidíte, je naprosto možné nezávisle dosáhnout mezní hodnoty IOPS nebo šířky pásma na limitu virtuálního počítače nebo jednotky. A proto je důležité pochopit velikost virtuálních počítačů a typ jednotky, protože mají omezení maximálního počtu IOPS i meze šířky pásma. Tato znalostní báze pomáhá najít kritická místa a pochopit výsledky výkonu. Další informace o tom, jaká velikost může být vhodná pro vaše zatížení, najdete v následujících zdrojích informací:

- [Velikost virtuálních počítačů](/azure/virtual-machines/sizes-general?bc=%2fazure%2fvirtual-machines%2flinux%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Typy disků](https://azure.microsoft.com/pricing/details/managed-disks/)

## <a name="understand-the-output"></a>Pochopení výstupu
Na základě vašeho porozumění parametrů a prostředí jste připraveni interpretovat výstup. Nejprve byl cílem předchozího testu maximální počet IOPS bez ohledu na latenci. Tímto způsobem můžete vizuálně zjistit, jestli jste v Azure dosáhli limitu umělého IOPS. Pokud chcete graficky vizualizovat celkový IOPS, použijte buď centrum pro správu systému Windows, nebo Správce úloh.

Následující diagram ukazuje, jak proces DISKSPD vypadá jako v našem ukázkovém prostředí. Zobrazuje příklad 1 operace zápisu MiB z uzlu bez koordinátora. Trojrozměrná struktura, společně s operací z uzlu bez koordinátora, vede ke dvěma segmentům směrování sítě a snížení výkonu. Pokud vás zajímá, co je uzel koordinátora, nedělejte si starosti! O tom se dozvíte v části [věci, které](#things-to-consider) je potřeba vzít v úvahu. Červené čtverce reprezentují virtuální počítač a označují místo na disku.

:::image type="content" source="media/diskspd/environment.png" alt-text="Ukázkové prostředí, které slouží k testování výkonu pomocí DISKSPD." lightbox="media/diskspd/environment.png":::

Teď, když máte vizuální porozumění, Podívejme se na čtyři hlavní části výstupu souboru. txt:
1. Nastavení vstupu
   
    Tato část popisuje příkaz, který jste spustili, vstupní parametry a další podrobnosti o testovacím běhu.

    :::image type="content" source="media/diskspd/command-input-parameters.png" alt-text="Příklad výstupu ukazuje parametry příkazu a Input." lightbox="media/diskspd/command-input-parameters.png":::

1. Podrobnosti o využití procesoru
   
    V této části se vysvětlují informace, jako je doba testování, počet vláken, počet dostupných procesorů a průměrné využití všech PROCESORových jader během testu. V tomto případě jsou k dispozici dvě jádra procesoru, která jsou průměrně přibližně 4,67% využití.

    :::image type="content" source="media/diskspd/cpu-details.png" alt-text="Příklady podrobností o procesoru." lightbox="media/diskspd/cpu-details.png":::

1. Celkový počet vstupně-výstupních operací
   
    V této části jsou tři pododdíly. V první části se zvýrazňují celková data výkonu včetně operací čtení i zápisu. Druhý a třetí oddíl rozdělí operace čtení a zápisu do samostatných kategorií.

    V tomto příkladu vidíte, že celkový počet vstupně-výstupních operací byl 234408 během doby trvání 120 sekund. Tedy IOPS = 234408/120 = 1953,30. Průměrná latence byla 32,763 milisekund a propustnost byla 7,63 MiB/s. Z dřívějších informací ví, že 1953,30 IOPS se blíží omezení 1920 IOPS pro náš Standard_B2ms virtuální počítač. Nevěří to? Pokud tento test znovu spustíte s použitím různých parametrů, například zvýšením hloubky fronty, zjistíte, že výsledky jsou stále omezené na tento počet.

    Poslední tři sloupce zobrazují směrodatnou odchylku IOPS v 17,72 (parametr-D), směrodatnou odchylku latence v 20,994 milisekundách (parametr z-L) a cestu k souboru.

      :::image type="content" source="media/diskspd/total-io.png" alt-text="Příklad ukazuje celkovou celkovou vstupně-výstupní data výkonu." lightbox="media/diskspd/total-io.png":::

    Z výsledků můžete rychle zjistit, jestli je konfigurace clusteru ještěrů. Můžete vidět, že dosáhli omezení virtuálního počítače 1920 před omezením SSD 5000. Pokud jste místo virtuálního počítače omezili disk SSD, mohli byste využít až 20000 vstupně-výstupních operací (4 jednotky × 5000) tak, že rozvedete testovací soubor na více jednotek.

    Na konci se musíte rozhodnout, jaké hodnoty jsou přijatelné pro konkrétní zatížení. Následující obrázek ukazuje několik důležitých vztahů, které vám pomůžou zvážit kompromisy:

    :::image type="content" source="media/diskspd/tradeoffs.png" alt-text="Obrázek znázorňuje kompromisy mezi úlohami." lightbox="media/diskspd/tradeoffs.png":::

    Druhý vztah na obrázku je důležitý a v některých případech se někdy označuje jako malý zákon. Zákon zavádí nápad, že existují tři charakteristiky, které řídí chování procesu a že stačí změnit pouze jeden, aby ovlivnil druhé dva, a tedy celý proces. A takže pokud nejste spokojeni s výkonem vašeho systému, máte k ovlivnění tří dimenzí volno. Zákonem v našem příkladu je, že IOPS je "propustnost" (vstupní výstupní operace za sekundu), latence je "doba ve frontě" a hloubka fronty je "inventář".

1. Analýza latence percentilu
   
    Tento poslední oddíl podrobně popisuje latenci percentilu podle typu operace výkonu úložiště z minimální hodnoty až po maximální hodnotu.

    Tato část je důležitá, protože určuje "kvalitu" vašeho IOPS. Ukazuje, kolik vstupně-výstupních operací bylo schopné dosáhnout určité hodnoty latence. Je až na vás, abyste se rozhodli přijatelné latence pro daný percentil.

    Kromě toho "devět" odkazuje na počet devíti. Například "3-devět" je ekvivalentem 99 percentilu. Počet devíti vystavuje, kolik vstupně-výstupních operací na daném percentilu běželo. Nakonec budete mít k dispozici bod, ve kterém již nedává smysl na to, aby byly hodnoty latence vážně nedostupné. V takovém případě vidíte, že hodnoty latence zůstávají po 4 až devět konstantní. V tomto okamžiku je hodnota latence založená jenom na jedné vstupně-výstupní operaci mimo operace 234408.

    :::image type="content" source="media/diskspd/storage-performance.png" alt-text="Příklad ukazuje latenci percentilu na typ operace výkonu úložiště." lightbox="media/diskspd/storage-performance.png":::

## <a name="things-to-consider"></a>Co je potřeba zvážit
Teď, když jste začali používat DISKSPD, je potřeba vzít v úvahu několik věcí, které vám pomohou získat výsledky testů reálného světa. Patří mezi ně i další platební pozornost pro vámi nastavené parametry, stav prostoru úložiště a proměnné, vlastnictví sdíleného svazku clusteru a rozdíl mezi DISKSPD a kopírováním souborů.

### <a name="diskspd-vs-real-world"></a>DISKSPD vs. Real-World
Umělý test DISKSPD poskytuje relativně srovnatelné výsledky pro skutečné úlohy. Je ale nutné věnovat pozornost všem nastaveným parametrům a zda odpovídají vašemu reálnému scénáři. Je důležité pochopit, že syntetické úlohy nebudou během nasazení nikdy přesně reprezentovat reálné úlohy vaší aplikace.

### <a name="preparation"></a>Příprava
Před spuštěním testu DISKSPD je k dispozici několik doporučených akcí. Mezi ně patří ověření stavu prostoru úložiště, kontrola využití prostředků, aby jiný program nenarušil test a připravuje správce výkonu, pokud chcete shromažďovat další data. Vzhledem k tomu, že cílem tohoto tématu je rychle získat DISKSPD, že se podrobně na konkrétní akce těchto akcí. Další informace najdete v tématu [testování výkonu prostorů úložiště pomocí syntetických úloh ve Windows serveru](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn894707(v=ws.11)).

### <a name="variables-that-affect-performance"></a>Proměnné ovlivňující výkon
Výkon úložiště je jemnáá věc. To znamená, že existuje mnoho proměnných, které mohou ovlivnit výkon. A proto je pravděpodobně možné zaznamenat číslo, které je nekonzistentní s vašimi očekáváními. Následující zvýrazní některé proměnné, které mají vliv na výkon, i když se nejedná o vyčerpávající seznam:
- Šířka pásma sítě
- Volba odolnosti
- Konfigurace disku úložiště: NVME, SSD, HDD
- Vstupně-výstupní vyrovnávací paměť
- Mezipaměť
- Konfigurace RAID
- Směrování sítě
- Rychlosti jednotek pevného disku

### <a name="csv-ownership"></a>Vlastnictví sdíleného svazku clusteru
Uzel je známý jako vlastník svazku nebo uzel **koordinátora** (nekoordinátorský uzel by byl uzel, který nevlastní konkrétní svazek). Každému standardnímu svazku je přiřazen uzel a ostatní uzly mají přístup k tomuto standardnímu svazku prostřednictvím směrování sítě, což vede ke snížení výkonu (vyšší latence).

Podobně má sdílený svazek clusteru (CSV) také "vlastník". Sdílený svazek clusteru je ale "dynamický" v tom smyslu, že se bude směrovat a měnit vlastnictví pokaždé, když restartujete systém (RDP). V důsledku toho je důležité ověřit, že se DISKSPD spouští z uzlu koordinátora, který vlastní sdílený svazek clusteru. V takovém případě může být nutné ručně změnit vlastnictví sdíleného svazku clusteru.

Potvrzení vlastnictví sdíleného svazku clusteru:
1. Ověřte vlastnictví spuštěním následujícího příkazu PowerShellu:

    ```powershell
     Get-ClusterSharedVolume
    ```

1. Pokud je vlastnictví sdíleného svazku clusteru nesprávné (například jste na Uzel1, ale Uzel2 vlastní sdílený svazek clusteru), přesuňte sdílený svazek clusteru do správného uzlu spuštěním následujícího příkazu PowerShellu:

    ```powershell
     Get-ClusterSharedVolume <INSERT_CSV_NAME> | Move-ClusterSharedVolume <INSERT _NODE_NAME>
    ```
### <a name="file-copy-vs-diskspd"></a>Kopírování souborů vs. DISKSPD
Někteří lidé se domnívají, že můžou "testovat výkon úložiště" zkopírováním a vložením souboru gigantických a měřením, jak dlouho proces trvá. Hlavní důvod na základě tohoto přístupu je nejpravděpodobnější, protože je to jednoduché a rychlé. Nápad není v tom smyslu, že testuje konkrétní úlohu, ale tuto metodu je obtížné kategorizovat jako "testování výkonu úložiště".

Pokud je skutečným cílem testování výkonu kopírování souborů, může to být naprosto platný důvod k použití této metody. Pokud ale vaším cílem je změřit výkon úložiště, doporučujeme tuto metodu nepoužívat. Proces kopírování souborů si můžete představit jako při použití jiné sady "parametrů" (například fronty, paralelního zpracování atd.), která je specifická pro souborové služby.

Následující stručný přehled vysvětluje, proč použití kopírování souborů k měření výkonu úložiště nemusí poskytovat výsledky, které hledáte:
- **Kopie souborů nemusí být optimalizované,** K dispozici jsou dvě úrovně paralelismu, jedna interní a druhá externí. Interně, pokud je kopie souboru zaCopyFileExa na vzdáleném cíli, použije modul nějaké paralelismus. Externě existují různé způsoby, jak vyvolali modul CopyFileEx. Například kopie z Průzkumníka souborů jsou tvořeny jedním vláknem, ale Robocopy jsou vícevláknové. Z těchto důvodů je důležité pochopit, zda důsledky testu jsou to, co hledáte.
- **Každá kopie má dvě strany.** Když jednoduše zkopírujete a vložíte soubor, můžete použít dva disky: zdrojový disk a cílový disk. Pokud je jeden pomalejší než druhý, budete v podstatě měřit výkon pomalejšího disku. Existují i jiné případy, kdy komunikace mezi zdrojem, cílem a kopírovacím strojem může ovlivnit výkon jedinečným způsobem.
    
    Další informace najdete v tématu [použití kopírování souborů k měření výkonu úložiště](/archive/blogs/josebda/using-file-copy-to-measure-storage-performance-why-its-not-a-good-idea-and-what-you-should-do-instead?epi=je6NUbpObpQ-OaAFQvelcuupBvT5Qlis7Q&irclickid=_rcvu3tufjwkftzjukk0sohzizm2xiezdpnxvqy9i00&irgwc=1&OCID=AID2000142_aff_7593_1243925&ranEAID=je6NUbpObpQ&ranMID=24542&ranSiteID=je6NUbpObpQ-OaAFQvelcuupBvT5Qlis7Q&tduid=(ir__rcvu3tufjwkftzjukk0sohzizm2xiezdpnxvqy9i00)(7593)(1243925)(je6NUbpObpQ-OaAFQvelcuupBvT5Qlis7Q)()).

## <a name="experiments-and-common-workloads"></a>Experimenty a běžné úlohy
Tato část obsahuje několik dalších příkladů, experimentů a typů úloh.

### <a name="confirming-the-coordinator-node"></a>Potvrzuje se uzel koordinátoru.
Jak už bylo zmíněno dříve, pokud virtuální počítač, který právě testujete, nevlastní sdílený svazek clusteru, zobrazí se na rozdíl od testování výkonu (IOPS, propustnost a latence) na rozdíl od jejich testování, když uzel vlastní sdílený svazek clusteru. Důvodem je to, že při každém vystavování vstupně-výstupních operací provede systém přesměrování sítě na uzel koordinátora k provedení této operace.

U tří uzlů, které se třemi uzly zrcadlí, operace zápisu vždy vytvoří síťové směrování, protože potřebuje ukládat data na všech jednotkách na těchto třech uzlech. Proto operace zápisu provedou bez ohledu na směrování sítě. Pokud ale použijete jinou strukturu odolnosti, může to být změnit.

Tady je příklad:
- **Spuštěno na místním uzlu:** .\DiskSpd-2.0.21a\amd64\diskspd.exe-T4-o32-b4k-r4k-W0-SH-D-L C:\ClusterStorage\test01\targetfile\IO.dat
- **Spuštěno na nemístním uzlu:** .\DiskSpd-2.0.21a\amd64\diskspd.exe-T4-o32-b4k-r4k-W0-SH-D-L C:\ClusterStorage\test01\targetfile\IO.dat

V tomto příkladu se můžete jasně podívat na výsledky následujícího obrázku, které latence snížila, počet vstupně-výstupních operací se zvýšil a propustnost se zvýšila, když je uzel koordinátora vlastníkem sdíleného svazku clusteru.

:::image type="content" source="media/diskspd/coordinator-node-data.png" alt-text="Příklad ukazuje výstup dat uzlu koordinátoru." lightbox="media/diskspd/coordinator-node-data.png":::

### <a name="online-transaction-processing-oltp-workload"></a>Úlohy OLTP (online Transaction Processing)
Dotazy na úlohy pro zpracování transakcí (OLTP) (aktualizace, vkládání, odstraňování), které se zaměřují na transakce orientované na transakce. V porovnání s OLAP (Online Analytical Processing) je OLTP závislý na latenci úložiště. Vzhledem k tomu, že každá operace vydává malý počet vstupně-výstupních operací, záleží na tom, kolik operací za sekundu můžete tolerovat.

Můžete navrhnout test úloh OLTP a zaměřit se na náhodný a malý výkon vstupu a výstupu. V případě těchto testů se zaměřte na to, jak daleko můžete doručovat propustnost a přitom zachovat přijatelné latence.

Základní volba návrhu pro tento test zatížení by měla být minimálně:
- velikost bloku 8 KB => se podobá velikosti stránky, kterou SQL Server používá pro své datové soubory.
- 70% čtení, 30% Write => se podobá typickému chování OLTP

### <a name="online-analytical-processing-olap-workload"></a>Úlohy OLAP (Online Analytical Processing)
Úlohy OLAP se zaměřují na načítání a analýzu dat a umožňují uživatelům provádět složité dotazy k extrakci multidimenzionálních dat. V rozporu s OLTP nejsou tyto úlohy citlivé na latenci úložiště. Zvýrazňují celou řadu operací, aniž by caring velkou šířku pásma. Výsledkem je, že úlohy OLAP mají často za následek delší dobu zpracování.

Můžete navrhnout test úloh OLAP, který se soustředí na sekvenční, velký výkon I/O. Pro tyto testy se zaměřte na objem zpracovaných dat za sekundu a nikoli na počet IOPS. Požadavky na latenci jsou také méně důležité, ale je to subjektivní.

Základní volba návrhu pro tento test zatížení by měla být minimálně:
- velikost bloku 512 KB => se podobá velikosti vstupu a výstupu, když SQL Server načítá dávku 64 datových stránek pro kontrolu tabulky pomocí techniky čtení předem.
- 1 vlákno na soubor => v současné době je nutné omezit vaše testování na jedno vlákno na soubor, protože při testování více sekvenčních vláken mohou nastat problémy v DISKSPD.
    Použijete-li více než jedno vlákno, řekněme dva a parametr **-s** , vlákna začnou být nedeterministické k vystavení vstupně-výstupních operací nad sebou v rámci stejného umístění. Je to proto, že každý z nich sleduje vlastní sekvenční posun.

    K vyřešení tohoto problému existují dvě řešení:
    - První řešení zahrnuje použití parametru **-si** . S tímto parametrem obě vlákna sdílejí jeden vzájemně uzamčený posun, aby vlákna v družstvě vydávala jeden sekvenční vzor přístupu k cílovému souboru. To umožňuje, aby jeden bod v souboru nepracoval více než jednou. Vzhledem k tomu, že stále ještě vzájemně dělají spory k vystavení vstupně-výstupních operací do fronty, mohou být operace doručeny mimo pořadí.

        Toto řešení funguje dobře, pokud se jedno vlákno omezí na CPU. Může být vhodné zapojit druhé vlákno na druhý procesor, aby se vstupně-výstupní operace úložiště s využitím procesoru lépe nasycené systémem.

    - Druhé řešení zahrnuje použití-T\<offset>\. To vám umožní určit velikost posunutí (mezi vstupně-výstupními operacemi) mezi vstupně-výstupními operacemi provedenými na stejném cílovém souboru pomocí různých vláken. Například vlákna obvykle začínají na posunu 0, ale tato specifikace umožňuje, aby se obě vlákna rozkryla tak, aby se vzájemně nepřekrývaly. V jakémkoli vláknovém prostředí budou vlákna pravděpodobně v různých částech pracovního cíle a jedná se o způsob, jak tuto situaci simulovat.

## <a name="next-steps"></a>Další kroky
Další informace a podrobné příklady optimalizace nastavení odolnosti najdete v tématu také:
- [OLTP a OLAP](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/dn894707(v=ws.11))
- [Volba odolnosti](https://techcommunity.microsoft.com/t5/storage-at-microsoft/volume-resiliency-and-efficiency-in-storage-spaces-direct/ba-p/425831)