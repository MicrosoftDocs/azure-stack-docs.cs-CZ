---
title: Konfigurace po nasazení pro ASDK
description: Přečtěte si o doporučených změnách konfigurace, které se mají provést po instalaci Azure Stack Development Kit (ASDK).
author: justinha
ms.topic: article
ms.date: 10/16/2020
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 10/16/2020
ms.openlocfilehash: 3eb5a9a4f9f52b6e9de5b33233c571da265b5893
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94543540"
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

Nejnovější modul Azure Stack PowerShell můžete nainstalovat s nebo bez připojení k Internetu do hostitelského počítače ASDK:

> [!IMPORTANT]
> Před instalací požadované verze se ujistěte, že jste [odinstalovali všechny existující Azure PowerShell moduly](../operator/powershell-install-az-module.md#3-uninstall-existing-versions-of-the-azure-stack-hub-powershell-modules).

- **S připojením k Internetu** z hostitelského počítače ASDK: spuštěním následujícího skriptu PowerShellu tyto moduly nainstalujte do instalace ASDK:


  ```powershell  
  Get-Module -Name Azs.* -ListAvailable | Uninstall-Module -Force -Verbose
  Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose

  # Install the Az.BootStrapper module. Select Yes when prompted to install NuGet
  Install-Module -Name Az.BootStrapper

  # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
  Use-AzProfile -Profile 2019-03-01-hybrid -Force
  Install-Module -Name AzureStack -RequiredVersion 2.0.2-preview -AllowPrerelease
  ```

  Pokud je instalace úspěšná, zobrazí se ve výstupu moduly AZ a AzureStack.

- **Bez připojení k Internetu** z hostitelského počítače ASDK: v odpojeném scénáři musíte nejdřív stáhnout moduly PowerShellu na počítač, který má připojení k Internetu, a to pomocí následujících příkazů PowerShellu:

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

## <a name="download-the-azure-stack-tools"></a>Stažení nástrojů pro Azure Stack

[AzureStack-Tools](https://github.com/Azure/AzureStack-Tools) je úložiště GitHub, které hostuje moduly PowerShellu pro správu a nasazování prostředků do Azure Stack. Pokud chcete tyto nástroje získat, naklonujte úložiště GitHubu nebo Stáhněte složku AzureStack-Tools-AZ spuštěním následujícího skriptu:

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

Pro nasazení, která používají Azure AD, musíte pro instalaci ASDK [Povolit víceklientské](../operator/azure-stack-enable-multitenancy.md#enable-multi-tenancy) prostředí.

> [!NOTE]
> Pokud se k přihlášení na portál Azure Stack používá účet správce nebo uživatelské účty z jiných domén, než je ta, která se používá k registraci Azure Stack, musí být název domény, který se používá k registraci Azure Stack, připojený k adrese URL portálu. Pokud je například Azure Stack zaregistrován v fabrikam.onmicrosoft.com a uživatelský účet přihlášení je admin@contoso.com , adresa URL použitá pro přihlášení k portálu User Portal by byla: https \: //Portal.Local.azurestack.external/Fabrikam.onmicrosoft.com.

## <a name="next-steps"></a>Další kroky

[Registrace ASDKu s využitím Azure](asdk-register.md)
