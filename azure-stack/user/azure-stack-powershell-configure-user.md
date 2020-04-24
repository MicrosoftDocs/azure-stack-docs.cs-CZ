---
title: Připojení k Azure Stack centra pomocí PowerShellu jako uživatel
description: Přečtěte si, jak se připojit k centru Azure Stack pomocí PowerShellu.
author: mattbriggs
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: 3656a5a6a992788ca8d4d975ac819f69793edb02
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "77702036"
---
# <a name="connect-to-azure-stack-hub-with-powershell-as-a-user"></a>Připojení k Azure Stack centra pomocí PowerShellu jako uživatel

Ke správě prostředků Azure Stack centra se můžete připojit ke službě Azure Stack hub pomocí prostředí PowerShell. Můžete například použít PowerShell k přihlášení k odběru nabídek, vytváření virtuálních počítačů a nasazování Azure Resource Manager šablon.

Postup získání instalačního programu:
  - Ujistěte se, že máte požadavky.
  - Připojte se pomocí Azure Active Directory (Azure AD) nebo Active Directory Federation Services (AD FS) (AD FS). 
  - Registrovat poskytovatele prostředků
  - Otestujte připojení.

## <a name="prerequisites-to-connecting-with-powershell"></a>Požadavky na připojení pomocí PowerShellu

Pokud jste [připojení prostřednictvím sítě VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn), nakonfigurujte tyto požadavky z [vývojové sady](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp)nebo z externího klienta se systémem Windows:

* Nainstalujte [Azure PowerShell moduly, které jsou kompatibilní s rozbočovačem Azure Stack](../operator/azure-stack-powershell-install.md).
* Stáhněte si [nástroje, které jsou potřeba pro práci s rozbočovačem Azure Stack](../operator/azure-stack-powershell-download.md).

Ujistěte se, že jste nahradili následující proměnné skriptu hodnotami z vaší konfigurace centra Azure Stack:

- **Název tenanta Azure AD**  
  Název vašeho tenanta Azure AD, který se používá ke správě centra Azure Stack. Například yourdirectory.onmicrosoft.com.
- **Azure Resource Manager koncový bod**  
  Pro Azure Stack Development Kit je tato hodnota nastavená na https://management.local.azurestack.external. Pokud chcete získat tuto hodnotu pro integrované systémy Azure Stack hub, obraťte se na svého poskytovatele služeb.

## <a name="connect-to-azure-stack-hub-with-azure-ad"></a>Připojení k centru Azure Stack pomocí Azure AD

```powershell  
    Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.local.azurestack.external"
    # Set your tenant name
    $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackUser").ActiveDirectoryAuthority.TrimEnd('/')
    $AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com"
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    # After signing in to your environment, Azure Stack Hub cmdlets
    # can be easily targeted at your Azure Stack Hub instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackUser" -TenantId $TenantId
```

## <a name="connect-to-azure-stack-hub-with-ad-fs"></a>Připojení k centru Azure Stack pomocí AD FS

  ```powershell  
  # Register an Azure Resource Manager environment that targets your Azure Stack Hub instance
  Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.local.azurestack.external"

  # Sign in to your environment
  Login-AzureRmAccount -EnvironmentName "AzureStackUser"
  ```

## <a name="register-resource-providers"></a>Registrovat poskytovatele prostředků

Poskytovatelé prostředků nejsou automaticky registrováni pro nové odběry uživatelů, kteří nemají žádné prostředky nasazené prostřednictvím portálu. Poskytovatele prostředků můžete explicitně zaregistrovat spuštěním následujícího skriptu:

```powershell  
foreach($s in (Get-AzureRmSubscription)) {
        Select-AzureRmSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzureRmResourceProvider -ListAvailable | Register-AzureRmResourceProvider
    }
```

## <a name="test-the-connectivity"></a>Testování připojení

Když máte všechno, co máte nastavené, otestujte připojení pomocí PowerShellu k vytváření prostředků v Azure Stack hub. Jako test vytvořte skupinu prostředků pro aplikaci a přidejte virtuální počítač. Spuštěním následujícího příkazu vytvořte skupinu prostředků s názvem "MyResourceGroup":

```powershell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Další kroky

- [Vývoj šablon pro centrum Azure Stack](azure-stack-develop-templates.md)
- [Nasazení šablon pomocí PowerShellu](azure-stack-deploy-template-powershell.md)
- [Odkazy na modul prostředí PowerShell pro Azure Stack hub](https://docs.microsoft.com/powershell/azure/azure-stack/overview)
- Pokud chcete nastavit PowerShell pro prostředí operátora cloudu, přečtěte si článek [Konfigurace prostředí PowerShell pro operátor centra Azure Stack](../operator/azure-stack-powershell-configure-admin.md) .
