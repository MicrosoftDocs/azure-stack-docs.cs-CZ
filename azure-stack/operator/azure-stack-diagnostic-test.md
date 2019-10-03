---
title: Ověření stavu systému pomocí nástroje pro ověření Azure Stack | Microsoft Docs
description: Naučte se používat nástroj pro ověření Azure Stack k ověření stavu systému.
services: azure-stack
author: justinha
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 06/26/2019
ms.author: justinha
ms.reviewer: adshar
ms.lastreviewed: 12/03/2018
ms.openlocfilehash: 194af241480cce42273ff81d91213a63b1b9fd59
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71829171"
---
# <a name="validate-azure-stack-system-state"></a>Ověřit Azure Stack stav systému

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Jako operátor Azure Stack je možné určit stav systému na vyžádání v podstatě. Nástroj pro ověření Azure Stack (**test-AzureStack**) je rutina prostředí PowerShell, která umožňuje spuštění řady testů v systému, aby bylo možné identifikovat chyby, pokud jsou k dispozici. Obvykle budete vyzváni ke spuštění tohoto nástroje prostřednictvím [privilegovaného koncového bodu (PEP)](azure-stack-privileged-endpoint.md) při kontaktování podpory zákaznických služeb Microsoftu (CSS) s problémem. Pomocí informací o stavu a stavu v rámci systému může šablona stylů CSS shromažďovat a analyzovat podrobné protokoly, soustředit se na oblast, ve které došlo k chybě, a s vámi vyřešit problémy.

## <a name="running-the-validation-tool-and-accessing-results"></a>Spuštění nástroje pro ověření a přístup k výsledkům

Jak je uvedeno výše, nástroj pro ověření se spouští přes PEP. Každý test vrátí stav **předání nebo selhání** v okně PowerShellu. Tady je přehled kompletního procesu testování ověřování:

1. Přístup k PEP. Spuštěním následujících příkazů vytvořte relaci PEP:

   ```powershell
   Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
   ```

   > [!TIP]
   > Pro přístup k PEP na hostitelském počítači s Azure Stack Development Kit (ASDK) použijte AzS-ERCS01 pro-ComputerName.

2. Až budete v PEP, spusťte:

   ```powershell
   Test-AzureStack
   ```

   Další informace najdete v tématu věnovaném [parametrům](azure-stack-diagnostic-test.md#parameter-considerations) a [příkladům případu použití](azure-stack-diagnostic-test.md#use-case-examples).

3. Pokud dojde k **selhání**jakékoli testy, spusťte `Get-AzureStackLog`. Pokyny k integrovanému systému najdete v tématu [spuštění rutiny Get-AzureStackLog v Azure Stack integrovaných systémech](azure-stack-configure-on-demand-diagnostic-log-collection.md#to-run-get-azurestacklog-on-azure-stack-integrated-systems)nebo na ASDK v tématu [spuštění rutiny Get-AZURESTACKLOG v systému ASDK](azure-stack-configure-on-demand-diagnostic-log-collection.md#run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system).

   Rutina shromáždí protokoly generované rutinou test-AzureStack. Doporučujeme, abyste neshromáždili protokoly a místo toho kontaktovali šablony stylů CSS, pokud testy **upozorňují**na zprávu.

4. Pokud jste požádali o spuštění nástroje pro ověření šablonou stylů CSS, vyžádá si zástupce šablon stylů CSS protokoly, které jste shromáždili, aby bylo možné pokračovat v řešení problému.

## <a name="tests-available"></a>Dostupné testy

Nástroj pro ověření umožňuje spustit řadu testů na úrovni systému a základních cloudových scénářů, které vám poskytnou přehled o aktuálním stavu, což vám umožní opravovat problémy v systému.

### <a name="cloud-infrastructure-tests"></a>Testy infrastruktury cloudu

Tyto testy s nízkým dopadem fungují na úrovni infrastruktury a poskytují informace o různých systémových součástech a funkcích. V současné době se testy seskupují do následujících kategorií:

| Kategorie testu                                        | Argument pro-include a-Ignore |
| :--------------------------------------------------- | :-------------------------------- |
| Shrnutí služby Azure Stack ACS                              | AzsAcsSummary                     |
| Shrnutí služby Azure Stack Active Directory                 | AzsAdSummary                      |
| Shrnutí výstrahy Azure Stack                            | AzsAlertSummary                   |
| Azure Stack souhrn chyby aplikace                | AzsApplicationCrashSummary        |
| Souhrn přístupnosti sdílené složky zálohování Azure Stack       | AzsBackupShareAccessibility       |
| Souhrn Azure Stack BMC                              | AzsStampBMCSummary                |
| Azure Stack souhrn infrastruktury hostování cloudu     | AzsHostingInfraSummary            |
| Azure Stack využití infrastruktury hostování cloudu | AzsHostingInfraUtilization        |
| Shrnutí roviny ovládacího prvku Azure Stack                    | AzsControlPlane                   |
| Souhrn Azure Stack Defenderu                         | AzsDefenderSummary                |
| Souhrn firmwaru infrastruktury Azure Stack hostování  | AzsHostingInfraFWSummary          |
| Azure Stack kapacity infrastruktury                  | AzsInfraCapacity                  |
| Azure Stack výkon infrastruktury               | AzsInfraPerformance               |
| Souhrn role Azure Stack infrastruktury              | AzsInfraRoleSummary               |
| Azure Stack síťové infračervené sítě                            | AzsNetworkInfra                   |
| Přehled Azure Stackho portálu a rozhraní API                   | AzsPortalAPISummary               |
| Události virtuálního počítače jednotky Azure Stack škálování                     | AzsScaleUnitEvents                |
| Prostředky virtuálních počítačů jednotek škálování Azure Stack                  | AzsScaleUnitResources             |
| Azure Stack scénáře                                | AzsScenarios                      |
| Azure Stack souhrn ověření SDN                   | AzsSDNValidation                  |
| Souhrn role Azure Stack Service Fabric              | AzsSFRoleSummary                  |
| Azure Stack rovina dat úložiště                       | AzsStorageDataPlane               |
| Souhrn služby Azure Stack Storage                 | AzsStorageSvcsSummary             |
| Souhrn Azure Stack SQL Storu                        | AzsStoreSummary                   |
| Souhrn aktualizace Azure Stack                           | AzsInfraUpdateSummary             |
| Shrnutí umístění virtuálního počítače Azure Stack                     | AzsVmPlacement                    |

### <a name="cloud-scenario-tests"></a>Cloudové testy scénářů

Kromě výše uvedených testů infrastruktury můžete také spustit testy cloudových scénářů pro kontrolu funkcí napříč součástmi infrastruktury. Přihlašovací údaje správce cloudu se vyžadují ke spuštění těchto testů, protože zahrnují nasazení prostředků.

> [!NOTE]
> V současné době nemůžete spouštět testy cloudových scénářů pomocí přihlašovacích údajů služby Active Directory federovaného Services (AD FS).

Následující cloudové scénáře jsou testovány nástrojem pro ověření:
- Vytvoření skupiny prostředků
- Vytvoření plánu
- Vytvoření nabídky
- Vytvoření účtu úložiště
- Vytváření virtuálních počítačů
- Operace BLOB Storage
- Operace úložiště fronty
- Operace úložiště tabulek

## <a name="parameter-considerations"></a>Požadavky na parametry

- **Seznam** parametrů lze použít k zobrazení všech dostupných kategorií testů.

- Parametry **include** a **Ignore** lze použít k zahrnutí nebo vyloučení kategorií testů. Další informace o těchto argumentech naleznete v následující části.

  ```powershell
  Test-AzureStack -Include AzsSFRoleSummary, AzsInfraCapacity
  ```

  ```powershell
  Test-AzureStack -Ignore AzsInfraPerformance
  ```

- Virtuální počítač tenanta je nasazený jako součást testů scénářů cloudu. Pomocí **DoNotDeployTenantVm** můžete zakázat nasazení tohoto virtuálního počítače.

- Pro spuštění testů cloudových scénářů, jak je popsáno v části [příklady případů použití](azure-stack-diagnostic-test.md#use-case-examples) , je nutné zadat parametr **ServiceAdminCredential** .

- **BackupSharePath** a **BackupShareCredential** se používají při testování nastavení zálohování infrastruktury, jak je znázorněno v části [Příklady případu použití](azure-stack-diagnostic-test.md#use-case-examples) .

- **DetailedResults** lze použít k získání informací o předání a selhání a upozornění pro každý test a také na celkovém běhu. Pokud není zadaný, **test-AzureStack** vrátí **$true** , pokud nedochází k žádným chybám, a **$false** , pokud dojde k chybám.
- **TimeoutSeconds** se dá použít k nastavení konkrétní doby pro každou skupinu k dokončení.

- Nástroj pro ověřování také podporuje běžné parametry prostředí PowerShell: Verbose, Debug, ErrorAction, ErrorVariable, WarningAction, WarningVariable, inbuffer, PipelineVariable a subvariable. Další informace najdete v tématu [o běžných parametrech](https://go.microsoft.com/fwlink/?LinkID=113216).  

## <a name="use-case-examples"></a>Příklady případů použití

### <a name="run-validation-without-cloud-scenarios"></a>Spustit ověřování bez cloudových scénářů

Spusťte nástroj pro ověření bez parametru **ServiceAdminCredential** , aby se přeskočily spuštěné testy scénářů cloudu: 

```powershell
New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred
Test-AzureStack
```

### <a name="run-validation-with-cloud-scenarios"></a>Spuštění ověřování s využitím cloudových scénářů

Zadání ověřovacího nástroje s parametrem **ServiceAdminCredentials** spustí ve výchozím nastavení testy scénáře cloudu: 

```powershell
Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -ServiceAdminCredential "<Cloud administrator user name>" 
```

Pokud chcete spustit pouze cloudové scénáře bez spuštění zbývajících testů, můžete k tomu použít parametr **include** : 

```powershell
Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -ServiceAdminCredential "<Cloud administrator user name>" -Include AzsScenarios   
```

Uživatelské jméno správce cloudu musí být zadané ve formátu hlavního názvu uživatele (UPN): serviceadmin@contoso.onmicrosoft.com(Azure AD). Po zobrazení výzvy zadejte heslo k účtu správce cloudu.

### <a name="groups"></a>Skupiny

Pro zlepšení prostředí operátora je povolený parametr **skupiny** , aby bylo možné spustit více kategorií testů současně. V současné době jsou definovány tři skupiny: **Default**, **UpdateReadiness**a **SecretRotationReadiness**.

- **Výchozí**: Považuje se za standardní spuštění rutiny **test-AzureStack**. Tato skupina se spouští ve výchozím nastavení, pokud nejsou vybrané žádné jiné skupiny.
- **UpdateReadiness**: Zkontroluje, jestli se instance Azure Stack dá aktualizovat. Když je spuštěná skupina **UpdateReadiness** , zobrazí se upozornění jako chyby ve výstupu konzoly a měla by se považovat za blokování pro aktualizaci. Následující kategorie jsou součástí skupiny **UpdateReadiness** :

  - **AzsAcsSummary**
  - **AzsDefenderSummary**
  - **AzsHostingInfraSummary**
  - **AzsInfraCapacity**
  - **AzsInfraRoleSummary**
  - **AzsPortalAPISummary**
  - **AzsSFRoleSummary**
  - **AzsStoreSummary**

- **SecretRotationReadiness**: Kontroluje, zda je instance Azure Stack ve stavu, ve kterém lze spustit rotaci tajných klíčů. Když je spuštěná skupina **SecretRotationReadiness** , zobrazí se upozornění jako chyby ve výstupu konzoly a měla by se považovat za blokování pro rotaci tajných klíčů. Následující kategorie jsou součástí skupiny SecretRotationReadiness:

  - **AzsAcsSummary**
  - **AzsDefenderSummary**
  - **AzsHostingInfraSummary**
  - **AzsInfraCapacity**
  - **AzsInfraRoleSummary**
  - **AzsPortalAPISummary**
  - **AzsSFRoleSummary**
  - **AzsStorageSvcsSummary**
  - **AzsStoreSummary**

#### <a name="group-parameter-example"></a>Příklad parametru skupiny

Následující příklad spustí **test-AzureStack** pro otestování připravenosti systému před instalací aktualizace nebo opravy hotfix pomocí **skupiny**. Než začnete s instalací aktualizace nebo opravy hotfix, spusťte rutinu **test-AzureStack** a zkontrolujte stav Azure Stack:

```powershell
Test-AzureStack -Group UpdateReadiness
```

Pokud Azure Stack používá verzi starší než 1811, spusťte rutinu **test-AzureStack**pomocí následujících příkazů PowerShellu:

```powershell
New-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
```

### <a name="run-validation-tool-to-test-infrastructure-backup-settings"></a>Spuštění nástroje pro ověření pro test nastavení zálohování infrastruktury

*Před* konfigurací zálohování infrastruktury můžete otestovat cestu k záložní sdílené složce a přihlašovací údaje pomocí testu **AzsBackupShareAccessibility** :

  ```powershell
  Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility -BackupSharePath "\\<fileserver>\<fileshare>" -BackupShareCredential $using:backupcred
  ```

*Po* konfiguraci zálohování můžete spustit **AzsBackupShareAccessibility** a ověřit, jestli je sdílená složka přístupná z ERCS:

  ```powershell
  Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility
  ```

Chcete-li otestovat nové přihlašovací údaje s nakonfigurovanou sdílenou složkou zálohování, spusťte příkaz: 

  ```powershell
  Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
  Test-AzureStack -Include AzsBackupShareAccessibility -BackupShareCredential "<PSCredential for backup share>"
  ```

### <a name="run-validation-tool-to-test-network-infrastructure"></a>Spuštění nástroje pro ověření pro otestování síťové infrastruktury

Tento test kontroluje konektivitu síťové infrastruktury, která obchází Azure Stack softwarově definované sítě (SDN). Ukazuje připojení z veřejné virtuální IP adresy k nakonfigurovaným serverům DNS pro přeposílání, serverům NTP a koncovým bodům ověřování. To zahrnuje připojení k Azure při použití Azure AD jako zprostředkovatele identity nebo federovaného serveru při použití AD FS jako zprostředkovatele identity.

K získání podrobného výstupu příkazu přidejte parametr Debug:

```powershell
Test-AzureStack -Include AzsNetworkInfra -Debug
```

## <a name="next-steps"></a>Další kroky

Další informace o Azure Stack diagnostických nástrojů a protokolování problémů najdete v tématu [nástroje Azure Stack Diagnostics](azure-stack-configure-on-demand-diagnostic-log-collection.md#using-pep-to-collect-diagnostic-logs).

Další informace o řešení potíží najdete v tématu [řešení potíží s Microsoft Azure Stack](azure-stack-troubleshooting.md).
