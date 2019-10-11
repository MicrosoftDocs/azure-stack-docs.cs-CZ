---
title: Kontrolní seznam operací Azure Stack | Microsoft Docs
description: Seznamte se s běžnými úkoly, které Azure Stack operátory potřebují a jak často je udělat.
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
ms.date: 10/07/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 10/07/2019
ms.openlocfilehash: 246fdc867080da1816f00c9fb6a68179b6944275
ms.sourcegitcommit: 12034a1190d52ca2c7d3f05c8c096416120d8392
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038193"
---
# <a name="azure-stack-operators-checklist"></a>Kontrolní seznam Azure Stackho operátoru

Pořídili jsme některé kontrolní seznamy operací, aby Azure Stack operátory byly úspěšné. Můžete přidat konkrétní operace, které je třeba provést pro vaše prostředí. Jsou určené k tomu, aby pomohly někomu připojit se jako operátor, který se má udělat denně, týdně a měsíčně. 

## <a name="routine-daily-weekly-and-monthly-operations"></a>Rutina každodenních, týdenních a měsíčních operací

Azure Stack operátory musí provádět rutiny na následujících věcech: 

> [!div class="checklist"]
> * Sledování a náprava chyb v [Azure monitor](https://docs.microsoft.com/azure/azure-monitor/overview) a [systen centra Operations Manager](https://docs.microsoft.com/system-center/scom/welcome) dvakrát denně
> * Spustit [test-AzureStack](azure-stack-diagnostic-test.md) jednou denně
> * Týdenní [využití Azure Stack](azure-stack-usage-reporting.md) sestavy 
> * Aktualizace [firmwaru OEM](azure-stack-update-oem.md) každé dva měsíce nebo na základě oznámení výrobce OEM
> * Příprava [aktualizací Azure Stack](release-notes-checklist.md) měsíčně

## <a name="day-to-day-operations-as-needed"></a>Každodenní operace podle potřeby

Od dnešního dne budou Azure Stack operátoři také muset tyto operace dokončit: 

> [!div class="checklist"]
> * Vytvoření [plánů](azure-stack-create-plan.md)
> * Vytváření [nabídek](azure-stack-create-offer.md)
> * Vytvoření [předplatných](azure-stack-subscribe-plan-provision-vm.md)
> * Aktualizovat [kvóty](azure-stack-quota-types.md)
> * Publikování [ukázek Marketplace](azure-stack-create-and-publish-marketplace-item.md)
> * Přidat [vlastní image virtuálního počítače](azure-stack-add-vm-image.md)
> * Nabídka [sady škálování virtuálních počítačů](azure-stack-compute-add-scalesets.md) 
> * Použití [aktualizací Marketplace](azure-stack-marketplace-changes.md)
> * Konfigurace [telemetrie](azure-stack-telemetry.md)
> * Kontrola [správy oblastí](azure-stack-region-management.md)
> * Vypnutí nebo [restartování služby Azure Stack Services](azure-stack-start-and-stop.md) 
> * Nahrazení [uzlu jednotky škálování](azure-stack-replace-node.md)
> * Nahrazení [fyzického disku](azure-stack-replace-disk.md)
> * Výměna [hardwarové součásti](azure-stack-replace-component.md)
> * Aktualizace poskytovatelů prostředků doplňku, jako je [SQL](azure-stack-sql-resource-provider-update.md), [MySQL](azure-stack-mysql-resource-provider-update.md)a [App Service](azure-stack-app-service-update.md)
> * Spustit [shromažďování diagnostických protokolů](azure-stack-diagnostic-log-collection-overview.md)  



<!---Ask Jeff, Brian, is this everything you do, how can we make it more useful? Theebs has another user.

To be successful, do these things

ask Brian what are all the the things they need to write down for quick access, like passswords, IP addresses, and so on, to make them more efficient

have a password manager for azure-stack, pep, inventory

A checklist so operators can be successful, so someone onboarding is operator know what to do weekly, daily, monthly. --->


