---
title: Shromažďování protokolů diagnostiky v centru Azure Stack
description: Přečtěte si o shromažďování protokolů diagnostiky v centru pro nápovědu a podporu centra Azure Stack.
author: myoungerman
ms.topic: article
ms.date: 08/24/2020
ms.author: v-myoung
ms.reviewer: shisab
ms.lastreviewed: 08/24/2020
ms.openlocfilehash: 88f2b267f493c05fdcd8a5419718d08d5b6efe37
ms.sourcegitcommit: 868887e4b13b1572f15004a9db2c334e60d8add2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91778256"
---
# <a name="diagnostic-log-collection-in-azure-stack-hub"></a>Shromažďování protokolů diagnostiky v centru Azure Stack

Centrum Azure Stack je kolekce komponent Windows i místních služeb Azure vzájemně komunikujících. Všechny tyto komponenty a služby generují svou vlastní sadu protokolů. Vzhledem k tomu, že podpora Microsoftu pomocí těchto protokolů efektivně identifikuje a opravuje vaše problémy, nabízíme shromažďování protokolů diagnostiky. Shromažďování diagnostických protokolů vám pomůže rychle shromáždit a sdílet diagnostické protokoly s podpora Microsoftu v jednoduchém uživatelském rozhraní, které nevyžaduje PowerShell. Protokoly se shromažďují i v případě, že jsou mimo provoz jiné služby infrastruktury.  

Doporučujeme použít tento postup pro shromažďování protokolů a [použít jenom privilegovaný koncový bod (PEP)](azure-stack-get-azurestacklog.md) , pokud není k dispozici portál pro správu nebo okno pro nápovědu a podporu. 

>[!NOTE]
>Aby bylo možné používat shromažďování protokolů diagnostiky, musí být rozbočovač Azure Stack zaregistrován. Pokud Azure Stack centrum není zaregistrované, nasdílejte protokoly pomocí [privilegovaného koncového bodu (PEP)](azure-stack-get-azurestacklog.md) . 

## <a name="collection-options-and-data-handling"></a>Možnosti kolekce a zpracování dat

::: moniker range=">= azs-2005"

V závislosti na připojení k Azure má Azure Stack centrum vhodné způsoby, jak shromažďovat, ukládat a odesílat diagnostické protokoly do šablon stylů CSS. Pokud se centrum Azure Stack může připojit k Azure, doporučuje se povolit **proaktivní shromažďování protokolů**, které při vyvolání kritické výstrahy automaticky nahraje diagnostické protokoly do objektu BLOB úložiště řízeného Microsoftem v Azure. Můžete taky shromažďovat protokoly na vyžádání pomocí příkazu **Odeslat protokoly**, nebo můžete ukládat protokoly místně, pokud je Azure Stack rozbočovač odpojený od Azure. 

Následující části vysvětlují jednotlivé možnosti a způsob, jakým jsou data v jednotlivých případech zpracovávána. 

::: moniker-end

Funkce shromažďování protokolů diagnostiky nabízí dva způsoby, jak odesílat protokoly:
* Proaktivní odesílání protokolů
* Poslat protokoly hned

Protokoly také můžete ukládat lokálně.

To ukazuje, kterou možnost použít k odesílání diagnostických protokolů v každém případě. 

![Vývojový diagram ukazuje, jak teď odesílat protokoly do Microsoftu.](media/azure-stack-help-and-support/send-logs-now-flowchart.png)

Následující části vysvětlují jednotlivé možnosti a způsob, jakým jsou data v jednotlivých případech zpracovávána. 

### <a name="send-logs-proactively"></a>Proaktivní odesílání protokolů

Proaktivní shromažďování protokolů automaticky shromažďuje a odesílá diagnostické protokoly z centra Azure Stack do Microsoftu před otevřením případu podpory. Tyto protokoly jsou shromažďovány pouze v případě, že je vyvolána [Výstraha o stavu systému](#proactive-diagnostic-log-collection-alerts) a že k nim přistupovali pouze podpora Microsoftu v kontextu případu podpory.

Proaktivní shromažďování protokolů lze kdykoli zakázat a znovu povolit. Pomocí těchto kroků nastavíte proaktivní shromažďování protokolů.

1. Přihlaste se k portálu pro správu služby Azure Stack Hub.
1. Otevřete okno **help + podpora – přehled**.
1. Pokud se zobrazí nápis, vyberte **Povolit proaktivní shromažďování protokolů**. Nebo můžete vybrat **Nastavení** a nastavit **proaktivní shromažďování protokolů** tak, aby se **povolilo**, a pak vybrat **Uložit**.

>[!NOTE]
>Pokud jsou nastavení umístění protokolu nakonfigurovaná pro místní sdílenou složku, ujistěte se, že zásady správy životního cyklu zabrání v dosažení kvóty úložiště sdílené složky. Centrum Azure Stack nesleduje místní sdílenou složku ani nevynutila žádné zásady uchovávání informací.   

### <a name="send-logs-now"></a>Poslat protokoly hned

> [!TIP]
> Ušetříte čas pomocí [proaktivní kolekce protokolů](#send-logs-proactively) místo odesílání protokolů.

Možnost Odeslat protokoly je teď možností, kde ručně shromáždíte a nahrajete diagnostické protokoly z centra Azure Stack, obvykle před otevřením případu podpory.

Protokoly diagnostiky můžete podpora Microsoftu odeslat ručně pomocí portálu pro správu nebo PowerShellu. Pokud je centrum Azure Stack připojené k Azure, doporučujeme používat portál pro správu, protože to je nejjednodušší způsob, jak odesílat protokoly přímo Microsoftu. Pokud portál není k dispozici, měli byste místo toho odesílat protokoly pomocí PowerShellu.

Odeslání protokolů teď:

1. Otevřete okno **help + support > Collection > zasílejte protokoly nyní**. 
1. Zadejte čas spuštění a čas ukončení pro shromažďování protokolů. 
1. Vyberte místní časové pásmo.
1. Vyberte **shromáždit a nahrát**.

Pokud jste odpojeni z Internetu nebo chcete ukládat pouze protokoly místně, použijte k odeslání protokolů metodu [Get-AzureStackLog](azure-stack-get-azurestacklog.md) . 

::: moniker range=">= azs-2005"

### <a name="save-logs-locally"></a>Místní uložení protokolů

Když se Azure Stack rozbočovač odpojí od Azure, můžete protokoly Uložit do místní sdílené složky SMB (Server Message Block). V okně **Nastavení** zadejte cestu a uživatelské jméno a heslo, které mají oprávnění k zápisu do sdílené složky. Během případu podpory vám podpora Microsoftu poskytne podrobné pokyny, jak přenést tyto místní protokoly. Pokud portál správce není k dispozici, můžete použít [příkaz Get-AzureStackLog](azure-stack-get-azurestacklog.md) k místnímu uložení protokolů.

![Snímek obrazovky s možnostmi shromažďování diagnostických protokolů](media/azure-stack-help-and-support/save-logs-locally.png)

::: moniker-end

## <a name="bandwidth-considerations"></a>Požadavky na šířku pásma

Průměrná velikost shromažďování protokolů diagnostiky se liší v závislosti na tom, zda se spouští proaktivně nebo ručně. Průměrná velikost pro **proaktivní shromažďování protokolů** je okolo 2 GB. Velikost kolekce pro funkce **Odeslat protokoly je teď** závislá na počtu hodin, které se shromažďují.

V následující tabulce jsou uvedeny požadavky pro prostředí s omezenými nebo měřenými připojeními k Azure.

| Síťové připojení | Dopad |
|----|---|
| Připojení s nízkou šířkou pásma a vysokou latencí | Nahrávání protokolu bude trvat delší dobu, než se dokončí. |
| Sdílené připojení | Nahrávání může mít vliv i na ostatní aplikace nebo uživatele sdílející síťové připojení. |
| Připojení účtované podle objemu dat | Od poskytovatele internetových služeb může docházet k dodatečnému využití sítě. |

## <a name="parameter-considerations"></a>Požadavky na parametry 

* Parametry **FromDate** a **ToDate** na více dní lze použít ke shromažďování protokolů pro konkrétní časové období. Nejsou-li tyto parametry zadány, budou ve výchozím nastavení shromažďovány protokoly za poslední čtyři hodiny.

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

Šetřete čas s zákaznickou podporou tím, že aktivně shromažďují protokoly diagnostiky, když se výstraha aktivuje na Azure Stackovém centru.

Pokud je nutné prozkoumat stav systému, protokoly je možné odeslat automaticky pro analýzu před otevřením případu podpory s podpora Microsoftu.

>[!NOTE]
>Pokud jste odpojeni z Internetu nebo chcete ukládat pouze místní protokoly, použijte k odeslání protokolů metodu [Get-AzureStackLog](azure-stack-get-azurestacklog.md) . 

## <a name="view-log-collection"></a>Zobrazit kolekci protokolů

Historie protokolů shromážděných z centra Azure Stack se zobrazí na stránce **shromažďování protokolů** v **nápovědě a podpoře**s následujícími daty a časy:

- **Čas shromažďování**: při zahájení operace shromažďování protokolů.
- **Stav**: buď probíhá, nebo dokončeno.
- **Zahajte zápisy**: začátek časového období, které chcete shromáždit.
- **Konec protokolů**: konec časového období.
- **Typ**: Pokud se jedná o manuální nebo proaktivní shromažďování protokolů.

![Kolekce protokolů v nápovědě a podpoře](media/azure-stack-help-and-support/azure-stack-log-collection.png)

## <a name="proactive-diagnostic-log-collection-alerts"></a>Výstrahy shromažďování proaktivní diagnostického protokolu

Je-li povoleno, služba proaktivní shromažďování protokolů odesílá protokoly pouze v případě, že je vyvolána jedna z následujících událostí.

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

## <a name="how-the-data-is-handled"></a>Způsob zpracování dat

Pokud povolíte možnost **Odeslat protokoly proaktivně**, vyjadřujete tím souhlas s pravidelnými automatickými kolekcemi protokolů, které Microsoft zakládá jenom na výstrahách o stavu systému Azure Stack hub. Také potvrzujete a vyjadřujete souhlas s odesláním a uchováváním těchto protokolů v účtu úložiště Azure spravovaném a řízeným Microsoftem.

Data budou použita pouze při odstraňování výstrah stavu systému a nebudou se používat k marketingovým, reklamním ani jiným komerčním účelům bez vašeho souhlasu. Data je možné uchovávat až 90 dní a veškerá data shromážděná společností Microsoft se budou zpracovávat podle našich [standardních postupů ochrany osobních údajů](https://privacy.microsoft.com/).

Veškerá data, která byla dříve shromážděna s vaším souhlasem, nebudou ovlivněna zrušením oprávnění.

## <a name="see-also"></a>Viz také

[Azure Stack zpracování dat protokolů a zákazníků centra](./azure-stack-data-collection.md)
