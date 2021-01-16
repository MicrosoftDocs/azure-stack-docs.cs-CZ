---
title: Kontrolní seznam aktivity aktualizace centra Azure Stack
description: Kontrolní seznam pro přípravu systému na nejnovější aktualizaci centra Azure Stack.
author: PatAltimore
ms.topic: article
ms.date: 08/10/2020
ms.author: patricka
ms.reviewer: TBD
ms.lastreviewed: 01/14/2020
ms.openlocfilehash: 740fa4fe450121f27211001974e91d47d3aed692
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2021
ms.locfileid: "98256077"
---
# <a name="azure-stack-hub-update-activity-checklist"></a>Kontrolní seznam aktivity aktualizace centra Azure Stack

Zkontrolujte Tento kontrolní seznam, aby bylo možné připravit Azure Stack aktualizace centra. Tento článek obsahuje kontrolní seznam aktivit souvisejících s aktualizací pro operátory centra Azure Stack.

## <a name="prepare-for-azure-stack-hub-update"></a>Příprava na aktualizaci služby Azure Stack Hub

| &nbsp;&nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; Aktivita &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;&nbsp;                   | Podrobnosti                                                   |
|------------------------------|-----------------------------------------------------------|
| Přečtěte si také o známých problémech.     | [Seznam známých problémů](known-issues.md).                |
| Kontrola aktualizací zabezpečení | [Seznam aktualizací zabezpečení](release-notes-security-updates.md)      |
| Kontrola aktualizací poskytovatele prostředků doplňku | [App Service](azure-stack-app-service-update.md)<br>[Event Hubs](resource-provider-apply-updates.md)<br> [MySQL](azure-stack-mysql-resource-provider-update.md)<br>[SQL](azure-stack-sql-resource-provider-update.md)<br>  |
| Použít nejnovější verzi balíčku OEM | Obraťte se na výrobce OEM, abyste zajistili, že váš systém splňuje požadavky na minimální verzi balíčku OEM pro verzi centra Azure Stack, na kterou se systém aktualizuje. Ujistěte se, že je balíček OEM kompatibilní s verzí centra Azure Stack, na kterou aktualizujete. Pokud balíček OEM není kompatibilní s verzí centra Azure Stack, na kterou aktualizujete, budete muset před spuštěním aktualizace centra Azure Stack provést aktualizaci balíčku OEM. Pokyny najdete v části "Azure Stack použití aktualizací z centra OEM (Original Equipment Manufacturer) od výrobce OEM". |
| Volitelné: Konfigurace automatického shromažďování protokolů | Doporučujeme, abyste v prostředí Azure Stack hub nakonfigurovali automatické shromažďování protokolů, abyste zjednodušili proces shromažďování systémových protokolů v případě, že budete potřebovat otevřít lístek podpory. Pokud chcete konfigurovat automatické shromažďování protokolů, přečtěte si pokyny v tématu [odesílání protokolů proaktivně](./diagnostic-log-collection.md#send-logs-proactively). |
| Použít nejnovější opravy hotfix | Použijte nejnovější opravy hotfix, které se vztahují k aktuálně nainstalované verzi. Seznam nejnovějších oprav hotfix najdete v části s [poznámkami k opravě verze poznámky k verzi](release-notes.md) . |
| Spustit nástroj pro Plánovač kapacity | Pro plánování úloh a změnu velikosti se ujistěte, že používáte nejnovější verzi [nástroje Capacity Planner centra Azure Stack](azure-stack-capacity-planning-overview.md) . Nejnovější verze obsahuje opravy chyb a poskytuje nové funkce, které jsou vydané s každou aktualizací centra Azure Stack. |
| Spuštění rutiny **test-AzureStack** | Spusťte `Test-AzureStack -Group UpdateReadiness` k identifikaci provozních problémů. Rutina je přístupná prostřednictvím relace privilegovaného koncového bodu (PEP). Další informace najdete v tématu [ověření stavu systému centra Azure Stack](azure-stack-diagnostic-test.md). |
| Vyřešit problémy | Vyřešte všechny provozní problémy, které identifikoval `Test-AzureStack` . |
| Dostupná aktualizace | V případě propojených scénářů Azure Stack nasazení centra pravidelně kontroluje zabezpečený koncový bod a automaticky vás upozorní, jestli je pro Cloud k dispozici aktualizace. Odpojení zákazníci mohou stáhnout a importovat nové balíčky pomocí [popsaného postupu](azure-stack-apply-updates.md). |
| Naplánování časového období údržby a informování uživatelů | Měli byste informovat uživatele o všech operacích údržby a v případě potřeby naplánovat normální časová období údržby v nepracovních hodinách. Operace údržby můžou ovlivnit existující úlohy tenanta a způsobit, že nové operace klientů (například vytváření, překonfigurování nebo odstraňování virtuálních počítačů) selže, ať už je operace iniciovaná z portálu nebo programově z rozhraní Azure Resource Manager API. Jiné operace, jako je zálohování, mohou být také nedostupné, dokud se aktualizace nedokončí. V případě centra Azure Stack Express a úplné aktualizace si můžete prohlédnout [poznámky k verzi](release-notes.md) , které vám pomůžou odhadnout, jak dlouho se má aktualizace očekávat pro verzi, kterou aplikujete. |

## <a name="during-azure-stack-hub-update"></a>Během aktualizace služby Azure Stack Hub

| Aktivita | Podrobnosti |
|--------------------|------------------------------------------------------------------------------------------------------|
| Správa aktualizace |[Spravujte aktualizace v centru Azure Stack pomocí portálu operátora](azure-stack-updates.md). |
|  |  |
| Monitorování aktualizace | Pokud portál operátora není k dispozici, [monitorujte aktualizace v centru Azure Stack pomocí privilegovaného koncového bodu](azure-stack-monitor-update.md). |
|  |  |
| Obnovit aktualizace | Po Oprava selhání aktualizace [obnovte v centru Azure Stack aktualizace pomocí privilegovaného koncového bodu](azure-stack-monitor-update.md). |

> [!IMPORTANT]  
> Během aktualizace nespouštějte rutinu **test-AzureStack** , protože to způsobí, že aktualizace bude zastavena.
>
>Nespouštějte opravu uzlů během aktualizace bez ohledu na její stav.
>Pokud se během aktualizace vyžaduje oprava uzlu, kontaktujte prosím podpora Microsoftu.

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