---
title: Shromažďování protokolů diagnostiky v centru Azure Stack
description: Přečtěte si o shromažďování protokolů diagnostiky v centru pro nápovědu a podporu centra Azure Stack.
author: justinha
ms.topic: article
ms.date: 08/24/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 08/24/2020
ms.openlocfilehash: 841c031b6009cdb7970194a3268010e745e9e0f0
ms.sourcegitcommit: 4922a14fdbc8a3b67df065336e8a21a42f224867
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2020
ms.locfileid: "88764507"
---
# <a name="diagnostic-log-collection-in-azure-stack-hub"></a>Shromažďování protokolů diagnostiky v centru Azure Stack


Centrum Azure Stack je rozsáhlá kolekce součástí Windows i místních služeb Azure, které vzájemně pracují. Všechny tyto komponenty a služby generují svou vlastní sadu protokolů. Abychom zajistili podpora Microsoftu pro efektivní diagnostiku problémů, poskytovali jsme bezproblémové prostředí pro shromažďování protokolů diagnostiky.

Shromažďování diagnostických protokolů v **nápovědě a podpoře**   pomáhá operátorům rychle shromažďovat a sdílet diagnostické protokoly s podpora Microsoftu v jednoduchém uživatelském rozhraní, které nevyžaduje PowerShell. Protokoly se shromažďují i v případě, že jsou mimo provoz jiné služby infrastruktury.  

Shromažďování diagnostických protokolů v nápovědě a podpoře pomáhá operátorům rychle shromažďovat a sdílet diagnostické protokoly se službami zákaznické podpory Microsoftu (CSS), což je jednoduché uživatelské rozhraní, které nevyžaduje PowerShell. Protokoly se shromažďují i v případě, že jsou mimo provoz jiné služby infrastruktury.  

::: moniker range=">= azs-2002"

Doporučuje se použít tento přístup ke shromažďování protokolů a použít ho jenom k [použití privilegovaného koncového bodu (PEP)](azure-stack-get-azurestacklog.md) , pokud není k dispozici portál pro správu nebo okno pro pomoc a podporu. 

>[!NOTE]
>Aby bylo možné používat shromažďování protokolů diagnostiky, musí být rozbočovač Azure Stack zaregistrován. Pokud není zaregistrované centrum Azure Stack, použijte ke sdílení protokolů [příkaz Get-AzureStackLog](azure-stack-get-azurestacklog.md) . 

![Možnosti shromažďování protokolů diagnostiky v centru Azure Stack](media/azure-stack-help-and-support/banner-enable-automatic-log-collection.png)

## <a name="collection-options-and-data-handling"></a>Možnosti kolekce a zpracování dat

::: moniker-end
::: moniker range=">= azs-2005"

V závislosti na připojení k Azure má Azure Stack centrum vhodné způsoby, jak shromažďovat, ukládat a odesílat diagnostické protokoly do šablon stylů CSS. Pokud se centrum Azure Stack může připojit k Azure, doporučuje se povolit **proaktivní shromažďování protokolů**, které při vyvolání kritické výstrahy automaticky nahraje diagnostické protokoly do objektu BLOB úložiště řízeného Microsoftem v Azure. Můžete taky shromažďovat protokoly na vyžádání pomocí příkazu **Odeslat protokoly**, nebo můžete ukládat protokoly místně, pokud je Azure Stack rozbočovač odpojený od Azure. 

Následující části vysvětlují jednotlivé možnosti a způsob, jakým jsou data v jednotlivých případech zpracovávána. 

::: moniker-end

::: moniker range="= azs-2002"
Funkce shromažďování protokolů diagnostiky nabízí dvě možnosti odesílání protokolů. Následující části vysvětlují jednotlivé možnosti a způsob, jakým jsou data v jednotlivých případech zpracovávána. 
::: moniker-end

::: moniker range=">= azs-2002"

## <a name="send-logs-proactively"></a>Proaktivní odesílání protokolů

[Proaktivní shromažďování protokolů](./azure-stack-configure-automatic-diagnostic-log-collection.md?view=azs-2002) zjednodušuje a zjednodušuje shromažďování protokolů diagnostiky, takže zákazníci mohou před otevřením případu podpory Odeslat protokoly společnosti Microsoft. Diagnostické protokoly se proaktivně odesílají z centra Azure Stack k analýze. Tyto protokoly jsou shromažďovány pouze v případě, že je vyvolána [Výstraha o stavu systému](./azure-stack-configure-automatic-diagnostic-log-collection.md?view=azs-2002#proactive-diagnostic-log-collection-alerts) a že k nim přistupovali pouze podpora Microsoftu v kontextu případu podpory.


### <a name="how-the-data-is-handled"></a>Způsob zpracování dat

Souhlasíte s pravidelnými automatickými kolekcemi protokolů od Microsoftu, které jsou založené jenom na upozorněních o stavu systému Azure Stack hub. Také potvrzujete a vyjadřujete souhlas s odesláním a uchováváním těchto protokolů v účtu úložiště Azure spravovaném a řízeným Microsoftem.

Data budou použita pouze při odstraňování výstrah stavu systému a nebudou se používat k marketingovým, reklamním ani jiným komerčním účelům bez vašeho souhlasu. Data je možné uchovávat až 90 dní a veškerá data shromážděná společností Microsoft se budou zpracovávat podle našich [standardních postupů ochrany osobních údajů](https://privacy.microsoft.com/).

Veškerá data, která byla dříve shromážděna s vaším souhlasem, nebudou ovlivněna zrušením oprávnění.

Protokoly shromážděné pomocí **proaktivní kolekce protokolů** se odesílají do účtu úložiště Azure spravovaného a řízeného Microsoftem. K těmto protokolům může společnost Microsoft přihlašovat v rámci případu podpory a zlepšit stav centra Azure Stack.

## <a name="send-logs-now"></a>Poslat protokoly hned

[Odeslat protokoly je teď](./azure-stack-configure-on-demand-diagnostic-log-collection-portal.md?view=azs-2002) ruční možnost, kde se protokoly diagnostiky odesílají z centra Azure Stack jenom v případě, že jste (jako zákazník) zahájili shromažďování, obvykle před otevřením případu podpory.

Azure Stack operátory mohou odesílat diagnostické protokoly na vyžádání podpora Microsoftu pomocí portálu pro správu nebo PowerShellu. Pokud je centrum Azure Stack připojené k Azure, doporučuje se [nyní použít možnost Odeslat protokoly na portálu pro správu](./azure-stack-configure-on-demand-diagnostic-log-collection-portal.md?view=azs-2002) , protože se jedná o nejjednodušší způsob, jak odesílat protokoly přímo do Microsoftu. Pokud portál není dostupný, operátory by místo toho měli [odesílat protokoly pomocí PowerShellu](./azure-stack-configure-on-demand-diagnostic-log-collection-powershell.md?view=azs-2002).

::: moniker-end
::: moniker range="= azs-2002"
Pokud jste odpojeni z Internetu nebo chcete ukládat pouze místní protokoly, použijte k odeslání protokolů metodu [Get-AzureStackLog](azure-stack-get-azurestacklog.md) . Následující vývojový diagram znázorňuje, kterou možnost použít pro odesílání diagnostických protokolů v každém případě. 
::: moniker-end

::: moniker range=">= azs-2002"

![Vývojový diagram ukazuje, jak teď odesílat protokoly do Microsoftu.](media/azure-stack-help-and-support/send-logs-now-flowchart.png)

### <a name="how-the-data-is-handled"></a>Způsob zpracování dat

Tím, že inicializujete shromažďování diagnostických protokolů z centra Azure Stack, potvrzujete a souhlasíte s odesláním těchto protokolů a jejich uchování v účtu úložiště Azure spravovaném a řízeným Microsoftem. Podpora Microsoftu mají přístup k těmto protokolům hned s případem podpory bez nutnosti zapojení zákazníka do shromažďování protokolů.

::: moniker-end

::: moniker range=">= azs-2005"

## <a name="save-logs-locally"></a>Místní uložení protokolů

Protokoly můžete ukládat do místní sdílené složky SMB, když je rozbočovač Azure Stack odpojený od Azure. V okně **Nastavení** zadejte cestu a uživatelské jméno a heslo, které mají oprávnění k zápisu do sdílené složky. Během případu podpory vám podpora Microsoftu poskytne podrobné pokyny, jak přenést tyto místní protokoly. Pokud portál správce není k dispozici, můžete použít [příkaz Get-AzureStackLog](azure-stack-get-azurestacklog.md) k místnímu uložení protokolů.

![Snímek obrazovky s možnostmi shromažďování diagnostických protokolů](media/azure-stack-help-and-support/save-logs-locally.png)

::: moniker-end

::: moniker range=">= azs-2002"

## <a name="bandwidth-considerations"></a>Požadavky na šířku pásma

Průměrná velikost shromažďování protokolů diagnostiky se liší v závislosti na tom, zda se spouští proaktivně nebo ručně. Průměrná velikost pro **proaktivní shromažďování protokolů** je okolo 2 GB. Velikost kolekce pro funkce **Odeslat protokoly je teď** závislá na počtu hodin, které se shromažďují.

V následující tabulce jsou uvedeny požadavky pro prostředí s omezenými nebo měřenými připojeními k Azure.

| Síťové připojení | Dopad |
|----|---|
| Připojení s nízkou šířkou pásma a vysokou latencí | Nahrávání protokolu bude trvat delší dobu, než se dokončí. |
| Sdílené připojení | Nahrávání může mít vliv i na ostatní aplikace nebo uživatele sdílející síťové připojení. |
| Připojení účtované podle objemu dat | Od poskytovatele internetových služeb může docházet k dodatečnému využití sítě. |

::: moniker-end
::: moniker range="<= azs-1910"

## <a name="collecting-logs-from-multiple-azure-stack-hub-systems"></a>Shromažďování protokolů z více systémů Azure Stack hub

Nastavte jeden kontejner objektů BLOB pro každou jednotku škálování centra Azure Stack, ze které chcete shromažďovat protokoly. Další informace o konfiguraci kontejneru objektů BLOB najdete v tématu [Konfigurace automatického shromažďování protokolů diagnostiky Azure Stack hub](./azure-stack-configure-automatic-diagnostic-log-collection.md?view=azs-2002). Osvědčeným postupem je ukládat jenom diagnostické protokoly ze stejné jednotky škálování centra Azure Stack v jednom kontejneru objektů BLOB.

## <a name="retention-policy"></a>Zásady uchovávání informací

Vytvořte [pravidlo správy životního cyklu](/azure/storage/blobs/storage-lifecycle-management-concepts) Azure Blob Storage pro správu zásad uchovávání protokolů. Doporučujeme uchovávat protokoly diagnostiky po dobu 30 dnů. Pokud chcete v Azure Storage vytvořit pravidlo správy životního cyklu, přihlaste se k Azure Portal, vyberte **účty úložiště**, vyberte kontejner objektů BLOB a v části **BLOB Service**vyberte **Správa životního cyklu**.

![Správa životního cyklu v Azure Portal](media/azure-stack-automatic-log-collection/blob-storage-lifecycle-management.png)

## <a name="sas-token-expiration"></a>Vypršení platnosti tokenu SAS

Nastavte vypršení platnosti adresy URL SAS na dva roky. Pokud jste někdy obnovili klíče účtu úložiště, nezapomeňte znovu vygenerovat adresu URL SAS. Token SAS byste měli spravovat podle osvědčených postupů. Další informace najdete v tématu [osvědčené postupy při použití SAS](/azure/storage/common/storage-dotnet-shared-access-signature-part-1#best-practices-when-using-sas).

## <a name="bandwidth-consumption"></a>Spotřeba šířky pásma

Průměrná velikost shromažďování protokolů diagnostiky se liší v závislosti na tom, jestli je shromažďování protokolů na vyžádání nebo automatické.

Pro shromažďování protokolů na vyžádání závisí velikost kolekce logs na tom, kolik hodin se shromažďuje. V posledních sedmi dnech můžete zvolit libovolné posunuté okno v 1-4 hodinách.

Pokud je povoleno automatické shromažďování protokolů diagnostiky, služba sleduje kritické výstrahy. Po vygenerování kritické výstrahy a trvá zhruba 30 minut služba shromažďuje a nahrává vhodné protokoly. Velikost kolekce protokolů je v průměru přibližně 2 GB. Pokud dojde k chybě opravy a aktualizace, spustí se automatické shromažďování protokolů pouze v případě, že je vyvolána kritická výstraha a trvá zhruba 30 minut. Doporučujeme vám postupovat podle [pokynů k monitorování opravy a aktualizace](azure-stack-updates.md). Monitorování výstrah, shromažďování protokolů a nahrávání jsou pro uživatele transparentní.

V systému v pořádku nebudou protokoly shromažďovány vůbec. V systému, který není v pořádku, může shromažďování protokolů v jednom dni běžet dvakrát nebo třikrát, ale obvykle jenom jednou. Ve většině případů to může v nejhorším scénáři trvat až 10 dní.  

Následující tabulka může pomáhat prostředím s omezenými nebo měřenými připojeními k Azure, které berou v úvahu dopad povolování automatického shromažďování protokolů.

| Síťové připojení | Dopad |
|---|---|
| Připojení s nízkou šířkou pásma a vysokou latencí | Nahrávání protokolu bude trvat delší dobu, než se dokončí. | 
| Sdílené připojení | Nahrávání může mít vliv i na ostatní aplikace nebo uživatele sdílející síťové připojení. |
| Připojení účtované podle objemu dat | Od poskytovatele internetových služeb může docházet k dodatečnému využití sítě. |

## <a name="managing-costs"></a>Správa nákladů

[Poplatky za úložiště objektů BLOB](https://azure.microsoft.com/pricing/details/storage/blobs/) v Azure závisí na tom, kolik dat se ukládá každý měsíc a dalších faktorech, jako je třeba redundance dat. Pokud nemáte existující účet úložiště, můžete se přihlásit k Azure Portal, vybrat **účty úložiště**a postupovat podle pokynů k [vytvoření adresy URL SAS kontejneru objektů BLOB v Azure](./azure-stack-configure-automatic-diagnostic-log-collection.md?view=azs-2002).

Osvědčeným postupem je vytvořit [zásadu správy životního cyklu](/azure/storage/blobs/storage-lifecycle-management-concepts) Azure Blob Storage, která minimalizuje průběžné náklady na úložiště. Další informace o tom, jak nastavit účet úložiště, najdete v tématu [Konfigurace automatického shromažďování protokolů diagnostiky Azure Stack hub](./azure-stack-configure-automatic-diagnostic-log-collection.md?view=azs-2002) .

::: moniker-end

## <a name="see-also"></a>Viz také

[Azure Stack zpracování dat protokolů a zákazníků centra](./azure-stack-data-collection.md)
