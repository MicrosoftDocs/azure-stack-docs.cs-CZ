---
title: Plánování kapacity pro Azure Stack | Dokumentace Microsoftu
description: Další informace o plánování kapacity pro nasazení Azure Stack.
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
ms.date: 05/30/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 05/30/2019
ms.openlocfilehash: a67a5010da2a67fb002a351b04a12fe7671fb73b
ms.sourcegitcommit: 4e0b450c91c6515794b663a39f9a4b8b49999918
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/30/2019
ms.locfileid: "66411695"
---
# <a name="azure-stack-capacity-planning"></a>Plánování kapacity v Azure stacku
Při vyhodnocování řešení s Azure Stack, existují možnosti konfigurace hardwaru, které mají přímý vliv na celkovou kapacitu cloudu Azure Stack. Jedná se o classic volby procesoru, paměti hustota, konfiguraci úložiště a celkové řešení škálování nebo počet serverů. Na rozdíl od tradiční virtualizaci řešení jednoduché aritmetické operace těchto komponent k určení využitelné kapacity se nevztahuje. Prvním důvodem je, že Azure Stack je navržený pro hostování součásti infrastruktury nebo správu v rámci vlastním řešením. Druhý důvodem je, že některé z kapacity řešení nebylo vyhrazeno podporu odolnost proti chybám, aktualizace softwaru řešení tak, aby se minimalizovalo přerušení úlohy klientů. 

> [!IMPORTANT]
> Tato kapacita informace o plánování a [Azure Stack Capacity Planner](https://aka.ms/azstackcapacityplanner) jsou výchozím bodem pro rozhodnutí o plánování a konfigurace Azure Stack. Ale nemají sloužit jako náhradu pro zkoumání a analýzy. Společnost Microsoft neposkytuje žádná vyjádření či záruky, vyjádřené nebo předpokládané, s ohledem na uvedené informace.
 

Řešení s Azure Stack je vytvořený jako cluster konvergovaný na hyper-výpočetního výkonu a úložiště. Konvergence umožňuje sdílení kapacitu hardwaru v clusteru, uvedené jako *jednotka škálování*. Jednotky škálování ve službě Azure Stack, poskytuje dostupnost a škálovatelnost prostředků. Jednotka škálování se skládá ze sady Azure Stack servery, označuje jako *hostitele*. Software infrastruktury hostována v rámci sady virtuálních počítačů a sdílí stejné fyzické servery jako virtuální počítače klientů. Všechny virtuální počítače Azure Stack spravuje pak jednotka škálování technologie clusteringu Windows Server a jednotlivých instancí technologie Hyper-V. Jednotka škálování zjednodušuje pořízení a správu služby Azure Stack. Jednotka škálování umožňuje také pro přesun a škálovatelnost všech služeb (tenantů a infrastruktury) ve službě Azure Stack. 

## <a name="azure-stack-compute"></a>Výpočetní prostředky Azure Stack

[Velikosti virtuálních počítačů](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-sizes) podporované ve službě Azure Stack, jsou podmnožinou, které jsou podporovány v Azure. Azure má omezení prostředků podél mnoho vektorů, aby overconsumption prostředků (server místní a úrovni služeb). Bez uložení některá omezení na prostředky spotřebované klienty, sníží tenanta prostředí při jiných tenantů overconsume prostředky. Pro sítě odchozího přenosu dat z virtuálního počítače existují omezení šířky pásma na místě ve službě Azure Stack, odpovídající omezení Azure. Pro prostředky úložiště limity vstupně-výstupních operací úložiště je implementovaná v Azure stacku, aby se zabránilo základní overconsumption prostředků tenantů pro přístup k úložišti.

>[!IMPORTANT]
>[Azure Stack Capacity Planner](https://aka.ms/azstackcapacityplanner) vezměte v úvahu nebo zaručí výkon vstupně-výstupních operací.

### <a name="vm-placement"></a>Umístění virtuálního počítače

Ve službě Azure Stack umístění virtuálního počítače klienta se provádí automaticky modulem umístění napříč dostupných hostitelů. Pouze dvě okolnosti při umísťování virtuálních počítačů se, zda je dostatek paměti na hostiteli pro tento typ virtuálního počítače a virtuální počítače jsou součástí [dostupnosti](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) nebo jsou [škálovací sady virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).  

Abyste dosáhli vysoké dostupnosti systému produkčního prostředí více virtuálních počítačů ve službě Azure Stack, jsou virtuální počítače umístěné ve skupině dostupnosti, který se šíří mezi více domén selhání. Doména selhání ve skupině dostupnosti je definován jako jeden uzel v jednotce škálování. Azure Stack podporuje s dostupnosti s délkou maximálně tři domény selhání pro zajištění konzistence s Azure. Virtuální počítače umístěné ve skupině dostupnosti budou fyzicky izolované od sebe navzájem tím, že rozprostírá co nejrovnoměrněji rozložené přes víc domén selhání, to znamená hostitelů Azure Stack. Pokud dojde k selhání hardwaru, virtuálních počítačů z neúspěšných doména bude být restartování v jiných doménách selhání, ale pokud je to možné udržovat v samostatných doménách selhání z jiných virtuálních počítačů ve stejné sadě dostupnosti. Když hostitel přejde do režimu online, virtuálních počítačů bude možné znovu vyrovnána udržet vysokou dostupnost.  

Škálovací sady virtuálních počítačů pomocí sad dostupnosti na zadní ukončit a ujistěte se, že instance každého virtuálního počítače škálovací sady je umístěn v různých chybových domény. To znamená, že používají samostatné infrastruktury uzly služby Azure Stack. Například ve 4 uzly systémech pro Azure Stack mohou existovat situace, ve kterém se nezdaří počet 3 instancí škálovací sady virtuálních počítačů při vytváření z důvodu nedostatku kapacity 4 uzly umístit 3 instancí škálovací sady virtuálních počítačů na 3 samostatných uzlech služby Azure Stack. Kromě toho můžete uzlů Azure Stack vyplněné nahoru na různých úrovních před pokusu o umístění. 

Azure Stack není over-pass-the potvrdit paměť. Útoky over-pass-the potvrzení počet fyzických jader je však povoleno. Protože umístění algoritmy nevypadají v existujících virtuálních a fyzických jader over-pass-the zřizování poměr jako faktor, každý hostitel může mít různý poměr. Jako Microsoft neposkytujeme pokyny na fyzický virtuální jádrům kvůli kolísání úloh a požadavky na úroveň služby. 

### <a name="azure-stack-memory"></a>Azure Stack paměti 

Azure Stack je navržena pro zachovat virtuální počítače spuštěné, které po úspěšném zřízení. Například pokud je hostitel v režimu offline z důvodu selhání hardwaru, Azure Stack se pokusí restartování tohoto virtuálního počítače na jiného hostitele. Druhý příklad je opravy a aktualizace softwaru Azure Stack. Pokud je potřeba restartování fyzického hostitele, je proveden pokus o přesunutí virtuálních počítačů, spouštění na tomto hostiteli na jiného hostitele k dispozici v řešení.   

Tato Správa virtuálních počítačů nebo přesun jde dosáhnout jenom pokud existuje vyhrazené paměti kapacitu umožňující dojde k restartování nebo migrace. Část paměti celkový počet hostitelů je rezervována a není k dispozici pro umístění virtuálního počítače tenanta. 

Můžete zkontrolovat výsečový graf v portálu pro správu, který zobrazuje volné a využité paměti v Azure stacku. Následující diagram znázorňuje kapacita fyzické paměti na jednotce škálování Azure Stack ve službě Azure Stack:

![Kapacita fyzické paměti](media/azure-stack-capacity-planning/physical-memory-capacity.png)

Využitá paměť se skládá z několika komponent. Následující komponenty využití paměti v části Použití výsečového grafu.  

- Používání hostitelský operační systém nebo rezervy – to je paměti používané podle operačního systému (OS) na hostitele, tabulky stránky virtuální paměti, procesy, které jsou spuštěny na hostitelském operačním systému a prostorů s přímým přístupem mezipaměti. 
- Služby infrastruktury – jedná se o infrastrukturu virtuálních počítačů, které tvoří Azure Stack. Od verze 1902 verzi služby Azure Stack, to znamená 31 virtuálních počítačů, které zabírají 242 GB + (4 GB × počet uzlů). Tato struktura interní služba umožní pro budoucí zavlečení nových služeb infrastruktury, jako jsou vyvíjeny.
- Odolnost proti chybám rezervy – Azure Stack si vyhrazuje část paměti umožňující tenanta dostupnosti při selhání jednoho hostitele, a také opravy a aktualizace umožňující úspěšné migrace za provozu virtuálních počítačů. 
- Virtuální počítače tenanta – jedná se o klientské virtuální počítače vytvořené uživateli Azure stacku. Kromě spouštění virtuálních počítačů, paměť je využívána všechny virtuální počítače, které jste dostali v prostředcích infrastruktury. To znamená, že virtuální počítače v **vytváření** nebo **neúspěšné** stavu nebo virtuální počítače vypnout v hostovaném počítači, bude využití paměti. Virtuální počítače, které bylo uvolněno pomocí zastavení navrácena možnost však nebude využívat paměti ze služby Azure Stack. 

Nejlepší způsob, jak porozumět využití paměti na portálu je použít [Azure Stack Capacity Planner](https://aka.ms/azstackcapacityplanner) zobrazení dopadů různé úlohy. Následující výpočtu je stejný jako ten používá plánovač.

Tento výpočet je výsledkem celkovou a dostupnou paměť, která lze použít pro umístění virtuálního počítače tenanta. Tato kapacita paměti je podkladové jednotka škálování služby Azure Stack. 


  Paměť k dispozici pro umístění virtuálního počítače = celková paměť hostitele – odolnost proti chybám rezervy - využité spuštěním klientské virtuální počítače – Azure Stack infrastruktury režii <sup>1</sup>

  Odolnost proti chybám rezervy = H + R * ((N-1) * H) + V * (N-2)

> Kde:
> - H = velikost paměti na jeden server
> - N = velikost z jednotky škálování (počet serverů)
> - R = operační systém rezervy pro operační systém režie, což je v tomto vzorci.15<sup>2</sup>
> - V = největší virtuální počítač v jednotce škálování

  <sup>1</sup> zatížení infrastruktury azure Stack = 242 GB + (4 GB × počet uzlů). Přibližně 31 virtuálních počítačů se používají k hostování infrastruktura Azure stacku a celkem, využívat přibližně 242 GB + (4 GB × počet uzlů) paměti a 146 virtuálních jader. Důvody pro tento počet virtuálních počítačů je oddělení potřebné služby podle zabezpečení, škálovatelnost, údržby a používání dílčích oprav požadavky splňují. Tato struktura interní služba umožní pro budoucí zavlečení nových služeb infrastruktury, jako jsou vyvíjeny. 

  <sup>2</sup> rezerva operačního systému pro režii = 15 % (. 15) paměti uzlu. Hodnotu rezervy operačního systému je odhad a budou lišit v závislosti na kapacita fyzické paměti serveru a režijní náklady na obecné operačního systému.


Hodnota V největší virtuální počítač v jednotce škálování dynamicky podle největší tenanta velikost paměti virtuálního počítače. Největší hodnota virtuálního počítače může být například, 7 GB nebo 112 GB nebo jakékoli jiné podporovanou velikost virtuálního počítače paměti v řešení služby Azure Stack. Změna největší virtuálního počítače v prostředcích infrastruktury Azure stacku způsobí zvýšení odolnosti proti chybám rezervy kromě nárůst objemu paměti Virtuálního počítače. 

> [!NOTE]
> Plánování požadavků na kapacitu pro sítě jsou minimální, jak je možné konfigurovat pouze velikost veřejných virtuálních IP adres. Informace o tom, jak přidat další veřejné IP adresy do služby Azure Stack najdete v tématu [přidání veřejné IP adresy](azure-stack-add-ips.md).

## <a name="azure-stack-storage"></a>Úložiště Azure Stack 
Azure Stack úložiště plánování kapacity informace jako pomoc při plánování řešení se úložiště udržet pod naleznete v následujících částech.

### <a name="uses-and-organization-of-storage-capacity"></a>Použití a organizace kapacita úložiště
Hyperkonvergovaná konfigurace služby Azure Stack umožňuje sdílení fyzických úložných zařízení. Tři hlavní divizí úložiště k dispozici jsou v rozmezí od infrastruktury, dočasné úložiště tenantské virtuální počítače a úložiště, zálohování, objekty BLOB, tabulky a fronty služby konzistentní úložiště Azure (ACS).

### <a name="spaces-direct-cache-and-capacity-tiers"></a>Prostorů s přímým přístupem, mezipaměť a kapacitní vrstvy
Je kapacita úložiště používá pro operační systém, místní protokolování, výpisů paměti a jiné dočasné infrastruktury úložiště potřebám. Tato kapacita místní úložiště je nezávislá na infrastruktuře (zařízení a kapacita) úložných zařízení, převedené pod správu konfigurace prostorů úložiště s přímým. Zbývající část úložných zařízení nachází v jeden fond kapacity úložiště bez ohledu na počet serverů v jednotce škálování. Tato zařízení jsou dvou typů: Mezipaměť a kapacity.  Zařízení mezipaměti jsou jenom - mezipaměť. Prostory s přímým přístupem budou používat tato zařízení se zpětným zápisem a ukládání do mezipaměti pro čtení. Kapacity těchto zařízení, i když je použít, nejsou potvrzeny formátovaný, "visible" kapacita formátovaný virtuálních disků. Úložných zařízení se používají pro tento účel a zadejte "domovské umístění" data spravovaná přes prostory úložiště.

Všechny kapacity úložiště je přidělena a spravuje infrastruktury Azure stacku. Operátor, který se potřebuje rozhodovat o konfiguraci, přidělení, nebo řešení s možností při rozhodování o rozšiřování kapacity. Tato rozhodnutí o návrhu se provedly bylo v souladu s požadavky na řešení a jsou automatizované během buď počáteční instalaci a nasazování nebo při rozšiřování kapacity. Podrobnosti o odolnosti proti chybám, rezervované kapacity pro znovu sestaví a další podrobnosti byly zohledněny jako součást návrhu. 

Operátory můžete si vybrat mezi všechny flash nebo hybridní úložiště konfigurace:

![Plánování kapacity služby Azure storage](media/azure-stack-capacity-planning/storage.png)

V konfiguraci všech flash může být konfigurace buď dvouvrstvé nebo Jednoúrovňová konfigurací.  Pokud je konfigurace Jednoúrovňová, budou všechna kapacitou zařízení stejného typu (např. NVMe nebo SATA SSD nebo SAS SSD) a mezipaměti zařízení nepoužívají. Ve všech dvouvrstvé je NVMe flash konfigurace, Typická konfigurace jako mezipaměť zařízení a potom buď SATA nebo SAS SSD jako úložných zařízení.

V hybridním nasazení dvouvrstvé konfigurace mezipaměti je volby NVMe, SATA nebo SAS SSD a kapacita HDD. 

Stručný přehled prostorů úložiště s přímým a konfiguraci úložiště služby Azure Stack je následujícím způsobem:
- Jeden fondu prostorů úložiště na jednotce škálování (všechna zařízení úložiště jsou nakonfigurované v rámci jeden fond)
- Virtuální disky se vytvoří jako tři kopie zrcadlení pro nejvyšší výkon a odolnost proti chybám
- Každý virtuální disk je formátován jako systém souborů ReFS
- Virtuální diskové kapacity se počítá a přidělených způsobem, ponechat jeden kapacity zařízení množství datové kapacity volné ve fondu. Jedná se o ekvivalent jednu jednotku kapacity na server.
- Každý systém souborů ReFS budou mít povoleno neaktivních dat šifrování nástrojem BitLocker. 

Virtuální – disky automaticky vytvořen a jejich kapacity jsou následující:

|Název|Výpočet kapacity|Popis|
|-----|-----|-----|
|Místní/spouštěcí zařízení|Minimální 340 GB<sup>1</sup>|Úložiště jednotlivých serverů pro bitové kopie operačního systému a "local" virtuální počítače infrastruktury|
|Infrastruktura|3,5 TB|Veškeré využití infrastruktury Azure stacku|
|VmTemp|Viz níže<sup>2</sup>|Dočasný disk připojený mají tenantské virtuální počítače a tato data uložená v těchto virtuálních disků|
|ACS|Viz níže <sup>3</sup>|Azure konzistentní kapacitou pro obsluhu objekty BLOB, tabulky a fronty|

<sup>1</sup> minimální kapacitu úložiště vyžaduje partnera řešení Azure Stack.

<sup>2</sup> velikost virtuálního disku použité pro dočasné disky virtuálního počítače se počítá jako poměr fyzické paměti serveru. Jak je uvedeno v následující tabulce pro velikosti virtuálních počítačů Azure IaaS, dočasný disk je poměr fyzické paměti přidělené virtuálnímu počítači. Přidělení Hotovo "dočasného úložiště na"disku ve službě Azure Stack se provede tak, aby sběr většinu případů použití, ale nemusí být schopen dál uspokojit se úložiště udržet pod všechny dočasného disku. Poměr zvolili je kompromis mezi při spotřebě nejsou většinou kapacity úložiště řešení pro dočasné diskové kapacity pouze zpřístupnění dočasné úložiště. Jeden server v jednotce škálování se vytvoří jeden disk dočasného úložiště. Kapacita dočasného úložiště nebude nárůst 10 % celkové dostupné kapacity úložiště ve fondu úložiště jednotce škálování. Výpočet je něco jako v následujícím příkladu:

```
  DesiredTempStoragePerServer = PhysicalMemory * 0.65 * 8
  TempStoragePerSolution = DesiredTempStoragePerServer * NumberOfServers
  PercentOfTotalCapacity = TempStoragePerSolution / TotalAvailableCapacity
  If (PercentOfTotalCapacity <= 0.1)
      TempVirtualDiskSize = DesiredTempStoragePerServer
  Else
      TempVirtualDiskSize = (TotalAvailableCapacity * 0.1) / NumberOfServers
```

<sup>3</sup> virtuální – disky vytvořené pro použití službou ACS jsou jednoduché dělení zbývající kapacity. Jak je uvedeno, všechny virtuální – disky jsou třícestný zrcadlový svazek a za jednu jednotku kapacity kapacity pro každý server je volné. Různé virtuální – disky uvedené výše se nejprve přiděluje a zbývající kapacity se pak použije pro ACS virtuální – disky.

## <a name="azure-stack-capacity-planner"></a>Capacity Planner služby Azure Stack
Azure Stack Capacity Planner je, že tabulky, který ukazuje, jak různé přidělování výpočetních prostředků by vešla výběr nabídky hardwaru. 

### <a name="worksheet-descriptions"></a>Popisy list
Následující tabulka obsahuje popis každého listu v Capacity Planner Azure Stack, který si můžete stáhnout z [ https://aka.ms/azstackcapacityplanner ](https://aka.ms/azstackcapacityplanner). 

|Název karty|Popis|
|-----|-----|
|Verze právní omezení|Účelem kalkulačky, číslo verze a datum vydání.|
|Pokyny|Podrobné pokyny pro model plánování kapacity pro kolekci virtuálních počítačů.|
|DefinedSolutionSKUs|Tabulka s definicemi až o pěti hardwaru. Položky jsou příklady. Změňte podrobnosti tak, aby odpovídaly konfigurace systému v úvahu.|
|DefineByVMFootprint|Najdete vhodný hardware skladové položky porovnáním konfigurace pomocí různých velikostí a množství virtuálních počítačů.|
|DefineByWorkloadFootprint|Najdete vhodný hardware SKU tak, že vytvoříte kolekci úloh služby Azure Stack.|
|  |  |

### <a name="definedsolutionskus-instructions"></a>DefinedSolutionSKUs pokyny
Tento sešit obsahuje příklady definice až o pěti hardwaru. Změnit podrobnosti tak, aby odpovídaly konfigurace systému v úvahu.

#### <a name="hardware-selections-provided-by-authorized-hardware-partners"></a>Výběr hardwaru poskytuje oprávnění hardwarových partnerů
Azure Stack se dodává jako integrovaný systém se softwarem nainstalovaným partneři řešení. Partneři řešení poskytují jejich vlastní autoritativní verze nástroje pro plánování kapacity služby Azure Stack. Pomocí těchto nástrojů pro poslední diskuse o kapacitě řešení.

#### <a name="multiple-ways-to-model-computing-resources"></a>Několik způsobů, jak výpočetních prostředků modelu
Prostředek modelování v rámci plánovače Azure Stack, závisí na různých velikostí virtuálních počítačů Azure Stack. Virtuální počítače rozsah od nejnižší základní 0 až po největší Standard_Fsv2. Výpočetní přidělení prostředků lze modelovat dvěma různými způsoby:

- Vyberte nabídku konkrétní hardware a kombinace různým prostředkům, které bude vyhovovat. 

- Vytvořit konkrétní kombinaci přidělení virtuálního počítače a nechat kalkulačky prostředků Azure zobrazit, které se tyto skladové položky k dispozici hardwaru schopný zajistit podporu této konfigurace virtuálního počítače.

Tento nástroj nabízí dvě metody přidělování prostředků virtuálních počítačů: buď jako jednu jedinou kolekci přidělení prostředků virtuálního počítače nebo kolekce až šest různé úlohy konfigurace. Každá úloha konfigurace může obsahovat různé přidělení dostupné prostředky virtuálních počítačů. V následujících částech mít podrobné pokyny k vytvoření a použití všech těchto modelů přidělení. Pouze hodnoty obsažené v jiných pozadí Stínované buňky nebo v rámci SKU rozevírací seznamech tohoto listu by měl být upraven. Změny provedené v rámci Stínované buňky způsobit nefunkčnost prostředků výpočty.


### <a name="definebyvmfootprint-instructions"></a>DefineByVMFootprint pokyny
Chcete-li vytvořit model s použitím jedné kolekce různé velikosti a množství virtuálních počítačů, vyberte na kartě "DefineByVMFootprint" a postupujte podle těchto kroků:

1. V pravém horním rohu tohoto listu pomocí ovládací prvky zadané rozevírací seznamu vyberte počáteční počet serverů (mezi 4 a 16), které chcete nainstalovat v každém hardwaru systému (SKU). Tento počet serverů může změnit kdykoli během procesu modelování, chcete-li zjistit, jak to ovlivní celkové dostupné prostředky pro váš model přidělení prostředků.
2. Pokud chcete k modelování různých přidělení prostředků virtuálních počítačů na jeden konkrétní hardwarové konfigurace, najdete v pravém horním rohu stránky modré rozevírací seznam přímo pod popiskem "Aktuální skladová položka". Stáhněte dolů toto pole se seznamem a vyberte požadovaný hardware SKU.
3. Nyní jste připraveni začít přidávat různé velikosti virtuálních počítačů do modelu. Zahrnout konkrétní typ virtuálního počítače, zadejte hodnotu množství do modré pole osnovy nalevo od položky tohoto virtuálního počítače.

   > [!NOTE]
   > Celková velikost úložiště virtuálního počítače odkazuje na celkovou kapacitu datového disku virtuálního počítače (počet podporovaných disky * maximální kapacitu jednoho disku (1 TB)). Založené na ukazatelích konfigurace tak, aby se můžete rozhodnout požadovanou úroveň prostředku úložiště pro každý virtuální počítač Azure Stack jsme jste vyplnili tabulku dostupné úložiště konfigurace. Je důležité si uvědomit, že můžete přidávat nebo dostupné úložiště konfigurace tabulku podle potřeby změnit.<br><br>Začíná na každý virtuální počítač původně přiřazené místní dočasné úložiště. Tak, aby odrážely dynamické zajišťování dočasného úložiště můžete změnit temp místní číslo k ničemu v rozevírací nabídce včetně dočasného úložiště o maximální povolená velikost.

4. Jak budete přidávat virtuální počítače, zobrazí se grafy, které se zobrazí dostupné zdroje SKU změna. To umožňuje vidět jejich vliv přidávání různé velikosti a množství virtuálních počítačů během procesu modelování. Dalším způsobem, jak zobrazit změny je sledovat spotřebovaná a stále k dispozici čísla, které jsou uvedeny přímo pod seznamem dostupných virtuálních počítačů. Tato čísla odrážejí hodnoty podle aktuálně vybraného hardwaru SKU.
5. Jakmile vytvoříte sadu virtuálních počítačů můžete najít navrhované hardwaru SKU kliknutím na tlačítko "Navrhované SKU" v pravém horním rohu stránky přímo pod popiskem "Aktuální skladová položka". Pomocí tohoto tlačítka můžete potom změnit konfiguraci virtuálního počítače a podívejte se, jaké hardware podporuje každou konfiguraci.


### <a name="definebyworkloadfootprint-instructions"></a>DefineByWorkloadFootprint pokyny
Vytvořit model pomocí sady Azure Stack úlohy, vyberte na kartě "DefineByWorkloadFootprint" a postupujte podle tohoto pořadí kroků. Úlohy v Azure Stack, vytvářely pomocí dostupných prostředků virtuálního počítače.   

> [!TIP]
> Chcete-li změnit velikost zadaná úložiště pro virtuální počítač Azure Stack, přečtěte si poznámku z kroku 3 v předchozí části.

1. V pravém horním rohu této stránky pomocí ovládací prvky zadané rozevírací seznamu vyberte počáteční počet serverů (mezi 4 a 16), které chcete nainstalovat v každém hardwaru systému (SKU).
2. Pokud chcete k modelování různých přidělení prostředků virtuálních počítačů na jeden konkrétní hardwarové konfigurace, najdete v pravém horním rohu stránky modré rozevírací seznam přímo pod popiskem "Aktuální skladová položka". Stáhněte dolů toto pole se seznamem a vyberte požadovaný hardware SKU.
3. Vyberte vhodnou velikost pro všechny vaše požadované zásobníku virtuálních počítačů Azure na stránce DefineByVMFootprint, jak je popsáno výše v kroku 3 DefineByVMFootprint pokyny. Velikost úložiště na virtuální počítač je definována v listu DefineByVMFootprint.
4. Spouští se v levém horním rohu stránky DefineByWorkloadFootprint, vytvořte konfigurace pro až šest různých typů zatížení tak, že zadáte množství jednotlivých virtuálních počítačů typu obsažené v rámci úlohy. To se provádí tak, že číselné hodnoty do sloupce přímo pod názvem této úlohy. Názvy úloh může být upravena podle typu úlohy, které budou podporované v této konkrétní konfiguraci.
5. Můžete zahrnout konkrétní množství jednotlivých typů úloh tak, že zadáte hodnotu v dolní části sloupce přímo pod popiskem "Quantity".
6. Po vytvoření typů úloh a množství kliknutím na "Navrhované SKU" tlačítko v pravém horním rohu stránky přímo pod popiskem "Aktuální SKU" způsobí nejmenší SKU s dostatkem prostředků pro podporu tohoto celkové Konfigurace úloh, který se má zobrazit.
7. Další modelování může udělat tak, že upravíte počet serverů vybraných pro hardware SKU, nebo změna přidělení virtuálního počítače nebo množství v rámci konfigurace vaší úlohy. Přidružené grafy se zobrazují okamžitou zpětnou vazbu znázorňující, jak provedené změny ovlivní celkové spotřeby prostředků.
8. Jakmile budete spokojeni se změnami, kliknutím na **navrhované SKU** znovu zobrazí SKU navržené pro novou konfiguraci. Můžete také kliknout na rozevírací nabídky vyberte požadovanou SKU.


## <a name="next-steps"></a>Další postup
Další informace o [aspekty integrace datových center pro službu Azure Stack](azure-stack-datacenter-integration.md)
