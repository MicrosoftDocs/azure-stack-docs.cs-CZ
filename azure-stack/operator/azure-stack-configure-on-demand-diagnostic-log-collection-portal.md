---
title: Odeslat protokoly diagnostiky centra Azure Stack hned
description: Naučte se shromažďovat diagnostické protokoly na vyžádání v centru Azure Stack pomocí portálu pro správu nebo skriptu PowerShellu.
author: myoungerman
ms.topic: article
ms.date: 08/24/2020
ms.author: v-myoung
ms.reviewer: shisab
ms.lastreviewed: 08/24/2020
ms.openlocfilehash: 3d4725a832aa1ba88c0ecc5e2b435696cba3bbcd
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90573152"
---
# <a name="send-azure-stack-hub-diagnostic-logs-now"></a>Odeslat protokoly diagnostiky centra Azure Stack hned

::: moniker range=">= azs-2002"

Azure Stack operátory mohou odesílat diagnostické protokoly na vyžádání podpora Microsoftu a teprve potom požádat o podporu pomocí portálu pro správu nebo PowerShellu. Pokud je centrum Azure Stack připojené k Azure, doporučuje se možnost **Odeslat protokoly nyní** na portálu pro správu, protože se jedná o nejjednodušší způsob, jak odesílat protokoly přímo do Microsoftu. Pokud portál není dostupný, operátory by místo toho měli [odesílat protokoly pomocí Send-AzureStackDiagnosticLog](./azure-stack-configure-on-demand-diagnostic-log-collection-powershell.md?view=azs-2002). 

Pokud jste odpojeni z Internetu nebo chcete ukládat pouze místní protokoly, použijte k odeslání protokolů metodu [Get-AzureStackLog](azure-stack-get-azurestacklog.md) . Následující vývojový diagram znázorňuje, kterou možnost použít pro odesílání diagnostických protokolů v každém případě. 

![Vývojový diagram ukazuje, jak teď odesílat protokoly do Microsoftu.](media/azure-stack-help-and-support/send-logs-now-flowchart.png)

>[!NOTE]
>Jako alternativu ke shromažďování protokolů na vyžádání můžete zjednodušit proces řešení potíží tím, že [proaktivně shromažďujete diagnostické protokoly](./azure-stack-configure-automatic-diagnostic-log-collection.md?view=azs-2002). Pokud je potřeba prozkoumat stav systému, protokoly se nahrají automaticky pro analýzu před otevřením případu s podpora Microsoftu. Pokud je zapnuté proaktivní shromažďování protokolů, **pomoc a podpora** se zobrazí, když probíhá shromažďování protokolů. Pokud teď kliknete na **Odeslat protokoly** a shromažďovat protokoly z určitého času, zatímco probíhá proaktivní shromažďování protokolů, začne shromažďování na vyžádání po dokončení proaktivní shromažďování protokolů.

Zadejte čas spuštění a čas ukončení shromažďování protokolů a klikněte na tlačítko **shromáždit a nahrát**. 

![Snímek obrazovky s možností poslat protokoly hned](media/azure-stack-help-and-support/send-logs-now.png)


::: moniker-end
::: moniker range=">= azs-2005"
## <a name="save-logs-locally"></a>Místní uložení protokolů

Protokoly můžete ukládat do místní sdílené složky SMB, když je rozbočovač Azure Stack odpojený od Azure. V okně **Nastavení** zadejte cestu a uživatelské jméno a heslo, které mají oprávnění k zápisu do sdílené složky. Během případu podpory vám podpora Microsoftu poskytne podrobné pokyny, jak přenést tyto místní protokoly. Pokud portál správce není k dispozici, můžete použít [příkaz Get-AzureStackLog](azure-stack-get-azurestacklog.md) k místnímu uložení protokolů.

![Snímek obrazovky s možnostmi shromažďování diagnostických protokolů](media/azure-stack-help-and-support/save-logs-locally.png)

::: moniker-end
::: moniker range="<= azs-1910"
## <a name="use-help-and-support-to-collect-diagnostic-logs-on-demand"></a>Použití pomoci a podpory ke shromažďování diagnostických protokolů na vyžádání

Pro řešení problému může podpora Microsoftu požádat o operátor centra Azure Stack, aby shromáždil diagnostické protokoly na vyžádání pro konkrétní časový interval z předchozího týdne. V takovém případě podpora Microsoftu poskytne operátor s adresou URL SAS pro nahrání kolekce. 
Pomocí následujících kroků proveďte konfiguraci shromažďování protokolů na vyžádání pomocí adresy URL SAS z podpora Microsoftu:

1. Otevřete okno **help + podpora >ho protokolu > shromažďování protokolů nyní**. 
1. Vyberte okno posunuté z 1-4 hodin za posledních sedm dní. 
1. Vyberte místní časové pásmo.
1. Zadejte adresu URL SAS, kterou podpora Microsoftu poskytnout.

   ![Snímek obrazovky shromažďování protokolů na vyžádání](media/azure-stack-automatic-log-collection/collect-logs-now.png)

>[!NOTE]
>Pokud je povolená funkce automatického shromažďování protokolů diagnostiky, zobrazí se v **nápovědě a podpoře** , když probíhá shromažďování protokolů. Pokud kliknete na **shromažďovat protokoly** a shromažďovat protokoly z konkrétního okamžiku, když probíhá automatické shromažďování protokolů, začne shromažďování na vyžádání po dokončení automatického shromažďování protokolů. 


::: moniker-end


## <a name="next-steps"></a>Další kroky

[Použití privilegovaného koncového bodu (PEP) k odesílání diagnostických protokolů centra Azure Stack](./azure-stack-configure-on-demand-diagnostic-log-collection-powershell.md?view=azs-2002)
