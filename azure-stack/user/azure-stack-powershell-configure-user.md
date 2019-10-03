---
title: Připojení ke službě Azure Stack pomocí prostředí PowerShell jako uživatel | Dokumentace Microsoftu
description: Naučte se, jak se připojit k Azure Stack pomocí PowerShellu.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: 6a75eb788afd84b6619326293ae2399d8ed5b0e1
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71824210"
---
# <a name="connect-to-azure-stack-with-powershell-as-a-user"></a>Připojení ke službě Azure Stack pomocí prostředí PowerShell jako uživatel

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Ke správě prostředků Azure Stack se můžete připojit k Azure Stack pomocí PowerShellu. Můžete například použít PowerShell k přihlášení k odběru nabídek, vytváření virtuálních počítačů a nasazování Azure Resource Manager šablon.

Postup získání instalačního programu:
  - Ujistěte se, že máte požadavky.
  - Spojte se s Azure Active Directory (Azure AD) nebo služby Active Directory Federation Services (AD FS). 
  - Zaregistrujte poskytovatele prostředků.
  - Test připojení.

## <a name="prerequisites-to-connecting-with-powershell"></a>Požadavky na připojení pomocí PowerShellu

Pokud jste [připojení prostřednictvím sítě VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn), nakonfigurujte tyto požadavky z [vývojové sady](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp)nebo z externího klienta se systémem Windows:

* Nainstalujte [moduly Azure Powershellu kompatibilní s Azure Stack](../operator/azure-stack-powershell-install.md).
* Ve službě [Azure Stack development Kit by měl být blobEndpoint](../operator/azure-stack-powershell-download.md) .

Nezapomeňte že nahradit proměnné následujícího skriptu s hodnotami z konfigurace služby Azure Stack:

- **Název tenanta Azure AD**  
  Název vašeho tenanta Azure AD, který se používá ke správě Azure Stack. Například yourdirectory.onmicrosoft.com.
- **Koncový bod Azure Resource Manageru**  
  Pro Azure Stack development kit, tato hodnota nastavená na https://management.local.azurestack.external. K získání této hodnoty pro integrované systémy Azure Stack, obraťte se na svého poskytovatele služeb.

## <a name="connect-to-azure-stack-with-azure-ad"></a>Připojení k Azure Stack pomocí Azure AD

```powershell  
    Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.local.azurestack.external"
    # Set your tenant name
    $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackUser").ActiveDirectoryAuthority.TrimEnd('/')
    $AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com"
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    # After signing in to your environment, Azure Stack cmdlets
    # can be easily targeted at your Azure Stack instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackUser" -TenantId $TenantId
```

## <a name="connect-to-azure-stack-with-ad-fs"></a>Připojení k Azure Stack s využitím AD FS

  ```powershell  
  # Register an Azure Resource Manager environment that targets your Azure Stack instance
  Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.local.azurestack.external"

  # Sign in to your environment
  Login-AzureRmAccount -EnvironmentName "AzureStackUser"
  ```

## <a name="register-resource-providers"></a>Zaregistrovat poskytovatele prostředků

Poskytovatelé prostředků nejsou automaticky registrováni pro nové odběry uživatelů, kteří nemají žádné prostředky nasazené prostřednictvím portálu. Můžete explicitně zaregistrovat poskytovatele prostředků spuštěním následujícího skriptu:

```powershell  
foreach($s in (Get-AzureRmSubscription)) {
        Select-AzureRmSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzureRmResourceProvider -ListAvailable | Register-AzureRmResourceProvider
    }
```

## <a name="test-the-connectivity"></a>Otestovat připojení

Když máte všechno, co máte nastavené, otestujte připojení pomocí PowerShellu k vytváření prostředků v Azure Stack. Jako test vytvořte skupinu prostředků pro aplikaci a přidejte virtuální počítač. Spuštěním následujícího příkazu vytvořte skupinu prostředků s názvem "MyResourceGroup":

```powershell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Další postup

- [Vývoj šablon pro Azure Stack](azure-stack-develop-templates.md)
- [Nasazení šablon pomocí PowerShellu](azure-stack-deploy-template-powershell.md)
- [Reference k modulu Azure Stack PowerShellu](https://docs.microsoft.com/powershell/azure/azure-stack/overview)
- Pokud chcete nastavit prostředí PowerShell pro operátor prostředí cloud, podívejte se na [konfigurace prostředí PowerShell pro operátory Azure stacku](../operator/azure-stack-powershell-configure-admin.md) článku.
