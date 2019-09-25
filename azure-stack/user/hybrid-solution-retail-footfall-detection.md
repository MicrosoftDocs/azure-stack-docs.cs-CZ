---
title: Hybridní řešení pro implementaci vzoru detekce Footfall založeného na AI pomocí Azure a Azure Stack
description: Naučte se používat služby Azure a Azure Stack k implementaci vzoru detekce Footfall založeného na AI pro analýzu provozu v maloobchodním obchodě.
services: azure-stack
documentationcenter: ''
author: BryanLa
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/23/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 09/23/2019
ms.openlocfilehash: e87baeba1b961c134fc5754fda8c9f1688f3fe90
ms.sourcegitcommit: f38903cb925276e96dd62ebe8ffe92df8bf6e5c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71256531"
---
# <a name="footfall-detection-solution"></a>Řešení Footfall Detection

Tento článek poskytuje přehled řešení pro implementaci vzoru detekce Footfall založeného na AI. Tento model je užitečný při analýze provozu návštěvníků v prodejnách. Řešení generuje přehledy z reálných akcí, pomocí Azure, Azure Stack a sady Custom Vision AI pro vývoj.

## <a name="context-and-problem"></a>Kontext a problém

Obchody společnosti Contoso by chtěli získat přehled o tom, jak zákazníci získávají své aktuální produkty, ve vztahu k uložení rozložení. Nemůžou do každého jednotlivého oddílu umístit zaměstnance a je neefektivně mít tým analytiků, kteří kontrolují celé úložiště na kameře. Kromě toho žádný z jejich úložišť nemá dostatek šířky pásma pro streamování videa ze všech kamer do cloudu pro účely analýzy. 

Společnost Contoso by chtěla najít nenáročné a uživatelsky přívětivý způsob, jak určit demografické údaje zákazníků, věrnostní věci a reakce na ukládání a zobrazování produktů.

## <a name="solution"></a>Řešení

Toto řešení maloobchodní analýzy používá vrstvený přístup k Inferencing na hranici. Pomocí Custom Vision AI dev Kit se k analýze pošlou jenom image s lidskými obličejemi na privátní Azure Stack, na kterých běží Azure Cognitive Services. Anonymní agregovaná data se do Azure odešlou pro agregaci napříč všemi obchody a vizualizací v Power BI. Kombinování hraničního a veřejného cloudu umožňuje společnosti Contoso využívat moderní technologie AI. Zároveň zůstává v souladu se svými podnikovými zásadami a respektuje jejich soukromí zákazníků.

[![Model detekce Footfall](media/hybrid-pattern-ai-footfall-detection/solution-architecture.png)](media/hybrid-pattern-ai-footfall-detection/solution-architecture.png)

Tady je přehled toho, jak řešení funguje: 

1. Sada Custom Vision AI dev Kit Získá konfiguraci z IoT Hub, která nainstaluje IoT Edge modul runtime a model ML.
2. Pokud model uvidí osobu, převezme obrázek a nahraje ho pro Azure Stack úložiště objektů BLOB. 
3. Služba BLOB Service aktivuje funkci Azure na Azure Stack. 
4. Funkce Azure volá kontejner s Face API, aby získala data demografických a emoce z obrázku.
5. Data se replikují a odesílají do centra událostí Azure.
6. Centrum událostí odesílá data do Stream Analytics.
7. Stream Analytics agreguje data a odešle je do Power BI.

## <a name="components"></a>Komponenty

Toto řešení používá následující komponenty:

| Vrstva | Komponenta | Popis |
|----------|-----------|-------------|
| Hardware v obchodě | [Custom Vision AI dev Kit](https://azure.github.io/Vision-AI-DevKit-Pages/) | Poskytuje filtrování in-Store pomocí místního modelu ML, který zachycuje jenom obrázky lidí pro účely analýzy. Bezpečně zřízené a aktualizované prostřednictvím IoT Hub.<br><br>|
| Azure Stack | [App Service](../operator/azure-stack-app-service-overview.md) | Poskytovatel prostředků App Service (RP) poskytuje základ pro komponenty Edge. Včetně funkcí hostování a správy pro webové aplikace/rozhraní API a funkce. |
| | Cluster [modulu AKS (](https://github.com/Azure/aks-engine) Azure Kubernetes Service) | AKS RP s clusterem AKS nasazeným do Azure Stack poskytuje škálovatelný a odolný modul pro spuštění kontejneru Face API. |
| | [Kontejnery Face API](/azure/cognitive-services/face/face-how-to-install-containers) Azure Cognitive Services| Azure Cognitive Services RP s Face API Containers poskytuje demografické, emoce a jedinečné zjišťování návštěvníků v privátní síti společnosti Contoso. |
| | Úložiště objektů blob | Image zachycené ze sady AI dev Kit se nahrají do úložiště objektů BLOB v Azure Stack. |
| | Azure Functions | Funkce Azure běžící na Azure Stack přijímá vstup z úložiště objektů BLOB a spravuje interakce s Face API. Emituje data do centra událostí umístěného v Azure.<br><br>|
| Azure |  |  |
|  | [Azure Event Hubs](/azure/event-hubs/) | Azure Event Hubs poskytuje škálovatelnou platformu pro ingestování anonymních dat, která se v Azure Stream Analytics integrují s využitím. |
|  | [Azure Stream Analytics](/azure/stream-analytics/) | Úloha Azure Stream Analytics agreguje data a seskupuje je do 15 sekund Windows pro vizualizaci. |
|  | [Microsoft Power BI](https://powerbi.microsoft.com/) | Power BI poskytuje snadno použitelné rozhraní řídicího panelu pro zobrazení výstupu z Azure Stream Analytics. |

## <a name="issues-and-considerations"></a>Problémy a důležité informace

Když se budete rozhodovat, jak tento model implementovat, měli byste vzít v úvahu následující skutečnosti:

### <a name="scalability"></a>Škálovatelnost 

Pokud chcete toto řešení povolit pro škálování napříč několika fotoaparáty a umístěními, musíte zajistit, aby všechny součásti mohly zpracovat zvýšené zatížení. Možná budete muset provést následující akce:

- Zvýšit počet Stream Analytics jednotek streamování
- Horizontální navýšení kapacity Face API nasazení
- Zvýšení propustnosti Event Hubs
- V extrémních případech může být potřeba migrovat z Azure Functions do virtuálního počítače.

### <a name="availability"></a>Dostupnost

Vzhledem k tomu, že toto řešení je vrstveno, je důležité vzít v úvahu, jak řešit potíže se sítí nebo výpadky napájení. V závislosti na obchodních potřebách může být vhodné implementovat mechanismus pro místní ukládání imagí do mezipaměti a pak přecházet Azure Stack, když se připojení vrátí. Pokud je umístění dostatečně velké, může být lepší volbou nasazení Data Box Edge s kontejnerem Face API do tohoto umístění.

### <a name="manageability"></a>Možnosti správy

Toto řešení může zahrnovat mnoho zařízení a umístění, což by mohlo mít nepraktický. [Služby IoT v Azure](/azure/iot-fundamentals/) je možné použít k automatickému uvedení nových umístění a zařízení do režimu online a jejich udržování v aktuálním stavu. 

### <a name="security"></a>Zabezpečení

Toto řešení zachycuje obrázky zákazníků a má jistotu na nejdůležitější aspekty. Ujistěte se, že jsou všechny účty úložiště zabezpečené pomocí zásad správného přístupu a že se klíče pravidelně otáčí. Ujistěte se, že účty úložiště a Event Hubs mají zásady uchovávání informací, které splňují předpisy pro ochranu osobních údajů podniku a vlády Také se ujistěte, že všechny úrovně přístupu ke všem osobám, které vyžadují přístup k systému, mají přístup k datům, která potřebují pro jejich roli.

## <a name="next-steps"></a>Další kroky

Další informace o souvisejících vzorech, které toto řešení používá, najdete v tématu [vrstvený model dat](azure-stack-solution-staged-data.md). 

Další informace o používání vlastní vize najdete v [Custom Vision AI dev Kit](https://azure.github.io/Vision-AI-DevKit-Pages/). 