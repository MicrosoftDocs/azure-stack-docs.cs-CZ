---
title: Připojení k centru Azure Stack pomocí PowerShellu
description: Přečtěte si, jak se připojit k centru Azure Stack pomocí PowerShellu.
author: mattbriggs
ms.topic: article
ms.date: 2/1/2021
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 2/1/2021
ms.openlocfilehash: ab38cc4b00da140a529df43ac49789d7eab80327
ms.sourcegitcommit: e88f0a1f2f4ed3bb8442bfb7b754d8b3a51319b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2021
ms.locfileid: "99534177"
---
# <a name="connect-to-azure-stack-hub-with-powershell"></a>Připojení k centru Azure Stack pomocí PowerShellu

Centrum Azure Stack můžete nakonfigurovat tak, aby používalo PowerShell ke správě prostředků, jako je vytváření nabídek, plánů, kvót a výstrah. Toto téma vám pomůže nakonfigurovat prostředí operátorů.

## <a name="prerequisites"></a>Požadavky

Pokud jste [připojení k ASDK prostřednictvím sítě VPN](../asdk/asdk-connect.md#connect-with-vpn), spusťte následující předpoklady buď z [Azure Stack Development Kit (ASDK)](../asdk/asdk-connect.md#connect-with-rdp) , nebo z externího klienta založeného na systému Windows.

- Nainstalujte [Azure PowerShell moduly, které jsou kompatibilní s rozbočovačem Azure Stack](powershell-install-az-module.md).  
- Stáhněte si [nástroje, které jsou potřeba pro práci s rozbočovačem Azure Stack](azure-stack-powershell-download.md).  

## <a name="connect-with-azure-ad"></a>Připojení ke službě Azure AD

Chcete-li nakonfigurovat prostředí operátora centra Azure Stack pomocí prostředí PowerShell, spusťte jeden z následujících skriptů. Nahraďte Azure Active Directory (Azure AD) tenant a Azure Resource Manager hodnoty koncového bodu vlastní konfigurací prostředí.

### <a name="az-modules"></a>[AZ modules](#tab/az1)

[!include[Remove Account](../includes/remove-account-az.md)]

```powershell  
    # Register an Azure Resource Manager environment that targets your Azure Stack Hub instance. Get your Azure Resource Manager endpoint value from your service provider.
    Add-AzEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
      -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external

    # Set your tenant name.
    $AuthEndpoint = (Get-AzEnvironment -Name "AzureStackAdmin").ActiveDirectoryAuthority.TrimEnd('/')
    $AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com"
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    # After signing in to your environment, Azure Stack Hub cmdlets
    # can be easily targeted at your Azure Stack Hub instance.
    Connect-AzAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantId
```
### <a name="azurerm-modules"></a>[Moduly AzureRM](#tab/azurerm1)

[!include[Remove Account](../includes/remove-account-azurerm.md)]

```powershell  
    # Register an Azure Resource Manager environment that targets your Azure Stack Hub instance. Get your Azure Resource Manager endpoint value from your service provider.
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
      -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external

    # Set your tenant name.
    $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackAdmin").ActiveDirectoryAuthority.TrimEnd('/')
    $AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com"
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    # After signing in to your environment, Azure Stack Hub cmdlets
    # can be easily targeted at your Azure Stack Hub instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantId
```

---


## <a name="connect-with-ad-fs"></a>Připojení pomocí AD FS

Připojte se k prostředí operátora centra Azure Stack pomocí PowerShellu s Azure Active Directory federované služby (Azure AD FS). Pro ASDK je tento koncový bod Azure Resource Manager nastaven na hodnotu `https://adminmanagement.local.azurestack.external` . Pokud chcete získat Azure Resource Manager koncový bod pro integrované systémy centra Azure Stack, obraťte se na svého poskytovatele služeb.

### <a name="az-modules"></a>[AZ modules](#tab/az2)

  ```powershell  
  # Register an Azure Resource Manager environment that targets your Azure Stack Hub instance. Get your Azure Resource Manager endpoint value from your service provider.
    Add-AzEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
      -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external

  # Sign in to your environment.
  Connect-AzAccount -EnvironmentName "AzureStackAdmin"
  ```

### <a name="azurerm-modules"></a>[Moduly AzureRM](#tab/azurerm2)

```powershell  
# Register an Azure Resource Manager environment that targets your Azure Stack Hub instance. Get your Azure Resource Manager endpoint value from your service provider.
  Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
    -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
    -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external

# Sign in to your environment.
Login-AzureRmAccount -EnvironmentName "AzureStackAdmin"
```

---

[!Include [AD FS only supports interactive authentication with user identities](../includes/note-powershell-adfs.md)]

## <a name="test-the-connectivity"></a>Testování připojení

Teď, když máte všechno nastavené, můžete pomocí PowerShellu vytvářet prostředky v rámci centra Azure Stack. Můžete například vytvořit skupinu prostředků pro aplikaci a přidat virtuální počítač. Pomocí následujícího příkazu vytvořte skupinu prostředků s názvem **MyResourceGroup**.

### <a name="az-modules"></a>[AZ modules](#tab/az3)
```powershell  
New-AzResourceGroup -Name "MyResourceGroup" -Location "Local"
```

### <a name="azurerm-modules"></a>[Moduly AzureRM](#tab/azurerm3)

```powershell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

---


## <a name="next-steps"></a>Další kroky

- [Použití PowerShellu ke správě předplatných, plánů a nabídek v centru Azure Stack](azure-stack-powershell-plan-offer.md)
- [Vytvořte šablony pro centrum Azure Stack](../user/azure-stack-develop-templates.md).
- [Nasaďte šablony pomocí PowerShellu](../user/azure-stack-deploy-template-powershell.md).
- [Odkaz na modul centra Azure Stack](/powershell/azure/azure-stack/overview).
