---
title: Monitorování Azure Stack HCL pomocí Azure Monitor
description: Monitorujte servery a nakonfigurujte výstrahy pomocí Azure Monitor z centra pro správu Windows.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 07/21/2020
ms.openlocfilehash: f721b16d6742cde5e27fae8b81d8d256c7defa2a
ms.sourcegitcommit: 0e52f460295255b799bac92b40122a22bf994e27
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/21/2020
ms.locfileid: "86866803"
---
# <a name="monitor-azure-stack-hci-with-azure-monitor"></a>Monitorování Azure Stack HCL pomocí Azure Monitor

> Platí pro: Azure Stack HCI, verze 20H2; Windows Server 2019

[Azure monitor](/azure/azure-monitor/overview) shromažďuje, analyzuje a funguje na telemetrie z nejrůznějších prostředků, včetně serverů Windows a virtuálních počítačů (VM), místních i cloudových. I když Azure Monitor vyžádá data z virtuálních počítačů Azure a dalších prostředků Azure, Tento článek se zaměřuje na to, jak Azure Monitor pracuje s místními servery a virtuálními počítači běžícími na Azure Stack HCL, konkrétně v centru pro správu Windows.

## <a name="how-does-azure-monitor-work"></a>Jak Azure Monitor funguje?
:::image type="content" source="media/monitor/azure-monitor-diagram.png" alt-text="Diagram způsobu, jakým Azure Monitor fungovat" border="false":::
Data generovaná z místních Windows serverů se shromažďují v Log Analytics pracovním prostoru v Azure Monitor. V pracovním prostoru můžete povolit různá řešení monitorování – sady logiky, které poskytují přehledy pro konkrétní scénář. Například Azure Update Management, Azure Security Center a Azure Monitor pro virtuální počítače jsou všechna řešení monitorování, která lze povolit v rámci pracovního prostoru.

Když povolíte řešení monitorování v pracovním prostoru Log Analytics, všechny servery, které do tohoto pracovního prostoru nahlásí, začnou shromažďovat data relevantní pro toto řešení, takže řešení může vygenerovat přehledy pro všechny servery v pracovním prostoru.

Pro shromažďování diagnostických dat na místním serveru a jejich odesílání do pracovního prostoru Log Analytics Azure Monitor vyžaduje instalaci Microsoft Monitoring Agent (MMA). Některá řešení monitorování také vyžadují sekundárního agenta. Například Azure Monitor pro virtuální počítače také závisí na agentovi ServiceMap pro další funkce, které toto řešení poskytuje.

Některá řešení, jako je například Azure Update Management, závisí také na Azure Automation, která umožňují centrálně spravovat prostředky v prostředích Azure a mimo Azure. Například Azure Update Management používá Azure Automation k naplánování a orchestraci instalace aktualizací napříč počítači ve vašem prostředí, a to centrálně z Azure Portal.

## <a name="what-data-does-azure-monitor-collect"></a>Jaká data shromažďuje služba Azure Monitor?

Všechna data shromažďovaná Azure Monitor se vejdou do jednoho ze dvou základních typů: metriky a protokoly.

1. [Metriky](/azure/azure-monitor/platform/data-platform#metrics) jsou číselné hodnoty, které popisují určitý aspekt systému v určitém časovém okamžiku. Jsou odlehčené a schopné podporovat scénáře téměř v reálném čase. Data shromážděná pomocí Azure Monitor najdete přímo na stránce **Přehled** v Azure Portal.

    :::image type="content" source="media/monitor/metrics.png" alt-text="Obrázek ingestování metrik v Průzkumníkovi metrik" border="false":::

2. [Protokoly](/azure/azure-monitor/platform/data-platform#logs) obsahují různé druhy dat uspořádané do záznamů s různými sadami vlastností pro každý typ. Telemetrie, jako jsou události a trasování, se kromě údajů o výkonu ukládají jako protokoly, aby se mohla kombinovat pro účely analýzy. Data protokolu shromážděná pomocí Azure Monitor lze analyzovat pomocí [dotazů](/azure/azure-monitor/log-query/log-query-overview) pro rychlé načítání, konsolidaci a analýzu shromážděných dat. Můžete vytvářet a testovat dotazy pomocí [Log Analytics](/azure/azure-monitor/log-query/portals) v Azure Portal a potom buď přímo analyzovat data pomocí těchto nástrojů, nebo ukládat dotazy pro použití s [vizualizacemi](/azure/azure-monitor/visualizations) nebo [pravidly výstrah](/azure/azure-monitor/platform/alerts-overview).

    :::image type="content" source="media/monitor/logs.png" alt-text="Obrázek přijímání protokolů v Log Analytics" border="false":::

## <a name="how-does-windows-admin-center-enable-you-to-use-azure-monitor"></a>Jak vám centrum pro správu systému Windows umožní použít Azure Monitor?

V centru pro správu Windows můžete povolit tři řešení monitorování:

- [Azure Monitor pro clustery](#onboard-your-cluster-using-windows-admin-center)
- [Azure Update Management](/windows-server/manage/windows-admin-center/azure/azure-update-management) (v nástroji **Updates** )
- Azure Monitor pro virtuální počítače (v nastavení serveru), a. k. a přehledy virtuálních počítačů

Můžete začít používat Azure Monitor z kteréhokoli z těchto nástrojů. Pokud jste už Azure Monitor nikdy nepoužili, bude centrum pro správu systému Windows automaticky zřizovat pracovní prostor Log Analytics (a v případě potřeby Azure Automation účet) a nainstaluje a nakonfiguruje MMA na cílovém serveru. Pak bude příslušné řešení instalovat do pracovního prostoru.

Například pokud nejprve přejdete na nástroj **aktualizace** a nakonfigurujete Update Management Azure, centrum pro správu Windows bude:

1. Nainstalujte do počítače MMA.
2. Vytvořte Log Analytics pracovní prostor a účet Azure Automation (protože v tomto případě je potřeba účet Azure Automation).
3. Nainstalujte řešení Update Management do nově vytvořeného pracovního prostoru.

Pokud chcete přidat další řešení monitorování z centra pro správu Windows na stejný server, centrum pro správu systému Windows jednoduše nainstaluje toto řešení do existujícího pracovního prostoru, ke kterému je tento server připojen. Centrum pro správu systému Windows nainstaluje také další potřebné agenty.

Pokud se připojujete k jinému serveru, ale již jste nastavili pracovní prostor Log Analytics (buď prostřednictvím centra pro správu systému Windows, nebo ručně v Azure Portal), můžete MMA také nainstalovat na server a připojit ho k existujícímu pracovnímu prostoru. Když připojíte Server k pracovnímu prostoru, automaticky se spustí shromažďování dat a vytváření sestav do řešení instalovaných v daném pracovním prostoru.

## <a name="azure-monitor-for-virtual-machines-virtual-machine-insights"></a>Azure Monitor pro virtuální počítače (přehledy služby Virtual Machine Insights)

Když nastavíte Azure Monitor pro virtuální počítače v **nastavení serveru**, centrum pro správu systému Windows umožní řešení Azure monitor pro virtuální počítače, označované také jako přehled služby Virtual Machine Insights. Toto řešení vám umožňuje monitorovat stav serveru a události, vytvářet e-mailová upozornění, získat konsolidované zobrazení výkonu serveru napříč vaším prostředím a vizualizovat aplikace, systémy a služby připojené k danému serveru.

> [!NOTE]
> Bez ohledu na jeho název funguje přehledy virtuálních počítačů u fyzických serverů i virtuálních počítačů.

Díky Azure Monitor zdarma 5 GB dat/měsíc nebo ceny zákazníka můžete tento problém snadno vyzkoušet pro server nebo dva, aniž byste se museli účtovat. Přečtěte si, abyste viděli další výhody připojování serverů do Azure Monitor, jako je například získání konsolidovaného zobrazení výkonu systémů napříč servery ve vašem prostředí.

## <a name="onboard-your-cluster-using-windows-admin-center"></a>Připojení clusteru pomocí centra pro správu Windows

Nejjednodušší způsob, jak připojit cluster k Azure Monitor je pomocí automatizovaného pracovního postupu v centru pro správu systému Windows, které konfiguruje Health Service a Log Analytics, pak nainstaluje MMA.

:::image type="content" source="media/monitor/onboarding.gif" alt-text="Obrázek clusteru připojování k Azure Monitor":::

Na stránce Přehled připojení k serveru klikněte na tlačítko nový **Spravovat výstrahy**nebo přejděte na **nastavení serveru > monitorování a výstrahy**. V rámci této stránky připojte Server k Azure Monitor kliknutím na **nastavit** a dokončením podokna nastavení. Centrum pro správu se postará o zřizování pracovního prostoru Azure Log Analytics, instalaci potřebného agenta a zajištění konfigurace řešení Virtual Machine Insights. Po dokončení bude server odesílat data čítače výkonu Azure Monitor a umožní vám zobrazit a vytvořit e-mailová upozornění založená na tomto serveru, a to z Azure Portal.

## <a name="onboard-your-cluster-manually-using-powershell"></a>Ruční zprovoznění clusteru pomocí PowerShellu

Pokud dáváte přednost ručnímu zprovoznění clusteru, postupujte podle následujících kroků.

### <a name="configure-health-service"></a>Konfigurace Health Service

První věc, kterou je potřeba udělat, je konfigurace clusteru. Jak víte, [Health Service](/windows-server/failover-clustering/health-service-overview) zlepšuje každodenní monitorování a provozní prostředí pro clustery se systémem prostory úložiště s přímým přístupem.

Jak jsme viděli výše, Azure Monitor shromažďuje protokoly z každého uzlu, na kterém je spuštěný ve vašem clusteru. Proto musíme nakonfigurovat Health Service k zápisu do kanálu událostí, který se stane:

```
Event Channel: Microsoft-Windows-Health/Operational
Event ID: 8465
```

Chcete-li nakonfigurovat Health Service, spusťte příkaz:

```PowerShell
get-storagesubsystem clus* | Set-StorageHealthSetting -Name "Platform.ETW.MasTypes" -Value "Microsoft.Health.EntityType.Subsystem,Microsoft.Health.EntityType.Server,Microsoft.Health.EntityType.PhysicalDisk,Microsoft.Health.EntityType.StoragePool,Microsoft.Health.EntityType.Volume,Microsoft.Health.EntityType.Cluster"
```

Po spuštění rutiny uvedené výše se zobrazí nastavení stavu, ve kterém se začnou zapisovat události do kanálu *Microsoft-Windows-Health/Operational* Event.

### <a name="configure-log-analytics"></a>Konfigurace Log Analytics

Teď, když jste si nastavili správné protokolování v clusteru, je dalším krokem správné konfigurace Log Analytics.

K získání přehledu [Azure Log Analytics](/azure/azure-monitor/platform/agent-windows) může shromažďovat data přímo z fyzických nebo virtuálních počítačů s Windows v datovém centru nebo v jiném cloudovém prostředí do jednoho úložiště pro účely podrobných analýz a korelace.

Abyste lépe porozuměli podporované konfiguraci, přečtěte si o [podporovaných operačních systémech Windows](/azure/azure-monitor/platform/log-analytics-agent#supported-windows-operating-systems) a [konfiguraci síťové brány firewall](/azure/azure-monitor/platform/log-analytics-agent#network-firewall-requirements).

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

#### <a name="log-in-to-azure-portal"></a>Přihlášení k webu Azure Portal

Přihlaste se k Azure Portal v [https://portal.azure.com](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) .

#### <a name="create-a-workspace"></a>Vytvoření pracovního prostoru

Další podrobnosti o níže uvedených krocích najdete v [dokumentaci k Azure monitor](/azure/azure-monitor/learn/quick-collect-windows-computer).

1. Na webu Azure Portal klikněte na **Všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics**.

    :::image type="content" source="media/monitor/azure-portal-01.png" alt-text="Azure Portal":::

2. Klikněte na **Vytvořit** a podle potřeby změňte hodnoty následujících položek:

   * Zadejte název nového **pracovního prostoru Log Analytics**, například *DefaultLAWorkspace*.
   * Vyberte **předplatné**, které má být cílem propojení, výběrem z rozevíracího seznamu, pokud výchozí vybrané předplatné není vhodné.
   * Jako **skupinu prostředků** vyberte existující skupinu prostředků, která obsahuje jeden nebo několik virtuálních počítačů Azure.

    :::image type="content" source="media/monitor/create-loganalytics-workspace-02.png" alt-text="Okno pro vytvoření Log Analytics prostředku":::

3. Po zadání požadovaných informací v podokně **log Analyticsho pracovního prostoru** klikněte na tlačítko **OK**.

Během ověřování informací a vytváření pracovního prostoru můžete průběh zpracování sledovat prostřednictvím položky nabídky **Oznámení**.

#### <a name="obtain-workspace-id-and-key"></a>Získání ID a klíče pracovního prostoru
Než nainstalujete MMA pro Windows, budete potřebovat ID a klíč pracovního prostoru pro váš pracovní prostor Log Analytics.  Tyto informace vyžaduje průvodce instalací ke správné konfiguraci agenta a zajištění, aby agent mohl úspěšně komunikovat s Log Analytics.

1. Na webu Azure Portal klikněte v levém horním rohu na **Všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics**.
2. V seznamu pracovních prostorů Log Analytics vyberte *DefaultLAWorkspace* vytvořený dříve.
3. Vyberte **Upřesňující nastavení**.
    :::image type="content" source="media/monitor/log-analytics-advanced-settings-01.png" alt-text="Upřesňující nastavení Log Analytics":::
4. Vyberte **Připojené zdroje** a pak **Servery Windows**.
5. Napravo se zobrazí hodnoty **ID pracovního prostoru** a **Primární klíč**. Uložte do svého oblíbeného editoru jak dočasně zkopírovat, tak i vložit do svého oblíbeného editoru.

### <a name="installing-the-mma-on-windows"></a>Instalace MMA ve Windows
Následující postup nainstaluje a nakonfiguruje Microsoft Monitoring Agent.

> [!IMPORTANT]
> Nezapomeňte nainstalovat tohoto agenta na každý server v clusteru a určit, že chcete, aby se agent spouštěl při spuštění systému Windows.

1. Na stránce **Servery Windows** vyberte odpovídající verzi **agenta pro Windows ke stažení** v závislosti na architektuře procesorů operačního systému Windows.
2. Spusťte instalační program a nainstalujte agenta na svém počítači.
3. Na **úvodní** stránce klikněte na **Další**.
4. Na stránce **Licenční podmínky** si přečtěte licenční podmínky a pak klikněte na **Souhlasím**.
5. Na stránce **Cílová složka** změňte nebo ponechte výchozí instalační složku a pak klikněte na **Další**.
6. Na stránce **Možnosti instalace agenta** zvolte připojení agenta k Azure Log Analytics a pak klikněte na **Další**.
7. Na stránce **Azure Log Analytics** vložte **ID pracovního prostoru** a **klíč pracovního prostoru (primární klíč)** , který jste zkopírovali dříve. Pokud počítač potřebuje komunikovat se službou Log Analytics přes proxy server, klikněte na **Upřesnit** a zadejte adresu URL a číslo portu proxy serveru. Pokud váš proxy server vyžaduje ověření, zadejte uživatelské jméno a heslo pro ověření proxy serveru a pak klikněte na **Další**.
8. Jakmile dokončíte zadávání nezbytných nastavení konfigurace, klikněte na **Další**.
    :::image type="content" source="media/monitor/log-analytics-mma-setup-laworkspace.png" alt-text="vložení ID pracovního prostoru a primárního klíče":::
9. Na stránce **Připraveno k instalaci** zkontrolujte zvolené volby a pak klikněte na **Nainstalovat**.
10. Na stránce **Konfigurace byla úspěšně dokončena** klikněte na **Dokončit**.

Po dokončení se **Microsoft Monitoring Agent** zobrazí v **Ovládacích panelech**. Můžete zkontrolovat svou konfiguraci a ověřit připojení agenta k Log Analytics. Pokud je agent připojený, na kartě **Azure Log Analytics** zobrazí zprávu: **The Microsoft Monitoring Agent has successfully connected to the Microsoft Log Analytics service** (Microsoft Monitoring Agent se úspěšně připojil ke službě Microsoft Log Analytics).

:::image type="content" source="media/monitor/log-analytics-mma-laworkspace-status.png" alt-text="Stav připojení MMA k Log Analytics":::

Abyste lépe porozuměli podporované konfiguraci, přečtěte si o [podporovaných operačních systémech Windows](/azure/azure-monitor/platform/log-analytics-agent#supported-windows-operating-systems) a [konfiguraci síťové brány firewall](/azure/azure-monitor/platform/log-analytics-agent#network-firewall-requirements).

## <a name="setting-up-alerts-using-windows-admin-center"></a>Nastavení výstrah pomocí centra pro správu Windows

Po připojení serveru k Azure Monitor můžete pomocí inteligentních hypertextových odkazů na stránce **nastavení > monitorování a výstrahy** přejít na Azure Portal. V centru pro správu systému Windows můžete snadno nakonfigurovat výchozí výstrahy, které budou platit pro všechny servery v pracovním prostoru Log Analytics. Centrum pro správu systému Windows automaticky umožňuje shromažďování čítačů výkonu, takže můžete [vytvořit novou výstrahu](/azure/azure-monitor/platform/alerts-log) přizpůsobením jednoho z mnoha předem definovaných dotazů nebo napsat vlastní.

:::image type="content" source="media/monitor/setup1.gif" alt-text="Snímek obrazovky konfigurace výstrah":::

Jedná se o výstrahy a jejich výchozí podmínky, se kterými se můžete rozhodnout:

| Název výstrahy                | Výchozí podmínka                                  |
|---------------------------|----------------------------------------------------|
| Využití procesoru           | Více než 85% po dobu 10 minut                            |
| Využití kapacity disku | Více než 85% po dobu 10 minut                            |
| Využití paměti        | Dostupná paměť méně než 100 MB po dobu 10 minut   |
| Tep                 | Méně než 2 Beats po dobu 5 minut                   |
| Kritická chyba systému     | Veškerá kritická výstraha v protokolu systémových událostí clusteru |
| Výstraha služby Health Service      | Jakákoli chyba služby Health Service v clusteru            |

Po nakonfigurování výstrah v centru pro správu systému Windows můžete výstrahy zobrazit v pracovním prostoru Log Analytics v Azure.

:::image type="content" source="media/monitor/setup2.gif" alt-text="Zobrazit snímek obrazovky výstrahy":::

### <a name="collecting-event-and-performance-data"></a>Shromažďování dat o událostech a výkonu

Log Analytics může shromažďovat události z protokolu událostí Windows a z čítačů výkonu, které určíte pro dlouhodobější analýzu a generování sestav, a provést akci při zjištění konkrétní podmínky. Postupujte podle těchto kroků a pro začátek nakonfigurujte shromažďování událostí z protokolu událostí Windows a několika běžných čítačů výkonu.

1. Na webu Azure Portal klikněte v levém dolním rohu na **Další služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics**.
2. Vyberte **Upřesňující nastavení**.
    :::image type="content" source="media/monitor/log-analytics-advanced-settings-01.png" alt-text="Log Analytics Upřesnit nastavení":::
3. Vyberte **Data** a pak vyberte **Protokoly událostí systému Windows**.
4. Sem přidejte Health Service kanál událostí, a to tak, že zadáte následující název a kliknete na symbol plus **+** .
   ```
   Event Channel: Microsoft-Windows-Health/Operational
   ```
5. V tabulce zaškrtněte závažnosti **Chyby** a **Upozornění**.
6. Uložte konfiguraci kliknutím na **Uložit** v horní části stránky.
7. Pokud chcete povolit shromažďování čítačů výkonu na počítači s Windows, vyberte **čítače výkonu systému Windows** .
8. Při první konfiguraci čítačů výkonu Windows pro nový pracovní prostor služby Log Analytics máte možnost rychle vytvořit několik běžných čítačů. Jsou zobrazené v seznamu a vedle každého je zaškrtávací políčko.
    :::image type="content" source="media/monitor/windows-perfcounters-default.png" alt-text="[Vybrané výchozí čítače výkonu Windows":::
    Klikněte na **Přidat vybrané čítače výkonu**.  Čítače se přidají a přednastaví s použitím ukázkového desetisekundového intervalu shromažďování.
9. Uložte konfiguraci kliknutím na **Uložit** v horní části stránky.

## <a name="create-queries-and-alerts-based-on-log-data"></a>Vytváření dotazů a upozornění na základě dat protokolu

Pokud jste to udělali, měl by cluster odesílat protokoly a čítače výkonu do Log Analytics. Dalším krokem je vytvoření pravidel upozornění, která automaticky spouští prohledávání protokolů v pravidelných intervalech. Pokud výsledky prohledávání protokolu splňují určitá kritéria, je vyvolána výstraha, která vám pošle e-mail nebo textové oznámení. Pojďme to prozkoumat níže.

### <a name="create-a-query"></a>Vytvoření dotazu

Začněte tím, že otevřete portál pro **prohledávání protokolů** .

1. Na webu Azure Portal klikněte na **Všechny služby**. V seznamu prostředků zadejte **Monitor**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Monitor**.
2. V navigační nabídce **monitorování** vyberte **Log Analytics** a pak vyberte pracovní prostor.

Nejrychlejší způsob načtení nějakých dat, se kterými můžeme pracovat, je použít tento jednoduchý dotaz, který vrátí všechny záznamy v tabulce. Do vyhledávacího pole zadejte následující dotazy a klikněte na tlačítko Hledat.

```
Event
```

Data se vrátí ve výchozím zobrazení seznamu a zobrazí se celkový počet vrácených záznamů.

:::image type="content" source="media/monitor/log-analytics-portal-eventlist-01.png" alt-text="Snímek obrazovky s jednoduchým dotazem":::

Na levé straně obrazovky je podokno filtru, ve kterém můžete do dotazu přidat filtrování, aniž byste ho upravovali přímo.  Pro daný typ záznamu se zobrazí několik vlastností a výběrem jedné nebo několika hodnot vlastností můžete zúžit výsledky hledání.

Zaškrtněte políčko vedle položky **Chyba** v **EVENTLEVELNAME** nebo zadejte následující pro omezení výsledků na chybové události.

```
Event | where (EventLevelName == "Error")
```

:::image type="content" source="media/monitor/log-analytics-portal-eventlist-02.png" alt-text="Snímek obrazovky filtru":::

Jakmile budete mít vhodné dotazy vytvořené pro události, které vás zajímají, uložte je pro další krok.

### <a name="create-alerts"></a>Vytváření upozornění
Nyní se podívejme na příklad vytvoření výstrahy.

1. Na webu Azure Portal klikněte na **Všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics**.
2. V levém podokně vyberte **Upozornění** a potom nahoře na stránce klikněte na **Nové pravidlo upozornění** a vytvořte nové upozornění.
    :::image type="content" source="media/monitor/alert-rule-02.png" alt-text="Snímek obrazovky pro vytvoření nového pravidla výstrahy":::
3. V prvním kroku v části **vytvořit výstrahu** zvolíte pracovní prostor Log Analytics jako prostředek, protože se jedná o signál výstrahy založený na protokolu.  Filtrování výsledků výběrem konkrétního **předplatného** z rozevíracího seznamu, pokud máte více než jeden, který obsahuje Log Analytics pracovního prostoru, který jste vytvořili dříve.  Vyfiltrujte **typ prostředku** tím, že v rozevíracím seznamu vyberete **Log Analytics**.  Nakonec vyberte v poli **Prostředek položku ** **DefaultLAWorkspace** a pak klikněte na **Hotovo**.
    :::image type="content" source="media/monitor/alert-rule-03.png" alt-text="Vytvořit nové pravidlo upozornění krok 1 snímek obrazovky":::
4. V části **kritéria upozornění**klikněte na **Přidat kritéria** a vyberte uložený dotaz a pak zadejte logiku, které pravidlo výstrahy sleduje.
5. Nakonfigurujte upozornění podle následujících informací: a. Z rozevíracího seznamu na **základě** vyberte **měření metriky**.  Měření metriky vytvoří pro každý objekt dotazu upozornění s hodnotou, která překračuje zadanou prahovou hodnotu.
   b. V případě **podmínky**vyberte **větší než** a zadejte prahovou hodnotu.
   c. Pak definujte, kdy se má výstraha aktivovat. Můžete například vybrat možnost **po sobě jdoucí porušení** a v rozevíracím seznamu vybrat hodnotu **větší než** 3.
   d. V části vyhodnocení na základě oddílu změňte hodnotu **perioda** na **30** minut a **četnost** na 5. Pravidlo se spustí každých pět minut a vrátí záznamy vytvořené za posledních třicet minut od aktuálního času.  Nastavení delšího období zvyšuje potenciál latence dat a zajišťuje, aby dotaz vrátil data a aby se zabránilo falešně negativním hodnotám, kdy se výstraha nespustí.
6. Klikněte na **Hotovo** a dokončete pravidlo upozornění.
    :::image type="content" source="media/monitor/alert-signal-logic-02.png" alt-text="Obrázek konfigurace signálu výstrahy":::
7. Teď se přesunete na druhý krok, v poli **název pravidla výstrahy** zadejte název vaší výstrahy, jako je například **výstraha pro všechny chybové události**.  Do pole **Popis** zadejte podrobné informace o upozornění a v poli **Závažnost** vyberte **Kritické (záv. 0)**.
8. Pokud chcete vytvořené pravidlo ihned aktivovat, potvrďte výchozí hodnotu přepínače **Po vytvoření povolit pravidlo**.
9. Ve třetím a posledním kroku zadejte **Skupinu akcí**, abyste zajistili, že se při každé aktivaci upozornění provedou stejné akce. Skupinu akcí můžete použít pro každé definované pravidlo. Ke konfiguraci nové skupiny akcí použijte následující informace: a. Vyberte **Nová skupina akcí**. Zobrazí se podokno **Přidat skupinu akcí**.
   b. Do pole **Název skupiny akcí** zadejte název, třeba **Operace IT – oznámení** a do pole **Krátký název** zadejte třeba **itop-ozn**.
   c. Zkontrolujte správnost výchozích hodnot v polích **Předplatné** a **Skupina prostředků**. Pokud nejsou správné, vyberte správné hodnoty v rozevíracím seznamu.
   d. V oddílu Akce zadejte název akce, třeba **Poslat e-mail** a jako **Typ akce** vyberte v rozevíracím seznamu **E-mail/SMS/Vytažení/Hlasová**. Vpravo se otevře podokno vlastností **E-mail/SMS/Vytažení/Hlasová**, kde můžete zadat další informace.
   e. V podokně **e-mail/SMS/nabízení/hlas** vyberte a nastavte svou předvolbu. Například povolte **e-mail** a zadejte platnou e-mailovou adresu SMTP pro doručení zprávy do.
   f. Klikněte na tlačítko **OK** a uložte změny.<br><br>

    :::image type="content" source="media/monitor/action-group-properties-01.png" alt-text="Snímek obrazovky pro vytvoření nové skupiny akcí":::

10. Skupinu akcí dokončete kliknutím na **OK**.
11. K dokončení pravidla upozornění klikněte na **Vytvořit pravidlo upozornění**. Pravidlo se okamžitě spustí.
    :::image type="content" source="media/monitor/alert-rule-01.png" alt-text="Snímek obrazovky dokončení vytváření nového pravidla výstrahy":::

### <a name="example-alert"></a>Příklad upozornění

V případě, že se jedná o referenci, vypadá v Azure příklad výstrahy.

:::image type="content" source="media/monitor/alert.gif" alt-text="Snímek obrazovky Azure s upozorněním":::

Níže je příklad e-mailu, který budete odesílat pomocí Azure Monitor:

:::image type="content" source="media/monitor/warning.png" alt-text="Ukázka snímku e-mailu s výstrahou":::

## <a name="create-custom-kusto-queries-in-log-analytics"></a>Vytváření vlastních dotazů Kusto v Log Analytics

Pomocí dotazovacího jazyka Kusto můžete shromažďovat data z jednoho nebo více virtuálních počítačů. můžete také [zapisovat vlastní dotazy protokolu](/azure/azure-monitor/log-query/get-started-queries) v Azure monitor.

## <a name="get-a-consolidated-view-across-multiple-servers"></a>Získat konsolidované zobrazení na více serverech

Pokud připojíte více serverů k jednomu pracovnímu prostoru Log Analytics v rámci Azure Monitor, můžete získat konsolidované zobrazení všech těchto serverů z řešení [Virtual Machines Insights](/azure/azure-monitor/insights/vminsights-overview) v Azure monitor. (Všimněte si, že se na místních serverech budou pracovat jenom karty výkon a mapa služby Virtual Machines Insights pro Azure Monitor – karta stav funguje jenom s virtuálními počítači Azure.) Pokud to chcete zobrazit v Azure Portal, přejděte na **Azure Monitor > Virtual Machines** (v části přehledy) a přejděte na kartu **výkon** nebo **mapy** .

## <a name="visualize-connected-services"></a>Vizualizovat připojené služby

Když centrum pro správu systému Windows zaregistruje server do řešení Virtual Machine Insights v rámci Azure Monitor, povede také k tomu, že se jedná o funkci nazvanou [Service map](/azure/azure-monitor/insights/service-map). Tato funkce automaticky zjišťuje součásti aplikace a mapuje komunikaci mezi službami, takže můžete snadno vizualizovat připojení mezi servery s velkou podrobností od Azure Portal. Můžete to zjistit tak, že přejdete na Azure Portal a vyberete **Azure Monitor > Virtual Machines** (v části přehledy) a přejdete na kartu **mapy** .

> [!NOTE]
> Vizualizace pro přehledy pro Virtual Machine Insights pro Azure Monitor se v současnosti nabízejí ve šesti veřejných oblastech.  Nejnovější informace najdete v [dokumentaci k Azure monitor pro virtuální počítače](/azure/azure-monitor/insights/vminsights-onboard#log-analytics). Pokud chcete získat další výhody poskytované řešením Virtual Machine Insights popsanými výše, musíte nasadit Log Analytics pracovní prostor v jedné z podporovaných oblastí.

## <a name="disabling-monitoring"></a>Zákaz monitorování

Pokud chcete server úplně odpojit od Log Analytics pracovního prostoru, odinstalujte MMA. To znamená, že tento server už nebude odesílat data do pracovního prostoru a všechna řešení nainstalovaná v tomto pracovním prostoru už nebudou shromažďovat a zpracovávat data z tohoto serveru. To ale nemá vliv na samotný pracovní prostor. všechny prostředky, které nahlásí do tohoto pracovního prostoru, budou i nadále pokračovat. Pokud chcete agenta MMA odinstalovat v centru pro správu Windows, připojte se k serveru a pak přejít na **nainstalované aplikace**, Najděte Microsoft Monitoring Agent a pak vyberte **Odebrat**.

Pokud chcete vypnout konkrétní řešení v rámci pracovního prostoru, budete muset [odebrat řešení monitorování z Azure Portal](/azure/azure-monitor/insights/solutions#remove-a-management-solution). Odebrání řešení monitorování znamená, že přehledy vytvořené tímto řešením už nebudou vygenerované pro _žádné_ servery, které do tohoto pracovního prostoru nahlásí. Pokud například odinstalujete řešení Azure Monitor pro virtuální počítače, nebudete už zobrazovat informace o výkonu virtuálního počítače nebo serveru z libovolného počítače připojeného k vašemu pracovnímu prostoru.

## <a name="next-steps"></a>Další kroky

Související témata najdete v tématu také:

- [Další informace o integraci Azure s centrem pro správu Windows](/windows-server/manage/windows-admin-center/azure/)
- [Odeslání e-mailů pro chyby Health Service pomocí Azure Monitor](/windows-server/storage/storage-spaces/configure-azure-monitor)
