---
title: Změna majitele fakturace pro předplatné uživatele centra Azure Stacku | Microsoft Docs
description: Naučte se, jak změnit vlastníka fakturace pro předplatné Azure Stackho centra uživatele.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: conceptual
ms.date: 09/17/2019
ms.author: justinha
ms.reviewer: shnatara
ms.lastreviewed: 10/19/2018
ms.openlocfilehash: 65a47ac3fa9c146060dc05962c84ca60a17f898e
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75804584"
---
# <a name="change-the-billing-owner-for-an-azure-stack-hub-user-subscription"></a>Změna majitele fakturace pro předplatné uživatele centra Azure Stack

Operátory centra Azure Stack můžou pomocí PowerShellu změnit vlastníka fakturace pro předplatné uživatele. Jedním z důvodů, proč změnu vlastníka změnit, je třeba nahradit uživatele, který vaši organizaci opouští.

Existují dva typy *vlastníků* , které jsou přiřazeny k předplatnému:

- **Vlastník fakturace**: ve výchozím nastavení je vlastníkem fakturace uživatelský účet, který získá předplatné z nabídky a následně vlastní fakturační vztah pro toto předplatné. Tento účet je také správcem předplatného. V rámci předplatného může být toto označení pouze jeden uživatelský účet. Vlastník fakturace je často organizací nebo vedoucím týmu.

  K změně vlastníka fakturace můžete použít rutinu PowerShellu [set-AzsUserSubscription](/powershell/module/azs.subscriptions.admin/set-azsusersubscription) .  

- **Vlastníci přidaní prostřednictvím rolí RBAC** – roli **vlastníka** je možné udělit pomocí [řízení přístupu na základě role](azure-stack-manage-permissions.md) (RBAC). Libovolný počet dalších uživatelských účtů se dá přidat jako vlastníci, aby si vyčíslí vlastníka fakturace. Další vlastníci jsou také správci předplatného a mají všechna oprávnění k tomuto předplatnému, s výjimkou oprávnění k odstranění majitele fakturace.

  Ke správě dalších vlastníků můžete použít PowerShell. Další informace najdete v [tomto článku](/azure/role-based-access-control/role-assignments-powershell).

## <a name="change-the-billing-owner"></a>Změna vlastníka fakturace

Chcete-li změnit vlastníka fakturace předplatného uživatele, spusťte následující skript. Počítač, který použijete ke spuštění skriptu, se musí připojit k Azure Stackmu centru a spustit modul Azure Stack centra PowerShell 1.3.0 nebo novější. Další informace najdete v tématu [Instalace centra PowerShell pro Azure Stack](azure-stack-powershell-install.md).

>[!NOTE]
>V Azure Stackovém centru pro více tenantů musí být nový vlastník ve stejném adresáři jako stávající vlastník. Než budete moci poskytnout vlastnictví předplatného uživateli, který je v jiném adresáři, musíte nejprve [pozvat tohoto uživatele jako hosta do svého adresáře](/azure/active-directory/b2b/add-users-administrator).

Před spuštěním ve skriptu nahraďte následující hodnoty:

- **$ArmEndpoint**: koncový bod správce prostředků pro vaše prostředí.
- **$TenantId**: ID tenanta.
- **$SubscriptionId**: ID vašeho předplatného.
- **$OwnerUpn**: účet, například **uživatel\@example.com**, se má přidat jako nový vlastník fakturace.

```powershell
# Set up Azure Stack Hub admin environment
Add-AzureRmEnvironment -ARMEndpoint $ArmEndpoint -Name AzureStack-admin
Add-AzureRmAccount -Environment AzureStack-admin -TenantId $TenantId

# Select admin subscription
$providerSubscriptionId = (Get-AzureRmSubscription -SubscriptionName "Default Provider Subscription").Id
Write-Output "Setting context to the Default Provider Subscription: $providerSubscriptionId"
Set-AzureRmContext -Subscription $providerSubscriptionId

# Change user subscription owner
$subscription = Get-AzsUserSubscription -SubscriptionId $SubscriptionId
$Subscription.Owner = $OwnerUpn
Set-AzsUserSubscription -InputObject $subscription
```

[!include[Remove Account](../../includes/remove-account.md)]

## <a name="next-steps"></a>Další kroky

- [Správa Access Control na základě rolí](azure-stack-manage-permissions.md)
