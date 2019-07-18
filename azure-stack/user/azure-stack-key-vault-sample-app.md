---
title: Dovolit aplikacím přístup k Azure Stack Key Vault tajných kódů | Microsoft Docs
description: Naučte se, jak spustit ukázkovou aplikaci, která načte klíče a tajné klíče z trezoru klíčů v Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 3748b719-e269-4b48-8d7d-d75a84b0e1e5
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/17/2019
ms.author: sethm
ms.lastreviewed: 04/08/2019
ms.openlocfilehash: fa5a602fbdca32aed635f587fef248bcff0dae06
ms.sourcegitcommit: 2063332b4d7f98ee944dd1f443847eea70eb5614
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2019
ms.locfileid: "68303170"
---
# <a name="allow-apps-to-access-azure-stack-key-vault-secrets"></a>Dovolit aplikacím přístup k Azure Stackovým tajným klíčům Key Vault

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Postupujte podle kroků v tomto článku a spusťte ukázkovou aplikaci **HelloKeyVault** , která načte klíče a tajné kódy z trezoru klíčů v Azure Stack.

## <a name="prerequisites"></a>Požadavky

Pokud se připojujete [prostřednictvím sítě VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn), můžete nainstalovat následující požadavky z [Azure Stack Development Kit](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp)nebo z externího klienta se systémem Windows:

* Nainstalujte [moduly Azure Powershellu kompatibilní s Azure Stack](../operator/azure-stack-powershell-install.md).
* Ve službě [Azure Stack development Kit by měl být blobEndpoint](../operator/azure-stack-powershell-download.md) .

## <a name="create-a-key-vault-and-register-an-app"></a>Vytvoření trezoru klíčů a registrace aplikace

Příprava na ukázkovou aplikaci:

* Vytvořte Trezor klíčů v Azure Stack.
* Registrace aplikace v Azure Active Directory (Azure AD)

Pro přípravu ukázkové aplikace použijte Azure Portal nebo PowerShell.

> [!NOTE]
> Ve výchozím nastavení vytvoří skript prostředí PowerShell novou aplikaci ve službě Active Directory. Můžete ale zaregistrovat jednu ze stávajících aplikací.

Před spuštěním následujícího skriptu se ujistěte, že zadáváte hodnoty pro `aadTenantName` proměnné `applicationPassword` a. Pokud nezadáte hodnotu pro `applicationPassword`, tento skript vygeneruje náhodné heslo.

```powershell
$vaultName           = 'myVault'
$resourceGroupName   = 'myResourceGroup'
$applicationName     = 'myApp'
$location            = 'local'

# Password for the application. If not specified, this script generates a random password during app creation.
$applicationPassword = ''

# Function to generate a random password for the application.
Function GenerateSymmetricKey()
{
    $key = New-Object byte[](32)
    $rng = [System.Security.Cryptography.RNGCryptoServiceProvider]::Create()
    $rng.GetBytes($key)
    return [System.Convert]::ToBase64String($key)
}

Write-Host 'Please log into your Azure Stack user environment' -foregroundcolor Green

$tenantARM = "https://management.local.azurestack.external"
$aadTenantName = "FILL THIS IN WITH YOUR AAD TENANT NAME. FOR EXAMPLE: myazurestack.onmicrosoft.com"

# Configure the Azure Stack operator's PowerShell environment.
Add-AzureRMEnvironment `
  -Name "AzureStackUser" `
  -ArmEndpoint $tenantARM

$TenantID = Get-AzsDirectoryTenantId `
  -AADTenantName $aadTenantName `
  -EnvironmentName AzureStackUser

# Sign in to the user portal.
Add-AzureRmAccount `
  -EnvironmentName "AzureStackUser" `
  -TenantId $TenantID `

$now = [System.DateTime]::Now
$oneYearFromNow = $now.AddYears(1)

$applicationPassword = GenerateSymmetricKey

# Create a new Azure AD application.
$identifierUri = [string]::Format("http://localhost:8080/{0}",[Guid]::NewGuid().ToString("N"))
$homePage = "https://contoso.com"

Write-Host "Creating a new AAD Application"
$ADApp = New-AzureRmADApplication `
  -DisplayName $applicationName `
  -HomePage $homePage `
  -IdentifierUris $identifierUri `
  -StartDate $now `
  -EndDate $oneYearFromNow `
  -Password $applicationPassword

Write-Host "Creating a new AAD service principal"
$servicePrincipal = New-AzureRmADServicePrincipal `
  -ApplicationId $ADApp.ApplicationId

# Create a new resource group and a key vault in that resource group.
New-AzureRmResourceGroup `
  -Name $resourceGroupName `
  -Location $location

Write-Host "Creating vault $vaultName"
$vault = New-AzureRmKeyVault -VaultName $vaultName `
  -ResourceGroupName $resourceGroupName `
  -Sku standard `
  -Location $location

# Specify full privileges to the vault for the application.
Write-Host "Setting access policy"
Set-AzureRmKeyVaultAccessPolicy -VaultName $vaultName `
  -ObjectId $servicePrincipal.Id `
  -PermissionsToKeys all `
  -PermissionsToSecrets all

Write-Host "Paste the following settings into the app.config file for the HelloKeyVault project:"
'<add key="VaultUrl" value="' + $vault.VaultUri + '"/>'
'<add key="AuthClientId" value="' + $servicePrincipal.ApplicationId + '"/>'
'<add key="AuthClientSecret" value="' + $applicationPassword + '"/>'
Write-Host
```

Následující obrázek ukazuje výstup skriptu použitého k vytvoření trezoru klíčů:

![Trezor klíčů s přístupovými klíči](media/azure-stack-key-vault-sample-app/settingsoutput.png)

Poznamenejte si hodnoty **VaultUrl**, **AuthClientId**a **AuthClientSecret** , které vrátil předchozí skript. Tyto hodnoty použijete ke spuštění aplikace **HelloKeyVault** .

## <a name="download-and-configure-the-sample-application"></a>Stažení a konfigurace ukázkové aplikace

Stáhněte si ukázku trezoru klíčů ze stránky [ukázek klientů Azure Key Vault](https://www.microsoft.com/download/details.aspx?id=45343) . Extrahujte obsah souboru. zip na vývojové pracovní stanici. Ve složce Samples jsou dvě aplikace. Tento článek používá **HelloKeyVault**.

Načtení ukázky **HelloKeyVault** :

1. Přejděte do složky **Microsoft. Azure. webtrezor.**  > Samples. Samples**Samples** > **HelloKeyVault** .
2. Otevřete aplikaci **HelloKeyVault** v aplikaci Visual Studio.

### <a name="configure-the-sample-application"></a>Konfigurace ukázkové aplikace

V sadě Visual Studio:

1. Otevřete soubor HelloKeyVault\App.config a vyhledejte `<appSettings>` element.
2. Aktualizujte klíče **VaultUrl**, **AuthClientId**a **AuthClientSecret** hodnotami vracenými při vytváření trezoru klíčů. Ve výchozím nastavení má soubor App. config zástupný symbol pro `AuthCertThumbprint`. Nahraďte tento zástupný `AuthClientSecret`symbol pomocí.

   ![Nastavení aplikace](media/azure-stack-key-vault-sample-app/appconfig.png)

3. Znovu sestavte řešení.

## <a name="run-the-app"></a>Spuštění aplikace

Když spustíte **HelloKeyVault**, aplikace se přihlásí do služby Azure AD a potom pomocí `AuthClientSecret` tokenu ověří pro Trezor klíčů v Azure Stack.

Ukázku **HelloKeyVault** můžete použít k těmto akcím:

* Pomocí klíčů a tajných kódů provádějte základní operace, jako je vytváření, šifrování, zalamování a odstranění.
* Předejte parametry jako `encrypt` a `decrypt` na **HelloKeyVault**a aplikujte zadané změny do trezoru klíčů.

## <a name="next-steps"></a>Další kroky

* [Nasazení virtuálního počítače s heslem Key Vaultu](azure-stack-key-vault-deploy-vm-with-secret.md)
* [Nasazení virtuálního počítače s certifikátem Key Vault](azure-stack-key-vault-push-secret-into-vm.md)
