---
title: Použití modulu zásad centra Azure Stack | Microsoft Docs
description: Zjistěte, jak omezit předplatné Azure tak, aby se chovalo jako předplatné centra Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2020
ms.author: sethm
ms.lastreviewed: 03/26/2019
ms.openlocfilehash: aedc2915f3e2be473b74766c4274197112ecf6fe
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75878743"
---
# <a name="manage-azure-policy-using-the-azure-stack-hub-policy-module"></a>Správa zásad Azure pomocí modulu zásad služby Azure Stack hub

Modul zásad centra Azure Stack umožňuje nakonfigurovat předplatné Azure se stejnou verzí a dostupností služeb jako centrum Azure Stack. Modul používá rutinu [**New-AzureRmPolicyDefinition**](/powershell/module/azurerm.resources/new-azurermpolicydefinition) prostředí PowerShell k vytvoření zásady Azure, která omezuje typy prostředků a služby, které jsou v předplatném k dispozici. Pak vytvoříte přiřazení zásady v rámci příslušného oboru pomocí rutiny [**New-AzureRmPolicyAssignment**](/powershell/module/azurerm.resources/new-azurermpolicyassignment) . Po nakonfigurování zásady můžete použít předplatné Azure k vývoji aplikací, které jsou cílené pro centrum Azure Stack.

## <a name="install-the-module"></a>Nainstalovat modul

1. Nainstalujte požadovanou verzi modulu AzureRM PowerShell, jak je popsáno v kroku 1 [instalace PowerShellu pro Azure Stack hub](../operator/azure-stack-powershell-install.md).
2. [Stáhněte si nástroje Azure Stack hub z GitHubu](../operator/azure-stack-powershell-download.md).
3. [Nakonfigurujte PowerShell pro použití s Azure Stack hub](azure-stack-powershell-configure-user.md).
4. Importujte modul **AzureStack. Policy. psm1** :

   ```powershell
   Import-Module .\Policy\AzureStack.Policy.psm1
   ```

## <a name="apply-policy-to-azure-subscription"></a>Použití zásad u předplatného Azure

Pomocí následujících příkazů můžete u svého předplatného Azure použít výchozí zásady Azure Stack hub. Než tyto příkazy spustíte, nahraďte `Azure subscription name` názvem vašeho předplatného Azure:

```powershell
Add-AzureRmAccount
$s = Select-AzureRmSubscription -SubscriptionName "Azure subscription name"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID
```

## <a name="apply-policy-to-a-resource-group"></a>Použití zásad pro skupinu prostředků

Můžete chtít použít podrobnější zásady. Můžete mít například další prostředky spuštěné ve stejném předplatném. Můžete nastavit obor aplikace zásad na konkrétní skupinu prostředků, která vám umožní testovat aplikace pro Azure Stack centrum pomocí prostředků Azure. Před spuštěním následujících příkazů nahraďte `Azure subscription name` názvem vašeho předplatného Azure:

```powershell
Add-AzureRmAccount
$rgName = 'myRG01'
$s = Select-AzureRmSubscription -SubscriptionName "Azure subscription name"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID/resourceGroups/$rgName
```

## <a name="policy-in-action"></a>Zásada v akci

Po nasazení zásad Azure se při pokusu o nasazení prostředku, který je zakázaný zásadou, zobrazí chyba:

![Výsledek nasazení prostředků se nezdařil z důvodu omezení zásad.](./media/azure-stack-policy-module/image1.png)

## <a name="next-steps"></a>Další kroky

* [Nasazení šablon pomocí PowerShellu](azure-stack-deploy-template-powershell.md)
* [Nasazení šablon pomocí Azure CLI](azure-stack-deploy-template-command-line.md)
* [Nasazení šablon pomocí sady Visual Studio](azure-stack-deploy-template-visual-studio.md)
