---
title: Automatizace aktualizací a správy virtuálních počítačů v centru Azure Stack
description: Pomocí řešení Azure Monitor pro virtuální počítače, Update Management, Change Tracking a inventáře v Azure Automation můžete spravovat virtuální počítače s Windows a Linux nasazené v centru pro Azure Stack.
author: mattbriggs
ms.topic: article
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: rtiberiu
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: f0d1ecb2f5ad2313894cb15998f43f2bfd257c77
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90572438"
---
# <a name="vm-update-and-management-automation-in-azure-stack-hub"></a>Automatizace aktualizací a správy virtuálních počítačů v centru Azure Stack
Ke správě virtuálních počítačů s Windows a Linux, které se nasazují pomocí centra Azure Stack, použijte následující Azure Automation řešení:

- **[Update Management](/azure/automation/automation-update-management)**: pomocí řešení Update Management můžete rychle posoudit stav dostupných aktualizací na všech počítačích agenta a spravovat proces instalace požadovaných aktualizací pro virtuální počítače s Windows a Linux.

- **[Change Tracking](/azure/automation/automation-change-tracking)**: do služby Azure monitor v cloudu se odesílají změny nainstalovaného softwaru, služeb systému Windows, registru a souborů Windows a démony Linux na monitorovaných serverech. Na přijatá data se aplikuje logika a cloudová služba data zaznamená. Pomocí informací na řídicím panelu Change Tracking můžete snadno zobrazit změny provedené v serverové infrastruktuře.

- **[Inventář](/azure/automation/automation-vm-inventory)**. Sledování inventáře pro virtuální počítač centra Azure Stack poskytuje uživatelské rozhraní založené na prohlížeči pro nastavení a konfiguraci shromažďování inventáře.

- **[Azure monitor pro virtuální počítače](/azure/azure-monitor/insights/vminsights-overview)**: Azure monitor pro virtuální počítače monitoruje vaše virtuální počítače Azure a služby Azure Stack hub a škálování virtuálních počítačů. Analyzuje výkon a stav virtuálních počítačů s Windows a Linux a také monitoruje jejich procesy a závislosti na jiných prostředcích a externích procesech.

> [!IMPORTANT]
> Tato řešení jsou stejná jako ta, která slouží ke správě virtuálních počítačů Azure. Virtuální počítače s rozbočovačem Azure i Azure Stack se spravují stejným způsobem, ze stejného rozhraní, pomocí stejných nástrojů. Virtuální počítače centra Azure Stack se také účtují stejně jako virtuální počítače Azure při použití řešení Update Management, Change Tracking, inventarizace a Azure Monitor pro virtuální počítače s centrem Azure Stack.

## <a name="prerequisites"></a>Požadavky
Před použitím těchto funkcí k aktualizaci a správě virtuálních počítačů centra Azure Stack musí být splněno několik požadavků. Patří sem kroky, které je třeba provést v Azure Portal a také na portálu pro správu centra Azure Stack.

### <a name="in-the-azure-portal"></a>Na webu Azure Portal
Pokud chcete používat funkce Azure Monitor pro virtuální počítače, inventáře, Change Tracking a Update Management Azure Automation pro virtuální počítače centra Azure Stack, musíte tato řešení v Azure nejdřív povolit.

> [!TIP]
> Pokud jste už tyto funkce pro virtuální počítače Azure povolili, můžete použít již existující přihlašovací údaje k pracovnímu prostoru LogAnalytics. Pokud už máte LogAnalytics ID pracovního prostoru a primární klíč, který chcete použít, přeskočte dopředu k [Další části](./vm-update-management.md#in-the-azure-stack-hub-administrator-portal). V opačném případě pokračujte v této části a vytvořte nový pracovní prostor LogAnalytics a účet Automation.

Prvním krokem při povolování těchto řešení je [Vytvoření pracovního prostoru LogAnalytics](/azure/log-analytics/log-analytics-quick-create-workspace) ve vašem předplatném Azure. Log Analytics pracovní prostor je jedinečné Azure Monitor protokoluje prostředí s vlastním úložištěm dat, zdroji dat a řešeními. Po vytvoření pracovního prostoru si poznamenejte ID pracovního prostoru a klíč. Pokud si chcete zobrazit tyto informace, přejděte do okna pracovní prostor, klikněte na **Upřesnit nastavení**a zkontrolujte hodnoty **ID pracovního prostoru** a **primární klíč** . 

Dále musíte [vytvořit účet Automation](/azure/automation/automation-create-standalone-account). Účet Automation je kontejner pro prostředky Azure Automation. Poskytuje způsob, jak oddělit vaše prostředí a dále organizovat pracovní postupy a prostředky služby Automation. Po vytvoření účtu Automation je potřeba povolit funkce inventáře, Change Tracking a Update Management. K povolení jednotlivých funkcí použijte následující postup:

1. V Azure Portal přejít na účet Automation, který chcete použít.

2. Vyberte řešení, které chcete povolit ( **inventarizace**, **sledování změn**nebo **Správa aktualizací**).

3. Pomocí rozevíracího seznamu **vybrat pracovní prostor...** vyberte Log Analytics pracovní prostor, který chcete použít.

4. Ověřte správnost všech zbývajících informací a kliknutím na **Povolit** povolte řešení.

5. Opakujte kroky 2-4 pro povolení všech tří řešení. 

   [![V okně diagnostikovat a řešit problémy se zobrazí dva seznamy se zvýrazněnými třemi možnostmi. Je vybrán inventář. K dispozici je také rozevírací seznam "Log Analytics pracovní prostor" a tlačítko Povolit.](media//vm-update-management/1-sm.PNG "Povolit funkce účtu Azure Automation")](media//vm-update-management/1-lg.PNG)

### <a name="enable-azure-monitor-for-vms"></a>Povolit Azure Monitor pro virtuální počítače

Azure Monitor pro virtuální počítače monitoruje vaše virtuální počítače Azure a škálování virtuálních počítačů ve velkém měřítku. Analyzuje výkon a stav virtuálních počítačů s Windows a Linux a také monitoruje jejich procesy a závislosti na jiných prostředcích a externích procesech.

Jako řešení Azure Monitor pro virtuální počítače zahrnuje podporu monitorování výkonu a závislostí aplikací pro virtuální počítače, které jsou hostované místně nebo v jiném poskytovateli cloudu. Tři klíčové funkce poskytují podrobný přehled:

1. Logické součásti virtuálních počítačů Azure se systémem Windows a Linux, které jsou měřeny proti předem nakonfigurovaným kritériím stavu, a upozorní vás při splnění vyhodnocené podmínky.

2. Předdefinované grafy výkonu trendů, které zobrazují metriky výkonu jádra z hostovaného operačního systému virtuálního počítače.

3. Mapa závislostí, která zobrazuje propojené komponenty s virtuálním počítačem z různých skupin prostředků a předplatných.

Po vytvoření pracovního prostoru Log Analytics povolte čítače výkonu v pracovním prostoru pro shromažďování dat na virtuálních počítačích se systémy Linux a Windows. Pak v pracovním prostoru nainstalujte a povolte řešení ServiceMap a InfrastructureInsights. Tento postup je popsaný v průvodci [nasazením Azure monitor pro virtuální počítače](/azure/azure-monitor/insights/vminsights-enable-overview) .

### <a name="in-the-azure-stack-hub-administrator-portal"></a>Na portálu pro správu centra Azure Stack
Po povolení Azure Automation řešení v Azure Portal se budete muset přihlásit k portálu Azure Stack správce centra jako správce cloudu a stáhnout **Azure monitor, správu aktualizací a konfigurací** a rozšíření **Azure monitor, aktualizace a konfigurace pro Linux** na webu centra pro správu Azure Stack.

   ![Dialogová okna > > přidat ze správy Azure > Azure Monitor, aktualizace a Správa konfigurace, popisuje rozšíření a poskytuje tlačítko pro stažení.](media//vm-update-management/2.PNG) 

Pokud chcete povolit řešení Azure Monitor pro virtuální počítače map a získat přehled o závislostech sítě, Stáhněte si **Azure Monitor Dependency agent**:

   ![Dialogová okna > > přidat ze služby Azure Dependency Agent Azure Monitor > pro správu Marketplace popisuje rozšíření a poskytuje tlačítko pro stažení.](media//vm-update-management/2-dependency.PNG) 

## <a name="enable-update-management-for-azure-stack-hub-vms"></a>Povolit Update Management pro virtuální počítače centra Azure Stack
Pomocí těchto kroků můžete povolit správu aktualizací pro virtuální počítače centra Azure Stack.

1. Přihlaste se k portálu pro uživatele centra Azure Stack.

2. Na portálu Azure Stack User Portal přejděte do okna rozšíření virtuálních počítačů, pro které chcete tato řešení povolit, klikněte na **+ Přidat**, vyberte rozšíření **pro správu služby Azure Update a konfigurace** a pak klikněte na **vytvořit**:

   [![Dialogové okno "Správa aktualizací a konfigurace Azure" obsahuje vysvětlující informace, tlačítko vytvořit (zvýrazněno) pro přidání rozšíření a odkaz na Další informace.](media//vm-update-management/3-sm.PNG "Okno rozšíření virtuálního počítače")](media//vm-update-management/3-lg.PNG)

3. Poskytněte dříve vytvořené ID pracovního prostoru a primární klíč k propojení agenta s pracovním prostorem LogAnalytics. Pak kliknutím na **OK** nasaďte rozšíření.

   [![V dialogovém okně nainstalovat rozšíření jsou textová pole pro Azure ID pracovního prostoru a WorkspaceKey.](media//vm-update-management/4-sm.PNG "Zadání ID pracovního prostoru a klíče")](media//vm-update-management/4-lg.PNG) 

4. Jak je popsáno v [dokumentaci k Update Management](/azure/automation/automation-update-management), je nutné povolit řešení Update Management pro každý virtuální počítač, který chcete spravovat. Chcete-li povolit řešení pro všechny virtuální počítače, které jsou podřízeny k pracovnímu prostoru, vyberte možnost **Správa aktualizací**, klikněte na položku **spravovat počítače**a potom vyberte možnost **Povolit u všech dostupných a budoucích počítačů** .

   [![V dialogovém okně spravovat počítače – Update Management se zobrazí počítače, které nemají Update Management povoleny. Jsou k dispozici tři možnosti povolení a výběr možnosti Povolit u všech dostupných a budoucích počítačů je zaškrtnuté a zvýrazněné. Existuje tlačítko Povolit.](media//vm-update-management/5-sm.PNG "Povolit Update Management řešení na všech počítačích")](media//vm-update-management/5-lg.PNG) 

   > [!TIP]
   > Zopakováním tohoto kroku povolíte každé řešení pro virtuální počítače Azure Stack centra, které nahlásíte do pracovního prostoru. 
  
Po povolení rozšíření pro správu služby Azure Update a konfigurace se kontrola provádí dvakrát denně pro každý spravovaný virtuální počítač. Rozhraní API se volá každých 15 minut, než se dotazuje na čas poslední aktualizace, abyste zjistili, jestli se změnil stav. Pokud se změní stav, spustí se kontrola kompatibility.

Po prohledání virtuálních počítačů se tyto virtuální počítače zobrazí v Azure Automationm účtu v řešení Update Management: 

   [![Jsou uvedené naskenované počítače. Pro každý je k dispozici stav dodržování předpisů, platforma, operační systém a počet kritických chybějících aktualizací. K dispozici jsou souhrny, které ukazují, kolik počítačů potřebuje pozornost a kolik z nich znamená chybějící aktualizace atd.](media//vm-update-management/6-sm.PNG "Účet Azure Automation v Update Management")](media//vm-update-management/6-lg.PNG) 

> [!IMPORTANT]
> Zobrazení aktuálních dat o spravovaných počítačích může trvat 30 minut až 6 hodin.

Virtuální počítače centra Azure Stack se teď dají zahrnout do plánovaných nasazení aktualizací společně s virtuálními počítači Azure.

##  <a name="create-an-update-deployment-schedule"></a>Vytvořit plán nasazení aktualizace

Pokud chcete vytvořit plán nasazení aktualizace, musíte k předání počítačů použít rutinu prostředí PowerShell nebo Azure REST API. K naplánování vašich počítačů použijte následující příklad PowerShellu. K vytvoření plánu můžete použít rutinu **[New-AzAutomationSchedule](/powershell/module/az.automation/new-azautomationschedule)** s `ForUpdateConfiguration` parametrem. Pak použijte rutinu **[New-AzAutomationSoftwareUpdateConfiguration](/powershell/module/az.automation/new-azautomationsoftwareupdateconfiguration)** a předejte do parametru počítače Azure Stack hub `NonAzureComputer` . Pokud chcete tento skript spustit, budete muset použít globální [Azure PowerShell AZ Module](/powershell/azure/).

Následující příklad ukazuje, jak to provést:

```Powershell  
$nonAzurecomputers = @("server-01", "server-02")

$startTime = ([DateTime]::Now).AddMinutes(10)

$s = New-AzAutomationSchedule -ResourceGroupName mygroup -AutomationAccountName myaccount -Name myupdateconfig -Description test-OneTime -OneTime -StartTime $startTime -ForUpdateConfiguration

New-AzAutomationSoftwareUpdateConfiguration  -ResourceGroupName $rg -AutomationAccountName $aa -Schedule $s -Windows -AzureVMResourceId $azureVMIdsW -NonAzureComputer $nonAzurecomputers -Duration (New-TimeSpan -Hours 2) -IncludedUpdateClassification Security,UpdateRollup -ExcludedKbNumber KB01,KB02 -IncludedKbNumber KB100
```

## <a name="enable-azure-monitor-for-vms-running-on-azure-stack-hub"></a>Povolit Azure Monitor pro virtuální počítače běžící v centru Azure Stack
Jakmile má virtuální počítač **Azure monitor, správu aktualizací a konfigurací**a nainstalují se rozšíření **Azure monitor Dependency agent** , začnou v řešení [Azure monitor pro virtuální počítače](/azure/azure-monitor/insights/vminsights-overview) začít vytvářet data. 

> [!TIP]
> Rozšíření **Azure Monitor Dependency agent** nevyžaduje žádné parametry. Agent závislostí Azure Monitor pro virtuální počítače neodesílá žádná data a nevyžaduje žádné změny bran firewall nebo portů. Data mapy jsou vždy přenášena agentem Log Analytics do služby Azure Monitor, a to buď přímo, nebo prostřednictvím [brány OMS](/azure/azure-monitor/platform/gateway) , pokud zásady zabezpečení IT nedovolují počítačům v síti připojení k Internetu.

Azure Monitor pro virtuální počítače obsahuje sadu grafů výkonu, které cílí na několik klíčových ukazatelů výkonu (KPI), které vám pomůžou určit, jak dobře funguje virtuální počítač. Grafy ukazují použití prostředků v časovém intervalu, takže můžete identifikovat slabá místa a anomálie. Můžete také přepnout na seznam perspektiv v každém počítači a zobrazit tak použití prostředků na základě vybrané metriky. I když existuje mnoho prvků, které je potřeba vzít v úvahu při práci s výkonem, Azure Monitor pro virtuální počítače sleduje klíčové ukazatele výkonu operačního systému týkající se procesoru, paměti, síťového adaptéru a využití disku. Grafy výkonu doplňují funkci monitorování stavu a umožňují vystavovat problémy, které indikují chybu při selhání součásti systému. Azure Monitor pro virtuální počítače podporuje taky plánování kapacity a optimalizaci a optimalizaci, abyste dosáhli efektivity.

   ![Karta výkon virtuálních počítačů s Azure Monitor](/azure/azure-monitor/insights/media/vminsights-performance/vminsights-performance-aggview-01.png)

Zobrazení zjištěných součástí aplikace na virtuálních počítačích s Windows a Linux spuštěných v centru Azure Stack se může vymezit dvěma způsoby pomocí Azure Monitor pro virtuální počítače. První z nich je přímo z virtuálního počítače a druhý je mezi skupinami virtuálních počítačů z Azure Monitor.
Článek [použití Azure monitor pro virtuální počítače mapy k porozumění součástem aplikací](/azure/azure-monitor/insights/vminsights-maps) vám pomůže pochopit možnosti těchto dvou perspektiv a využít funkci map.

   ![Karta mapa Azure Monitor virtuálních počítačů](/azure/azure-monitor/insights/media/vminsights-maps/map-multivm-azure-monitor-01.png)

V případě, že vám [Azure monitor pro virtuální počítače](/azure/azure-monitor/insights/vminsights-overview) nezobrazuje žádná data o výkonu, musíte povolit shromažďování údajů o výkonu pro Windows a Linux v rozšířeném nastavení [LogAnalytics pracovního prostoru](/azure/azure-monitor/platform/data-sources-performance-counters) .

## <a name="enable-update-management-using-a-resource-manager-template"></a>Povolení Update Management pomocí šablony Správce prostředků
Pokud máte velký počet Azure Stackch virtuálních počítačů centra, můžete [tuto šablonu Azure Resource Manager](https://aka.ms/aa6zdzy) použít k jednoduššímu nasazení řešení na virtuální počítače. Šablona nasadí rozšíření Microsoft Monitoring Agent na existující virtuální počítač centra Azure Stack a přidá ho do existujícího pracovního prostoru Azure LogAnalytics.
 
## <a name="next-steps"></a>Další kroky
[Optimalizace výkonu SQL Server virtuálních počítačů](azure-stack-sql-server-vm-considerations.md)
