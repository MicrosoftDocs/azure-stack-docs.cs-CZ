---
title: Konfigurace víceklientské architektury v centru Azure Stack
description: Přečtěte si, jak nakonfigurovat víceklientské architektury pro klienty hosta Azure Active Directory v centru Azure Stack.
author: BryanLa
ms.topic: how-to
ms.date: 01/26/2021
ms.author: bryanla
ms.reviewer: bryanr
ms.lastreviewed: 01/26/2021
ms.openlocfilehash: 3de6c5db42285f90e1d4ce6c1ebf6736d7ce4863
ms.sourcegitcommit: d542b68b299b73e045f30916afb6018e365e9db6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2021
ms.locfileid: "99975941"
---
# <a name="configure-multi-tenancy-in-azure-stack-hub"></a>Konfigurace víceklientské architektury v centru Azure Stack

Centrum Azure Stack můžete nakonfigurovat tak, aby podporovala přihlášení uživatelů, kteří se nacházejí v jiných adresářích Azure Active Directory (Azure AD), což jim umožňuje používat služby v centru Azure Stack. Tyto adresáře mají vztah "hosta" s vaším centrem Azure Stack a považují se za klienty Azure AD typu Host. Představte si třeba následující scénář:

- Jste správcem služby contoso.onmicrosoft.com, který poskytuje služby pro správu identit a přístupu do centra Azure Stack.
- Marie je Správce adresáře fabrikam.onmicrosoft.com, tenant hosta Azure AD, kde se nacházejí uživatelé typu Host.
- Společnost společnosti Marie používá služby IaaS a PaaS z vaší společnosti. Společnost Fabrikam chce uživatelům dovolit, aby se přihlásili z adresáře hostů (fabrikam.onmicrosoft.com) a používali prostředky centra Azure Stack zabezpečené pomocí contoso.onmicrosoft.com.

V této příručce najdete požadované kroky v souvislosti s tímto scénářem, pokud chcete povolit nebo zakázat víceklientské prostředí v Azure Stack hub pro tenanta adresáře hostů. Tento proces se dá dokončit tak, že zaregistrujete nebo zrušíte registraci tenanta adresáře hosta, který povolí nebo zakáže přihlášení k rozbočovači Azure Stack a spotřebu služeb od uživatelů společnosti Fabrikam. 

Pokud jste poskytovatelem Cloud Solution Provider (CSP), máte k dispozici další způsoby, jak můžete [Konfigurovat a spravovat Azure Stackho centra pro více tenantů](azure-stack-add-manage-billing-as-a-csp.md). 

## <a name="prerequisites"></a>Požadavky

Před registrací nebo zrušením registrace adresáře hosta je potřeba, abyste vy a Marie dokončili kroky správy pro příslušné klienty Azure AD: domovský adresář centra Azure Stack (Contoso) a adresář hosta (Fabrikam):

 - [Nainstalujte](powershell-install-az-module.md) a [nakonfigurujte](azure-stack-powershell-configure-admin.md) PowerShell pro Azure Stack hub.
 - [Stáhněte si nástroje Azure Stack hub](azure-stack-powershell-download.md)a importujte moduly připojení a identita:

    ```powershell
    Import-Module .\Identity\AzureStack.Identity.psm1
    ```

## <a name="register-a-guest-directory"></a>Registrace adresáře hosta

Pokud chcete zaregistrovat adresář hostů pro víceklientské architektury, bude nutné nakonfigurovat adresář domovského Azure Stackového centra i adresář hostů.

#### <a name="configure-azure-stack-hub-directory"></a>Konfigurovat adresář centra Azure Stack

Jako správce služby contoso.onmicrosoft.com je třeba nejdřív připojit tenanta adresáře hosta společnosti Fabrikam do centra Azure Stack. Následující skript nastaví Azure Resource Manager pro přijímání přihlášení uživatelů a instančních objektů v tenantovi fabrikam.onmicrosoft.com:

```powershell  
## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint, formatted as adminmanagement.<region>.<FQDN>.
$adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

## Replace the value below with the Azure Stack Hub directory
$azureStackDirectoryTenant = "contoso.onmicrosoft.com"

## Replace the value below with the guest directory tenant. 
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

#### <a name="configure-guest-directory"></a>Konfigurace adresáře hosta

Dále musí Marie (Správce adresáře společnosti Fabrikam) zaregistrovat Azure Stack centrum s adresářem hosta fabrikam.onmicrosoft.com spuštěním následujícího skriptu:

```powershell
## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint, formatted as management.<region>.<FQDN>.
$tenantARMEndpoint = "https://management.local.azurestack.external"
    
## Replace the value below with the guest directory tenant.
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

Nakonec mohou uživatelé společnosti Fabrikam s účty, kteří se @fabrikam.onmicrosoft.com budou přihlašovat, směrovat pomocí [portálu Azure Stack User Portal](../user/azure-stack-use-portal.md). Pro systémy s více uzly je adresa URL uživatelského portálu formátována jako `https://management.<region>.<FQDN>` . V případě nasazení ASDK je adresa URL `https://portal.local.azurestack.external` .

Marie musí také směrovat jakékoli cizí objekty zabezpečení (uživatelé v adresáři Fabrikam bez přípony fabrikam.onmicrosoft.com), aby se přihlásili pomocí `https://<user-portal-url>/fabrikam.onmicrosoft.com` . Pokud neurčíte `/fabrikam.onmicrosoft.com` tenanta adresáře v adrese URL, odešlou se do výchozího adresáře a zobrazí se chyba oznamující, že správce nesouhlasí.

## <a name="unregister-a-guest-directory"></a>Zrušení registrace adresáře hostů

Pokud už nechcete, aby se přihlásili Azure Stack služby centra z tenanta hostovaného adresáře, můžete zrušit registraci adresáře. Znovu se musí nakonfigurovat adresář domovského Azure Stack centra i adresář hosta:

1. Jako správce adresáře hosta (Marie v tomto scénáři) spusťte `Unregister-AzsWithMyDirectoryTenant` . Rutina odinstaluje všechny aplikace Azure Stack hub z nového adresáře.

    ``` PowerShell
    ## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint, formatted as management.<region>.<FQDN>.
    $tenantARMEndpoint = "https://management.local.azurestack.external"
        
    ## Replace the value below with the guest directory tenant.
    $guestDirectoryTenantName = "fabrikam.onmicrosoft.com"
    
    Unregister-AzsWithMyDirectoryTenant `
     -TenantResourceManagerEndpoint $tenantARMEndpoint `
     -DirectoryTenantName $guestDirectoryTenantName `
     -Verbose 
    ```

2. Jako správce služby Azure Stack hub (v tomto scénáři) spusťte `Unregister-AzSGuestDirectoryTenant` rutinu:

    ``` PowerShell
    ## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint, formatted as adminmanagement.<region>.<FQDN>.
    $adminARMEndpoint = "https://adminmanagement.local.azurestack.external"
    
    ## Replace the value below with the Azure Stack Hub directory
    $azureStackDirectoryTenant = "contoso.onmicrosoft.com"
    
    ## Replace the value below with the guest directory tenant. 
    $guestDirectoryTenantToBeDecommissioned = "fabrikam.onmicrosoft.com"
    
    ## Replace the value below with the name of the resource group in which the directory tenant resource was created (resource group must already exist).
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

## <a name="update-azure-ad-tenant-permissions"></a>Aktualizovat oprávnění tenanta Azure AD

Tato akce vymaže výstrahu v Azure Stackovém centru, což značí, že adresář vyžaduje aktualizaci. Ze složky **Azurestack-Tools-Master/identity** spusťte následující příkaz:

```powershell
Import-Module ..\Identity\AzureStack.Identity.psm1

$adminResourceManagerEndpoint = "https://adminmanagement.<region>.<domain>"

# This is the primary tenant Azure Stack Hub is registered to:
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
