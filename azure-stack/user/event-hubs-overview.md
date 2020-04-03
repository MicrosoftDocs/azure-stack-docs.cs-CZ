---
title: Přehled Event Hubs v centru Azure Stack
description: Přečtěte si o poskytovateli prostředků Event Hubs v centru Azure Stack.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 11/27/2019
ms.reviewer: jfggdl
ms.lastreviewed: 11/27/2019
ms.openlocfilehash: c7c1d11d2ef432ddc8b750fde5cb6970c4076db4
ms.sourcegitcommit: 48e493256b0b8bd6cea931cd68a9bd932ca77090
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80614374"
---
# <a name="event-hubs-on-azure-stack-hub-overview"></a>Přehled Event Hubs v centru Azure Stack

[!INCLUDE [preview-banner](../includes/event-hubs-preview.md)]

Event Hubs v centru Azure Stack vám umožní realizovat hybridní cloudové scénáře. Podpora streamování a řešení založeného na událostech je podporovaná pro místní i cloudové zpracování Azure. Bez ohledu na to, jestli je váš scénář hybridní (připojený) nebo odpojený, může vaše řešení podporovat zpracování událostí nebo datových proudů ve velkém měřítku. Váš scénář je vázaný jenom na velikost clusteru Event Hubs, kterou můžete zřídit podle svých potřeb. 

## <a name="run-event-processing-tasks-and-build-event-driven-applications-on-site"></a>Spuštění úloh zpracování událostí a sestavování aplikací řízených událostmi v lokalitě

Pomocí Event Hubs v centru Azure Stack můžete implementovat obchodní scénáře, jako například:

- Úlohy AI a Machine Learning, kde Event Hubs je modul streamování událostí.
- Implementujte architektury založené na událostech ve vašich vlastních lokalitách mimo datová centra Azure.
- Navštívených Analytics pro vaše webové aplikace nasazené místně.
- Analýza telemetrie zařízení.
- Zpracování datových proudů pomocí Open Source rozhraní, které používá Apache Kafka, jako jsou například Apache Spark, Flink, Samza, a.
- Využijte [výpočetní metriky a události operačního systému v hostovaném](azure-stack-metrics-monitor.md)prostředí.

## <a name="build-hybrid-solutions"></a>Sestavování hybridních řešení

Sestavujte hybridní řešení, která ingestují a zpracovávají data Edge místně v centru Azure Stack. Odešlete agregovaná data do Azure pro další zpracování, vizualizaci a úložiště. V případě potřeby Využijte výpočetní prostředí bez serveru v Azure.

[Diagram ![hybridních řešení](media/event-hubs-overview/hybrid-architecture-ehoash.png)](media/event-hubs-overview/hybrid-architecture-ehoash.png#lightbox)

## <a name="features"></a>Funkce 

Edice Event Hubs (v centru Azure Stack a v Azure) nabízejí vysoký stupeň parity funkcí. Tato parita znamená, že sady SDK, ukázky, PowerShellu, rozhraní příkazového řádku a portálů nabízejí podobné prostředí s několika rozdíly. Následující tabulka shrnuje rozdíly vysoké úrovně v dostupnosti funkcí, které existují mezi edicemi.  

| Funkce | Event Hubs v centru Azure Stack | Azure Event Hubs |
|-|-|-|-|
| Činnost správce operátorů | ✔ | ✘ |
| Podpora Kafka | ✔ | ✔ |
| Stejná sada sad SDK | ✔ | ✔ |
| Autorizace přístupu k Event Hubs pomocí Azure Active Directory | ✘ | ✔ |
| Funkce Capture | ✘ | ✔ |
| Geografické zotavení po havárii | ✘ | ✔ |
| Azure Monitor | ✔ | ✔ |
| Funkce Automatické deflace | ✘ | ✔ |

Operace správy prostředků Azure je možné provádět taky pomocí Azure Resource Manager šablon, [PowerShellu](/powershell/module/azurerm.eventhub/)a rozhraní příkazového [řádku Azure CLI](/cli/azure/eventhubs/eventhub/). V současné době není v PowerShellu a v Azure CLI dostupná podpora pro operace správy operátorů.

## <a name="feature-documentation"></a>Dokumentace k funkcím

[Dokumentace k Azure Event Hubs](/azure/event-hubs/) se vztahuje na obě edice Event Hubs: Event Hubs v centru pro Azure Stack a v Azure Event Hubs. Tato dokumentace obsahuje témata týkající se používání Event Hubs a aktivit, jako jsou:

- Podrobnosti o [Event Hubs konceptech](/azure/event-hubs/event-hubs-features)
- Postup [Vytvoření clusteru Event Hubs a oboru názvů](event-hubs-quickstart-cluster-portal.md)
- Vytvoření [centra událostí](/azure/event-hubs/event-hubs-create#create-an-event-hub)
- Postup při streamování [pomocí protokolu Kafka](/azure/event-hubs/event-hubs-quickstart-kafka-enabled-event-hubs)

### <a name="operator-documentation"></a>Dokumentace k operátorovi 
 
Další informace o Event Hubs v prostředí operátorů centra Azure Stack najdete v [dokumentaci k operátoru Event Hubs](/azure-stack/operator/event-hubs-rp-overview). Tato dokumentace poskytuje informace o aktivitách, jako jsou:

- Probíhá instalace Event Hubs.
- Zpřístupnění Event Hubs uživatelům.
- Získání informací o stavu služby.
- Shromažďování protokolů.


## <a name="next-steps"></a>Další kroky

Pokud ve vašem předplatném není Event Hubs k dispozici, spolupracujte se svým správcem a [nainstalujte Event Hubs do poskytovatele prostředků služby Azure Stack hub](../operator/event-hubs-rp-overview.md).

Pokud jste nainstalovali Event Hubs a jste připraveni ho použít, přečtěte si další informace o této službě v [dokumentaci k Event Hubs](/azure/event-hubs/event-hubs-about) .
