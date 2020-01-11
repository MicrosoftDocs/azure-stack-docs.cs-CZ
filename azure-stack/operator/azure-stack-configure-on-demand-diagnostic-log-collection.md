---
title: Shromažďovat diagnostické protokoly centra Azure Stack na vyžádání | Microsoft Docs
description: Naučte se shromažďovat diagnostické protokoly na vyžádání v centru Azure Stack s využitím pomoci a podpory nebo privilegovaného koncového bodu (PEP).
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
ms.date: 11/07/2019
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 11/07/2019
ms.openlocfilehash: f30791ec7514a68a6137d2b5d2db3441cf217f58
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75883078"
---
# <a name="collect-azure-stack-hub-diagnostic-logs-on-demand"></a>Shromažďovat diagnostické protokoly centra Azure Stack na vyžádání

V rámci řešení potíží můžou služby Microsoft Customer Support Services (CSS) potřebovat analyzovat diagnostické protokoly. Od verze 1907 mohou operátoři centra Azure Stack nahrávat diagnostické protokoly do kontejneru objektů BLOB v Azure pomocí nástroje **Help and Support**. Použití **pomoci a podpory** se doporučuje nad předchozí metodou použití prostředí PowerShell, protože je jednodušší. Pokud však portál není k dispozici, operátory mohou nadále shromažďovat protokoly pomocí **Get-AzureStackLog** prostřednictvím privilegovaného koncového bodu (PEP) jako v předchozích verzích. Toto téma popisuje jak shromažďovat diagnostické protokoly na vyžádání.

>[!Note]
>Jako alternativu ke shromažďování protokolů na vyžádání můžete zjednodušit proces řešení potíží tím, že povolíte [automatické shromažďování diagnostických protokolů](azure-stack-configure-automatic-diagnostic-log-collection.md). Pokud je potřeba prozkoumat stav systému, protokoly se nahrají automaticky pro účely analýzy šablonou CSS. 

## <a name="use-help-and-support-to-collect-diagnostic-logs-on-demand"></a>Použití pomoci a podpory ke shromažďování diagnostických protokolů na vyžádání

V případě řešení problému může CSS požádat o operátora centra Azure Stack ke shromáždění diagnostických protokolů na vyžádání pro konkrétní časové období z předchozího týdne. V takovém případě vám CSS poskytne operátor s adresou URL SAS pro nahrání kolekce. Pomocí následujících kroků proveďte konfiguraci shromažďování protokolů na vyžádání pomocí adresy URL SAS z šablony stylů CSS:

1. Otevřete okno **pomoc a podpora – přehled** a hned klikněte na **shromáždit protokoly**. 
1. Vyberte okno posunuté z 1-4 hodin za posledních sedm dní. 
1. Vyberte místní časové pásmo.
1. Zadejte adresu URL SAS, kterou poskytuje CSS.

   ![Snímek obrazovky shromažďování protokolů na vyžádání](media/azure-stack-automatic-log-collection/collect-logs-now.png)

>[!NOTE]
>Pokud je povolená funkce automatického shromažďování protokolů diagnostiky, zobrazí se v **nápovědě a podpoře** , když probíhá shromažďování protokolů. Pokud kliknete na **shromažďovat protokoly** a shromažďovat protokoly z konkrétního okamžiku, když probíhá automatické shromažďování protokolů, začne shromažďování na vyžádání po dokončení automatického shromažďování protokolů. 

## <a name="use-the-privileged-endpoint-pep-to-collect-diagnostic-logs"></a>Shromažďování diagnostických protokolů pomocí privilegovaného koncového bodu (PEP)

<!--how do you look up the PEP IP address. You look up the azurestackstampinfo.json--->



### <a name="run-get-azurestacklog-on-azure-stack-hub-integrated-systems"></a>Spuštění rutiny Get-AzureStackLog v integrovaných systémech centra Azure Stack

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

### <a name="run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system"></a>Spuštění rutiny Get-AzureStackLog v systému Azure Stack Development Kit (ASDK)

Pomocí těchto kroků spustíte `Get-AzureStackLog` v hostitelském počítači s ASDK.

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

* Shromažďovat protokoly z nasazení klientů, na kterých běží samoobslužně spravované služby Azure Kubernetes (AKS) na rozbočovači Azure Stack. Protokoly AKS by se měly ukládat do účtu úložiště tenanta ve formátu, který umožní, aby se na ně používal časový rozsah shromažďování dat. 

  ```powershell
  Get-AzureStackLog -OutputPath <Path> -InputSasUri "<Blob Service Sas URI>" -FromDate "<Beginning of the time range>" -ToDate "<End of the time range>"
  ```

  Příklad:

  ```powershell
  Get-AzureStackLog -OutputPath C:\KubernetesLogs -InputSasUri "https://<storageAccountName>.blob.core.windows.net/<ContainerName><SAS token>" -FromDate (Get-Date).AddHours(-8) -ToDate (Get-Date).AddHours(-2) 
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
  |ACSBlob               |CacheService                   |IBC                            |OEM                       |WAS            |
  |ACSDownloadService    |Služby Compute                        |InfraServiceController         |OnboardRP                 |WASPUBLIC|
  |ACSFabric             |CPI                            |KeyVaultAdminResourceProvider  |PXE                       |         |
  |ACSFrontEnd           |CRP                            |KeyVaultControlPlane           |QueryServiceCoordinator   |         | 
  |ACSMetrics            |DeploymentMachine              |KeyVaultDataPlane              |QueryServiceWorker        |         |
  |ACSMigrationService   |DiskRP                         |KeyVaultInternalControlPlane   |SeedRing                  |         |
  |ACSMonitoringService  |Domain (Doména)                         |KeyVaultInternalDataPlane      |SeedRingServices          |         |
  |ACSSettingsService    |OSN                            |KeyVaultNamingService          |SLB                       |         |
  |ACSTableMaster        |EventAdminRP                   |MDM                            |SQL                       |         |
  |ACSTableServer        |EventRP                        |MetricsAdminRP                 |SRP                       |         |
  |ACSWac                |ExternalDNS                    |MetricsRP                      |Storage                   |         |
  |ADFS                  |FabricRing                     |MetricsServer                  |StorageController         |         |
  |ApplicationController |FabricRingServices             |MetricsStoreService            |URP                       |         |
  |ASAppGateway          |FirstTierAggregationService    |MonAdminRP                     |SupportBridgeController   |         |
  |AzureBridge           |FRP                            |MonRP                          |SupportRing               |         |
  |AzureMonitor          |brána                        |NC                             |SupportRingServices       |         |
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
* brána

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


