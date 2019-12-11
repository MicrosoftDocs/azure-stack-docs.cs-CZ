---
title: Kontrolní seznam aktivity Azure Stack aktualizace | Microsoft Docs
description: Kontrolní seznam pro přípravu systému na nejnovější aktualizaci Azure Stack.
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
ms.openlocfilehash: a560b37051cf3b8b54e3cfec69dc6a9b28cc1cfb
ms.sourcegitcommit: d9430072dd96ae305101da6d8a47d6c23a0a64c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74995565"
---
# <a name="azure-stack-update-activity-checklist"></a>Kontrolní seznam aktivity Azure Stack aktualizace

*Platí pro: integrované systémy Azure Stack*

Projděte si tento kontrolní seznam, abyste se připravili na aktualizaci Azure Stack. Tento článek obsahuje kontrolní seznam aktivit souvisejících s aktualizací pro operátory Azure Stack.

## <a name="prepare-for-azure-stack-update"></a>Příprava na aktualizaci Azure Stack

| Aktivita                     | Podrobnosti                                                   |
|------------------------------|-----------------------------------------------------------|
| Zkontrolovat známé problémy     | [Seznam známých problémů](known-issues.md).                |
| Kontrola aktualizací zabezpečení | [Seznam aktualizací zabezpečení](release-notes-security-updates.md)      |
| Použít nejnovější verzi balíčku OEM | Obraťte se na výrobce OEM, abyste zajistili, že systém splňuje požadavky na minimální verzi balíčku OEM pro verzi Azure Stack, na kterou se systém aktualizuje. Ujistěte se, že je balíček OEM kompatibilní s verzí Azure Stack, na kterou aktualizujete. Pokud balíček OEM není kompatibilní s verzí Azure Stack, na kterou aktualizujete, budete muset před spuštěním aktualizace Azure Stack provést aktualizaci balíčku OEM. Pokyny najdete v části "použití Azure Stack aktualizací OEM (Original Equipment Manufacturer)". |
| Volitelné: Konfigurace automatického shromažďování protokolů | Doporučujeme, abyste v prostředí Azure Stack hub nakonfigurovali automatické shromažďování protokolů, abyste zjednodušili proces shromažďování systémových protokolů v případě, že budete potřebovat otevřít lístek podpory. Chcete-li konfigurovat automatické shromažďování protokolů, přečtěte si pokyny v tématu [Konfigurace automatického Azure Stackho protokolu diagnostiky](azure-stack-configure-automatic-diagnostic-log-collection.md). |
| Použít nejnovější opravy hotfix | Použijte nejnovější opravy hotfix, které se vztahují k aktuálně nainstalované verzi. Seznam nejnovějších oprav hotfix najdete v části Oprava hotfix poznámky k verzi. |
| Spustit nástroj pro Plánovač kapacity | Pro plánování úloh a změnu velikosti se ujistěte, že používáte nejnovější verzi [nástroje Azure Stack Capacity Planner Tool](azure-stack-capacity-planning-overview.md) . Nejnovější verze obsahuje opravy chyb a poskytuje nové funkce, které jsou vydány s každou aktualizací Azure Stack. |
| Run Test-AzureStack | Pro identifikaci provozních problémů spusťte `Test-AzureStack -Group UpdateReadiness`. Rutina je přístupná prostřednictvím relace privilegovaného koncového bodu (PEP). Další informace najdete v tématu [ověření stavu systému Azure Stack](azure-stack-diagnostic-test.md). |
| Vyřešit problémy | Vyřešte všechny provozní problémy identifikované `Test-AzureStack`. |
| Dostupná aktualizace | Pouze v případě propojených scénářů Azure Stack nasazení pravidelně kontroluje zabezpečený koncový bod a automaticky vás upozorní, pokud je k dispozici aktualizace pro Cloud. Odpojení zákazníci mohou stáhnout a importovat nové balíčky pomocí [popsaného postupu](azure-stack-apply-updates.md). |
| Upozorněte uživatele | Měli byste informovat uživatele o všech operacích údržby a v případě potřeby můžete naplánovat normální časová období údržby v nepracovních hodinách. Operace údržby mohou ovlivnit úlohy tenanta i operace portálu. |

## <a name="during-azure-stack-update"></a>Během Azure Stack aktualizace

| Aktivita | Podrobnosti |
|--------------------|------------------------------------------------------------------------------------------------------|
| Správa aktualizace |[Pomocí portálu Operators spravujte aktualizace v Azure Stack](azure-stack-updates.md). |
|  |  |
| Monitorování aktualizace | Pokud portál operátorů není k dispozici, [monitorujte aktualizace v Azure Stack pomocí privilegovaného koncového bodu](azure-stack-monitor-update.md). |
|  |  |
| Obnovit aktualizace | Po Oprava selhání aktualizace [obnovte aktualizace v Azure Stack pomocí privilegovaného koncového bodu](azure-stack-monitor-update.md). |

> [!IMPORTANT]  
> Během aktualizace nespouštějte rutinu **test-AzureStack** , protože to způsobí, že aktualizace bude zastavena.

## <a name="after-azure-stack-update"></a>Po Azure Stack aktualizaci

| Aktivita | Podrobnosti |
|--------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Použít nejnovější opravy hotfix | Použijte nejnovější opravy hotfix, které se vztahují k aktualizované verzi. |
| Načtení šifrovacích klíčů | Načtěte data z neaktivních šifrovacích klíčů a bezpečně je uložte mimo vaše nasazení Azure Stack. Postupujte podle [pokynů pro načtení klíčů](azure-stack-security-bitlocker.md). |
|  |  |
| Opětovné povolení víceklientské architektury | V případě Azure Stack s více klienty nezapomeňte po úspěšné aktualizaci [nakonfigurovat všechny klienty adresáře hostů](azure-stack-enable-multitenancy.md#configure-guest-directory) . |

## <a name="next-steps"></a>Další kroky

- [Kontrola seznamu známých problémů](known-issues.md)
- [Kontrola seznamu aktualizací zabezpečení](release-notes-security-updates.md)
