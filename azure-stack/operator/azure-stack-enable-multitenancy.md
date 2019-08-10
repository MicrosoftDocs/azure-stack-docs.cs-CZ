---
title: Víceklientská architektura v Azure Stack
description: Naučte se, jak podporovat více adresářů Azure Active Directory v Azure Stack
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2019
ms.author: patricka
ms.reviewer: bryanr
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: 3b06945eefcdb8cb51ffea2e9c4d626d538f3202
ms.sourcegitcommit: 94669fe8a55fadd3103e80be307e9e8c823bf746
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2019
ms.locfileid: "68940240"
---
# <a name="multi-tenancy-in-azure-stack"></a>Víceklientská architektura v Azure Stack

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Azure Stack můžete nakonfigurovat tak, aby podporovaly uživatele z více tenantů Azure Active Directory (Azure AD), aby mohli používat služby v Azure Stack. Zvažte například následující scénář:

- Jste správcem služby contoso.onmicrosoft.com, kde je nainstalovaný Azure Stack.
- Marie je správcem adresáře fabrikam.onmicrosoft.com, kde se nacházejí uživatelé typu Host.
- Společnost Marie obdrží z vaší společnosti služby IaaS a PaaS a musí uživatelům dovolit, aby se přihlásili a používali Azure Stack prostředky v contoso.onmicrosoft.com.

Tato příručka popisuje požadované kroky v souvislosti s tímto scénářem ke konfiguraci víceklientské architektury v Azure Stack. V tomto scénáři je nutné provést kroky, aby se uživatelé ze společnosti Fabrikam mohli přihlašovat a využívat služby z nasazení Azure Stack ve společnosti Contoso.  

## <a name="enable-multi-tenancy"></a>Povolení víceklientské architektury

Před konfigurací víceklientské architektury v Azure Stack existuje několik požadavků, které je potřeba zohlednit:
  
 - A Marie musí koordinovat postup správy v rámci adresáře Azure Stack je nainstalován v (Contoso) a v adresáři hosta (Fabrikam).  
 - Ujistěte se, že jste [nainstalovali](azure-stack-powershell-install.md) a nakonfigurovali PowerShell pro Azure Stack. [](azure-stack-powershell-configure-admin.md)
 - [Stáhněte si nástroje Azure Stack](azure-stack-powershell-download.md)a importujte moduly připojení a identita:

    ```powershell  
    Import-Module .\Connect\AzureStack.Connect.psm1
    Import-Module .\Identity\AzureStack.Identity.psm1
    ```

### <a name="configure-azure-stack-directory"></a>Konfigurace Azure Stack Directory

V této části nakonfigurujete Azure Stack pro povolení přihlášení z tenantů klienta adresáře služby Azure AD společnosti Fabrikam.

Připojte klienta adresáře hosta (Fabrikam) k Azure Stack konfigurací Azure Resource Manager pro příjem uživatelů a instančních objektů z tenanta hostovaného adresáře.

Správce služby contoso.onmicrosoft.com spustí následující příkazy.

```powershell  
## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
$adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

## Replace the value below with the Azure Stack directory
$azureStackDirectoryTenant = "contoso.onmicrosoft.com"

## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantToBeOnboarded = "fabrikam.onmicrosoft.com"

## Replace the value below with the name of the resource group in which the directory tenant registration resource should be created (resource group must already exist).
$ResourceGroupName = "system.local"

## Replace the value below with the region location of the resource group. 
$location = "local"

# Subscription Name
$SubscriptionName = "Default Provider Subscription"

Register-AzSGuestDirectoryTenant -AdminResourceManagerEndpoint $adminARMEndpoint `
 -DirectoryTenantName $azureStackDirectoryTenant `
 -GuestDirectoryTenantName $guestDirectoryTenantToBeOnboarded `
 -Location $location `
 -ResourceGroupName $ResourceGroupName `
 -SubscriptionName $SubscriptionName
```

### <a name="configure-guest-directory"></a>Konfigurace adresáře hosta

Jakmile správce nebo operátor Azure Stack povolí, aby se adresář Fabrikam používal v Azure Stack, Marie musí zaregistrovat Azure Stack u klienta adresáře společnosti Fabrikam.

#### <a name="registering-azure-stack-with-the-guest-directory"></a>Registrace Azure Stack s adresářem hosta

Marie Správce adresáře společnosti Fabrikam spustí v adresáři hosta fabrikam.onmicrosoft.com následující příkazy.

```powershell
## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
$tenantARMEndpoint = "https://management.local.azurestack.external"
    
## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantName = "fabrikam.onmicrosoft.com"

Register-AzSWithMyDirectoryTenant `
 -TenantResourceManagerEndpoint $tenantARMEndpoint `
 -DirectoryTenantName $guestDirectoryTenantName `
 -Verbose 
```

> [!IMPORTANT]
> Pokud správce Azure Stack nainstaluje v budoucnu nové služby nebo aktualizace, bude pravděpodobně nutné spustit tento skript znovu.
>
> Spusťte tento skript kdykoli znovu, abyste zkontrolovali stav aplikací Azure Stack ve vašem adresáři.
>
> Pokud jste si všimli potíží s vytvářením virtuálních počítačů v Managed Disks (představených v aktualizaci 1808), přidal se nový **poskytovatel prostředků disku** , který vyžaduje, aby se tento skript spouštěl znovu.

### <a name="direct-users-to-sign-in"></a>Přímé přihlašování uživatelů

Teď, když jste vy a Marie dokončili kroky k zařazení adresáře Marie, může uživatel s názvem Marie nasměrovat uživatele společnosti Fabrikam, aby se přihlásili.  Uživatelé společnosti Fabrikam (to znamená, že uživatelé s příponou Fabrikam.onmicrosoft.com) se přihlásí\:na webu https//Portal.Local.azurestack.external.  

Marie bude směrovat jakékoli [cizí objekty zabezpečení](/azure/role-based-access-control/rbac-and-directory-admin-roles) v adresáři Fabrikam (to znamená uživatele v adresáři Fabrikam bez přípony Fabrikam.onmicrosoft.com) pro přihlášení pomocí protokolu HTTPS\://Portal.Local.azurestack.external/ fabrikam.onmicrosoft.com.  Pokud tuto adresu URL nepoužívají, odešlou se jejich výchozímu adresáři (Fabrikam) a zobrazí se chyba oznamující, že správce nesouhlasí.

## <a name="disable-multi-tenancy"></a>Zakázat víceklientské architektury

Pokud již nechcete, aby se v Azure Stack více tenantů, můžete zakázat víceklientské prostředí provedením následujících kroků v uvedeném pořadí:

1. Jako správce adresáře hosta (Marie v tomto scénáři) spusťte příkaz *zrušit registraci – AzsWithMyDirectoryTenant*. Rutina odinstaluje všechny aplikace Azure Stack z nového adresáře.

    ``` PowerShell
    ## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
    $tenantARMEndpoint = "https://management.local.azurestack.external"
        
    ## Replace the value below with the guest tenant directory. 
    $guestDirectoryTenantName = "fabrikam.onmicrosoft.com"
    
    Unregister-AzsWithMyDirectoryTenant `
     -TenantResourceManagerEndpoint $tenantARMEndpoint `
     -DirectoryTenantName $guestDirectoryTenantName `
     -Verbose 
    ```

2. Jako správce služby Azure Stack (v tomto scénáři jste v tomto scénáři) spusťte příkaz *zrušit registraci – AzSGuestDirectoryTenant*. 

    ``` PowerShell  
    ## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
    $adminARMEndpoint = "https://adminmanagement.local.azurestack.external"
    
    ## Replace the value below with the Azure Stack directory
    $azureStackDirectoryTenant = "contoso.onmicrosoft.com"
    
    ## Replace the value below with the guest tenant directory. 
    $guestDirectoryTenantToBeDecommissioned = "fabrikam.onmicrosoft.com"
    
    ## Replace the value below with the name of the resource group in which the directory tenant registration resource should be created (resource group must already exist).
    $ResourceGroupName = "system.local"
    
    Unregister-AzSGuestDirectoryTenant -AdminResourceManagerEndpoint $adminARMEndpoint `
     -DirectoryTenantName $azureStackDirectoryTenant `
     -GuestDirectoryTenantName $guestDirectoryTenantToBeDecommissioned `
     -ResourceGroupName $ResourceGroupName
    ```

    > [!WARNING]
    > Kroky pro vypnutí víceklientské architektury se musí provádět v daném pořadí. Krok #1 se nezdařil, pokud je nejprve dokončen krok #2.

## <a name="next-steps"></a>Další postup

- [Spravovat delegované zprostředkovatele](azure-stack-delegated-provider.md)
- [Azure Stack klíčové koncepty](azure-stack-overview.md)
- [Správa využití a fakturace služby Azure Stack jako poskytovatel cloudových služeb](azure-stack-add-manage-billing-as-a-csp.md)
- [Přidání tenanta pro využití a fakturaci do služby Azure Stack](azure-stack-csp-howto-register-tenants.md)
