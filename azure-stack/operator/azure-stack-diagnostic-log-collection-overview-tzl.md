---
title: Přehled shromažďování protokolů diagnostiky centra Azure Stack
description: Vysvětluje shromažďování diagnostických protokolů v centru Azure Stack Help a podpora, včetně kolekce na vyžádání a proaktivní shromažďování protokolů.
author: justinha
ms.topic: article
ms.date: 02/26/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 02/26/2020
ms.openlocfilehash: b1c1048a8ad8bdb8d16d2e86c82febc8c74b03af
ms.sourcegitcommit: 355e21dd9b8c3f44e14abaae0b4f176443cf7495
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81624937"
---
# <a name="overview-of-azure-stack-hub-diagnostic-log-collection"></a>Přehled shromažďování protokolů diagnostiky centra Azure Stack 

::: moniker range=">= azs-2002"

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

::: moniker-end
::: moniker range="<= azs-1910"

## <a name="collecting-logs-from-multiple-azure-stack-hub-systems"></a>Shromažďování protokolů z více systémů Azure Stack hub

Nastavte jeden kontejner objektů BLOB pro každou jednotku škálování centra Azure Stack, ze které chcete shromažďovat protokoly. Další informace o konfiguraci kontejneru objektů BLOB najdete v tématu [Konfigurace automatického shromažďování protokolů diagnostiky Azure Stack hub](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md). Osvědčeným postupem je ukládat jenom diagnostické protokoly ze stejné jednotky škálování centra Azure Stack v jednom kontejneru objektů BLOB. 

## <a name="retention-policy"></a>Zásady uchovávání informací

Vytvořte [pravidlo správy životního cyklu](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts) Azure Blob Storage pro správu zásad uchovávání protokolů. Doporučujeme uchovávat protokoly diagnostiky po dobu 30 dnů. Pokud chcete vytvořit pravidlo správy životního cyklu ve službě Azure Storage, přihlaste se k Azure Portal, klikněte na **účty úložiště**, klikněte na kontejner objektů BLOB a v části **BLOB Service**klikněte na **Správa životního cyklu**.

![Snímek obrazovky znázorňující správu životního cyklu v Azure Portal](media/azure-stack-automatic-log-collection/blob-storage-lifecycle-management.png)


## <a name="sas-token-expiration"></a>Vypršení platnosti tokenu SAS

Nastavte vypršení platnosti adresy URL SAS na dva roky. Pokud jste někdy obnovili klíče účtu úložiště, nezapomeňte znovu vygenerovat adresu URL SAS. Token SAS byste měli spravovat podle osvědčených postupů. Další informace najdete v tématu [osvědčené postupy při použití SAS](https://docs.microsoft.com/azure/storage/common/storage-dotnet-shared-access-signature-part-1#best-practices-when-using-sas).


## <a name="bandwidth-consumption"></a>Spotřeba šířky pásma

Průměrná velikost shromažďování protokolů diagnostiky se liší v závislosti na tom, jestli je shromažďování protokolů na vyžádání nebo automatické. 

Pro shromažďování protokolů na vyžádání závisí velikost kolekce logs na tom, kolik hodin se shromažďuje. V posledních sedmi dnech můžete zvolit libovolné posunuté okno v 1-4 hodinách. 

Pokud je povoleno automatické shromažďování protokolů diagnostiky, služba sleduje kritické výstrahy. 
Po vygenerování kritické výstrahy a trvá zhruba 30 minut služba shromažďuje a nahrává vhodné protokoly. 
Velikost kolekce protokolů je v průměru přibližně 2 GB. 
V případě chyby opravy a aktualizace se automatické shromažďování protokolů spustí pouze v případě, že je vyvolána kritická výstraha a trvá zhruba 30 minut. Doporučujeme vám postupovat podle [pokynů k monitorování opravy a aktualizace](azure-stack-updates.md).
Monitorování výstrah, shromažďování protokolů a nahrávání jsou pro uživatele transparentní. 



V systému v pořádku nebudou protokoly shromažďovány vůbec. 
V systému, který není v pořádku, může shromažďování protokolů v jednom dni běžet dvakrát nebo třikrát, ale obvykle jenom jednou. 
Ve většině případů to může v nejhorším scénáři trvat až desetkrát za den.  

Následující tabulka může pomáhat prostředím s omezenými nebo měřenými připojeními k Azure, které berou v úvahu dopad povolování automatického shromažďování protokolů.

| Síťové připojení | Dopad |
|--------------------|--------|
| Připojení s nízkou šířkou pásma a vysokou latencí | Nahrávání protokolu bude trvat delší dobu, než se dokončí. | 
| Sdílené připojení | Nahrávání může mít vliv i na jiné aplikace nebo uživatele sdílející síťové připojení. |
| Připojení účtované podle objemu dat | Od poskytovatele internetových služeb může docházet k dodatečnému využití sítě. |


## <a name="managing-costs"></a>Správa nákladů

[Poplatky za službu Azure Blob Storage](https://azure.microsoft.com/pricing/details/storage/blobs/) závisí na množství dat uložených každý měsíc a dalších faktorech, jako je třeba redundance dat. 
Pokud nemáte existující účet úložiště, můžete se přihlásit k Azure Portal, kliknout na **účty úložiště**a podle postupu [vytvořit adresu URL SAS kontejneru objektů BLOB v Azure](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md).

Osvědčeným postupem je vytvořit [zásadu správy životního cyklu](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts) Azure Blob Storage, která minimalizuje průběžné náklady na úložiště. Další informace o tom, jak nastavit účet úložiště, najdete v tématu [Konfigurace automatického shromažďování protokolů diagnostiky Azure Stack hub](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md) .

::: moniker-end

## <a name="see-also"></a>Viz také

[Azure Stack zpracování dat protokolů a zákazníků centra](https://docs.microsoft.com/azure-stack/operator/azure-stack-data-collection)

