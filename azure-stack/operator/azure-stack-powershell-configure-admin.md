---
title: Připojení k centru Azure Stack pomocí PowerShellu
description: Přečtěte si, jak se připojit k centru Azure Stack pomocí PowerShellu.
author: mattbriggs
ms.topic: article
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 09/19/2019
ms.openlocfilehash: 56782d79483f544dc019b4c162ed9af1a1163e4b
ms.sourcegitcommit: cad40ae88212cc72f40c84a1c88143ea0abb65ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84111228"
---
# <a name="connect-to-azure-stack-hub-with-powershell"></a>Připojení k centru Azure Stack pomocí PowerShellu

Centrum Azure Stack můžete nakonfigurovat tak, aby používalo PowerShell ke správě prostředků, jako je vytváření nabídek, plánů, kvót a výstrah. Toto téma vám pomůže nakonfigurovat prostředí operátorů.

## <a name="prerequisites"></a>Požadavky

Pokud jste [připojení k ASDK prostřednictvím sítě VPN](../asdk/asdk-connect.md#connect-with-vpn), spusťte následující předpoklady buď z [Azure Stack Development Kit (ASDK)](../asdk/asdk-connect.md#connect-with-rdp) , nebo z externího klienta založeného na systému Windows.

- Nainstalujte [Azure PowerShell moduly, které jsou kompatibilní s rozbočovačem Azure Stack](azure-stack-powershell-install.md).  
- Stáhněte si [nástroje, které jsou potřeba pro práci s rozbočovačem Azure Stack](azure-stack-powershell-download.md).  

## <a name="connect-with-azure-ad"></a>Připojení ke službě Azure AD

Chcete-li nakonfigurovat prostředí operátora centra Azure Stack pomocí prostředí PowerShell, spusťte jeden z následujících skriptů. Nahraďte Azure Active Directory (Azure AD) tenant a Azure Resource Manager hodnoty koncového bodu vlastní konfigurací prostředí.

[!include[Remove Account](../../includes/remove-account.md)]

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

## <a name="connect-with-ad-fs"></a>Připojení pomocí AD FS

Připojte se k prostředí operátora centra Azure Stack pomocí PowerShellu s Azure Active Directory federované služby (Azure AD FS). Pro ASDK je tento koncový bod Azure Resource Manager nastaven na hodnotu `https://adminmanagement.local.azurestack.external` . Pokud chcete získat Azure Resource Manager koncový bod pro integrované systémy centra Azure Stack, obraťte se na svého poskytovatele služeb.

  ```powershell  
  # Register an Azure Resource Manager environment that targets your Azure Stack Hub instance. Get your Azure Resource Manager endpoint value from your service provider.
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
      -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external

  # Sign in to your environment.
  Login-AzureRmAccount -EnvironmentName "AzureStackAdmin"
  ```

> [!Note]  
> AD FS podporuje pouze interaktivní ověřování s identitami uživatele. Pokud je vyžadován objekt přihlašovacích údajů, je nutné použít instanční objekt (SPN). Další informace o nastavení instančního objektu pomocí centra Azure Stack a AD FS jako služby správy identit najdete v tématu [Správa identity AD FS aplikace](azure-stack-create-service-principals.md#manage-an-ad-fs-app-identity).

## <a name="test-the-connectivity"></a>Testování připojení

Teď, když máte všechno nastavené, můžete pomocí PowerShellu vytvářet prostředky v rámci centra Azure Stack. Můžete například vytvořit skupinu prostředků pro aplikaci a přidat virtuální počítač. Pomocí následujícího příkazu vytvořte skupinu prostředků s názvem **MyResourceGroup**.

```powershell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Další kroky

- [Vytvořte šablony pro centrum Azure Stack](../user/azure-stack-develop-templates.md).
- [Nasaďte šablony pomocí PowerShellu](../user/azure-stack-deploy-template-powershell.md).
  - [Odkaz na modul centra Azure Stack](https://docs.microsoft.com/powershell/azure/azure-stack/overview).
