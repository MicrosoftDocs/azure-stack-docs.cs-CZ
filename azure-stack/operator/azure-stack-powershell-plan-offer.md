---
title: Použití PowerShellu ke správě předplatných, plánů a nabídek v centru Azure Stack
description: Způsob správy předplatných, plánů a nabídek pomocí prostředí PowerShell v centru Azure Stack.
author: PatAltimore
ms.topic: how-to
ms.date: 12/18/2020
ms.author: patricka
ms.lastreviewed: 12/18/2020
ms.reviewer: bganapa
ms.openlocfilehash: 3dcc3eae565f2e2a41078c2696a09f60ffa618b9
ms.sourcegitcommit: e13f27291bab236aac5d8b05401056961e9cc1e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2020
ms.locfileid: "97697844"
---
# <a name="use-powershell-to-manage-subscriptions-plans-and-offers-in-azure-stack-hub"></a>Použití PowerShellu ke správě předplatných, plánů a nabídek v centru Azure Stack

PowerShell můžete použít ke konfiguraci a doručování služeb pomocí nabídek, plánů a předplatných. pomocí PowerShellu. Pokyny k nastavení prostředí PowerShell v centru Azure Stack najdete v tématu [instalace prostředí PowerShell AZ Module for Azure Stack hub](powershell-install-az-module.md). Informace o připojení k centru Azure Stack pomocí prostředí PowerShell najdete v tématu [připojení k Azure Stack centra pomocí PowerShellu](azure-stack-powershell-configure-admin.md).

Než začnete, ověřte, že se načetl modul PowerShell pro Azure Stack hub. Do konzoly PowerShellu zadejte `Import-Module AzureStack` .

## <a name="create-a-plan"></a>Vytvoření plánu

Při vytváření plánu se vyžadují kvóty. Můžete použít existující kvóty nebo vytvořit nové kvóty. Pokud například chcete vytvořit kvótu úložiště, výpočetní prostředky a sítě, můžete použít rutiny [New-AzsStorageQuota](/powershell/module/azs.storage.admin/new-azsstoragequota), [New-AzsComputeQuota](/powershell/module/azs.compute.admin/new-azscomputequota)a [New-AzsNetworkQuota](/powershell/module/azs.network.admin/new-azsnetworkquota) :

```powershell
$serviceQuotas  = @()
$serviceQuotas += (New-AzsStorageQuota -Name "Example storage quota with defaults").Id
$serviceQuotas += (New-AzsComputeQuota -Name "Example compute quota with defaults").Id
$serviceQuotas += (New-AzsNetworkQuota -Name "Example network quota with defaults").Id
```

Pokud chcete vytvořit nebo aktualizovat plán základního nebo doplňku, použijte [New-AzsPlan](/powershell/module/azs.subscriptions.admin/new-azsplan).

```powershell
$testPlan = New-AzsPlan -Name "testplan" -ResourceGroupName "testrg" -QuotaIds $serviceQuotas -Description "Test plan"
```

## <a name="create-an-offer"></a>Vytvoření nabídky

Pokud chcete vytvořit nabídku, použijte [New-AzsOffer](/powershell/module/azs.subscriptions.admin/new-azsoffer).

```powershell
New-AzsOffer -Name "testoffer" -ResourceGroupName "testrg" -BasePlanIds @($testPlan.Id)
```

Až nabídku máte, můžete do ní přidat plány. Použijte [příkaz Add-AzsPlanToOffer](/powershell/module/azs.subscriptions.admin/add-azsplantooffer). Parametr **-PlanLinkType** odlišuje typ plánu.

```powershell
Add-AzsPlanToOffer -PlanName "addonplan" -PlanLinkType Addon -OfferName "testoffer" -ResourceGroupName "testrg" -MaxAcquisitionCount 18
```

Pokud chcete změnit stav nabídky, použijte rutinu [set-AzsOffer](/powershell/module/azs.subscriptions.admin/set-azsoffer) .

```powershell
$offer = Get-AzsAdminManagedOffer -Name "testoffer" -ResourceGroupName "testrg"
$offer.state = "Public"
$offer | Set-AzsOffer -Confirm:$false
```

## <a name="create-subscription-to-an-offer"></a>Vytvoření předplatného pro nabídku

Když vytvoříte nabídku, uživatelé potřebují předplatné pro tuto nabídku, aby ji mohli využít. Existují dva způsoby, jak se uživatelé můžou přihlásit k odběru nabídky:

* Jako operátor cloudu můžete vytvořit předplatné pro uživatele. Můžete vytvářet předplatná veřejných i privátních nabídek.
* Jako uživatel se můžete přihlásit k odběru veřejné nabídky.

Pokud chcete vytvořit předplatné pro uživatele jako operátora cloudu, použijte [New-AzsUserSubscription](/powershell/module/azs.subscriptions.admin/new-azsusersubscription).

```powershell
New-AzsUserSubscription -Owner "user@contoso.com" -DisplayName "User subscription" -OfferId "/subscriptions/<Subscription ID>/resourceGroups/testrg/providers/Microsoft.Subscriptions.Admin/offers/testoffer"
```

Pokud se chcete přihlásit k odběru veřejné nabídky jako uživatel, použijte [New-AzsSubscription](/powershell/module/azs.subscriptions/new-azssubscription). *New-AzsSubscription* vyžaduje připojení k prostředí uživatele Azure Resource Manager. Použijte postup v části [připojení k Azure Stack centrum s prostředím PowerShell](azure-stack-powershell-configure-admin.md) , ale použijte koncový bod uživatele Azure Resource Manager. Například `Add-AzEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.local.azurestack.external"`.

```powershell
$testOffer = Get-AzsOffer | Where-Object Name -eq "testoffer"
New-AzsSubscription -OfferId "User subscription" -OfferId $testOffer.Id -DisplayName "My subscription"
```

## <a name="delete-quotas-plans-offers-and-subscriptions"></a>Odstranění kvót, plánů, nabídek a předplatných

K dispozici jsou doprovodné rutiny PowerShellu pro odstranění kvót, plánů, nabídek a předplatných centra Azure Stack. Následující příklad ukazuje příklady pro každý z nich.

Pomocí [Remove-AzsUserSubscription](/powershell/module/azs.subscriptions.admin/remove-azsusersubscription) odeberte předplatné z nabídky.

```powershell
Remove-AzsUserSubscription -TargetSubscriptionId "c90173b1-de7a-4b1d-8600-b8325ca1eab1e"
```

Pokud chcete z nabídky odebrat plán, použijte [Remove-AzsPlanFromOffer](/powershell/module/azs.subscriptions.admin/remove-azsplanfromoffer).

```powershell
Remove-AzsPlanFromOffer -PlanName "addonplan" -PlanLinkType Addon -OfferName "testoffer" -ResourceGroupName "testrg"
Remove-AzsPlanFromOffer -PlanName "testplan" -PlanLinkType Base -OfferName "testoffer" -ResourceGroupName "testrg"
```

K odebrání plánu použijte [příkaz Remove-AzsPlan](/powershell/module/azs.subscriptions.admin/remove-azsplan) .

```powershell
Remove-AzsPlan -Name "testplan" -ResourceGroupName "testrg"
```

K odebrání nabídky použijte [příkaz Remove-AzsOffer](/powershell/module/azs.subscriptions.admin/remove-azsoffer) .

```powershell
Remove-AzsOffer -Name "testoffer" -ResourceGroupName "testrg"
```

K odebrání kvót použijte [Remove-AzsStorageQuota](/powershell/module/azs.storage.admin/remove-azsstoragequota), [Remove-AzsComputeQuota](/powershell/module/azs.compute.admin/remove-azscomputequota), [Remove-AzsNetworkQuota](/powershell/module/azs.network.admin/remove-azsnetworkquota) .

```powershell
Remove-AzsStorageQuota -Name "Example storage quota with defaults"
Remove-AzsComputeQuota -Name "Example compute quota with defaults"
Remove-AzsNetworkQuota -Name "Example network quota with defaults"
```

## <a name="next-steps"></a>Další kroky

- [Správa aktualizací v centru Azure Stack](./azure-stack-updates.md)
