---
title: Shromažďování protokolů diagnostiky
description: Přečtěte si o shromažďování protokolů diagnostiky.
author: PatAltimore
ms.topic: article
ms.date: 02/03/2021
ms.author: patricka
ms.reviewer: shisab
ms.lastreviewed: 02/03/2021
ms.openlocfilehash: ad5f0a7f6028249dba3d63490cdc3c91d7a45e72
ms.sourcegitcommit: 69c700a456091adc31e4a8d78e7a681dfb55d248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/10/2021
ms.locfileid: "100013213"
---
# <a name="diagnostic-log-collection"></a>Shromažďování protokolů diagnostiky

Můžete sdílet diagnostické protokoly vytvořené pomocí centra Azure Stack. Tyto protokoly jsou vytvářeny součástmi systému Windows a místními službami Azure. Podpora Microsoftu můžou pomocí protokolů opravovat nebo identifikovat problémy s instancí centra Azure Stack.

Chcete-li začít se shromažďováním protokolů diagnostiky centra Azure Stack, je nutné svou instanci zaregistrovat. Pokud jste nezaregistrovali Azure Stack centrum, sdílejte protokoly pomocí [privilegovaného koncového bodu (PEP)](azure-stack-get-azurestacklog.md) . 

::: moniker range=">= azs-2005"

Existují různé způsoby, jak odesílat diagnostické protokoly do podpora Microsoftu. V závislosti na vašem připojení k Azure máte tyto možnosti:
* [Proaktivní odesílání protokolů (doporučeno)](#send-logs-proactively)
* [Poslat protokoly hned](#send-logs-now)
* [Místní uložení protokolů](#save-logs-locally)

Vývojový diagram ukazuje, kterou možnost použít k odesílání diagnostických protokolů. Pokud se centrum Azure Stack připojuje k Azure, povolte **shromažďování proaktivních protokolů**. Proaktivní shromažďování protokolů automaticky nahrává protokoly diagnostiky do objektu BLOB úložiště řízeného Microsoftem v Azure, když se aktivuje kritická výstraha. Protokoly můžete shromažďovat také na vyžádání pomocí příkazu **Odeslat protokoly nyní**. U Azure Stackového centra, které běží v odpojeném prostředí, nebo pokud máte problémy s připojením, vyberte možnost **ukládat protokoly místně**.

![Vývojový diagram ukazuje, jak teď odesílat protokoly do Microsoftu.](media/azure-stack-help-and-support/send-logs-now-flowchart.png)

::: moniker-end

## <a name="send-logs-proactively"></a>Proaktivní odesílání protokolů

Proaktivní shromažďování protokolů automaticky shromažďuje a odesílá diagnostické protokoly z centra Azure Stack do Microsoftu před otevřením případu podpory. Tyto protokoly jsou shromažďovány pouze v případě, že je vyvolána výstraha o stavu systému a že k nim přistupovali pouze podpora Microsoftu v kontextu případu podpory.

::: moniker range=">= azs-2008"

Od centra Azure Stack verze 2008 používá aktivní kolekce protokolů Vylepšený algoritmus, který zachycuje protokoly i v případě chybových stavů, které nejsou viditelné pro operátora. Tím se zajistěte, aby byly správné diagnostické informace shromažďovány ve správnou dobu bez nutnosti zásahu operátoru. Podpora Microsoftu může zahájit řešení potíží a vyřešit problémy dřív v některých případech. Počáteční vylepšení algoritmu se soustředí na operace oprav a aktualizace.

Centrum Azure Stack shromažďuje protokoly pro výstrahy a jiné události skrytého selhání, které nejsou pro vás viditelné.

Rozbočovač Azure Stack proaktivně shromažďuje protokoly pro:

- Aktualizace se nezdařila.
- Aktualizace vyžaduje pozornost.

Když událost aktivuje tyto výstrahy, Azure Stack centrum proaktivně odesílá protokoly společnosti Microsoft.

Azure Stack centrum navíc odesílá protokoly do Microsoftu, které aktivovaly jiné události selhání. Tyto události nejsou pro vás viditelné.

Doporučuje se povolit proaktivní kolekce protokolů, protože se optimalizují víc operací a zvyšují se výhody.

::: moniker-end

Proaktivní shromažďování protokolů lze kdykoli zakázat a znovu povolit. Pomocí těchto kroků nastavíte proaktivní shromažďování protokolů.

1. Přihlaste se k portálu pro správu služby Azure Stack Hub.
1. Otevřete okno **help + podpora – přehled**.
1. Pokud se zobrazí nápis, vyberte **Povolit proaktivní shromažďování protokolů**. Nebo můžete vybrat **Nastavení** a nastavit **proaktivní shromažďování protokolů** tak, aby se **povolilo**, a pak vybrat **Uložit**.

> [!NOTE]
> Pokud jsou nastavení umístění protokolu nakonfigurovaná pro místní sdílenou složku, ujistěte se, že zásady správy životního cyklu zabrání v dosažení kvóty úložiště sdílené složky. Centrum Azure Stack nesleduje místní sdílenou složku ani nevynutila žádné zásady uchovávání informací.   

### <a name="how-the-data-is-handled"></a>Způsob zpracování dat

Souhlasíte s pravidelnými automatickými kolekcemi protokolů od Microsoftu, které jsou založené jenom na upozorněních o stavu systému Azure Stack hub. Také potvrzujete a vyjadřujete souhlas s odesláním a uchováváním těchto protokolů v účtu úložiště Azure spravovaném a řízeným Microsoftem.

Data budou použita pouze při odstraňování výstrah stavu systému a nebudou se používat k marketingovým, reklamním ani jiným komerčním účelům bez vašeho souhlasu. Data je možné uchovávat až 90 dní a veškerá data shromážděná společností Microsoft se budou zpracovávat podle našich [standardních postupů ochrany osobních údajů](https://privacy.microsoft.com/).

Veškerá data, která byla dříve shromážděna s vaším souhlasem, nebudou ovlivněna zrušením oprávnění.

Protokoly shromážděné pomocí **proaktivní kolekce protokolů** se odesílají do účtu úložiště Azure spravovaného a řízeného Microsoftem. K těmto protokolům může společnost Microsoft přihlašovat v rámci případu podpory a zlepšit stav centra Azure Stack.

## <a name="send-logs-now"></a>Poslat protokoly hned

> [!TIP]
> Šetřete čas pomocí [interaktivního odesílání protokolů](#send-logs-proactively) , místo aby se teď odesílaly protokoly.

Možnost Odeslat protokoly je nyní možnost, kde ručně shromáždíte a nahrajete diagnostické protokoly z centra Azure Stack, obvykle před otevřením případu podpory.

Existují dva způsoby, jak můžete ručně odeslat protokoly diagnostiky do podpora Microsoftu:
* [Portál pro správu (doporučeno)](#send-logs-now-with-the-administrator-portal)
* [PowerShell](#send-logs-now-with-powershell)

Pokud je centrum Azure Stack připojené k Azure, doporučujeme používat portál pro správu, protože to je nejjednodušší způsob, jak odesílat protokoly přímo Microsoftu. Pokud portál není k dispozici, měli byste odesílat protokoly pomocí PowerShellu.

### <a name="send-logs-now-with-the-administrator-portal"></a>Posílání protokolů teď pomocí portálu pro správu

Pokud chcete odesílat protokoly hned pomocí portálu pro správu:

1. Otevřete okno **help + support > Collection > zasílejte protokoly nyní**. 
1. Zadejte čas spuštění a čas ukončení pro shromažďování protokolů. 
1. Vyberte místní časové pásmo.
1. Vyberte **shromáždit a nahrát**.

Pokud jste odpojeni z Internetu nebo chcete ukládat pouze protokoly místně, použijte k posílání protokolů metodu [Get-AzureStackLog](azure-stack-get-azurestacklog.md) .

### <a name="send-logs-now-with-powershell"></a>Poslat protokoly hned pomocí PowerShellu

Pokud používáte metodu **Odeslat protokoly** a chcete místo portálu pro správu používat PowerShell, můžete `Send-AzureStackDiagnosticLog` k shromažďování a odesílání konkrétních protokolů použít rutinu.

* Parametry **FromDate** a  na více dní lze použít ke shromažďování protokolů pro konkrétní časové období. Nejsou-li tyto parametry zadány, budou ve výchozím nastavení shromažďovány protokoly za poslední čtyři hodiny.

* Pomocí parametru **FilterByNode** můžete filtrovat protokoly podle názvu počítače. Příklad:

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByNode azs-xrp01
  ```

* Pomocí parametru **FilterByLogType** můžete filtrovat protokoly podle typu. Můžete zvolit filtrování podle souboru, sdílení nebo WindowsEvent. Příklad:

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByLogType File
  ```

* Pomocí parametru **FilterByResourceProvider** můžete odesílat diagnostické protokoly pro poskytovatele prostředků s hodnotou Add (RPS). Obecná syntaxe je:
 
  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvider <<value-add RP name>>
  ```

  ::: moniker range=">= azs-2008"

  Odesílání diagnostických protokolů pro SQL RP: 

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvider SQLAdapter
  ```
  Odeslání diagnostických protokolů pro MySQL RP: 

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvider MySQLAdapter
  ```
  
  ::: moniker-end

  Postup odesílání diagnostických protokolů pro IoT Hub: 

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvider IotHub
  ```
 
  Postup odesílání diagnostických protokolů pro Event Hubs:

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvider eventhub
  ```
 
  Postup odesílání diagnostických protokolů pro Azure Stack Edge:

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvide databoxedge
  ```

* K odesílání diagnostických protokolů z rolí VirtualMachines a BareMetal použijte parametr **FilterByRole** :

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByRole VirtualMachines,BareMetal
  ```

* K odesílání diagnostických protokolů z rolí VirtualMachines a BareMetal s filtrováním data pro soubory protokolů za posledních 8 hodin:

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8)
  ```

* Pro odesílání diagnostických protokolů z rolí VirtualMachines a BareMetal s filtrováním data pro soubory protokolů za časové období před 8 hodinami a před 2 hodinami:

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2)
  ```

> [!NOTE]
> Pokud nejste připojení k Internetu, nebo chcete ukládat jenom protokoly místně, použijte k odesílání protokolů metodu [Get-AzureStackLog](azure-stack-get-azurestacklog.md) . 

### <a name="how-the-data-is-handled"></a>Způsob zpracování dat

Tím, že inicializujete shromažďování diagnostických protokolů z centra Azure Stack, potvrzujete a souhlasíte s odesláním těchto protokolů a jejich uchování v účtu úložiště Azure spravovaném a řízeným Microsoftem. Podpora Microsoftu mají přístup k těmto protokolům hned s případem podpory bez nutnosti zapojení zákazníka do shromažďování protokolů.

::: moniker range=">= azs-2005"

## <a name="save-logs-locally"></a>Místní uložení protokolů

Když se Azure Stack rozbočovač odpojí od Azure, můžete protokoly Uložit do místní sdílené složky SMB (Server Message Block). Můžete například spustit odpojené prostředí. Pokud se normálně připojujete, ale dochází k problémům s připojením, můžete ukládat protokoly místně a pomáhat s odstraňováním potíží.

 V okně **Nastavení** zadejte cestu a uživatelské jméno a heslo, které mají oprávnění k zápisu do sdílené složky. Během případu podpory vám podpora Microsoftu poskytne podrobné pokyny, jak přenést tyto místní protokoly. Pokud portál správce není k dispozici, můžete použít [příkaz Get-AzureStackLog](azure-stack-get-azurestacklog.md) k místnímu uložení protokolů.

![Snímek obrazovky s možnostmi shromažďování diagnostických protokolů](media/azure-stack-help-and-support/save-logs-locally.png)

::: moniker-end

## <a name="bandwidth-considerations"></a>Aspekty šířky pásma

Průměrná velikost shromažďování protokolů diagnostiky se liší v závislosti na tom, zda se spouští proaktivně nebo ručně. Průměrná velikost pro **proaktivní shromažďování protokolů** je okolo 2 GB. Velikost kolekce pro funkce **Odeslat protokoly je teď** závislá na počtu hodin, které se shromažďují.

V následující tabulce jsou uvedeny požadavky pro prostředí s omezenými nebo měřenými připojeními k Azure.

| Síťové připojení | Dopad |
|----|---|
| Připojení s nízkou šířkou pásma a vysokou latencí | Nahrávání protokolu bude trvat delší dobu, než se dokončí. |
| Sdílené připojení | Nahrávání může mít vliv i na ostatní aplikace nebo uživatele sdílející síťové připojení. |
| Připojení účtované podle objemu dat | Od poskytovatele internetových služeb může být další poplatek za využití sítě. |

## <a name="view-log-collection"></a>Zobrazit kolekci protokolů

Historie protokolů shromážděných z centra Azure Stack se zobrazí na stránce **shromažďování protokolů** v **nápovědě a podpoře** s následujícími daty a časy:

- **Čas shromažďování**: při zahájení operace shromažďování protokolů.
- **Stav**: buď probíhá, nebo dokončeno.
- **Zahajte zápisy**: začátek časového období, které chcete shromáždit.
- **Konec protokolů**: konec časového období.
- **Typ**: Pokud se jedná o manuální nebo proaktivní shromažďování protokolů.

![Kolekce protokolů v nápovědě a podpoře](media/azure-stack-help-and-support/azure-stack-log-collection.png)

## <a name="see-also"></a>Viz také

[Azure Stack zpracování dat protokolů a zákazníků centra](./azure-stack-data-collection.md)
