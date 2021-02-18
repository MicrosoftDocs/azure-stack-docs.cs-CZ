---
title: Změna majitele fakturace pro předplatné uživatele centra Azure Stack
description: Naučte se, jak změnit vlastníka fakturace pro předplatné Azure Stackho centra uživatele.
author: PatAltimore
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: patricka
ms.reviewer: shnatara
ms.lastreviewed: 02/11/2021
ms.openlocfilehash: c612ace63515a4df8c8195cfdd1e58797dcd9ba2
ms.sourcegitcommit: 4c97ed2caf054ebeefa94da1f07cfb6be5929aac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "100647840"
---
# <a name="change-the-billing-owner-for-an-azure-stack-hub-user-subscription"></a>Změna majitele fakturace pro předplatné uživatele centra Azure Stack

Operátory centra Azure Stack můžou pomocí PowerShellu změnit vlastníka fakturace pro předplatné uživatele. Jedním z důvodů, proč změnu vlastníka změnit, je třeba nahradit uživatele, který vaši organizaci opouští.

Existují dva typy *vlastníků* , které jsou přiřazeny k předplatnému:

- **Vlastník fakturace**: ve výchozím nastavení je vlastníkem fakturace uživatelský účet, který získá předplatné z nabídky a následně vlastní fakturační vztah pro toto předplatné. Tento účet je také správcem předplatného. V rámci předplatného může být toto označení pouze jeden uživatelský účet. Vlastník fakturace je často organizací nebo vedoucím týmu.

  K změně vlastníka fakturace můžete použít rutinu PowerShellu [set-AzsUserSubscription](/powershell/module/azs.subscriptions.admin/set-azsusersubscription) .  

- **Vlastníci přidaní prostřednictvím rolí RBAC** – roli **vlastníka** je možné udělit pomocí [řízení přístupu na základě role](azure-stack-manage-permissions.md) (RBAC). Libovolný počet dalších uživatelských účtů se dá přidat jako vlastníci, aby si vyčíslí vlastníka fakturace. Další vlastníci jsou také správci předplatného a mají všechna oprávnění k tomuto předplatnému, s výjimkou oprávnění k odstranění majitele fakturace.

  Ke správě dalších vlastníků můžete použít PowerShell. Další informace najdete v [tomto článku](/azure/role-based-access-control/role-assignments-powershell).

## <a name="change-the-billing-owner"></a>Změna vlastníka fakturace

Chcete-li změnit vlastníka fakturace předplatného uživatele, spusťte následující skript. Počítač, který použijete ke spuštění skriptu, se musí připojit k Azure Stackmu centru a spustit modul Azure Stack centra PowerShell 1.3.0 nebo novější. Další informace najdete v tématu [Instalace centra PowerShell pro Azure Stack](powershell-install-az-module.md).

>[!NOTE]
>V Azure Stackovém centru pro více tenantů musí být nový vlastník ve stejném adresáři jako stávající vlastník. Než budete moci poskytnout vlastnictví předplatného uživateli, který je v jiném adresáři, musíte nejprve [pozvat tohoto uživatele jako hosta do svého adresáře](/azure/active-directory/b2b/add-users-administrator).

Před spuštěním ve skriptu nahraďte následující hodnoty:

- **$ArmEndpoint**: koncový bod správce prostředků pro vaše prostředí.
- **$TenantId**: ID tenanta.
- **$SubscriptionId**: ID vašeho předplatného.
- **$OwnerUpn**: účet, například **Uživatel \@ example.com**, se má přidat jako nový vlastník fakturace.

### <a name="az-modules"></a>[AZ modules](#tab/az)

```powershell
# Set up Azure Stack Hub admin environment
Add-AzEnvironment -ARMEndpoint $ArmEndpoint -Name AzureStack-admin
Connect-AzAccount -Environment AzureStack-admin -TenantId $TenantId

# Select admin subscription
$providerSubscriptionId = (Get-AzSubscription -SubscriptionName "Default Provider Subscription").Id
Write-Output "Setting context to the Default Provider Subscription: $providerSubscriptionId"
Set-AzContext -Subscription $providerSubscriptionId

# Change user subscription owner
$subscription = Get-AzsUserSubscription -SubscriptionId $SubscriptionId
$Subscription.Owner = $OwnerUpn
$Subscription | Set-AzsUserSubscription | fl *
```

[!include[Remove Account](../includes/remove-account-az.md)]

### <a name="azurerm-modules"></a>[Moduly AzureRM](#tab/azurerm)

```powershell
# Set up Azure Stack Hub admin environment
Add-AzureRmEnvironment -ARMEndpoint $ArmEndpoint -Name AzureStack-admin
Add-AzureRmAccount  -Environment AzureStack-admin -TenantId $TenantId

# Select admin subscription
$providerSubscriptionId = (Get-AzureRMSubscription -SubscriptionName "Default Provider Subscription").Id
Write-Output "Setting context to the Default Provider Subscription: $providerSubscriptionId"
Set-AzureRMContext -Subscription $providerSubscriptionId

# Change user subscription owner
$subscription = Get-AzsUserSubscription -SubscriptionId $SubscriptionId
$Subscription.Owner = $OwnerUpn
$Subscription | Set-AzsUserSubscription | fl *
```
[!include[Remove Account](../includes/remove-account-azurerm.md)]
---

## <a name="next-steps"></a>Další kroky

- [Spravovat Role-Based Access Control](azure-stack-manage-permissions.md)
