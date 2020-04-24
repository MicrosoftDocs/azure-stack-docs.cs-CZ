---
title: Zálohování centra Azure Stack
description: Naučte se, jak provést zálohování na vyžádání v centru Azure Stack.
author: justinha
ms.topic: article
ms.date: 02/12/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 09/05/2019
ms.openlocfilehash: 10a7c60a18faa5528fb9c1917c62994b43bee51a
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "77701033"
---
# <a name="back-up-azure-stack-hub"></a>Zálohování centra Azure Stack

V tomto článku se dozvíte, jak provést zálohování na vyžádání v centru Azure Stack. Pokyny týkající se konfigurace prostředí PowerShellu najdete v tématu [instalace PowerShellu pro Azure Stack hub](azure-stack-powershell-install.md). Pokud se chcete přihlásit do centra Azure Stack, přečtěte si téma [použití portálu pro správu v centru Azure Stack](azure-stack-manage-portals.md).

## <a name="start-azure-stack-hub-backup"></a>Spustit zálohování centra Azure Stack

### <a name="start-a-new-backup-without-job-progress-tracking"></a>Spuštění nové zálohy bez sledování průběhu úlohy
K okamžitému spuštění nové zálohy bez sledování průběhu úlohy použijte Start-AzSBackup.

```powershell
   Start-AzsBackup -Force
```

### <a name="start-azure-stack-hub-backup-with-job-progress-tracking"></a>Spustit zálohování centra Azure Stack pomocí sledování průběhu úlohy
Pomocí Start-AzSBackup spusťte novou zálohu s parametrem **-AsJob** a uložte ji jako proměnnou pro sledování průběhu úlohy zálohování.

> [!NOTE]
> Úloha zálohování se na portálu zobrazí jako úspěšně dokončená přibližně po 10-15 minutách, než se úloha dokončí.
>
> Skutečný stav je lépe pozorován prostřednictvím kódu níže.

> [!IMPORTANT]
> Počáteční zpoždění od 1 milisekundy je zavedeno, protože kód je příliš rychlý pro správné zaregistrování úlohy a vrátí se bez **PSBeginTime** a zase se neshoduje s žádným **stavem** úlohy.

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
        $Pattern = '^P?T?((?<Years>\d+)Y)?((?<Months>\d+)M)?((?<Weeks>\d+)W)?((?<Days>\d+)D)?(T((?<Hours>\d+)H)?((?<Minutes>\d+)M)?((?<Seconds>\d*(\.)?\d*)S)?)$'
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

```powershell
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
Pomocí portálu Azure Stack hub správce ověřte, že se zálohování úspěšně dokončilo, pomocí následujících kroků:

1. Otevřete [portál Azure Stack centrum pro správu](azure-stack-manage-portals.md).
2. Vyberte **všechny služby**a potom v kategorii **Správa** vyberte > **zálohování infrastruktury**. V okně **zálohování infrastruktury** vyberte **Konfigurace** .
3. V seznamu **dostupné zálohy** vyhledejte **název** a **Datum dokončení** zálohování.
4. Ověřte, zda je **stav** **úspěšný**.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o pracovním postupu pro [zotavení z události ztráty dat](azure-stack-backup-recover-data.md).
