---
author: mattbriggs
ms.service: azure-stack
ms.topic: include
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2018
ms.openlocfilehash: 23d08667c5047ddb3f29cf609baba68d2618448f
ms.sourcegitcommit: cad40ae88212cc72f40c84a1c88143ea0abb65ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84124338"
---
**Plánování** testu v pracovních postupech ověřování používá společné parametry na úrovni pracovního postupu, které jste zadali během vytváření pracovního postupu (viz [společné parametry pracovních postupů pro ověřování centra Azure Stack jako služba](../azure-stack-vaas-parameters.md)). Pokud se některá z hodnot parametrů testu stane neplatných, musíte je znovu zadat podle pokynů v části [Úprava parametrů pracovního postupu](../azure-stack-vaas-monitor-test.md#change-workflow-parameters).

> [!NOTE]
> Plánování ověřovacího testu přes existující instanci vytvoří na portálu novou instanci místo staré instance. Protokoly pro starou instanci budou zachovány, ale nebudou přístupné z portálu. Po úspěšném dokončení testu se akce **plánu** zakáže.

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](azure-stack-vaas-workflow-step_select-agent.md)]

1. V místní nabídce vyberte **plán** a otevřete tak výzvu pro plánování testovací instance.

1. Zkontrolujte parametry testu a pak vyberte **Odeslat** a naplánujte test pro spuštění.