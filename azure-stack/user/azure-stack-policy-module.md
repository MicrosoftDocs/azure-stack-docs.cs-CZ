---
title: Použití modulu zásad služby Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak omezit předplatné Azure, se chovají jako předplatnému Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 937ef34f-14d4-4ea9-960b-362ba986f000
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/26/2019
ms.author: sethm
ms.lastreviewed: 03/26/2019
ms.openlocfilehash: fdb17c1f8f7fd183b4542a1a0417b6880a8afe9b
ms.sourcegitcommit: b36d078e699c7924624b79641dbe9021af9606ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67816161"
---
# <a name="manage-azure-policy-using-the-azure-stack-policy-module"></a>Správa Azure policy použití modulu zásad Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Modul zásad služby Azure Stack umožňuje konfigurovat předplatné Azure s stejné správy verzí a dostupnost služeb jako Azure Stack. Modul používá [New-AzureRmPolicyDefinition](/powershell/module/azurerm.resources/new-azurermpolicydefinition) rutina prostředí PowerShell pro vytvoření zásady služby Azure, který omezuje typy prostředků a služeb dostupných v předplatném. Pak vytvoříte přiřazení zásady v rámci příslušeného oboru s použitím [New-AzureRmPolicyAssignment](/powershell/module/azurerm.resources/new-azurermpolicyassignment) rutiny. Po konfiguraci zásad, můžete použít své předplatné Azure pro vývoj aplikací určená pro Azure Stack.

## <a name="install-the-module"></a>Instalace modulu

1. Nainstalujte požadovanou verzi modul AzureRM Powershellu, jak je popsáno v kroku 1 [instalace Powershellu pro Azure Stack](../operator/azure-stack-powershell-install.md).
2. [Stáhněte si nástroje Azure Stack z Githubu](../operator/azure-stack-powershell-download.md).
3. [Konfigurace Powershellu pro použití s Azure Stackem](azure-stack-powershell-configure-user.md).
4. Import *AzureStack.Policy.psm1* modul:


   ```powershell
   Import-Module .\Policy\AzureStack.Policy.psm1
   ```

## <a name="apply-policy-to-azure-subscription"></a>Použití zásad k předplatnému Azure

Použít výchozí zásady služby Azure Stack ve vašem předplatném Azure můžete použít následující příkaz. Před spuštěním tohoto příkazu nahraďte `Azure subscription name` s názvem vašeho předplatného Azure:

```powershell
Add-AzureRmAccount
$s = Select-AzureRmSubscription -SubscriptionName "Azure subscription name"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID
```

## <a name="apply-policy-to-a-resource-group"></a>Použít zásady skupiny prostředků

Můžete chtít použít zásady, které jsou přesnější. Například můžete mít další prostředky spuštěné ve stejném předplatném. Použití zásad pro konkrétní skupině prostředků, které umožňuje provádět testování aplikací pro Azure Stack pomocí prostředků Azure můžete omezit rozsah. Před spuštěním následujícího příkazu, nahraďte `Azure subscription name` s názvem vašeho předplatného Azure:

```powershell
Add-AzureRmAccount
$rgName = 'myRG01'
$s = Select-AzureRmSubscription -SubscriptionName "Azure subscription name"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID/resourceGroups/$rgName
```

## <a name="policy-in-action"></a>Zásady v akci

Po nasazení Azure policy, obdržíte chybu při pokusu o nasazení prostředku, který je zakázána zásadami:

![Výsledek nasazení prostředků se nezdařilo z důvodu omezení zásad](./media/azure-stack-policy-module/image1.png)

## <a name="next-steps"></a>Další postup

* [Nasazení šablon pomocí PowerShellu](azure-stack-deploy-template-powershell.md)
* [Nasazení šablon pomocí Azure CLI](azure-stack-deploy-template-command-line.md)
* [Nasazení šablon pomocí sady Visual Studio](azure-stack-deploy-template-visual-studio.md)
