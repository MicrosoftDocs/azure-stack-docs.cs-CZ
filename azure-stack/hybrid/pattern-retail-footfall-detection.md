---
title: Hybridní vzor pro implementaci rozpoznávání Footfall založeného na AI pomocí centra Azure a centra Azure Stack.
description: Naučte se používat služby Azure a Azure Stack hub k implementaci řešení pro detekci Footfall založeného na AI pro analýzu provozu v maloobchodním obchodě.
author: BryanLa
ms.topic: article
ms.date: 10/31/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 10/31/2019
ms.openlocfilehash: 27fb31a29313543c3eec2b973cdf8e8ce32940fd
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76877319"
---
# <a name="footfall-detection-pattern"></a>Model detekce Footfall

Tento model poskytuje přehled implementace řešení pro detekci Footfall založené na AI pro analýzu provozu návštěvníků v prodejních. Řešení generuje přehledy z reálných akcí, pomocí Azure, centra Azure Stack a Custom Vision AI pro vývojáře.

## <a name="context-and-problem"></a>Kontext a problém

Obchody společnosti Contoso by chtěli získat přehled o tom, jak zákazníci získávají své aktuální produkty, ve vztahu k uložení rozložení. Nemůžou do každého oddílu umístit pracovníky a je neefektivně mít tým analytiků, kteří kontrolují celý záběr v obchodě. Kromě toho žádný z jejich úložišť nemá dostatek šířky pásma pro streamování videa ze všech kamer do cloudu pro účely analýzy. 

Společnost Contoso by chtěla najít nenáročné a uživatelsky přívětivý způsob, jak určit demografické údaje zákazníků, věrnostní věci a reakce na ukládání a zobrazování produktů.

## <a name="solution"></a>Řešení

Tento vzor maloobchodní analýzy používá vrstvený přístup k Inferencing na hranici. Pomocí Custom Vision AI dev Kit se k analýze odesílají jenom obrázky s lidskými obličejemi do privátního centra Azure Stack, na kterém běží Azure Cognitive Services. Anonymní agregovaná data se do Azure odešlou pro agregaci napříč všemi obchody a vizualizací v Power BI. Kombinování hraničního a veřejného cloudu umožňuje společnosti Contoso využívat moderní technologie AI. Zároveň zůstává v souladu se svými podnikovými zásadami a respektuje jejich soukromí zákazníků.

[![řešení pro detekci Footfall](media/pattern-retail-footfall-detection/solution-architecture.png)](media/pattern-retail-footfall-detection/solution-architecture.png)

Tady je přehled toho, jak řešení funguje: 

1. Sada Custom Vision AI dev Kit Získá konfiguraci z IoT Hub, která nainstaluje IoT Edge modul runtime a model ML.
2. Pokud se model dohlíží na osobu, převezme obrázek a nahraje ho pro Azure Stack úložiště objektů BLOB hub. 
3. Služba BLOB Service aktivuje funkci Azure ve službě Azure Stack hub. 
4. Funkce Azure volá kontejner s Face API, aby získala data demografických a emoce z obrázku.
5. Data se v clusteru Azure Event Hubs neodesílají a odešlou.
6. Cluster Event Hubs zasune data do Stream Analytics.
7. Stream Analytics agreguje data a odešle je do Power BI.

## <a name="components"></a>Komponenty

Toto řešení používá následující komponenty:

| Vrstva | Součást | Popis |
|----------|-----------|-------------|
| Hardware v obchodě | [Custom Vision AI dev Kit](https://azure.github.io/Vision-AI-DevKit-Pages/) | Poskytuje filtrování in-Store pomocí místního modelu ML, který zachycuje jenom obrázky lidí pro účely analýzy. Bezpečně zřízené a aktualizované prostřednictvím IoT Hub.<br><br>|
| Azure | [Azure Event Hubs](/azure/event-hubs/) | Azure Event Hubs poskytuje škálovatelnou platformu pro ingestování anonymních dat, která se v Azure Stream Analytics integrují s využitím. |
|  | [Azure Stream Analytics](/azure/stream-analytics/) | Úloha Azure Stream Analytics agreguje data a seskupuje je do 15 sekund Windows pro vizualizaci. |
|  | [Microsoft Power BI](https://powerbi.microsoft.com/) | Power BI poskytuje snadno použitelné rozhraní řídicího panelu pro zobrazení výstupu z Azure Stream Analytics. |
| Azure Stack Hub | [App Service](../operator/azure-stack-app-service-overview.md) | Poskytovatel prostředků App Service (RP) poskytuje základ pro komponenty Edge. Včetně funkcí hostování a správy pro webové aplikace/rozhraní API a funkce. |
| | Cluster [modulu AKS (](https://github.com/Azure/aks-engine) Azure Kubernetes Service) | AKS RP s clusterem AKS nasazeným do centra Azure Stack přináší škálovatelný a odolný modul pro spuštění kontejneru Face API. |
| | [Kontejnery Face API](/azure/cognitive-services/face/face-how-to-install-containers) Azure Cognitive Services| Azure Cognitive Services RP s Face API Containers poskytuje demografické, emoce a jedinečné zjišťování návštěvníků v privátní síti společnosti Contoso. |
| | Blob Storage | Image zachycené ze sady AI dev Kit se nahrají do úložiště objektů BLOB v centru Azure Stack. |
| | Funkce Azure | Funkce Azure spuštěná v centru Azure Stack přijímá vstup z úložiště objektů BLOB a spravuje interakce s Face API. Emituje data do clusteru Event Hubs umístěného v Azure.<br><br>|

## <a name="issues-and-considerations"></a>Problémy a důležité informace

Při rozhodování, jak implementovat toto řešení, vezměte v úvahu následující body:

### <a name="scalability"></a>Škálovatelnost 

Pokud chcete toto řešení povolit pro škálování napříč několika fotoaparáty a umístěními, musíte zajistit, aby všechny součásti mohly zpracovat zvýšené zatížení. Možná budete muset provést následující akce:

- Zvýšit počet Stream Analytics jednotek streamování
- Horizontální navýšení kapacity Face API nasazení
- Zvýšení propustnosti Event Hubs clusteru
- V extrémních případech může být potřeba migrovat z Azure Functions do virtuálního počítače.

### <a name="availability"></a>Dostupnost

Vzhledem k tomu, že toto řešení je vrstveno, je důležité vzít v úvahu, jak řešit potíže se sítí nebo výpadky napájení. V závislosti na obchodních potřebách může být vhodné implementovat mechanismus pro místní ukládání imagí do mezipaměti a pak přecházet do centra Azure Stack, když se připojení vrátí. Pokud je umístění dostatečně velké, může být lepší volbou nasazení Data Box Edge s kontejnerem Face API do tohoto umístění.

### <a name="manageability"></a>Možnosti správy

Toto řešení může zahrnovat mnoho zařízení a umístění, což by mohlo mít nepraktický. [Služby IoT v Azure](/azure/iot-fundamentals/) je možné použít k automatickému uvedení nových umístění a zařízení do režimu online a jejich udržování v aktuálním stavu. 

### <a name="security"></a>Zabezpečení

Toto řešení zachycuje obrázky zákazníků a má jistotu na nejdůležitější aspekty. Ujistěte se, že jsou všechny účty úložiště zabezpečené pomocí zásad správného přístupu a že se klíče pravidelně otáčí. Ujistěte se, že účty úložiště a Event Hubs mají zásady uchovávání informací, které splňují předpisy pro ochranu osobních údajů podniku a vlády Také nezapomeňte nastavit úroveň přístupu uživatele. Vrstvení zajišťuje, že uživatelé mají přístup jenom k datům, která potřebují pro jejich roli.

## <a name="next-steps"></a>Další kroky

Další informace o tématech představených v tomto článku:
- Podívejte se na [model vrstvené dat](https://aka.ms/tiereddatadeploy), který je vydaný vzorem detekce Footfall.
- Další informace o používání vlastní vize najdete v sadě [Custom Vision AI dev Kit](https://azure.github.io/Vision-AI-DevKit-Pages/) . 

Až budete připraveni otestovat příklad řešení, pokračujte v [Průvodci nasazením Footfall Detection](solution-deployment-guide-retail-footfall-detection.md). Průvodce nasazením poskytuje podrobné pokyny pro nasazení a testování jeho komponent.
