---
title: Odeslání konfigurace nasazení pro Azure Stack Development Kit (ASDK) | Dokumentace Microsoftu
description: Popisuje změny doporučené konfigurace po instalaci Azure Stack Development Kit (ASDK).
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2019
ms.author: mabrigg
ms.reviewer: misainat
ms.lastreviewed: 10/10/2018
ms.openlocfilehash: 308edbc351b52d94842a1a96602371f6edb8ff5d
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2019
ms.locfileid: "65617523"
---
# <a name="post-asdk-installation-configuration-tasks"></a>Po dokončení instalace ASDK úlohy konfigurace

Po [instalaci Azure Stack Development Kit (ASDK)](asdk-install.md), musí provést několik změn doporučené konfigurace po instalaci Zůstaňte přihlášeni jako AzureStack\AzureStackAdmin na hostitelském počítači ASDK. 

## <a name="install-azure-stack-powershell"></a>Instalace Azure Stack PowerShellu

Moduly prostředí Azure PowerShell kompatibilní služby Azure Stack jsou vyžadována pro práci s Azure Stack.

Příkazy prostředí PowerShell pro Azure Stack jsou nainstalovány v galerii prostředí PowerShell. Registrace PSGallery úložiště, otevřete relaci Powershellu se zvýšenými oprávněními a spusťte následující příkaz:

``` Powershell
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

Profilů verzí API můžete použít k určení kompatibilní moduly AzureRM Azure Stack.  Profilů verzí API poskytují způsob, jak spravovat verze rozdíly mezi Azure a Azure Stack. Profilu verze rozhraní API je sada moduly AzureRM Powershellu s konkrétní verzí rozhraní API. **AzureRM.Bootstrapper** modul, který je dostupný v galerii prostředí PowerShell obsahuje rutiny Powershellu, které jsou nutné k práci pomocí profilů verzí API.

Nejnovější modul Azure Stack Powershellu můžete nainstalovat s nebo bez připojení k Internetu na hostitelském počítači ASDK:

> [!IMPORTANT]
> Než začnete instalovat na požadovanou verzi, ujistěte se, že jste [odinstalujte všechny existující moduly Azure Powershellu](../operator/azure-stack-powershell-install.md#3-uninstall-existing-versions-of-the-azure-stack-powershell-modules).

- **Připojení k Internetu** od ASDK hostitelského počítače. Spusťte následující skript prostředí PowerShell k instalaci těchto modulů na instalaci development kit:

- Pro 1904 byl sestavení nebo novější:

    ```powershell  
      # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
      Install-Module -Name AzureRM.BootStrapper
      
      # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
      Get-AzureRMProfile -Update
      Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
      Install-Module -Name AzureStack -RequiredVersion 1.7.2
    ```

- Azure Stack verze 1903 nebo starší, pouze nainstalovat následující moduly níže:

    ```powershell
    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Install-Module AzureRM -RequiredVersion 2.4.0
    Install-Module -Name AzureStack -RequiredVersion 1.7.1
    ```

    > [!Note]  
    > Verze modulu Azure Stack 1.7.1 je zásadní změnu. Migrace ze služby Azure Stack 1.6.0 najdete [Průvodce migrací](https://aka.ms/azspshmigration171).

  - Azure Stack 1811:

    ``` PowerShell
    # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet. 
    Install-Module -Name AzureRm.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force

    # Install Azure Stack Module Version 1.6.0.
    Install-Module -Name AzureStack -RequiredVersion 1.6.0
    ```

  Pokud je instalace úspěšná, zobrazí se moduly AzureRM a AzureStack ve výstupu.

- **Bez připojení k Internetu** od ASDK hostitelského počítače. Ve scénáři odpojené napřed musíte stáhnout moduly Powershellu k počítači, který má připojení k Internetu pomocí následujících příkazů prostředí PowerShell:

  ```powershell
  $Path = "<Path that is used to save the packages>"

  Save-Package `
    -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
  
  Save-Package `
    -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.5.0
  ```

  V dalším kroku zkopírujte stažených balíčků do počítače ASDK a zaregistrujte se umístění jako výchozí úložiště a nainstalujte moduly AzureRM a AzureStack z tohoto úložiště:

    ```powershell  
    $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
    $RepoName = "MyNuGetSource"

    Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation -InstallationPolicy Trusted

    Install-Module AzureRM -Repository $RepoName

    Install-Module AzureStack -Repository $RepoName
    ```

## <a name="download-the-azure-stack-tools"></a>Stáhněte si nástroje Azure Stack

[Nástroje AzureStack](https://github.com/Azure/AzureStack-Tools) je úložiště GitHub, který je hostitelem moduly Powershellu pro správu a nasazování prostředků do služby Azure Stack. Pokud chcete získat tyto nástroje, naklonujte úložiště GitHub nebo stažení složce AzureStack nástroje spuštěním následujícího skriptu:

  ```powershell
  # Change directory to the root directory.
  cd \

  # Enforce usage of TLSv1.2 to download the Azure Stack tools archive from GitHub
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
  invoke-webrequest `
    https://github.com/Azure/AzureStack-Tools/archive/master.zip `
    -OutFile master.zip

  # Expand the downloaded files.
  expand-archive master.zip -DestinationPath . -Force

  # Change to the tools directory.
  cd AzureStack-Tools-master
  ```

## <a name="validate-the-asdk-installation"></a>Ověření instalace ASDK

Aby bylo zajištěno, že ASDK nasazení bylo úspěšné, můžete použít rutinu Test-AzureStack pomocí následujících kroků:

1. Přihlaste se jako AzureStack\AzureStackAdmin na hostitelském počítači ASDK.
2. Otevřete PowerShell jako správce (ne prostředí PowerShell ISE).
3. Spusťte: `Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint`
4. Spusťte: `Test-AzureStack`

Testy trvat několik minut. Pokud byla instalace úspěšná, zobrazí výstup vypadá podobně jako:

![test-azurestack](media/asdk-post-deploy/test-azurestack.png)

Pokud došlo k chybě, použijte postup řešení potíží zobrazíte nápovědu.

## <a name="reset-the-password-expiration-policy"></a>Resetovat zásady vypršení platnosti hesla 

Pokud chcete mít jistotu, že heslo pro hostitele development kit platnost pasu nevyprší před vám zkušební období skončí, postupujte podle těchto kroků po nasazení ASDK.

### <a name="to-change-the-password-expiration-policy-from-powershell"></a>Změna zásad vypršení platnosti hesla z prostředí Powershell

Z konzole Powershellu se zvýšenými oprávněními spusťte příkaz:

```powershell
Set-ADDefaultDomainPasswordPolicy -MaxPasswordAge 180.00:00:00 -Identity azurestack.local
```

### <a name="to-change-the-password-expiration-policy-manually"></a>Chcete-li ručně změnit zásady vypršení platnosti hesla

1. Na hostiteli development kit, otevřete **Správa zásad skupiny** (pro správu zásad skupiny. Konzoly MMC) a přejděte do **Správa zásad skupiny** - **doménová struktura: azurestack.local** - **domén**  -  **azurestack.local**.
2. Klikněte pravým tlačítkem na **výchozí zásady domény** a klikněte na tlačítko **upravit**.
3. Přejděte v skupiny zásad správy editoru **konfigurace počítače** - **zásady** - **nastavení Windows**  -  **Nastavení zabezpečení** - **zásady účtů** - **zásady pro hesla**.
4. V pravém podokně klikněte dvakrát na **maximální stáří hesla**.
5. V **maximální stáří hesla vlastnosti** dialogovém okně Změnit **heslo vyprší za** hodnota, která se **180**a potom klikněte na tlačítko **OK**.

![Konzola pro správu zásad skupiny](media/asdk-post-deploy/gpmc.png)

## <a name="enable-multi-tenancy"></a>Povolení víceklientské architektury

Pro nasazení s využitím Azure AD, je potřeba [povolení víceklientské architektury](../operator/azure-stack-enable-multitenancy.md#enable-multi-tenancy) ASDK pro vaši instalaci.

> [!NOTE]
> Když správce nebo uživatelské účty z jiných domén, než jaký se používá k registraci Azure Stack se používají pro přihlášení k portálu Azure Stack, použili k registraci ve službě Azure Stack název domény musí být připojeno k portálu pro adresu url. Pokud Azure Stack je zaregistrován s fabrikam.onmicrosoft.com a uživatelský účet přihlášení je například admin@contoso.com, by měla adresa url pro použití k protokolování do portálu user portal: https://portal.local.azurestack.external/fabrikam.onmicrosoft.com.

## <a name="next-steps"></a>Další postup

[Zaregistrujte ASDK v Azure](asdk-register.md)
