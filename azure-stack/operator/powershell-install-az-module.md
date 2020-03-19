---
title: Instalace prostředí PowerShell AZ Module pro Azure Stack hub
description: Přečtěte si, jak nainstalovat PowerShell pro centrum Azure Stack.
author: mattbriggs
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/19/2019
ms.openlocfilehash: c3e18ca850bb96791dbaa5d546864a11eb39dc62
ms.sourcegitcommit: 53efd12bf453378b6a4224949b60d6e90003063b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2020
ms.locfileid: "79520482"
---
# <a name="install-powershell-az-module-for-azure-stack-hub"></a>Instalace prostředí PowerShell AZ Module pro Azure Stack hub

Tento článek vysvětluje, jak nainstalovat Azure PowerShell AZ moduls using PowerShellGet. Tyto pokyny jsou určené pro platformy Windows, macOS a Linux.

Pokud chcete nainstalovat modul PowerShell AzureRM pro centrum Azure Stack, přečtěte si téma [Instalace modulu PowerShell AzureRM pro centrum Azure Stack](azure-stack-powershell-install.md).

> [!IMPORTANT]
>  Prostředí PowerShell AZ Module je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

K určení kompatibilních koncových bodů pro poskytovatele prostředků Azure Stack hub je také potřeba použít *profily rozhraní API* .
Profily rozhraní API poskytují způsob, jak spravovat rozdíly mezi verzemi Azure a centra Azure Stack. Profil verze rozhraní API je sada Azure Resource Managerch modulů PowerShellu s konkrétními verzemi rozhraní API. Každá cloudová platforma má sadu podporovaných profilů verze rozhraní API. Například centrum Azure Stack podporuje konkrétní verzi profilu, například **2019-03-01-Hybrid**. Když nainstalujete profil, nainstalují se moduly Azure Resource Manager PowerShellu, které odpovídají zadanému profilu.

Prostředí PowerShellu kompatibilní s centrem Azure Stack AZ moduls můžete nainstalovat ve scénářích připojených k Internetu, částečně propojených nebo odpojených. Tento článek vás provede podrobnými pokyny pro tyto scénáře.

## <a name="1-verify-your-prerequisites"></a>1. ověřte požadavky.

Azure PowerShell funguje s PowerShellem 5.1 nebo novějším ve Windows nebo PowerShellem Core 6.x nebo novějším na všech platformách. Měli byste nainstalovat [nejnovější verzi prostředí PowerShell Core](/powershell/scripting/install/installing-powershell#powershell-core) , která je k dispozici pro váš operační systém. Azure PowerShell nemá žádné další požadavky při spuštění v prostředí PowerShell Core.

Pokud chcete zkontrolovat verzi PowerShellu, spusťte následující příkaz:

```powershell  
$PSVersionTable.PSVersion
```

### <a name="prerequisites-for-windows"></a>Předpoklady pro Windows
Použití Azure PowerShell v prostředí PowerShell 5,1 ve Windows:

1. V případě potřeby proveďte aktualizaci na [Windows PowerShell 5.1](/powershell/scripting/install/installing-windows-powershell#upgrading-existing-windows-powershell). Pokud používáte Windows 10, máte už PowerShell 5.1 nainstalovaný.
2. Nainstalujte si rozhraní [.NET Framework 4.7.2 nebo novější](/dotnet/framework/install).
3. Ujistěte se, že máte nejnovější verzi PowerShellGet. Spusťte `Update-Module PowerShellGet -Force`.

## <a name="2-validate-the-powershell-gallery-accessibility"></a>2. Ověřte přístupnost Galerie prostředí PowerShell.

Ve výchozím nastavení není Galerie prostředí PowerShell pro PowerShellGet nakonfigurovaná jako důvěryhodné úložiště. Při prvním použití PSGallery se zobrazí tato výzva:

```Output
Untrusted repository

You are installing the modules from an untrusted repository. If you trust this repository, change its InstallationPolicy value by running the `Set-PSRepository` cmdlet.

Are you sure you want to install the modules from 'PSGallery'?
[Y] Yes [A] Yes to All [N] No [L] No to All [S] Suspend [?] Help (default is "N"):
```

Pokračujte v instalaci výběrem `Yes` nebo `Yes to All`.

Modul Az je kumulativní modul pro rutiny Azure PowerShellu. Jeho instalace stáhne všechny dostupné moduly Azure Resource Manageru a zpřístupní jejich rutiny k použití.

Nejdřív [nainstalujte PowerShell Core 6. x nebo novější.](/powershell/scripting/install/installing-powershell-core-on-windows)

Pak z relace PowerShellu Core nainstalujte jenom aktuálního uživatele pomocí rutiny AZ Module. Toto je doporučený obor instalace.

```powershell  
Install-Module -Name Az -AllowClobber -Scope CurrentUser
```

Instalace modulu pro všechny uživatele v systému vyžaduje zvýšená oprávnění. Spusťte relaci PowerShellu pomocí příkazu **Spustit jako správce** ve Windows nebo použijte příkaz `sudo` v systému MacOS nebo Linux:

```powershell  
Install-Module -Name Az -AllowClobber -Scope AllUsers
```

## <a name="3-connected-install-with-internet-connectivity"></a>3. připojeno: instalace s připojením k Internetu

Azure Stack AZ Module bude pracovat Azure Stack hub 2002 nebo novější. Navíc Azure Stack AZ Module bude pracovat s prostředím PowerShell 5,1 nebo vyšším na počítači s Windows, nebo PowerShell 6. x nebo vyšší na platformě Linux nebo macOS. Použití rutin PowerShellGet je upřednostňovanou metodou instalace. Tato metoda funguje stejně jako na podporovaných platformách. 

Z relace PowerShellu spusťte následující příkaz:

```powershell  
Install-Module -Name Az -AllowClobber
Install-Module -Name Az.BootStrapper
Use-AzProfile -Profile 2019-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 2.0.0-preview -AllowPrerelease
```

> [!Note]  
> 2\.0.0 pro modul centra Azure Stack je zásadní změna. Podrobnosti najdete [v tématu Migrace z AzureRM na adresu Azure PowerShell AZ in Azure Stack hub](migrate-azurerm-az.md) .

> [!WARNING]  
> Nemůžete mít současně nainstalované moduly AzureRM a AZ pro PowerShell 5,1 pro Windows. Pokud ve svém systému potřebujete zachovat AzureRM, nainstalujte si modul Az pro PowerShell Core 6.x nebo novější. Pokud to chcete provést, [nainstalujte si PowerShell Core 6.x nebo novější](https://docs.microsoft.com/powershell/scripting/install/installing-powershell-core-on-windows) a pak postupujte podle těchto pokynů v terminálu PowerShellu Core.


## <a name="5-disconnected-install-without-internet-connection"></a>5. odpojeno: instalace bez připojení k Internetu

V některých prostředích se nejde připojit ke Galerii prostředí PowerShell. V těchto situacích můžete přesto instalovat offline pomocí jedné z těchto metod:

* Stáhněte moduly do jiného umístění ve vaší síti a použijte je jako zdroj instalace.
  Díky tomu můžete ukládat moduly PowerShellu na jeden server nebo sdílenou složku, která se má nasadit s PowerShellGetem, do všech odpojených systémů. Informace o nastavení místního úložiště a instalaci na odpojených systémech najdete v tématu zaměřeném na [práci s místními úložišti pro PowerShellGet](https://docs.microsoft.com/powershell/scripting/gallery/how-to/working-with-local-psrepositories).
* [Stáhněte si Azure PowerShell MSI](https://docs.microsoft.com/powershell/azure/install-az-ps-msi) do počítače připojeného k síti a potom zkopírujte tento instalační program do systému, který nemá přístup ke Galerii prostředí PowerShell. Nezapomeňte, že instalační program MSI funguje jenom pro PowerShell 5.1 ve Windows.
* Uložte si tento modul pomocí příkazu [Save-Module](https://docs.microsoft.com/powershell/module/PowershellGet/Save-Module) do sdílené složky nebo jiného zdrojového umístění a potom ho ručně zkopírujte do ostatních počítačů:

  ```powershell  
  Save-Module -Name Az -Path '\\server\share\PowerShell\modules' -Force
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

Můžete použít rutiny a ukázky kódu založené na AzureRM. Nicméně budete chtít změnit název modulů a rutin. Názvy modulů se změnily, takže `AzureRM` a Azure se stanou `Az`a jsou stejné pro rutiny. Například `AzureRM.Compute` modul byl přejmenován na `Az.Compute`.` New-AzureRMVM` se změnilo na ` New-AzVM` a `Get-AzureStorageBlob` je teď `Get-AzStorageBlob`.

Důkladnější diskuzi a pokyny pro přesunutí skriptu AzurRM k AZ a průlom Changes in the Azure Stack hub 's hub najdete v tématu [migrace z AzureRM na Azure PowerShell AZ](migrate-azurerm-az.md).

## <a name="next-steps"></a>Další kroky

- [Stažení nástrojů centra Azure Stack z GitHubu](azure-stack-powershell-download.md)
- [Konfigurace prostředí PowerShell uživatele centra Azure Stack](../user/azure-stack-powershell-configure-user.md)
- [Konfigurace prostředí PowerShell pro operátor centra Azure Stack](azure-stack-powershell-configure-admin.md)
- [Správa profilů verzí rozhraní API v centru Azure Stack](../user/azure-stack-version-profiles.md)

