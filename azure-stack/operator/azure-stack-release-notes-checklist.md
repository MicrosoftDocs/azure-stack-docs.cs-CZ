---
title: Azure Stack zpráva k vydání verze – kontrolní seznam aktualizace aktivity | Dokumentace Microsoftu
description: Rychlé kontrolní seznam pro přípravu systému nejnovější Azure Stack aktualizace.
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
ms.date: 06/05/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 06/05/2019
ms.openlocfilehash: e079121fda268e9892648fca99da34de04f08101
ms.sourcegitcommit: 593d40bccf1b2957a763017a8a2d7043f8d8315c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2019
ms.locfileid: "67152483"
---
# <a name="azure-stack-update-activity-checklist"></a>Azure Stack kontrolní seznam aktualizace aktivity

Tento článek obsahuje kontrolní aktivity související aktualizace pro operátory Azure stacku. Pokud se chystáte použít aktualizace do služby Azure Stack, najdete tyto informace.

## <a name="prepare-for-azure-stack-update"></a>Příprava pro aktualizace služby Azure Stack

| Aktivita              | Podrobnosti                                                                          |
|-----------------------|----------------------------------------------------------------------------------|
| Kontrola známých problémů   | [Seznam známých problémů](azure-stack-release-notes-known-issues-1905.md).                |
| Kontrola aktualizací zabezpečení | [Seznam aktualizací zabezpečení](azure-stack-release-notes-security-updates-1905.md).      |
| Run Test-AzureStack   | Spustit `Test-AzureStack -Group UpdateReadiness` identifikovat provozní problémy.      |
| Řešení potíží        | Vyřešte všechny provozní problémy identifikovaný **testovací AzureStack**.                |
| Použít nejnovější opravy hotfix | Použijte nejnovější opravy hotfix, která se vztahují na aktuálně nainstalovanou verzi.         |
| Spusťte nástroj Plánovač kapacity | Ujistěte se, že používáte nejnovější verzi [Azure Stack Capacity Planner](https://aka.ms/azstackcapacityplanner) nástroj k provedení úlohy plánování a velikosti. Nejnovější verze obsahuje opravy chyb a nabízí nové funkce, které se vydávají s každou aktualizaci Azure Stack. |
| Je dostupná aktualizace       | Nasazení Azure Stack v propojených scénářích pouze, pravidelně kontrolovat zabezpečeného koncového bodu a automaticky upozorní, pokud je aktualizace k dispozici pro váš cloud. Odpojené zákazníků můžete stáhnout a naimportovat nové 1905 pomocí balíčku [procesu je zde popsáno,](azure-stack-apply-updates.md).               |

## <a name="during-azure-stack-update"></a>Během aktualizace služby Azure Stack

| Aktivita              | Podrobnosti                                                                          |
|-----------------------|----------------------------------------------------------------------------------|
| Správa aktualizací         | [Správa aktualizací ve službě Azure Stack pomocí portálu pro operátor](azure-stack-updates.md). |
| Průběh aktualizace můžete sledovat        | Pokud není k dispozici, portál operátor [monitorování aktualizací ve službě Azure Stack pomocí privilegovaných koncového bodu](azure-stack-monitor-update.md). |
| Obnovit aktualizace            | Po selhání aktualizace, oprava [obnovit aktualizace ve službě Azure Stack pomocí privilegovaných koncového bodu](azure-stack-monitor-update.md). |

> [!IMPORTANT]  
> Nespouštějte **testovací AzureStack** během aktualizace, jako to způsobí, že aktualizace zpomalí.

## <a name="after-azure-stack-update"></a>Po aktualizaci služby Azure Stack

| Aktivita              | Podrobnosti                                                                          |
|-----------------------|----------------------------------------------------------------------------------|
| Použít nejnovější opravy hotfix | Použijte nejnovější opravy hotfix pro aktualizovanou verzi.                          |
| Získat šifrovací klíče | Načíst data na zbývající šifrovacích klíčů a bezpečně je uložte mimo nasazení Azure Stack. Postupujte podle [pokyny, jak k načtení klíčů](azure-stack-security-bitlocker.md). |

## <a name="next-steps"></a>Další postup

- [Zkontrolujte seznam známých problémů](azure-stack-release-notes-known-issues-1905.md)
- [Zkontrolujte seznam aktualizací zabezpečení](azure-stack-release-notes-security-updates-1905.md)
