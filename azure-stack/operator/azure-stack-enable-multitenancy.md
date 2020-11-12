---
title: Konfigurace víceklientské architektury v centru Azure Stack
description: Naučte se, jak povolit a zakázat více Azure Active Directory tenantů v centru Azure Stack.
author: BryanLa
ms.topic: how-to
ms.date: 10/16/2020
ms.author: bryanla
ms.reviewer: bryanr
ms.lastreviewed: 10/16/2020
ms.openlocfilehash: 923c430291c742069a29806449b45d4fc9cdef07
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94544220"
---
# <a name="configure-multi-tenancy-in-azure-stack-hub"></a>Konfigurace víceklientské architektury v centru Azure Stack

Centrum Azure Stack můžete nakonfigurovat tak, aby podporovalo uživatele z více tenantů Azure Active Directory (Azure AD), což jim umožňuje používat služby v centru Azure Stack. Představte si třeba následující scénář:

- Jste správcem služby contoso.onmicrosoft.com, kde je nainstalovaný Azure Stack hub.
- Marie je správcem adresáře fabrikam.onmicrosoft.com, kde se nacházejí uživatelé typu Host.
- Společnost Marie přijímá služby IaaS a PaaS od vaší společnosti a potřebuje, aby uživatelům v adresáři hosta (fabrikam.onmicrosoft.com) přihlásili a používali prostředky centra Azure Stack v contoso.onmicrosoft.com.

Tato příručka popisuje požadované kroky v souvislosti s tímto scénářem ke konfiguraci víceklientské architektury v Azure Stackovém centru. V tomto scénáři je nutné provést kroky, aby se uživatelé ze společnosti Fabrikam mohli přihlašovat a využívat služby z nasazení centra Azure Stack ve společnosti Contoso.

Pokud jste poskytovatelem Cloud Solution Provider (CSP), máte k dispozici další způsoby, jak můžete [Konfigurovat a spravovat Azure Stackho centra pro více tenantů](azure-stack-add-manage-billing-as-a-csp.md). 

## <a name="enable-multi-tenancy"></a>Povolení víceklientské architektury

Předtím, než nakonfigurujete víceklientské architektury v Azure Stackovém centru, je nutné mít na zřeteli několik požadavků:
  
 - A Marie musí koordinovat kroky správy v rámci adresáře Azure Stackho centra, který je nainstalovaný v (Contoso), a v adresáři hosta (Fabrikam).
 - Ujistěte se, že jste [nainstalovali](powershell-install-az-module.md) a [nakonfigurovali](azure-stack-powershell-configure-admin.md) PowerShell pro Azure Stack hub.
 - [Stáhněte si nástroje Azure Stack hub](azure-stack-powershell-download.md)a importujte moduly připojení a identita:

    ```powershell
    Import-Module .\Identity\AzureStack.Identity.psm1
    ```

### <a name="configure-azure-stack-hub-directory"></a>Konfigurovat adresář centra Azure Stack

V této části nakonfigurujete centrum Azure Stack tak, aby povolovalo přihlášení z tenantů klienta adresáře služby Azure AD společnosti Fabrikam.

Připojte tenanta adresáře hostů (Fabrikam) do centra Azure Stack, a to tak, že nakonfigurujete Azure Resource Manager pro příjem uživatelů a instančních objektů z tenanta hostovaného adresáře.

Správce služby contoso.onmicrosoft.com spustí následující příkazy:

```powershell  
## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint.
$adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

## Replace the value below with the Azure Stack Hub directory
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

Jakmile operátor centra Azure Stack povolí, aby se adresář Fabrikam používal s centrem Azure Stack, musí Marie zaregistrovat Azure Stackho centra s tenant adresáře společnosti Fabrikam.

#### <a name="register-azure-stack-hub-with-the-guest-directory"></a>Registrace centra Azure Stack s adresářem hosta

Marie (Správce adresáře společnosti Fabrikam) spustí v adresáři hosta fabrikam.onmicrosoft.com následující příkazy:

```powershell
## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint.
$tenantARMEndpoint = "https://management.local.azurestack.external"
    
## Replace the value below with the guest tenant directory.
$guestDirectoryTenantName = "fabrikam.onmicrosoft.com"

Register-AzSWithMyDirectoryTenant `
 -TenantResourceManagerEndpoint $tenantARMEndpoint `
 -DirectoryTenantName $guestDirectoryTenantName `
 -Verbose
```

> [!IMPORTANT]
> Pokud váš správce centra Azure Stack v budoucnu nainstaluje nové služby nebo aktualizace, možná budete muset tento skript spustit znovu.
>
> Spusťte tento skript kdykoli znovu, abyste zkontrolovali stav aplikací centra Azure Stack ve vašem adresáři.
>
> Pokud jste si všimli potíží s vytvářením virtuálních počítačů v Managed Disks (představených v aktualizaci 1808), přidal se nový **poskytovatel prostředků disku** , který vyžaduje, aby se tento skript spouštěl znovu.

### <a name="direct-users-to-sign-in"></a>Přímé přihlašování uživatelů

Teď, když jste vy a Marie dokončili kroky k zařazení adresáře Marie, může uživatel s názvem Marie nasměrovat uživatele společnosti Fabrikam, aby se přihlásili. Uživatelé společnosti Fabrikam (uživatelé s příponou fabrikam.onmicrosoft.com) se přihlásí návštěvou https \: //Portal.Local.azurestack.external.

Marie bude směrovat jakékoli [cizí objekty zabezpečení](/azure/role-based-access-control/rbac-and-directory-admin-roles) v adresáři Fabrikam (uživatelé v adresáři Fabrikam bez přípony Fabrikam.onmicrosoft.com), aby se mohli přihlásit pomocí protokolu HTTPS \: //Portal.Local.azurestack.external/Fabrikam.onmicrosoft.com. Pokud tuto adresu URL nepoužívají, odešlou se jejich výchozímu adresáři (Fabrikam) a zobrazí se chyba oznamující, že správce nesouhlasí.

## <a name="disable-multi-tenancy"></a>Zakázat víceklientské architektury

Pokud již nechcete více tenantů v centru Azure Stack, můžete zakázat víceklientské prostředí provedením následujících kroků v uvedeném pořadí:

1. Jako správce adresáře hosta (Marie v tomto scénáři) spusťte příkaz *zrušit registraci – AzsWithMyDirectoryTenant*. Rutina odinstaluje všechny aplikace Azure Stack hub z nového adresáře.

    ``` PowerShell
    ## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint.
    $tenantARMEndpoint = "https://management.local.azurestack.external"
        
    ## Replace the value below with the guest tenant directory.
    $guestDirectoryTenantName = "fabrikam.onmicrosoft.com"
    
    Unregister-AzsWithMyDirectoryTenant `
     -TenantResourceManagerEndpoint $tenantARMEndpoint `
     -DirectoryTenantName $guestDirectoryTenantName `
     -Verbose 
    ```

2. Jako správce služby Azure Stackového centra (v tomto scénáři) spusťte příkaz *zrušit registraci – AzSGuestDirectoryTenant*.

    ``` PowerShell
    ## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint.
    $adminARMEndpoint = "https://adminmanagement.local.azurestack.external"
    
    ## Replace the value below with the Azure Stack Hub directory
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

## <a name="retrieve-azure-stack-hub-identity-health-report"></a>Načíst sestavu stavu identity centra Azure Stack 

Nahraďte `<region>` `<domain>` `<homeDirectoryTenant>` zástupné symboly, a pak spusťte následující rutinu jako správce centra Azure Stack.

```powershell

$AdminResourceManagerEndpoint = "https://adminmanagement.<region>.<domain>"
$DirectoryName = "<homeDirectoryTenant>.onmicrosoft.com"
$healthReport = Get-AzsHealthReport -AdminResourceManagerEndpoint $AdminResourceManagerEndpoint -DirectoryTenantName $DirectoryName
Write-Host "Healthy directories: "
$healthReport.directoryTenants | Where status -EQ 'Healthy' | Select -Property tenantName,tenantId,status | ft


Write-Host "Unhealthy directories: "
$healthReport.directoryTenants | Where status -NE 'Healthy' | Select -Property tenantName,tenantId,status | ft
```

### <a name="update-azure-ad-tenant-permissions"></a>Aktualizovat oprávnění tenanta Azure AD

Tato akce vymaže výstrahu v centru Azure Stack, což značí, že adresář vyžaduje aktualizaci. Ze složky **Azurestack-Tools-Master/identity** spusťte následující příkaz:

```powershell
Import-Module ..\Identity\AzureStack.Identity.psm1

$adminResourceManagerEndpoint = "https://adminmanagement.<region>.<domain>"

# This is the primary tenant Azure Stack is registered to:
$homeDirectoryTenantName = "<homeDirectoryTenant>.onmicrosoft.com"

Update-AzsHomeDirectoryTenant -AdminResourceManagerEndpoint $adminResourceManagerEndpoint `
   -DirectoryTenantName $homeDirectoryTenantName -Verbose
```

Skript vás vyzve k zadání přihlašovacích údajů správce v tenantovi Azure AD a spuštění trvá několik minut. Výstraha by se měla po spuštění rutiny vymazat.

## <a name="next-steps"></a>Další kroky

- [Spravovat delegované zprostředkovatele](azure-stack-delegated-provider.md)
- [Klíčové koncepty centra Azure Stack](azure-stack-overview.md)
- [Správa využití a fakturace pro centra Azure Stack jako poskytovatele Cloud Solution Provider](azure-stack-add-manage-billing-as-a-csp.md)
- [Přidat tenanta pro využití a fakturace do centra Azure Stack](azure-stack-csp-howto-register-tenants.md)
