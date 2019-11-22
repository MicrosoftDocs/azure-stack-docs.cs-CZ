---
title: Připojení k Azure Stack pomocí PowerShellu | Microsoft Docs
description: Naučte se, jak se připojit k Azure Stack pomocí PowerShellu.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 09/19/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 09/19/2019
ms.openlocfilehash: 988d2e75dfecd499293576bff3d9722e7ff2c96f
ms.sourcegitcommit: cefba8d6a93efaedff303d3c605b02bd28996c5d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/21/2019
ms.locfileid: "74298867"
---
# <a name="connect-to-azure-stack-with-powershell"></a>Připojení k Azure Stack pomocí PowerShellu

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Azure Stack můžete nakonfigurovat tak, aby používaly prostředí PowerShell ke správě prostředků, jako je vytváření nabídek, plánů, kvót a výstrah. Toto téma vám pomůže nakonfigurovat prostředí operátora.

## <a name="prerequisites"></a>Požadavky

Pokud jste [připojení k ASDK prostřednictvím sítě VPN](../asdk/asdk-connect.md#connect-with-vpn), spusťte následující předpoklady buď z [Azure Stack Development Kit (ASDK)](../asdk/asdk-connect.md#connect-with-rdp) , nebo z externího klienta založeného na systému Windows.

- Nainstalujte [moduly Azure Powershellu kompatibilní s Azure Stack](azure-stack-powershell-install.md).  
- Ve službě [Azure Stack development Kit by měl být blobEndpoint](azure-stack-powershell-download.md) .  

## <a name="connect-with-azure-ad"></a>Spojte se s Azure AD

Pokud chcete nakonfigurovat prostředí Azure Stack operator pomocí prostředí PowerShell, spusťte jeden z následujících skriptů. Nahraďte Azure Active Directory (Azure AD) tenant a Azure Resource Manager hodnoty koncového bodu vlastní konfigurací prostředí.

[!include[Remove Account](../../includes/remove-account.md)]

```powershell  
    # Register an Azure Resource Manager environment that targets your Azure Stack instance. Get your Azure Resource Manager endpoint value from your service provider.
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
      -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external

    # Set your tenant name.
    $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackAdmin").ActiveDirectoryAuthority.TrimEnd('/')
    $AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com"
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    # After signing in to your environment, Azure Stack cmdlets
    # can be easily targeted at your Azure Stack instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantId
```

## <a name="connect-with-ad-fs"></a>Spojte se s AD FS

Připojte se k prostředí Azure Stack operátorem pomocí Powershellu s Azure Active Directory Federated Services (Azure AD FS). Pro ASDK je tento koncový bod Azure Resource Manager nastaven na `https://adminmanagement.local.azurestack.external`. Pro získání koncového bodu Azure Resource Manageru pro integrované systémy Azure Stack, obraťte se na svého poskytovatele služeb.

  ```powershell  
  # Register an Azure Resource Manager environment that targets your Azure Stack instance. Get your Azure Resource Manager endpoint value from your service provider.
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
      -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external

  # Sign in to your environment.
  Login-AzureRmAccount -EnvironmentName "AzureStackAdmin"
  ```

> [!Note]  
> AD FS podporuje pouze interaktivní ověřování s identitami uživatele. Pokud je vyžadován objekt přihlašovacích údajů, je nutné použít instanční objekt (SPN). Další informace o nastavení instančního objektu pomocí Azure Stack a AD FS jako služby správy identit najdete v tématu [Správa služby AD FS instančního objektu](azure-stack-create-service-principals.md#manage-an-ad-fs-service-principal).

## <a name="test-the-connectivity"></a>Otestovat připojení

Teď, když máte všechno, co nastavení, pomocí prostředí PowerShell vytvářet prostředky v rámci služby Azure Stack. Můžete například vytvořit skupinu prostředků pro aplikaci a přidat virtuální počítač. Pomocí následujícího příkazu vytvořte skupinu prostředků s názvem **MyResourceGroup**.

```powershell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Další kroky

- [Vývoj šablon pro Azure Stack](../user/azure-stack-develop-templates.md).
- [Nasaďte šablony pomocí PowerShellu](../user/azure-stack-deploy-template-powershell.md).
  - [Odkaz na modul Azure Stack](https://docs.microsoft.com/powershell/azure/azure-stack/overview).
