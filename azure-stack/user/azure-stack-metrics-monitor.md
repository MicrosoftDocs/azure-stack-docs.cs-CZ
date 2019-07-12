---
title: Používat data monitorování z Azure Stack | Dokumentace Microsoftu
description: Získejte informace o možnostech využívání data monitorování z Azure Stack.
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
ms.date: 04/15/2019
ms.author: mabrigg
ms.lastreviewed: 12/01/2018
ms.openlocfilehash: 0b04a7834218d07706e6314a9c41e0370ff6f6fd
ms.sourcegitcommit: b36d078e699c7924624b79641dbe9021af9606ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67816191"
---
# <a name="how-to-consume-monitoring-data-from-azure-stack"></a>Jak používat data monitorování z Azure Stack

*Platí pro: Integrované systémy Azure Stack*

Data monitorování se nachází na jednom místě s kanálem monitorování Azure, stejně jako Azure Monitor v globální Azure. Ale ne všechny data monitorování, které jsou součástí globální Azure je k dispozici ve službě Azure Stack. Tento článek poskytuje přehled různých způsobů využívání dat monitorování v Azure stacku.
 
## <a name="options-for-data-consumption"></a>Možnosti využití dat

| Datový typ | Kategorie | Podporované služby | Metody přístupu |
|-------------------------------------------------------------|----------|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Metriky Azure Monitor úrovni platformy | Metriky | [Podporované metriky ve službě Azure Monitor ve službě Azure Stack](azure-stack-metrics-supported.md) | REST API |
| Vypočítat metriky hostovaného operačního systému (například počet výk.) | Metriky | Windows a virtuální počítače s Linuxem | Storage table nebo blob:<br>Windows nebo Linux Azure Diagnostics <br>Centrum událostí:<br>Diagnostika Azure pro Windows |
| Metriky úložiště | Metriky | Azure Storage | Tabulka úložiště:<br>Storage Analytics |
| Protokol aktivit | Duration | Všechny služby Azure | REST API:<br>Azure Monitor událostí rozhraní API |
| COMPUTE protokoly operačního systému hosta (například služby IIS, trasování událostí pro Windows, syslogy) | Duration | Windows a virtuální počítače s Linuxem | Storage table nebo blob:<br>Windows nebo Linux Azure Diagnostics <br>Centrum událostí:<br>Diagnostika Azure pro Windows |
| Protokoly úložiště | Duration | Azure Storage | Tabulka úložiště:<br>Storage Analytics |

## <a name="next-steps"></a>Další kroky

Další informace o [monitorování Azure ve službě Azure Stack](azure-stack-metrics-azure-data.md).
