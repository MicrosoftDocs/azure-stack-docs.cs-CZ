---
title: Pracovní stanice pro přístup k operátorovi centra Azure Stack
description: Přečtěte si, jak stáhnout a nakonfigurovat pracovní stanici pro přístup k operátorovi centra Azure Stack.
author: mattbriggs
ms.topic: article
ms.date: 03/02/2021
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 03/02/2021
ms.openlocfilehash: 0fb49d981889a11bea857823cb2e614bc222a12e
ms.sourcegitcommit: 2c6418ee465e67edd417961b1f5211b2e09dbd5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102117052"
---
# <a name="azure-stack-hub-operator-access-workstation"></a>Pracovní stanice pro přístup k operátorovi centra Azure Stack

Pracovní stanice pro přístup k operátoru (OAW) se používá k nasazení virtuálního počítače na hostitele životního cyklu centra Azure Stack (HLH) nebo na jakémkoli jiném počítači, na kterém běží Microsoft Hyper-V. Vyžaduje síťové připojení k koncovým bodům centra Azure Stack, které se mají použít pro scénáře operátorů nebo uživatelů.

Virtuální počítač OAW je volitelný virtuální počítač, který není vyžadován funkcí centra Azure Stack. Jeho účelem je poskytnout nejnovější nástroje operátorům nebo uživateli, když komunikují s Azure Stack hub.

## <a name="oaw-scenarios"></a>Scénáře OAW

V následujících tabulkách jsou uvedeny běžné scénáře pro OAW. Připojte se k OAW pomocí vzdálené plochy.

| **Scénář**                                                                                                                                          | **Popis**                                                                                                                                                                                                                                                                                                         |
|-------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| [Přístup k portálu pro správu](https://docs.microsoft.com/azure-stack/operator/azure-stack-manage-portals?view=azs-2008)                    | Provádět operace správy.                                                                                                                                                                                                                                                                                       |
| [Přístup k PEP](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint?view=azs-2008)                                     | Shromažďování a nahrávání protokolů:<br>-[Vytvořte sdílenou SLOŽKU SMB](https://docs.microsoft.com/azure-stack/operator/operator-access-workstation?view=azs-2008#transfer-files-between-the-hlh-and-oaw) na HLH pro přenos souborů z centra Azure Stack.<br>– K nahrání protokolů uložených do sdílené složky SMB použijte Průzkumník služby Azure Storage. |
| [Registrace centra Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-registration?view=azs-2008#renew-or-change-registration) | Pro novou registraci Získejte předchozí název registrace a skupinu prostředků z portálu pro správu.                                                                                                                                                                                                                   |
| [Syndikace Marketplace](https://docs.microsoft.com/azure-stack/operator/azure-stack-download-azure-marketplace-item?view=azs-2008)            | [Vytvořte sdílenou SLOŽKU SMB](https://docs.microsoft.com/azure-stack/operator/operator-access-workstation?view=azs-2008#transfer-files-between-the-hlh-and-oaw) na HLH, ve které se uloží stažený obrázek nebo rozšíření.                                                                                                     |
| [Vytvořit Virtual Machines](https://docs.microsoft.com/azure-stack/user/azure-stack-quick-create-vm-windows-cli?view=azs-2008)                    | Vytváření virtuálních počítačů pomocí rozhraní příkazového řádku                                                                                                                                                                                                                                                                                       |
| [Správa AKS](https://docs.microsoft.com/azure-stack/user/azure-stack-kubernetes-aks-engine-scale?view=azs-2008)                                 | Spravujte clustery AKS, například škálování nebo upgrade.                                                                                                                                                                                                                                                                        |

## <a name="pre-installed-software"></a>Předinstalovaný software

V následující tabulce je uveden předinstalovaný software na virtuálním počítači s OAW.

| **Název softwaru**                                                                                              | **Umístění**                                                         |
|----------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------|
| [Microsoft Edge pro firmy](https://www.microsoft.com/edge/business/)                                        | [Systémová_jednotka] \\ \\ \\ Aplikace Microsoft Edge pro Program Files (x86) \\     |
| [AZ modules](https://docs.microsoft.com/azure-stack/operator/powershell-install-az-module?view=azs-2008) | [Systémová_jednotka] \\ \\WindowsPowerShell \\ moduly              |
| [PowerShell 7](https://devblogs.microsoft.com/powershell/announcing-PowerShell-7-0/)                           | [Systémová_jednotka] \\ Soubory programu \\ PowerShell \\ 7                          |
| [Rozhraní příkazového řádku Azure (CLI)](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest)        | [Systémová_jednotka] \\ Program Files (x86) \\ Microsoft SDK pro \\ Azure \\ CLI2      |
| [Microsoft Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)                     | [Systémová_jednotka] \\ Programové soubory (x86) \\ Průzkumník služby Microsoft Azure Storage |
| [AzCopy](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-v10)                         | [Systémová_jednotka] \\ VMSoftware \\ azcopy_windows_amd64_10.3.4               |
| [AzureStack – nástroje](https://github.com/Azure/AzureStack-Tools/tree/az)                                          | [Systémová_jednotka] \\ VMSoftware \\ AzureStack – nástroje                          |

## <a name="download-files"></a>Stažení souborů

Pokud chcete získat soubory k vytvoření virtuálního počítače s OAW, [Stáhněte si ho tady](https://aka.ms/OAWDownload). Před stažením si přečtěte [prohlášení o ochraně osobních údajů společnosti Microsoft](https://privacy.microsoft.com/privacystatement) a [právní smlouvy](https://docs.microsoft.com/legal/azure-stack-hub/azure-stack-operator-access-workstation-legal-terms) .

Z důvodu bezstavového charakteru řešení nejsou k dispozici žádné aktualizace pro virtuální počítač OAW. Pro každý milník se uvolní nová verze souboru bitové kopie virtuálního počítače. K vytvoření nového virtuálního počítače s OAW použijte nejnovější verzi. Soubor bitové kopie je založený na nejnovější verzi Windows serveru 2019. Po instalaci můžete pomocí web Windows Update použít aktualizace, včetně důležitých aktualizací.

Ověřte hodnotu hash staženého souboru OAW.zip, abyste se ujistili, že se ještě nezměnila před jeho použitím k vytvoření virtuálního počítače s OAW. Spusťte následující skript prostředí PowerShell. Pokud je vrácená hodnota `True` , můžete použít stažený OAW.zip:

> [!NOTE]  
> Odblokovat soubory skriptu po extrakci souboru ke stažení.

```powershell
param( 
    [Parameter(Mandatory=$True)] 
    [ValidateNotNullOrEmpty()] 
    [ValidateScript({Test-Path $_ -PathType Leaf})] 
    [string] 
    $DownloadedOAWZipFilePath 
) 
$expectedHash = '6E412551C7921A6FA4E9D64812A4D3770EB1B1257E2BE55B8426E5EFBCB2C001' 
$actualHash = (Get-FileHash -Path $DownloadedOAWZipFilePath).Hash 
Write-Host "Expected hash: $expectedHash" 
if ($expectedHash -eq $actualHash) 
{ 
    Write-Host 'SUCCESS: OAW.zip file hash matches.' 
} 
else 
{ 
    Write-Error 'ERROR: OAW.zip file hash does not match! It isn't safe to use it, please download it again.' 
    Write-Error "Actual hash: $actualHash" 
} 
```

## <a name="check-hlh-version"></a>Ověřit verzi HLH

> [!NOTE]  
> Tento krok je důležitý, abyste zjistili, jestli jste nasadili OAW na HLH nasazené pomocí Image Microsoftu nebo Image OEM. Pokud OAW nasadíte na Obecné Microsoft Hyper-V, můžete tento krok přeskočit.

1.  Přihlaste se k HLH pomocí svých přihlašovacích údajů.

2.  Otevřete PowerShell ISE a spusťte následující skript:
    ```powershell  
    C:\Version\Get-Version.ps1
    ```

    Například:

    ![Snímek obrazovky s rutinou prostředí PowerShell pro kontrolu verze virtuálního počítače OAW.](media/operator-access-workstation/check-operator-access-workstation-vm-version.png)

> [!NOTE]  
> Tato rutina PowerShellu nemusí být k dispozici ve HLH, která byla nasazena pomocí Image OEM.

## <a name="create-the-oaw-vm-using-a-script"></a>Vytvoření virtuálního počítače s OAW pomocí skriptu

Následující skript připraví virtuální počítač jako pracovní stanici pro přístup k operátoru (OAW), která se používá pro přístup k centru Microsoft Azure Stack.

1.  Přihlaste se k HLH pomocí svých přihlašovacích údajů.

2.  Stáhněte OAW.zip a extrahujte soubory.

3.  Otevřete relaci PowerShellu se zvýšenými oprávněními.

4.  Přejděte na extrahovaný obsah souboru OAW.zip.

5.  Spusťte skript New-OAW.ps1.

### <a name="example-deploy-on-hlh-using-a-microsoft-image"></a>Příklad: nasazení na HLH pomocí Image Microsoft

```powershell  
$securePassword = Read-Host -Prompt "Enter password for Azure Stack OAW's local administrator" -AsSecureString 
New-OAW.ps1 -LocalAdministratorPassword $securePassword 
```


### <a name="example-deploy-on-hlh-using-an-oem-image"></a>Příklad: nasazení na HLH pomocí Image OEM

```powershell  
$securePassword = Read-Host -Prompt "Enter password for Azure Stack OAW's local administrator" -AsSecureString 
New-OAW.ps1 -LocalAdministratorPassword $securePassword `
   -AzureStackCertificatePath 'F:\certroot.cer' `
   -DeploymentDataFilePath 'F:\DeploymentData.json' `
   -AzSStampInfoFilePath 'F:\AzureStackStampInformation.json'
```

Pokud DeploymentData.jsv souboru obsahuje předponu pojmenování pro virtuální počítač OAW, tato hodnota se použije pro `VirtualMachineName` parametr. V opačném případě je výchozí název `AzSOAW` nebo libovolný zadaný název uživatelem.

> [!NOTE]  
> Parametr `AzureStackCertificatePath` by měl být použit pouze v případě, že byl Azure Stack rozbočovač nasazen pomocí certifikátů vydaných certifikační autoritou rozlehlé sítě.

### <a name="example-deploy-on-microsoft-hyper-v"></a>Příklad: nasazení na Microsoft Hyper-V

Počítač, na kterém běží Microsoft Hyper-V, vyžaduje čtyři jádra a čtyři GB dostupné paměti.

```powershell  
$securePassword = Read-Host -Prompt "Enter password for Azure Stack OAW's local administrator" -AsSecureString 
New-OAW.ps1 -LocalAdministratorPassword $securePassword ` 
-AzureStackCertificatePath 'F:\certroot.cer' ` 
-SkipNetworkConfiguration ` 
-VirtualSwitchName Example  
```

> [!NOTE]  
> Parametr `AzureStackCertificatePath` by měl být použit pouze v případě, že byl Azure Stack rozbočovač nasazen pomocí certifikátů vydaných certifikační autoritou rozlehlé sítě. Virtuální počítač OAW bude nasazen bez konfigurace sítě. Můžete nakonfigurovat statickou IP adresu nebo získat IP adresu prostřednictvím protokolu DHCP.

## <a name="user-account-policy"></a>Zásady uživatelského účtu

Na virtuálním počítači OAW se použije následující zásada uživatelského účtu:
 - Předdefinované uživatelské jméno správce: AdminUser
 - MinimumPasswordLength = 14
 - PasswordComplexity je povolené.
 - MinimumPasswordAge = 1 (den)
 - MaximumPasswordAge = 42 (dny)
 - NewGuestName = GUser (ve výchozím nastavení vypnuté)

## <a name="new-oaw-cmdlet-parameters"></a>Parametry rutiny New-OAW

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

| **Parametr**              | **Požadováno/volitelné** | **Popis**                                                                                                                                                                                                                                                                                                                                     |
|----------------------------|-----------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| LocalAdministratorPassword | Vyžadováno              | Heslo pro účet místního správce virtuálního počítače AdminUser.                                                                                                                                                                                                                                                                           |
| IPAddress                  | Vyžadováno              | Statická adresa IPv4 pro konfiguraci protokolu TCP/IP na virtuálním počítači.                                                                                                                                                                                                                                                                                 |
| SubnetMask                 | Vyžadováno              | Maska podsítě IPv4 pro konfiguraci protokolu TCP/IP na virtuálním počítači.                                                                                                                                                                                                                                                                                    |
| DefaultGateway             | Vyžadováno              | Adresa IPv4 výchozí brány pro konfiguraci protokolu TCP/IP na virtuálním počítači.                                                                                                                                                                                                                                                                     |
| DNS                        | Vyžadováno              | Servery DNS pro konfiguraci protokolu TCP/IP na virtuálním počítači.                                                                                                                                                                                                                                                                                           |
| ImageFilePath              | Volitelné              | Cesta k souboru OAW. vhdx, kterou poskytla společnost Microsoft. Výchozí hodnota je **OAW. vhdx** ve stejné nadřazené složce tohoto skriptu.                                                                                                                                                                                                                                  |
| VirtualMachineName         | Volitelné              | Název, který se má přiřadit k virtuálnímu počítači. Pokud se dá předpona pro pojmenování najít v DeploymentData.jssouboru, použije se jako výchozí název. V opačném případě se jako výchozí název použije **AzSOAW**. Pokud chcete přepsat výchozí hodnotu, můžete zadat jiný název.                                                                         |
| VirtualMachineMemory       | Volitelné              | Paměť, která se má přiřadit k virtuálnímu počítači. Výchozí hodnota je **4 GB**.                                                                                                                                                                                                                                                                             |
| VirtualProcessorCount      | Volitelné              | Počet virtuálních procesorů, které mají být přiřazeny k virtuálnímu počítači. Výchozí hodnota je **8**.                                                                                                                                                                                                                                                         |
| VirtualMachineDiffDiskPath | Volitelné              | Cesta pro uložení dočasných souborů rozdílového disku, zatímco byl virtuální počítač pro správu aktivní. Výchozí hodnota je **DiffDisks** podadresář ve stejné nadřazené složce tohoto skriptu.                                                                                                                                                                                |
| AzureStackCertificatePath  | Volitelné              | Cesta k certifikátům, které se mají importovat do virtuálního počítače pro přístup ke službě Azure Stack hub                                                                                                                                                                                                                                                              |
| AzSStampInfoFilePath       | Volitelné              | Cesta AzureStackStampInformation.jsv souboru, kde skript může načíst IP adresy virtuálního počítače ERCS.                                                                                                                                                                                                                                                  |
| CertificatePassword        | Volitelné              | Heslo certifikátu, který se má importovat do virtuálního počítače pro přístup ke službě Azure Stack hub                                                                                                                                                                                                                                                           |
| ERCSVMIP                   | Volitelné              | IP Azure Stack adresa ERCS hub virtuálních počítačů, které se mají přidat do seznamu důvěryhodných hostitelů virtuálního počítače. Neprojeví se při nastavení **-SkipNetworkConfiguration** .                                                                                                                                                                                                |
| SkipNetworkConfiguration   | Volitelné              | Přeskočí konfiguraci sítě pro virtuální počítač, aby se uživatel mohl později nakonfigurovat.                                                                                                                                                                                                                                                                    |
| DeploymentDataFilePath     | Volitelné              | Cesta DeploymentData.js. Neprojeví se při nastavení **-SkipNetworkConfiguration** .                                                                                                                                                                                                                                                             |
| PhysicalAdapterMACAddress  | Volitelné              | Adresa MAC síťového adaptéru hostitele, který bude použit k připojení virtuálního počítače k.<br>– Pokud existuje pouze jeden fyzický síťový adaptér, tento parametr není potřebný a bude použit jediný síťový adaptér.<br>– Pokud existuje více než jeden fyzický síťový adaptér, tento parametr je vyžadován k určení, který z nich použít. |
| VirtualSwitchName          | Volitelné              | Název virtuálního přepínače, který je třeba nakonfigurovat v technologii Hyper-V pro virtuální počítač.<br>– Pokud existuje VMSwitch se zadaným názvem, bude tato VMSwitch vybrána.<br>– Pokud neexistuje žádný VMSwitch se zadaným názvem, vytvoří se VMSwitch se zadaným názvem.                                                            |
| Re-Create                   | Volitelné              | Odstraní a znovu vytvoří virtuální počítač, pokud už existuje virtuální počítač se stejným názvem.                                                                                                                                                                                                                                       |

## <a name="check-the-oaw-vm-version"></a>Podívejte se na verzi virtuálního počítače OAW.

1.  Přihlaste se k virtuálnímu počítači s OAW pomocí vašich přihlašovacích údajů.

2.  Otevřete PowerShell ISE a spusťte následující skript:

    ```powershell  
    C:\\Version\\Get-Version.ps1
    ```

    Například:
    
    ![Snímek obrazovky s rutinou prostředí PowerShell pro kontrolu verze hostitele životního cyklu životnosti hardwaru.](media/operator-access-workstation/check-operator-access-workstation-vm-version.png)

## <a name="transfer-files-between-the-hlh-and-oaw"></a>Přenos souborů mezi HLH a OAW

Pokud potřebujete přenášet soubory mezi HLH a OAW, vytvořte sdílenou složku SMB pomocí rutiny [New-SmbShare](https://docs.microsoft.com/powershell/module/smbshare/new-smbshare?view=win10-ps) .
New-SmbShare zpřístupňuje složku systému souborů vzdáleným klientům jako sdílenou složku protokolu SMB (Server Message Block). Například:

Pokud chcete odstranit sdílenou složku, kterou vytvořila Tato rutina, použijte rutinu [Remove-SmbShare](https://docs.microsoft.com/powershell/module/smbshare/remove-smbshare?view=win10-ps) .

## <a name="remove-the-oaw-vm"></a>Odebrání virtuálního počítače s OAW

Následující skript odebere virtuální počítač OAW, který se používá pro přístup k centru Azure Stack pro správu a diagnostiku. Tento skript také odebere diskové soubory a strážce přidružené k virtuálnímu počítači.

1. Přihlaste se k HLH pomocí vašich přihlašovacích údajů.

2.  Otevřete relaci PowerShellu se zvýšenými oprávněními.

3.  Přejděte na extrahovaný obsah nainstalovaného OAW.zip souboru.

4.  Odeberte virtuální počítač spuštěním skriptu Remove-OAW.ps1:

    ```powershell  
    Remove-OAW.ps1 -VirtualMachineName \<name\>
    ```
    Kde \<name\> je název virtuálního počítače, který se má odebrat. Ve výchozím nastavení je název **AzSOAW**.

    Například:

    ```powershell  
    Remove-OAW.ps1 -VirtualMachineName AzSOAW
    ```

## <a name="next-steps"></a>Další kroky

[Úlohy správy Azure Stack](azure-stack-manage-basics.md)
