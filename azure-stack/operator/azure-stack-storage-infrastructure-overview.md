---
title: Správa infrastruktury úložiště pro centrum Azure Stack
titleSuffix: Azure Stack
description: Naučte se spravovat infrastrukturu úložiště pro centrum Azure Stack.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 1/22/2020
ms.author: inhenkel
ms.lastreviewed: 03/11/2019
ms.reviewer: jiaha
ms.openlocfilehash: 4ac1d0de3775c22c0c982d79713847e7cd171f41
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "80152243"
---
# <a name="manage-storage-infrastructure-for-azure-stack-hub"></a>Správa infrastruktury úložiště pro centrum Azure Stack

Tento článek popisuje stav a provozní stav prostředků infrastruktury úložiště služby Azure Stack hub. Mezi tyto prostředky patří jednotky a svazky úložiště. Informace v tomto tématu vám pomůžou při odstraňování různých problémů, například když se do fondu nedá přidat jednotka.

## <a name="understand-drives-and-volumes"></a>Principy jednotek a svazků

### <a name="drives"></a>Drives

Služba Azure Stack hub využívá software Windows serveru a umožňuje kombinovat možnosti úložiště s kombinací Prostory úložiště s přímým přístupem (S2D) a clusteringu s podporou převzetí služeb při selhání Windows serveru. Tato kombinace poskytuje výkonné, škálovatelné a odolné služby úložiště.

Azure Stack partneři integrovaných systémů pro rozbočovače nabízejí řadu variant řešení, včetně široké škály flexibility úložiště. V současné době můžete vybrat až dva typy jednotek ze tří podporovaných typů jednotek: NVMe (Non Volatile Memory Express), SATA/SAS SSD (Solid-State Drive), HDD (pevný disk). 

Prostory úložiště s přímým přístupem poskytuje mezipaměť pro maximalizaci výkonu úložiště. V zařízení centra Azure Stack s jedním typem jednotky (tj. NVMe nebo SSD) se pro kapacitu používají všechny jednotky. Pokud existují dva typy jednotek, Prostory úložiště s přímým přístupem pro ukládání do mezipaměti automaticky používat všechny jednotky typu "nejrychlejší" &gt; pevný &gt; disk SSD. Zbývající jednotky se použijí k ukládání. Jednotky mohou být seskupeny do nasazení "all-Flash" nebo "hybrid":

![Azure Stack infrastruktura úložiště centra](media/azure-stack-storage-infrastructure-overview/image1.png)

Nasazení all-Flash se zaměřuje na maximalizaci výkonu úložiště a nezahrnují rotaci HDD.

![Azure Stack infrastruktura úložiště centra](media/azure-stack-storage-infrastructure-overview/image2.png)

Hybridní nasazení mají za cíl vyvážit výkon a kapacitu nebo maximalizovat kapacitu a zahrnout rotační Hddy.

Chování mezipaměti je určeno automaticky na základě typů jednotek, které jsou ukládány do mezipaměti pro. Při ukládání do mezipaměti pro SSD (například ukládání do mezipaměti NVMe pro SSD) se do mezipaměti ukládají pouze zápisy. Tím se snižuje kapacita jednotek kapacity, což snižuje počet kumulovaných přenosů na kapacitní jednotky a prodlouží jejich dobu života. Do té doby se čtení neukládá do mezipaměti. Nejsou ukládány do mezipaměti, protože čtení nemají významně vliv na životnost blesku a protože SSD univerzálně nabízí nízkou latenci čtení. Při ukládání do mezipaměti pro HDD (jako je například ukládání do mezipaměti SSD pro HDD) jsou čtení i zápisy ukládány do mezipaměti, aby se zajistila latence s podobným rozhraním Flash (často/~ 10x) pro obojí.

![Azure Stack infrastruktura úložiště centra](media/azure-stack-storage-infrastructure-overview/image3.png)

K dispozici je konfigurace úložiště, kterou můžete vyhledat v Azure Stack partnerovi OEMhttps://azure.microsoft.com/overview/azure-stack/partners/) (pro podrobnou specifikaci).

> [!Note]  
> Zařízení centra Azure Stack lze doručovat v hybridním nasazení s jednotkami HDD i SSD (nebo NVMe). Jednotky rychlejšího typu se ale používají jako jednotky mezipaměti a všechny zbývající jednotky se jako fond používají jako jednotky kapacity. Data tenanta (objekty blob, tabulky, fronty a disky) se umístí na jednotky kapacity. Zřizování prémiových disků nebo výběr typu účtu Premium Storage nezaručí, že se objekty přidělují na jednotky SSD nebo NVMe.

### <a name="volumes"></a>Svazky

*Služba úložiště* rozdělí dostupné úložiště na samostatné svazky, které jsou přiděleny k uchování dat systému a klienta. Svazky spojují jednotky ve fondu úložiště, aby poskytovaly odolnost proti chybám, škálovatelnost a výkonnostní výhody Prostory úložiště s přímým přístupem.

![Azure Stack infrastruktura úložiště centra](media/azure-stack-storage-infrastructure-overview/image4.png)

Existují tři typy svazků, které jsou vytvořené ve fondu úložiště Azure Stack hub:

- Infrastruktura: hostitelské soubory používané Azure Stackmi virtuálními počítači infrastruktury centra a základními službami.

- Dočasné virtuální počítače: hostovat dočasné disky připojené k virtuálním počítačům tenanta a tato data jsou uložená na těchto discích.

- Úložiště objektů: objekty blob, tabulky, fronty a disky virtuálních počítačů obsluhy hostitelských dat klienta.

V nasazení s více uzly vidíte tři svazky infrastruktury, zatímco počet dočasnou svazky virtuálního počítače a svazky úložiště objektů se rovná počtu uzlů v nasazení centra Azure Stack:

- Při nasazení se čtyřmi uzly jsou k dispozici čtyři stejné dočasné svazky virtuálních počítačů a čtyři stejné svazky úložiště objektů.

- Pokud do clusteru přidáte nový uzel, bude pro oba typy vytvořen nový svazek.

- Počet svazků zůstává stejný i v případě, že uzel nefunguje nebo je odebraný.

- Pokud používáte Azure Stack Development Kit, existuje jeden svazek s více sdílenými složkami.

Svazky v Prostory úložiště s přímým přístupem poskytují odolnost proti problémům s hardwarem, jako je například selhání disku nebo serveru. Zároveň umožňují nepřetržitou dostupnost v rámci údržby serveru, jako jsou aktualizace softwaru. Nasazení centra Azure Stack používá k zajištění odolnosti dat trojrozměrné zrcadlení. Tři kopie dat tenanta se zapisují na různé servery, kde se nacházejí v mezipaměti:

![Azure Stack infrastruktura úložiště centra](media/azure-stack-storage-infrastructure-overview/image5.png)

Zrcadlení zajišťuje odolnost proti chybám tím, že udržuje více kopií všech dat. Způsob, jakým jsou data rozložená a umístěná, jsou netriviální, ale mají pravdu, že všechna data uložená pomocí zrcadlení se napíší několikrát. Každá kopie je zapsána na jiný fyzický hardware (různé jednotky na různých serverech), u kterých se předpokládá nezávisle na selhání. Třícestný zrcadlení může bezpečně tolerovat alespoň dva problémy s hardwarem (na disku nebo na serveru). Pokud například restartujete jeden server, když dojde k výpadku jiné jednotky nebo serveru, všechna data zůstanou bezpečná a nepřetržitě dostupná.

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

V následujících částech jsou uvedeny stav a provozní stavy:

### <a name="volume-health-state-healthy"></a>Stav svazku: v dobrém stavu

| Provozní stav | Popis |
|---|---|
| OK | Svazek je v pořádku. |
| Neoptimální | Data nejsou zapsána rovnoměrně mezi jednotky.<br> <br>**Akce:** Pokud chcete optimalizovat využití jednotky ve fondu úložiště, obraťte se na podporu. Než to uděláte, spusťte proces shromažďování souborů protokolu pomocí pokynů v https://aka.ms/azurestacklogfilesčásti. Po obnovení neúspěšného připojení bude pravděpodobně nutné provést obnovení ze zálohy. |

### <a name="volume-health-state-warning"></a>Stav svazku: upozornění

Pokud je svazek ve stavu varování, znamená to, že jedna nebo více kopií vašich dat není k dispozici, ale Azure Stack hub si pořád může přečíst aspoň jednu kopii vašich dat.

| Provozní stav | Popis |
|---|---|
| V provozu | Centrum Azure Stack opravuje svazek, například po přidání nebo odebrání jednotky. Po dokončení opravy by se svazek měl vrátit do stavu OK.<br> <br>**Akce:** Počkejte, než centrum Azure Stack dokončí opravu svazku, a potom stav ověřte. |
| Dokončena | Odolnost svazku se snížila, protože jedna nebo více jednotek selhala nebo chybí. Chybějící jednotky ale obsahují aktuální kopie vašich dat.<br> <br>**Akce:** Znovu připojte všechny chybějící jednotky, nahraďte všechny jednotky, které selhaly, a přepněte do režimu online všechny servery, které jsou offline. |
| Snížený výkon | Odolnost svazku se snížila z důvodu jedné nebo více chybných nebo chybějících jednotek a také zastaralých kopií dat na jednotkách.<br> <br>**Akce:** Znovu připojte všechny chybějící jednotky, nahraďte všechny jednotky, které selhaly, a přepněte do režimu online všechny servery, které jsou offline. |

### <a name="volume-health-state-unhealthy"></a>Stav svazku: není v pořádku

Pokud je svazek ve stavu není v pořádku, některá nebo všechna data ve svazku nejsou v tuto chvíli k dispozici.

| Provozní stav | Popis |
|---|---|
| Bez redundance | Svazek ztratil data, protože selhalo příliš mnoho jednotek.<br> <br>**Akce:** Obraťte se na podporu. Než to uděláte, spusťte proces shromažďování souborů protokolu pomocí pokynů v https://aka.ms/azurestacklogfilesčásti. |

### <a name="volume-health-state-unknown"></a>Stav svazku: neznámý

Pokud se virtuální disk stane odpojený, může být svazek také v neznámém stavu.

| Provozní stav | Popis |
|---|---|
| Odpojit | Došlo k chybě úložného zařízení, což může způsobit nedostupnost svazku. Některá data mohou být ztracena.<br> <br>**Kroky** <br>1. Ověřte fyzické a síťové připojení všech úložných zařízení.<br>2. Pokud jsou všechna zařízení správně připojená, obraťte se na podporu. Než to uděláte, spusťte proces shromažďování souborů protokolu pomocí pokynů v https://aka.ms/azurestacklogfilesčásti. Po obnovení neúspěšného připojení bude pravděpodobně nutné provést obnovení ze zálohy. |

## <a name="drive-states"></a>Stavy jednotek

Ke sledování stavu jednotek použijte následující příkazy PowerShellu:

```powershell
$scaleunit_name = (Get-AzsScaleUnit)[0].name

$subsystem_name = (Get-AzsStorageSubSystem -ScaleUnit $scaleunit_name)[0].name

Get-AzsDrive -ScaleUnit $scaleunit_name -StorageSubSystem $subsystem_name | Select-Object StorageNode, PhysicalLocation, HealthStatus, OperationalStatus, Description, Action, Usage, CanPool, CannotPoolReason, SerialNumber, Model, MediaType, CapacityGB
```

Následující části popisují stav, ve kterém může být jednotka:

### <a name="drive-health-state-healthy"></a>Stav jednotky: v dobrém stavu

| Provozní stav | Popis |
|---|---|
| OK | Svazek je v pořádku. |
| V provozu | Jednotka provádí některé interní operace údržbu. Po dokončení akce by se měla jednotka vrátit do stavu OK. |

### <a name="drive-health-state-healthy"></a>Stav jednotky: v dobrém stavu

Jednotka ve stavu varování může úspěšně číst a zapisovat data, ale má problém.

| Provozní stav | Popis |
|---|---|
| Ztracená komunikace | Připojení k jednotce bylo ztraceno.<br> <br>**Akce:** Převeďte všechny servery zpět do režimu online. Pokud to neopraví, připojte jednotku znovu. Pokud tento stav přetrvává, nahraďte jednotku, abyste zajistili plnou odolnost. |
| Prediktivní selhání | Brzy dojde k selhání jednotky.<br> <br>**Akce:** Pokud chcete zajistit plnou odolnost, nahraďte jednotku co nejdříve. |
| Vstupně-výstupní chyba | Při přístupu k jednotce došlo k dočasné chybě.<br> <br>**Akce:** Pokud tento stav přetrvává, nahraďte jednotku, abyste zajistili plnou odolnost. |
| Přechodná chyba | Došlo k dočasné chybě disku. Tato chyba obvykle znamená, že jednotka nereagovala, ale může to znamenat, že Prostory úložiště s přímým přístupem ochranný oddíl nebyl z jednotky vhodně odebrán. <br> <br>**Akce:** Pokud tento stav přetrvává, nahraďte jednotku, abyste zajistili plnou odolnost. |
| Abnormální latence | Jednotka někdy neodpovídá a zobrazuje znaménka selhání.<br> <br>**Akce:** Pokud tento stav přetrvává, nahraďte jednotku, abyste zajistili plnou odolnost. |
| Odebírá se z fondu | Azure Stack hub probíhá proces odebírání jednotky z fondu úložiště.<br> <br>**Akce:** Počkejte, než centrum Azure Stack dokončí odebrání jednotky, a potom stav ověřte.<br>Pokud stav zůstane, obraťte se na podporu. Než to uděláte, spusťte proces shromažďování souborů protokolu pomocí pokynů v https://aka.ms/azurestacklogfilesčásti. |
| Spouští se režim údržby. | Azure Stack hub probíhá proces vkládání jednotky do režimu údržby. Tento stav je dočasný – jednotka by brzy měla být ve stavu režim údržby.<br> <br>**Akce:** Počkejte, než centrum Azure Stack proces dokončí, a potom stav ověřte. |
| V režimu údržby | Jednotka je v režimu údržby, probíhá zastavení čtení a zápisu z jednotky. Tento stav obvykle znamená, že Azure Stack úlohy správy centra, jako je například PNU nebo jednotka FRU. Správce ale může jednotku taky umístit do režimu údržby.<br> <br>**Akce:** Počkejte, než centrum Azure Stackí dokončí úlohu správy, a poté stav ověřte.<br>Pokud stav zůstane, obraťte se na podporu. Než to uděláte, spusťte proces shromažďování souborů protokolu pomocí pokynů v https://aka.ms/azurestacklogfilesčásti. |
| Zastavení režimu údržby | Azure Stack centrum se v procesu převádění jednotky zpátky do režimu online. Tento stav je dočasný – jednotka by brzy měla být v jiném stavu, ideálně v pořádku.<br> <br>**Akce:** Počkejte, než centrum Azure Stack proces dokončí, a potom stav ověřte. |

### <a name="drive-health-state-unhealthy"></a>Stav jednotky: není v pořádku

Jednotka v nesprávném stavu se momentálně nedá zapisovat do nebo k ní nelze přistupovat.

| Provozní stav | Popis |
|---|---|
| Rozdělení | Jednotka se od fondu oddělí.<br> <br>**Akce:** Nahraďte jednotku novým diskem. Pokud musíte použít tento disk, vyjměte disk ze systému, zajistěte, aby na disku neexistovala žádná užitečná data, disk smažete a pak disk znovu vložte. |
| Nelze použít | Fyzický disk je v karanténě, protože není podporován vaším dodavatelem řešení. Podporují se jenom disky, které jsou schválené pro řešení a mají správný firmware disku.<br> <br>**Akce:** Nahraďte jednotku diskem, který má schválený výrobce a číslo modelu pro dané řešení. |
| Zastaralá metadata | Náhradní disk byl dříve použit a může obsahovat data z neznámého systému úložiště. Disk je v karanténě. <br> <br>**Akce:** Nahraďte jednotku novým diskem. Pokud musíte použít tento disk, vyjměte disk ze systému, zajistěte, aby na disku neexistovala žádná užitečná data, disk smažete a pak disk znovu vložte. |
| Nerozpoznaná metadata | Na disku byla nalezena nerozpoznaná metadata, což obvykle znamená, že má jednotka metadata z jiného fondu.<br> <br>**Akce:** Nahraďte jednotku novým diskem. Pokud musíte použít tento disk, vyjměte disk ze systému, zajistěte, aby na disku neexistovala žádná užitečná data, disk smažete a pak disk znovu vložte. |
| Neúspěšné médium | Jednotka se nezdařila a prostory úložiště už nepoužívají.<br> <br>**Akce:** Pokud chcete zajistit plnou odolnost, nahraďte jednotku co nejdříve. |
| Selhání hardwaru zařízení | V této jednotce došlo k chybě hardwaru. <br> <br>**Akce:** Pokud chcete zajistit plnou odolnost, nahraďte jednotku co nejdříve. |
| aktualizace firmwaru; | Centrum Azure Stack aktualizuje firmware na jednotce. Tento stav je dočasný a obvykle trvá méně než minutu a během této doby všechny ostatní jednotky ve fondu zpracovávají všechny operace čtení a zápisu.<br> <br>**Akce:** Počkejte, než centrum Azure Stack dokončí aktualizaci, a potom stav ověřte. |
| Spouštění | Jednotka je připravena k operaci. Tento stav by měl být dočasný – po dokončení by jednotka měla přejít na jiný provozní stav.<br> <br>**Akce:** Počkejte, než centrum Azure Stack operaci dokončí, a poté stav ověřte. |

## <a name="reasons-a-drive-cant-be-pooled"></a>Důvody, proč se jednotka nedá zařadit do fondu

Některé jednotky teď nejsou připravené na Azure Stack ve fondu úložiště centra. Informace o tom, proč jednotka nemá nárok na sdružování, najdete v části `CannotPoolReason` vlastnosti jednotky. Následující tabulka obsahuje další informace o každém z důvodů.

| Důvod | Popis |
|---|---|
| Nekompatibilní hardware | Jednotka není v seznamu schválených modelů úložiště zadaných pomocí Health Service.<br> <br>**Akce:** Nahraďte jednotku novým diskem. |
| Neodpovídající firmware | Firmware na fyzické jednotce není v seznamu schválených revizí firmwaru pomocí Health Service.<br> <br>**Akce:** Nahraďte jednotku novým diskem. |
| Používáno clusterem | Jednotku aktuálně používá cluster s podporou převzetí služeb při selhání.<br> <br>**Akce:** Nahraďte jednotku novým diskem. |
| Vyměnitelná média | Jednotka je klasifikována jako vyměnitelná jednotka. <br> <br>**Akce:** Nahraďte jednotku novým diskem. |
| Není v pořádku | Jednotka není v dobrém stavu a může být potřeba ji nahradit.<br> <br>**Akce:** Nahraďte jednotku novým diskem. |
| Nedostatečná kapacita | K dispozici jsou oddíly, které zabírají volné místo na jednotce.<br> <br>**Akce:** Nahraďte jednotku novým diskem. Pokud musíte použít tento disk, vyjměte disk ze systému, zajistěte, aby na disku neexistovala žádná užitečná data, disk smažete a pak disk znovu vložte. |
| Probíhá ověřování. | Health Service kontroluje, jestli je jednotka nebo firmware na jednotce schválený k použití.<br> <br>**Akce:** Počkejte, než centrum Azure Stack proces dokončí, a poté stav ověřte. |
| Ověření se nezdařilo | Health Service se nepovedlo ověřit, jestli je jednotka nebo firmware na jednotce schválený k použití.<br> <br>**Akce:** Obraťte se na podporu. Než to uděláte, spusťte proces shromažďování souborů protokolu pomocí pokynů v https://aka.ms/azurestacklogfilesčásti. |
| Offline | Jednotka je offline. <br> <br>**Akce:** Obraťte se na podporu. Než to uděláte, spusťte proces shromažďování souborů protokolu pomocí pokynů v https://aka.ms/azurestacklogfilesčásti. |

## <a name="next-step"></a>Další krok

[Správa kapacity úložiště](azure-stack-manage-storage-shares.md) 
