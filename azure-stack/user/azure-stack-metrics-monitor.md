---
title: Spotřebovat data monitorování z Azure Stack | Microsoft Docs
description: Seznamte se s možnostmi pro využívání dat monitorování z Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2019
ms.author: mabrigg
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 20e4f71480aa377e56115c499f96492e768010c3
ms.sourcegitcommit: 102ef41963b5d2d91336c84f2d6af3fdf2ce11c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73955743"
---
# <a name="how-to-consume-monitoring-data-from-azure-stack"></a>Jak spotřebovat data monitorování z Azure Stack

*Platí pro: Azure Stack integrovaných systémů*

Data monitorování se nacházejí v jednom umístění s kanálem Azure Monitor, stejně jako Azure Monitor v globálním Azure. Ale ne všechna data monitorování, která najdete v globálním Azure, jsou k dispozici v Azure Stack. V tomto článku poskytujeme přehled různých způsobů využívání dat monitorování v Azure Stack.
 
## <a name="options-for-data-consumption"></a>Možnosti pro spotřebu dat

| Data type | Kategorie | Podporované služby | Metody přístupu |
|-------------------------------------------------------------|----------|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Azure Monitor metriky na úrovni platformy | Metriky | [Podporované metriky s Azure Monitor v Azure Stack](azure-stack-metrics-supported.md) | Rozhraní REST API |
| Metriky pro operační systémy služby COMPUTE pro výpočty (například čítač výkonu) | Metriky | Virtuální počítače se systémem Windows a Linux | Tabulka nebo objekt BLOB úložiště:<br>Azure Diagnostics systému Windows nebo Linux <br>Centrum událostí:<br>Diagnostika Azure pro Windows |
| Metriky úložiště | Metriky | Azure Storage | Tabulka úložiště:<br>Storage Analytics |
| Protokol aktivit | Akce | Všechny služby Azure | REST API:<br>Rozhraní API pro Azure Monitor události |
| Protokoly hostovaného operačního systému služby COMPUTE (například IIS, ETW, syslogs) | Akce | Virtuální počítače se systémem Windows a Linux | Tabulka nebo objekt BLOB úložiště:<br>Azure Diagnostics systému Windows nebo Linux <br>Centrum událostí:<br>Diagnostika Azure pro Windows |
| Protokoly úložiště | Akce | Azure Storage | Tabulka úložiště:<br>Storage Analytics |

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [službě Azure monitor na Azure Stack](azure-stack-metrics-azure-data.md).
