---
title: Převedení Azure Stack serveru HCI do režimu offline kvůli údržbě
description: Toto téma poskytuje pokyny k tomu, jak správně pozastavit, vyprázdnit a obnovit servery, na kterých běží operační systém Azure Stack HCI pomocí centra pro správu Windows a PowerShellu.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 10/23/2020
ms.openlocfilehash: 3b2c462ad8e6db96be5968074ce5478f1ca9c870
ms.sourcegitcommit: 6a51687a98c417a004cd4295ad06ae813e1978cc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92641021"
---
# <a name="taking-an-azure-stack-hci-server-offline-for-maintenance"></a>Převedení Azure Stack serveru HCI do režimu offline kvůli údržbě

> Platí pro: Azure Stack HCI, verze 20H2; Windows Server 2019

Když je Azure Stack HCI, převedení serveru do offline režimu vyžaduje pořizování částí úložiště, které jsou sdíleny napříč všemi servery v clusteru. To vyžaduje pozastavení serveru, který chcete převést do režimu offline, přesunutí rolí a virtuálních počítačů do jiných serverů v clusteru a ověření, zda jsou všechna data k dispozici na ostatních serverech v clusteru. Tento proces zajistí, aby data zůstala bezpečná a přístupná po celou dobu údržby.

K převedení serveru do offline režimu můžete použít buď centrum pro správu systému Windows, nebo PowerShell. V tomto tématu jsou popsány obě metody.

   > [!IMPORTANT]
   > V tomto tématu se předpokládá, že k provedení údržby budete potřebovat vypnout fyzický server nebo ho z nějakého důvodu restartovat. Informace o instalaci aktualizací do clusteru Azure Stack HCI naleznete v tématu [Update Azure Stack clustery](update-cluster.md)HCI, který vysvětluje, jak pomocí aktualizace Cluster-Aware (CAU) automaticky provádět všechny kroky v tomto tématu, a to v případě potřeby i při aktualizaci serverů a jejich restartování.

## <a name="take-a-server-offline-using-windows-admin-center"></a>Převedení serveru do režimu offline pomocí centra pro správu systému Windows

Nejjednodušší způsob, jak připravit server v clusteru Azure Stack HCI v režimu offline, je použití centra pro správu systému Windows.

### <a name="verify-its-safe-to-take-the-server-offline"></a>Ověřte, že je možné převést Server do offline režimu.

1. Pomocí centra pro správu systému Windows se připojte k serveru, který chcete převést do režimu offline. V nabídce **nástroje** vyberte **úložiště > disky** a ověřte, že se ve sloupci **stav** u všech virtuálních disků zobrazuje **online** .

2. Pak vyberte **úložiště > svazky** a ověřte, že sloupec stav pro každý svazek zobrazuje **stav** **v pořádku** a že se ve sloupci **stav** pro každý svazek zobrazuje **OK** .

### <a name="pause-and-drain-the-server"></a>Pozastavení a vyprázdnění serveru

Před vypnutím nebo restartováním serveru byste měli pozastavit Server a vyprázdnit (přesunout) jakékoli clusterové role, jako jsou třeba virtuální počítače se systémem, aby se zajistilo, že vypnutí serveru nemá vliv na stav aplikace. Servery s clustery vždy pozastavte a vyprázdnte, než je budete mít offline kvůli údržbě.

1. Pomocí centra pro správu Windows se připojte ke clusteru a potom v nabídce **nástroje** ve Správci clusteru vyberte **výpočetní > uzly** .

2. Klikněte na název serveru, který chcete pozastavit a vyprázdnit, a vyberte **pozastavit** . Měla by se zobrazit následující výzva:

   *Pokud tento uzel pozastavíte, všechny clusterové role se přesunou do jiných uzlů a do tohoto uzlu nemůžete přidat žádné role, dokud nebude obnovený. Opravdu chcete pozastavit uzel clusteru?*

3. Vyberte **Ano** , pokud chcete server pozastavit a zahájit proces vyprázdnění. Stav uzlu clusteru se bude zobrazovat jako **vyprazdňování** a role jako Hyper-V a virtuální počítače se hned začnou migrovat na ostatní servery v clusteru. Může to trvat několik minut.

   > [!NOTE]
   > Když Server pozastavíte a vyřadíte správně, Azure Stack HCI provede automatickou kontrolu bezpečnosti, aby bylo zajištěno, že bude bezpečné pokračovat. Pokud se vyskytnou chybné svazky, zastaví se a upozorní vás, že není bezpečné pokračovat.

### <a name="shut-down-the-server"></a>Vypnout server

Po vyprázdnění serveru se stav zobrazuje jako **pozastavený** v centru pro správu systému Windows. Nyní můžete server bezpečně vypnout kvůli údržbě nebo ho restartovat.

### <a name="resume-the-server"></a>Obnovit server

Až budete připraveni na server, aby znovu spustil hostování clusterových rolí a virtuálních počítačů, jednoduše ho zapněte, počkejte, až se spustí, a pak obnovte server pomocí následujících kroků.

1. Ve Správci clusteru vyberte v nabídce **nástroje** na levé straně **výpočetní > uzly** .

2. Vyberte název serveru, který chcete obnovit, a potom klikněte na tlačítko **obnovit** . Měla by se zobrazit následující výzva:

   *Opravdu chcete obnovit uzel clusteru?*

3. Ve většině případů byste měli zaškrtnout políčko, které říká *přenos clusterovaných rolí zpátky do tohoto uzlu* . Vyberte **Ano** , pokud chcete server obnovit.

Pokud jste zaškrtli políčko v kroku 3 výše, clusterované role a virtuální počítače začnou hned migrovat za provozu zpátky na server. Může to trvat několik minut.

### <a name="wait-for-storage-to-resync"></a>Počkejte, až se úložiště znovu synchronizuje.

Po obnovení serveru se musí znovu synchronizovat všechny nové zápisy, k nimž došlo v době, kdy byla nedostupná. K tomu dochází automaticky pomocí inteligentního sledování změn. Není nutné, aby byla *všechna* data prohledávána nebo synchronizována; pouze změny. Tento proces se omezuje, aby se zmírnil dopad na produkční úlohy. V závislosti na tom, jak dlouho byl server pozastaven a kolik nových dat bylo zapsáno, může trvat několik minut, než se dokončí.

   > [!IMPORTANT]
   > Než budete přebírat jakékoli jiné servery v clusteru offline, musíte počkat na dokončení opětovné synchronizace.

Pokud chcete zjistit, jestli se opětovná synchronizace dokončila, připojte se k serveru pomocí centra pro správu Windows a v nabídce **nástroje** na levé straně vyberte **úložiště > svazky** a pak vyberte **svazky** v horní části stránky. Pokud se **ve** sloupci Stav u každého svazku zobrazuje stav **v pořádku** a sloupec **stav** pro každý svazek zobrazuje **OK** , pak se synchronizace znovu dokončila a teď je bezpečně přebírat ostatní servery v clusteru offline.

## <a name="take-a-server-offline-using-powershell"></a>Použití prostředí PowerShell k převedení serveru do offline režimu

Pomocí následujících postupů můžete správně pozastavit, vyprázdnit a obnovit server v Azure Stack clusteru HCI pomocí prostředí PowerShell.

### <a name="verify-its-safe-to-take-the-server-offline"></a>Ověřte, že je možné převést Server do offline režimu.

Pokud chcete ověřit, jestli jsou všechny vaše svazky v pořádku, spusťte následující rutinu jako správce:

```PowerShell
Get-VirtualDisk
```

Tady je příklad toho, co může vypadat jako výstup:

```
FriendlyName              ResiliencySettingName FaultDomainRedundancy OperationalStatus HealthStatus    Size FootprintOnPool StorageEfficiency
------------              --------------------- --------------------- ----------------- ------------    ---- --------------- -----------------
Mirror II                 Mirror                1                     OK                Healthy         4 TB         8.01 TB            49.99%
Mirror-accelerated parity                                             OK                Healthy      1002 GB         1.96 TB            49.98%
Mirror                    Mirror                1                     OK                Healthy         1 TB            2 TB            49.98%
ClusterPerformanceHistory Mirror                1                     OK                Healthy        24 GB           49 GB            48.98%
```

Ověřte, zda je vlastnost **funkčnosti** pro každý svazek **v pořádku** a **provozním** zobrazí OK.

### <a name="pause-and-drain-the-server"></a>Pozastavení a vyprázdnění serveru

Spusťte následující rutinu jako správce pro pozastavení a vyprázdnění serveru:

```PowerShell
Suspend-ClusterNode -Drain
```

### <a name="shut-down-the-server"></a>Vypnout server

Až se server dokončí, bude se v PowerShellu zobrazovat jako **pozastavený** .

Server teď můžete bezpečně vypnout nebo restartovat pomocí `Stop-Computer` `Restart-Computer` rutin PowerShellu nebo.

   > [!NOTE]
   > Při spuštění `Get-VirtualDisk` příkazu na serverech, které vypíná nebo spouští nebo zastavuje Clusterovou službu, může být provozní stav serveru hlášen jako neúplný nebo snížený a sloupec stavu může obsahovat upozornění. To je normální a nemělo by to mít obavy. Všechny vaše svazky zůstávají online a přístupné.

### <a name="resume-the-server"></a>Obnovit server

Spusťte následující rutinu jako správce a obnovte server do clusteru. Chcete-li vrátit clusterové role a virtuální počítače, které byly dříve spuštěny na serveru, použijte příznak **-navrácení služeb po obnovení** :

```PowerShell
Resume-ClusterNode –Failback Immediate
```

Po obnovení serveru se tento server **zobrazí v** PowerShellu.

### <a name="wait-for-storage-to-resync"></a>Počkejte, až se úložiště znovu synchronizuje.

Po obnovení serveru je nutné počkat na opětovnou synchronizaci, aby bylo možné provést další servery v clusteru offline.

Spusťte následující rutinu jako správce a sledujte průběh:

```PowerShell
Get-StorageJob
```

Pokud se opětovná synchronizace už dokončila, nebudete mít žádný výstup.

Tady je příklad výstupu ukazujícího, že stále běží úlohy opětovné synchronizace (opravy):

```
Name   IsBackgroundTask ElapsedTime JobState  PercentComplete BytesProcessed BytesTotal
----   ---------------- ----------- --------  --------------- -------------- ----------
Repair True             00:06:23    Running   65              11477975040    17448304640
Repair True             00:06:40    Running   66              15987900416    23890755584
Repair True             00:06:52    Running   68              20104802841    22104819713
```

Ve sloupci **bytesTotal** se zobrazuje, kolik úložného prostoru se musí znovu synchronizovat. Ve sloupci **PercentComplete** se zobrazuje průběh.

   > [!WARNING]
   > Není bezpečné přebírat jiný server offline, dokud nebudou dokončeny tyto úlohy opravy.

Během této doby se v části **funkčnosti** budou vaše svazky dál zobrazovat jako **Upozornění** , což je normální.

Pokud například použijete `Get-VirtualDisk` rutinu při opětovné synchronizaci úložiště, může se zobrazit následující výstup:

```
FriendlyName ResiliencySettingName OperationalStatus HealthStatus IsManualAttach Size
------------ --------------------- ----------------- ------------ -------------- ----
MyVolume1    Mirror                InService         Warning      True           1 TB
MyVolume2    Mirror                InService         Warning      True           1 TB
MyVolume3    Mirror                InService         Warning      True           1 TB
```

Až se úlohy dokončí, ověřte, že svazky znovu zobrazují **v pořádku** pomocí `Get-VirtualDisk` rutiny. Tady je příklad výstupu:

```
FriendlyName ResiliencySettingName OperationalStatus HealthStatus IsManualAttach Size
------------ --------------------- ----------------- ------------ -------------- ----
MyVolume1    Mirror                OK                Healthy      True           1 TB
MyVolume2    Mirror                OK                Healthy      True           1 TB
MyVolume3    Mirror                OK                Healthy      True           1 TB
```

Nyní je bezpečné pozastavit a restartovat další servery v clusteru.

## <a name="next-steps"></a>Další kroky

Související informace najdete v tématu také:

- [Přehled Prostory úložiště s přímým přístupem](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [Aktualizace Azure Stackch clusterů HCI](update-cluster.md)