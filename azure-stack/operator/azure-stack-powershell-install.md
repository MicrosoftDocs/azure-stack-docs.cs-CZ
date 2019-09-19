---
title: Instalace PowerShellu pro Azure Stack | Microsoft Docs
description: Přečtěte si, jak nainstalovat PowerShell pro Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 09/18/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 09/18/2019
ms.openlocfilehash: 79d7a0adfc7d869f600e864264716b34d6452213
ms.sourcegitcommit: c46d913ebfa4cb6c775c5117ac5c9e87d032a271
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71101128"
---
# <a name="install-powershell-for-azure-stack"></a>Instalace PowerShellu pro Azure Stack

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Azure PowerShell poskytuje sadu rutin, které používají model Azure Resource Manager ke správě Azure Stack prostředků.

Pokud chcete pracovat s cloudem, musíte nainstalovat Azure Stack kompatibilní moduly PowerShellu. Azure Stack používá modul **AzureRM** spíše než novější modul **AzureAZ** používaný v globálním Azure. K určení kompatibilních koncových bodů pro poskytovatele prostředků Azure Stack musíte také použít *profily rozhraní API* .

Profily rozhraní API poskytují způsob, jak spravovat rozdíly mezi verzemi Azure a Azure Stack. Profil verze rozhraní API je sada Azure Resource Managerch modulů PowerShellu s konkrétními verzemi rozhraní API. Každá cloudová platforma má sadu podporovaných profilů verze rozhraní API. Azure Stack například podporuje konkrétní verzi profilu, jako je například **2019-03-01 – Hybrid**. Když nainstalujete profil, nainstalují se moduly Azure Resource Manager PowerShellu, které odpovídají zadanému profilu.

Můžete nainstalovat Azure Stack kompatibilní moduly PowerShellu ve scénářích připojených k Internetu, částečně propojených nebo odpojených scénářích. Tento článek vás provede podrobnými pokyny pro tyto scénáře.

## <a name="1-verify-your-prerequisites"></a>1. Ověření požadavků

Než začnete s Azure Stack a prostředím PowerShell, musíte mít následující požadavky:

- **PowerShell verze 5,0** <br>
Pokud chcete zjistit verzi, spusťte **$PSVersionTable. PSVersion** a porovnejte **hlavní** verzi. Pokud nemáte PowerShell 5,0, postupujte podle pokynů pro [instalaci Windows PowerShellu](https://docs.microsoft.com/powershell/scripting/setup/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell).

  > [!Note]
  > Prostředí PowerShell 5,0 vyžaduje počítač se systémem Windows.

- **Spusťte PowerShell v příkazovém řádku se zvýšenými oprávněními**.

- **Přístup k Galerie prostředí PowerShell** <br>
  Potřebujete přístup k [Galerie prostředí PowerShell](https://www.powershellgallery.com). Galerie je centrální úložiště pro obsah PowerShellu. Modul **PowerShellGet** obsahuje rutiny pro zjišťování, instalaci, aktualizaci a publikování artefaktů prostředí PowerShell. Příklady těchto artefaktů jsou moduly, prostředky DSC, možnosti rolí a skripty z Galerie prostředí PowerShell a dalších privátních úložišť. Pokud používáte PowerShell v odpojeném scénáři, je nutné načíst prostředky z počítače s připojením k Internetu a uložit je do umístění, které je k dispozici pro váš odpojený počítač.

## <a name="2-validate-the-powershell-gallery-accessibility"></a>2. Ověření dostupnosti Galerie prostředí PowerShell

Ověří, jestli je PSGallery zaregistrovaný jako úložiště.

> [!Note]  
> Tento krok vyžaduje přístup k Internetu.

Otevřete příkazový řádek prostředí PowerShell se zvýšenými oprávněními a spusťte následující rutiny:

```powershell
Import-Module -Name PowerShellGet -ErrorAction Stop
Import-Module -Name PackageManagement -ErrorAction Stop
Get-PSRepository -Name "PSGallery"
```

Pokud úložiště není zaregistrované, otevřete relaci PowerShellu se zvýšenými oprávněními a spusťte následující příkaz:

```powershell
Register-PSRepository -Default
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

## <a name="3-uninstall-existing-versions-of-the-azure-stack-powershell-modules"></a>3. Odinstalace stávajících verzí modulů Azure Stack PowerShellu

Před instalací požadované verze se ujistěte, že jste odinstalovali všechny dříve nainstalované Azure Stack moduly PowerShellu pro AzureRM. Moduly odinstalujte pomocí jedné z následujících dvou metod:

1. Pokud chcete odinstalovat stávající moduly PowerShellu AzureRM, zavřete všechny aktivní relace PowerShellu a spusťte následující rutiny:

    ```powershell
    Get-Module -Name Azs.* -ListAvailable | Uninstall-Module -Force -Verbose
    Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose
    ```

    Pokud se zobrazí chyba, například modul se už používá, zavřete relace PowerShellu, které moduly používají, a znovu spusťte výše uvedený skript.

2. Odstraní všechny složky, které `Azure` začínají nebo `Azs.` ze `C:\Program Files\WindowsPowerShell\Modules` složek a `C:\Users\{yourusername}\Documents\WindowsPowerShell\Modules` . Odstranění těchto složek odebere všechny existující moduly prostředí PowerShell.

## <a name="4-connected-install-powershell-for-azure-stack-with-internet-connectivity"></a>4. Připojen Instalace PowerShellu pro Azure Stack s připojením k Internetu

Profil verze rozhraní API a moduly Azure Stack PowerShellu, které požadujete, budou záviset na verzi Azure Stack, kterou používáte.

### <a name="install-azure-stack-powershell"></a>Instalace Azure Stack PowerShellu

Spusťte následující skript PowerShellu, který nainstaluje tyto moduly na svou vývojovou pracovní stanici:

- Pro Azure Stack 1904 nebo novější:

    ```powershell  
    # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
    Install-Module -Name AzureRM.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
    Install-Module -Name AzureStack -RequiredVersion 1.7.2
    ```
  
- Pro Azure Stack verze 1903 nebo starší nainstalujte jenom tyto dva moduly:

    ```powershell  
    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.

    Install-Module -Name AzureRM -RequiredVersion 2.4.0
    Install-Module -Name AzureStack -RequiredVersion 1.7.1
    ```

    > [!Note]  
    > - Verze modulu Azure Stack 1.7.1 je zásadním vydáním změn. Chcete-li provést migraci z Azure Stack 1.6.0, přečtěte si [příručku k migraci](https://aka.ms/azspshmigration171).
    > - Verze modulu AzureRM 2.4.0 obsahuje zásadní změnu rutiny Remove-AzureRmStorageAccount. Tato rutina `-Force` očekává zadání parametru pro odebrání účtu úložiště bez potvrzení.
    > - Nemusíte instalovat **AzureRM. zaváděcí nástroj** pro instalaci modulů pro Azure Stack verze 1901 nebo novější.
    > - Kromě používání výše uvedených Azure Stack modulů AzureRM verze 1901 nebo novější neinstalujte profil 2018-03-01-Hybrid.

### <a name="confirm-the-installation-of-powershell"></a>Potvrzení instalace PowerShellu

Potvrďte instalaci spuštěním následujícího příkazu:

```powershell
Get-Module -Name "Azure*" -ListAvailable
Get-Module -Name "Azs*" -ListAvailable
```

Pokud je instalace úspěšná, zobrazí se ve výstupu moduly AzureRM a AzureStack.

## <a name="5-disconnected-install-powershell-without-an-internet-connection"></a>5. Propojení Instalace PowerShellu bez připojení k Internetu

V odpojeném scénáři nejprve stáhnete moduly PowerShellu do počítače, který má připojení k Internetu. Pak je přenesete do Azure Stack Development Kit (ASDK) pro instalaci.

Přihlaste se k počítači s připojením k Internetu a pomocí následujících skriptů Stáhněte balíčky Azure Resource Manager a Azure Stack v závislosti na vaší verzi Azure Stack.

Instalace má čtyři kroky:

1. Instalace Azure Stack PowerShellu do připojeného počítače.
2. Povolí další funkce úložiště.
3. Přenos balíčků PowerShellu na odpojenou pracovní stanici.
4. Ručně nabootstrap zprostředkovatele NuGet na odpojené pracovní stanici.
5. Potvrďte instalaci PowerShellu.

### <a name="install-azure-stack-powershell"></a>Instalace Azure Stack PowerShellu

- Azure Stack 1904 nebo novější.

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.5.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.7.2
    ```

- Azure Stack 1903 nebo starší.

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.4.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.7.1
    ```

    > [!Note]  
    > 1\.7.1 modul Azure Stack verze je zásadní změna. Pokud chcete provést migraci z AzureStack 1.6.0, přečtěte si příručku k [migraci](https://github.com/Azure/azure-powershell/tree/AzureRM/documentation/migration-guides/Stack).

    > [!NOTE]
    > V počítačích bez připojení k Internetu doporučujeme pro zakázání shromažďování dat telemetrie spustit následující rutinu. Může dojít ke snížení výkonu rutin bez zakázání shromažďování dat telemetrie. To platí jenom pro počítače bez připojení k Internetu.
    > ```powershell
    > Disable-AzureRmDataCollection
    > ```

### <a name="add-your-packages-to-your-workstation"></a>Přidání balíčků do pracovní stanice

1. Stažené balíčky zkopírujte do zařízení USB.

2. Přihlaste se k odpojené pracovní stanici a zkopírujte balíčky ze zařízení USB do umístění v pracovní stanici.

3. Ručně nabootstrap zprostředkovatele NuGet na odpojené pracovní stanici. Pokyny najdete v tématu [Ruční zavedení zprostředkovatele NuGet na počítači, který není připojený k Internetu](https://docs.microsoft.com/powershell/gallery/how-to/getting-support/bootstrapping-nuget#manually-bootstrapping-the-nuget-provider-on-a-machine-that-is-not-connected-to-the-internet).

4. Zaregistrujte toto umístění jako výchozí úložiště a nainstalujte moduly AzureRM a AzureStack z tohoto úložiště:

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

## <a name="6-configure-powershell-to-use-a-proxy-server"></a>6. Konfigurace PowerShellu pro použití proxy server

Ve scénářích, které vyžadují proxy server pro přístup k Internetu, musíte nejprve nakonfigurovat prostředí PowerShell tak, aby používalo existující proxy server:

1. Otevřete příkazový řádek PowerShell se zvýšenými oprávněními.
2. Spusťte následující příkazy:

   ```powershell
   #To use Windows credentials for proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials

   #Alternatively, to prompt for separate credentials that can be used for #proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = Get-Credential
   ```

## <a name="next-steps"></a>Další postup

- [Stažení Azure Stack nástrojů z GitHubu](azure-stack-powershell-download.md)
- [Konfigurace prostředí PowerShellu Azure Stackho uživatele](../user/azure-stack-powershell-configure-user.md)
- [Konfigurace prostředí PowerShell pro Azure Stack operátor](azure-stack-powershell-configure-admin.md)
- [Správa profilů verzí API ve službě Azure Stack](../user/azure-stack-version-profiles.md)
