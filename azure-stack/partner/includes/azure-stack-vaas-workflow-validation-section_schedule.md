---
author: mattbriggs
ms.service: azure-stack
ms.topic: include
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2018
ms.openlocfilehash: 15afed256db592def52f06f8507bcb266d2ad9d3
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97874777"
---
**Plánování** testu v pracovních postupech ověřování používá společné parametry na úrovni pracovního postupu, které jste zadali během vytváření pracovního postupu (viz [společné parametry pracovních postupů pro ověřování centra Azure Stack jako služba](../azure-stack-vaas-parameters.md)). Pokud se některá z hodnot parametrů testu stane neplatných, musíte je znovu zadat podle pokynů v části [Úprava parametrů pracovního postupu](../azure-stack-vaas-monitor-test.md#change-workflow-parameters).

> [!NOTE]
> Plánování ověřovacího testu přes existující instanci vytvoří na portálu novou instanci místo staré instance. Protokoly pro starou instanci budou zachovány, ale nebudou přístupné z portálu. Po úspěšném dokončení testu se akce **plánu** zakáže.

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](azure-stack-vaas-workflow-step_select-agent.md)]

1. V místní nabídce vyberte **plán** a otevřete tak výzvu pro plánování testovací instance.

1. Zkontrolujte parametry testu a pak vyberte **Odeslat** a naplánujte test pro spuštění.