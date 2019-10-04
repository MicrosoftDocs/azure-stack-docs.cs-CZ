---
author: mattbriggs
ms.service: azure-stack
ms.topic: include
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2018
ms.openlocfilehash: c3a85e866acc615d2d33219232f7890b32df50f5
ms.sourcegitcommit: a7207f4a4c40d4917b63e729fd6872b3dba72968
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2019
ms.locfileid: "71938718"
---
**Plánování** testu v pracovních postupech ověřování používá společné parametry na úrovni pracovního postupu, které jste zadali během vytváření pracovního postupu (viz [společné parametry pracovních postupů pro Azure Stack ověřování jako služby](../azure-stack-vaas-parameters.md)). Pokud se některá z hodnot parametrů testu stane neplatných, musíte je znovu zadat podle pokynů v části [Úprava parametrů pracovního postupu](../azure-stack-vaas-monitor-test.md#change-workflow-parameters).

> [!NOTE]
> Plánování ověřovacího testu přes existující instanci vytvoří na portálu novou instanci místo staré instance. Protokoly pro starou instanci budou zachovány, ale nebudou přístupné z portálu.  
Po úspěšném dokončení testu se akce **plánu** zakáže.

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](azure-stack-vaas-workflow-step_select-agent.md)]

1. V místní nabídce vyberte **plán** a otevřete tak výzvu pro plánování testovací instance.

1. Zkontrolujte parametry testu a pak vyberte **Odeslat** a naplánujte test pro spuštění.