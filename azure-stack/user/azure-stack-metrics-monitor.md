---
title: Využití dat monitorování z centra Azure Stack
description: Seznamte se s možnostmi pro využívání dat monitorování z centra Azure Stack.
author: mattbriggs
ms.topic: article
ms.date: 08/24/2020
ms.author: mabrigg
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 7d45b0bf71914ab8344fa162c6758f7914474495
ms.sourcegitcommit: 65a115d1499b5fe16b6fe1c31cce43be21d05ef8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/25/2020
ms.locfileid: "88818942"
---
# <a name="consume-monitoring-data-from-azure-stack-hub"></a>Využití dat monitorování z centra Azure Stack

Data monitorování se nacházejí v jednom umístění s kanálem Azure Monitor, stejně jako Azure Monitor v globálním Azure. Ale ne všechna data monitorování, která najdete v globálním Azure, jsou k dispozici v Azure Stack hub. V tomto článku poskytujeme přehled různých způsobů využívání dat monitorování v centru Azure Stack.
 
## <a name="options-for-data-consumption"></a>Možnosti pro spotřebu dat

| Datový typ | Kategorie | Podporované služby | Metody přístupu |
|-------------------------------------------------------------|----------|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Azure Monitor metriky na úrovni platformy | Metriky | [Podporované metriky s Azure Monitor v centru Azure Stack](azure-stack-metrics-supported.md) | REST API |
| Metriky pro operační systémy služby COMPUTE pro výpočty (například čítač výkonu) | Metriky | Virtuální počítače se systémem Windows a Linux | Tabulka nebo objekt BLOB úložiště:<br>Azure Diagnostics systému Windows nebo Linux <br>Centrum událostí:<br>Diagnostika Azure pro Windows |
| Metriky úložiště | Metriky | Azure Storage | Tabulka úložiště:<br>Storage Analytics |
| Protokol aktivit | Události | Všechny služby Azure | REST API:<br>Rozhraní API pro Azure Monitor události |
| Protokoly hostovaného operačního systému služby COMPUTE (například IIS, ETW, syslogs) | Události | Virtuální počítače se systémem Windows a Linux | Tabulka nebo objekt BLOB úložiště:<br>Azure Diagnostics systému Windows nebo Linux <br>Centrum událostí:<br>Diagnostika Azure pro Windows |
| Protokoly úložiště | Události | Azure Storage | Tabulka úložiště:<br>Storage Analytics |

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [službě Azure monitor v centru Azure Stack](azure-stack-metrics-azure-data.md).
