---
title: Povolení zálohování pro Azure Stack s využitím PowerShellu | Microsoft Docs
description: Naučte se, jak povolit službu Infrastructure Backup pomocí prostředí PowerShell, aby bylo možné Azure Stack obnovit v případě selhání.
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
ms.date: 12/16/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 12/16/2019
ms.openlocfilehash: f0476f3ee331c014436e9ee2f879afcafb3320d7
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96939539"
---
# <a name="configure-backup-for-azure-stack-with-powershell"></a>Konfigurace zálohování pro Azure Stack s využitím PowerShellu

*Platí pro: modulární datové centrum, Azure Stack centrum je robustní*

Službu Infrastructure Backup můžete nakonfigurovat tak, aby exportovali zálohy infrastruktury do externího úložiště v umístění s využitím PowerShellu. Zálohy infrastruktury je možné využívat prostřednictvím podpory k nápravě degradované služby.

## <a name="prepare-powershell-environment"></a>Příprava prostředí PowerShell

Pokyny týkající se konfigurace prostředí PowerShellu najdete v tématu [instalace PowerShellu pro Azure Stack](../../operator/azure-stack-powershell-install.md). Pokud se chcete přihlásit k Azure Stack, přečtěte si téma [Konfigurace prostředí operátorů a přihlášení k Azure Stack](../../operator/azure-stack-powershell-configure-admin.md).

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>Zadání záložní sdílené složky, přihlašovacích údajů a šifrovacího klíče pro povolení zálohování

Ve stejné relaci prostředí PowerShell upravte následující skript prostředí PowerShell přidáním proměnných pro vaše prostředí. Spusťte aktualizovaný skript a poskytněte službě Infrastructure Backup záložní sdílenou složku, přihlašovací údaje a šifrovací klíč.

|Proměnná  |Popis  |
|---------|---------|
|$username     | Zadejte **uživatelské jméno** s použitím domény a uživatelského jména pro umístění sdílené jednotky s dostatečným přístupem ke čtení a zápisu souborů. Například contoso \\ backupshareuser.        |
|$password     | Zadejte **heslo** pro uživatele.        |
|$path     | Zadejte cestu k **umístění úložiště zálohy**. Je nutné použít řetězec UNC (Universal Naming Convention) pro cestu ke sdílené složce hostované na samostatném zařízení. Řetězec UNC určuje umístění prostředků, jako jsou třeba sdílené soubory nebo zařízení. Aby se zajistila dostupnost zálohovaných dat, mělo by být zařízení v samostatném umístění.        |
|$backupfrequencyinhours     | Frekvence v hodinách určuje, jak často se vytvářejí zálohy. Výchozí hodnota je 12. Scheduler podporuje minimálně 4 a maximálně 12.        |
|$backupretentionperiodindays     | Doba uchování ve dnech určuje, kolik dní zálohování se uchová na externím umístění. Výchozí hodnota je 7. Scheduler podporuje minimálně 2 a maximálně 14. Zálohy starší než doba uchování se automaticky odstraní z externího umístění.        |
|$encryptioncertpath     | Certifikát poskytnutý při nasazení Při konfiguraci externího umístění úložiště není potřeba zadávat novou. Cesta k šifrovacímu certifikátu určuje cestu k souboru. Soubor CER s veřejným klíčem, který se používá pro šifrování dat        |
|$isbackupschedulerenabled     | Povolí nebo zakáže automatické zálohování. Po poskytnutí sdílené složky a přihlašovacích údajů jsou automatické zálohování povolené ve výchozím nastavení.        |

## <a name="configure-backup"></a>Konfigurace zálohování

```powershell
# Example username
$username = "domain\backupadmin"

# Example share path
$sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"

$password = Read-Host -Prompt ("Password for: " + $username) -AsSecureString

# Set the backup settings with the name, password, share, and CER certificate file.
Set-AzsBackupConfiguration -BackupShare path -Username $username -Password $password
```

## <a name="confirm-backup-settings"></a>Potvrdit nastavení zálohování

Ve stejné relaci prostředí PowerShell spusťte následující příkazy:

```powershell
Get-AzsBackupConfiguration | Select-Object -Property Path, UserName
```

Výsledek by měl vypadat jako v následujícím příkladu výstupu:

```shell
Path : \\serverIP\AzsBackupStore\contoso.com\seattle
UserName : domain\backupadmin
```

## <a name="update-backup-settings"></a>Aktualizovat nastavení zálohování

Ve stejné relaci prostředí PowerShell můžete aktualizovat výchozí hodnoty pro dobu uchování a četnost záloh:

```powershell
# Set the backup frequency and retention period values.
$frequencyInHours = 10
$retentionPeriodInDays = 5

Set-AzsBackupConfiguration -BackupFrequencyInHours $backupfrequencyInHours -BackupRetentionPeriodInDays $backupretentionPeriodInDays

Get-AzsBackupConfiguration | Select-Object -Property Path, UserName, AvailableCapacity, BackupFrequencyInHours, BackupRetentionPeriodInDays
```

## <a name="update-encryption-certificate"></a>Aktualizovat šifrovací certifikát

Ve stejné relaci prostředí PowerShell můžete aktualizovat šifrovací certifikát používaný k šifrování zálohovaných dat. Nový šifrovací certifikát budou používat jenom nové zálohy. Všechna stávající zálohování zůstanou zašifrovaná s předchozím certifikátem. Nezapomeňte si nechat kopii předchozího certifikátu aspoň na měsíc, abyste měli jistotu, že předchozí zálohy zašifrované pomocí starého certifikátu jsou vyčištěné:

```powershell
#Set the new encryption certificate by providing local path to CER file.
Set-AzsBackupConfiguration -EncryptionCertPath "c:\temp\cert.cer"
```

## <a name="enable-or-disable-automatic-backups"></a>Povolit nebo zakázat automatické zálohování

Ve stejné relaci prostředí PowerShell můžete povolit nebo zakázat automatické zálohování. Automatické zálohování jsou ve výchozím nastavení povolená.

```powershell
#Disable automatic backups.
Set-AzsBackupConfiguration - Isbackupschedulerenabled $false

#Enable automatic backups.
Set-AzsBackupConfiguration - Isbackupschedulerenabled $true
```

## <a name="next-steps"></a>Další kroky

Informace o tom, jak ověřit, že se zálohování spustilo, najdete [v tématu potvrzení zálohování dokončeno na portálu pro správu](../../operator/azure-stack-backup-back-up-azure-stack.md).
