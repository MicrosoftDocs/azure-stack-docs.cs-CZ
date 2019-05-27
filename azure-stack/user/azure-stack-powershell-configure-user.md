---
title: Připojení ke službě Azure Stack pomocí prostředí PowerShell jako uživatel | Dokumentace Microsoftu
description: Zjistěte, jak se připojit ke službě Azure Stack pomocí prostředí PowerShell.
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
ms.date: 04/26/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 01/24/2019
ms.openlocfilehash: c9ef9c1e936c71a8b0a2a0eb636da1eac5bf69da
ms.sourcegitcommit: be5382f715a9c1c18c660b630d8fcd823f13aae3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/24/2019
ms.locfileid: "66197327"
---
# <a name="connect-to-azure-stack-with-powershell-as-a-user"></a>Připojení ke službě Azure Stack pomocí prostředí PowerShell jako uživatel

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Můžete připojit ke službě Azure Stack pomocí Powershellu ke správě prostředků Azure Stack. Například můžete použít PowerShell k odběru nabídek, vytvořit virtuální počítače (VM) a nasazení šablon Azure Resource Manageru.

Pokud chcete získat instalační program:
  - Ujistěte se, že máte požadavky.
  - Spojte se s Azure Active Directory (Azure AD) nebo služby Active Directory Federation Services (AD FS). 
  - Zaregistrujte poskytovatele prostředků.
  - Test připojení.

## <a name="prerequisites-to-connecting-with-powershell"></a>Požadavky na připojení pomocí prostředí PowerShell

Konfigurace z těchto požadavků [sada](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp), nebo z Windows na základě externí klienta Pokud jste [připojené prostřednictvím sítě VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn):

* Nainstalujte [moduly Azure Powershellu kompatibilní s Azure Stack](../operator/azure-stack-powershell-install.md).
* Ve službě [Azure Stack development Kit by měl být blobEndpoint](../operator/azure-stack-powershell-download.md) .

Nezapomeňte že nahradit proměnné následujícího skriptu s hodnotami z konfigurace služby Azure Stack:

- **Název tenanta Azure AD**  
  Název tenanta Azure AD slouží ke správě služby Azure Stack. Například yourdirectory.onmicrosoft.com.
- **Koncový bod Azure Resource Manageru**  
  Pro Azure Stack development kit, tato hodnota nastavená na https://management.local.azurestack.external. K získání této hodnoty pro integrované systémy Azure Stack, obraťte se na svého poskytovatele služeb.

## <a name="connect-to-azure-stack-with-azure-ad"></a>Připojení k Azure Stack s Azure AD

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

## <a name="connect-to-azure-stack-with-ad-fs"></a>Připojení k Azure Stack se službou AD FS

  ```powershell  
  # Register an Azure Resource Manager environment that targets your Azure Stack instance
  Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.local.azurestack.external"

  # Sign in to your environment
  Login-AzureRmAccount -EnvironmentName "AzureStackUser"
  ```

## <a name="register-resource-providers"></a>Zaregistrovat poskytovatele prostředků

Poskytovatelé prostředků nejsou registrovány automaticky pro nové předplatné uživatele, kteří nemají žádné prostředky nasazené prostřednictvím portálu. Můžete explicitně zaregistrovat poskytovatele prostředků spuštěním následujícího skriptu:

```powershell  
foreach($s in (Get-AzureRmSubscription)) {
        Select-AzureRmSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzureRmResourceProvider -ListAvailable | Register-AzureRmResourceProvider
    }
```

## <a name="test-the-connectivity"></a>Otestovat připojení

Když máte všechno, co instalační program, testovací připojení pomocí prostředí PowerShell k vytváření prostředků v Azure stacku. Jako test vytvořte skupinu prostředků pro aplikace a přidání virtuálního počítače. Spuštěním následujícího příkazu vytvořte skupinu prostředků s názvem "MyResourceGroup":

```powershell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Další postup

- [Vývoj šablon pro Azure Stack](azure-stack-develop-templates.md)
- [Nasazení šablon pomocí PowerShellu](azure-stack-deploy-template-powershell.md)
- [Odkaz na modul prostředí PowerShell Azure Stack](https://docs.microsoft.com/powershell/azure/azure-stack/overview)
- Pokud chcete nastavit prostředí PowerShell pro operátor prostředí cloud, podívejte se na [konfigurace prostředí PowerShell pro operátory Azure stacku](../operator/azure-stack-powershell-configure-admin.md) článku.
