---
title: Správa infrastruktury úložiště pro centrum Azure Stack
titleSuffix: Azure Stack
description: Naučte se spravovat infrastrukturu úložiště pro centrum Azure Stack. Viz jak monitorovat svazky a jednotky. Podívejte se na tipy pro řešení potíží při přidávání jednotek do fondů.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 10/19/2020
ms.author: inhenkel
ms.lastreviewed: 5/4/2020
ms.reviewer: jiaha
ms.openlocfilehash: 7141d52e100ce465a20637da2a40cbb75b417939
ms.sourcegitcommit: e4e2cc6a68f02c3e856f58ca5ee51b3313c7ff8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92179454"
---
# <a name="manage-storage-infrastructure-for-azure-stack-hub"></a>Správa infrastruktury úložiště pro centrum Azure Stack
Tento článek popisuje stav a provozní stav prostředků infrastruktury úložiště služby Azure Stack hub. Mezi tyto prostředky patří jednotky a svazky úložiště. Informace v tomto tématu vám pomůžou při odstraňování různých problémů, například když se do fondu nedá přidat jednotka.

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
| ObjStore_1 | Neznámý | Odpojit |
| ObjStore_2 | Upozornění | {Degradované, neúplné} |

V následujících částech jsou uvedeny stav a provozní stavy:

### <a name="volume-health-state-healthy"></a>Stav svazku: v dobrém stavu

| Provozní stav | Popis |
|---|---|
| OK | Svazek je v pořádku. |
| Neoptimální | Data nejsou zapsána rovnoměrně mezi jednotky.<br> <br>**Akce:** Pokud chcete optimalizovat využití jednotky ve fondu úložiště, obraťte se na podporu. Než to uděláte, spusťte proces shromažďování souborů protokolu pomocí pokynů v části https://aka.ms/azurestacklogfiles . Po obnovení neúspěšného připojení bude pravděpodobně nutné provést obnovení ze zálohy. |

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
| Bez redundance | Svazek ztratil data, protože selhalo příliš mnoho jednotek.<br> <br>**Akce:** Obraťte se na podporu. Než to uděláte, spusťte proces shromažďování souborů protokolu pomocí pokynů v části https://aka.ms/azurestacklogfiles . |

### <a name="volume-health-state-unknown"></a>Stav svazku: neznámý

Pokud se virtuální disk stane odpojený, může být svazek také v neznámém stavu.

| Provozní stav | Popis |
|---|---|
| Odpojit | Došlo k chybě úložného zařízení, což může způsobit nedostupnost svazku. Některá data mohou být ztracena.<br> <br>**Kroky** <br>1. Ověřte fyzické a síťové připojení všech úložných zařízení.<br>2. Pokud jsou všechna zařízení správně připojená, obraťte se na podporu. Než to uděláte, spusťte proces shromažďování souborů protokolu pomocí pokynů v části https://aka.ms/azurestacklogfiles . Po obnovení neúspěšného připojení bude pravděpodobně nutné provést obnovení ze zálohy. |

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

### <a name="drive-health-state-warning"></a>Stav jednotky: upozornění

Jednotka ve stavu varování může úspěšně číst a zapisovat data, ale má problém.

| Provozní stav | Popis |
|---|---|
| Ztracená komunikace | Připojení k jednotce bylo ztraceno.<br> <br>**Akce:** Převeďte všechny servery zpět do režimu online. Pokud to neopraví, připojte jednotku znovu. Pokud tento stav přetrvává, nahraďte jednotku, abyste zajistili plnou odolnost. |
| Prediktivní selhání | Brzy dojde k selhání jednotky.<br> <br>**Akce:** Pokud chcete zajistit plnou odolnost, nahraďte jednotku co nejdříve. |
| Vstupně-výstupní chyba | Při přístupu k jednotce došlo k dočasné chybě.<br> <br>**Akce:** Pokud tento stav přetrvává, nahraďte jednotku, abyste zajistili plnou odolnost. |
| Přechodná chyba | Došlo k dočasné chybě disku. Tato chyba obvykle znamená, že jednotka nereagovala, ale může to znamenat, že Prostory úložiště s přímým přístupem ochranný oddíl nebyl z jednotky vhodně odebrán. <br> <br>**Akce:** Pokud tento stav přetrvává, nahraďte jednotku, abyste zajistili plnou odolnost. |
| Abnormální latence | Jednotka někdy neodpovídá a zobrazuje znaménka selhání.<br> <br>**Akce:** Pokud tento stav přetrvává, nahraďte jednotku, abyste zajistili plnou odolnost. |
| Odebírá se z fondu | Azure Stack hub probíhá proces odebírání jednotky z fondu úložiště.<br> <br>**Akce:** Počkejte, než centrum Azure Stack dokončí odebrání jednotky, a potom stav ověřte.<br>Pokud stav zůstane, obraťte se na podporu. Než to uděláte, spusťte proces shromažďování souborů protokolu pomocí pokynů v části https://aka.ms/azurestacklogfiles . |
| Spouští se režim údržby. | Azure Stack hub probíhá proces vkládání jednotky do režimu údržby. Tento stav je dočasný – jednotka by brzy měla být ve stavu režim údržby.<br> <br>**Akce:** Počkejte, než centrum Azure Stack proces dokončí, a potom stav ověřte. |
| V režimu údržby | Jednotka je v režimu údržby, probíhá zastavení čtení a zápisu z jednotky. Tento stav obvykle znamená, že Azure Stack úlohy správy centra, jako je například PNU nebo jednotka FRU. Správce ale může jednotku taky umístit do režimu údržby.<br> <br>**Akce:** Počkejte, než centrum Azure Stackí dokončí úlohu správy, a poté stav ověřte.<br>Pokud stav zůstane, obraťte se na podporu. Než to uděláte, spusťte proces shromažďování souborů protokolu pomocí pokynů v části https://aka.ms/azurestacklogfiles . |
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
| Chyba ověřování | Health Service se nepovedlo ověřit, jestli je jednotka nebo firmware na jednotce schválený k použití.<br> <br>**Akce:** Obraťte se na podporu. Než to uděláte, spusťte proces shromažďování souborů protokolu pomocí pokynů v části https://aka.ms/azurestacklogfiles . |
| Offline | Jednotka je offline. <br> <br>**Akce:** Obraťte se na podporu. Než to uděláte, spusťte proces shromažďování souborů protokolu pomocí pokynů v části https://aka.ms/azurestacklogfiles . |
