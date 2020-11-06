---
title: Ověření stavu systému pomocí nástroje Azure Stackho ověřování centra
description: Naučte se používat nástroj pro ověření centra Azure Stack k ověření stavu systému.
author: justinha
ms.topic: article
ms.date: 01/10/2020
ms.author: justinha
ms.reviewer: adshar
ms.lastreviewed: 01/10/2020
ms.openlocfilehash: 4c91954e4a3a19640d519d16363c0d2742077d67
ms.sourcegitcommit: 30ea43f486895828710297967270cb5b8d6a1a18
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2020
ms.locfileid: "93415160"
---
# <a name="validate-azure-stack-hub-system-state"></a>Ověřit stav systému centra Azure Stack

Jako operátor centra Azure Stack je možné určit stav systému na vyžádání v podstatě. Nástroj pro ověření centra Azure Stack ( **test-AzureStack** ) je rutina prostředí PowerShell, která umožňuje spustit sérii testů v systému, aby bylo možné identifikovat chyby, pokud jsou k dispozici. Obvykle budete požádáni o spuštění tohoto nástroje prostřednictvím [privilegovaného koncového bodu (PEP)](azure-stack-privileged-endpoint.md) při kontaktování podpory zákaznických služeb microsoftu (podpora Microsoftu) s problémem. Když jsou dostupné informace o stavu a stavu v rámci systému, podpora Microsoftu můžou shromažďovat a analyzovat podrobné protokoly, soustředit se na oblast, kde došlo k chybě, a spolupracovat s vámi, abyste problém vyřešili.

## <a name="running-the-validation-tool-and-accessing-results"></a>Spuštění nástroje pro ověřování a přístup k výsledkům

Jak je uvedeno výše, nástroj pro ověření se spouští přes PEP. Každý test vrátí stav **předání nebo selhání** v okně PowerShellu. Tady je přehled kompletního procesu testování ověřování:

1. Vytvořte vztah důvěryhodnosti. V integrovaném systému spusťte následující příkaz z relace se zvýšenými oprávněními Windows PowerShellu a přidejte PEP jako důvěryhodného hostitele na zesílený virtuální počítač, který běží na hostiteli životního cyklu hardwaru nebo na pracovní stanici privilegovaného přístupu.

   ```powershell
   winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
   ```

   Pokud používáte Azure Stack Development Kit (ASDK), přihlaste se k hostiteli vývojové sady.

1. Přístup k PEP. Spuštěním následujících příkazů vytvořte relaci PEP:

   ```powershell
   Enter-PSSession -ComputerName "<ERCS VM-name/IP address>" -ConfigurationName PrivilegedEndpoint -Credential $localcred 
   ```

   > [!TIP]
   > Pro přístup k PEP na hostitelském počítači s Azure Stack Development Kit (ASDK) použijte AzS-ERCS01 pro-ComputerName.

1. Až budete v PEP, spusťte:

   ```powershell
   Test-AzureStack
   ```

   Další informace najdete v tématu věnovaném [parametrům](azure-stack-diagnostic-test.md#parameter-considerations) a [příkladům případu použití](azure-stack-diagnostic-test.md#use-case-examples).

1. Pokud dojde k **selhání** jakékoli testy, spusťte příkaz `Get-AzureStackLog` . Pokyny k integrovanému systému najdete v tématu spuštění rutiny [Get-AzureStackLog v systémech integrovaných v centru Azure Stack](azure-stack-get-azurestacklog.md).

   Rutina shromáždí protokoly generované rutinou test-AzureStack. Doporučujeme, abyste protokoly neshromáždili a místo toho kontaktovali podpora Microsoftu, když testy hlásí **Upozornění**.

1. Pokud jste vyzváni ke spuštění ověřovacího nástroje podpora Microsoftu, bude zástupce podpora Microsoftu požádat, aby shromážděné protokoly pokračovaly v řešení potíží.

## <a name="tests-available"></a>Dostupné testy

Nástroj pro ověření umožňuje spustit řadu testů na úrovni systému a základních cloudových scénářů, které vám poskytnou přehled o aktuálním stavu, což vám umožní opravovat problémy v systému.

### <a name="cloud-infrastructure-tests"></a>Testy infrastruktury cloudu

Tyto testy s nízkým dopadem fungují na úrovni infrastruktury a poskytují informace o různých systémových součástech a funkcích. V současné době se testy seskupují do následujících kategorií:

| Kategorie testu                                        | Argument pro-include a-Ignore |
| :--------------------------------------------------- | :-------------------------------- |
| Shrnutí služby ACS centra Azure Stack                              | AzsAcsSummary                     |
| Souhrn služby Active Directory centra Azure Stack                 | AzsAdSummary                      |
| Souhrn výstrah centra Azure Stack                            | AzsAlertSummary                   |
| Shrnutí selhání aplikace centra Azure Stack                | AzsApplicationCrashSummary        |
| Shrnutí dostupnosti sdílené složky centra Azure Stackho zálohování       | AzsBackupShareAccessibility       |
| Souhrn řadiče pro správu základní desky Azure Stack centra                              | AzsStampBMCSummary                |
| Shrnutí infrastruktury cloudového hostování Azure Stack centra     | AzsHostingInfraSummary            |
| Využití infrastruktury cloudového hostingu centra Azure Stack | AzsHostingInfraUtilization        |
| Azure Stack souhrn roviny ovládacího prvku centra                    | AzsControlPlane                   |
| Souhrn Defenderu centra Azure Stack                         | AzsDefenderSummary                |
| Souhrn firmwaru infrastruktury služby Azure Stack hub pro hostování  | AzsHostingInfraFWSummary          |
| Kapacita infrastruktury centra Azure Stack                  | AzsInfraCapacity                  |
| Azure Stack výkonu infrastruktury centra               | AzsInfraPerformance               |
| Souhrn rolí infrastruktury centra Azure Stack              | AzsInfraRoleSummary               |
| Infrastruktura sítě Azure Stack hub                            | AzsNetworkInfra                   |
| Portál centra Azure Stack a Přehled rozhraní API                   | AzsPortalAPISummary               |
| Události virtuálního počítače jednotky škálování centra Azure Stack                     | AzsScaleUnitEvents                |
| Prostředky virtuálních počítačů jednotky škálování centra Azure Stack                  | AzsScaleUnitResources             |
| Scénáře centra Azure Stack                                | AzsScenarios                      |
| Shrnutí ověřování SDN centra Azure Stack                   | AzsSDNValidation                  |
| Souhrn rolí Service Fabric centra Azure Stack              | AzsSFRoleSummary                  |
| Azure Stack rovina dat úložiště centra                       | AzsStorageDataPlane               |
| Shrnutí služby úložiště Azure Stack hub                 | AzsStorageSvcsSummary             |
| Shrnutí úložiště SQL centra Azure Stack                        | AzsStoreSummary                   |
| Shrnutí aktualizace centra Azure Stack                           | AzsInfraUpdateSummary             |
| Shrnutí umístění virtuálních počítačů centra Azure Stack                     | AzsVmPlacement                    |

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

- Nástroj pro ověřování také podporuje běžné parametry prostředí PowerShell: Verbose, Debug, ErrorAction, ErrorVariable, WarningAction, WarningVariable, inbuffer, PipelineVariable a subvariable. Další informace najdete v tématu [o běžných parametrech](/powershell/module/microsoft.powershell.core/about/about_commonparameters).  

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

Uživatelské jméno správce cloudu musí být zadané ve formátu hlavního názvu uživatele serviceadmin@contoso.onmicrosoft.com (UPN): (Azure AD). Po zobrazení výzvy zadejte heslo k účtu správce cloudu.

### <a name="groups"></a>Skupiny

Pro zlepšení prostředí operátora je povolený parametr **skupiny** , aby bylo možné spustit více kategorií testů současně. V současné době jsou definovány tři skupiny: **Default** , **UpdateReadiness** a **SecretRotationReadiness**.

- **Výchozí** : považuje se za standardní běh rutiny **test-AzureStack**. Tato skupina se spouští ve výchozím nastavení, pokud nejsou vybrané žádné jiné skupiny.
- **UpdateReadiness** : zkontroluje, jestli se instance centra Azure Stack dá aktualizovat. Když je spuštěná skupina **UpdateReadiness** , zobrazí se upozornění jako chyby ve výstupu konzoly a měla by se považovat za blokování pro aktualizaci. Od centra Azure Stack verze 1910 jsou součástí skupiny **UpdateReadiness** tyto kategorie:

  - **AzsInfraFileValidation**
  - **AzsActionPlanStatus**
  - **AzsStampBMCSummary**

- **SecretRotationReadiness** : zkontroluje, jestli je instance centra Azure Stack ve stavu, ve kterém je možné spustit rotaci tajných klíčů. Když je spuštěná skupina **SecretRotationReadiness** , zobrazí se upozornění jako chyby ve výstupu konzoly a měla by se považovat za blokování pro rotaci tajných klíčů. Následující kategorie jsou součástí skupiny SecretRotationReadiness:

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

Následující příklad spustí **test-AzureStack** pro otestování připravenosti systému před instalací aktualizace nebo opravy hotfix pomocí **skupiny**. Než začnete s instalací aktualizace nebo opravy hotfix, spusťte rutinu **test-AzureStack** a zkontrolujte stav centra Azure Stack:

```powershell
Test-AzureStack -Group UpdateReadiness
```

Pokud v centru Azure Stack běží verze před 1811, spusťte rutinu **test-AzureStack** pomocí následujících příkazů PowerShellu:

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

Tento test ověří konektivitu síťové infrastruktury, která obchází síťovou definovanou síť (SDN) Azure Stack hub. Ukazuje připojení z veřejné virtuální IP adresy k nakonfigurovaným serverům DNS pro přeposílání, serverům NTP a koncovým bodům ověřování. To zahrnuje připojení k Azure při použití Azure AD jako zprostředkovatele identity nebo federovaného serveru při použití AD FS jako zprostředkovatele identity.

K získání podrobného výstupu příkazu přidejte parametr Debug:

```powershell
Test-AzureStack -Include AzsNetworkInfra -Debug
```

## <a name="next-steps"></a>Další kroky

Další informace o nástrojích pro diagnostiku centra Azure Stack a protokolování problémů najdete v tématu [Nástroje pro diagnostiku Azure Stack centra](./azure-stack-diagnostic-log-collection-overview.md?view=azs-2002).

Další informace o řešení potíží najdete v tématu [řešení potíží s centrem Microsoft Azure Stack](azure-stack-troubleshooting.md).