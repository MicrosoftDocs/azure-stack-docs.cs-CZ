---
title: Konfigurace po nasazení pro Azure Stack Development Kit (ASDK) | Microsoft Docs
description: Popisuje doporučené změny konfigurace, které se mají provést po instalaci Azure Stack Development Kit (ASDK).
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 07/31/2019
ms.openlocfilehash: cbf9872fa75013fdb3e933c102b813924d396a83
ms.sourcegitcommit: bf4d265a3522cbfdd9dd295a0f4ad0daf2ed5eca
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68692100"
---
# <a name="post-asdk-installation-configuration-tasks"></a>Úlohy konfigurace instalace po ASDK

Po [instalaci Azure Stack Development Kit (ASDK)](asdk-install.md)byste měli udělat několik doporučených změn konfigurace po instalaci, když se přihlásíte jako AzureStack\AzureStackAdmin na hostitelském počítači ASDK.

## <a name="install-azure-stack-powershell"></a>Instalace Azure Stack PowerShellu

Pro práci s Azure Stack jsou vyžadovány Azure PowerShell moduly kompatibilní s Azure Stack.

Příkazy prostředí PowerShell pro Azure Stack jsou nainstalovány prostřednictvím Galerie prostředí PowerShell. Pokud chcete zaregistrovat úložiště PSGallery, otevřete relaci PowerShellu se zvýšenými oprávněními a spusťte následující příkaz:

``` Powershell
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

Profily verzí rozhraní API můžete použít k určení Azure Stack kompatibilních AzureRM modulů.  Profily verzí rozhraní API poskytují způsob, jak spravovat rozdíly ve verzích mezi Azure a Azure Stack. Profil verze rozhraní API je sada AzureRM modulů PowerShellu s konkrétními verzemi rozhraní API. Modul **AzureRM. zaváděcího nástroje** , který je k dispozici prostřednictvím Galerie prostředí PowerShell, poskytuje rutiny prostředí PowerShell, které jsou nutné pro práci s profily verze rozhraní API.

Nejnovější modul Azure Stack PowerShell můžete nainstalovat s nebo bez připojení k Internetu do hostitelského počítače ASDK:

> [!IMPORTANT]
> Před instalací požadované verze se ujistěte, že jste odinstalovali [všechny existující Azure PowerShell moduly](../operator/azure-stack-powershell-install.md#3-uninstall-existing-versions-of-the-azure-stack-powershell-modules).

- **S připojením k Internetu** z hostitelského počítače ASDK. Spuštěním následujícího skriptu PowerShellu nainstalujte tyto moduly do instalace sady Development Kit:


  ```powershell  
  Get-Module -Name Azs.* -ListAvailable | Uninstall-Module -Force -Verbose
  Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose

  # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
  Install-Module -Name AzureRM.BootStrapper

  # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
  Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
  Install-Module -Name AzureStack -RequiredVersion 1.7.2
  ```

  Pokud je instalace úspěšná, zobrazí se ve výstupu moduly AzureRM a AzureStack.

- **Bez připojení k Internetu** z hostitelského počítače ASDK. V odpojeném scénáři musíte nejdřív stáhnout moduly PowerShellu na počítač, který má připojení k Internetu, a to pomocí následujících příkazů PowerShellu:

  ```powershell
  $Path = "<Path that is used to save the packages>"

  Save-Package `
    -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
  
  Save-Package `
    -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.5.0
  ```

  Potom zkopírujte stažené balíčky do počítače ASDK a zaregistrujte umístění jako výchozí úložiště a nainstalujte moduly AzureRM a AzureStack z tohoto úložiště:

    ```powershell  
    $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
    $RepoName = "MyNuGetSource"

    Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation -InstallationPolicy Trusted

    Install-Module AzureRM -Repository $RepoName

    Install-Module AzureStack -Repository $RepoName
    ```

## <a name="download-the-azure-stack-tools"></a>Stažení nástrojů pro Azure Stack

[AzureStack-Tools](https://github.com/Azure/AzureStack-Tools) je úložiště GitHub, které hostuje moduly PowerShellu pro správu a nasazování prostředků do Azure Stack. Pokud chcete tyto nástroje získat, naklonujte úložiště GitHubu nebo Stáhněte složku AzureStack-Tools spuštěním následujícího skriptu:

  ```powershell
  # Change directory to the root directory.
  cd \

  # Enforce usage of TLSv1.2 to download the Azure Stack tools archive from GitHub
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
  Invoke-WebRequest `
    -Uri https://github.com/Azure/AzureStack-Tools/archive/master.zip `
    -OutFile master.zip

  # Expand the downloaded files.
  Expand-Archive -Path master.zip -DestinationPath . -Force

  # Change to the tools directory.
  cd AzureStack-Tools-master
  ```

## <a name="validate-the-asdk-installation"></a>Ověření instalace ASDK

Pokud chcete mít jistotu, že vaše nasazení ASDK bylo úspěšné, můžete použít rutinu test-AzureStack pomocí následujících kroků:

1. Přihlaste se jako AzureStack\AzureStackAdmin na hostitelském počítači ASDK.
2. Otevřete PowerShell jako správce (ne PowerShell ISE).
3. Spusťte: `Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint`
4. Spusťte: `Test-AzureStack`

Dokončení testů trvá několik minut. Pokud byla instalace úspěšná, výstup vypadá nějak takto:

![test-azurestack](media/asdk-post-deploy/test-azurestack.png)

Pokud dojde k selhání, získáte nápovědu pomocí kroků pro řešení potíží.

## <a name="enable-multi-tenancy"></a>Povolení víceklientské architektury

Pro nasazení, která používají Azure AD, musíte pro instalaci ASDK [Povolit víceklientské](../operator/azure-stack-enable-multitenancy.md#enable-multi-tenancy) prostředí.

> [!NOTE]
> Pokud se k přihlášení na portál Azure Stack používá účet správce nebo uživatelské účty z jiných domén, než je ta, která se používá k registraci Azure Stack, musí být název domény, který se používá k registraci Azure Stack, připojený k adrese URL portálu. Například pokud je Azure Stack zaregistrován v Fabrikam.onmicrosoft.com a uživatelský účet přihlášení je admin@contoso.com, adresa URL, která se má použít pro přihlášení k portálu User Portal, bude:. https://portal.local.azurestack.external/fabrikam.onmicrosoft.com

## <a name="next-steps"></a>Další postup

[Registrace ASDKu s využitím Azure](asdk-register.md)
