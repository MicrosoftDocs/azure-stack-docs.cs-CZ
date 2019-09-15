---
title: Povolení zálohování pro Azure Stack s využitím PowerShellu | Microsoft Docs
description: Naučte se, jak povolit službu Infrastructure Backup pomocí prostředí PowerShell, aby bylo možné Azure Stack obnovit v případě selhání.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 03/14/2019
ms.openlocfilehash: 2e419c32caf78d97ee38e570ce0fa823cc94651a
ms.sourcegitcommit: 245a4054a52e54d5989d6148fbbe386e1b2aa49c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2019
ms.locfileid: "70975188"
---
# <a name="enable-backup-for-azure-stack-with-powershell"></a>Povolit zálohování pro Azure Stack pomocí Powershellu

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Povolení služby Infrastructure Backup v prostředí Windows PowerShell pro provedení pravidelných záloh:
 - Interní služba identit a kořenový certifikát.
 - Uživatelské plány, nabídky a odběry.
 - Výpočetní prostředky, úložiště a síťové kvóty uživatelů.
 - Key Vault tajné klíče uživatele.
 - Role a zásady RBAC uživatele.
 - Uživatelské účty úložiště.

K rutinám PowerShellu máte přístup, abyste mohli povolit zálohování, spustit zálohování a získávat informace o zálohování prostřednictvím koncového bodu správy operátorů.

## <a name="prepare-powershell-environment"></a>Příprava prostředí PowerShell

Pokyny týkající se konfigurace prostředí PowerShellu najdete v tématu [instalace PowerShellu pro Azure Stack](azure-stack-powershell-install.md). Pokud se chcete přihlásit k Azure Stack, přečtěte si téma [Konfigurace prostředí operátorů a přihlášení k Azure Stack](azure-stack-powershell-configure-admin.md).

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>Zadání záložní sdílené složky, přihlašovacích údajů a šifrovacího klíče pro povolení zálohování

Ve stejné relaci prostředí PowerShell upravte následující skript prostředí PowerShell přidáním proměnných pro vaše prostředí. Spusťte aktualizovaný skript a poskytněte službě Infrastructure Backup záložní sdílenou složku, přihlašovací údaje a šifrovací klíč.

| Proměnná        | Popis   |
|---              |---                                        |
| `$username`       | Zadejte **uživatelské jméno** s použitím domény a uživatelského jména pro umístění sdílené jednotky s dostatečným přístupem ke čtení a zápisu souborů. Například, `Contoso\backupshareuser`. |
| `$password`       | Zadejte **heslo** pro uživatele. |
| `$sharepath`      | Zadejte cestu k **umístění úložiště zálohy**. Je nutné použít řetězec UNC (Universal Naming Convention) pro cestu ke sdílené složce hostované na samostatném zařízení. Řetězec UNC určuje umístění prostředků, jako jsou třeba sdílené soubory nebo zařízení. Aby se zajistila dostupnost zálohovaných dat, mělo by být zařízení v samostatném umístění. |
| `$frequencyInHours` | Frekvence v hodinách určuje, jak často se vytvářejí zálohy. Výchozí hodnota je 12. Scheduler podporuje maximálně 12 a minimálně 4.|
| `$retentionPeriodInDays` | Doba uchování ve dnech určuje, kolik dní zálohování se uchová na externím umístění. Výchozí hodnota je 7. Scheduler podporuje maximálně 14 a minimálně 2. Zálohy starší než doba uchování se automaticky odstraní z externího umístění.|
| `$encryptioncertpath` | Platí pro 1901 a novější. Parametr je k dispozici v modulu Azure Stack verze 1,7 a novější. Cesta k šifrovacímu certifikátu určuje cestu k souboru. Soubor CER s veřejným klíčem, který se používá pro šifrování dat |
| `$encryptionkey` | Platí pro Build 1811 nebo starší. Parametr je k dispozici v modulu Azure Stack verze 1,6 nebo starší. Šifrovací klíč se používá k šifrování dat. K vygenerování nového klíče použijte rutinu [New-AzsEncryptionKeyBase64](https://docs.microsoft.com/powershell/module/azs.backup.admin/new-azsencryptionkeybase64) . |
|     |     |

### <a name="enable-backup-on-1901-and-later-using-certificate"></a>Povolit zálohování v 1901 a novějším pomocí certifikátu
```powershell
    # Example username:
    $username = "domain\backupadmin"
 
    # Example share path:
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"

    $password = Read-Host -Prompt ("Password for: " + $username) -AsSecureString

    # Create a self-signed certificate using New-SelfSignedCertificate, export the public key portion and save it locally.

    $cert = New-SelfSignedCertificate `
        -DnsName "www.contoso.com" `
        -CertStoreLocation "cert:\LocalMachine\My" 

    New-Item -Path "C:\" -Name "Certs" -ItemType "Directory" 

    #make sure to export the PFX format of the certificate with the public and private keys and then delete the certificate from the local certificate store of the machine where you created the certificate
    
    Export-Certificate `
        -Cert $cert `
        -FilePath c:\certs\AzSIBCCert.cer 

    # Set the backup settings with the name, password, share, and CER certificate file.
    Set-AzsBackupConfiguration -BackupShare $sharepath -Username $username -Password $password -EncryptionCertPath "c:\temp\cert.cer"
```
### <a name="enable-backup-on-1811-or-earlier-using-certificate"></a>Povolení zálohování v 1811 nebo starším pomocí certifikátu
```powershell
    # Example username:
    $username = "domain\backupadmin"
 
    # Example share path:
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"

    $password = Read-Host -Prompt ("Password for: " + $username) -AsSecureString

    # Create a self-signed certificate using New-SelfSignedCertificate, export the public key portion and save it locally.

    $key = New-AzsEncryptionKeyBase64
    $Securekey = ConvertTo-SecureString -String ($key) -AsPlainText -Force

    # Set the backup settings with the name, password, share, and CER certificate file.
    Set-AzsBackupConfiguration -BackupShare $sharepath -Username $username -Password $password -EncryptionKey $Securekey
```

   
##  <a name="confirm-backup-settings"></a>Potvrdit nastavení zálohování

Ve stejné relaci prostředí PowerShell spusťte následující příkazy:

   ```powershell
    Get-AzsBackupConfiguration | Select-Object -Property Path, UserName
   ```

Výsledek by měl vypadat jako v následujícím příkladu výstupu:

   ```powershell
    Path                        : \\serverIP\AzsBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
   ```

## <a name="update-backup-settings"></a>Aktualizovat nastavení zálohování
Ve stejné relaci prostředí PowerShell můžete aktualizovat výchozí hodnoty pro dobu uchování a četnost zálohování. 

   ```powershell
    #Set the backup frequency and retention period values.
    $frequencyInHours = 10
    $retentionPeriodInDays = 5

    Set-AzsBackupConfiguration -BackupFrequencyInHours $frequencyInHours -BackupRetentionPeriodInDays $retentionPeriodInDays

    Get-AzsBackupConfiguration | Select-Object -Property Path, UserName, AvailableCapacity, BackupFrequencyInHours, BackupRetentionPeriodInDays
   ```

Výsledek by měl vypadat jako v následujícím příkladu výstupu:

   ```powershell
    Path                        : \\serverIP\AzsBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
    AvailableCapacity           : 60 GB
    BackupFrequencyInHours      : 10
    BackupRetentionPeriodInDays : 5
   ```

### <a name="azure-stack-powershell"></a>Azure Stack PowerShell 
Rutina prostředí PowerShell pro konfiguraci zálohování infrastruktury je nastavená na AzsBackupConfiguration. V předchozích verzích byla rutina nastavená na AzsBackupShare. Tato rutina vyžaduje poskytnutí certifikátu. Pokud je zálohování infrastruktury nakonfigurované pomocí šifrovacího klíče, nemůžete aktualizovat šifrovací klíč ani zobrazit jeho vlastnost. Musíte použít verzi 1,6 prostředí PowerShell pro správu.

Pokud byla před aktualizací na 1901 nakonfigurovaná záloha infrastruktury, můžete k nastavení a zobrazení šifrovacího klíče použít verzi 1,6 prostředí PowerShell pro správu. Verze 1,6 vám neumožní aktualizovat ze šifrovacího klíče na soubor certifikátu.
Další informace o instalaci správné verze modulu najdete v tématu věnovaném [instalaci Azure Stack PowerShellu](azure-stack-powershell-install.md) .


## <a name="next-steps"></a>Další postup

Další informace o spuštění zálohování najdete v tématu [zálohování Azure Stack](azure-stack-backup-back-up-azure-stack.md).

Informace o tom, jak ověřit, že se zálohování spustilo, najdete [v tématu potvrzení zálohování dokončeno na portálu pro správu](azure-stack-backup-back-up-azure-stack.md).
