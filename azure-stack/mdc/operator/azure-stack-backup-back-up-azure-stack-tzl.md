---
title: Zálohování Azure Stack – MDC | Microsoft Docs
description: Naučte se, jak provést zálohování na vyžádání v Azure Stack pro modulární datové centrum (MDC).
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: tzl
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2020
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 10/26/2020
ms.openlocfilehash: f64b656b6a0a0d5310b6d6e2fbb8ff26d5206ad1
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2021
ms.locfileid: "97910887"
---
# <a name="back-up-azure-stack---modular-data-center-mdc"></a>Zálohování Azure Stackho datového centra (MDC)

*Platí pro: modulární datové centrum, Azure Stack centrum je robustní*

V tomto článku se dozvíte, jak provést zálohování Azure Stack na vyžádání.

## <a name="start-backup"></a>Spustit zálohování

Zálohy jsou automaticky naplánovány. Spuštění zálohování na vyžádání je nezbytné pouze v případě, že obdržíte výstrahu k ručnímu spuštění zálohování. Pokud chcete spustit zálohování na vyžádání, vyberte **Zálohovat nyní**. Zálohování na vyžádání nemění čas příštího plánovaného zálohování. Po dokončení úlohy můžete nastavení potvrdit v okně **základy**:

![Nastavení zálohování](media/azure-stack-backup-back-up-azure-stack-tzl/on-demand-backup.png)

## <a name="start-azure-stack-backup"></a>Spustit zálohování Azure Stack

Můžete také spustit rutinu PowerShellu **Start-AzsBackup** na počítači správce Azure Stack.

### <a name="start-a-new-backup-without-job-progress-tracking"></a>Spuštění nové zálohy bez sledování průběhu úlohy

K okamžitému spuštění nové zálohy bez sledování průběhu úlohy použijte **Start-AzSBackup** .

```powershell
Start-AzsBackup -Force
```

## <a name="start-azure-stack-backup-with-job-progress-tracking"></a>Spustit zálohování Azure Stack pomocí sledování průběhu úlohy

Pomocí **Start-AzSBackup** spusťte novou zálohu s parametrem **-AsJob** a uložte ji jako proměnnou pro sledování průběhu úlohy zálohování.

> [!NOTE]
> Úloha zálohování se na portálu zobrazí jako úspěšně dokončená přibližně po 10-15 minutách, než se úloha dokončí.
>
> Skutečný stav je lépe pozorován prostřednictvím následujícího kódu.

Počáteční zpoždění od 1 milisekundy je zavedeno, protože kód je příliš rychlý na to, aby byla úloha správně registrována. Kód hlásí žádné **PSBeginTime** a zase, žádný **stav** úlohy.

```powershell
$BackupJob = Start-AzsBackup -Force -AsJob
While (!$BackupJob.PSBeginTime) {
    Start-Sleep -Milliseconds 1
}
Write-Host "Start time: $($BackupJob.PSBeginTime)"
While ($BackupJob.State -eq "Running") {
    Write-Host "Job is currently: $($BackupJob.State) - Duration: $((New-TimeSpan -Start ($BackupJob.PSBeginTime) -End (Get-Date)).ToString().Split(".")[0])"
    Start-Sleep -Seconds 30
}

If ($BackupJob.State -eq "Completed") {
    Get-AzsBackup | Where-Object {$_.BackupId -eq $BackupJob.Output.BackupId}
    $Duration = $BackupJob.Output.TimeTakenToCreate
    $Pattern = '^P?T?((?<Years>\d+)Y)?((?<Mohs>\d+)M)?((?<Weeks>\d+)W)?((?<Days>\d+)D)?(T((?<Hours>\d+)H)?((?<Minutes>\d+)M)?((?<Seconds>\d*(\.)?\d*)S)?)$'
    If ($Duration -match $Pattern) {
        If (!$Matches.ContainsKey("Hours")) {
            $Hours = ""
        } 
        Else {
            $Hours = ($Matches.Hours).ToString + 'h '
        }
        $Minutes = ($Matches.Minutes)
        $Seconds = [math]::round(($Matches.Seconds))
        $Runtime = '{0}{1:00}m {2:00}s' -f $Hours, $Minutes, $Seconds
    }
    Write-Host "BackupJob: $($BackupJob.Output.BackupId) - Completed with Status: $($BackupJob.Output.Status) - It took: $($Runtime) to run" -ForegroundColor Green
}
ElseIf ($BackupJob.State -ne "Completed") {
    $BackupJob
    $BackupJob.Output
}
```

## <a name="confirm-backup-has-completed"></a>Potvrzení zálohování bylo dokončeno.

### <a name="confirm-backup-has-completed-using-powershell"></a>Potvrzení zálohování bylo dokončeno pomocí prostředí PowerShell.

Pomocí následujících příkazů PowerShellu zajistěte, aby se zálohování úspěšně dokončilo:

```powershell
Get-AzsBackup
```

Výsledek by měl vypadat jako následující výstup:

```shell
BackupDataVersion : 1.0.1
BackupId          : <backup ID>
RoleStatus        : {NRP, SRP, CRP, KeyVaultInternalControlPlane...}
Status            : Succeeded
CreatedDateTime   : 7/6/2018 6:46:24 AM
TimeTakenToCreate : PT20M32.364138S
DeploymentID      : <deployment ID>
StampVersion      : 1.1807.0.41
OemVersion        : 
Id                : /subscriptions/<subscription ID>/resourceGroups/System.local/providers/Microsoft.Backup.Admin/backupLocations/local/backups/<backup ID>
Name              : local/<local name>
Type              : Microsoft.Backup.Admin/backupLocations/backups
Location          : local
Tags              : {}
```

### <a name="confirm-backup-has-completed-in-the-administrator-portal"></a>Potvrzení zálohování se dokončilo na portálu pro správu.

Pomocí portálu Azure Stack správce ověřte, že se zálohování úspěšně dokončilo pomocí následujících kroků:

1. Otevřete [portál Azure Stack správce](../../operator/azure-stack-manage-portals.md).

2. Vyberte **všechny služby** a potom v kategorii **Správa** vyberte **zálohování infrastruktury**. V okně **zálohování infrastruktury** vyberte **Konfigurace** .

3. V seznamu **dostupné zálohy** vyhledejte **název** a **Datum dokončení** zálohování.

4. Ověřte, zda je **stav** **úspěšný**.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [osvědčených postupech zálohování infrastruktury](azure-stack-backup-best-practices-tzl.md).
