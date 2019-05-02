---
title: Připojení ke službě Azure Stack pomocí prostředí PowerShell jako uživatel | Dokumentace Microsoftu
description: Kroky pro připojení ke službě Azure Stack powershellu.
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
ms.openlocfilehash: 855d7c03f4a18c4409d36b8ac5fd702c8549e413
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2019
ms.locfileid: "64986251"
---
# <a name="connect-to-azure-stack-with-powershell-as-a-user"></a>Připojení ke službě Azure Stack pomocí prostředí PowerShell jako uživatel

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Můžete připojit ke službě Azure Stack pomocí Powershellu. Musíte se připojit ke správě prostředků Azure Stack pomocí Powershellu. Například můžete použít PowerShell k odběru nabídky, vytvářet virtuální počítače a nasazení šablon Azure Resource Manageru.

Získat nastavení:
  - Ujistěte se, že máte požadavky.
  - Spojte se s Azure Active Directory (Azure AD) nebo služby Active Directory Federation Services (AD FS). 
  - Zaregistrujte poskytovatele prostředků.
  - Test připojení.

## <a name="prerequisites-to-connect-using-powershell"></a>Požadavky a připojte se pomocí Powershellu

Konfigurace z těchto požadavků [sada](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp), nebo z Windows na základě externí klienta máte [připojené prostřednictvím sítě VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn):

* Nainstalujte [moduly Azure Powershellu kompatibilní s Azure Stack](../operator/azure-stack-powershell-install.md).
* Ve službě [Azure Stack development Kit by měl být blobEndpoint](../operator/azure-stack-powershell-download.md) .

Nezapomeňte že nahradit proměnné následujícího skriptu s hodnotami z konfigurace služby Azure Stack:

- **Název tenanta Azure AD**  
  Název tenanta Azure AD slouží ke správě služby Azure Stack, například yourdirectory.onmicrosoft.com.
- **Koncový bod Azure Resource Manageru**  
  Pro Azure Stack development kit, tato hodnota nastavená na https://management.local.azurestack.external. K získání této hodnoty pro integrované systémy Azure Stack, obraťte se na svého poskytovatele služeb.

## <a name="connect-with-azure-ad"></a>Spojte se s Azure AD

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

## <a name="connect-with-ad-fs"></a>Spojte se s AD FS

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

Když máte všechna nastavení, otestujte připojení pomocí prostředí PowerShell k vytváření prostředků v Azure stacku. Jako test vytvořte skupinu prostředků pro aplikaci a přidejte virtuální počítač. Spuštěním následujícího příkazu vytvořte skupinu prostředků s názvem "MyResourceGroup":

```powershell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Další postup

- [Vývoj šablon pro Azure Stack](azure-stack-develop-templates.md)
- [Nasazení šablon pomocí PowerShellu](azure-stack-deploy-template-powershell.md)
- [Odkaz na modul prostředí PowerShell Azure Stack](https://docs.microsoft.com/powershell/azure/azure-stack/overview)
- Pokud chcete nastavit prostředí PowerShell pro operátor prostředí cloud, podívejte se na [konfigurace prostředí PowerShell pro operátory Azure stacku](../operator/azure-stack-powershell-configure-admin.md) článku.
