---
title: Pracovní stanice pro přístup k operátorovi centra Azure Stack
description: Přečtěte si, jak stáhnout a nakonfigurovat pracovní stanici pro přístup k operátorovi centra Azure Stack.
author: ashika789
ms.topic: article
ms.date: 11/04/2020
ms.author: patricka
ms.reviewer: asganesh
ms.lastreviewed: 11/04/2020
ms.openlocfilehash: bd94503efe97eb30955b0fd9deb7a5c41e263e7b
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97868586"
---
# <a name="azure-stack-hub-operator-access-workstation"></a>Pracovní stanice pro přístup k operátorovi centra Azure Stack 

Pracovní stanice pro přístup k síti (OAW) se používá k nasazení virtuálního počítače s odkazem na hostitele životního cyklu (HLH), na kterém běží verze 2005 nebo novější, takže operátor centra Azure Stack má přístup k privilegovanému koncovému bodu (PEP) a portálu pro správu pro scénáře podpory. 

Virtuální počítač OAW by měl být vytvořen, když operátor provede novou úlohu. Po dokončení požadované úlohy v rámci virtuálního počítače by se měl virtuální počítač vypnout a odebrat, protože Azure Stack hub nemusí vždy spouštět.  

## <a name="oaw-scenarios"></a>Scénáře OAW

V následujících tabulkách jsou uvedeny běžné scénáře pro OAW, ale to není exkluzivní. Pro připojení k OAW se doporučuje použít vzdálenou plochu. 

|Scénář                                                                                                                          |Popis                 |
|----------------------------------------------------------------------------------------------------------------------------------|-----------------------------|
|[Přístup k portálu pro správu](./azure-stack-manage-portals.md)                     |Provádění operací správy                                                                           |
|[Přístup k PEP](./azure-stack-privileged-endpoint.md)                                     |Shromažďování a nahrávání protokolů:<br>-[Vytvoření sdílené složky SMB](#transfer-files-between-the-hlh-and-oaw) na HLH pro přenos souborů z centra Azure Stack<br>– Použijte Průzkumník služby Azure Storage k nahrání protokolů uložených do sdílené složky protokolu SMB. |
|[Registrace centra Azure Stack](./azure-stack-registration.md#renew-or-change-registration) |Pro opakovanou registraci získat z portálu pro správu název předchozí registrace a skupinu prostředků                               |
|[Syndikace Marketplace](./azure-stack-download-azure-marketplace-item.md)            |[Vytvoření sdílené složky SMB](#transfer-files-between-the-hlh-and-oaw) na HLH pro uložení staženého obrázku nebo rozšíření                                                        |

## <a name="download-files"></a>Stažení souborů

Pokud chcete získat soubory k vytvoření virtuálního počítače s OAW, [**Stáhněte si ho tady**](https://aka.ms/OAWDownload). Před stažením si prosím přečtěte [prohlášení o ochraně osobních údajů společnosti Microsoft](https://privacy.microsoft.com/privacystatement) a [právní smlouvy](/legal/azure-stack-hub/azure-stack-operator-access-workstation-legal-terms) .

Z důvodu bezstavového charakteru řešení nejsou k dispozici žádné aktualizace pro virtuální počítač OAW. Pro každý milník se uvolní nová verze souboru bitové kopie virtuálního počítače. K vytvoření nového virtuálního počítače s OAW použijte nejnovější verzi. Soubor bitové kopie je založený na nejnovější verzi Windows serveru 2019. Po instalaci můžete pomocí web Windows Update použít aktualizace, včetně důležitých aktualizací. 

Ověřte hodnotu hash staženého souboru OAW.zip, abyste se ujistili, že se ještě nezměnily, než je použijete k vytvoření virtuálního počítače s OAW. Spusťte následující skript prostředí PowerShell. Pokud je vrácená hodnota true, můžete použít stažený OAW.zip:

```powershell
param(
    [Parameter(Mandatory=$True)]
    [ValidateNotNullOrEmpty()]
    [ValidateScript({Test-Path $_ -PathType Leaf})]
    [string]
    $DownloadedOAWZipFilePath
)

$expectedHash = '459D8BA232E4315372FCE7CDD705057D051C1BD60772FC36E0136C741C27A273'
$actualHash = (Get-FileHash -Path $DownloadedOAWZipFilePath).Hash

Write-Host "Expected hash: $expectedHash"

if ($expectedHash -eq $actualHash)
{
    Write-Host 'SUCCESS: OAW.zip file hash matches.'
}
else
{
    Write-Error 'ERROR: OAW.zip file hash does not match! It is not safe to use it, please download it again.'
    Write-Error "Actual hash: $actualHash"
}
```

## <a name="user-account-policy"></a>Zásady uživatelského účtu 
Na virtuálním počítači OAW se použije následující zásada uživatelského účtu:

- Předdefinované uživatelské jméno správce: AdminUser
- MinimumPasswordLength = 14
- PasswordComplexity je povolené.
- MinimumPasswordAge = 1 (den)
- MaximumPasswordAge = 42 (dny)
- NewGuestName = GUser (ve výchozím nastavení vypnuté)

## <a name="pre-installed-software"></a>Předinstalovaný software
V následující tabulce je uveden předinstalovaný software na virtuálním počítači s OAW.

| Název softwaru           | Umístění                                                                                       |
|--------------------------|------------------------------------------------------------------------------------------------|
| [Microsoft Edge pro firmy](https://www.microsoft.com/edge/business/)                                            | \[Systémová_jednotka \] \Program Files (x86) \Microsoft\Edge\Application                                                                                        |
| [AZ modules](./powershell-install-az-module.md)                         | \[Systémová_jednotka \] \ProgramFiles\WindowsPowerShell\Modules                                         |  
| [PowerShell 7](https://devblogs.microsoft.com/powershell/announcing-PowerShell-7-0/)| \[Systémová_jednotka \] \Program Files\PowerShell\7                                                                       |
| [Rozhraní příkazového řádku Azure Command-Line (CLI)](/cli/azure/?view=azure-cli-latest) | \[Systémová_jednotka \] \Program Files (x86) \Microsoft SDKs\Azure\CLI2 |
| [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)   | \[Systémová_jednotka \] \Program Files (x86) \microsoft Průzkumník služby Azure Storage                                                                       |
| [AzCopy](/azure/storage/common/storage-use-azcopy-v10)                             | \[Systémová_jednotka \] \vmsoftware\ azcopy_windows_amd64_10.3.4                                         |
| [AzureStack – nástroje](https://github.com/Azure/AzureStack-Tools/tree/az)                  | \[Systémová_jednotka \] \VMSoftware\AzureStack-Tools                                                    |

## <a name="check-hlh-version"></a>Ověřit verzi HLH

1. Přihlaste se k HLH pomocí vašich přihlašovacích údajů.
1. Otevřete PowerShell ISE a spusťte následující skript:

   ```powershell
   'C:\Version\Get-Version.ps1'
   ```

   Například:

   ![Snímek obrazovky s rutinou prostředí PowerShell pro kontrolu verze virtuálního počítače s OAW](./media/operator-access-workstation/check-hardware-lifecycle-host-version.png)

## <a name="create-the-oaw-vm-using-a-script"></a>Vytvoření virtuálního počítače s OAW pomocí skriptu

Následující skript připraví virtuální počítač jako pracovní stanici pro přístup k operátoru (OAW), která se používá pro přístup k centru Microsoft Azure Stack pro správu a diagnostiku.

1. Přihlaste se k HLH pomocí vašich přihlašovacích údajů.
1. Stáhněte OAW.zip a extrahujte soubory.
1. Otevřete relaci PowerShellu se zvýšenými oprávněními.
1. Přejděte na extrahovaný obsah souboru OAW.zip.
1. Spusťte skript New-OAW.ps1. 

Pokud například chcete vytvořit virtuální počítač s OAW v HLH bez přizpůsobení pomocí centra Azure Stack verze 2005 nebo novější, spusťte New-OAW.ps1 skript s pouze parametrem **-LocalAdministratorPassword** :

```powershell
$securePassword = Read-Host -Prompt "Enter password for Azure Stack OAW's local administrator" -AsSecureString
New-OAW.ps1 -LocalAdministratorPassword $securePassword  
```

Vytvoření virtuálního počítače s OAW na hostiteli, který má síťové připojení k Azure Stack hub:

```powershell
$securePassword = Read-Host -Prompt "Enter password for Azure Stack OAW's local administrator" -AsSecureString
New-OAW.ps1 -LocalAdministratorPassword $securePassword `
   -IPAddress '192.168.0.20' `
   -SubnetMask '255.255.255.0' `
   -DefaultGateway '192.168.0.1' `
   -DNS '192.168.0.10'
```

Chcete-li načíst IP adresu virtuálního počítače ERCS z AzureStackStampInformation.jsv souboru:

```powershell
$securePassword = Read-Host -Prompt "Enter password for Azure Stack OAW's local administrator" -AsSecureString
New-OAW.ps1 -LocalAdministratorPassword $securePassword `
   -AzureStackCertificatePath 'F:\certroot.cer' `
   -DeploymentDataFilePath 'F:\DeploymentData.json' `
   -AzSStampInfoFilePath 'F:\AzureStackStampInformation.json'
```

Vytvoření virtuálního počítače s OAW na HLH s DeploymentData.js:

```powershell
$securePassword = Read-Host -Prompt "Enter password for Azure Stack OAW's local administrator" -AsSecureString
New-OAW.ps1 -LocalAdministratorPassword $securePassword `
   -DeploymentDataFilePath 'D:\AzureStack\DeploymentData.json'
```

Pokud DeploymentData.jsv souboru obsahuje předponu pojmenování pro virtuální počítač OAW, tato hodnota se použije pro parametr **VirtualMachineName** . Jinak je výchozí název **AzSOAW** nebo jakýkoli zadaný název je uživatel.

Pro New-OAW jsou k dispozici dvě sady parametrů. Volitelné parametry jsou uvedeny v závorkách.

```powershell
New-OAW 
-LocalAdministratorPassword <Security.SecureString> `
[-AzureStackCertificatePath <String>] `
[-AzSStampInfoFilePath <String>] `
[-CertificatePassword <Security.SecureString>] `
[-ERCSVMIP <String[]>] `
[-DNS <String[]>] `
[-DeploymentDataFilePath <String>] `
[-SkipNetworkConfiguration] `
[-ImageFilePath <String>] `
[-VirtualMachineName <String>] `
[-VirtualMachineMemory <int64>] `
[-VirtualProcessorCount <int>] `
[-VirtualMachineDiffDiskPath <String>] `
[-PhysicalAdapterMACAddress <String>] `
[-VirtualSwitchName <String>] `
[-ReCreate] `
[-AsJob] `
[-Passthru] `
[-WhatIf] `
[-Confirm] `
[<CommonParameters>]
```

```powershell
New-OAW
-LocalAdministratorPassword <Security.SecureString> `
-IPAddress <String> `
-SubnetMask <String> `
-DefaultGateway <String> `
-DNS <String[]> `
[-AzureStackCertificatePath <String>] `
[-AzSStampInfoFilePath <String>] `
[-CertificatePassword <Security.SecureString>] `
[-ERCSVMIP <String[]>] `
[-ImageFilePath <String>] `
[-VirtualMachineName <String>] `
[-VirtualMachineMemory <int64>] `
[-VirtualProcessorCount <int>] `
[-VirtualMachineDiffDiskPath <String>] `
[-PhysicalAdapterMACAddress <String>] `
[-VirtualSwitchName <String>] `
[-ReCreate] `
[-AsJob] `
[-Passthru] `
[-WhatIf] `
[-Confirm] `
[<CommonParameters>]
```

V následující tabulce je uveden seznam definic pro každý parametr.

| Parametr   | Požadováno/volitelné  | Popis       |
|-------------|--------------------|-------------------|
| LocalAdministratorPassword | Povinné | Heslo pro účet místního správce virtuálního počítače AdminUser. |
| IPAddress                  | Povinné | Statická adresa IPv4 pro konfiguraci protokolu TCP/IP na virtuálním počítači.                                                |
| SubnetMask                 | Povinné | Maska podsítě IPv4 pro konfiguraci protokolu TCP/IP na virtuálním počítači.                                                   |
| DefaultGateway             | Povinné | Adresa IPv4 výchozí brány pro konfiguraci protokolu TCP/IP na virtuálním počítači.                                    |
| DNS                        | Povinné | Servery DNS pro konfiguraci protokolu TCP/IP na virtuálním počítači.                                                          |
| ImageFilePath              | Volitelné | Cesta k souboru OAW. vhdx, kterou poskytla společnost Microsoft. Výchozí hodnota je **OAW. vhdx** ve stejné nadřazené složce tohoto skriptu. |
| VirtualMachineName         | Volitelné | Název, který se má přiřadit k virtuálnímu počítači. Pokud se dá předpona pro pojmenování najít v DeploymentData.jssouboru, použije se jako výchozí název. V opačném případě se jako výchozí název použije **AzSOAW** . Pokud chcete přepsat výchozí hodnotu, můžete zadat jiný název. |
| VirtualMachineMemory       | Volitelné | Paměť, která se má přiřadit k virtuálnímu počítači. Výchozí hodnota je **4 GB**.                            |
| VirtualProcessorCount      | Volitelné | Počet virtuálních procesorů, které mají být přiřazeny k virtuálnímu počítači. Výchozí hodnota je **8**.        |
| VirtualMachineDiffDiskPath | Volitelné | Cesta pro uložení dočasných souborů rozdílového disku, zatímco byl virtuální počítač pro správu aktivní. Výchozí hodnota je **DiffDisks** podadresář ve stejné nadřazené složce tohoto skriptu. |
| AzureStackCertificatePath  | Volitelné | Cesta k certifikátům, které se mají importovat do virtuálního počítače pro přístup ke službě Azure Stack hub |
| AzSStampInfoFilePath       | Volitelné | Cesta AzureStackStampInformation.jsv souboru, kde skript může načíst IP adresy virtuálního počítače ERCS. |
| CertificatePassword        | Volitelné | Heslo certifikátu, který se má importovat do virtuálního počítače pro přístup ke službě Azure Stack hub |
| ERCSVMIP                   | Volitelné | IP Azure Stack adresa ERCS hub virtuálních počítačů, které se mají přidat do seznamu důvěryhodných hostitelů virtuálního počítače. Neprojeví se při nastavení **-SkipNetworkConfiguration** . |
SkipNetworkConfiguration     | Volitelné | Přeskočí konfiguraci sítě pro virtuální počítač, aby se uživatel mohl později nakonfigurovat. |
| DeploymentDataFilePath     | Volitelné | Cesta DeploymentData.js. Neprojeví se při nastavení **-SkipNetworkConfiguration** .            |
| PhysicalAdapterMACAddress  | Volitelné | Adresa MAC síťového adaptéru hostitele, který bude použit k připojení virtuálního počítače k.<br>– Pokud existuje pouze jeden fyzický síťový adaptér, tento parametr není vyžadován a bude použit jediný síťový adaptér.<br>– Pokud existuje více než jeden fyzický síťový adaptér, tento parametr je vyžadován k určení, který z nich použít.<br> |
| VirtualSwitchName          | Volitelné | Název virtuálního přepínače, který je třeba nakonfigurovat v technologii Hyper-V pro virtuální počítač.<br>– Pokud existuje VMSwitch se zadaným názvem, bude tato VMSwitch vybrána.<br>– Pokud neexistuje žádný VMSwitch se zadaným názvem, vytvoří se VMSwitch se zadaným názvem.<br> |
| Znovu vytvořit                   | Volitelné | Odebere a znovu vytvoří virtuální počítač, pokud již existuje virtuální počítač se stejným názvem. |

## <a name="check-the-oaw-vm-version"></a>Podívejte se na verzi virtuálního počítače OAW.

1. Přihlaste se k virtuálnímu počítači s OAW pomocí vašich přihlašovacích údajů.
1. Otevřete PowerShell ISE a spusťte následující skript:

   ```powershell
   'C:\Version\Get-Version.ps1'
   ```

   Například:

   ![Snímek obrazovky s rutinou prostředí PowerShell pro kontrolu verze hostitele životního cyklu životnosti hardwaru](./media/operator-access-workstation/check-operator-access-workstation-vm-version.png)

## <a name="transfer-files-between-the-hlh-and-oaw"></a>Přenos souborů mezi HLH a OAW

Pokud potřebujete přenášet soubory mezi HLH a OAW, vytvořte sdílenou složku SMB pomocí rutiny [New-SmbShare](/powershell/module/smbshare/new-smbshare?view=win10-ps) . New-SmbShare zpřístupňuje složku systému souborů vzdáleným klientům jako sdílenou složku protokolu SMB (Server Message Block). Například:

Pokud chcete odstranit sdílenou složku, kterou vytvořila Tato rutina, použijte rutinu [Remove-SmbShare](/powershell/module/smbshare/remove-smbshare?view=win10-ps) . Například:

## <a name="remove-the-oaw-vm"></a>Odebrání virtuálního počítače s OAW

Následující skript odebere virtuální počítač OAW, který se používá pro přístup k centru Azure Stack pro správu a diagnostiku. Tento skript také odebere diskové soubory a strážce přidružené k virtuálnímu počítači.

1. Přihlaste se k HLH pomocí vašich přihlašovacích údajů.
1. Otevřete relaci PowerShellu se zvýšenými oprávněními. 
1. Přejděte na extrahovaný obsah nainstalovaného OAW.zip souboru.
1. Odeberte virtuální počítač spuštěním skriptu Remove-OAW.ps1: 

   ```powershell
   Remove-OAW.ps1 -VirtualMachineName <name>
   ```

   Kde \<name\> je název virtuálního počítače, který se má odebrat. Ve výchozím nastavení je název **AzSOAW**.

   Například:

   ```powershell
   Remove-OAW.ps1 -VirtualMachineName AzSOAW
   ```

## <a name="next-steps"></a>Další kroky

[Úlohy správy Azure Stack](azure-stack-manage-basics.md)
