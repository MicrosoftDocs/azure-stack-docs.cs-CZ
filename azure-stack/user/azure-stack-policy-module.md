---
title: Použití modulu zásad centra Azure Stack
description: Zjistěte, jak omezit předplatné Azure tak, aby se chovalo jako předplatné centra Azure Stack.
author: sethmanheim
ms.topic: article
ms.date: 11/22/2020
ms.author: sethm
ms.lastreviewed: 11/22/2020
ms.openlocfilehash: 13d3e006d676e7e24f94741c59cb8837d5200d1d
ms.sourcegitcommit: 8c745b205ea5a7a82b73b7a9daf1a7880fd1bee9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95518122"
---
# <a name="manage-azure-policy-using-the-azure-stack-hub-policy-module"></a>Správa zásad Azure pomocí modulu zásad služby Azure Stack hub

Modul zásad centra Azure Stack umožňuje nakonfigurovat předplatné Azure se stejnou verzí a dostupností služeb jako centrum Azure Stack. Modul používá rutinu [**New-AzPolicyDefinition**](/powershell/module/Az.resources/new-Azpolicydefinition) prostředí PowerShell k vytvoření zásady Azure, která omezuje typy prostředků a služby, které jsou v předplatném k dispozici. Pak vytvoříte přiřazení zásady v rámci příslušného oboru pomocí rutiny [**New-AzPolicyAssignment**](/powershell/module/Az.resources/new-Azpolicyassignment) . Po nakonfigurování zásady můžete použít předplatné Azure k vývoji aplikací, které jsou cílené pro centrum Azure Stack.

## <a name="install-the-module"></a>Nainstalovat modul

1. Nainstalujte požadovanou verzi modulu AZ PowerShell, jak je popsáno v kroku 1 [instalace PowerShellu pro Azure Stack hub](../operator/powershell-install-az-module.md).
2. [Stáhněte si nástroje Azure Stack hub z GitHubu](../operator/azure-stack-powershell-download.md).
3. [Nakonfigurujte PowerShell pro použití s Azure Stack hub](azure-stack-powershell-configure-user.md).
4. Importujte modul **AzureStack. Policy. psm1** :

   ```powershell
   Import-Module .\Policy\AzureStack.Policy.psm1
   ```

## <a name="apply-policy-to-azure-subscription"></a>Použití zásad u předplatného Azure

Pomocí následujících příkazů můžete pro své předplatné Azure použít výchozí zásadu centra Azure Stack. Než tyto příkazy spustíte, nahraďte `Azure subscription name` název vašeho předplatného Azure.

### <a name="az-modules"></a>[AZ modules](#tab/az1)

```powershell
Add-AzAccount
$s = Select-AzSubscription -SubscriptionName "Azure subscription name"
$policy = New-AzPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID
```
### <a name="azurerm-modules"></a>[Moduly AzureRM](#tab/azurerm1)

```powershell
Add-AzureRMAccount
$s = Select-AzureRMSubscription -SubscriptionName "Azure subscription name"
$policy = New-AzureRMPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRMPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID
```

---

## <a name="apply-policy-to-a-resource-group"></a>Použití zásad pro skupinu prostředků

Můžete chtít použít podrobnější zásady. Můžete mít například další prostředky spuštěné ve stejném předplatném. Můžete nastavit obor aplikace zásad na konkrétní skupinu prostředků, která vám umožní testovat aplikace pro Azure Stack centrum pomocí prostředků Azure. Před spuštěním následujících příkazů nahraďte `Azure subscription name` názvem vašeho předplatného Azure:

### <a name="az-modules"></a>[AZ modules](#tab/az2)

```powershell
Add-AzAccount
$rgName = 'myRG01'
$s = Select-AzSubscription -SubscriptionName "Azure subscription name"
$policy = New-AzPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID/resourceGroups/$rgName
```
### <a name="azurerm-modules"></a>[Moduly AzureRM](#tab/azurerm2)
 
```powershell
Add-AzureRMAccount
$rgName = 'myRG01'
$s = Select-AzureRMSubscription -SubscriptionName "Azure subscription name"
$policy = New-AzureRMPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRMPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID/resourceGroups/$rgName
```

---

## <a name="policy-in-action"></a>Zásada v akci

Po nasazení zásad Azure se při pokusu o nasazení prostředku, který je zakázaný zásadou, zobrazí chyba:

![Výsledek nasazení prostředků se nezdařil z důvodu omezení zásad.](./media/azure-stack-policy-module/image1.png)

## <a name="next-steps"></a>Další kroky

* [Nasazení šablon pomocí PowerShellu](azure-stack-deploy-template-powershell.md)
* [Nasazení šablon pomocí Azure CLI](azure-stack-deploy-template-command-line.md)
* [Nasazení šablon pomocí sady Visual Studio](azure-stack-deploy-template-visual-studio.md)
