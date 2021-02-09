---
title: Konfigurace po nasazení pro ASDK
description: Přečtěte si o doporučených změnách konfigurace, které se mají provést po instalaci Azure Stack Development Kit (ASDK).
author: PatAltimore
ms.topic: article
ms.date: 12/03/2020
ms.author: patricka
ms.reviewer: misainat
ms.lastreviewed: 12/03/2020
ms.openlocfilehash: 11e79a4d998f012a58c7f3b0ab8ecb928c861851
ms.sourcegitcommit: d542b68b299b73e045f30916afb6018e365e9db6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2021
ms.locfileid: "99975856"
---
# <a name="post-deployment-configurations-for-asdk"></a>Konfigurace po nasazení pro ASDK

Po [instalaci Azure Stack Development Kit (ASDK)](asdk-install.md)byste měli udělat několik doporučených změn konfigurace po nasazení, které se přihlásily jako AzureStack\AzureStackAdmin na hostitelském počítači ASDK.

## <a name="install-azure-stack-powershell"></a>Instalace Azure Stack PowerShellu

Pro práci s Azure Stack jsou vyžadovány Azure PowerShell moduly kompatibilní s Azure Stack.

Příkazy prostředí PowerShell pro Azure Stack jsou nainstalovány prostřednictvím Galerie prostředí PowerShell. Pokud chcete zaregistrovat úložiště PSGallery, otevřete relaci PowerShellu se zvýšenými oprávněními a spusťte následující příkaz:

``` Powershell
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

Pomocí profilů verzí rozhraní API můžete určit Azure Stack kompatibilní moduly AZ.  Profily verzí rozhraní API poskytují způsob, jak spravovat rozdíly ve verzích mezi Azure a Azure Stack. Profil verze rozhraní API je sada AZ PowerShell moduls s konkrétními verzemi rozhraní API. Modul **AZ. zaváděcího nástroje** , který je dostupný prostřednictvím Galerie prostředí PowerShell, poskytuje rutiny prostředí PowerShell, které jsou nutné pro práci s profily verze rozhraní API.

Nejnovější modul Azure Stack PowerShell můžete nainstalovat s nebo bez připojení k Internetu do hostitelského počítače ASDK.

1.  Ověřte požadavky na počítači s Windows. Pokyny najdete v tématu [požadavky pro Windows](../operator/powershell-install-az-module.md#prerequisites-for-windows).
2. Před instalací požadované verze prostředí PowerShell se ujistěte, že jste [odinstalovali všechny existující Azure PowerShell moduly](../operator/powershell-install-az-module.md#3-uninstall-existing-versions-of-the-azure-stack-hub-powershell-modules). 

- **S připojením k Internetu** z hostitelského počítače ASDK: spuštěním následujícího skriptu PowerShellu tyto moduly nainstalujte do instalace ASDK:

### <a name="az-modules"></a>[AZ modules](#tab/az1)

  ```powershell  
    [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12

    Install-Module -Name Az.BootStrapper -Force -AllowPrerelease
    Install-AzProfile -Profile 2019-03-01-hybrid -Force
    Install-Module -Name AzureStack -RequiredVersion 2.0.2-preview -AllowPrerelease

    Get-Module -Name "Az*" -ListAvailable
    Get-Module -Name "Azs*" -ListAvailable
  ```

Pokud je instalace úspěšná, zobrazí se ve výstupu moduly AZ a AzureStack.

### <a name="azurerm-modules"></a>[Moduly AzureRM](#tab/azurerm1)

  ```powershell  
    [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
    
    # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
    Install-Module -Name AzureRM.BootStrapper
    
    # Install and import the API Version Profile required by Azure Stack Hub into the current PowerShell session.
    Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
    Install-Module -Name AzureStack -RequiredVersion 1.8.2
    
    Get-Module -Name "Az*" -ListAvailable
    Get-Module -Name "Azs*" -ListAvailable
  ```

Pokud je instalace úspěšná, zobrazí se ve výstupu moduly AureRM a AzureStack.

---

- **Bez připojení k Internetu** z hostitelského počítače ASDK: v odpojeném scénáři musíte nejdřív stáhnout moduly PowerShellu na počítač, který má připojení k Internetu, a to pomocí následujících příkazů PowerShellu:

### <a name="az-modules"></a>[AZ modules](#tab/az2)

  ```powershell
  $Path = "<Path that is used to save the packages>"

  Save-Package `
    -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name Az -Path $Path -Force -RequiredVersion 2.3.0
  
  Save-Package `
    -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.5.0
  ```

  Potom zkopírujte stažené balíčky do počítače ASDK a zaregistrujte umístění jako výchozí úložiště a nainstalujte moduly AZ a AzureStack z tohoto úložiště:

  ```powershell  
  $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
  $RepoName = "MyNuGetSource"

  Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation -InstallationPolicy Trusted

  Install-Module Az -Repository $RepoName

  Install-Module AzureStack -Repository $RepoName
  ```

### <a name="azurerm-modules"></a>[Moduly AzureRM](#tab/azurerm2)

  ```powershell
  $Path = "<Path that is used to save the packages>"

  Save-Package `
    -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.5.0
  
  Save-Package `
    -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.8.0
  ```

  Potom zkopírujte stažené balíčky do počítače ASDK a zaregistrujte umístění jako výchozí úložiště a nainstalujte moduly AzureRM a AzureStack z tohoto úložiště:

  ```powershell  
  $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
  $RepoName = "MyNuGetSource"

  Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation -InstallationPolicy Trusted

  Install-Module AzureRM -Repository $RepoName

  Install-Module AzureStack -Repository $RepoName
  ```

---

## <a name="download-the-azure-stack-tools"></a>Stažení nástrojů pro Azure Stack

[AzureStack-Tools](https://github.com/Azure/AzureStack-Tools) je úložiště GitHub, které hostuje moduly PowerShellu pro správu a nasazování prostředků do Azure Stack. Nástroje použijete pomocí modulů AZ PowerShell nebo AzureRM.

### <a name="az-modules"></a>[AZ modules](#tab/az3)

Pokud chcete tyto nástroje získat, naklonujte úložiště GitHub z `az` větve nebo Stáhněte složku **AzureStack-Tools** spuštěním následujícího skriptu:

```powershell
# Change directory to the root directory.
cd \

# Download the tools archive.
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/az.zip `
  -OutFile az.zip

# Expand the downloaded files.
expand-archive az.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory.
cd AzureStack-Tools-az

```
### <a name="azurerm-modules"></a>[Moduly AzureRM](#tab/azurerm3)

Pokud chcete tyto nástroje získat, naklonujte úložiště GitHub z `master` větve nebo Stáhněte složku **AzureStack-Tools** spuštěním následujícího skriptu na příkazovém řádku PowerShellu se zvýšenými oprávněními:

```powershell
# Change directory to the root directory.
cd \

# Download the tools archive.
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files.
expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory.
cd AzureStack-Tools-master

```
Další informace o použití modulu AzureRM pro centrum Azure Stack najdete v tématu [Instalace modulu PowerShell AzureRM pro centrum Azure Stack](../operator/azure-stack-powershell-install.md) .

---

## <a name="validate-the-asdk-installation"></a>Ověření instalace ASDK

K zajištění úspěšného nasazení ASDK použijte rutinu Test-AzureStack pomocí následujících kroků:

1. Přihlaste se jako AzureStack\AzureStackAdmin na hostitelském počítači ASDK.
2. Otevřete PowerShell jako správce (ne PowerShell ISE).
3. Spouštěl `Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint`
4. Spouštěl `Test-AzureStack`

Dokončení testů trvá několik minut. Pokud byla instalace úspěšná, výstup vypadá nějak takto:

![Testovací Azure Stack – instalace byla úspěšná.](media/asdk-post-deploy/test-azurestack.png)

Pokud dojde k selhání, získáte nápovědu pomocí kroků pro řešení potíží.

## <a name="enable-multi-tenancy"></a>Povolení víceklientské architektury

Pro nasazení, která používají Azure AD, musíte pro instalaci ASDK [Povolit víceklientské](../operator/azure-stack-enable-multitenancy.md) prostředí.

> [!NOTE]
> Pokud se k přihlášení na portál Azure Stack používá účet správce nebo uživatelské účty z jiných domén, než je ta, která se používá k registraci Azure Stack, musí být název domény, který se používá k registraci Azure Stack, připojený k adrese URL portálu. Pokud je například Azure Stack zaregistrován v fabrikam.onmicrosoft.com a uživatelský účet přihlášení je admin@contoso.com , adresa URL použitá pro přihlášení k portálu User Portal by byla: https \: //Portal.Local.azurestack.external/Fabrikam.onmicrosoft.com.

## <a name="next-steps"></a>Další kroky

[Registrace ASDKu s využitím Azure](asdk-register.md)
