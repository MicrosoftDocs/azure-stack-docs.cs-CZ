---
title: Kontrolní seznam aktivit aktualizace centra Azure Stack | Microsoft Docs
description: Kontrolní seznam pro přípravu systému na nejnovější aktualizaci centra Azure Stack.
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
ms.date: 12/10/2019
ms.author: sethm
ms.reviewer: ppacent
ms.lastreviewed: 12/10/2019
ms.openlocfilehash: 8fe3a2ac7685eeb3e9bb116cdb5754cc7b567e28
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75880239"
---
# <a name="azure-stack-hub-update-activity-checklist"></a>Kontrolní seznam aktivity aktualizace centra Azure Stack

Zkontrolujte Tento kontrolní seznam, aby bylo možné připravit Azure Stack aktualizace centra. Tento článek obsahuje kontrolní seznam aktivit souvisejících s aktualizací pro operátory centra Azure Stack.

## <a name="prepare-for-azure-stack-hub-update"></a>Příprava na Azure Stack aktualizace centra

| Aktivita                     | Podrobnosti                                                   |
|------------------------------|-----------------------------------------------------------|
| Zkontrolovat známé problémy     | [Seznam známých problémů](known-issues.md).                |
| Kontrola aktualizací zabezpečení | [Seznam aktualizací zabezpečení](release-notes-security-updates.md)      |
| Použít nejnovější verzi balíčku OEM | Obraťte se na výrobce OEM, abyste zajistili, že váš systém splňuje požadavky na minimální verzi balíčku OEM pro verzi centra Azure Stack, na kterou se systém aktualizuje. Ujistěte se, že je balíček OEM kompatibilní s verzí centra Azure Stack, na kterou aktualizujete. Pokud balíček OEM není kompatibilní s verzí centra Azure Stack, na kterou aktualizujete, budete muset před spuštěním aktualizace centra Azure Stack provést aktualizaci balíčku OEM. Pokyny najdete v části "Azure Stack použití aktualizací z centra OEM (Original Equipment Manufacturer) od výrobce OEM". |
| Volitelné: Konfigurace automatického shromažďování protokolů | Doporučujeme, abyste v prostředí Azure Stack hub nakonfigurovali automatické shromažďování protokolů, abyste zjednodušili proces shromažďování systémových protokolů v případě, že budete potřebovat otevřít lístek podpory. Pokud chcete konfigurovat automatické shromažďování protokolů, přečtěte si pokyny v tématu [Konfigurace automatického shromáždění protokolů diagnostiky centra Azure Stack](azure-stack-configure-automatic-diagnostic-log-collection.md). |
| Použít nejnovější opravy hotfix | Použijte nejnovější opravy hotfix, které se vztahují k aktuálně nainstalované verzi. Seznam nejnovějších oprav hotfix najdete v části Oprava hotfix poznámky k verzi. |
| Spustit nástroj pro Plánovač kapacity | Pro plánování úloh a změnu velikosti se ujistěte, že používáte nejnovější verzi [nástroje Capacity Planner centra Azure Stack](azure-stack-capacity-planning-overview.md) . Nejnovější verze obsahuje opravy chyb a poskytuje nové funkce, které jsou vydané s každou aktualizací centra Azure Stack. |
| Run Test-AzureStack | Pro identifikaci provozních problémů spusťte `Test-AzureStack -Group UpdateReadiness`. Rutina je přístupná prostřednictvím relace privilegovaného koncového bodu (PEP). Další informace najdete v tématu [ověření stavu systému centra Azure Stack](azure-stack-diagnostic-test.md). |
| Vyřešit problémy | Vyřešte všechny provozní problémy identifikované `Test-AzureStack`. |
| Dostupná aktualizace | V případě propojených scénářů Azure Stack nasazení centra pravidelně kontroluje zabezpečený koncový bod a automaticky vás upozorní, jestli je pro Cloud k dispozici aktualizace. Odpojení zákazníci mohou stáhnout a importovat nové balíčky pomocí [popsaného postupu](azure-stack-apply-updates.md). |
| Upozorněte uživatele | Měli byste informovat uživatele o všech operacích údržby a v případě potřeby můžete naplánovat normální časová období údržby v nepracovních hodinách. Operace údržby mohou ovlivnit úlohy tenanta i operace portálu. |

## <a name="during-azure-stack-hub-update"></a>Během aktualizace centra Azure Stack

| Aktivita | Podrobnosti |
|--------------------|------------------------------------------------------------------------------------------------------|
| Správa aktualizace |[Spravujte aktualizace v centru Azure Stack pomocí portálu operátora](azure-stack-updates.md). |
|  |  |
| Monitorování aktualizace | Pokud portál operátora není k dispozici, [monitorujte aktualizace v centru Azure Stack pomocí privilegovaného koncového bodu](azure-stack-monitor-update.md). |
|  |  |
| Obnovit aktualizace | Po Oprava selhání aktualizace [obnovte v centru Azure Stack aktualizace pomocí privilegovaného koncového bodu](azure-stack-monitor-update.md). |

> [!IMPORTANT]  
> Během aktualizace nespouštějte rutinu **test-AzureStack** , protože to způsobí, že aktualizace bude zastavena.

## <a name="after-azure-stack-hub-update"></a>Po Azure Stack aktualizace centra

| Aktivita | Podrobnosti |
|--------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Použít nejnovější opravy hotfix | Použijte nejnovější opravy hotfix, které se vztahují k aktualizované verzi. |
| Načtení šifrovacích klíčů | Načtěte data na základě šifrovacích klíčů a bezpečně je uložte mimo vaše nasazení centra Azure Stack. Postupujte podle [pokynů pro načtení klíčů](azure-stack-security-bitlocker.md). |
|  |  |
| Opětovné povolení víceklientské architektury | V případě víceúrovňového Azure Stackho centra se ujistěte, že jste po úspěšné aktualizaci [nakonfigurovali všechny klienty hostovaného adresáře](azure-stack-enable-multitenancy.md#configure-guest-directory) . |

## <a name="next-steps"></a>Další kroky

- [Kontrola seznamu známých problémů](known-issues.md)
- [Kontrola seznamu aktualizací zabezpečení](release-notes-security-updates.md)
