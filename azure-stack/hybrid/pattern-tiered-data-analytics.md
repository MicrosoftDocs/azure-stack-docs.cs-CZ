---
title: Vzor pro implementaci vrstvených dat pro analytické řešení pomocí Azure a centra Azure Stack.
description: Naučte se používat služby Azure a Azure Stack hub k implementaci řešení vrstvených dat napříč hybridním cloudem.
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 5c19c6b407d4d05c3a4da26078050668d023c86a
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77700744"
---
# <a name="tiered-data-for-analytics-pattern"></a>Model vrstvené dat pro analýzu

Tento model znázorňuje použití centra Azure Stack a Azure pro přípravu, analýzu, zpracování, úpravu a uložení dat napříč různými místními a cloudovým umístěním.

## <a name="context-and-problem"></a>Kontext a problém

Jedním z problémů, které čelí podnikovým organizacím na vývoji moderní technologie, je zabezpečení úložiště, zpracování a analýzy dat. K důležitým hlediskům patří:
- obsah dat
- umístění
- požadavky na zabezpečení a ochranu osobních údajů
- přístupová oprávnění
- svítivost
- skladování úložiště

Azure, v kombinaci s centrem Azure Stack, řeší aspekty dat a nabízí řešení s nízkými náklady. Toto řešení se nejlépe vyjádří prostřednictvím distribuované výrobní nebo logistické společnosti. 

Řešení je založené na následujícím scénáři:
- Rozsáhlá výrobní organizace pro více větví.
- Vyžadují se rychlé a zabezpečené ukládání, zpracování a distribuce dat mezi globálními vzdálenými umístěními a ústředním ústředím. 
- Aktivity zaměstnanců a zařízení, informace o zařízení a data obchodních sestav, která musí zůstat v bezpečí. Data je třeba distribuovat správně a splňovat regionální zásady dodržování předpisů a oborové předpisy.

## <a name="solution"></a>Řešení

Používání místních i veřejných cloudových prostředí splňuje požadavky podniků pro více zařízení. Centrum Azure Stack nabízí rychlé, zabezpečené a flexibilní řešení pro shromažďování, zpracování, ukládání a distribuci místních a vzdálených dat. Zejména v případě, že se zabezpečení, důvěrnost, podnikové zásady a zákonné požadavky mohou mezi umístěními a uživateli lišit. 

![vrstvená data pro architekturu řešení Analytics](media/pattern-tiered-data-analytics/solution-architecture.png)

## <a name="components"></a>Komponenty

Toto řešení používá následující komponenty:

| Vrstva | Komponenta | Popis |
|----------|-----------|-------------|
| Azure | Úložiště | Účet [Azure Storage](/azure/storage/) poskytuje sterilní koncový bod pro datovou spotřebu. Azure Storage je řešení cloudového úložiště Microsoftu pro scénáře moderního datového úložiště. Azure Storage nabízí rozsáhle škálovatelné úložiště objektů pro datové objekty a službu systému souborů pro Cloud. Poskytuje taky úložiště pro zasílání zpráv pro spolehlivé zasílání zpráv a NoSQL úložiště. |
| Centrum Azure Stack | Úložiště | Účet [úložiště centra Azure Stack](/azure-stack/user/azure-stack-storage-overview) se používá pro více služeb:<br>**úložiště objektů Blob** - pro úložiště nezpracovaných dat. Úložiště objektů BLOB může obsahovat jakýkoli typ textu nebo binárních dat, jako je dokument, soubor médií nebo instalační program aplikace. Každý objekt BLOB je uspořádaný do kontejneru. Kontejnery poskytují užitečný způsob, jak přiřadit zásady zabezpečení skupinám objektů. Účet úložiště může obsahovat libovolný počet kontejnerů a kontejner může obsahovat libovolný počet objektů blob, až do limitu kapacity 500 TB účtu úložiště.<br>**úložiště objektů Blob** - pro archiv dat. K dispozici jsou výhody pro vynechání archivů dat s nízkými náklady na úložiště. Příklady studených dat zahrnují zálohy, mediální obsah, vědecká data, dodržování předpisů a Archivovaná data. Obecně platí, že všechna data, ke kterým se běžně přistupovalo, se považují za studená úložiště. vrstvení dat na základě atributů, jako je četnost přístupu a doba uchování. K zákaznickým datům se nepoužívá často, ale vyžaduje podobnou latenci a výkon pro aktivní data.<br>Služba - **Queue Storage** pro zpracovaná úložiště dat. Queue Storage poskytuje cloudové zprávy mezi součástmi aplikace. V návrhu aplikací pro škálování jsou součásti aplikace často odděleny, takže je lze škálovat nezávisle. Queue Storage zajišťuje asynchronní zasílání zpráv pro komunikaci mezi součástmi aplikace.  Bez ohledu na to, jestli běží v cloudu, v desktopovém prostředí, na místním serveru nebo na mobilním zařízení. Queue Storage také podporuje správu asynchronních úloh a pracovní postupy procesů sestavování buildů. |
| | Azure Functions | Službu [Azure Functions](/azure/azure-functions/) poskytuje [Azure App Service v Azure Stack](/azure-stack/operator/azure-stack-app-service-overview) poskytovatel prostředků centra. Azure Functions umožňuje spuštění kódu v jednoduchém prostředí bez serveru, ke spouštění skriptu nebo kódu v reakci na nejrůznější události. Azure Functions škálování pro splnění požadavků bez nutnosti vytvořit virtuální počítač nebo publikovat webovou aplikaci pomocí vámi zvoleného programovacího jazyka. Řešení používají funkce pro:<br>- **příjem dat**<br>- **sterilizaci dat.** Ručně aktivované funkce můžou provádět naplánované zpracování dat, vyčištění a archivaci. Příklady mohou zahrnovat noční a měsíční zpracování sestav zákazníků.|

## <a name="issues-and-considerations"></a>Problémy a důležité informace

Při rozhodování, jak implementovat toto řešení, vezměte v úvahu následující body:

### <a name="scalability"></a>Škálovatelnost 

Řešení Azure functions a Storage se škálují pro splnění požadavků na objem a zpracování dat. Informace o škálovatelnosti a cílech Azure najdete v [dokumentaci Azure Storage škálovatelnosti](/azure/storage/common/storage-scalability-targets). 

### <a name="availability"></a>Dostupnost

Úložiště je primárním aspektem dostupnosti tohoto modelu. Pro zpracování velkých objemů dat a distribuci se vyžadují připojení prostřednictvím rychlých odkazů. 

### <a name="manageability"></a>Možnosti správy

Spravovatelnost tohoto řešení závisí na tom, jaké vývojové nástroje se používají, a na zapojení správy zdrojového kódu. 

## <a name="next-steps"></a>Další kroky

Další informace o tématech zavedených v tomto článku:
- Přečtěte si dokumentaci [Azure Storage](/azure/storage/) a [Azure Functions](/azure/azure-functions/) . Tento model umožňuje používat Azure Storage účty a Azure Functions na rozbočovači Azure i Azure Stack.
- Další informace o osvědčených postupech najdete v tématu [aspekty návrhu hybridní aplikace](overview-app-design-considerations.md) a odpovědi na další otázky.
- Podívejte se na [Azure Stack rodinu produktů a řešení](/azure-stack), abyste se dozvěděli víc o celém portfoliu produktů a řešení.

Až budete připraveni otestovat příklad řešení, pokračujte v [Průvodci nasazením vrstvených dat pro analytické řešení](https://aka.ms/tiereddatadeploy). Průvodce nasazením poskytuje podrobné pokyny pro nasazení a testování jeho komponent.