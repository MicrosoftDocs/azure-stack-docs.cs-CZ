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
ms.date: 07/23/2019
ms.author: mabrigg
ms.lastreviewed: 12/01/2018
ms.openlocfilehash: 64935022f8ace33bd4350ab4ef5421636ff3991e
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68418504"
---
# <a name="how-to-consume-monitoring-data-from-azure-stack"></a>Jak spotřebovat data monitorování z Azure Stack

*Platí pro: Azure Stack integrovaných systémů*

Data monitorování se nacházejí v jednom umístění s kanálem Azure Monitor, stejně jako Azure Monitor v globálním Azure. Ale ne všechna data monitorování, která najdete v globálním Azure, jsou k dispozici v Azure Stack. V tomto článku poskytujeme přehled různých způsobů využívání dat monitorování v Azure Stack.
 
## <a name="options-for-data-consumption"></a>Možnosti pro spotřebu dat

| Datový typ | Kategorie | Podporované služby | Metody přístupu |
|-------------------------------------------------------------|----------|------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------|
| Azure Monitor metriky na úrovni platformy | Metriky | [Podporované metriky s Azure Monitor v Azure Stack](azure-stack-metrics-supported.md) | REST API |
| Metriky pro operační systémy služby COMPUTE pro výpočty (například čítač výkonu) | Metriky | Virtuální počítače se systémem Windows a Linux | Tabulka nebo objekt BLOB úložiště:<br>Azure Diagnostics systému Windows nebo Linux <br>Centrum událostí:<br>Diagnostika Azure pro Windows |
| Metriky úložiště | Metriky | Azure Storage | Tabulka úložiště:<br>Storage Analytics |
| Protokol aktivit | Duration | Všechny služby Azure | REST API:<br>Rozhraní API pro Azure Monitor události |
| Protokoly hostovaného operačního systému služby COMPUTE (například IIS, ETW, syslogs) | Duration | Virtuální počítače se systémem Windows a Linux | Tabulka nebo objekt BLOB úložiště:<br>Azure Diagnostics systému Windows nebo Linux <br>Centrum událostí:<br>Diagnostika Azure pro Windows |
| Protokoly úložiště | Duration | Azure Storage | Tabulka úložiště:<br>Storage Analytics |

## <a name="next-steps"></a>Další postup

Další informace o [monitorování Azure ve službě Azure Stack](azure-stack-metrics-azure-data.md).
