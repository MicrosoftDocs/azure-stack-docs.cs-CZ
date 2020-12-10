---
title: Instalace prostředí PowerShell AZ Module pro Azure Stack hub
description: Přečtěte si, jak nainstalovat PowerShell pro centrum Azure Stack.
author: mattbriggs
ms.topic: article
ms.date: 12/2/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: 91ddbc5aeb9c10b49b21db331e6e7c71a8a15764
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96935198"
---
# <a name="install-powershell-az-module-for-azure-stack-hub"></a>Instalace prostředí PowerShell AZ Module pro Azure Stack hub

Tento článek vysvětluje, jak nainstalovat Azure PowerShell AZ a Compatible Azure Stack správce centra pomocí PowerShellGet. Moduly AZ lze nainstalovat na platformy Windows, macOS a Linux.

Můžete také spustit příkaz AZ modules for Azure Stack hub v kontejneru Docker. Pokyny najdete v tématu [použití Docker ke spuštění PowerShellu pro Azure Stack hub](../user/azure-stack-powershell-user-docker.md).

Pokud chcete nainstalovat modul AzureRM (PowerShell Resource modules) pro centrum Azure Stack, přečtěte si téma [Instalace modulu PowerShell AzureRM pro centrum Azure Stack](azure-stack-powershell-install.md).

> [!IMPORTANT]
> Pravděpodobně nebudete mít k dispozici nové verze modulů prostředků Azure. Moduly modulů prostředků Azure jsou v rámci podpory jenom pro kritické opravy. Až se vám pošle dál, bude se používat jenom AZ releases for Azure Stack hub.

*Profily rozhraní API* můžete použít k určení kompatibilních koncových bodů pro poskytovatele prostředků služby Azure Stack hub.

Profily rozhraní API poskytují způsob, jak spravovat rozdíly mezi verzemi Azure a centra Azure Stack. Profil verze rozhraní API je sada Azure Resource Managerch modulů PowerShellu s konkrétními verzemi rozhraní API. Každá cloudová platforma má sadu podporovaných profilů verze rozhraní API. Například centrum Azure Stack podporuje konkrétní verzi profilu, například **2019-03-01-Hybrid**. Když nainstalujete profil, nainstalují se moduly Azure Resource Manager PowerShellu, které odpovídají zadanému profilu.

Prostředí PowerShellu kompatibilní s centrem Azure Stack AZ moduls můžete nainstalovat ve scénářích připojených k Internetu, částečně propojených nebo odpojených. Tento článek vás provede podrobnými pokyny pro tyto scénáře.

## <a name="1-verify-your-prerequisites"></a>1. ověřte požadavky.

AZ modules se podporuje v Azure Stackovém centru s aktualizací Update 2002 nebo novějším a s nainstalovanými aktuálními opravami hotfix. Další informace najdete v [poznámkách k verzi centra Azure Stack](release-notes.md) .

Azure PowerShell AZ modules Work s prostředím PowerShell 5,1 nebo vyšším ve Windows nebo PowerShell Core 6. x a novějším na všech platformách. Měli byste nainstalovat [nejnovější verzi prostředí PowerShell Core](/powershell/scripting/install/installing-powershell#powershell-core) , která je k dispozici pro váš operační systém. Azure PowerShell nemá žádné další požadavky při spuštění v prostředí PowerShell Core.

Pokud chcete zkontrolovat verzi PowerShellu, spusťte následující příkaz:

```powershell  
$PSVersionTable.PSVersion
```

### <a name="prerequisites-for-windows"></a>Předpoklady pro Windows
Použití Azure PowerShellu v PowerShellu 5.1 ve Windows:

1. V případě potřeby proveďte aktualizaci na [Windows PowerShell 5.1](/powershell/scripting/windows-powershell/install/installing-windows-powershell#upgrading-existing-windows-powershell). Pokud používáte Windows 10, máte už PowerShell 5.1 nainstalovaný.
2. Nainstalujte si rozhraní [.NET Framework 4.7.2 nebo novější](/dotnet/framework/install).
3. Ujistěte se, že máte nejnovější verzi modulu PowerShellGet. Spusťte `Install-Module PowerShellGet -MinimumVersion 2.2.3 -Force`. 

## <a name="2-prerequisites-for-linux-and-mac"></a>2. předpoklady pro Linux a Mac
Je potřeba PowerShell Core 6. x nebo novější verze. Pokyny najdete na tomto [odkazu](/powershell/scripting/install/installing-powershell-core-on-windows) .

## <a name="3-uninstall-existing-versions-of-the-azure-stack-hub-powershell-modules"></a>3. odinstalujte existující verze modulů prostředí PowerShell centra Azure Stack.

Před instalací požadované verze se ujistěte, že jste odinstalovali všechny dříve nainstalované Azure Stack centra Azure Resource Manager nebo AZ PowerShell Module. Moduly odinstalujte pomocí jedné z následujících dvou metod:

1. Pokud chcete odinstalovat existující Azure Resource Manager a AZ PowerShell modules, zavřete všechny aktivní relace PowerShellu a spusťte následující rutiny:

    ```powershell
    Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose -ErrorAction Continue
    Get-Module -Name Azs.* -ListAvailable | Uninstall-Module -Force -Verbose -ErrorAction Continue
    Get-Module -Name Az.* -ListAvailable | Uninstall-Module -Force -Verbose -ErrorAction Continue
    ```
    Pokud se zobrazí chyba, například modul se už používá, zavřete relace PowerShellu, které moduly používají, a znovu spusťte výše uvedený skript.

2. Odstraňte všechny složky, které začínají `Azure` na, `Az` nebo `Azs.` ze `C:\Program Files\WindowsPowerShell\Modules` složky a `C:\Users\{yourusername}\Documents\WindowsPowerShell\Modules` . Odstranění těchto složek odebere všechny existující moduly prostředí PowerShell.

## <a name="4-connected-install-with-internet-connectivity"></a>4. připojeno: instalace s připojením k Internetu

Azure Stack AZ Module bude pracovat Azure Stack hub 2002 nebo novější. Kromě toho Azure Stack AZ Module bude pracovat s prostředím PowerShell 5,1 nebo vyšším na počítači s Windows, nebo PowerShell 6. x nebo vyšší na platformě Linux nebo macOS. Upřednostňovanou metodou instalace je použití rutin PowerShellGet. Tato metoda funguje stejně jako na podporovaných platformách.

Z relace PowerShellu spusťte následující příkaz:

```powershell  
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12

Install-Module -Name Az.BootStrapper -Force -AllowPrerelease
Install-AzProfile -Profile 2019-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 2.0.2-preview -AllowPrerelease
```

> [!Note]  
> 2.0.0 pro modul centra Azure Stack je zásadní změna. Podrobnosti najdete [v tématu Migrace z AzureRM na adresu Azure PowerShell AZ in Azure Stack hub](migrate-azurerm-az.md) .

> [!WARNING]
> V prostředí PowerShell 5,1 pro Windows nemůžete současně nainstalovat současně Azure Resource Manager (AzureRM) a AZ Module. Pokud potřebujete zachovat Azure Resource Manager k dispozici v systému, nainstalujte modul AZ Module pro PowerShell Core 6. x nebo novější. Pokud to chcete provést, [nainstalujte PowerShell Core 6. x nebo novější](/powershell/scripting/install/installing-powershell-core-on-windows) a pak postupujte podle těchto pokynů v terminálu PowerShell Core.

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

Můžete buď použít Azure Resource Manager, nebo AZ Modules. Azure Resource Manager najdete v pokynech v tématu [install PowerShell AzureRM Module](powershell-install-az-module.md). Následující kód ukládá moduly z důvěryhodného online úložiště https://www.powershellgallery.com/ .

```powershell
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12

Install-module -Name PowerShellGet -MinimumVersion 2.2.3 -Force
Import-Module -Name PackageManagement -ErrorAction Stop

$savedModulesPath = "<Path that is used to save the packages>"
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name Az -Path $savedModulesPath -Force -RequiredVersion 0.10.0-preview
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $savedModulesPath -Force -RequiredVersion 2.0.2-preview
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

3. Ručně nabootstrap zprostředkovatele NuGet na odpojené pracovní stanici. Pokyny najdete v tématu [Ruční zavedení zprostředkovatele NuGet na počítači, který není připojený k Internetu](/powershell/scripting/gallery/how-to/getting-support/bootstrapping-nuget#manually-bootstrapping-the-nuget-provider-on-a-machine-that-is-not-connected-to-the-internet).

4. Zaregistrujte toto umístění jako výchozí úložiště a nainstalujte `AzureRM` `AzureStack` moduly a z tohoto úložiště:

   ```powershell
   # requires -Version 5
   # requires -RunAsAdministrator
   # requires -Module PowerShellGet
   # requires -Module PackageManagement

   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12

   Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation -InstallationPolicy Trusted

   Install-Module -Name AzureStack -Repository $RepoName -RequiredVersion 2.0.2-preview -AllowPrerelease -Scope AllUsers

   Install-Module -Name Az -Repository $RepoName -RequiredVersion 0.10.0-preview -AllowPrerelease -Scope AllUsers
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

Můžete použít rutiny a ukázky kódu založené na Azure Resource Manager. Nicméně budete chtít změnit název modulů a rutin. Názvy modulů se změnily tak, že `AzureRM` a Azure se stanou a `Az` jsou stejné pro rutiny. Například `AzureRM.Compute` modul byl přejmenován na `Az.Compute` .` New-AzureRMVM` se změnilo na ` New-AzVM` a `Get-AzureStorageBlob` je teď `Get-AzStorageBlob`.

Důkladnější diskuzi a pokyny pro přesunutí skriptu AzurRM k AZ a průlom Changes in the Azure Stack hub 's hub najdete v tématu [migrace z AzureRM na Azure PowerShell AZ](migrate-azurerm-az.md).

## <a name="next-steps"></a>Další kroky

- [Stažení nástrojů centra Azure Stack z GitHubu](azure-stack-powershell-download.md)
- [Konfigurace prostředí PowerShell uživatele centra Azure Stack](../user/azure-stack-powershell-configure-user.md)
- [Konfigurace prostředí PowerShell pro operátor centra Azure Stack](azure-stack-powershell-configure-admin.md)
- [Správa profilů verzí rozhraní API v centru Azure Stack](../user/azure-stack-version-profiles.md)
