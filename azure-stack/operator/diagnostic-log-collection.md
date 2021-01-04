---
title: Shromažďování protokolů diagnostiky
description: Přečtěte si o shromažďování protokolů diagnostiky.
author: myoungerman
ms.topic: article
ms.date: 10/30/2020
ms.author: v-myoung
ms.reviewer: shisab
ms.lastreviewed: 12/08/2020
ms.openlocfilehash: eaa265189769bf1f192ef6fce260a221935736cb
ms.sourcegitcommit: 076ece88c3177db321f0ae32cba1d05179ffc393
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2020
ms.locfileid: "97794188"
---
# <a name="diagnostic-log-collection"></a>Shromažďování protokolů diagnostiky

Centrum Azure Stack je kolekce komponent Windows i místních služeb Azure, které vzájemně spolupracuje. Všechny tyto komponenty a služby generují svou vlastní sadu protokolů. Vzhledem k tomu, že podpora Microsoftu používá tyto protokoly k identifikaci a řešení problémů, nabízíme shromažďování protokolů diagnostiky. Shromažďování diagnostických protokolů vám pomůže rychle shromáždit a sdílet diagnostické protokoly s podpora Microsoftu.

> [!IMPORTANT]
> Aby bylo možné použít shromažďování protokolů diagnostiky, je nutné zaregistrovat centrum Azure Stack. Pokud jste nezaregistrovali Azure Stack centrum, sdílejte protokoly pomocí [privilegovaného koncového bodu (PEP)](azure-stack-get-azurestacklog.md) . 

::: moniker range=">= azs-2005"

Centrum Azure Stack má několik způsobů, jak shromažďovat, ukládat a odesílat diagnostické protokoly do podpora Microsoftu. V závislosti na připojení k Azure jsou k disviset tyto možnosti shromažďování a odesílání protokolů:
* [Proaktivní odesílání protokolů (doporučeno)](#send-logs-proactively)
* [Poslat protokoly hned](#send-logs-now)
* [Místní uložení protokolů](#save-logs-locally)

Vývojový diagram níže ukazuje, kterou možnost použít k odesílání diagnostických protokolů v každém případě. Pokud se centrum Azure Stack může připojit k Azure, doporučujeme povolit **proaktivní shromažďování protokolů**, které při vyvolání kritické výstrahy automaticky nahraje diagnostické protokoly do objektu BLOB úložiště se spravovaným Microsoftem v Azure. Můžete také shromažďovat protokoly na vyžádání pomocí příkazu **Odeslat protokoly nyní**. Pokud je rozbočovač Azure Stack od Azure odpojený, můžete **ukládat protokoly místně**. 

![Vývojový diagram ukazuje, jak teď odesílat protokoly do Microsoftu.](media/azure-stack-help-and-support/send-logs-now-flowchart.png)

::: moniker-end

## <a name="send-logs-proactively"></a>Proaktivní odesílání protokolů

Proaktivní shromažďování protokolů automaticky shromažďuje a odesílá diagnostické protokoly z centra Azure Stack do Microsoftu před otevřením případu podpory. Tyto protokoly jsou shromažďovány pouze v případě, že je vyvolána [Výstraha o stavu systému](#proactive-diagnostic-log-collection-alerts) a že k nim přistupovali pouze podpora Microsoftu v kontextu případu podpory.

::: moniker range=">= azs-2008"

Od centra Azure Stack verze 2008 používá aktivní kolekce protokolů Vylepšený algoritmus, který zachycuje protokoly i v případě chybových stavů, které nejsou viditelné pro operátora. Tím se zajistěte, aby byly správné diagnostické informace shromažďovány ve správnou dobu bez nutnosti zásahu operátoru. Podpora Microsoftu může zahájit řešení potíží a vyřešit problémy dřív v některých případech. Počáteční vylepšení algoritmu se soustředí na operace oprav a aktualizace. Doporučuje se povolit proaktivní kolekce protokolů, protože se optimalizují víc operací a zvyšují se výhody.

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

### <a name="proactive-diagnostic-log-collection-alerts"></a>Výstrahy shromažďování proaktivní diagnostického protokolu

Pokud je povoleno, v případě, že je aktivována jedna z následujících událostí, odešle protokol proaktivní kolekce protokolů.

Například **Chyba aktualizace** je výstraha, která spouští proaktivní shromažďování protokolů diagnostiky. Pokud je tato možnost povolena, diagnostické protokoly budou aktivně zachyceny během chyby aktualizace, což může pomoct podpora Microsoftu řešení problému. Diagnostické protokoly jsou shromažďovány pouze v případě, že se vyvolá výstraha pro **aktualizaci** .

| Název výstrahy | FaultIdType |
|---|---|
|Nejde se připojit ke vzdálené službě | UsageBridge.NetworkError|
|Aktualizace se nezdařila | Urp.UpdateFailure |
|Infrastruktura nebo závislosti poskytovatele prostředků úložiště nejsou k dispozici. |    StorageResourceProviderDependencyUnavailable |
|Uzel není připojený k řadiči.| ServerHostNotConnectedToController |  
|Selhání publikování trasy | SlbMuxRoutePublicationFailure |
|Interní úložiště dat poskytovatele prostředků úložiště není dostupné. |    StorageResourceProvider. DataStoreConnectionFail |
|Selhání úložného zařízení | Microsoft. Health. typ FaultType. VirtualDisks. odpojilo se |
|Kontroler stavu nemůže získat přístup k účtu úložiště. | Microsoft. Health. typ FaultType. StorageError |
|Připojení k fyzickému disku bylo ztraceno. | Microsoft. Health. typ FaultType. fyzický disk. LostCommunication |
|Služba BLOB Service není spuštěná na uzlu. | StorageService. blob. Service. is. not. Running. on. Node-Critical |
|Role infrastruktury není v pořádku. | Microsoft. Health. typ FaultType. GenericExceptionFault |
|Chyby služby Table service | StorageService. Table. Service. Errors – kritický |
|Sdílená složka je větší než 80% využití. | Microsoft. Health. typ FaultType. sdílené složky. Capacity. Warning. |
|Uzel jednotky škálování je offline | FRP. Prezenční signál. PhysicalNode |
|Instance role infrastruktury není dostupná. | FRP. Prezenční signál. InfraVM |
|Instance role infrastruktury není dostupná.  | FRP. Prezenční signál. NonHaVm |
|Role infrastruktury, Správa adresářů, ohlásila chyby synchronizace času. | DirectoryServiceTimeSynchronizationError |
|Blížící se vypršení platnosti externího certifikátu | CertificateExpiration. ExternalCert. Warning |
|Blížící se vypršení platnosti externího certifikátu | CertificateExpiration. ExternalCert. Critical |
|Pro konkrétní třídu a velikost nejde zřídit virtuální počítače kvůli nedostatečné kapacitě paměti | AzureStack. ComputeController. VmCreationFailure. LowMemory |
|Nedostupný uzel pro umístění virtuálního počítače | AzureStack. ComputeController. HostUnresponsive |
|Zálohování nebylo úspěšné.  | AzureStack. BackupController. BackupFailedGeneralFault |
|Naplánované zálohování bylo přeskočeno z důvodu konfliktu s neúspěšnými operacemi.    | AzureStack. BackupController. BackupSkippedWithFailedOperationFault |

## <a name="send-logs-now"></a>Poslat protokoly hned

> [!TIP]
> Šetřete čas pomocí [interaktivního odesílání protokolů](#send-logs-proactively) , místo aby se teď odesílaly protokoly.

Možnost Odeslat protokoly je teď možností, kde ručně shromáždíte a nahrajete diagnostické protokoly z centra Azure Stack, obvykle před otevřením případu podpory.

Existují dva způsoby, jak můžete ručně odeslat protokoly diagnostiky do podpora Microsoftu:
* [Portál pro správu (doporučeno)](#send-logs-now-with-the-administrator-portal)
* [PowerShell](#send-logs-now-with-powershell)

Pokud je centrum Azure Stack připojené k Azure, doporučujeme používat portál pro správu, protože to je nejjednodušší způsob, jak odesílat protokoly přímo Microsoftu. Pokud portál není k dispozici, měli byste místo toho odesílat protokoly pomocí PowerShellu.

### <a name="send-logs-now-with-the-administrator-portal"></a>Posílání protokolů teď pomocí portálu pro správu

Pokud chcete odesílat protokoly hned pomocí portálu pro správu:

1. Otevřete okno **help + support > Collection > zasílejte protokoly nyní**. 
1. Zadejte čas spuštění a čas ukončení pro shromažďování protokolů. 
1. Vyberte místní časové pásmo.
1. Vyberte **shromáždit a nahrát**.

Pokud jste odpojeni z Internetu nebo chcete ukládat pouze protokoly místně, použijte k odeslání protokolů metodu [Get-AzureStackLog](azure-stack-get-azurestacklog.md) .

### <a name="send-logs-now-with-powershell"></a>Poslat protokoly hned pomocí PowerShellu

Pokud používáte metodu **Odeslat protokoly** a chcete místo portálu pro správu používat PowerShell, můžete `Send-AzureStackDiagnosticLog` k shromažďování a odesílání konkrétních protokolů použít rutinu.

* Parametry **FromDate** a  na více dní lze použít ke shromažďování protokolů pro konkrétní časové období. Nejsou-li tyto parametry zadány, budou ve výchozím nastavení shromažďovány protokoly za poslední čtyři hodiny.

* Pomocí parametru **FilterByNode** můžete filtrovat protokoly podle názvu počítače. Například:

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByNode azs-xrp01
  ```

* Pomocí parametru **FilterByLogType** můžete filtrovat protokoly podle typu. Můžete zvolit filtrování podle souboru, sdílení nebo WindowsEvent. Například:

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByLogType File
  ```

* Pomocí parametru **FilterByResourceProvider** můžete odesílat diagnostické protokoly pro poskytovatele prostředků s hodnotou Add (RPS). Obecná syntaxe je:
 
  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvider <<value-add RP name>>
  ```
  Odesílání diagnostických protokolů pro SQL RP: 

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvider SQLAdapter
  ```
  Odeslání diagnostických protokolů pro MySQL RP: 

  ```powershell
  Send-AzureStackDiagnosticLog -FilterByResourceProvider MySQLAdapter
  ```

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
> Pokud jste odpojeni z Internetu nebo chcete ukládat pouze místní protokoly, použijte k odeslání protokolů metodu [Get-AzureStackLog](azure-stack-get-azurestacklog.md) . 

### <a name="how-the-data-is-handled"></a>Způsob zpracování dat

Tím, že inicializujete shromažďování diagnostických protokolů z centra Azure Stack, potvrzujete a souhlasíte s odesláním těchto protokolů a jejich uchování v účtu úložiště Azure spravovaném a řízeným Microsoftem. Podpora Microsoftu mají přístup k těmto protokolům hned s případem podpory bez nutnosti zapojení zákazníka do shromažďování protokolů.

::: moniker range=">= azs-2005"

## <a name="save-logs-locally"></a>Místní uložení protokolů

Když se Azure Stack rozbočovač odpojí od Azure, můžete protokoly Uložit do místní sdílené složky SMB (Server Message Block). V okně **Nastavení** zadejte cestu a uživatelské jméno a heslo, které mají oprávnění k zápisu do sdílené složky. Během případu podpory vám podpora Microsoftu poskytne podrobné pokyny, jak přenést tyto místní protokoly. Pokud portál správce není k dispozici, můžete použít [příkaz Get-AzureStackLog](azure-stack-get-azurestacklog.md) k místnímu uložení protokolů.

![Snímek obrazovky s možnostmi shromažďování diagnostických protokolů](media/azure-stack-help-and-support/save-logs-locally.png)

::: moniker-end

## <a name="bandwidth-considerations"></a>Aspekty šířky pásma

Průměrná velikost shromažďování protokolů diagnostiky se liší v závislosti na tom, zda se spouští proaktivně nebo ručně. Průměrná velikost pro **proaktivní shromažďování protokolů** je okolo 2 GB. Velikost kolekce pro funkce **Odeslat protokoly je teď** závislá na počtu hodin, které se shromažďují.

V následující tabulce jsou uvedeny požadavky pro prostředí s omezenými nebo měřenými připojeními k Azure.

| Síťové připojení | Dopad |
|----|---|
| Připojení s nízkou šířkou pásma a vysokou latencí | Nahrávání protokolu bude trvat delší dobu, než se dokončí. |
| Sdílené připojení | Nahrávání může mít vliv i na ostatní aplikace nebo uživatele sdílející síťové připojení. |
| Připojení účtované podle objemu dat | Od poskytovatele internetových služeb může docházet k dodatečnému využití sítě. |

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
