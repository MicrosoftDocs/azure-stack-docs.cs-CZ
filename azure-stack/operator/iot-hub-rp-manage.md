---
title: Správa IoT Hub v centru Azure Stack
description: Prostředí pro správu IoT Hub a výstrahy
author: yiyiguo
ms.author: yiygu
ms.service: azure-stack
ms.topic: how-to
ms.date: 1/6/2020
ms.openlocfilehash: 3fe1459d28c399269ccdb1d4cffed024bd741000
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2021
ms.locfileid: "98255737"
---
# <a name="how-to-manage-iot-hub-on-azure-stack-hub"></a>Správa IoT Hub v centru Azure Stack

Prostředí pro správu IoT Hub umožňuje vizualizovat a spravovat celkový stav, výstrahy a kapacitu.

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

## <a name="iot-hub-management-dashboard"></a>Řídicí panel pro správu IoT Hub

Přístup k řídicímu panelu pro správu IoT Hub:

1. Přihlaste se k portálu správce centra Azure Stack, na levé straně vyberte zobrazení **řídicího panelu** a pak vyberte poskytovatele prostředků **IoT Hub** :

   [![řídicí panel operátoru](media\iot-hub-rp-manage\dashboard.png)](media\iot-hub-rp-manage-capacity\dashboard.png#lightbox)

2. Řídicí panel IoT Hub poskytuje souhrnné zobrazení, zobrazuje aktuální výstrahy, kvóty vytvořené na razítku a celkový počet clusterů IoT Hub ve vašem předplatném: 

   [![řídicí panel centra IoT Hub – přehled](media\iot-hub-rp-manage\dashboard-rp-iot-hub-overview.png)](media\iot-hub-rp-manage-capacity\dashboard-rp-iot-hub-overview.png#lightbox)

## <a name="alerts"></a>Upozornění

Poskytovatel prostředků IoT Hub podporuje následující výstrahy:

|Kategorie|Výstrahy|Typ|Popis|
|-|-|-|-|
|Výkon|IoT Hub využití procesoru vyžaduje pozornost.|Upozornění|Průměr% využití CPU poskytovatele prostředků IoT Hub za posledních 6 hodin je větší než 75%.|
|Výkon|Použití IoT Hub paměti vyžaduje pozornost.|Upozornění|Zbývající využití paměti poskytovatele prostředků IoT Hub za posledních 6 hodin má méně než 1024 MB.|
|Výkon|Nedostatek místa na disku pro poskytovatele prostředků IoT Hub.|Upozornění|Zbývající místo na disku je méně než 10%.|
|Prostředek|Nepovedlo se vytvořit nebo aktualizovat prostředek IoT Hub.|Upozornění|IoT Hub poskytovatel prostředků vytvořit nebo aktualizovat IoT Hub počet selhání není kratší než 1 za 15 minut.|
|Služba|Chyby protokolu IoT Hub poskytovatele prostředků vyžadují pozornost.|Upozornění|Počet selhání protokolu IoT Hub poskytovatele prostředků na instanci role je více než 3 za 15 minut.|
|Služba|IoTHub-SU-InternalError|Upozornění|Interní selhání IoTHub SU a počet časových limitů v posledních 30 minutách není menší než 5.|
|Služba|Došlo k chybě zálohování IoT Hub roviny dat.|Upozornění|IoT Hub rovině dat došlo k selháním zálohování během posledních 15 minut. Data zařízení nebudou chráněná.|
|Služba|Došlo k chybě při následném zálohování IoT Hub datové roviny.|Upozornění|IoT Hub rovině dat se během posledních 15 minut vyskytly chyby zálohování po sobě. Data zařízení nebudou chráněná.|
|Služba|IoT Hub rovině dat došlo k chybě zálohování.|Upozornění|IoT Hub nemá v posledních 3 hodinách žádné úspěšné zálohování. Vaše data zařízení možná nebudou chráněná.|
|Služba|IoT Hub obnovení roviny dat se nezdařilo.|Upozornění|Nepovedlo se obnovit informace IoT Hub zařízení. IotHubPerformanceMetrics překračuje prahovou hodnotu za posledních 15 minut.|
|Služba|Došlo k chybě IoT Hub brány.|Upozornění|Došlo k chybě IoT Hub brány. Může to mít vliv na funkčnost telemetrie zařízení.|
|Služba|Došlo k chybě kontejneru IoT Hub.|Upozornění|Došlo k chybě kontejneru IoT Hub. Ověření zařízení může selhat. |
|Služba|Došlo k selhání kontejneru správy zařízení IoT Hub.|Upozornění|Došlo k selhání kontejneru správy zařízení IoT Hub. Je možné, že jsou omezené funkce přímých metod, které jsou v zařízení.|

Monitorování výstrah a kvót:

1. Vybrat **výstrahy** pro zobrazení historie výstrah: 
 
   [![řídicí panel centra IoT – výstrahy](media\iot-hub-rp-manage\dashboard-rp-iot-hub-alerts.png)](media\iot-hub-rp-manage-capacity\dashboard-rp-iot-hub-alerts.png#lightbox)  

2. Vyberte **kvóty** , aby se zobrazil seznam kvót v platnosti:  

   > [!NOTE]
   > Během období Preview je funkce **vytvořit** zakázaná a poskytuje se neomezená výchozí kvóta. **Vytváření** bude povolené pro ga.

   [![řídicí panel centra IoT Hub – kvóty](media\iot-hub-rp-manage\dashboard-rp-iot-hub-quotas.png)](media\iot-hub-rp-manage-capacity\dashboard-rp-iot-hub-quotas.png#lightbox) 

## <a name="capacity-management"></a>Správa kapacity

Jako operátor můžete instanci centra Azure Stack spravovat a provozovat stejným způsobem jako jakýkoli operátor cloudu. Ujistěte se, že je software správně nainstalovaný, správně nakonfigurovaný a zabezpečený a pracuje s vysokou dostupností (HA), a to soudržně a efektivně. V rámci provozu instance centra Azure Stack i poskytovatele prostředků IoT Hub budete muset použít zásady správy kapacity.

### <a name="azure-stack-hub-capacity-management"></a>Správa kapacity centra Azure Stack

Chcete-li určit požadovanou kapacitu pro IoT Hub, budete muset odhadnout zatížení, hlavně počet zařízení a propustnost zprávy. Abychom vám pomohli s plánováním, provedli jsme následující testy v prostředí centra Azure Stack se čtyřmi uzly pro referenci:

| Scenario | Virtuální počítače | Virtuální jádra | Zařízení/centrum | [Edice centra](https://azure.microsoft.com/pricing/details/iot-hub) | Centra | Jednotky a centrum | Celkem zařízení | Celkem jednotek centra | Miliony zpráv za den |
|----------|---------------|------------------|-----------------------|-------------------|-|-|-|-|-|
|Výchozí|5|20|300 000|S2|4|200|1 200 000|800|4 800|
|12 virtuálních počítačů|12|48|500 000|S2|4|200|2 000 000|800|4 800|
|18 virtuálních počítačů|18|72|400 000|S3|4|10|1 600 000|40|12 000|

Další podrobnosti najdete v [Capacity Planner centra Azure Stack](azure-stack-capacity-planner.md) .

### <a name="iot-hub-capacity-management"></a>IoT Hub správa kapacity

Vzhledem k tomu, že centrum Azure Stack je nasazené v místním datovém centru s omezenými prostředky, všechny služby spuštěné v centru Azure Stack sdílejí sdílenou složku a soutěží pro stejný fond zdrojů. Operátoři potřebují plánovat a spravovat kapacitu na základě obchodních potřeb. Poskytovatel prostředků IoT Hub dává operátorům možnost spravovat požadavky na kapacitu pro službu.

IoT Hub má jeden typ virtuálního počítače. V rámci nasazení IoT Hub systém zřídí sadu těchto virtuálních počítačů v centru Azure Stack. Virtuální počítače můžou podporovat určitý počet zařízení a propustnost zpráv. Výchozí nastavení by mělo splňovat většinu požadavků. Pokud ale potřebujete víc zařízení nebo vyšší propustnost zpráv, můžete kapacitu zvýšit pomocí portálu pro správu, CLI nebo PowerShellu. 

Monitorování a změna nastavení kapacity:

1. Vyberte zobrazení **kapacity** na levé straně. Zobrazí se stav kapacity včetně počtu virtuálních počítačů zřízených v clusteru IoT Hub a jejich využití prostředků. Počet zobrazených uzlů je aktuální počet uzlů přidělených IoT Hub. 

2. Pokud chcete zvýšit kapacitu, vyberte IoT Hub název clusteru, změňte počet uzlů a pak vyberte **aktualizovat škálování**. Kapacitu můžete zvýšit tak, že přidáte tolik virtuálních počítačů jako dostupné prostředky k dispozici.

   > [!IMPORTANT]
   > Pro verzi Preview se podporuje jenom IoT Hub škálování clusteru na více instancí (menší až větší). Škálování (s větším až menším) bude podporováno v IoT Hub verze obecné dostupnosti (GA).

   [![řídicí panel centra IoT – kapacita](media\iot-hub-rp-manage\dashboard-rp-iot-hub-capacity.png)](media\iot-hub-rp-manage-capacity\dashboard-rp-iot-hub-capacity.png#lightbox)


## <a name="next-steps"></a>Další kroky

Další informace o:

Azure Stack možnosti monitorování centra, včetně upozorňování, najdete v tématu [monitorování stavu a výstrah](azure-stack-monitor-health.md).

Shromažďování protokolů centra Azure Stack najdete v tématu [přehled Azure Stackho diagnostického protokolu](./diagnostic-log-collection.md).