---
title: Azure Stack shromažďování dat v HCI
description: Toto téma popisuje návrh a zásady související s diagnostickými daty shromažďovanými nástrojem Azure Stack HCI.
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 12/10/2020
ms.openlocfilehash: 91f578bcb0dc3a9d2debd086e6ed95cb591de90d
ms.sourcegitcommit: afdae61022037b5dba8345cb264049897e0aca8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97053067"
---
# <a name="azure-stack-hci-data-collection"></a>Azure Stack shromažďování dat v HCI

> Platí pro: Azure Stack HCI, verze 20H2

V tomto tématu jsou popsána požadovaná shromážděná data, která zajistí Azure Stack zabezpečení HCI, aktuální a v prosinci 2020 funguje podle očekávání pro obecnou dostupnost (GA).

K poskytování Azure Stack HCL je potřeba, aby Microsoft poskytovala níže uvedená data. Tato data se shromažďují jednou denně a události shromažďování dat se dají zobrazit v protokolech událostí. Azure Stack HCI shromažďuje minimální data potřebná k zajištění aktuálnosti clusterů, jejich zabezpečení a správné fungování v aktuálním stavu.

   > [!IMPORTANT]
   > Níže uvedená data, která Azure Stack shromažďuje shromažďována, jsou nezávislá na diagnostických datech systému Windows, která lze nakonfigurovat pro různé úrovně kolekce. V Azure Stack HCI je výchozím nastavením pro shromažďování diagnostických dat systému Windows zabezpečení (vypnuto), což znamená, že nejsou odesílána žádná diagnostická data systému Windows, pokud správce nezmění nastavení diagnostických dat. Další informace najdete v tématu [Konfigurace diagnostických dat Windows ve vaší organizaci](/windows/privacy/configure-windows-diagnostic-data-in-your-organization). Microsoft je nezávislý kontroler všech diagnostických dat Windows shromážděných v souvislosti s Azure Stackm HCI. Microsoft bude zpracovávat diagnostická data Windows v souladu s [prohlášením o zásadách ochrany osobních údajů společnosti Microsoft](https://privacy.microsoft.com/privacystatement).

## <a name="data-collection-and-residency"></a>Shromažďování a zasídlí dat

Tato Azure Stack data HCI: 

- neposílá se společnosti Microsoft, dokud není produkt zaregistrován v Azure. Při zrušení registrace Azure Stack HCI se tato kolekce dat zastaví.
- je zaznamenán do kanálu událostí Microsoft-AzureStack-HCI/Analytics. 
- je ve formátu JSON, aby správci systému mohli kontrolovat a analyzovat odesílaná data.
- je uložený v rámci USA v zabezpečeném datovém centru provozovaném Microsoftem.

## <a name="data-retention"></a>Uchovávání dat

Jakmile Azure Stack HCI shromáždí tato data, bude se uchovávat po dobu 90 dnů. Agregovaná, deidentifikovaná data se můžou uchovávat déle.

## <a name="what-data-is-collected"></a>Jaká data se shromažďují?

Azure Stack shromažďuje shromážděné údaje:

- Informace o serverech, jako je verze operačního systému, model procesoru, počet jader procesoru, velikost paměti, identifikátor clusteru a hodnota hash ID hardwaru
- Seznam nainstalovaných funkcí serveru Azure Stack HCI (například BitLocker)
- Informace nezbytné k výpočtu spolehlivosti operačního systému Azure Stack HCI
- Informace nezbytné k výpočtu spolehlivosti dat shromažďování informací o stavu
- Informace získané z protokolu událostí pro konkrétní chyby, jako je stažení aktualizace, se nezdařilo.
- Informace pro výpočetní spolehlivost úložiště
- Informace k výpočtu spolehlivosti fyzického disku
- Informace o výpočtu spolehlivosti šifrování svazku
- Informace o výpočtu spolehlivosti a výkonu opravy prostorů úložiště
- Informace k ověření zabezpečení Azure Stack operační systém HCI
- Informace o tom, jak vypočítat spolehlivost antivirového nebo antimalwarového stavu Azure Stackho operačního systému HCI
- Informace pro korelaci spolehlivosti síťových součástí
- Informace pro korelaci výkonu sítě
- Informace pro korelaci spolehlivosti aktualizací a instalací
- Informace k měření spolehlivosti technologie Hyper-V
- Informace o tom, jak měřit nebo korelovat spolehlivost komponent clusteringu
- Informace, které sledují úspěch funkce aktualizace pro clustery (CAU)
- Informace o tom, jak změřit nebo korelovat spolehlivost funkce zotavení po havárii
- Informace, které popisují omezení šířky pásma protokolu SMB použité pro Azure Stack serverech HCI

## <a name="view-this-data"></a>Zobrazit tato data

1. Povolte analytický protokol pomocí následujícího příkazu PowerShellu:

   ```PowerShell
   wevtutil sl Microsoft-AzureStack-HCI/Analytic /e:True
   ```

2. Podívejte se na protokol pro zobrazení shromážděných dat:

   ```PowerShell
   Get-WinEvent -LogName Microsoft-AzureStack-HCI/Analytic -Oldest
   ```

3. Naformátujte data pro export:

   ```PowerShell
   Get-WinEvent -LogName Microsoft-AzureStack-HCI/Analytic -Oldest `
   | Where-Object Id -eq 802 `
   | ForEach-Object { 
       [pscustomobject] @{
           TimeCreated = $_.TimeCreated 
           EventName=$_.Properties[0].Value 
           Value=$_.Properties[1].Value 
       } 
   }
   ```
 
Výstup by měl vypadat přibližně takto:

```shell
TimeCreated            EventName                                                  Value
-----------            ---------                                                  -----
11/16/2020 10:36:28 AM Microsoft.AzureStack.HCI.Diagnostic.Core                   {"OEMName":"Microsoft Corporation"...
11/16/2020 10:36:28 AM Microsoft.AzureStack.HCI.Diagnostic.ProductFeatures        {"InstalledFeatures":["Server-Core...
11/16/2020 10:36:28 AM Microsoft.AzureStack.HCI.Diagnostic.OSReliability          {"DailyDirtyRestarts":0,"WeeklyDir...
11/16/2020 10:36:28 AM Microsoft.AzureStack.HCI.Diagnostic.DiagnosticHealth       {"DailySuccessfulDiagnosticUploads...
11/16/2020 10:36:28 AM Microsoft.AzureStack.HCI.Diagnostic.ErrorSummary           {"ErrorSummary":[{"EventName":"Win...
11/16/2020 10:36:29 AM Microsoft.AzureStack.HCI.Diagnostic.VolumeSummary          {"VolumeCount":2,"HealthyVolumeCou...
11/16/2020 10:36:29 AM Microsoft.AzureStack.HCI.Diagnostic.DiskSummary            {"DiskCount":33,"Summary":[]}
11/16/2020 10:36:29 AM Microsoft.AzureStack.HCI.Diagnostic.BitlockerVolumeSummary {"BitlockerVolumeCount":0,"Summary...
11/16/2020 10:36:29 AM Microsoft.AzureStack.HCI.Diagnostic.StorageErrors          {"ErrorSummary":[{"EventName":"Sto...
11/16/2020 10:36:29 AM Microsoft.AzureStack.HCI.Diagnostic.StorageRepairSummary   {"DailyRepairStartCount":0,"Weekly...
11/16/2020 10:36:29 AM Microsoft.AzureStack.HCI.Diagnostic.TrustedPlatformModule  {"Manufacturer":"MSFT","Manufactur...
11/16/2020 10:36:29 AM Microsoft.AzureStack.HCI.Diagnostic.MicrosoftDefender      {"AMEngineVersion":"1.1.17600.5","...
11/16/2020 10:36:30 AM Microsoft.AzureStack.HCI.Diagnostic.NetworkInfo            {"NetworkDirect":true,"NetworkDire...
11/16/2020 10:36:30 AM Microsoft.AzureStack.HCI.Diagnostic.NetworkAdapterSummary  {"NetworkAdapterGroup":[{"DriverNa...
11/16/2020 10:36:30 AM Microsoft.AzureStack.HCI.Diagnostic.OSDeploy               {"OSInstallType":0}
11/16/2020 10:36:30 AM Microsoft.AzureStack.HCI.Diagnostic.ClusterProperties      {"Id":"fd2fc061-b924-4d61-a45b-3b3...
11/16/2020 10:36:30 AM Microsoft.AzureStack.HCI.Diagnostic.DisasterRecovery       {"IsDisasterRecoveryEnabled":false...
```
