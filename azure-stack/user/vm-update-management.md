---
title: Automatizace aktualizací a správy virtuálních počítačů v Azure Stack | Microsoft Docs
description: Naučte se používat řešení Azure Monitor pro virtuální počítače, Update Management, Change Tracking a inventarizace v Azure Automation ke správě virtuálních počítačů s Windows a Linux nasazených v Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: rtiberiu
ms.lastreviewed: 03/20/2019
ms.openlocfilehash: 891727c8e80af479e800feef9c292f7355fb208c
ms.sourcegitcommit: 637018771ac016b7d428174e88d4dcb131b54959
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68842691"
---
# <a name="vm-update-and-management-automation-in-azure-stack"></a>Automatizace aktualizací a správy virtuálních počítačů v Azure Stack
Ke správě virtuálních počítačů s Windows a Linux nasazených pomocí Azure Stack použijte následující Azure Automation řešení:

- **[Update Management](https://docs.microsoft.com/azure/automation/automation-update-management)** : Pomocí řešení Update Management můžete rychle vyhodnotit stav dostupných aktualizací na všech počítačích agenta a spravovat proces instalace požadovaných aktualizací pro virtuální počítače s Windows a Linux.

- **[Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking)** : Změny nainstalovaného softwaru, služeb systému Windows, registru a souborů systému Windows a démonů systému Linux na monitorovaných serverech se odesílají do služby Azure Monitor v cloudu ke zpracování. Logika platí pro přijatá data a cloudové službě zaznamenává data. Pomocí informací na řídicím panelu Change Tracking můžete snadno zobrazit změny provedené v serverové infrastruktuře.

- **[Inventář](https://docs.microsoft.com/azure/automation/automation-vm-inventory)** . Sledování inventáře pro Azure Stack virtuální počítač poskytuje uživatelské rozhraní založené na prohlížeči pro nastavení a konfiguraci shromažďování inventáře.

- **[Azure monitor pro virtuální počítače](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview)** : Azure Monitor pro virtuální počítače monitoruje vaše virtuální počítače Azure a Azure Stack a škálování virtuálních počítačů ve velkém měřítku. Analyzuje výkon a stav virtuálních počítačů s Windows a Linux a také monitoruje jejich procesy a závislosti na jiných prostředcích a externích procesech.

> [!IMPORTANT]
> Tato řešení jsou stejná jako ta, která slouží ke správě virtuálních počítačů Azure. Virtuální počítače Azure i Azure Stack jsou spravované stejným způsobem ze stejného rozhraní, a to pomocí stejných nástrojů. Azure Stack virtuální počítače se také účtují stejně jako virtuální počítače Azure při použití řešení Update Management, Change Tracking, inventarizace a Azure Monitor pro virtuální počítače s Azure Stack.

## <a name="prerequisites"></a>Požadavky
Před použitím těchto funkcí k aktualizaci a správě Azure Stack virtuálních počítačů musí být splněno několik požadavků. Patří sem kroky, které je třeba provést v Azure Portal a také na portálu pro správu Azure Stack.

### <a name="in-the-azure-portal"></a>V Azure Portal
Pokud chcete používat funkce Azure Monitor pro virtuální počítače, inventáře, Change Tracking a Update Management Azure Automation pro Azure Stack virtuální počítače, musíte tato řešení v Azure nejdřív povolit.

> [!TIP]
> Pokud jste už tyto funkce pro virtuální počítače Azure povolili, můžete použít již existující přihlašovací údaje k pracovnímu prostoru LogAnalytics. Pokud už máte LogAnalytics ID pracovního prostoru a primární klíč, který chcete použít, přeskočte dopředu k [Další části](./vm-update-management.md#in-the-azure-stack-admin-portal). V opačném případě pokračujte v této části a vytvořte nový pracovní prostor LogAnalytics a účet Automation.

Prvním krokem při povolování těchto řešení je [Vytvoření pracovního prostoru LogAnalytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace) ve vašem předplatném Azure. Log Analytics pracovní prostor je jedinečné Azure Monitor protokoluje prostředí s vlastním úložištěm dat, zdroji dat a řešeními. Po vytvoření pracovního prostoru si poznamenejte ID pracovního prostoru a klíč. Pokud si chcete zobrazit tyto informace, přejděte do okna pracovní prostor, klikněte na **Upřesnit nastavení**a zkontrolujte hodnoty **ID pracovního prostoru** a **primární klíč** . 

Dále musíte [vytvořit účet Automation](https://docs.microsoft.com/azure/automation/automation-create-standalone-account). Účet Automation je kontejner pro prostředky Azure Automation. Poskytuje způsob, jak oddělit vaše prostředí a dále organizovat pracovní postupy a prostředky služby Automation. Po vytvoření účtu Automation je potřeba povolit funkce inventáře, Change Tracking a Update Management. K povolení jednotlivých funkcí použijte následující postup:

1. V Azure Portal přejít na účet Automation, který chcete použít.

2. Vyberte řešení, které chcete povolit ( **inventarizace**, **sledování změn**nebo **Správa aktualizací**).

3. Pomocí rozevíracího seznamu **vybrat pracovní prostor...** vyberte Log Analytics pracovní prostor, který chcete použít.

4. Ověřte správnost všech zbývajících informací a kliknutím na **Povolit** povolte řešení.

5. Opakujte kroky 2-4 pro povolení všech tří řešení. 

   [![](media/vm-update-management/1-sm.PNG "Povolit funkce účtu Azure Automation")](media/vm-update-management/1-lg.PNG#lightbox)

### <a name="enable-azure-monitor-for-vms"></a>Povolit Azure Monitor pro virtuální počítače

Azure Monitor pro virtuální počítače monitoruje vaše virtuální počítače Azure a škálování virtuálních počítačů ve velkém měřítku. Analyzuje výkon a stav virtuálních počítačů s Windows a Linux a také monitoruje jejich procesy a závislosti na jiných prostředcích a externích procesech.

Jako řešení Azure Monitor pro virtuální počítače zahrnuje podporu monitorování výkonu a závislostí aplikací pro virtuální počítače, které jsou hostované místně nebo v jiném poskytovateli cloudu. Tři klíčové funkce poskytovat podrobnější informace:

1. Logické součásti virtuálních počítačů Azure se systémem Windows a Linux, které jsou měřeny proti předem nakonfigurovaným kritériím stavu, a upozorní vás při splnění vyhodnocené podmínky.

2. Předdefinované grafy výkonu trendů, které zobrazují metriky výkonu jádra z hostovaného operačního systému virtuálního počítače.

3. Mapa závislostí, která zobrazuje propojené komponenty s virtuálním počítačem z různých skupin prostředků a předplatných.

Po vytvoření pracovního prostoru Log Analytics povolte čítače výkonu v pracovním prostoru pro shromažďování dat na virtuálních počítačích se systémy Linux a Windows. Pak v pracovním prostoru nainstalujte a povolte řešení ServiceMap a InfrastructureInsights. Tento postup je popsaný v průvodci [nasazením Azure monitor pro virtuální počítače](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-onboard#how-to-enable-azure-monitor-for-vms-preview) .

### <a name="in-the-azure-stack-admin-portal"></a>Na portálu pro správu Azure Stack
Po povolení Azure Automation řešení v Azure Portal se budete muset přihlásit k portálu Azure Stack pro správu jako správce cloudu a stáhnout **Azure monitor, správu aktualizací a konfigurací** a **Azure monitor, aktualizovat a Správa konfigurace pro Linux** Extension na webu Azure Stack Marketplace.

   ![Azure Monitor, aktualizace a rozšíření pro správu konfigurace – položka Marketplace](media/vm-update-management/2.PNG) 

Pokud chcete povolit řešení Azure Monitor pro virtuální počítače map a získat přehled o závislostech sítě, Stáhněte si **Azure Monitor Dependency agent**:

   ![Azure Monitor Dependency Agent](media/vm-update-management/2-dependency.PNG) 

## <a name="enable-update-management-for-azure-stack-vms"></a>Povolit Update Management pro virtuální počítače s Azure Stack
Pomocí těchto kroků můžete povolit správu aktualizací pro virtuální počítače s Azure Stack.

1. Přihlaste se k portálu Azure Stack User Portal.

2. Na portálu Azure Stack User-Portal přejděte do okna rozšíření virtuálních počítačů, pro které chcete povolit tato řešení, klikněte na **+ Přidat**, vyberte rozšíření **pro správu služby Azure Update a konfigurace** a pak klikněte na **vytvořit**:

   [![](media/vm-update-management/3-sm.PNG "Okno rozšíření virtuálního počítače")](media/vm-update-management/3-lg.PNG#lightbox)

3. Poskytněte dříve vytvořené ID pracovního prostoru a primární klíč k propojení agenta s pracovním prostorem LogAnalytics. Pak kliknutím na **OK** nasaďte rozšíření.

   [![](media/vm-update-management/4-sm.PNG "Zadání ID pracovního prostoru a klíče")](media/vm-update-management/4-lg.PNG#lightbox) 

4. Jak je popsáno v [dokumentaci k Update Management](https://docs.microsoft.com/azure/automation/automation-update-management), je nutné povolit řešení Update Management pro každý virtuální počítač, který chcete spravovat. Chcete-li povolit řešení pro všechny virtuální počítače, které jsou podřízeny k pracovnímu prostoru, vyberte možnost **Správa aktualizací**, klikněte na položku **spravovat počítače**a potom vyberte možnost **Povolit u všech dostupných a budoucích počítačů** .

   [![](media/vm-update-management/5-sm.PNG "Povolit Update Management řešení na všech počítačích")](media/vm-update-management/5-lg.PNG#lightbox) 

   > [!TIP]
   > Zopakováním tohoto kroku povolíte každé řešení pro Azure Stack virtuálních počítačů, které se nahlásí do pracovního prostoru. 
  
Po povolení rozšíření pro správu služby Azure Update a konfigurace se kontrola provádí dvakrát denně pro každý spravovaný virtuální počítač. Rozhraní API se volá každých 15 minut, než se dotazuje na čas poslední aktualizace, abyste zjistili, jestli se změnil stav. Pokud se změní stav, spustí se kontrola kompatibility.

Po prohledání virtuálních počítačů se tyto virtuální počítače zobrazí v Azure Automationm účtu v řešení Update Management: 

   [![](media/vm-update-management/6-sm.PNG "Účet Azure Automation v Update Management")](media/vm-update-management/6-lg.PNG#lightbox) 

> [!IMPORTANT]
> Může trvat 30 minut a 6 hodin, než se na řídicím panelu zobrazí aktualizovaná data ze spravovaných počítačů.

Virtuální počítače s Azure Stack teď můžou být zahrnuté do plánovaných nasazení aktualizací společně s virtuálními počítači Azure.

## <a name="enable-azure-monitor-for-vms-running-on-azure-stack"></a>Povolit Azure Monitor pro virtuální počítače běžící na Azure Stack
Jakmile má virtuální počítač **Azure monitor, správu aktualizací a konfigurací**a nainstalují se rozšíření **Azure monitor Dependency agent** , začnou v řešení [Azure monitor pro virtuální počítače](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview) začít vytvářet data. 

> [!TIP]
> Rozšíření **Azure Monitor Dependency agent** nevyžaduje žádné parametry. Azure Monitor pro agenta závislostí mapování virtuálních počítačů nebude přenášet vlastní data a nevyžaduje žádné změny brány firewall nebo porty. Mapy dat je vždy přenášených v rámci agenta Log Analytics ve službě Azure Monitor, buď přímo nebo prostřednictvím [bránu OMS](https://docs.microsoft.com/azure/azure-monitor/platform/gateway) Pokud zásady zabezpečení IT neumožňují počítače v síti pro připojení k Internetu.

Azure Monitor pro virtuální počítače obsahuje sadu grafů výkonu, které cílí na několik klíčových ukazatelů výkonu (KPI), které vám pomůžou určit, jak dobře funguje virtuální počítač. Grafy ukazují použití prostředků v časovém intervalu, takže můžete identifikovat slabá místa a anomálie. Můžete také přepnout na seznam perspektiv v každém počítači a zobrazit tak použití prostředků na základě vybrané metriky. I když existuje mnoho prvků, které je potřeba vzít v úvahu při práci s výkonem, Azure Monitor pro virtuální počítače sleduje klíčové ukazatele výkonu operačního systému týkající se procesoru, paměti, síťového adaptéru a využití disku. Grafy výkonu doplňují funkci monitorování stavu a umožňují vystavovat problémy, které indikují chybu při selhání součásti systému. Azure Monitor pro virtuální počítače podporuje taky plánování kapacity a optimalizaci a optimalizaci, abyste dosáhli efektivity.

   ![Karta výkon virtuálních počítačů s Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/media/vminsights-performance/vminsights-performance-aggview-01.png)

Zobrazení zjištěných součástí aplikace na virtuálních počítačích s Windows a Linux spuštěných v Azure Stack se může vymezit dvěma způsoby pomocí Azure Monitor pro virtuální počítače. První z nich je přímo z virtuálního počítače a druhý je mezi skupinami virtuálních počítačů z Azure Monitor.
Článek [použití Azure monitor pro virtuální počítače mapy k porozumění součástem aplikací](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-maps) vám pomůže pochopit možnosti těchto dvou perspektiv a využít funkci map.

   ![Karta mapa Azure Monitor virtuálních počítačů](https://docs.microsoft.com/azure/azure-monitor/insights/media/vminsights-maps/map-multivm-azure-monitor-01.png)

V případě, že vám [Azure monitor pro virtuální počítače](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview) nezobrazuje žádná data o výkonu, musíte povolit shromažďování údajů o výkonu pro Windows a Linux v rozšířeném nastavení [LogAnalytics pracovního prostoru](https://docs.microsoft.com/azure/azure-monitor/platform/data-sources-performance-counters) .

## <a name="enable-update-management-using-a-resource-manager-template"></a>Povolení Update Management pomocí šablony Správce prostředků
Pokud máte velký počet Azure Stack virtuálních počítačů, můžete [tuto šablonu Azure Resource Manager](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) použít k jednoduššímu nasazení řešení na virtuální počítače. Šablona nasadí rozšíření Microsoft Monitoring Agent pro existující virtuální počítač Azure Stack a přidá ho do existujícího pracovního prostoru Azure LogAnalytics.
 
## <a name="next-steps"></a>Další kroky
[Optimalizace výkonu SQL Server virtuálních počítačů](azure-stack-sql-server-vm-considerations.md)
