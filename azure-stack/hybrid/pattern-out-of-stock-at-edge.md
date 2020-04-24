---
title: Neuložené zjišťování pomocí Azure a Azure Stack Edge
description: Naučte se používat Azure a Azure Stack Edge Services k implementaci detekce z provozu.
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 865f63bc4234e50ed169aa29cefdb1886750594c
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "80891106"
---
# <a name="out-of-stock-detection-at-the-edge-pattern"></a>Zjišťování z hranice hraničního modelu z neaktivního zásobníku

Tento model znázorňuje, jak zjistit, jestli jsou položky police mimo skladové položky pomocí Azure Stack hraničních zařízení nebo Azure IoT Edgech síťových fotoaparátů.

## <a name="context-and-problem"></a>Kontext a problém

Fyzické maloobchodní obchody ztratí prodej, protože když zákazníci hledají položku, neexistují na police. Položka však může být na zadní straně úložiště a nebyla přeuložená. Úložiště by chtěla používat své zaměstnance efektivněji a automaticky dostávat oznámení, když je potřeba přezásobovat položky.

## <a name="solution"></a>Řešení

Příklad řešení používá hraniční zařízení, například Azure Stack Edge v každém obchodě, což efektivně zpracovává data z fotoaparátů v úložišti. Tento optimalizovaný návrh umožňuje úložištím odesílat do cloudu pouze relevantní události a obrázky. Návrh šetří šířku pásma, prostor úložiště a zajišťuje ochranu osobních údajů zákazníků. Když jsou snímky čteny z jednotlivých kamer, model ML zpracuje obrázek a vrátí z oblasti akcií. Obrázek a mimo oblast zásob se zobrazí v místní webové aplikaci. Tato data se dají odeslat do prostředí Time Series Insight, kde se zobrazí přehledy o Power BI.

![Neuložený v architektuře hraničních řešení](media/pattern-out-of-stock-at-edge/solution-architecture.png)

Jak řešení funguje:

1. Image se zaznamenávají ze síťové kamery přes protokol HTTP nebo RTSP.
2. Velikost bitové kopie se změní a pošle se do ovladače odvození, který komunikuje s modelem ML a určí, jestli jsou dostupné žádné z uložených imagí.
3. Model ML vrátí jakékoli z burzovních oblastí.
4. Ovladač Inferencing nahraje nezpracovaný obrázek do objektu BLOB (Pokud je zadaný) a pošle výsledky z modelu do Azure IoT Hub a s procesorem ohraničujícího pole na zařízení.
5. Procesor ohraničujících uzlů přidává do obrázku ohraničovací rámečky a cestu k imagi ukládá do mezipaměti v paměti databáze.
6. Webová aplikace se dotazuje na obrázky a zobrazí je v přijatém pořadí.
7. Zprávy z IoT Hub jsou agregovány v Time Series Insights.
8. Power BI zobrazí interaktivní sestavu z neskladovaných položek v průběhu času s daty z Time Series Insights.


## <a name="components"></a>Komponenty

Toto řešení používá následující komponenty:

| Vrstva | Součást | Popis |
|----------|-----------|-------------|
| Místní hardware | Síťová kamera | Je vyžadován síťový fotoaparát s kanálem HTTP nebo RTSP pro poskytování imagí pro odvození. |
| Azure | Azure IoT Hub | [Azure IoT Hub](/azure/iot-hub/) zpracovává zřizování zařízení a zasílání zpráv pro hraniční zařízení. |
|  | Azure Time Series Insights | [Azure Time Series Insights](/azure/time-series-insights/) ukládá zprávy z IoT Hub pro vizualizaci. |
|  | Power BI | [Microsoft Power BI](https://powerbi.microsoft.com/) poskytuje podnikové sestavy o neskladovaných událostech. Power BI poskytuje snadno použitelné rozhraní řídicího panelu pro zobrazení výstupu z Azure Stream Analytics. |
| Azure Stack Edge nebo<br>Azure IoT Edge zařízení | Azure IoT Edge | [Azure IoT Edge](/azure/iot-edge/) orchestruje modul runtime pro místní kontejnery a zpracovává správu a aktualizace zařízení.|
| | Brainwave projektu Azure | V Azure Stack hraničním zařízení [Brainwave Project](https://blogs.microsoft.com/ai/build-2018-project-brainwave/) pro zrychlení inferencingí používá pole brány (FPGA) s programovatelnými poli.|

## <a name="issues-and-considerations"></a>Problémy a důležité informace

Při rozhodování, jak implementovat toto řešení, vezměte v úvahu následující body:

### <a name="scalability"></a>Škálovatelnost

Většina modelů strojového učení se dá spustit jenom na určitém počtu snímků za sekundu v závislosti na zadaném hardwaru. Určete optimální vzorkovací frekvenci z fotoaparátů, aby se zajistilo, že kanál ML nezálohuje. Různé typy hardwaru budou pracovat s různými počty fotoaparátů a snímků.

### <a name="availability"></a>Dostupnost

Je důležité zvážit, co se může stát, když hraniční zařízení ztratí připojení. Zvažte, jaká data mohou být ztracena z Time Series Insights a Power BI řídicím panelu. Ukázkové řešení, které je uvedené, není navržené tak, aby bylo vysoce dostupné.

### <a name="manageability"></a>Možnosti správy

Toto řešení může zahrnovat mnoho zařízení a umístění, což by mohlo mít nepraktický. Služby IoT v Azure můžou automaticky přenést nová umístění a zařízení do režimu online a udržovat je v aktuálním stavu. Musí následovat i správné postupy správného řízení dat.

### <a name="security"></a>Zabezpečení

Tento model zpracovává potenciálně citlivá data. Ujistěte se, že jsou klíče pravidelně otočené a že jsou správně nastavená oprávnění pro účet Azure Storage a místní sdílené složky.

## <a name="next-steps"></a>Další kroky

Další informace o tématech zavedených v tomto článku:
- V tomto vzoru se používá několik služeb souvisejících s IoT, včetně [Azure IoT Edge](/azure/iot-edge/), [Azure IoT Hub](/azure/iot-hub/)a [Azure Time Series Insights](/azure/time-series-insights/).
- Další informace o Microsoft Project Brainwave najdete [v oznámení na blogu](https://blogs.microsoft.com/ai/build-2018-project-brainwave/) a s využitím [Projectu Brainwave video k rezervaci Azure akcelerovaných Machine Learning](https://www.youtube.com/watch?v=DJfMobMjCX0).
- Další informace o osvědčených postupech a odpovědi na další otázky najdete v tématu [aspekty návrhu hybridní aplikace](overview-app-design-considerations.md) .
- Další informace o celém portfoliu produktů a řešení najdete v [Azure Stack rodině produktů a řešení](/azure-stack) .

Až budete připraveni otestovat příklad řešení, pokračujte v [Průvodci nasazením vrstvených dat pro analytické řešení](https://aka.ms/edgeinferencingdeploy). Průvodce nasazením poskytuje podrobné pokyny pro nasazení a testování jeho komponent.
