---
title: Shromažďování diagnostických protokolů pomocí privilegovaného koncového bodu (PEP)
description: Naučte se shromažďovat diagnostické protokoly na vyžádání v centru Azure Stack pomocí portálu pro správu nebo skriptu PowerShellu.
author: justinha
ms.topic: article
ms.date: 03/05/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 03/05/2020
ms.openlocfilehash: df5a98e8526181a84d8b214fbdf82eb1dba00088
ms.sourcegitcommit: 53efd12bf453378b6a4224949b60d6e90003063b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2020
ms.locfileid: "79520460"
---
# <a name="send-azure-stack-hub-diagnostic-logs-by-using-the-privileged-endpoint-pep"></a>Odesílání diagnostických protokolů centra Azure Stack pomocí privilegovaného koncového bodu (PEP)

<!--how do you look up the PEP IP address. You look up the azurestackstampinfo.json--->


Ke spuštění rutiny Get-AzureStackLog v integrovaném systému je potřeba mít přístup k privilegovanému koncovému bodu (PEP). Tady je ukázkový skript, který můžete spustit pomocí PEP a shromažďovat protokoly. Pokud rušíte spuštěnou kolekci protokolů, abyste spustili novou, počkejte prosím 5 minut, než začnete novou kolekci protokolů, a zadejte `Remove-PSSession -Session $session`.


```powershell
$ipAddress = "<IP ADDRESS OF THE PEP VM>" # You can also use the machine name instead of IP here.

$password = ConvertTo-SecureString "<CLOUD ADMIN PASSWORD>" -AsPlainText -Force
$cred = New-Object -TypeName System.Management.Automation.PSCredential ("<DOMAIN NAME>\CloudAdmin", $password)

$shareCred = Get-Credential

$session = New-PSSession -ComputerName $ipAddress -ConfigurationName PrivilegedEndpoint -Credential $cred

$fromDate = (Get-Date).AddHours(-8)
$toDate = (Get-Date).AddHours(-2) # Provide the time that includes the period for your issue

Invoke-Command -Session $session { Get-AzureStackLog -OutputSharePath "<EXTERNAL SHARE ADDRESS>" -OutputShareCredential $using:shareCred -FilterByRole Storage -FromDate $using:fromDate -ToDate $using:toDate}

if ($session) {
    Remove-PSSession -Session $session
}
```

### <a name="examples"></a>Příklady

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

* Shromažďovat protokoly z nasazení klientů, Azure Stack na kterých běží samoobslužně spravované clustery Kubernetes (AKS Engine) Protokoly Kubernetes by se měly ukládat do účtu úložiště tenanta ve formátu, který umožní, aby se na ně používal časový rozsah shromažďování dat. 

  ```powershell
  Get-AzureStackLog -OutputPath <Path> -InputSasUri "<Blob Service Sas URI>" -FromDate "<Beginning of the time range>" -ToDate "<End of the time range>"
  ```

  Příklad:

  ```powershell
  Get-AzureStackLog -OutputPath C:\KubernetesLogs -InputSasUri "https://<storageAccountName>.blob.core.windows.net/<ContainerName><SAS token>" -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2) 
  ```

* Shromáždí protokoly pro hodnotu Add RPs. Obecná syntaxe je:
 
  ```powershell
  Get-AzureStackLogs -FilterByResourceProvider <<value-add RP name>>
  ```
 
  Postup shromáždění protokolů pro IoT Hub: 

  ```powershell
  Get-AzureStackLogs -FilterByResourceProvider IotHub
  ```
 
  Postup shromáždění protokolů pro Event Hubs:

  ```powershell
  Get-AzureStackLogs -FilterByResourceProvider eventhub
  ```
 
  Postup shromáždění protokolů pro Azure Stack Edge:

  ```powershell
  Get-AzureStackLogs -FilterByResourceProvide databoxedge
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
  > Tento postup je vhodný pro nahrávání protokolů. I když nemáte přístup ke sdílené složce SMB nebo přístup k Internetu, můžete v centru Azure Stack vytvořit účet Blob Storage pro přenos protokolů a potom pomocí klienta tyto protokoly načíst.  

  K vygenerování tokenu SAS pro účet úložiště se vyžadují následující oprávnění:

  * Přístup ke službě Blob Storage.
  * Přístup k typu prostředku kontejneru.

  Pokud chcete vygenerovat hodnotu identifikátoru URI SAS, která se má použít pro parametr `-OutputSasUri`, postupujte takto:

  1. Podle kroků [v tomto článku](/azure/storage/common/storage-quickstart-create-account)vytvořte účet úložiště.
  2. Otevřete instanci Průzkumník služby Azure Storage.
  3. Připojte se k účtu úložiště vytvořenému v kroku 1.
  4. Přejděte na **kontejnery objektů BLOB** ve **službě úložiště**.
  5. Vyberte **vytvořit nový kontejner**.
  6. Klikněte pravým tlačítkem na nový kontejner a pak klikněte na **získat sdílený přístupový podpis**.
  7. V závislosti na vašich požadavcích Vyberte platný **čas spuštění** a **čas ukončení**.
  8. Pro požadovaná oprávnění vyberte **čtení**, **zápis**a **seznam**.
  9. Vyberte **Vytvořit**.
  10. Získáte sdílený přístupový podpis. Zkopírujte část adresy URL a zadejte ji do parametru `-OutputSasUri`.

### <a name="parameter-considerations"></a>Požadavky na parametry 

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
  |ACSBlob               |CacheService                   |IBC                            |OEM                       |WAS            |
  |ACSDownloadService    |Compute                        |InfraServiceController         |OnboardRP                 |WASPUBLIC|
  |ACSFabric             |CPI                            |KeyVaultAdminResourceProvider  |PROTOKOLU                       |         |
  |ACSFrontEnd           |CRP                            |KeyVaultControlPlane           |QueryServiceCoordinator   |         | 
  |ACSMetrics            |DeploymentMachine              |KeyVaultDataPlane              |QueryServiceWorker        |         |
  |ACSMigrationService   |DiskRP                         |KeyVaultInternalControlPlane   |SeedRing                  |         |
  |ACSMonitoringService  |Domain (Doména)                         |KeyVaultInternalDataPlane      |SeedRingServices          |         |
  |ACSSettingsService    |OSN                            |KeyVaultNamingService          |SLB                       |         |
  |ACSTableMaster        |EventAdminRP                   |MDM                            |SQL                       |         |
  |ACSTableServer        |EventRP                        |MetricsAdminRP                 |SRP                       |         |
  |ACSWac                |ExternalDNS                    |MetricsRP                      |Úložiště                   |         |
  |ADFS                  |FabricRing                     |MetricsServer                  |StorageController         |         |
  |ApplicationController |FabricRingServices             |MetricsStoreService            |URP                       |         |
  |ASAppGateway          |FirstTierAggregationService    |MonAdminRP                     |SupportBridgeController   |         |
  |AzureBridge           |FRP                            |MonRP                          |SupportRing               |         |
  |AzureMonitor          |Brána                        |NC                             |SupportRingServices       |         |
  |BareMetal             |HealthMonitoring               |NonPrivilegedAppGateway        |SupportBridgeRP           |         |
  |BRP                   |HintingServiceV2               |NRP                            |UsageBridge               |         |
  |   |   |   |    |     | 

### <a name="additional-considerations-on-diagnostic-logs"></a>Další požadavky na diagnostické protokoly

* Spuštění příkazu může nějakou dobu trvat, a to na základě rolí, které protokoly shromažďují. Přispívající faktory také zahrnují dobu určenou pro shromažďování protokolů a počty uzlů v prostředí Azure Stack hub.
* Když je shromažďování protokolů spuštěno, ověřte novou složku vytvořenou v parametru **OutputSharePath** zadaného v příkazu.
* Každá role má své protokoly uvnitř jednotlivých souborů zip. V závislosti na velikosti shromážděných protokolů může být role rozdělená do více souborů zip. Pokud pro takovou roli chcete, aby všechny soubory protokolu byly extrahovány do jediné složky, použijte nástroj, který se může volně rozkomprimovat. Vyberte všechny soubory zip pro roli a vyberte **extrahovat sem**. Všechny soubory protokolu této role budou v jedné sloučené složce extrahovány.
* Ve složce, která obsahuje soubory protokolu zip, se vytvoří i soubor s názvem **Get-AzureStackLog_Output. log** . Tento soubor je protokolem výstupu příkazu, který se dá použít k řešení problémů během shromažďování protokolů. V některých případech soubor protokolu obsahuje `PS>TerminatingError` položky, které je možné ignorovat, pokud po spuštění shromažďování protokolů nechybějí žádné soubory protokolu.
* Pro prošetření konkrétního selhání mohou být protokoly potřeba z více než jedné součásti.

  * V roli **VirtualMachines** jsou shromažďovány systémové protokoly a protokoly událostí pro všechny virtuální počítače infrastruktury.
  * V roli **BareMetal** jsou shromažďovány systémové protokoly a protokoly událostí pro všechny hostitele.
  * Cluster s podporou převzetí služeb při selhání a protokoly událostí technologie Hyper-V se shromažďují v roli **úložiště** .
  * Protokoly ACS se shromažďují v rolích **úložiště** a **ACS** .

> [!NOTE]
> Limity velikosti a stáří se v protokolech shromažďují, protože jsou nezbytné k zajištění efektivního využití prostoru úložiště a k tomu, abyste se vyhnuli zahlcení protokoly. Při diagnostikování problému ale někdy budete potřebovat protokoly, které už neexistují z důvodu těchto limitů. Proto se **důrazně doporučuje** přesměrovat protokoly do externího prostoru úložiště (účet úložiště v Azure, dalších místních úložných zařízení atd.) každých 8 až 12 hodin a v závislosti na vašich požadavcích je uchovávat po dobu 1-3 měsíců. Zajistěte také, aby bylo toto umístění úložiště šifrované.

### <a name="invoke-azurestackondemandlog"></a>Invoke-AzureStackOnDemandLog

Pomocí rutiny **Invoke-AzureStackOnDemandLog** můžete pro určité role generovat protokoly na vyžádání (viz seznam na konci této části). Protokoly generované touto rutinou nejsou ve výchozím nastavení k dispozici v sadě protokolů, které obdržíte při spuštění rutiny **Get-AzureStackLog** . Také se doporučuje shromažďovat tyto protokoly pouze v případě, že je požaduje tým podpory společnosti Microsoft.

V současné době můžete pomocí parametru `-FilterByRole` filtrovat shromažďování protokolů podle následujících rolí:

* OEM
* NC
* SLB
* Brána

#### <a name="example-of-collecting-on-demand-diagnostic-logs"></a>Příklad shromažďování diagnostických protokolů na vyžádání

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

### <a name="how-diagnostic-log-collection-using-the-pep-works"></a>Jak shromažďování protokolů diagnostiky pomocí PEP funguje

Nástroje pro diagnostiku centra Azure Stack umožňují snadno a efektivně vytvářet kolekce protokolů. Následující diagram znázorňuje fungování diagnostických nástrojů:

![Diagram pracovního postupu diagnostických nástrojů centra Azure Stack](media/azure-stack-diagnostics/get-azslogs.png)


#### <a name="trace-collector"></a>Kolektor trasování

Kolektor trasování je ve výchozím nastavení povolená a na pozadí se bude shromažďovat všechny protokoly ETW (Event Tracing for Windows) ze služby komponent Azure Stack hub. Protokoly ETW se ukládají do běžné místní sdílené složky s omezením stáří na pět dní. Po dosažení tohoto limitu se nejstarší soubory odstraní, jakmile se vytvoří nové. Výchozí maximální velikost povolená pro každý soubor je 200 MB. K kontrole velikosti dojde každé 2 minuty a pokud je aktuální soubor > = 200 MB, je uložen a generuje se nový soubor. Celková velikost souborů vygenerovaných každou relaci události je také omezena na 8 GB.

#### <a name="get-azurestacklog"></a>Get-AzureStackLog

Rutina PowerShellu Get-AzureStackLog se dá použít ke shromažďování protokolů ze všech komponent v prostředí centra Azure Stack. Ukládá je do souborů zip v uživatelsky definovaném umístění. Pokud tým technické podpory centra Azure Stack potřebuje vaše protokoly, aby mohl pomoct vyřešit problém, může vás požádat o spuštění rutiny Get-AzureStackLog.

> [!CAUTION]
> Tyto soubory protokolu mohou obsahovat identifikovatelné osobní údaje (PII). Před tím, než budete veřejně publikovat všechny soubory protokolu, je nutné vzít v úvahu.

Níže jsou uvedeny příklady typů protokolů, které jsou shromažďovány:

* **Protokoly nasazení centra Azure Stack**
* **Protokoly událostí systému Windows**
* **Protokoly Panther**
* **Protokoly clusteru**
* **Diagnostické protokoly úložiště**
* **Protokoly ETW**

Tyto soubory se shromažďují a ukládají do sdílené složky pomocí Kolektor trasování. Příkaz Get-AzureStackLog je pak možné použít k jejich shromáždění v případě potřeby.

