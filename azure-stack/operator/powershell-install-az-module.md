---
title: Instalace prostředí PowerShell AZ Module pro Azure Stack hub
description: Přečtěte si, jak nainstalovat PowerShell pro centrum Azure Stack.
author: mattbriggs
ms.topic: article
ms.date: 04/14/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/14/2020
ms.openlocfilehash: d39bac1a99c2dc7d7a43f211a5fd3e5a7275de33
ms.sourcegitcommit: d930d52e27073829b8bf8ac2d581ec2accfa37e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2020
ms.locfileid: "82174011"
---
# <a name="install-powershell-az-preview-module-for-azure-stack-hub"></a>Instalace prostředí PowerShell AZ Preview Module pro Azure Stack hub

Tento článek vysvětluje, jak nainstalovat Azure PowerShell AZ a Compatible Azure Stack správce centra pomocí PowerShellGet. Moduly AZ lze nainstalovat na platformy Windows, macOS a Linux.

Pokud chcete nainstalovat modul PowerShell AzureRM pro centrum Azure Stack, přečtěte si téma [Instalace modulu PowerShell AzureRM pro centrum Azure Stack](azure-stack-powershell-install.md).

> [!IMPORTANT]
> Prostředí PowerShell AZ Module je aktuálně ve verzi Public Preview.
> Tato verze Preview může mít za následek přerušit změny v nadcházejících vydáních. Další informace najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verze Preview.
> Pravděpodobně nebudete mít k dispozici nové verze modulu AzureRM. Moduly AzureRM jsou v rámci podpory pouze pro kritické opravy. V případě, že budete pokračovat, bude jenom AZ releases for Azurestack

*Profily rozhraní API* můžete použít k určení kompatibilních koncových bodů pro poskytovatele prostředků služby Azure Stack hub.

Profily rozhraní API poskytují způsob, jak spravovat rozdíly mezi verzemi Azure a centra Azure Stack. Profil verze rozhraní API je sada Azure Resource Managerch modulů PowerShellu s konkrétními verzemi rozhraní API. Každá cloudová platforma má sadu podporovaných profilů verze rozhraní API. Například centrum Azure Stack podporuje konkrétní verzi profilu, například **2019-03-01-Hybrid**. Když nainstalujete profil, nainstalují se moduly Azure Resource Manager PowerShellu, které odpovídají zadanému profilu.

Prostředí PowerShellu kompatibilní s centrem Azure Stack AZ moduls můžete nainstalovat ve scénářích připojených k Internetu, částečně propojených nebo odpojených. Tento článek vás provede podrobnými pokyny pro tyto scénáře.

## <a name="1-verify-your-prerequisites"></a>1. ověřte požadavky.

AZ modules se podporuje jenom v [Azure Stackovém centru s aktualizací Update 2002](https://docs.microsoft.com/azure-stack/operator/release-notes?view=azs-2002#2002-build-reference) a nejnovější [opravou hotfix](https://docs.microsoft.com/azure-stack/operator/release-notes?view=azs-2002#hotfixes).

Azure PowerShell funguje s PowerShellem 5.1 nebo novějším ve Windows nebo PowerShellem Core 6.x nebo novějším na všech platformách. Měli byste nainstalovat [nejnovější verzi prostředí PowerShell Core](https://docs.microsoft.com/powershell/scripting/install/installing-powershell#powershell-core) , která je k dispozici pro váš operační systém. Azure PowerShell nemá žádné další požadavky při spuštění v prostředí PowerShell Core.

Pokud chcete zkontrolovat verzi PowerShellu, spusťte následující příkaz:

```powershell  
$PSVersionTable.PSVersion
```

### <a name="prerequisites-for-windows"></a>Předpoklady pro Windows
Použití Azure PowerShell v prostředí PowerShell 5,1 ve Windows:

1. V případě potřeby proveďte aktualizaci na [Windows PowerShell 5.1](https://docs.microsoft.com//powershell/scripting/install/installing-windows-powershell#upgrading-existing-windows-powershell). Pokud používáte Windows 10, máte už PowerShell 5.1 nainstalovaný.
2. Nainstalujte si rozhraní [.NET Framework 4.7.2 nebo novější](https://docs.microsoft.com//dotnet/framework/install).
3. Ujistěte se, že máte nejnovější verzi PowerShellGet. Spusťte `Install-Module PowerShellGet -MinimumVersion 2.2.3 -Force`. 

## <a name="2-prerequisites-for-linux-and-mac"></a>2. předpoklady pro Linux a Mac
Je potřeba PowerShell Core 6. x nebo novější verze. Pokyny najdete na tomto [odkazu](https://docs.microsoft.com//powershell/scripting/install/installing-powershell-core-on-windows) .

## <a name="3-uninstall-existing-versions-of-the-azure-stack-hub-powershell-modules"></a>3. odinstalujte existující verze modulů prostředí PowerShell centra Azure Stack.

Před instalací požadované verze se ujistěte, že jste odinstalovali všechny dříve nainstalované Azure Stack centra AzureRM nebo AZ PowerShell Module. Moduly odinstalujte pomocí jedné z následujících dvou metod:

1. Pokud chcete odinstalovat stávající moduly AzureRM a AZ PowerShell, zavřete všechny aktivní relace PowerShellu a spusťte následující rutiny:

    ```powershell
    Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose -ErrorAction Continue
    Get-Module -Name Azs.* -ListAvailable | Uninstall-Module -Force -Verbose -ErrorAction Continue
    Get-Module -Name Az.* -ListAvailable | Uninstall-Module -Force -Verbose -ErrorAction Continue
    ```
    Pokud se zobrazí chyba, například modul se už používá, zavřete relace PowerShellu, které moduly používají, a znovu spusťte výše uvedený skript.

2. Odstraňte všechny složky `Azure`, které začínají na, `Az` nebo `Azs.` ze složky `C:\Program Files\WindowsPowerShell\Modules` a `C:\Users\{yourusername}\Documents\WindowsPowerShell\Modules` . Odstranění těchto složek odebere všechny existující moduly prostředí PowerShell.

## <a name="4-connected-install-with-internet-connectivity"></a>4. připojeno: instalace s připojením k Internetu

Azure Stack AZ Module bude pracovat Azure Stack hub 2002 nebo novější. Kromě toho Azure Stack AZ Module bude pracovat s prostředím PowerShell 5,1 nebo vyšším na počítači s Windows, nebo PowerShell 6. x nebo vyšší na platformě Linux nebo macOS. Použití rutin PowerShellGet je upřednostňovanou metodou instalace. Tato metoda funguje stejně jako na podporovaných platformách.

Z relace PowerShellu spusťte následující příkaz:

```powershell  
Install-Module -Name Az.BootStrapper -Force -AllowPrerelease
Install-AzProfile -Profile 2019-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 2.0.0-preview -AllowPrerelease
```

> [!Note]  
> 2.0.0 pro modul centra Azure Stack je zásadní změna. Podrobnosti najdete [v tématu Migrace z AzureRM na adresu Azure PowerShell AZ in Azure Stack hub](migrate-azurerm-az.md) .

> [!WARNING]
> Pro PowerShell 5.1 pro Windows nemůžete mít nainstalované moduly AzureRM a Az současně. Pokud ve svém systému potřebujete zachovat AzureRM, nainstalujte si modul Az pro PowerShell Core 6.x nebo novější. Pokud to chcete provést, [nainstalujte si PowerShell Core 6.x nebo novější](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-windows) a pak postupujte podle těchto pokynů v terminálu PowerShellu Core.

## <a name="5-disconnected-install-without-internet-connection"></a>5. odpojeno: instalace bez připojení k Internetu

V odpojeném scénáři nejprve stáhnete moduly PowerShellu do počítače, který má připojení k Internetu. Pak je přenesete do Azure Stack Development Kit (ASDK) pro instalaci.

Přihlaste se k počítači s připojením k Internetu a pomocí následujících skriptů Stáhněte balíčky Azure Resource Manager a Azure Stack centra v závislosti na vaší verzi centra Azure Stack.

Instalace má pět kroků:

1. Instalace prostředí PowerShell centra Azure Stack do připojeného počítače.
2. Povolí další funkce úložiště.
3. Přenos balíčků PowerShellu na odpojenou pracovní stanici.
4. Ručně nabootstrap zprostředkovatele NuGet na odpojené pracovní stanici.
5. Potvrďte instalaci PowerShellu.

### <a name="install-azure-stack-hub-powershell"></a>Nainstalovat Azure Stack centra PowerShell

::: moniker range=">=azs-2002"
Azure Stack hub 2002 nebo novější.

Můžete buď použít AzureRM nebo AZ Preview Module. Moduly RM najdete v pokynech v tématu [install PowerShell AzureRM Module](azure-stack-powershell-install.md).

```powershell

Install-module -Name PowerShellGet -MinimumVersion 2.2.3 -Force
Import-Module -Name PackageManagement -ErrorAction Stop

$Path = "<Path that is used to save the packages>"
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name Az -Path $Path -Force -RequiredVersion 0.10.0-preview
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 2.0.0-preview
```
::: moniker-end

> [!NOTE]  
> V počítačích bez připojení k Internetu doporučujeme pro zakázání shromažďování dat telemetrie spustit následující rutinu. Může dojít ke snížení výkonu rutin bez zakázání shromažďování dat telemetrie. To platí jenom pro počítače bez připojení k Internetu.
> ```powershell
> Disable-AzDataCollection
> ```

### <a name="add-your-packages-to-your-workstation"></a>Přidání balíčků do pracovní stanice

1. Stažené balíčky zkopírujte do zařízení USB.

2. Přihlaste se k odpojené pracovní stanici a zkopírujte balíčky ze zařízení USB do umístění v pracovní stanici.

3. Ručně nabootstrap zprostředkovatele NuGet na odpojené pracovní stanici. Pokyny najdete v tématu [Ruční zavedení zprostředkovatele NuGet na počítači, který není připojený k Internetu](https://docs.microsoft.com/powershell/scripting/gallery/how-to/getting-support/bootstrapping-nuget#manually-bootstrapping-the-nuget-provider-on-a-machine-that-is-not-connected-to-the-internet).

4. Zaregistrujte toto umístění jako výchozí úložiště a nainstalujte AzureRM a `AzureStack` moduly z tohoto úložiště:

   ```powershell
   # requires -Version 5
   # requires -RunAsAdministrator
   # requires -Module PowerShellGet
   # requires -Module PackageManagement

   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation -InstallationPolicy Trusted

   Install-Module -Name Az.BootStrapper -Repository $RepoName -Scope AllUsers -AllowPrerelease

   Set-BootstrapRepo -Repo $RepoName

   Install-AzProfile -Profile '2019-03-01-hybrid' -Force -Scope AllUsers

   Install-Module -Name AzureStack -Repository $RepoName -RequiredVersion 2.0.0-preview -AllowPrerelease -Scope AllUsers
   ```

### <a name="confirm-the-installation-of-powershell"></a>Potvrzení instalace PowerShellu

Potvrďte instalaci spuštěním následujícího příkazu:

```powershell
Get-Module -Name "Az*" -ListAvailable
Get-Module -Name "Azs*" -ListAvailable
```

## <a name="6-configure-powershell-to-use-a-proxy-server"></a>6. konfigurace PowerShellu pro použití proxy server

Ve scénářích, které vyžadují proxy server pro přístup k Internetu, musíte nejprve nakonfigurovat prostředí PowerShell tak, aby používalo existující proxy server:

1. Otevřete příkazový řádek PowerShell se zvýšenými oprávněními.
2. Spusťte následující příkazy:

   ```powershell
   #To use Windows credentials for proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials

   #Alternatively, to prompt for separate credentials that can be used for #proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = Get-Credential
   ```

## <a name="7-use-the-az-module"></a>7. použijte modul AZ Module

Můžete použít rutiny a ukázky kódu založené na AzureRM. Nicméně budete chtít změnit název modulů a rutin. Názvy modulů se změnily tak, že `AzureRM` a Azure se `Az`stanou a jsou stejné pro rutiny. Například `AzureRM.Compute` modul byl přejmenován na `Az.Compute`.` New-AzureRMVM` se změnilo na ` New-AzVM` a `Get-AzureStorageBlob` je teď `Get-AzStorageBlob`.

Důkladnější diskuzi a pokyny pro přesunutí skriptu AzurRM k AZ a průlom Changes in the Azure Stack hub 's hub najdete v tématu [migrace z AzureRM na Azure PowerShell AZ](migrate-azurerm-az.md).

Fragmenty prostředí PowerShell použité v obsahu centra Azure Stack používají modul AzureRM. Modul AZ je ve verzi Preview. Fragmenty kódu, které se mají použít s modulem AZ, můžete Refaktorovat podle pokynů v Průvodci migrací a [migrovat z AzureRM na Azure PowerShell AZ in Azure Stack hub](powershell-install-az-module.md).

## <a name="next-steps"></a>Další kroky

- [Stažení nástrojů centra Azure Stack z GitHubu](azure-stack-powershell-download.md)
- [Konfigurace prostředí PowerShell uživatele centra Azure Stack](../user/azure-stack-powershell-configure-user.md)
- [Konfigurace prostředí PowerShell pro operátor centra Azure Stack](azure-stack-powershell-configure-admin.md)
- [Správa profilů verzí rozhraní API v centru Azure Stack](../user/azure-stack-version-profiles.md)
