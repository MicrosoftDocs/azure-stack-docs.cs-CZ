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
ms.openlocfilehash: b0a4e18afdc8ce68ffa212bcc7bc78506420c235
ms.sourcegitcommit: dd53af1b0fc2390de162d41e3d59545d1baad1a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80424243"
---
# <a name="event-hubs-on-azure-stack-hub-overview"></a>Přehled Event Hubs v centru Azure Stack

[!INCLUDE [preview-banner](../includes/event-hubs-preview.md)]

Event Hubs v centru Azure Stack vám umožní realizovat hybridní cloudové scénáře. Podpora streamování a řešení založeného na událostech je podporovaná pro místní i cloudové zpracování Azure. Bez ohledu na to, jestli je váš scénář hybridní (připojený) nebo odpojený, může vaše řešení podporovat zpracování událostí nebo datových proudů ve velkém měřítku. Váš scénář je vázaný jenom na velikost clusteru Event Hubs, kterou můžete zřídit podle svých potřeb. 

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

Kromě toho je možné provádět operace správy prostředků Azure pomocí Azure Resource Manager šablon, [PowerShellu](/powershell/module/azurerm.eventhub/)a rozhraní příkazového [řádku Azure CLI](/cli/azure/eventhubs/eventhub/). V současné době není v PowerShellu a v Azure CLI dostupná podpora pro operace správy operátorů.

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
