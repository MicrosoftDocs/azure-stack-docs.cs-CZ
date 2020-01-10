---
title: Připojení k Azure Stack centra pomocí PowerShellu jako uživatel | Microsoft Docs
description: Přečtěte si, jak se připojit k centru Azure Stack pomocí PowerShellu.
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
ms.openlocfilehash: 80a9adc5aca5f3a2abc54009d40209e957f78a6f
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75819535"
---
# <a name="connect-to-azure-stack-hub-with-powershell-as-a-user"></a>Připojení k Azure Stack centra pomocí PowerShellu jako uživatel

*Platí pro: Azure Stack integrovaných systémů centra a Azure Stack Development Kit*

Ke správě prostředků Azure Stack centra se můžete připojit ke službě Azure Stack hub pomocí prostředí PowerShell. Můžete například použít PowerShell k přihlášení k odběru nabídek, vytváření virtuálních počítačů a nasazování Azure Resource Manager šablon.

Postup získání instalačního programu:
  - Ujistěte se, že máte požadavky.
  - Spojte se s Azure Active Directory (Azure AD) nebo služby Active Directory Federation Services (AD FS). 
  - Zaregistrujte poskytovatele prostředků.
  - Test připojení.

## <a name="prerequisites-to-connecting-with-powershell"></a>Požadavky na připojení pomocí PowerShellu

Pokud jste [připojení prostřednictvím sítě VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn), nakonfigurujte tyto požadavky z [vývojové sady](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp)nebo z externího klienta se systémem Windows:

* Nainstalujte [Azure PowerShell moduly, které jsou kompatibilní s rozbočovačem Azure Stack](../operator/azure-stack-powershell-install.md).
* Stáhněte si [nástroje, které jsou potřeba pro práci s rozbočovačem Azure Stack](../operator/azure-stack-powershell-download.md).

Ujistěte se, že jste nahradili následující proměnné skriptu hodnotami z vaší konfigurace centra Azure Stack:

- **Název tenanta Azure AD**  
  Název vašeho tenanta Azure AD, který se používá ke správě centra Azure Stack. Například yourdirectory.onmicrosoft.com.
- **Koncový bod Azure Resource Manageru**  
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

Když máte všechno, co máte nastavené, otestujte připojení pomocí PowerShellu k vytváření prostředků v Azure Stack hub. Jako test vytvořte skupinu prostředků pro aplikaci a přidejte virtuální počítač. Spuštěním následujícího příkazu vytvořte skupinu prostředků s názvem "MyResourceGroup":

```powershell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Další kroky

- [Vývoj šablon pro centrum Azure Stack](azure-stack-develop-templates.md)
- [Nasazení šablon pomocí PowerShellu](azure-stack-deploy-template-powershell.md)
- [Odkazy na modul prostředí PowerShell pro Azure Stack hub](https://docs.microsoft.com/powershell/azure/azure-stack/overview)
- Pokud chcete nastavit PowerShell pro prostředí operátora cloudu, přečtěte si článek [Konfigurace prostředí PowerShell pro operátor centra Azure Stack](../operator/azure-stack-powershell-configure-admin.md) .
