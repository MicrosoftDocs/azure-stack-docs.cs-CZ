---
title: Odeslat protokoly diagnostiky centra Azure Stack hned
description: Naučte se shromažďovat diagnostické protokoly na vyžádání v centru Azure Stack pomocí portálu pro správu nebo skriptu PowerShellu.
author: justinha
ms.topic: article
ms.date: 03/30/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 03/30/2020
ms.openlocfilehash: 2c741440deb92be81497b34a7e485d4628501100
ms.sourcegitcommit: 5f4f0ee043ff994efaad44129ce49be43c64d5dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/16/2020
ms.locfileid: "84819395"
---
# <a name="send-azure-stack-hub-diagnostic-logs-now"></a>Odeslat protokoly diagnostiky centra Azure Stack hned

::: moniker range=">= azs-2002"

Azure Stack operátory mohou odesílat diagnostické protokoly na vyžádání podpora Microsoftu a teprve potom požádat o podporu pomocí portálu pro správu nebo PowerShellu. Pokud je centrum Azure Stack připojené k Azure, doporučuje se možnost **Odeslat protokoly nyní** na portálu pro správu, protože se jedná o nejjednodušší způsob, jak odesílat protokoly přímo do Microsoftu. Pokud portál není dostupný, operátory by místo toho měli [odesílat protokoly pomocí Send-AzureStackDiagnosticLog](azure-stack-configure-on-demand-diagnostic-log-collection-powershell-tzl.md). 

Pokud jste odpojeni z Internetu nebo chcete ukládat pouze místní protokoly, použijte k odeslání protokolů metodu [Get-AzureStackLog](azure-stack-get-azurestacklog.md) . Následující vývojový diagram znázorňuje, kterou možnost použít pro odesílání diagnostických protokolů v každém případě. 

![Vývojový diagram ukazuje, jak teď odesílat protokoly do Microsoftu.](media/azure-stack-help-and-support/send-logs-now-flowchart.png)

>[!NOTE]
>Jako alternativu ke shromažďování protokolů na vyžádání můžete zjednodušit proces řešení potíží tím, že [proaktivně shromažďujete diagnostické protokoly](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md). Pokud je potřeba prozkoumat stav systému, protokoly se nahrají automaticky pro analýzu před otevřením případu s podpora Microsoftu. Pokud je zapnuté proaktivní shromažďování protokolů, **pomoc a podpora** se zobrazí, když probíhá shromažďování protokolů. Pokud teď kliknete na **Odeslat protokoly** a shromažďovat protokoly z určitého času, zatímco probíhá proaktivní shromažďování protokolů, začne shromažďování na vyžádání po dokončení proaktivní shromažďování protokolů.

Zadejte čas spuštění a čas ukončení shromažďování protokolů a klikněte na tlačítko **shromáždit a nahrát**. 

![Snímek obrazovky s možností poslat protokoly hned](media/azure-stack-help-and-support/send-logs-now.png)


::: moniker-end
::: moniker range="<= azs-1910"
## <a name="use-help-and-support-to-collect-diagnostic-logs-on-demand"></a>Použití pomoci a podpory ke shromažďování diagnostických protokolů na vyžádání

Pro řešení problému může podpora Microsoftu požádat o operátor centra Azure Stack, aby shromáždil diagnostické protokoly na vyžádání pro konkrétní časový interval z předchozího týdne. V takovém případě podpora Microsoftu poskytne operátor s adresou URL SAS pro nahrání kolekce. 
Pomocí následujících kroků proveďte konfiguraci shromažďování protokolů na vyžádání pomocí adresy URL SAS z podpora Microsoftu:

1. Otevřete okno **pomoc a podpora – přehled** a hned klikněte na **shromáždit protokoly**. 
1. Vyberte okno posunuté z 1-4 hodin za posledních sedm dní. 
1. Vyberte místní časové pásmo.
1. Zadejte adresu URL SAS, kterou podpora Microsoftu poskytnout.

   ![Snímek obrazovky shromažďování protokolů na vyžádání](media/azure-stack-automatic-log-collection/collect-logs-now.png)

>[!NOTE]
>Pokud je povolená funkce automatického shromažďování protokolů diagnostiky, zobrazí se v **nápovědě a podpoře** , když probíhá shromažďování protokolů. Pokud kliknete na **shromažďovat protokoly** a shromažďovat protokoly z konkrétního okamžiku, když probíhá automatické shromažďování protokolů, začne shromažďování na vyžádání po dokončení automatického shromažďování protokolů. 


::: moniker-end


## <a name="next-steps"></a>Další kroky

[Použití privilegovaného koncového bodu (PEP) k odesílání diagnostických protokolů centra Azure Stack](azure-stack-configure-on-demand-diagnostic-log-collection-powershell-tzl.md)

