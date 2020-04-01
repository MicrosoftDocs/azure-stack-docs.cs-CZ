---
title: Správa Event Hubs v centru Azure Stack
description: Naučte se spravovat Event Hubsho poskytovatele prostředků v centru Azure Stack.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 12/09/2019
ms.reviewer: jfggdl
ms.lastreviewed: 12/09/2019
ms.openlocfilehash: e9f500da4cab68688a6dd33374cfbc1cc166828d
ms.sourcegitcommit: dd53af1b0fc2390de162d41e3d59545d1baad1a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80424034"
---
# <a name="how-to-manage-event-hubs-on-azure-stack-hub"></a>Správa Event Hubs v centru Azure Stack

[!INCLUDE [preview-banner](../includes/event-hubs-preview.md)]

Prostředí pro správu Event Hubs umožňuje řídit službu a vizualizovat její stav a výstrahy. 

## <a name="overview"></a>Přehled

Pro přístup ke stránce pro správu Event Hubs použijte následující postup:

1. Přihlaste se k portálu pro správu centra Azure Stack.
2. V levém podokně vyberte **všechny služby** .
3. Vyhledejte "Event Hubs" a vyberte službu. Pokud nemůžete najít Event Hubs službu, musí se nejdřív [nainstalovat](event-hubs-rp-install.md)poskytovatel prostředků.
4. Zobrazí se stránka s přehledem správy Event Hubs. Čtyři části najdete v levém podokně:
   - **Přehled**: poskytuje obecné zobrazení a přístup ke konkrétním oblastem správy.
   - **Výstrahy**: zobrazí všechny kritické výstrahy a upozornění pro Event Hubs. Podrobnosti najdete v části [věnované výstrahám](#alerts) .
   - **Kvóty**: slouží k vytváření, aktualizaci a odstraňování kvót. Podrobnosti najdete v části [kvóty](#quotas) .
   - **Event Hubs clustery**: zobrazuje seznam všech nakonfigurovaných clusterů. Podrobnosti najdete v části věnované [clusterům Event Hubs](#event-hubs-clusters) .

   [![spravovat centra událostí](media/event-hubs-rp-manage/1-manage-event-hubs.png)](media/event-hubs-rp-manage/1-manage-event-hubs.png#lightbox)

## <a name="quotas"></a>kvóty,

Výběr **kvót** na hlavní stránce zobrazuje seznam používaných kvót, včetně přidružených plánů určujících kvóty. 
 
[![Správa centra událostí – kvóty](media/event-hubs-rp-manage/3-quotas.png)](media/event-hubs-rp-manage/3-quotas.png#lightbox)

Další informace o typech kvót definovaných pro Event Hubs najdete v tématu [typy kvót](azure-stack-quota-types.md#event-hubs-quota-types) .

## <a name="alerts"></a>Výstrahy

Poskytovatel prostředků Event Hubs podporuje následující výstrahy:
   
| Kategorie | Výstraha | Typ | Podmínka |
|----------|-------|------|-----------|
| Výkon | | | |
| | EventHub – CpuUsage | Upozornění | Průměr% využití CPU Event Hubs clusteru za posledních 6 hodin je větší než 50%. |
| | EventHub – MemoryUsage | Upozornění | Průměr% datového disku (E:) využití clusteru Event Hubs za posledních 6 hodin je větší než 50%. |
| | EventHub – SKU | Upozornění | Průměr% volného místa na disku Event Hubs clusteru za posledních 6 hodin je menší než 50%. |
| Využití/kvóta | | | |
| | EventHub – QuotaExceeded | Upozornění | Během posledních šesti hodin došlo k chybě překročení kvóty. |
| | EventHub – NamespaceCreditUsage | Upozornění | Součet využití kreditu z oboru názvů za posledních šest hodin je větší než 10000,0. |
| Služba byla snížena. | | | |
| | EventHub – Nenalezeno | Upozornění | Během posledních šesti hodin došlo k vnitřní chybě serveru. |
| | EventHub – ServerBusy | Upozornění | V posledních šesti hodinách došlo k chybě zaneprázdněnosti serveru. |
| Klient | | | |
| | EventHub – ClientError | Upozornění | V posledních šesti hodinách došlo k chybě klienta. |
| Prostředek | | | |
| | EventHub – PendingDeletingResources | Upozornění | Součet nedokončených odstranění prostředků za posledních šest hodin je větší než 100. |
| | EventHub – ProvisioningQueueLength | Upozornění | Průměrná délka fronty zřizování za posledních šest hodin je větší než 30. |

Výběr **Upozornění** na hlavní stránce zobrazuje seznam vydaných výstrah:

[![spravovat centra událostí – souhrn výstrah](media/event-hubs-rp-manage/2-alerts-summary.png)](media/event-hubs-rp-manage/2-alerts-summary.png#lightbox)

Výběrem výstrahy ze seznamu zobrazíte na pravé straně panel **Podrobnosti výstrahy** :

[![spravovat centra událostí – podrobnosti výstrah](media/event-hubs-rp-manage/2-alerts-detail.png)](media/event-hubs-rp-manage/2-alerts-detail.png#lightbox)

Další informace o možnosti monitorování centra Azure Stack, včetně upozorňování, najdete v tématu [monitorování stavu a výstrah](azure-stack-monitor-health.md). Podrobnosti o shromažďování protokolů najdete v tématu [přehled Azure Stackho diagnostického protokolu](azure-stack-diagnostic-log-collection-overview.md).

## <a name="event-hubs-clusters"></a>Clustery Event Hubs

Výběrem **Event Hubs clustery** na hlavní stránce se zobrazí seznam dostupných uživatelských clusterů. Seznam obsahuje pro každý cluster následující:

- Informace o konfiguraci vysoké úrovně.
- Stav služby.
- Stav zálohování

[![spravovat centra událostí – uživatelské prostředky](media/event-hubs-rp-manage/4-user-resources.png)](media/event-hubs-rp-manage/4-user-resources.png#lightbox)

Když vyberete odkaz v části **stav** nebo **záloha** , zobrazí se podrobné informace o stavu Event Hubs stavu zálohování. Odkaz pod **názvem** zobrazí další podrobnosti o clusteru, včetně:
- Informace o stavu a konfiguraci.
- Seznam omezení služby pro cluster.

[![Správa centra událostí – uživatelské clustery](media/event-hubs-rp-manage/4-user-clusters.png)](media/event-hubs-rp-manage/4-user-clusters.png#lightbox)

Omezení služby jsou konfigurační parametry, které definují provozní hranice Event Hubs. Dostupná omezení služby jsou podobná těm, která jsou nabízená pro Azure Event Hubs úrovně Dedicated. Výběrem odkazů v části **konfigurační hodnota**můžete změnit přiřazenou hodnotu.

> [!IMPORTANT]
> Před změnou omezení služby byste měli věnovat čas analýze úplných dopadů. Změny omezení služby můžou mít vliv na chování vašeho řešení, které využívá a generuje události. Změny také mohou ovlivnit spotřebu prostředků z vaší Azure Stack kapacity.

## <a name="next-steps"></a>Další kroky

Další informace o:

- Typy kvót definované pro Event Hubs, které se projednávají s [typy kvót](azure-stack-quota-types.md#event-hubs-quota-types).
- Azure Stack možnosti monitorování centra, včetně upozorňování, najdete v tématu [monitorování stavu a výstrah](azure-stack-monitor-health.md). 
- Shromažďování protokolů centra Azure Stack najdete v tématu [přehled Azure Stackho diagnostického protokolu](azure-stack-diagnostic-log-collection-overview.md).













