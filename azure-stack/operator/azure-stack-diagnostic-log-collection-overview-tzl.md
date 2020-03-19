---
title: Přehled shromažďování protokolů diagnostiky centra Azure Stack
description: Vysvětluje shromažďování diagnostických protokolů v centru Azure Stack Help a podpora, včetně kolekce na vyžádání a proaktivní shromažďování protokolů.
author: justinha
ms.topic: article
ms.date: 02/26/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 02/26/2020
ms.openlocfilehash: 8f97ecd20e7ef8db69033268baf96060e1315751
ms.sourcegitcommit: 53efd12bf453378b6a4224949b60d6e90003063b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2020
ms.locfileid: "79520427"
---
# <a name="overview-of-azure-stack-hub-diagnostic-log-collection"></a>Přehled shromažďování protokolů diagnostiky centra Azure Stack 

Centrum Azure Stack je rozsáhlá kolekce součástí Windows i místních služeb Azure, které vzájemně pracují. Všechny tyto komponenty a služby generují svou vlastní sadu protokolů. Abychom službě Microsoft Customer Support Services (CSS) umožnili diagnostikovat problémy efektivně, poskytujeme bezproblémové prostředí pro shromažďování protokolů diagnostiky. 

Shromažďování diagnostických protokolů v nápovědě a podpoře pomáhá operátorům rychle shromažďovat a sdílet diagnostické protokoly se službami zákaznické podpory Microsoftu (CSS), což je jednoduché uživatelské rozhraní, které nevyžaduje PowerShell. Protokoly se shromažďují i v případě, že jsou mimo provoz jiné služby infrastruktury.  
 
Doporučuje se použít tento přístup ke shromažďování protokolů a použít ho jenom k [použití privilegovaného koncového bodu (PEP)](azure-stack-get-azurestacklog.md) , pokud není k dispozici portál pro správu nebo okno pro pomoc a podporu. 

>[!NOTE]
>Centrum Azure Stack musí být zaregistrované a musí mít připojení k Internetu, aby bylo možné používat shromažďování protokolů diagnostiky. Pokud služba Azure Stack hub není zaregistrovaná, sdílejte protokoly [pomocí privilegovaného koncového bodu (PEP)](azure-stack-get-azurestacklog.md) . 

![Snímek obrazovky s možnostmi shromažďování diagnostických protokolů](media/azure-stack-help-and-support/banner-enable-automatic-log-collection.png)

## <a name="collection-options-and-data-handling"></a>Možnosti kolekce a zpracování dat

Funkce shromažďování protokolů diagnostiky nabízí dvě možnosti odesílání protokolů. Následující tabulka vysvětluje jednotlivé možnosti a způsob zpracování dat v jednotlivých případech. 

### <a name="send-logs-proactively"></a>Proaktivní odesílání protokolů

[Proaktivní shromažďování protokolů](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md) zjednodušuje a zjednodušuje shromažďování protokolů diagnostiky, takže zákazníci mohou před otevřením případu podpory Odeslat protokoly společnosti Microsoft. Diagnostické protokoly se proaktivně odesílají z centra Azure Stack k analýze. Tyto protokoly jsou shromažďovány pouze v případě, že je vyvolána [Výstraha o stavu systému](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md#proactive-diagnostic-log-collection-alerts) a k nim mají k dispozici pouze šablony stylů CSS v kontextu případu podpory.


#### <a name="how-the-data-is-handled"></a>Způsob zpracování dat

Souhlasíte s pravidelnými automatickými kolekcemi protokolů od Microsoftu, které jsou založené jenom na upozorněních o stavu systému Azure Stack hub. Také potvrzujete a vyjadřujete souhlas s odesláním a uchováváním těchto protokolů v účtu úložiště Azure spravovaném a řízeným Microsoftem. 

Data budou použita pouze pro účely řešení potíží výstrah stavu systému a nebudou se používat k marketingovým, reklamním ani jiným komerčním účelům bez vašeho souhlasu. Data je možné uchovávat až 90 dnů a všechna data, která Microsoft shromažďuje, se budou zpracovávat v souladu s našimi [standardními postupy ochrany osobních údajů](https://privacy.microsoft.com/).

Žádná data dříve shromážděná s vaším souhlasem nebudou ovlivněna zrušením oprávnění.

Protokoly shromážděné pomocí **proaktivní kolekce protokolů** se odesílají do účtu úložiště Azure spravovaného a řízeného Microsoftem. K těmto protokolům může společnost Microsoft přihlašovat v rámci případu podpory a zlepšit stav centra Azure Stack. |

### <a name="send-logs-now"></a>Poslat protokoly hned

[Odeslat protokoly je teď](azure-stack-configure-on-demand-diagnostic-log-collection-portal-tzl.md) ruční možnost, kde se protokoly diagnostiky odesílají z centra Azure Stack jenom v případě, že jste (jako zákazník) zahájili shromažďování, obvykle před otevřením případu podpory. 

Operátoři Azure Stack můžou odesílat diagnostické protokoly na vyžádání do služeb Microsoft Customer Support Services (CSS) pomocí portálu pro správu nebo PowerShellu. Pokud je centrum Azure Stack připojené k Azure, doporučuje se [nyní použít možnost Odeslat protokoly na portálu pro správu](azure-stack-configure-on-demand-diagnostic-log-collection-portal-tzl.md) , protože se jedná o nejjednodušší způsob, jak odesílat protokoly přímo do Microsoftu. Pokud portál není dostupný, operátory by místo toho měli [odesílat protokoly pomocí PowerShellu](azure-stack-configure-on-demand-diagnostic-log-collection-powershell-tzl.md). 

Pokud jste odpojeni z Internetu nebo chcete ukládat pouze místní protokoly, použijte k odeslání protokolů metodu [Get-AzureStackLog](azure-stack-get-azurestacklog.md) . Následující vývojový diagram znázorňuje, kterou možnost použít pro odesílání diagnostických protokolů v každém případě. 

![Vývojový diagram ukazuje, jak teď odesílat protokoly do Microsoftu.](media/azure-stack-help-and-support/send-logs-now-flowchart.png)

#### <a name="how-the-data-is-handled"></a>Způsob zpracování dat

Tím, že inicializujete shromažďování diagnostických protokolů z centra Azure Stack, potvrzujete a souhlasíte s odesláním těchto protokolů a jejich uchování v účtu úložiště Azure spravovaném a řízeným Microsoftem. Microsoft CSS má k těmto protokolům přístup hned s případem podpory bez nutnosti zapojení zákazníka do shromažďování protokolů. 

Data budou použita pouze pro účely řešení potíží výstrah stavu systému a nebudou se používat k marketingovým, reklamním ani jiným komerčním účelům bez vašeho souhlasu. Data je možné uchovávat až 90 dnů a všechna data, která Microsoft shromažďuje, se budou zpracovávat v souladu s našimi [standardními postupy ochrany osobních údajů](https://privacy.microsoft.com/). 

Protokoly shromážděné pomocí Odeslat protokoly se teď nahrají do spravovaného a řízeného úložiště Microsoftu. K těmto protokolům přistupoval Microsoft v kontextu případu podpory a ke zlepšení stavu centra Azure Stack. 

## <a name="bandwidth-considerations"></a>Požadavky na šířku pásma

Průměrná velikost shromažďování protokolů diagnostiky se liší v závislosti na tom, zda se spouští proaktivně nebo ručně. Průměrná velikost pro **proaktivní shromažďování protokolů** je okolo 2 GB. Velikost kolekce pro funkce **Odeslat protokoly je teď** závislá na počtu hodin, které se shromažďují.

V následující tabulce jsou uvedeny požadavky pro prostředí s omezenými nebo měřenými připojeními k Azure.


| Síťové připojení | Dopad |
|--------------------|--------|
| Připojení s nízkou šířkou pásma a vysokou latencí | Nahrávání protokolu bude trvat delší dobu, než se dokončí. | 
| Sdílené připojení | Nahrávání může mít vliv i na jiné aplikace nebo uživatele sdílející síťové připojení. |
| Připojení účtované podle objemu dat | Od poskytovatele internetových služeb může docházet k dodatečnému využití sítě. | 

## <a name="see-also"></a>Viz také

[Azure Stack zpracování dat protokolů a zákazníků centra](https://docs.microsoft.com/azure-stack/operator/azure-stack-data-collection)

