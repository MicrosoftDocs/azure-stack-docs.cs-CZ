---
title: Shromažďovat diagnostické protokoly Azure Stack nyní | Microsoft Docs
description: Postup konfigurace shromažďování diagnostických protokolů na vyžádání v Azure Stack nápovědě a podpoře
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: a20bea32-3705-45e8-9168-f198cfac51af
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/31/2019
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 07/31/2019
ms.openlocfilehash: 988c24f004222d80b866024c2c392473cdf73bab
ms.sourcegitcommit: ddb625bb01de11bfb75d9f7a1cc61d5814b3bc31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68712939"
---
# <a name="collect-azure-stack-diagnostic-logs-now-on-demand"></a>Shromažďovat protokoly diagnostiky Azure Stack hned (na vyžádání)

*Platí pro: Azure Stack integrovaných systémů*

V rámci řešení potíží můžou služby Microsoft Customer Support Services (CSS) potřebovat analyzovat diagnostické protokoly. Od verze 1907 mohou operátoři Azure Stack do kontejneru objektů BLOB v Azure nahrávat diagnostické protokoly na vyžádání pomocí **pomoci a podpory**. Alternativně, pokud je portál nedostupný, můžou operátory shromažďovat protokoly pomocí Get-AzureStackLog prostřednictvím privilegovaného koncového bodu (PEP). Toto téma se věnuje oběma způsobům shromažďování diagnostických protokolů na vyžádání.

## <a name="using-help-and-support"></a>Používání pomoci a podpory

V případě řešení problému může CSS požádat o operátora Azure Stack ke shromáždění diagnostických protokolů na vyžádání pro konkrétní časové období z předchozího týdne. V takovém případě vám CSS poskytne operátor s adresou URL SAS pro nahrání kolekce. Pomocí následujících kroků proveďte konfiguraci shromažďování protokolů na vyžádání pomocí adresy URL SAS z šablony stylů CSS:

1. Otevřete okno **pomoc a podpora – přehled** a hned klikněte na **shromáždit protokoly**. 
1. Vyberte okno posunuté z 1-4 hodin za posledních sedm dní. 
1. Vyberte místní časové pásmo.
1. Zadejte adresu URL SAS, kterou poskytuje CSS.

   ![Snímek obrazovky shromažďování protokolů na vyžádání](media/azure-stack-automatic-log-collection/collect-logs-now.png)

>[!NOTE]
>Pokud je povolená funkce automatického shromažďování protokolů diagnostiky, zobrazí se v **nápovědě a podpoře** , když probíhá shromažďování protokolů. Pokud kliknete na **shromažďovat protokoly** a shromažďovat protokoly z určitou skupinu doby, kdy probíhá automatické shromažďování protokolů, začne shromažďování na vyžádání po dokončení automatického shromažďování protokolů. 

## <a name="using-pep"></a>Použití PEP

<!--how do you look up the PEP IP address. You look up the azurestackstampinfo.json--->

Nástroje pro diagnostiku Azure Stack umožňují snadno a efektivně vytvářet kolekce protokolů. Následující diagram znázorňuje fungování diagnostických nástrojů:

![Diagnostické nástroje Azure Stack](media/azure-stack-diagnostics/get-azslogs.png)

### <a name="trace-collector"></a>Kolektor trasování

Kolektor trasování je ve výchozím nastavení povolená a na pozadí se bude shromažďovat všechny protokoly ETW (Event Tracing for Windows) ze služby Azure Stack Component Services. Protokoly ETW se ukládají do běžné místní sdílené složky s omezením na stáří po pěti dnech. Po dosažení tohoto limitu se nejstarší soubory odstraní, jakmile se vytvoří nové. Výchozí maximální velikost povolená pro každý soubor je 200 MB. K kontrole velikosti dochází každé 2 minuty, pokud je aktuální soubor > = 200 MB, je uložen a vygeneruje se nový soubor. Celková velikost souborů vygenerovaná pro každou relaci události je také omezena na 8 GB.

### <a name="get-azurestacklog"></a>Get-AzureStackLog

Rutina PowerShellu Get-AzureStackLog se dá použít ke shromažďování protokolů ze všech komponent v prostředí Azure Stack. Ukládá je do souborů zip v uživatelsky definovaném umístění. Pokud tým technické podpory Azure Stack potřebuje k tomu, aby vaše protokoly pomohl problém vyřešit, může požádat o spuštění rutiny Get-AzureStackLog.

> [!CAUTION]
> Tyto soubory protokolu mohou obsahovat identifikovatelné osobní údaje (PII). Před tím, než budete veřejně publikovat všechny soubory protokolu, je nutné vzít v úvahu.

Níže jsou uvedeny příklady typů protokolů, které jsou shromažďovány:

* **Protokoly nasazení Azure Stack**
* **Protokoly událostí systému Windows**
* **Protokoly Panther**
* **Protokoly clusteru**
* **Diagnostické protokoly úložiště**
* **Protokoly ETW**

Tyto soubory se shromažďují a ukládají do sdílené složky pomocí Kolektor trasování. Příkaz Get-AzureStackLog je pak možné použít k jejich shromáždění v případě potřeby.

#### <a name="to-run-get-azurestacklog-on-azure-stack-integrated-systems"></a>Spuštění rutiny Get-AzureStackLog v integrovaných systémech Azure Stack

Ke spuštění rutiny Get-AzureStackLog v integrovaném systému je potřeba mít přístup k privilegovanému koncovému bodu (PEP). Tady je ukázkový skript, který můžete spustit pomocí PEP a shromažďovat protokoly v integrovaném systému:

```powershell
$ipAddress = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.

$password = ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object -TypeName System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $password)

$shareCred = Get-Credential

$session = New-PSSession -ComputerName $ipAddress -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2) # Provide the time that includes the period for your issue

Invoke-Command -Session $session { Get-AzureStackLog -OutputSharePath "<EXTERNAL SHARE ADDRESS>" -OutputShareCredential $using:shareCred  -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate}

if ($session) {
    Remove-PSSession -Session $session
}
```

#### <a name="run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system"></a>Spuštění rutiny Get-AzureStackLog v systému Azure Stack Development Kit (ASDK)

Pomocí těchto kroků můžete spustit `Get-AzureStackLog` na hostitelském počítači s ASDK.

1. Přihlaste se jako **AzureStack\CloudAdmin** na hostitelském počítači ASDK.
2. Otevřete nové okno PowerShellu jako správce.
3. Spusťte rutinu PowerShellu **Get-AzureStackLog** .

#### <a name="examples"></a>Příklady

* Shromažďovat všechny protokoly pro všechny role:

  ```powershell
  Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred
  ```

* Shromažďovat protokoly z rolí VirtualMachines a BareMetal:

  ```powershell
  Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal
  ```

* Shromažďování protokolů z rolí VirtualMachines a BareMetal s filtrováním data pro soubory protokolu za posledních 8 hodin:

  ```powershell
  Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8)
  ```

* Shromažďování protokolů z rolí VirtualMachines a BareMetal s filtrováním data pro soubory protokolu za časové období před 8 hodinami a před 2 hodinami:

  ```powershell
  Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByRole VirtualMachines,BareMetal -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2)
  ```

* Shromáždí protokoly a uloží je v zadaném Azure Storage kontejneru objektů BLOB. Obecná syntaxe pro tuto operaci je následující:

  ```powershell
  Get-AzureStackLog -OutputSasUri "<Blob service SAS Uri>"
  ```

  Příklad:

  ```powershell
  Get-AzureStackLog -OutputSasUri "https://<storageAccountName>.blob.core.windows.net/<ContainerName><SAS token>"
  ```

  > [!NOTE]
  > Tento postup je vhodný pro nahrávání protokolů. I když nemáte přístup ke sdílené složce SMB nebo přístup k Internetu, můžete v Azure Stack vytvořit účet Blob Storage pro přenos protokolů a potom pomocí klienta tyto protokoly načíst.  

  K vygenerování tokenu SAS pro účet úložiště se vyžadují následující oprávnění:

  * Přístup ke službě Blob Storage
  * Přístup k typu prostředku kontejneru

  K vygenerování hodnoty identifikátoru URI SAS, která se `-OutputSasUri` má použít pro parametr, proveďte následující kroky:

  1. Podle kroků [v tomto článku](/azure/storage/common/storage-quickstart-create-account)vytvořte účet úložiště.
  2. Otevřete instanci Průzkumník služby Azure Storage.
  3. Připojte se k účtu úložiště vytvořenému v kroku 1.
  4. Přejděte na **kontejnery objektů BLOB** ve **službě úložiště**.
  5. Vyberte **vytvořit nový kontejner**.
  6. Klikněte pravým tlačítkem na nový kontejner a pak klikněte na **získat sdílený přístupový podpis**.
  7. V závislosti na vašich požadavcích Vyberte platný **čas spuštění** a **čas ukončení**.
  8. Pro požadovaná oprávnění vyberte **čtení**, **zápis**a **seznam**.
  9. Vyberte **Vytvořit**.
  10. Zobrazí se sdílený přístupový podpis. Zkopírujte část adresy URL a poskytněte ji `-OutputSasUri` parametru.

### <a name="parameter-considerations-for-both-asdk-and-integrated-systems"></a>Hlediska parametrů pro ASDK i integrované systémy

* Parametry **OutputSharePath** a **OutputShareCredential** se používají k ukládání protokolů v umístění zadaném uživatelem.

* Parametry **FromDate** a na více dní lze použít ke shromažďování protokolů pro konkrétní časové období. Nejsou-li tyto parametry zadány, budou ve výchozím nastavení shromažďovány protokoly za poslední čtyři hodiny.

* Pomocí parametru **FilterByNode** můžete filtrovat protokoly podle názvu počítače. Příklad:

    ```powershell
    Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByNode azs-xrp01
    ```

* Pomocí parametru **FilterByLogType** můžete filtrovat protokoly podle typu. Můžete zvolit filtrování podle souboru, sdílení nebo WindowsEvent. Příklad:

    ```powershell
    Get-AzureStackLog -OutputSharePath "<path>" -OutputShareCredential $cred -FilterByLogType File
    ```

* Pomocí parametru **TimeOutInMinutes** můžete nastavit časový limit pro shromažďování protokolů. Ve výchozím nastavení je nastavená na 150 (2,5 hodin).
* Shromažďování protokolu souborů výpisu paměti je ve výchozím nastavení zakázáno. Pokud ho chcete povolit, použijte parametr přepínače **IncludeDumpFile** .
* V současné době můžete pomocí parametru **FilterByRole** filtrovat shromažďování protokolů podle následujících rolí:

  |   |   |   |    |     |
  | - | - | - | -  |  -  |
  |ACS                   |CA                             |HRP                            |OboService                |VirtualMachines|
  |ACSBlob               |CacheService                   |IBC                            |OEM                       |VYTVOŘEN            |
  |ACSDownloadService    |Compute                        |InfraServiceController         |OnboardRP                 |WASPUBLIC|
  |ACSFabric             |CPI                            |KeyVaultAdminResourceProvider  |PROTOKOLU                       |         |
  |ACSFrontEnd           |CRP                            |KeyVaultControlPlane           |QueryServiceCoordinator   |         | 
  |ACSMetrics            |DeploymentMachine              |KeyVaultDataPlane              |QueryServiceWorker        |         |
  |ACSMigrationService   |DiskRP                         |KeyVaultInternalControlPlane   |SeedRing                  |         |
  |ACSMonitoringService  |Doména                         |KeyVaultInternalDataPlane      |SeedRingServices          |         |
  |ACSSettingsService    |OSN                            |KeyVaultNamingService          |SLB                       |         |
  |ACSTableMaster        |EventAdminRP                   |MDM                            |SQL                       |         |
  |ACSTableServer        |EventRP                        |MetricsAdminRP                 |OBSAŽEN                       |         |
  |ACSWac                |ExternalDNS                    |MetricsRP                      |Storage                   |         |
  |ADFS                  |FabricRing                     |MetricsServer                  |StorageController         |         |
  |ApplicationController |FabricRingServices             |MetricsStoreService            |URP                       |         |
  |ASAppGateway          |FirstTierAggregationService    |MonAdminRP                     |SupportBridgeController   |         |
  |AzureBridge           |FRP                            |MonRP                          |SupportRing               |         |
  |AzureMonitor          |brána                        |NC                             |SupportRingServices       |         |
  |BareMetal             |HealthMonitoring               |NonPrivilegedAppGateway        |SupportBridgeRP           |         |
  |BRP                   |HintingServiceV2               |NRP                            |UsageBridge               |         |
  |   |   |   |    |     | 

### <a name="additional-considerations"></a>Další aspekty

* Spuštění příkazu může nějakou dobu trvat, a to na základě rolí, které protokoly shromažďují. Přispívající faktory také zahrnují dobu trvání určenou pro shromažďování protokolů a počty uzlů v prostředí Azure Stack.
* Když je shromažďování protokolů spuštěno, ověřte novou složku vytvořenou v parametru **OutputSharePath** zadaného v příkazu.
* Každá role má své protokoly uvnitř jednotlivých souborů zip. V závislosti na velikosti shromážděných protokolů může být role rozdělená do více souborů zip. Pokud v takové roli chcete, aby všechny soubory protokolu byly v jednom adresáři, použijte nástroj, který se může volně rozkomprimovat. Vyberte všechny soubory zip pro roli a vyberte **extrahovat sem**. Tím se v jedné sloučené složce rozbalení všechny soubory protokolu pro tuto roli.
* Ve složce, která obsahuje soubory protokolu zip, se vytvoří také soubor s názvem **Get-AzureStackLog_Output. log** . Tento soubor je protokolem výstupu příkazu, který se dá použít k řešení problémů během shromažďování protokolů. V některých případech soubor protokolu `PS>TerminatingError` obsahuje položky, které je možné ignorovat, pokud po spuštění shromažďování protokolů nechybějí žádné soubory protokolu.
* Pro prošetření konkrétního selhání mohou být protokoly potřeba z více než jedné součásti.

  * V roli **VirtualMachines** jsou shromažďovány systémové protokoly a protokoly událostí pro všechny virtuální počítače infrastruktury.
  * V roli **BareMetal** jsou shromažďovány systémové protokoly a protokoly událostí pro všechny hostitele.
  * Cluster s podporou převzetí služeb při selhání a protokoly událostí technologie Hyper-V se shromažďují v roli **úložiště** .
  * Protokoly ACS se shromažďují v rolích **úložiště** a **ACS** .

> [!NOTE]
> Pro shromážděné protokoly se vynutily limity velikosti a stáří, protože jsou nezbytné k zajištění efektivního využití prostoru úložiště, aby se zajistilo, že se protokoly přestanou používat. Ale při diagnostikování problému někdy potřebujete protokoly, které už možná neexistují z důvodu těchto limitů. Proto se důrazně **doporučuje** přesměrovat protokoly do externího prostoru úložiště (účet úložiště v Azure, dalších místních úložných zařízení atd.) každých 8 až 12 hodin a v závislosti na vašich požadavcích je uchovávat po dobu 1-3 měsíců. Ujistěte se také, že je toto umístění úložiště šifrované.

### <a name="invoke-azurestackondemandlog"></a>Invoke-AzureStackOnDemandLog

Pomocí rutiny **Invoke-AzureStackOnDemandLog** můžete pro určité role generovat protokoly na vyžádání (viz seznam na konci této části). Protokoly generované touto rutinou nejsou ve výchozím nastavení k dispozici v sadě protokolů, které obdržíte při spuštění rutiny **Get-AzureStackLog** . Také se doporučuje shromáždit tyto protokoly pouze v případě, že je požaduje tým podpory společnosti Microsoft.

V současné době můžete pomocí `-FilterByRole` parametru filtrovat shromažďování protokolů podle následujících rolí:

* OEM
* NC
* SLB
* brána

#### <a name="example-of-collecting-on-demand-logs"></a>Příklad shromažďování protokolů na vyžádání

```powershell
$ipAddress = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.

$password = ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object -TypeName System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $password)

$shareCred = Get-Credential

$session = New-PSSession -ComputerName $ipAddress -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2) # Provide the time that includes the period for your issue

Invoke-Command -Session $session {
   Invoke-AzureStackOnDemandLog -Generate -FilterByRole "<on-demand role name>" # Provide the supported on-demand role name e.g. OEM, NC, SLB, Gateway
   Get-AzureStackLog -OutputSharePath "<external share address>" -OutputShareCredential $using:shareCred -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate
}

if ($session) {
   Remove-PSSession -Session $session
}
```

