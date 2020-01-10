---
title: Správa infrastruktury úložiště pro centrum Azure Stack | Microsoft Docs
description: Správa infrastruktury úložiště pro centrum Azure Stack.
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
ms.openlocfilehash: 2fc96f1c5749ddd28ece1a8eefd476556ab557a1
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75814163"
---
# <a name="manage-storage-infrastructure-for-azure-stack-hub"></a>Správa infrastruktury úložiště pro centrum Azure Stack

*Platí pro: Azure Stack integrovaných systémů centra a Azure Stack Development Kit*

Tento článek popisuje stav a provozní stav prostředků infrastruktury úložiště služby Azure Stack hub. Mezi tyto prostředky patří jednotky a svazky úložiště. Informace v tomto tématu můžou být nedůležité, pokud se snažíte řešit různé problémy, jako je například jednotka, kterou nelze do fondu přidat.

## <a name="understand-drives-and-volumes"></a>Principy jednotek a svazků

### <a name="drives"></a>Jednotky

Služba Azure Stack hub využívá software Windows serveru a nabízí podporu úložiště s kombinací Prostory úložiště s přímým přístupem (S2D) a clusteringu s podporou převzetí služeb při selhání Windows serveru, která poskytuje výkonné, škálovatelné a odolné služby úložiště.

Azure Stack partneři integrovaných systémů pro rozbočovače nabízejí nejrůznější variace řešení, včetně široké škály flexibility úložiště. V současné době můžete vybrat kombinaci tří typů jednotek: NVMe (Non Volatile Memory Express), SATA/SAS SSD (SSD-State Drive), HDD (pevný disk).

Prostory úložiště s přímým přístupem poskytuje mezipaměť pro maximalizaci výkonu úložiště. V zařízení Azure Stack hub s jedním nebo několika typy jednotek Prostory úložiště s přímým přístupem automaticky použít všechny jednotky typu "nejrychlejší" (NVMe &gt; SSD &gt; HDD) pro ukládání do mezipaměti. Zbývající jednotky se využívají k ukládání dat. Jednotky mohou být seskupeny do nasazení "all-Flash" nebo "hybrid":

![Azure Stack infrastruktura úložiště centra](media/azure-stack-storage-infrastructure-overview/image1.png)

Cílem čistě flashových nasazení je dosažení maximálního výkonu úložiště s vyloučením jednotek rotačních pevných disků (HDD).

![Azure Stack infrastruktura úložiště centra](media/azure-stack-storage-infrastructure-overview/image2.png)

Cílem hybridních nasazení je vyvážení výkonu a kapacity nebo dosažení maximální kapacity s využitím jednotek rotačních pevných disků (HDD).

Chování mezipaměti se určuje automaticky podle typů jednotek, pro které se mezipaměť používá. Pokud se mezipaměť používá pro jednotky Solid-State (kdy například jednotky NVMe slouží jako mezipaměť pro jednotky SSD), ukládají se do mezipaměti jen zápisy. Tím se snižuje kapacita jednotek kapacity, což snižuje počet kumulovaných přenosů na kapacitní jednotky a prodlouží jejich dobu života. Vzhledem k tomu, že čtení nijak významně neovlivňuje životnost blesku a protože jednotky SSD (Solid-State) všeobecně nabízejí nízkou latenci čtení, čtení nejsou ukládány do mezipaměti. Při ukládání do mezipaměti pro jednotky pevného disku (například ukládání do mezipaměti SSD pro HDD) jsou čtení i zápisy ukládány do mezipaměti, aby se zajistila latence podobný tomuto typu Flash (často/~ 10x lepší) pro obojí.

![Azure Stack infrastruktura úložiště centra](media/azure-stack-storage-infrastructure-overview/image3.png)

Pro dostupnou konfiguraci úložiště můžete zaškrtnout Azure Stack partner výrobce OEM (https://azure.microsoft.com/overview/azure-stack/partners/) pro podrobnou specifikaci).

> [!Note]  
> Zařízení centra Azure Stack lze doručovat v hybridním nasazení s jednotkami HDD i SSD (nebo NVMe). Jednotky rychlejšího typu se ale používají jako jednotky mezipaměti a všechny zbývající jednotky se jako fond používají jako jednotky kapacity. Data tenanta (objekty blob, tabulky, fronty a disky) se umístí na jednotky kapacity. Proto zřizování prémiových disků nebo výběr typu účtu Premium Storage neznamená, že jsou objekty zaručené k přidělování na jednotky SSD nebo NVMe a získají vyšší výkon.

### <a name="volumes"></a>Svazky

*Služba úložiště* rozdělí dostupné úložiště na samostatné svazky, které jsou přiděleny k uchování dat systému a klienta. Svazky kombinují jednotky ve fondu úložiště, aby do Prostorů úložiště s přímým přístupem přinesly výhody odolnosti proti chybám, škálovatelnosti a výkonu.

![Azure Stack infrastruktura úložiště centra](media/azure-stack-storage-infrastructure-overview/image4.png)

Existují tři typy svazků, které jsou vytvořené ve fondu úložiště Azure Stack hub:

-   Infrastruktura: hostitelské soubory používané Azure Stackmi virtuálními počítači infrastruktury centra a základními službami.

-   Dočasné virtuální počítače: hostovat dočasné disky připojené k virtuálním počítačům tenanta a tato data jsou uložená na těchto discích.

-   Úložiště objektů: objekty blob, tabulky, fronty a disky virtuálních počítačů obsluhy hostitelských dat klienta.

V nasazení s více uzly vidíte tři svazky infrastruktury, zatímco počet dočasnou svazky virtuálního počítače a svazky úložiště objektů se rovná počtu uzlů v nasazení centra Azure Stack:

-   Při nasazení se čtyřmi uzly jsou k dispozici čtyři stejné dočasné svazky virtuálních počítačů a čtyři stejné svazky úložiště objektů.

-   Pokud do clusteru přidáte nový uzel, bude vytvořen nový svazek pro oba typy.

-   Počet svazků zůstává stejný i v případě, že uzel nefunguje nebo je odebraný.

-   Pokud používáte sadu SDK pro Azure Stack hub, existuje jeden svazek s více sdílenými složkami.

Svazky v Prostorech úložiště s přímým přístupem poskytují odolnost zajišťující ochranu při problémech s hardwarem, jako jsou selhání jednotek nebo serverů, a umožňují nepřetržitou dostupnost v průběhu údržby serverů, jako jsou například aktualizace softwaru. Nasazení centra Azure Stack používá pro zajištění odolnosti dat trojrozměrné zrcadlení. Tři kopie dat tenanta se zapisují na různé servery, kde se nacházejí v mezipaměti:

![Azure Stack infrastruktura úložiště centra](media/azure-stack-storage-infrastructure-overview/image5.png)

Zrcadlení poskytuje odolnost proti chybám tím, že uchovává několik kopií všech dat. Způsob, jakým jsou data rozložená a umístěná, jsou netriviální, ale mají naprosto pravdu za to, že všechna data uložená pomocí zrcadlení se zapisují v celém rozsahu několikrát. Každá kopie se zapisuje na jiný fyzický hardware (jiné jednotky na různých serverech), u kterého se předpokládá, že selže nezávisle na sobě. Třícestný zrcadlení může bezpečně tolerovat alespoň dva problémy s hardwarem (na disku nebo na serveru). Pokud například restartujete jeden server a najednou selže jiná jednotka nebo server, všechna data zůstanou v bezpečí a nepřetržitě přístupná.

## <a name="volume-states"></a>Stavy svazku

Chcete-li zjistit, jaké svazky stavu jsou v nástroji, použijte následující příkazy PowerShellu:

```powershell
$scaleunit_name = (Get-AzsScaleUnit)[0].name

$subsystem_name = (Get-AzsStorageSubSystem -ScaleUnit $scaleunit_name)[0].name

Get-AzsVolume -ScaleUnit $scaleunit_name -StorageSubSystem $subsystem_name | Select-Object VolumeLabel, HealthStatus, OperationalStatus, RepairStatus, Description, Action, TotalCapacityGB, RemainingCapacityGB
```

Tady je příklad výstupu ukazující odpojený svazek a degradované/nedokončený svazek:

| VolumeLabel | HealthStatus | OperationalStatus |
|-------------|--------------|------------------------|
| ObjStore_1 | Není známo | Odpojit |
| ObjStore_2 | Upozornění | {Degradované, neúplné} |

V následujících částech najdete seznam stavů a provozních stavů.

### <a name="volume-health-state-healthy"></a>Stav svazku: v dobrém stavu

| Provozní stav | Popis |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| OK | Svazek je v pořádku. |
| Neoptimální | Data nejsou zapsána rovnoměrně mezi jednotky.<br> <br>**Akce:** Pokud chcete optimalizovat využití jednotky ve fondu úložiště, obraťte se prosím na podporu. Než to uděláte, spusťte proces shromažďování souborů protokolu pomocí pokynů z https://aka.ms/azurestacklogfiles. Po obnovení neúspěšného připojení bude pravděpodobně nutné provést obnovení ze zálohy. |


### <a name="volume-health-state-warning"></a>Stav svazku: upozornění

Pokud je svazek ve stavu varování, znamená to, že jedna nebo více kopií vašich dat není k dispozici, ale centrum Azure Stack si pořád může přečíst aspoň jednu kopii vašich dat.

| Provozní stav | Popis |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| V provozu | Centrum Azure Stack opravuje svazek, například po přidání nebo odebrání jednotky. Po dokončení opravy by se svazek měl vrátit do stavu OK.<br> <br>**Akce:** Počkejte, než Azure Stack centrum dokončí opravu svazku, a poté stav ověřte. |
| Neúplné | Odolnost svazku se snížila, protože jedna nebo více jednotek selhala nebo chybí. Chybějící jednotky ale obsahují aktuální kopie vašich dat.<br> <br>**Akce:** Znovu připojte všechny chybějící jednotky, nahraďte všechny jednotky, které selhaly, a přepněte do režimu online všechny servery, které jsou offline. |
| Snížený výkon | Odolnost svazku se zmenší, protože jedna nebo více jednotek selhala nebo chybí, a na těchto jednotkách jsou zastaralé kopie vašich dat.<br> <br>**Akce:** Znovu připojte všechny chybějící jednotky, nahraďte všechny jednotky, které selhaly, a přepněte do režimu online všechny servery, které jsou offline. |

 

### <a name="volume-health-state-unhealthy"></a>Stav svazku: není v pořádku

Pokud je svazek ve stavu není v pořádku, některá nebo všechna data ve svazku nejsou v tuto chvíli k dispozici.

| Provozní stav | Popis |
|-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Bez redundance | Svazek ztratil data, protože selhalo příliš mnoho jednotek.<br> <br>**Akce:** Obraťte se prosím na podporu. Než to uděláte, spusťte proces shromažďování souborů protokolu pomocí pokynů z https://aka.ms/azurestacklogfiles. |


### <a name="volume-health-state-unknown"></a>Stav svazku: neznámý

Pokud se virtuální disk stane odpojený, může být svazek také v neznámém stavu.

| Provozní stav | Popis |
|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Odpojit | Došlo k chybě úložného zařízení, což může způsobit nedostupnost svazku. Některá data mohou být ztracena.<br> <br>**Kroky** <br>1. Ověřte fyzické a síťové připojení všech úložných zařízení.<br>2. Pokud jsou všechna zařízení správně připojená, obraťte se prosím na podporu. Než to uděláte, spusťte proces shromažďování souborů protokolu pomocí pokynů z https://aka.ms/azurestacklogfiles. Po obnovení neúspěšného připojení bude pravděpodobně nutné provést obnovení ze zálohy. |

## <a name="drive-states"></a>Stavy jednotek

Ke sledování stavu jednotek použijte následující příkazy PowerShellu:

```powershell
$scaleunit_name = (Get-AzsScaleUnit)[0].name

$subsystem_name = (Get-AzsStorageSubSystem -ScaleUnit $scaleunit_name)[0].name

Get-AzsDrive -ScaleUnit $scaleunit_name -StorageSubSystem $subsystem_name | Select-Object StorageNode, PhysicalLocation, HealthStatus, OperationalStatus, Description, Action, Usage, CanPool, CannotPoolReason, SerialNumber, Model, MediaType, CapacityGB
```

Následující části popisují stav, ve kterém může být jednotka.

### <a name="drive-health-state-healthy"></a>Stav jednotky: v dobrém stavu

| Provozní stav | Popis |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| OK | Svazek je v pořádku. |
| V provozu | Jednotka provádí některé interní operace údržbu. Po dokončení akce by se měla jednotka vrátit do stavu OK. |

### <a name="drive-health-state-healthy"></a>Stav jednotky: v dobrém stavu

Jednotka ve stavu varování může úspěšně číst a zapisovat data, ale má problém.

| Provozní stav | Popis |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ztracená komunikace | Připojení k jednotce bylo ztraceno.<br> <br>**Akce:** Převeďte všechny servery zpět do režimu online. Pokud to neopraví, připojte jednotku znovu. Pokud to bude pokračovat, nahraďte jednotku, abyste zajistili plnou odolnost. |
| Prediktivní selhání | Brzy dojde k selhání jednotky.<br> <br>**Akce:** Pokud chcete zajistit plnou odolnost, nahraďte jednotku co nejdříve. |
| Vstupně-výstupní chyba | Při přístupu k jednotce došlo k dočasné chybě.<br> <br>**Akce:** Pokud to bude pokračovat, nahraďte jednotku, abyste zajistili plnou odolnost. |
| Přechodná chyba | Došlo k dočasné chybě disku. To obvykle znamená, že jednotka nereagovala, ale může to znamenat, že Prostory úložiště s přímým přístupem ochranný oddíl byl z disku nevhodně odebrán. <br> <br>**Akce:** Pokud to bude pokračovat, nahraďte jednotku, abyste zajistili plnou odolnost. |
| Abnormální latence | Jednotka někdy neodpovídá a zobrazuje znaménka selhání.<br> <br>**Akce:** Pokud to bude pokračovat, nahraďte jednotku, abyste zajistili plnou odolnost. |
| Odebírá se z fondu | Azure Stack hub probíhá proces odebírání jednotky z fondu úložiště.<br> <br>**Akce:** Počkejte, než centrum Azure Stack dokončí odebrání jednotky, a potom stav ověřte.<br>Pokud zůstane stav, obraťte se prosím na podporu. Než to uděláte, spusťte proces shromažďování souborů protokolu pomocí pokynů z https://aka.ms/azurestacklogfiles. |
|  |  |
| Spouští se režim údržby. | Azure Stack hub probíhá proces vkládání jednotky do režimu údržby. Toto je dočasný stav – jednotka by brzy měla být ve stavu režimu údržby.<br> <br>**Akce:** Počkejte, než centrum Azure Stack proces dokončí, a poté stav ověřte. |
| V režimu údržby | Jednotka je v režimu údržby, probíhá zastavení čtení a zápisu z jednotky. To obvykle znamená, že Azure Stack úlohy správy centra, jako je například PNU nebo jednotka FRU. Správce ale může jednotku taky umístit do režimu údržby.<br> <br>**Akce:** Počkejte, než centrum Azure Stack dokončí úlohu správy, a poté stav ověřte.<br>Pokud zůstane stav, obraťte se prosím na podporu. Než to uděláte, spusťte proces shromažďování souborů protokolu pomocí pokynů z https://aka.ms/azurestacklogfiles. |
|  |  |
| Zastavení režimu údržby | Azure Stack centrum se v procesu převádění jednotky zpátky do režimu online. Toto je dočasný stav – jednotka by brzy měla být v jiném stavu – v ideálním stavu.<br> <br>**Akce:** Počkejte, než centrum Azure Stack proces dokončí, a poté stav ověřte. |

 

### <a name="drive-health-state-unhealthy"></a>Stav jednotky: není v pořádku

Jednotka v nesprávném stavu se momentálně nedá zapisovat do nebo k ní nelze přistupovat.

| Provozní stav | Popis |
|-------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Rozdělení | Jednotka se od fondu oddělí.<br> <br>**Akce:** Nahraďte jednotku novým diskem. Pokud musíte použít tento disk, vyjměte disk ze systému, ujistěte se, že na disku nejsou žádná užitečná data, disk smazat a pak disk znovu vložte. |
| Nelze použít | Fyzický disk je v karanténě, protože není podporován vaším dodavatelem řešení. Podporují se jenom disky, které jsou schválené pro řešení a mají správný firmware disku.<br> <br>**Akce:** Nahraďte jednotku diskem, který má schválený výrobce a číslo modelu pro dané řešení. |
| Zastaralá metadata | Náhradní disk byl dříve použit a může obsahovat data z neznámého systému úložiště. Disk je v karanténě.        <br> <br>**Akce:** Nahraďte jednotku novým diskem. Pokud musíte použít tento disk, vyjměte disk ze systému, ujistěte se, že na disku nejsou žádná užitečná data, disk smazat a pak disk znovu vložte. |
| Nerozpoznaná metadata | Na disku byla nalezena nerozpoznaná metadata, což obvykle znamená, že má jednotka metadata z jiného fondu.<br> <br>**Akce:** Nahraďte jednotku novým diskem. Pokud musíte použít tento disk, vyjměte disk ze systému, ujistěte se, že na disku nejsou žádná užitečná data, disk smazat a pak disk znovu vložte. |
| Neúspěšné médium | Jednotka se nezdařila a prostory úložiště už nepoužívají.<br> <br>**Akce:** Pokud chcete zajistit plnou odolnost, nahraďte jednotku co nejdříve. |
| Selhání hardwaru zařízení | V této jednotce došlo k chybě hardwaru. <br> <br>**Akce:** Pokud chcete zajistit plnou odolnost, nahraďte jednotku co nejdříve. |
| aktualizace firmwaru; | Centrum Azure Stack aktualizuje firmware na jednotce. Jedná se o dočasný stav, který obvykle trvá méně než minutu a během kterého se všechny jednotky ve fondu zpracovávají všemi čteními a zápisy.<br> <br>**Akce:** Počkejte, než se dokončí aktualizace centra Azure Stack, a potom stav ověřte. |
| Spouštění | Jednotka je připravena k operaci. Mělo by se jednat o dočasný stav po dokončení, jednotka by měla přejít na jiný provozní stav.<br> <br>**Akce:** Počkejte, než centrum Azure Stack operaci dokončí, a poté stav ověřte. |
 

## <a name="reasons-a-drive-cant-be-pooled"></a>Důvody, proč se jednotka nedá zařadit do fondu

Některé jednotky teď nejsou připravené na Azure Stack ve fondu úložiště centra. Informace o tom, proč jednotka není způsobilá pro sdružování, najdete v vlastnosti CannotPoolReason jednotky. Následující tabulka obsahuje další informace o každém z důvodů.

| Důvod | Popis |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Nekompatibilní hardware | Jednotka není v seznamu schválených modelů úložiště zadaných pomocí Health Service.<br> <br>**Akce:** Nahraďte jednotku novým diskem. |
| Neodpovídající firmware | Firmware na fyzické jednotce není v seznamu schválených revizí firmwaru pomocí Health Service.<br> <br>**Akce:** Nahraďte jednotku novým diskem. |
| Používáno clusterem | Jednotku aktuálně používá cluster s podporou převzetí služeb při selhání.<br> <br>**Akce:** Nahraďte jednotku novým diskem. |
| Vyměnitelná média | Jednotka je klasifikována jako vyměnitelná jednotka. <br> <br>**Akce:** Nahraďte jednotku novým diskem. |
| Není v pořádku | Jednotka není v dobrém stavu a může být potřeba ji nahradit.<br> <br>**Akce:** Nahraďte jednotku novým diskem. |
| Nedostatečná kapacita | K dispozici jsou oddíly, které zabírají volné místo na jednotce.<br> <br>**Akce:** Nahraďte jednotku novým diskem. Pokud musíte použít tento disk, vyjměte disk ze systému, ujistěte se, že na disku nejsou žádná užitečná data, disk smazat a pak disk znovu vložte. |
| Probíhá ověřování. | Health Service kontroluje, jestli je jednotka nebo firmware na jednotce schválený k použití.<br> <br>**Akce:** Počkejte, než centrum Azure Stack proces dokončí, a poté stav ověřte. |
| Ověření se nezdařilo | Health Service se nepovedlo ověřit, jestli je jednotka nebo firmware na jednotce schválený k použití.<br> <br>**Akce:** Obraťte se prosím na podporu. Než to uděláte, spusťte proces shromažďování souborů protokolu pomocí pokynů z https://aka.ms/azurestacklogfiles. |
| Offline | Jednotka je offline. <br> <br>**Akce:** Obraťte se prosím na podporu. Než to uděláte, spusťte proces shromažďování souborů protokolu pomocí pokynů z https://aka.ms/azurestacklogfiles. |

## <a name="next-step"></a>Další krok

[Správa kapacity úložiště](azure-stack-manage-storage-shares.md) 
