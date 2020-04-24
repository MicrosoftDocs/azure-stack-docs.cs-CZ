---
title: Vrstvená data pro analytické vzory pomocí Azure a centra Azure Stack
description: Naučte se používat Azure a centrum Azure Stack k implementaci řešení vrstvených dat napříč hybridním cloudem.
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: b671fa9f47fa51ab6e40633c04964957d613fec2
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "80891055"
---
# <a name="tiered-data-for-analytics-pattern"></a>Model vrstvené dat pro analýzu

Tento model znázorňuje použití centra Azure Stack a Azure pro přípravu, analýzu, zpracování, úpravu a uložení dat napříč různými místními a cloudovým umístěním.

## <a name="context-and-problem"></a>Kontext a problém

Jedním z problémů, které čelí podnikovým organizacím v oblasti moderní technologie, je zabezpečení úložiště, zpracování a analýzy dat. K důležitým hlediskům patří:

- obsah dat
- location
- požadavky na zabezpečení a ochranu osobních údajů
- přístupová oprávnění
- svítivost
- skladování úložiště

Azure, v kombinaci s centrem Azure Stack, řeší aspekty dat a nabízí řešení s nízkými náklady. Toto řešení se nejlépe vyjádří prostřednictvím distribuované výrobní nebo logistické společnosti.

Řešení je založené na následujícím scénáři:

- Rozsáhlá výrobní organizace pro více větví.
- Vyžadují se rychlé a zabezpečené ukládání, zpracování a distribuce dat mezi globálními vzdálenými umístěními a ústředním ústředím.
- Aktivity zaměstnanců a zařízení, informace o zařízení a data obchodních sestav, která musí zůstat v bezpečí. Data musí být správně distribuována a splňovat regionální zásady dodržování předpisů a oborové předpisy.

## <a name="solution"></a>Řešení

Používání místních i veřejných cloudových prostředí splňuje požadavky podniků pro více zařízení. Centrum Azure Stack nabízí rychlé, zabezpečené a flexibilní řešení pro shromažďování, zpracování, ukládání a distribuci místních a vzdálených dat. Tento model je zvlášť užitečný v případě, že se může lišit zabezpečení, důvěrnost, podnikové zásady a zákonné požadavky mezi umístěními a uživateli.

![Model vrstvenéch dat pro architekturu analytických řešení](media/pattern-tiered-data-analytics/solution-architecture.png)

## <a name="components"></a>Komponenty

Tento model používá následující komponenty:

| Vrstva | Součást | Popis |
|----------|-----------|-------------|
| Azure | Storage | Účet [Azure Storage](/azure/storage/) poskytuje sterilní koncový bod pro datovou spotřebu. Azure Storage je řešení cloudového úložiště Microsoftu pro scénáře moderního datového úložiště. Azure Storage nabízí rozsáhle škálovatelné úložiště objektů pro datové objekty a službu systému souborů pro Cloud. Poskytuje taky úložiště pro zasílání zpráv pro spolehlivé zasílání zpráv a NoSQL úložiště. |
| Centrum Azure Stack | Storage | Účet [úložiště centra Azure Stack](/azure-stack/user/azure-stack-storage-overview) se používá pro více služeb:<br><br>- **Úložiště objektů BLOB** pro úložiště nezpracovaných dat. Úložiště objektů BLOB může obsahovat jakýkoli typ textu nebo binárních dat, jako je dokument, soubor médií nebo instalační program aplikace. Každý objekt BLOB je uspořádaný do kontejneru. Kontejnery poskytují užitečný způsob, jak přiřadit zásady zabezpečení skupinám objektů. Účet úložiště může obsahovat libovolný počet kontejnerů a kontejner může obsahovat libovolný počet objektů blob, až do limitu kapacity 500 TB účtu úložiště.<br>- **Úložiště objektů BLOB** pro archiv dat K dispozici jsou výhody pro vynechání archivů dat s nízkými náklady na úložiště. Příklady studených dat zahrnují zálohy, mediální obsah, vědecká data, dodržování předpisů a Archivovaná data. Obecně platí, že všechna data, která jsou k častému použití, se považují za studená úložiště. Vrstvení dat na základě atributů, jako je četnost přístupu a doba uchování. K zákaznickým datům se nepoužívá často, ale vyžaduje podobnou latenci a výkon pro aktivní data.<br>- **Vyřadí úložiště do fronty** pro zpracovaná úložiště dat. Queue Storage poskytuje cloudové zasílání zpráv mezi součástmi aplikací. Při navrhování aplikací pro škálování se součásti aplikací často odpojí, aby se mohly škálovat nezávisle. Queue Storage zajišťuje asynchronní zasílání zpráv pro komunikaci mezi součástmi aplikací, ať už běží v cloudu, na ploše, na místním serveru nebo na mobilním zařízení. Queue Storage také podporuje správu asynchronních úloh a pracovní postupy procesů sestavování buildů. |
| | Azure Functions | Službu [Azure Functions](/azure/azure-functions/) poskytuje [Azure App Service v Azure Stack](/azure-stack/operator/azure-stack-app-service-overview) poskytovatel prostředků centra. Azure Functions umožňuje spustit kód v jednoduchém prostředí bez serveru v reakci na nejrůznější události. Azure Functions škálování pro splnění požadavků bez nutnosti vytvořit virtuální počítač nebo publikovat webovou aplikaci pomocí vámi zvoleného programovacího jazyka. Řešení používají funkce pro:<br><br>- **Příjem dat**<br>- **Sterilizace dat.** Ručně aktivované funkce můžou provádět naplánované zpracování dat, vyčištění a archivaci. Příklady mohou zahrnovat noční a měsíční zpracování sestav zákazníků.|

## <a name="issues-and-considerations"></a>Problémy a důležité informace

Při rozhodování, jak implementovat toto řešení, vezměte v úvahu následující body:

### <a name="scalability"></a>Škálovatelnost

Azure Functions a řešení úložiště se škálují pro splnění požadavků na objem a zpracování dat. Informace o škálovatelnosti a cílech Azure najdete v [dokumentaci Azure Storage škálovatelnosti](/azure/storage/common/storage-scalability-targets).

### <a name="availability"></a>Dostupnost

Úložiště je primárním aspektem dostupnosti tohoto modelu. Pro zpracování velkých objemů dat a distribuci se vyžadují připojení prostřednictvím rychlých odkazů.

### <a name="manageability"></a>Možnosti správy

Spravovatelnost tohoto řešení závisí na tom, jaké vývojové nástroje se používají, a na zapojení správy zdrojového kódu.

## <a name="next-steps"></a>Další kroky

Další informace o tématech zavedených v tomto článku:

- Přečtěte si dokumentaci [Azure Storage](/azure/storage/) a [Azure Functions](/azure/azure-functions/) . Tento model umožňuje používat Azure Storage účty a Azure Functions na rozbočovači Azure i Azure Stack.
- Další informace o osvědčených postupech a získání odpovědí na další otázky najdete v tématu [aspekty návrhu hybridní aplikace](overview-app-design-considerations.md) .
- Další informace o celém portfoliu produktů a řešení najdete v [Azure Stack rodině produktů a řešení](/azure-stack) .

Až budete připraveni otestovat příklad řešení, pokračujte v [Průvodci nasazením vrstvených dat pro analytické řešení](https://aka.ms/tiereddatadeploy). Průvodce nasazením poskytuje podrobné pokyny pro nasazení a testování jeho komponent.
