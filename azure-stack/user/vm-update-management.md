---
title: Aktualizace a správa virtuálních počítačů pomocí Azure Stack | Microsoft Docs
description: Naučte se používat řešení Azure Monitor pro virtuální počítače, Update Management, Change Tracking a inventarizace v Azure Automation ke správě virtuálních počítačů s Windows a Linux, které jsou nasazené v Azure Stack.
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
ms.openlocfilehash: 1e43f5df97ff91e7117759027499ffa6cd0df7c8
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68418245"
---
# <a name="azure-stack-vm-update-and-management"></a>Azure Stack aktualizace a správa virtuálních počítačů
Ke správě virtuálních počítačů se systémem Windows a Linux nasazených pomocí Azure Stack můžete použít následující Azure Automation řešení:

- **[Update Management](https://docs.microsoft.com/azure/automation/automation-update-management)** . Pomocí řešení Update Management můžete rychle posoudit stav dostupných aktualizací na všech počítačích agenta a spravovat proces instalace požadovaných aktualizací pro tyto virtuální počítače se systémem Windows a Linux.

- **[Change Tracking](https://docs.microsoft.com/azure/automation/automation-change-tracking)** . Změny nainstalovaného softwaru, služeb systému Windows, registru a souborů systému Windows a démonů systému Linux na monitorovaných serverech se odesílají do služby Azure Monitor v cloudu ke zpracování. Logika platí pro přijatá data a cloudové službě zaznamenává data. Pomocí informací na řídicím panelu Change Tracking můžete snadno zobrazit změny provedené v serverové infrastruktuře.

- **[Inventář](https://docs.microsoft.com/azure/automation/automation-vm-inventory)** . Sledování inventáře pro virtuální počítač s Azure Stack poskytuje uživatelské rozhraní založené na prohlížeči pro nastavení a konfiguraci shromažďování inventáře.

- **[Azure monitor pro virtuální počítače](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview)** . Azure Monitor pro virtuální počítače monitoruje vaše virtuální počítače s Azure a Azure Stack a škálování virtuálních počítačů ve velkém měřítku. Analyzuje výkon a stav virtuálních počítačů s Windows a Linuxem a monitoruje jejich procesy a závislosti na jiných prostředcích a externích procesech. 

> [!IMPORTANT]
> Tato řešení jsou stejná jako ta, která slouží ke správě virtuálních počítačů Azure. Virtuální počítače Azure i Azure Stack jsou spravované stejným způsobem ze stejného rozhraní, a to pomocí stejných nástrojů. Azure Stack virtuální počítače se také účtují stejně jako virtuální počítače Azure při použití Update Management, Change Tracking, inventáře a Azure Monitor Virtual Machines řešení s Azure Stack.

## <a name="prerequisites"></a>Požadavky
Před použitím těchto funkcí k aktualizaci a správě Azure Stack virtuálních počítačů musí být splněno několik požadavků. Patří sem kroky, které je třeba provést v Azure Portal a také na portálu pro správu Azure Stack.

### <a name="in-the-azure-portal"></a>V Azure Portal
Pokud chcete používat funkce služby Azure Automation Azure Monitor pro virtuální počítače, inventář, Change Tracking a Update Management pro virtuální počítače Azure Stack, musíte tato řešení v Azure nejdřív povolit.

> [!TIP]
> Pokud už máte tyto funkce povolené pro virtuální počítače Azure, můžete použít již existující přihlašovací údaje k pracovnímu prostoru LogAnalytics. Pokud už máte LogAnalytics ID pracovního prostoru a primární klíč, který chcete použít, přeskočte dopředu k [Další části](./vm-update-management.md#in-the-azure-stack-administration-portal). V opačném případě pokračujte v této části a vytvořte nový pracovní prostor LogAnalytics a účet Automation.

Prvním krokem při povolování těchto řešení je [Vytvoření pracovního prostoru LogAnalytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace) ve vašem předplatném Azure. Log Analytics pracovní prostor je jedinečné Azure Monitor protokoluje prostředí s vlastním úložištěm dat, zdroji dat a řešeními. Po vytvoření pracovního prostoru si poznamenejte ID pracovního prostoru a klíč. Pokud si chcete zobrazit tyto informace, přejděte do okna pracovní prostor, klikněte na **Upřesnit nastavení**a zkontrolujte hodnoty **ID pracovního prostoru** a **primární klíč** . 

Dále musíte [vytvořit účet Automation](https://docs.microsoft.com/azure/automation/automation-create-standalone-account). Účet Automation je kontejner pro prostředky Azure Automation. Poskytuje způsob, jak oddělit vaše prostředí a dále organizovat pracovní postupy a prostředky služby Automation. Po vytvoření účtu Automation je potřeba povolit funkce pro inventarizaci, sledování změn a aktualizaci. K tomu je potřeba pomocí těchto kroků povolit jednotlivé funkce:

1. V Azure Portal přejít na účet Automation, který chcete použít.

2. Vyberte řešení, které chcete povolit ( **inventarizace**, **sledování změn**nebo **Správa aktualizací**).

3. Pomocí rozevíracího seznamu **vybrat pracovní prostor...** vyberte Log Analytics pracovní prostor, který chcete použít.

4. Ověřte správnost všech zbývajících informací a kliknutím na **Povolit** povolte řešení.

5. Opakujte kroky 2-4 pro povolení všech tří řešení. 

   [![](media/vm-update-management/1-sm.PNG "Povolit funkce účtu Automation")](media/vm-update-management/1-lg.PNG#lightbox)

### <a name="enable-azure-monitor-for-vms"></a>Povolit Azure Monitor pro virtuální počítače

Azure Monitor pro virtuální počítače monitoruje virtuální počítače Azure (VM) a škálovacích sad virtuálních počítačů ve velkém měřítku. Analyzuje výkon a stav virtuálních počítačů s Windows a Linuxem a monitoruje jejich procesy a závislosti na jiných prostředcích a externích procesech.

Azure Monitor pro virtuální počítače jako řešení, zahrnuje podporu pro monitorování výkonu a používání závislosti virtuálních počítačů, které jsou hostované v místním prostředí nebo jiného zprostředkovatele cloudu. Tři klíčové funkce poskytovat podrobnější informace:

1. Logické součásti virtuálních počítačů Azure, na kterých běží Windows a Linux: Jsou měřeny proti předem nakonfigurovaným kritériím stavu a upozorní vás při splnění vyhodnocené podmínky. 

2. Předdefinované grafy výkonu trendů: Zobrazit základní metriky výkonu z hostovaného operačního systému virtuálního počítače.

3. Mapa závislostí: Zobrazí propojené komponenty s virtuálním počítačem z různých skupin prostředků a předplatných.

Po vytvoření pracovního prostoru Log Analytics budete muset v pracovním prostoru povolit čítače výkonu pro shromažďování dat na virtuálních počítačích se systémy Linux a Windows a také nainstalovat a povolit řešení ServiceMap a InfrastructureInsights ve vašem pracovním prostoru. Tento postup je popsaný v průvodci [nasazením Azure monitor pro virtuální počítače](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-onboard#how-to-enable-azure-monitor-for-vms-preview) .

### <a name="in-the-azure-stack-administration-portal"></a>Na portálu pro správu Azure Stack
Po povolení Azure Automation řešení v Azure Portal se budete muset přihlásit k portálu pro správu Azure Stack jako správce cloudu a stáhnout **Azure monitor, správu aktualizací a konfigurací** a **Azure monitor Aktualizace a Správa konfigurace pro Linux** extension Azure Stack položky Marketplace. 

   ![Azure Monitor, aktualizace a rozšíření pro správu konfigurace – položka Marketplace](media/vm-update-management/2.PNG) 

Pokud chcete povolit řešení Azure Monitor pro virtuální počítače map a získat přehled o síťových závislostech, budete muset také stáhnout **Azure Monitor Dependency agent**:

   ![Azure Monitor Dependency Agent](media/vm-update-management/2-dependency.PNG) 

## <a name="enable-update-management-for-azure-stack-virtual-machines"></a>Povolit Update Management pro virtuální počítače s Azure Stack
Pomocí těchto kroků můžete povolit správu aktualizací pro virtuální počítače s Azure Stack.

1. Přihlaste se k portálu Azure Stack User Portal.

2. Na portálu Azure Stack User-Portal přejděte do okna rozšíření virtuálních počítačů, pro které chcete povolit tato řešení, klikněte na **+ Přidat**, vyberte rozšíření **pro správu služby Azure Update a konfigurace** a klikněte na **vytvořit**:

   [![](media/vm-update-management/3-sm.PNG "Okno rozšíření virtuálního počítače")](media/vm-update-management/3-lg.PNG#lightbox)

3. Poskytněte dříve vytvořené ID pracovního prostoru a primární klíč pro připojení agenta k pracovnímu prostoru LogAnalytics a kliknutím na tlačítko **OK** nasazení rozšíření nasaďte.

   [![](media/vm-update-management/4-sm.PNG "Zadání ID pracovního prostoru a klíče")](media/vm-update-management/4-lg.PNG#lightbox) 

4. Jak je popsáno v [dokumentaci pro správu aktualizací služby Automation](https://docs.microsoft.com/azure/automation/automation-update-management), je nutné povolit řešení Update Management pro každý virtuální počítač, který chcete spravovat. Chcete-li povolit řešení pro všechny virtuální počítače, které jsou podřízeny k pracovnímu prostoru, vyberte možnost **Správa aktualizací**, klikněte na položku **spravovat počítače**a potom vyberte možnost **Povolit u všech dostupných a budoucích počítačů** .

   [![](media/vm-update-management/5-sm.PNG "Zadání ID pracovního prostoru a klíče")](media/vm-update-management/5-lg.PNG#lightbox) 

   > [!TIP]
   > Zopakováním tohoto kroku povolíte každé řešení pro Azure Stack virtuálních počítačů, které se nahlásí do pracovního prostoru. 
  
Po povolení rozšíření pro správu služby Azure Update a konfigurace se kontrola provádí dvakrát denně u každého spravovaného virtuálního počítače. Rozhraní API se volá každých 15 minut, než se dotazuje na čas poslední aktualizace, abyste zjistili, jestli se změnil stav. Pokud se stav změnil, je zahájeno prohledávání dodržování předpisů.

Po prohledání virtuálních počítačů se tyto virtuální počítače zobrazí v Azure Automationm účtu v řešení Update Management: 

   [![](media/vm-update-management/6-sm.PNG "Zadání ID pracovního prostoru a klíče")](media/vm-update-management/6-lg.PNG#lightbox) 

> [!IMPORTANT]
> Může trvat 30 minut a 6 hodin, než se na řídicím panelu zobrazí aktualizovaná data ze spravovaných počítačů.

Virtuální počítače s Azure Stack teď můžou být zahrnuté do plánovaných nasazení aktualizací společně s virtuálními počítači Azure.

## <a name="enable-azure-monitor-for-vms-running-on-azure-stack"></a>Povolit Azure Monitor pro virtuální počítače běžící na Azure Stack
Jakmile má virtuální počítač **Azure monitor, správu aktualizací a konfigurací** a nainstalovaná rozšíření **dependency agent Azure monitor** , spustí vytváření sestav dat v řešení [Azure monitor pro virtuální počítače](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-overview) . 

> [!TIP]
> Rozšíření **Azure Monitor Dependency agent** nevyžaduje žádné parametry. Azure Monitor pro agenta závislostí mapování virtuálních počítačů nebude přenášet vlastní data a nevyžaduje žádné změny brány firewall nebo porty. Mapy dat je vždy přenášených v rámci agenta Log Analytics ve službě Azure Monitor, buď přímo nebo prostřednictvím [bránu OMS](https://docs.microsoft.com/azure/azure-monitor/platform/gateway) Pokud zásady zabezpečení IT neumožňují počítače v síti pro připojení k Internetu.

Azure Monitor pro virtuální počítače obsahuje sadu grafů výkonu, které cílí na několik klíčových ukazatelů výkonu (KPI), které vám pomůžou určit, jak dobře je virtuální počítač prováděn. Grafy ukazují využití prostředků v časovém intervalu, takže můžete identifikovat kritická místa, anomálie nebo přepnout na jednotlivé počítače a zobrazit tak využití prostředků na základě vybrané metriky. I když existuje mnoho prvků, které je potřeba vzít v úvahu při práci s výkonem, Azure Monitor pro virtuální počítače sleduje klíčové ukazatele výkonu operačního systému související s procesorem, pamětí, síťovým adaptérem a využitím disku. Výkon doplňuje funkci monitorování stavu a pomáhá vystavovat problémy, které naznačují možné selhání součásti systému, podporují optimalizaci a optimalizaci, aby dosáhly efektivity, nebo podporovala plánování kapacity.

   ![Karta výkon Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/media/vminsights-performance/vminsights-performance-aggview-01.png)

Zobrazení zjištěných součástí aplikace na virtuálních počítačích s Windows a Linux, které běží v Azure Stack, se dá pozorovat dvěma způsoby pomocí Azure Monitor pro virtuální počítače, od virtuálního počítače přímo nebo přes skupiny virtuálních počítačů od Azure Monitor.
[Mapa použití Azure monitor pro virtuální počítače (Preview) k pochopení článku součásti aplikace](https://docs.microsoft.com/azure/azure-monitor/insights/vminsights-maps) vám pomůže pochopit, jak se tyto dva perspektivy nacházejí mezi oběma perspektivami a jak používat funkci mapa.

   ![Karta výkon Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/insights/media/vminsights-maps/map-multivm-azure-monitor-01.png)


## <a name="enable-update-management-using-a-resource-manager-template"></a>Povolení Update Management pomocí šablony Správce prostředků
Pokud máte velký počet Azure Stack virtuálních počítačů, můžete [tuto šablonu Azure Resource Manager](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/MicrosoftMonitoringAgent-ext-win) použít k jednoduššímu nasazení řešení na virtuální počítače. Šablona nasadí rozšíření Microsoft Monitoring Agent pro existující virtuální počítač Azure Stack a přidá ho do existujícího pracovního prostoru Azure LogAnalytics.
 
## <a name="next-steps"></a>Další postup
[Optimalizace výkonu SQL Server virtuálních počítačů](azure-stack-sql-server-vm-considerations.md)