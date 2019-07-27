---
title: Zpráva k vydání verze Azure Stack – kontrolní seznam aktivity aktualizace | Microsoft Docs
description: Rychlý kontrolní seznam pro přípravu systému na nejnovější aktualizaci Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 07/25/2019
ms.openlocfilehash: 0c7840c723aedd38b51002e4a1f18f5a87d66ec4
ms.sourcegitcommit: c2690b2dd36918ff3e47e359cac926128bb83101
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68544078"
---
# <a name="azure-stack-update-activity-checklist"></a>Kontrolní seznam aktivity Azure Stack aktualizace

Tento článek obsahuje kontrolní seznam aktivit souvisejících s aktualizací pro operátory Azure Stack. Pokud připravujete použití aktualizace pro Azure Stack, můžete si projít tyto informace.

## <a name="prepare-for-azure-stack-update"></a>Příprava na aktualizaci Azure Stack

| Aktivita                     | Podrobnosti                                                   |
|------------------------------|-----------------------------------------------------------|
| Zkontrolovat známé problémy     | [Seznam známých problémů](azure-stack-release-notes-known-issues-1906.md).                |
| Kontrola aktualizací zabezpečení | [Seznam aktualizací zabezpečení](azure-stack-release-notes-security-updates-1906.md)      |
| Použít nejnovější verzi balíčku OEM | Obraťte se na výrobce OEM, abyste zajistili, že systém splňuje požadavky na minimální verzi balíčku OEM pro verzi Azure Stack, na kterou se systém aktualizuje. |
| Run Test-AzureStack     | Spusťte `Test-AzureStack -Group UpdateReadiness` k identifikaci provozních problémů.      |
| Vyřešit problémy          | Vyřešte všechny provozní problémy, které identifikoval **test-AzureStack**.                |
| Použít nejnovější opravy hotfix   | Použijte nejnovější opravy hotfix, které se vztahují k aktuálně nainstalované verzi.         |
| Spustit nástroj pro Plánovač kapacity   | Pro plánování úloh a změnu velikosti se ujistěte, že používáte nejnovější verzi nástroje [Azure Stack Capacity Planner](https://aka.ms/azstackcapacityplanner) Tool. Nejnovější verze obsahuje opravy chyb a poskytuje nové funkce, které jsou vydány s každou aktualizací Azure Stack. |
| Je dostupná aktualizace.        | Pouze v případě propojených scénářů Azure Stack nasazení pravidelně kontroluje zabezpečený koncový bod a automaticky vás upozorní, pokud je k dispozici aktualizace pro Cloud. Odpojení zákazníci mohou stáhnout a importovat nový balíček 1906 pomocí popsaného [postupu](azure-stack-apply-updates.md). |


## <a name="during-azure-stack-update"></a>Během Azure Stack aktualizace

| Aktivita              | Podrobnosti                                                                          |
|-----------------------|----------------------------------------------------------------------------------|
| Správa aktualizace         | [Pomocí portálu Operators spravujte aktualizace v Azure Stack](azure-stack-updates.md). |
| Monitorování aktualizace        | Pokud portál operátorů není k dispozici, [monitorujte aktualizace v Azure Stack pomocí privilegovaného koncového bodu](azure-stack-monitor-update.md). |
| Obnovit aktualizace            | Po Oprava selhání aktualizace obnovte [aktualizace v Azure Stack pomocí privilegovaného koncového bodu](azure-stack-monitor-update.md). |

> [!IMPORTANT]  
> Během aktualizace nespouštějte rutinu **test-AzureStack** , protože to způsobí, že aktualizace bude zastavena.

## <a name="after-azure-stack-update"></a>Po Azure Stack aktualizaci

| Aktivita              | Podrobnosti                                                                          |
|-----------------------|----------------------------------------------------------------------------------|
| Použít nejnovější opravy hotfix | Použijte nejnovější opravy hotfix, které se vztahují k aktualizované verzi.                          |
| Načtení šifrovacích klíčů | Načtěte data z neaktivních šifrovacích klíčů a bezpečně je uložte mimo vaše nasazení Azure Stack. Postupujte podle [pokynů pro načtení klíčů](azure-stack-security-bitlocker.md). |
| Opětovné povolení víceklientské architektury | V případě Azure Stack s více klienty nezapomeňte po úspěšné aktualizaci [nakonfigurovat všechny klienty adresáře hostů](https://docs.microsoft.com/azure-stack/operator/azure-stack-enable-multitenancy#configure-guest-directory) . |

## <a name="next-steps"></a>Další postup

- [Kontrola seznamu známých problémů](azure-stack-release-notes-known-issues-1907.md)
- [Kontrola seznamu aktualizací zabezpečení](azure-stack-release-notes-security-updates-1907.md)
