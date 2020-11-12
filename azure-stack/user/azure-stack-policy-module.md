---
title: Použití modulu zásad centra Azure Stack
description: Zjistěte, jak omezit předplatné Azure tak, aby se chovalo jako předplatné centra Azure Stack.
author: sethmanheim
ms.topic: article
ms.date: 06/09/2020
ms.author: sethm
ms.lastreviewed: 03/26/2019
ms.openlocfilehash: ca96de45f50f48b91dbb2e6e8679df5dedab8d8f
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94547054"
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

Pomocí následujících příkazů můžete pro své předplatné Azure použít výchozí zásadu centra Azure Stack. Než tyto příkazy spustíte, nahraďte `Azure subscription name` názvem vašeho předplatného Azure:

```powershell
Add-AzAccount
$s = Select-AzSubscription -SubscriptionName "Azure subscription name"
$policy = New-AzPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID
```

## <a name="apply-policy-to-a-resource-group"></a>Použití zásad pro skupinu prostředků

Můžete chtít použít podrobnější zásady. Můžete mít například další prostředky spuštěné ve stejném předplatném. Můžete nastavit obor aplikace zásad na konkrétní skupinu prostředků, která vám umožní testovat aplikace pro Azure Stack centrum pomocí prostředků Azure. Před spuštěním následujících příkazů nahraďte `Azure subscription name` názvem vašeho předplatného Azure:

```powershell
Add-AzAccount
$rgName = 'myRG01'
$s = Select-AzSubscription -SubscriptionName "Azure subscription name"
$policy = New-AzPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID/resourceGroups/$rgName
```

## <a name="policy-in-action"></a>Zásada v akci

Po nasazení zásad Azure se při pokusu o nasazení prostředku, který je zakázaný zásadou, zobrazí chyba:

![Výsledek nasazení prostředků se nezdařil z důvodu omezení zásad.](./media/azure-stack-policy-module/image1.png)

## <a name="next-steps"></a>Další kroky

* [Nasazení šablon pomocí PowerShellu](azure-stack-deploy-template-powershell.md)
* [Nasazení šablon pomocí Azure CLI](azure-stack-deploy-template-command-line.md)
* [Nasazení šablon pomocí sady Visual Studio](azure-stack-deploy-template-visual-studio.md)
