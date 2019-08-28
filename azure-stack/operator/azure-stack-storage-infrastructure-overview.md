---
title: Správa infrastruktury úložiště pro Azure Stack | Microsoft Docs
description: Správa infrastruktury úložiště pro Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ''
ms.topic: article
ms.date: 08/27/2019
ms.author: mabrigg
ms.lastreviewed: 03/11/2019
ms.reviewer: jiahan
ms.openlocfilehash: c2a61e165ee64c4b0ee91fbe387973aaa5039f46
ms.sourcegitcommit: 9cb82df1eccb0486bcabec0bd674162d4820c00c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2019
ms.locfileid: "70060216"
---
# <a name="manage-storage-infrastructure-for-azure-stack"></a>Správa infrastruktury úložiště pro Azure Stack

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Tento článek popisuje stav a provozní stav prostředků infrastruktury úložiště Azure Stack. Mezi tyto prostředky patří jednotky a svazky úložiště. Informace v tomto tématu můžou být nedůležité, pokud se snažíte řešit různé problémy, jako je například jednotka, kterou nelze do fondu přidat.

## <a name="understand-drives-and-volumes"></a>Principy jednotek a svazků

### <a name="drives"></a>Spar

Pomocí softwaru Windows serveru Azure Stack definuje možnosti úložiště s kombinací Prostory úložiště s přímým přístupem (S2D) a clusteringu s podporou převzetí služeb při selhání ve Windows serveru, která poskytuje výkonné, škálovatelné a odolné služby úložiště.

Azure Stack integrovaných systémových partnerů nabízí řadu variant řešení, včetně široké škály flexibilního úložiště. V tuto chvíli můžete vybrat kombinaci tří typů jednotek: NVMe (nestálá paměť Express), SATA/SAS SSD (Solid-State Drive), HDD (pevný disk).

Prostory úložiště s přímým přístupem poskytuje mezipaměť pro maximalizaci výkonu úložiště. V Azure Stack zařízení s jedním nebo několika typy jednotek prostory úložiště s přímým přístupem automaticky použít všechny jednotky typu "nejrychlejší" (NVMe &gt; SSD &gt; HDD) pro ukládání do mezipaměti. Zbývající jednotky se použijí pro kapacitu. Jednotky mohou být seskupeny do nasazení "all-Flash" nebo "hybrid":

![Azure Stack infrastruktura úložiště](media/azure-stack-storage-infrastructure-overview/image1.png)

Nasazení all-Flash se zaměřuje na maximalizaci výkonu úložiště a nezahrnují jednotky pevného disku (HDD).

![Azure Stack infrastruktura úložiště](media/azure-stack-storage-infrastructure-overview/image2.png)

Hybridní nasazení mají za cíl vyvážit výkon a kapacitu nebo maximalizovat kapacitu a zahrnout rotační jednotky pevného disku (HDD).

Chování mezipaměti je určeno automaticky na základě typů jednotek, které jsou ukládány do mezipaměti pro. Při ukládání do mezipaměti pro jednotky SSD (například NVMe caching pro SSD) se do mezipaměti ukládají pouze zápisy. Tím se snižuje kapacita jednotek kapacity, což snižuje počet kumulovaných přenosů na kapacitní jednotky a prodlouží jejich dobu života. Vzhledem k tomu, že čtení nijak významně neovlivňuje životnost blesku a protože jednotky SSD (Solid-State) všeobecně nabízejí nízkou latenci čtení, čtení nejsou ukládány do mezipaměti. Při ukládání do mezipaměti pro jednotky pevného disku (například ukládání do mezipaměti SSD pro HDD) jsou čtení i zápisy ukládány do mezipaměti, aby se zajistila latence podobný tomuto typu Flash (často/~ 10x lepší) pro obojí.

![Azure Stack infrastruktura úložiště](media/azure-stack-storage-infrastructure-overview/image3.png)

Pro dostupnou konfiguraci úložiště můžete zaškrtnout Azure Stack partner OEM (https://azure.microsoft.com/overview/azure-stack/partners/) pro podrobnou specifikaci).

> [!Note]  
> Zařízení Azure Stack se dá doručit v hybridním nasazení s jednotkami HDD i SSD (nebo NVMe). Jednotky rychlejšího typu se ale používají jako jednotky mezipaměti a všechny zbývající jednotky se jako fond používají jako jednotky kapacity. Data tenanta (objekty blob, tabulky, fronty a disky) se umístí na jednotky kapacity. Proto zřizování prémiových disků nebo výběr typu účtu Premium Storage neznamená, že jsou objekty zaručené k přidělování na jednotky SSD nebo NVMe a získají vyšší výkon.

### <a name="volumes"></a>Svazky

*Služba úložiště* rozdělí dostupné úložiště na samostatné svazky, které jsou přiděleny k uchování dat systému a klienta. Svazky spojují jednotky ve fondu úložiště, aby zavedly odolnost proti chybám, škálovatelnost a výkonnostní výhody Prostory úložiště s přímým přístupem.

![Azure Stack infrastruktura úložiště](media/azure-stack-storage-infrastructure-overview/image4.png)

V Azure Stack fond úložiště se vytvořily tři typy svazků:

-   Infrastruktura: hostitelské soubory používané virtuálními počítači infrastruktury Azure Stack a základními službami.

-   Dočasné virtuální počítače: hostovat dočasné disky připojené k virtuálním počítačům tenanta a tato data jsou uložená na těchto discích.

-   Úložiště objektů: objekty blob, tabulky, fronty a disky virtuálních počítačů obsluhy hostitelských dat klienta.

V nasazení s více uzly vidíte tři svazky infrastruktury, zatímco počet dočasnou svazky virtuálního počítače a svazky úložiště objektů se rovná počtu uzlů v nasazení Azure Stack:

-   Při nasazení se čtyřmi uzly jsou k dispozici čtyři stejné dočasné svazky virtuálních počítačů a čtyři stejné svazky úložiště objektů.

-   Pokud do clusteru přidáte nový uzel, bude vytvořen nový svazek pro oba typy.

-   Počet svazků zůstává stejný i v případě, že uzel nefunguje nebo je odebraný.

-   Pokud používáte sadu Azure Stack Developer Kit, existuje jeden svazek s více sdílenými složkami.

Svazky v Prostory úložiště s přímým přístupem poskytují odolnost proti problémům s hardwarem, jako jsou například selhání jednotky nebo serveru a umožňují nepřetržitou dostupnost během údržby serveru, jako jsou například aktualizace softwaru. Nasazení Azure Stack používá pro zajištění odolnosti dat trojrozměrné zrcadlení. Tři kopie dat tenanta se zapisují na různé servery, kde se nacházejí v mezipaměti:

![Azure Stack infrastruktura úložiště](media/azure-stack-storage-infrastructure-overview/image5.png)

Zrcadlení zajišťuje odolnost proti chybám tím, že udržuje více kopií všech dat. Způsob, jakým jsou data rozložená a umístěná, jsou netriviální, ale mají naprosto pravdu za to, že všechna data uložená pomocí zrcadlení se zapisují v celém rozsahu několikrát. Každá kopie je zapsána na jiný fyzický hardware (různé jednotky na různých serverech), u kterých se předpokládá nezávisle na selhání. Třícestný zrcadlení může bezpečně tolerovat alespoň dva problémy s hardwarem (na disku nebo na serveru). Pokud například restartujete jeden server, když dojde k výpadku jiné jednotky nebo serveru, všechna data zůstanou bezpečná a nepřetržitě dostupná.

## <a name="volume-states"></a>Stavy svazku

Chcete-li zjistit, jaké svazky stavu jsou v nástroji, použijte následující příkazy PowerShellu:

```powershell
$scaleunit_name = (Get-AzsScaleUnit)[0].name

$subsystem_name = (Get-AzsStorageSubSystem -ScaleUnit $scaleunit_name)[0].name

Get-AzsVolume -ScaleUnit $scaleunit_name -StorageSubSystem $subsystem_name | Select-Object VolumeLabel, HealthStatus, OperationalStatus, RepairStatus, Description, Action, TotalCapacityGB, RemainingCapacityGB
```

Tady je příklad výstupu ukazující odpojený svazek a degradované/nedokončený svazek:

| VolumeLabel | HealthStatus | Provozním |
|-------------|--------------|------------------------|
| ObjStore_1 | Neznámé | Odpojit |
| ObjStore_2 | Upozornění | {Degradované, neúplné} |

V následujících částech najdete seznam stavů a provozních stavů.

### <a name="volume-health-state-healthy"></a>Stav svazku: V pořádku

| Provozní stav | Popis |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| OK | Svazek je v pořádku. |
| Neoptimální | Data nejsou zapsána rovnoměrně mezi jednotky.<br> <br>**Kroky** Pokud chcete optimalizovat využití jednotky ve fondu úložiště, obraťte se prosím na podporu. Než to uděláte, spusťte proces shromažďování souborů protokolu pomocí pokynů v https://aka.ms/azurestacklogfiles části. Po obnovení neúspěšného připojení bude pravděpodobně nutné provést obnovení ze zálohy. |


### <a name="volume-health-state-warning"></a>Stav svazku: Upozornění

Pokud je svazek ve stavu varování, znamená to, že jedna nebo více kopií vašich dat není k dispozici, ale Azure Stack může stále číst alespoň jednu kopii vašich dat.

| Provozní stav | Popis |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| V provozu | Azure Stack opravuje svazek, například po přidání nebo odebrání jednotky. Po dokončení opravy by se svazek měl vrátit do stavu OK.<br> <br>**Kroky** Počkejte, až Azure Stack dokončí opravu svazku, a potom stav ověřte. |
| Neúplný | Odolnost svazku se snížila, protože jedna nebo více jednotek selhala nebo chybí. Chybějící jednotky ale obsahují aktuální kopie vašich dat.<br> <br>**Kroky** Znovu připojte všechny chybějící jednotky, nahraďte všechny jednotky, které selhaly, a přepněte do režimu online všechny servery, které jsou offline. |
| Sníženo | Odolnost svazku se zmenší, protože jedna nebo více jednotek selhala nebo chybí, a na těchto jednotkách jsou zastaralé kopie vašich dat.<br> <br>**Kroky** Znovu připojte všechny chybějící jednotky, nahraďte všechny jednotky, které selhaly, a přepněte do režimu online všechny servery, které jsou offline. |

 

### <a name="volume-health-state-unhealthy"></a>Stav svazku: Není v pořádku

Pokud je svazek ve stavu není v pořádku, některá nebo všechna data ve svazku nejsou v tuto chvíli k dispozici.

| Provozní stav | Popis |
|-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Bez redundance | Svazek ztratil data, protože selhalo příliš mnoho jednotek.<br> <br>**Kroky** Obraťte se prosím na podporu. Než to uděláte, spusťte proces shromažďování souborů protokolu pomocí pokynů v https://aka.ms/azurestacklogfiles části. |


### <a name="volume-health-state-unknown"></a>Stav svazku: Neznámé

Pokud se virtuální disk stane odpojený, může být svazek také v neznámém stavu.

| Provozní stav | Popis |
|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Odpojit | Došlo k chybě úložného zařízení, což může způsobit nedostupnost svazku. Některá data mohou být ztracena.<br> <br>**Kroky** <br>1. Ověřte fyzické a síťové připojení všech úložných zařízení.<br>2. Pokud jsou všechna zařízení připojená správně, obraťte se prosím na podporu. Než to uděláte, spusťte proces shromažďování souborů protokolu pomocí pokynů v https://aka.ms/azurestacklogfiles části. Po obnovení neúspěšného připojení bude pravděpodobně nutné provést obnovení ze zálohy. |

## <a name="drive-states"></a>Stavy jednotek

Ke sledování stavu jednotek použijte následující příkazy PowerShellu:

```powershell
$scaleunit_name = (Get-AzsScaleUnit)[0].name

$subsystem_name = (Get-AzsStorageSubSystem -ScaleUnit $scaleunit_name)[0].name

Get-AzsDrive -ScaleUnit $scaleunit_name -StorageSubSystem $subsystem_name | Select-Object StorageNode, PhysicalLocation, HealthStatus, OperationalStatus, Description, Action, Usage, CanPool, CannotPoolReason, SerialNumber, Model, MediaType, CapacityGB
```

Následující části popisují stav, ve kterém může být jednotka.

### <a name="drive-health-state-healthy"></a>Stav jednotky: V pořádku

| Provozní stav | Popis |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| OK | Svazek je v pořádku. |
| V provozu | Jednotka provádí některé interní operace údržbu. Po dokončení akce by se měla jednotka vrátit do stavu OK. |

### <a name="drive-health-state-healthy"></a>Stav jednotky: V pořádku

Jednotka ve stavu varování může úspěšně číst a zapisovat data, ale má problém.

| Provozní stav | Popis |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ztracená komunikace | Připojení k jednotce bylo ztraceno.<br> <br>**Kroky** Převeďte všechny servery zpět do režimu online. Pokud to neopraví, připojte jednotku znovu. Pokud to bude pokračovat, nahraďte jednotku, abyste zajistili plnou odolnost. |
| Prediktivní selhání | Brzy dojde k selhání jednotky.<br> <br>**Kroky** Pokud chcete zajistit plnou odolnost, nahraďte jednotku co nejdříve. |
| Vstupně-výstupní chyba | Při přístupu k jednotce došlo k dočasné chybě.<br> <br>**Kroky** Pokud to bude pokračovat, nahraďte jednotku, abyste zajistili plnou odolnost. |
| Přechodná chyba | Došlo k dočasné chybě disku. To obvykle znamená, že jednotka nereagovala, ale může to znamenat, že Prostory úložiště s přímým přístupem ochranný oddíl byl z disku nevhodně odebrán. <br> <br>**Kroky** Pokud to bude pokračovat, nahraďte jednotku, abyste zajistili plnou odolnost. |
| Abnormální latence | Jednotka někdy neodpovídá a zobrazuje znaménka selhání.<br> <br>**Kroky** Pokud to bude pokračovat, nahraďte jednotku, abyste zajistili plnou odolnost. |
| Odebírá se z fondu | Azure Stack je proces odebrání jednotky z fondu úložiště.<br> <br>**Kroky** Počkejte, až Azure Stack dokončí odebrání jednotky, a potom stav ověřte.<br>Pokud zůstane stav, obraťte se prosím na podporu. Než to uděláte, spusťte proces shromažďování souborů protokolu pomocí pokynů v https://aka.ms/azurestacklogfiles části. |
|  |  |
| Spouští se režim údržby. | Azure Stack je proces vkládání jednotky do režimu údržby. Toto je dočasný stav – jednotka by brzy měla být ve stavu režimu údržby.<br> <br>**Kroky** Počkejte na dokončení procesu Azure Stack a potom stav ověřte. |
| V režimu údržby | Jednotka je v režimu údržby, probíhá zastavení čtení a zápisu z jednotky. To obvykle znamená, že Azure Stack úlohy správy, jako je například PNU nebo jednotka FRU, fungují jako jednotky. Správce ale může jednotku taky umístit do režimu údržby.<br> <br>**Kroky** Počkejte na dokončení úlohy správy Azure Stack a potom stav ověřte.<br>Pokud zůstane stav, obraťte se prosím na podporu. Než to uděláte, spusťte proces shromažďování souborů protokolu pomocí pokynů v https://aka.ms/azurestacklogfiles části. |
|  |  |
| Zastavení režimu údržby | Azure Stack je postup převedení jednotky zpátky do režimu online. Toto je dočasný stav – jednotka by brzy měla být v jiném stavu – v ideálním stavu.<br> <br>**Kroky** Počkejte na dokončení procesu Azure Stack a potom stav ověřte. |

 

### <a name="drive-health-state-unhealthy"></a>Stav jednotky: Není v pořádku

Jednotka v nesprávném stavu se momentálně nedá zapisovat do nebo k ní nelze přistupovat.

| Provozní stav | Popis |
|-------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Rozdělit | Jednotka se od fondu oddělí.<br> <br>**Kroky** Nahraďte jednotku novým diskem. Pokud musíte použít tento disk, vyjměte disk ze systému, ujistěte se, že na disku nejsou žádná užitečná data, disk smazat a pak disk znovu vložte. |
| Nelze použít | Fyzický disk je v karanténě, protože není podporován vaším dodavatelem řešení. Podporují se jenom disky, které jsou schválené pro řešení a mají správný firmware disku.<br> <br>**Kroky** Nahraďte jednotku diskem, který má schválený výrobce a číslo modelu pro dané řešení. |
| Zastaralá metadata | Náhradní disk byl dříve použit a může obsahovat data z neznámého systému úložiště. Disk je v karanténě.        <br> <br>**Kroky** Nahraďte jednotku novým diskem. Pokud musíte použít tento disk, vyjměte disk ze systému, ujistěte se, že na disku nejsou žádná užitečná data, disk smazat a pak disk znovu vložte. |
| Nerozpoznaná metadata | Na disku byla nalezena nerozpoznaná metadata, což obvykle znamená, že má jednotka metadata z jiného fondu.<br> <br>**Kroky** Nahraďte jednotku novým diskem. Pokud musíte použít tento disk, vyjměte disk ze systému, ujistěte se, že na disku nejsou žádná užitečná data, disk smazat a pak disk znovu vložte. |
| Neúspěšné médium | Jednotka se nezdařila a prostory úložiště už nepoužívají.<br> <br>**Kroky** Pokud chcete zajistit plnou odolnost, nahraďte jednotku co nejdříve. |
| Selhání hardwaru zařízení | V této jednotce došlo k chybě hardwaru. <br> <br>**Kroky** Pokud chcete zajistit plnou odolnost, nahraďte jednotku co nejdříve. |
| Aktualizuje se firmware | Azure Stack aktualizuje firmware na jednotce. Jedná se o dočasný stav, který obvykle trvá méně než minutu a během kterého se všechny jednotky ve fondu zpracovávají všemi čteními a zápisy.<br> <br>**Kroky** Počkejte, až se Azure Stack dokončí aktualizace, a potom stav ověřte. |
| Spouštění | Jednotka je připravena k operaci. Mělo by se jednat o dočasný stav po dokončení, jednotka by měla přejít na jiný provozní stav.<br> <br>**Kroky** Počkejte na dokončení operace Azure Stack a potom stav ověřte. |
 

## <a name="reasons-a-drive-cant-be-pooled"></a>Důvody, proč se jednotka nedá zařadit do fondu

Některé jednotky teď nejsou připravené na Azure Stack fond úložiště. Informace o tom, proč jednotka není způsobilá pro sdružování, najdete v vlastnosti CannotPoolReason jednotky. Následující tabulka obsahuje další informace o každém z důvodů.

| Reason | Popis |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nekompatibilní hardware | Jednotka není v seznamu schválených modelů úložiště zadaných pomocí Health Service.<br> <br>**Kroky** Nahraďte jednotku novým diskem. |
| Neodpovídající firmware | Firmware na fyzické jednotce není v seznamu schválených revizí firmwaru pomocí Health Service.<br> <br>**Kroky** Nahraďte jednotku novým diskem. |
| Používáno clusterem | Jednotku aktuálně používá cluster s podporou převzetí služeb při selhání.<br> <br>**Kroky** Nahraďte jednotku novým diskem. |
| Vyměnitelná média | Jednotka je klasifikována jako vyměnitelná jednotka. <br> <br>**Kroky** Nahraďte jednotku novým diskem. |
| Nejsou v dobrém stavu | Jednotka není v dobrém stavu a může být potřeba ji nahradit.<br> <br>**Kroky** Nahraďte jednotku novým diskem. |
| Nedostatečná kapacita | K dispozici jsou oddíly, které zabírají volné místo na jednotce.<br> <br>**Kroky** Nahraďte jednotku novým diskem. Pokud musíte použít tento disk, vyjměte disk ze systému, ujistěte se, že na disku nejsou žádná užitečná data, disk smazat a pak disk znovu vložte. |
| Probíhá ověřování. | Health Service kontroluje, jestli je jednotka nebo firmware na jednotce schválený k použití.<br> <br>**Kroky** Počkejte na dokončení procesu Azure Stack a potom stav ověřte. |
| Chyba ověřování | Health Service se nepovedlo ověřit, jestli je jednotka nebo firmware na jednotce schválený k použití.<br> <br>**Kroky** Obraťte se prosím na podporu. Než to uděláte, spusťte proces shromažďování souborů protokolu pomocí pokynů v https://aka.ms/azurestacklogfiles části. |
| Offline | Jednotka je offline. <br> <br>**Kroky** Obraťte se prosím na podporu. Než to uděláte, spusťte proces shromažďování souborů protokolu pomocí pokynů v https://aka.ms/azurestacklogfiles části. |

## <a name="next-step"></a>Další krok

[Správa kapacity úložiště](azure-stack-manage-storage-shares.md) 
