---
title: Nainstalovat modul PowerShellu AzureRM pro centrum Azure Stack
description: Přečtěte si, jak nainstalovat PowerShell pro centrum Azure Stack. Přečtěte si téma Jak nainstalovat modul PowerShell AzureRM a požadované profily rozhraní API.
author: mattbriggs
ms.topic: article
ms.date: 08/04/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 08/04/2020
ms.openlocfilehash: bbf1a5d296ddbef554a4401e66eab4226ae38dd3
ms.sourcegitcommit: a1e2003fb9c6dacdc76f97614ff5a26a5b197b49
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91623162"
---
# <a name="install-powershell-azurerm-module-for-azure-stack-hub"></a>Nainstalovat modul PowerShellu AzureRM pro centrum Azure Stack

Azure PowerShell AzureRM poskytuje sadu rutin, které používají model Azure Resource Manager ke správě prostředků služby Azure Stack hub.

K určení kompatibilních koncových bodů pro poskytovatele prostředků Azure Stack hub je také potřeba použít *profily rozhraní API* .

Profily rozhraní API poskytují způsob, jak spravovat rozdíly mezi verzemi Azure a centra Azure Stack. Profil verze rozhraní API je sada Azure Resource Managerch modulů PowerShellu s konkrétními verzemi rozhraní API. Každá cloudová platforma má sadu podporovaných profilů verze rozhraní API. Například centrum Azure Stack podporuje konkrétní verzi profilu, například **2019-03-01-Hybrid**. Když nainstalujete profil, nainstalují se moduly Azure Resource Manager PowerShellu, které odpovídají zadanému profilu.

Můžete nainstalovat moduly prostředí PowerShell kompatibilní s centrem Azure Stack ve scénářích připojených k Internetu, částečně propojených nebo odpojených. Tento článek vás provede podrobnými pokyny pro tyto scénáře.

V kontejneru Docker můžete také spustit moduly AzureRM pro Azure Stack hub. Pokyny najdete v tématu [použití Docker ke spuštění PowerShellu pro Azure Stack hub](../user/azure-stack-powershell-user-docker.md).

## <a name="1-verify-your-prerequisites"></a>1. ověřte požadavky.

Než začnete s Azure Stack hub a modulem PowerShell AzureRM, musíte mít následující požadavky:

- **PowerShell verze 5,1** <br>
Pokud chcete zjistit verzi, spusťte **$PSVersionTable. PSVersion** a porovnejte **hlavní** verzi. Pokud nemáte PowerShell 5,1, postupujte podle pokynů pro [instalaci Windows PowerShellu](/powershell/scripting/install/installing-windows-powershell#upgrading-existing-windows-powershell).

  > [!Note]
  > Prostředí PowerShell 5,1 vyžaduje počítač se systémem Windows.

- **Spusťte PowerShell v příkazovém řádku se zvýšenými oprávněními**.

- **Přístup k Galerie prostředí PowerShell** <br>
  Potřebujete přístup k [Galerie prostředí PowerShell](https://www.powershellgallery.com). Galerie je centrální úložiště pro obsah PowerShellu. Modul **PowerShellGet** obsahuje rutiny pro zjišťování, instalaci, aktualizaci a publikování artefaktů prostředí PowerShell. Příklady těchto artefaktů jsou moduly, prostředky DSC, možnosti rolí a skripty z Galerie prostředí PowerShell a dalších privátních úložišť. Pokud používáte PowerShell v odpojeném scénáři, je nutné načíst prostředky z počítače s připojením k Internetu a uložit je do umístění, které je k dispozici pro váš odpojený počítač.

## <a name="2-validate-the-powershell-gallery-accessibility"></a>2. Ověřte přístupnost Galerie prostředí PowerShell.

Ověří, jestli je PSGallery zaregistrovaný jako úložiště.

> [!Note]  
> Tento krok vyžaduje přístup k Internetu.

Otevřete příkazový řádek prostředí PowerShell se zvýšenými oprávněními a spusťte následující rutiny:

```powershell
Install-module -Name PowerShellGet -Force
Import-Module -Name PackageManagement -ErrorAction Stop
Get-PSRepository -Name "PSGallery"
```

Pokud úložiště není zaregistrované, otevřete relaci PowerShellu se zvýšenými oprávněními a spusťte následující příkaz:

```powershell
Register-PSRepository -Default
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

## <a name="3-uninstall-existing-versions-of-the-azure-stack-hub-powershell-modules"></a>3. odinstalujte existující verze modulů prostředí PowerShell centra Azure Stack.

Před instalací požadované verze se ujistěte, že jste odinstalovali všechny dříve nainstalované moduly AzureRM prostředí PowerShell pro Azure Stack hub. Moduly odinstalujte pomocí jedné z následujících dvou metod:

1. Pokud chcete odinstalovat stávající moduly AzureRM a AZ PowerShell, zavřete všechny aktivní relace PowerShellu a spusťte následující rutiny:

    ```powershell
    Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose -ErrorAction Continue
    Get-Module -Name Azs.* -ListAvailable | Uninstall-Module -Force -Verbose -ErrorAction Continue
    Get-Module -Name Az.* -ListAvailable | Uninstall-Module -Force -Verbose -ErrorAction Continue
    ```

    Pokud se zobrazí chyba, například modul se už používá, zavřete relace PowerShellu, které moduly používají, a znovu spusťte výše uvedený skript.

2. Odstraňte všechny složky, které začínají `Azure` na, `Az` nebo `Azs.` ze `C:\Program Files\WindowsPowerShell\Modules` složky a `C:\Users\{yourusername}\Documents\WindowsPowerShell\Modules` . Odstranění těchto složek odebere všechny existující moduly prostředí PowerShell.

## <a name="4-connected-install-powershell-for-azure-stack-hub-with-internet-connectivity"></a>4. připojeno: instalace PowerShellu pro centrum Azure Stack s připojením k Internetu

Profil verze rozhraní API a moduly Azure Stack centra prostředí PowerShell, které požadujete, budou záviset na verzi Azure Stack centra, kterou používáte.

### <a name="install-azure-stack-hub-powershell"></a>Nainstalovat Azure Stack centra PowerShell

Spusťte následující skript PowerShellu, který nainstaluje tyto moduly na svou vývojovou pracovní stanici:

::: moniker range=">=azs-2002"
Pro Azure Stack hub 2002 nebo novější:

Můžete použít buď moduly AzureRm uživatelů, nebo AZ Preview Module. Použití modulů AZ modules vyžaduje Azure Stack hub 2002 a nejnovější opravu hotfix.

Pokud chcete použít moduly AZ Preview, postupujte podle pokynů v tématu [instalace PowerShellu AZ Module](powershell-install-az-module.md).

```powershell  
# Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
Install-Module -Name AzureRM.BootStrapper

# Install and import the API Version Profile required by Azure Stack Hub into the current PowerShell session.
Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.8.2
```

::: moniker-end
::: moniker range="azs-1910"
Pro Azure Stack hub 1910:

```powershell  
# Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
Install-Module -Name AzureRM.BootStrapper

# Install and import the API Version Profile required by Azure Stack Hub into the current PowerShell session.
Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.8.0
```

> [!Note]  
> - 1.8.0 centra verze je zásadní verze změny. Azure Stack Podrobnosti najdete v [poznámkách k verzi](release-notes.md) .

::: moniker-end
::: moniker range="<=azs-1908"
Pro Azure Stack hub 1908 nebo starší:

```powershell  
# Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
Install-Module -Name AzureRM.BootStrapper

# Install and import the API Version Profile required by Azure Stack Hub into the current PowerShell session.
Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.7.2
```

> [!Note]  
> 1.7.2 modul centra Azure Stack pro správu verze je zásadní verzí změny. Pokud chcete provést migraci z centra Azure Stack 1.6.0, přečtěte si [příručku k migraci](https://aka.ms/azspshmigration171).

::: moniker-end

### <a name="confirm-the-installation-of-powershell"></a>Potvrzení instalace PowerShellu

Potvrďte instalaci spuštěním následujícího příkazu:

```powershell
Get-Module -Name "Azure*" -ListAvailable
Get-Module -Name "Azs*" -ListAvailable
```

Pokud je instalace úspěšná, zobrazí se `AzureRm` `AzureStack` ve výstupu moduly a.

## <a name="5-disconnected-install-powershell-without-an-internet-connection"></a>5. odpojeno: instalace PowerShellu bez připojení k Internetu

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

Můžete buď použít AzureRM nebo AZ Preview Module. Informace o AZ modules najdete v tématu pokyny v tématu [install PowerShell AZ Module](powershell-install-az-module.md).

```powershell

Install-module -Name PowerShellGet -Force 
Import-Module -Name PackageManagement -ErrorAction Stop

$Path = "<Path that is used to save the packages>"
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.5.0
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.8.2
```
::: moniker-end

::: moniker range="azs-1910"
Azure Stack hub 1910.

```powershell
Install-module -Name PowerShellGet -Force 
Import-Module -Name PackageManagement -ErrorAction Stop

$Path = "<Path that is used to save the packages>"
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.5.0
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.8.0
```

> [!NOTE]  
> 1.8.0 centra verze je zásadní verze změny. Azure Stack Podrobnosti najdete v [poznámkách k verzi](release-notes.md) .

::: moniker-end
::: moniker range="<=azs-1908"
Pro Azure Stack hub 1908 nebo starší:

```powershell
Install-module -Name PowerShellGet -Force 
Import-Module -Name PackageManagement -ErrorAction Stop

$Path = "<Path that is used to save the packages>"
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.5.0
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.7.2
```

> [!NOTE]  
> 1.7.1 modul centra Azure Stack je zásadní změnou. Pokud chcete provést migraci z centra Azure Stack 1.6.0, přečtěte si [příručku k migraci](https://github.com/Azure/azure-powershell/tree/AzureRM/documentation/migration-guides/Stack).

::: moniker-end

> [!NOTE]  
> V počítačích bez připojení k Internetu doporučujeme pro zakázání shromažďování dat telemetrie spustit následující rutinu. Může dojít ke snížení výkonu rutin bez zakázání shromažďování dat telemetrie. To platí jenom pro počítače bez připojení k Internetu.
> ```powershell
> Disable-AzureRmDataCollection
> ```

### <a name="add-your-packages-to-your-workstation"></a>Přidání balíčků do pracovní stanice

1. Stažené balíčky zkopírujte do zařízení USB.

2. Přihlaste se k odpojené pracovní stanici a zkopírujte balíčky ze zařízení USB do umístění v pracovní stanici.

3. Ručně nabootstrap zprostředkovatele NuGet na odpojené pracovní stanici. Pokyny najdete v tématu [Ruční zavedení zprostředkovatele NuGet na počítači, který není připojený k Internetu](/powershell/scripting/gallery/how-to/getting-support/bootstrapping-nuget#manually-bootstrapping-the-nuget-provider-on-a-machine-that-is-not-connected-to-the-internet).

4. Zaregistrujte toto umístění jako výchozí úložiště a nainstalujte AzureRM a `AzureStack` moduly z tohoto úložiště:

   ```powershell
   # requires -Version 5
   # requires -RunAsAdministrator
   # requires -Module PowerShellGet
   # requires -Module PackageManagement

   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation -InstallationPolicy Trusted

   Install-Module -Name AzureRM -Repository $RepoName

   Install-Module -Name AzureStack -Repository $RepoName
   ```

### <a name="confirm-the-installation-of-powershell"></a>Potvrzení instalace PowerShellu

Potvrďte instalaci spuštěním následujícího příkazu:

```powershell
Get-Module -Name "Azure*" -ListAvailable
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

## <a name="known-issue"></a>Známý problém

###  <a name="method-get_serializationsettings-error"></a>Chyba get_SerializationSettings metody 

- Příčina: moduly PowerShell AZ Module a PowerShell AzureRM nejsou kompatibilní.

    Následující chyba znamená, že moduly AzureRM a AZ modules jsou načteny do stejné relace: 

    ```powershell  
    >  Method 'get_SerializationSettings' in type 'Microsoft.Azure.Management.Internal.Resources.ResourceManagementClient' from assembly 'Microsoft.Azure.Commands.ResourceManager.Common, Version=4.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35' does 
    not have an implementation.
    ```

- Náprava: odinstalujte konfliktní moduly. 

  Chcete-li použít moduly AzureRM, odinstalujte moduly AZ Modules. Nebo odinstalujte AzureRM, chcete-li použít moduly AZ. Zavřete relaci prostředí PowerShell a odinstalujte moduly AZ nebo AzureRM. 
  
  Pokyny najdete v tématu [odinstalace existujících verzí modulů prostředí PowerShell centra Azure Stack](#3-uninstall-existing-versions-of-the-azure-stack-hub-powershell-modules).

## <a name="next-steps"></a>Další kroky

- [Stažení nástrojů centra Azure Stack z GitHubu](azure-stack-powershell-download.md)
- [Konfigurace prostředí PowerShell uživatele centra Azure Stack](../user/azure-stack-powershell-configure-user.md)
- [Konfigurace prostředí PowerShell pro operátor centra Azure Stack](azure-stack-powershell-configure-admin.md)
- [Správa profilů verzí rozhraní API v centru Azure Stack](../user/azure-stack-version-profiles.md)
