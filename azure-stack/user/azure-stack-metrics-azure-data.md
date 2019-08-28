---
title: Použít Azure Monitor na Azure Stack | Microsoft Docs
description: Naučte se používat Azure Monitor v Azure Stack.
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
ms.date: 08/27/2019
ms.author: mabrigg
ms.lastreviewed: 12/01/2018
ms.openlocfilehash: 88b07c5c3325263e068de49b78df25d17861a37e
ms.sourcegitcommit: f1a21af6517978ddb62f4cbfa1d1df8c867814d1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70064179"
---
# <a name="use-azure-monitor-on-azure-stack"></a>Použít Azure Monitor na Azure Stack

*Platí pro: Azure Stack integrovaných systémů*

Tento článek poskytuje přehled služby Azure Monitor v Azure Stack. Popisuje provoz Azure Monitor a další informace o tom, jak používat Azure Monitor na Azure Stack. 

Přehled Azure Monitor najdete v tématu věnovaném celosvětovému článku Azure [Začínáme s Azure monitor na Azure Stack](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-get-started).

![Okno monitorování Azure Stack](./media/azure-stack-metrics-azure-data/azs-monitor.png)

Azure Monitor je služba platformy, která poskytuje jeden zdroj pro monitorování prostředků Azure. Azure Monitor vám umožňuje vizualizovat, dotazovat, směrovat, archivovat a provádět další akce s metrikami a protokoly, které pocházejí z prostředků v Azure. S těmito daty můžete pracovat pomocí Azure Stack portálu pro správu, monitorovat rutiny PowerShellu, rozhraní příkazového řádku pro více platforem nebo rozhraní API pro Azure Monitor REST. Konkrétní připojení, které podporuje Azure Stack, najdete v tématu [jak spotřebovávat data monitorování z Azure Stack](azure-stack-metrics-monitor.md).

> [!Note]
> Metriky a diagnostické protokoly nejsou pro Azure Stack Development Kit k dispozici.

## <a name="prerequisites-for-azure-monitor-on-azure-stack"></a>Předpoklady pro Azure Monitor v Azure Stack

Zaregistrujte poskytovatele prostředků **Microsoft. Insights** v nastavení poskytovatelů prostředků nabídky vašeho předplatného. Můžete ověřit, jestli je poskytovatel prostředků dostupný v nabídce přidružené k vašemu předplatnému:

1. Otevřete portál Azure Stack User Portal.
2. Vyberte **nabídky**.
3. Vyberte nabídku přidruženou k předplatnému.
4. V části nastavení vyberte **poskytovatelé prostředků** **.** 
5. V seznamu vyhledejte **Microsoft. Insights** a ověřte, jestli je stav zaregistrovaný.

## <a name="overview-of-azure-monitor-on-azure-stack"></a>Přehled Azure Monitor v Azure Stack

Jako je Azure Monitor v Azure Azure Monitor ve službě Azure Stack poskytuje základní infrastruktura metriky a protokoly pro většina služeb.

## <a name="azure-monitor-sources-compute-subset"></a>Azure Monitor sources: COMPUTE – dílčí sada

![Azure Monitor na Azure Stack sources – COMPUTE – dílčí sada](media//azure-stack-metrics-azure-data/azs-monitor-computersubset.png)

Poskytovatel prostředků **Microsoft. COMPUTE** v Azure Stack zahrnuje:
 - Virtuální počítače 
 - Škálovací sady virtuálních počítačů

### <a name="application---diagnostics-logs-app-logs-and-metrics"></a>Application – protokoly diagnostiky, protokoly aplikací a metriky

Aplikace můžou běžet v operačním systému virtuálního počítače, který běží s poskytovatelem prostředků **Microsoft. COMPUTE** . Tyto aplikace a virtuální počítače emitují svoji vlastní sadu protokolů a metrik. Azure Monitor pro shromažďování většiny metrik a protokolů na úrovni aplikace spoléhá na rozšíření Azure Diagnostics (Windows nebo Linux).

Mezi typy měr patří:
 - Čítače výkonu
 - Protokoly aplikací
 - Protokoly událostí Windows
 - Zdroj události .NET
 - Protokoly IIS
 - Trasování událostí pro Windows na základě manifestu
 - Chybové výpisy
 - Protokoly chyb zákazníků

> [!Note]  
> Rozšíření diagnostiky Linux na Azure Stack se nepodporuje.

### <a name="host-and-guest-vm-metrics"></a>Metriky virtuálního počítače hostitele a hosta

Výše uvedené výpočetní prostředky mají vyhrazený hostitelský virtuální počítač a hostovaný operační systém. Hostitelský virtuální počítač a hostovaný operační systém jsou ekvivalentem kořenových virtuálních počítačů a virtuálních počítačů hosta v hypervisoru technologie Hyper-V. Metriky můžete shromažďovat pro virtuální počítač hostitele i pro hostovaný operační systém. Můžete také shromažďovat diagnostické protokoly pro hostovaný operační systém. Seznam metrik kolekční pro metriky virtuálního počítače hosta a hosta v Azure Stack je k dispozici v [podporovaných metrikách s Azure monitor na Azure Stack](azure-stack-metrics-supported.md). 

### <a name="activity-log"></a>Protokol aktivit

V protokolech aktivit můžete vyhledat informace o vašich výpočetních prostředcích, jak je vidět Azure Stack infrastruktury. Tento protokol obsahuje informace, jako jsou třeba časy, kdy se prostředky vytvořily nebo zničily. Protokoly aktivit v Azure Stack jsou konzistentní s Azure. Další informace najdete v tématu [Přehled protokolu aktivit v Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). 


## <a name="azure-monitor-sources-everything-else"></a>Zdroje Azure monitor: všechno ostatní

![Azure Monitor v Azure Stackch zdrojích – všechno ostatní](media//azure-stack-metrics-azure-data/azs-monitor-othersubset.png)

### <a name="resources---metrics-and-diagnostics-logs"></a>Prostředky – metriky a diagnostické protokoly

Metriky kolekční a protokoly diagnostiky se liší v závislosti na typu prostředku. Seznam metrik kolekční pro každý prostředek na Azure Stack je k dispozici na podporovaných metrikách. Další informace najdete v tématu [podporované metriky s Azure monitor v Azure Stack](azure-stack-metrics-supported.md).

### <a name="activity-log"></a>Protokol aktivit

Protokol aktivit je stejný pro výpočetní prostředky. 

### <a name="uses-for-monitoring-data"></a>Používá se pro monitorování dat.

**Ukládání a archivace**  

Některá data monitorování se už v Azure Monitoru uchovávají a jsou po určitou dobu dostupná. 
 - Metriky se uchovávají po dobu 90 dnů. 
 - Položky protokolu aktivit se uchovávají po dobu 90 dnů. 
 - Diagnostické protokoly nejsou uložené.
 - Archivujte data do účtu úložiště pro delší dobu uchování.

**Dotaz**  

Pro přístup k datům v systému nebo Azure Storage můžete použít příkazy rozhraní příkazového řádku (CLI) Azure Monitor REST API, rutiny prostředí PowerShell nebo rozhraní .NET SDK pro více platforem. 

**Vizualizac**

Vizualizace dat monitorování pomocí grafů a diagramů vám umožňuje rozpoznat trendy rychleji, než když prohlížíte samotná data. 

Mezi možné způsoby vizualizace patří:
 - Použijte Azure Stackho uživatele a portálu pro správu.
 - Směrovat data do Microsoft Power BI.
 - Přesměrujte data na nástroj pro vizualizaci třetí strany pomocí živého streamování nebo si nástroj Přečtěte z archivu ve službě Azure Storage.

## <a name="methods-of-accessing-azure-monitor-on-azure-stack"></a>Metody přístupu ke službě Azure monitor na Azure Stack

Pomocí jedné z následujících metod můžete pracovat se sledováním, směrováním a načítáním dat. Některé způsoby ale nemusí být dostupné pro všechny akce nebo typy dat. 

 - [Azure Stack User Portal](azure-stack-use-portal.md)
 - [PowerShell](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-powershell-samples)
 - [Rozhraní příkazového řádku pro různé platformy (CLI)](https://docs.microsoft.com/azure/monitoring-and-diagnostics/insights-cli-samples)
 - [REST API](https://docs.microsoft.com/rest/api/monitor)
 - [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor)

> [!Important]  
> Pokud narazíte na chybu **prostředku** při zobrazení grafu výkonu virtuálního počítače, ujistěte se, že jste zaregistrovali Microsoft. Insights v rámci předplatného, které je přidružené k virtuálnímu počítači.

## <a name="next-steps"></a>Další postup

Další informace o sledování spotřeby dat na Azure Stack v článku [využívání dat monitorování z Azure Stack](azure-stack-metrics-monitor.md).
