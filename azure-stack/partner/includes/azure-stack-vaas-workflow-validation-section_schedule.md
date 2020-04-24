---
author: mattbriggs
ms.service: azure-stack
ms.topic: include
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2018
ms.openlocfilehash: 4d428e13b00a6f941ea9d6967adce5e1881cb7c1
ms.sourcegitcommit: bdd4d529bd3e115a9f76eece62b1613448d5d020
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "77625423"
---
**Plánování** testu v pracovních postupech ověřování používá společné parametry na úrovni pracovního postupu, které jste zadali během vytváření pracovního postupu (viz [společné parametry pracovních postupů pro ověřování centra Azure Stack jako služba](../azure-stack-vaas-parameters.md)). Pokud se některá z hodnot parametrů testu stane neplatných, musíte je znovu zadat podle pokynů v části [Úprava parametrů pracovního postupu](../azure-stack-vaas-monitor-test.md#change-workflow-parameters).

> [!NOTE]
> Plánování ověřovacího testu přes existující instanci vytvoří na portálu novou instanci místo staré instance. Protokoly pro starou instanci budou zachovány, ale nebudou přístupné z portálu. Po úspěšném dokončení testu se akce **plánu** zakáže.

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](azure-stack-vaas-workflow-step_select-agent.md)]

1. V místní nabídce vyberte **plán** a otevřete tak výzvu pro plánování testovací instance.

1. Zkontrolujte parametry testu a pak vyberte **Odeslat** a naplánujte test pro spuštění.