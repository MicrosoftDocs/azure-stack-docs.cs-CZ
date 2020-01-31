---
title: Využití dat monitorování z centra Azure Stack
description: Seznamte se s možnostmi pro využívání dat monitorování z centra Azure Stack.
author: mattbriggs
ms.topic: article
ms.date: 11/11/2019
ms.author: mabrigg
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: bb03193c1c4b061a996e3bef135c6cfc630f36b0
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76883419"
---
# <a name="consume-monitoring-data-from-azure-stack-hub"></a>Využití dat monitorování z centra Azure Stack

Data monitorování se nacházejí v jednom umístění s kanálem Azure Monitor, stejně jako Azure Monitor v globálním Azure. Ale ne všechna data monitorování, která najdete v globálním Azure, jsou k dispozici v Azure Stack hub. V tomto článku poskytujeme přehled různých způsobů využívání dat monitorování v centru Azure Stack.
 
## <a name="options-for-data-consumption"></a>Možnosti pro spotřebu dat

| Data type | Kategorie | Podporované služby | Metody přístupu |
|-------------------------------------------------------------|----------|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Azure Monitor metriky na úrovni platformy | Metriky | [Podporované metriky s Azure Monitor v centru Azure Stack](azure-stack-metrics-supported.md) | Rozhraní REST API |
| Metriky pro operační systémy služby COMPUTE pro výpočty (například čítač výkonu) | Metriky | Virtuální počítače se systémem Windows a Linux | Tabulka nebo objekt BLOB úložiště:<br>Azure Diagnostics systému Windows nebo Linux <br>Centrum událostí:<br>Diagnostika Azure pro Windows |
| Metriky úložiště | Metriky | Azure Storage | Tabulka úložiště:<br>Storage Analytics |
| Protokol aktivit | Akce | Všechny služby Azure | REST API:<br>Rozhraní API pro Azure Monitor události |
| Protokoly hostovaného operačního systému služby COMPUTE (například IIS, ETW, syslogs) | Akce | Virtuální počítače se systémem Windows a Linux | Tabulka nebo objekt BLOB úložiště:<br>Azure Diagnostics systému Windows nebo Linux <br>Centrum událostí:<br>Diagnostika Azure pro Windows |
| Protokoly úložiště | Akce | Azure Storage | Tabulka úložiště:<br>Storage Analytics |

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [službě Azure monitor v centru Azure Stack](azure-stack-metrics-azure-data.md).
