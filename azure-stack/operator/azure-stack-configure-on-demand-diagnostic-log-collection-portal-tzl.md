---
title: Odeslat protokoly diagnostiky centra Azure Stack hned
description: Naučte se shromažďovat diagnostické protokoly na vyžádání v centru Azure Stack pomocí portálu pro správu nebo skriptu PowerShellu.
author: justinha
ms.topic: article
ms.date: 02/26/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 02/26/2020
ms.openlocfilehash: fb79a6378d2dec69804019b3ab0648ce874bf99d
ms.sourcegitcommit: 53efd12bf453378b6a4224949b60d6e90003063b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2020
ms.locfileid: "79520306"
---
# <a name="send-azure-stack-hub-diagnostic-logs-now"></a>Odeslat protokoly diagnostiky centra Azure Stack hned

Operátoři Azure Stack můžou odesílat diagnostické protokoly na vyžádání do služeb Microsoft Customer Support Services (CSS) před vyžádáním podpory pomocí portálu pro správu nebo PowerShellu. Pokud je centrum Azure Stack připojené k Azure, doporučuje se možnost **Odeslat protokoly nyní** na portálu pro správu, protože se jedná o nejjednodušší způsob, jak odesílat protokoly přímo do Microsoftu. Pokud portál není dostupný, operátory by místo toho měli [odesílat protokoly pomocí PowerShellu](azure-stack-configure-on-demand-diagnostic-log-collection-powershell-tzl.md). 

Pokud jste odpojeni z Internetu nebo chcete ukládat pouze místní protokoly, použijte k odeslání protokolů metodu [Get-AzureStackLog](azure-stack-get-azurestacklog.md) . Následující vývojový diagram znázorňuje, kterou možnost použít pro odesílání diagnostických protokolů v každém případě. 

![Vývojový diagram ukazuje, jak teď odesílat protokoly do Microsoftu.](media/azure-stack-help-and-support/send-logs-now-flowchart.png)

>[!NOTE]
>Jako alternativu ke shromažďování protokolů na vyžádání můžete zjednodušit proces řešení potíží tím, že [proaktivně shromažďujete diagnostické protokoly](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md). Pokud je potřeba prozkoumat stav systému, protokoly se nahrají automaticky pro analýzu před otevřením případu s CSS. Pokud je zapnuté proaktivní shromažďování protokolů, **pomoc a podpora** se zobrazí, když probíhá shromažďování protokolů. Pokud teď kliknete na **Odeslat protokoly** a shromažďovat protokoly z určitého času, zatímco probíhá proaktivní shromažďování protokolů, začne shromažďování na vyžádání po dokončení proaktivní shromažďování protokolů.

Zadejte čas spuštění a čas ukončení shromažďování protokolů a klikněte na tlačítko **shromáždit a nahrát**. 

![Snímek obrazovky s možností poslat protokoly hned](media/azure-stack-help-and-support/send-logs-now.png)

## <a name="next-steps"></a>Další kroky

[Použití privilegovaného koncového bodu (PEP) k odesílání diagnostických protokolů centra Azure Stack](azure-stack-get-azurestacklog.md)
