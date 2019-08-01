---
title: Ověření zálohy Azure Stack pomocí ASDK | Microsoft Docs
description: Jak ověřit zálohu Azure Stack integrovaných systémů pomocí nástroje ASDK.
services: azure-stack
author: justinha
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 07/31/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 07/31/2019
ms.openlocfilehash: 3ab7dfbaef82868f45b181fb81d9b98050147191
ms.sourcegitcommit: bf4d265a3522cbfdd9dd295a0f4ad0daf2ed5eca
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68692118"
---
# <a name="use-the-asdk-to-validate-an-azure-stack-backup"></a>Použití ASDK k ověření zálohy Azure Stack
Po nasazení Azure Stack a zřízení uživatelských prostředků, jako jsou nabídky, plány, kvóty a odběry, byste měli [povolit Azure Stack zálohování infrastruktury](../operator/azure-stack-backup-enable-backup-console.md). Plánování a spouštění pravidelných záloh infrastruktury zajistí, že data správy infrastruktury nebudou ztracena v případě závažného selhání hardwaru nebo služby.

> [!TIP]
> Před zahájením tohoto postupu doporučujeme [Spustit zálohování na vyžádání](../operator/azure-stack-backup-back-up-azure-stack.md) , abyste měli jistotu, že máte k dispozici kopii nejnovějších dat infrastruktury. Po úspěšném dokončení zálohování nezapomeňte zachytit ID zálohy. Toto ID se bude vyžadovat během obnovování cloudu. 

Zálohy infrastruktury Azure Stack obsahují důležitá data o cloudu, která se dají obnovit během opětovného nasazení Azure Stack. Pomocí ASDK můžete tyto zálohy ověřit bez dopadu na produkční Cloud. 

Ověřování záloh v ASDK se podporuje v následujících scénářích:

|Scénář|Účel|
|-----|-----|
|Ověří zálohy infrastruktury z integrovaného řešení.|Krátkodobé ověření, že jsou data v záloze platná.|
|Přečtěte si kompletní pracovní postup obnovení.|Pomocí ASDK ověříte celé prostředí pro zálohování a obnovení.|
|     |     |

Následující scénář **není** podporován při ověřování záloh na ASDK:

|Scénář|Účel|
|-----|-----|
|ASDK sestavení pro sestavení zálohování a obnovení.|Obnoví zálohovaná data z předchozí verze ASDK na novější verzi.|
|     |     |


## <a name="cloud-recovery-deployment"></a>Nasazení Cloud Recovery
Zálohy infrastruktury z nasazení integrovaných systémů se dají ověřit pomocí nasazení cloudového obnovení ASDK. V tomto typu nasazení se po instalaci ASDK na hostitelský počítač obnoví konkrétní data služby ze zálohy.

### <a name="prereqs"></a>Požadavky na Cloud Recovery
Než začnete s nasazením ASDK Cloud Recovery, ujistěte se, že máte následující informace:

**Požadavky na instalační program uživatelského rozhraní**

*Aktuální instalační program uživatelského rozhraní podporuje jenom šifrovací klíč.*

|Požadavek|Popis|
|-----|-----|
|Cesta ke sdílené složce zálohy|Cesta ke sdílené složce souborů UNC nejnovější zálohy Azure Stack, která se použije k obnovení informací o Azure Stack infrastruktuře. Tato místní sdílená složka se vytvoří během procesu nasazení Cloud Recovery.|
|ID zálohy, která se má obnovit|ID zálohy v alfanumerickém tvaru "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx", který identifikuje zálohu, která má být obnovena během obnovování cloudu.|
|IP adresa časového serveru|Pro nasazení Azure Stack se vyžaduje platná časová IP adresa serveru, například 132.163.97.2.|
|Heslo k externímu certifikátu|Heslo pro externí certifikát, který používá Azure Stack. Záloha certifikační autority obsahuje externí certifikáty, které je potřeba obnovit pomocí tohoto hesla.|
|Záložní šifrovací klíč|Vyžaduje se, pokud jsou nastavení zálohování nakonfigurovaná pomocí šifrovacího klíče, který se už nepoužívá. Instalační program bude podporovat šifrovací klíč v režimu zpětné kompatibility pro nejméně 3 verze. Po aktualizaci nastavení zálohování pro použití certifikátu si přečtěte požadované informace v další tabulce.|

|     |     | 

**Požadavky na instalační program prostředí PowerShell**

*Aktuální instalační program PowerShellu podporuje šifrovací klíč nebo dešifrovací certifikát.*

|Požadavek|Popis|
|-----|-----|
|Cesta ke sdílené složce zálohy|Cesta ke sdílené složce souborů UNC nejnovější zálohy Azure Stack, která se použije k obnovení informací o Azure Stack infrastruktuře. Tato místní sdílená složka se vytvoří během procesu nasazení Cloud Recovery.|
|ID zálohy, která se má obnovit|ID zálohy v alfanumerickém tvaru "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx", který identifikuje zálohu, která má být obnovena během obnovování cloudu.|
|IP adresa časového serveru|Pro nasazení Azure Stack se vyžaduje platná časová IP adresa serveru, například 132.163.97.2.|
|Heslo k externímu certifikátu|Heslo pro externí certifikát, který používá Azure Stack. Záloha certifikační autority obsahuje externí certifikáty, které je potřeba obnovit pomocí tohoto hesla.|
|Heslo certifikátu dešifrování|Volitelné. Požadováno jenom v případě, že je záloha zašifrovaná pomocí certifikátu. Heslo je pro certifikát podepsaný svým držitelem (. pfx), který obsahuje privátní klíč nezbytný k dešifrování zálohovaných dat.|
|Záložní šifrovací klíč|Volitelné. Vyžaduje se, pokud jsou nastavení zálohování pořád nakonfigurovaná pomocí šifrovacího klíče. Instalační program bude podporovat šifrovací klíč v režimu zpětné kompatibility pro nejméně 3 verze. Po aktualizaci nastavení zálohování pro použití certifikátu je nutné zadat heslo pro dešifrovací certifikát.|
|     |     | 

## <a name="prepare-the-host-computer"></a>Příprava hostitelského počítače 
Stejně jako v případě normálního nasazení ASDK musí být prostředí hostitelského systému ASDK připravené k instalaci. Po přípravě hostitelského počítače vývojové sady se spustí z pevného disku virtuálního počítače s CloudBuilder. vhdx, aby se zahájilo nasazení ASDK.

V hostitelském počítači ASDK Stáhněte novou cloudbuilder. vhdx odpovídající stejné verzi Azure Stack, která byla zálohována, a postupujte podle pokynů pro [přípravu hostitelského počítače ASDK](asdk-prepare-host.md).

Po restartování hostitelského serveru ze souboru cloudbuilder. vhdx musíte vytvořit sdílenou složku a zkopírovat data ze zálohy do nástroje. Sdílená složka by měla být přístupná pro účet, který spouští instalaci; Správce v těchto ukázkových příkazech PowerShell: 

```powershell
$shares = New-Item -Path "c:\" -Name "Shares" -ItemType "directory"
$azsbackupshare = New-Item -Path $shares.FullName -Name "AzSBackups" -ItemType "directory"
New-SmbShare -Path $azsbackupshare.FullName -FullAccess ($env:computername + "\Administrator")  -Name "AzSBackups"
```

Potom zkopírujte nejnovější Azure Stack záložní soubory do nově vytvořené sdílené složky. Struktura složek v rámci sdílené složky by měla být `\\<ComputerName>\AzSBackups\MASBackup\<BackupID>\`:.

Nakonec zkopírujte dešifrovací certifikát (. pfx) do adresáře certifikátu: `C:\CloudDeployment\Setup\Certificates\` a přejmenujte ho na. `BackupDecryptionCert.pfx`

## <a name="deploy-the-asdk-in-cloud-recovery-mode"></a>Nasazení ASDK v režimu Cloud Recovery

> [!IMPORTANT]
> 1. Aktuální uživatelské rozhraní instalačního programu podporuje jenom šifrovací klíč. Můžete ověřovat jenom zálohy ze systémů, které nadále používají šifrovací klíč. Pokud byla záloha zašifrovaná v integrovaném systému nebo ASDK pomocí certifikátu, musíte použít instalační program PowerShellu (**InstallAzureStackPOC. ps1**). 
> 2. Instalační program PowerShellu (**InstallAzureStackPOC. ps1**) podporuje šifrovací klíč nebo certifikát.
> 3. Instalace ASDK podporuje pro sítě právě jednu síťovou kartu (NIC). Pokud máte více síťových adaptérů, ujistěte se, že je před spuštěním skriptu nasazení povolená jenom jedna (a všechny ostatní jsou zakázané).

### <a name="use-the-installer-ui-to-deploy-the-asdk-in-recovery-mode"></a>Nasazení ASDK do režimu obnovení pomocí uživatelského rozhraní instalačního programu
Kroky v této části ukazují, jak nasadit ASDK pomocí grafického uživatelského rozhraní (GUI) poskytovaného stažením a spuštěním skriptu PowerShellu **asdk-Installer. ps1** .

> [!NOTE]
> Uživatelské rozhraní instalačního programu pro Azure Stack Development Kit je open source skript založený na WCF a PowerShellu.

> [!IMPORTANT]
> Aktuální uživatelské rozhraní instalačního programu podporuje jenom šifrovací klíč.

1. Po úspěšném spuštění hostitelského počítače do image CloudBuilder. vhdx se přihlaste pomocí přihlašovacích údajů správce zadaných při [přípravě hostitelského počítače vývojové sady](asdk-prepare-host.md) pro instalaci ASDK. To by mělo být stejné jako pověření místního správce hostitele vývojové sady.
2. Otevřete konzolu nástroje PowerShell se zvýšenými oprávněními a spusťte  **&lt;písmeno jednotky >** skriptu prostředí PowerShell pro \AzureStack_Installer\asdk-Installer.ps1. Skript se teď může nacházet na jiné jednotce než C:\. v imagi CloudBuilder. vhdx. Klikněte na tlačítko **obnovit**.

    ![Skript instalačního programu ASDK](media/asdk-validate-backup/1.PNG) 

3. Zadejte informace o adresáři služby Azure AD (volitelné) a heslo místního správce pro hostitelský počítač ASDK na stránce zprostředkovatel identity a přihlašovací údaje. Klikněte na **Další**.

    ![Stránka identita a přihlašovací údaje](media/asdk-validate-backup/2.PNG) 

4. Vyberte síťový adaptér, který má hostitelský počítač ASDK použít, a klikněte na **Další**. Všechna ostatní síťová rozhraní budou během instalace ASDK zakázaná. 

    ![Rozhraní síťového adaptéru](media/asdk-validate-backup/3.PNG) 

5. Na stránce konfigurace sítě zadejte platné IP adresy časového serveru a serveru DNS pro přeposílání. Klikněte na **Další**.

    ![Stránka konfigurace sítě](media/asdk-validate-backup/4.PNG) 

6. Až budou vlastnosti síťového rozhraní ověřené, klikněte na **Další**. 

    ![Ověření nastavení síťové karty](media/asdk-validate-backup/5.PNG) 

7. Zadejte požadované informace popsané výše v [části požadavky](#prereqs) na stránce nastavení zálohování a uživatelské jméno a heslo, které se má použít pro přístup ke sdílené složce. Klikněte na **Další**: 

   ![Stránka nastavení zálohování](media/asdk-validate-backup/6.PNG) 

8. Zkontrolujte skript nasazení, který se použije k nasazení ASDK na stránce Souhrn. Kliknutím na **nasadit** zahajte nasazení. 

    ![Stránka souhrnu](media/asdk-validate-backup/7.PNG) 


### <a name="use-powershell-to-deploy-the-asdk-in-recovery-mode"></a>Použití PowerShellu k nasazení ASDK v režimu obnovení

Upravte následující příkazy PowerShellu pro vaše prostředí a spusťte je, abyste nasadili ASDK v režimu Cloud Recovery:

**Pomocí skriptu InstallAzureStackPOC. ps1 zahajte obnovení cloudu pomocí šifrovacího klíče.**

```powershell
cd C:\CloudDeployment\Setup     
$adminpass = Read-Host -AsSecureString -Prompt "Local Administrator password"
$certPass = Read-Host -AsSecureString -Prompt "Password for the external certificate"
$backupstorecredential = Read-Host -AsSecureString -Prompt "Credential for backup share"
$key = Read-Host -AsSecureString -Prompt "Your backup encryption key"

.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass `
 -BackupStorePath ("\\" + $env:COMPUTERNAME + "\AzSBackups") `
 -BackupEncryptionKeyBase64 $key `
 -BackupStoreCredential $backupstorecredential `
 -BackupId "<Backup ID to restore>" `
 -TimeServer "<Valid time server IP>" -ExternalCertPassword $certPass
```

**Pomocí skriptu InstallAzureStackPOC. ps1 zahajte obnovení cloudu s dešifrovacím certifikátem.**

```powershell
cd C:\CloudDeployment\Setup     
$adminpass = Read-Host -AsSecureString -Prompt "Local Administrator password"
$certPass = Read-Host -AsSecureString -Prompt "Password for the external certificate"
$backupstorecredential = Read-Host -AsSecureString -Prompt "Credential for backup share"
$decryptioncertpassword  = Read-Host -AsSecureString -Prompt "Password for the decryption certificate"

.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass `
 -BackupStorePath ("\\" + $env:COMPUTERNAME + "\AzSBackups") `
 -BackupDecryptionCertPassword $decryptioncertpassword `
 -BackupStoreCredential $backupstorecredential `
 -BackupId "<Backup ID to restore>" `
 -TimeServer "<Valid time server IP>" -ExternalCertPassword $certPass
```

## <a name="complete-cloud-recovery"></a>Dokončení obnovení cloudu 
Po úspěšném nasazení Cloud Recovery je potřeba dokončit obnovení pomocí rutiny **Restore-AzureStack** . 

Po přihlášení jako operátor Azure Stack [nainstalujte Azure Stack PowerShellu](asdk-post-deploy.md#install-azure-stack-powershell) a spusťte následující příkazy, abyste zadali certifikát a heslo, které se má použít při obnovení ze zálohy:

**Režim obnovení se souborem certifikátu**

> [!NOTE] 
> Nasazení Azure Stack neuchovává certifikát dešifrování z bezpečnostních důvodů. Bude nutné znovu zadat certifikát dešifrování a přidružené heslo.

```powershell
$decryptioncertpassword = Read-Host -AsSecureString -Prompt "Password for the decryption certificate"
Restore-AzsBackup -ResourceId "<BackupID>" `
 -DecryptionCertPath "<path to decryption certificate with file name (.pfx)>" `
 -DecryptionCertPassword $decryptioncertpassword
```

**Režim obnovení se šifrovacím klíčem**
```powershell
$decryptioncertpassword = Read-Host -AsSecureString -Prompt "Password for the decryption certificate"
Restore-AzsBackup -ResourceId "<BackupID>"
```

Po volání této rutiny počkat 60 minut, aby se zahájilo ověřování zálohovaných dat v cloudu obnovených ASDK.

## <a name="next-steps"></a>Další kroky
[Registrovat Azure Stack](asdk-register.md)

