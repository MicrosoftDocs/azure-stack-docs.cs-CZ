---
title: Ověření registrace v Azure
titleSuffix: Azure Stack Hub
description: Přečtěte si, jak ověřit registraci Azure pomocí nástroje pro kontrolu připravenosti centra Azure Stack.
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 06/25/2020
ms.author: inhenkel
ms.reviewer: jerskine
ms.lastreviewed: 06/25/2020
ms.openlocfilehash: 24bec6b10017dd824800f860273c003dc8b53f0b
ms.sourcegitcommit: 28850ae18844213ee410cfe96fc936655b5f6412
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2020
ms.locfileid: "86148161"
---
# <a name="validate-azure-registration"></a>Ověření registrace v Azure

Pomocí nástroje pro kontrolu připravenosti centra Azure Stack (**AzsReadinessChecker**) ověřte, že vaše předplatné Azure je připravené k použití s centrem Azure Stack, než začnete s nasazením centra Azure Stack. Kontrola připravenosti ověřuje tyto údaje:

- Předplatné Azure, které používáte, je podporovaný typ. Odběry musí být poskytovatel Cloud Solution Provider (CSP) nebo smlouva Enterprise (EA).
- Účet, který použijete k registraci předplatného v Azure, se může přihlásit k Azure a je vlastníkem předplatného.

Další informace o registraci centra Azure Stack najdete v tématu [registrace centra Azure Stack s Azure](azure-stack-registration.md).

## <a name="get-the-readiness-checker-tool"></a>Získat nástroj pro kontrolu připravenosti

Stáhněte si nejnovější verzi **AzsReadinessChecker** z [Galerie prostředí PowerShell](https://aka.ms/AzsReadinessChecker).  

## <a name="install-and-configure"></a>Instalace a konfigurace

### <a name="azurerm-powershell"></a>[AzureRM PowerShell](#tab/rm)

### <a name="prerequisites"></a>Požadavky

Vyžadují se tyto požadavky:

#### <a name="azurerm-powershell-modules"></a>Moduly PowerShellu pro AzureRM

Budete muset mít nainstalované moduly AZ PowerShellu. Pokyny najdete v tématu [Instalace modulu PowerShell AzureRM](azure-stack-powershell-install.md).

#### <a name="the-computer-on-which-the-tool-runs"></a>Počítač, ve kterém se nástroj spouští

- Windows 10 nebo Windows Server 2016 s připojením k Internetu.
- PowerShell 5,1 nebo novější. Pokud chcete zkontrolovat verzi, spusťte následující rutinu prostředí PowerShell a pak zkontrolujte **hlavní** a **dílčí** verze:  
  ```powershell
  $PSVersionTable.PSVersion
  ```
- [PowerShell nakonfigurovaný pro centrum Azure Stack](azure-stack-powershell-install.md).
- Nejnovější verze nástroje pro [kontrolu připravenosti centra Microsoft Azure Stack](https://aka.ms/AzsReadinessChecker) .  

#### <a name="azure-active-directory-azure-ad-environment"></a>Prostředí Azure Active Directory (Azure AD)

- Identifikujte uživatelské jméno a heslo pro účet, který je vlastníkem předplatného Azure, které použijete u služby Azure Stack hub.  
- Identifikujte ID předplatného pro předplatné Azure, které budete používat.
- Identifikujte **AzureEnvironment** , který budete používat. Podporované hodnoty parametru název prostředí jsou **AzureCloud**, **AzureChinaCloud**nebo **AzureUSGovernment**v závislosti na tom, které předplatné Azure používáte.

### <a name="steps-to-validate-the-azure-registration"></a>Postup ověření registrace Azure

1. V počítači, který splňuje požadavky, otevřete příkazový řádek PowerShell se zvýšenými oprávněními a spusťte následující příkaz pro instalaci **AzsReadinessChecker**:

   ```powershell
   Install-Module Microsoft.AzureStack.ReadinessChecker -Force
   ```

2. Z příkazového řádku PowerShellu spusťte následující příkaz, který nastavíte `$registrationCredential` jako účet, který je vlastníkem předplatného. Nahraďte `subscriptionowner@contoso.onmicrosoft.com` názvem vašeho účtu a tenanta:

   ```powershell
   $registrationCredential = Get-Credential subscriptionowner@contoso.onmicrosoft.com -Message "Enter Credentials for Subscription Owner"
   ```

   > [!NOTE]
   > Při použití sdílených služeb nebo předplatného IUR musíte jako CSP zadat přihlašovací údaje uživatele z příslušné služby Azure AD. Obvykle se jedná o podobné `subscriptionowner@iurcontoso.onmicrosoft.com` . Tento uživatel musí mít příslušné přihlašovací údaje, jak je popsáno v předchozím kroku.

3. Z příkazového řádku PowerShellu spusťte následující příkaz, který nastavíte `$subscriptionID` jako předplatné Azure, které se má použít. Nahraďte `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` vlastním ID předplatného:

   ```powershell
   $subscriptionID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
   ```

4. Z příkazového řádku PowerShellu spusťte následující příkaz, který spustí ověření předplatného:

   - Zadejte hodnotu `AzureEnvironment` jako **AzureCloud**, **AzureGermanCloud**nebo **AzureChinaCloud**.  
   - Zadejte svého správce Azure AD a název tenanta Azure AD.
      ```powershell
      Invoke-AzsRegistrationValidation -RegistrationAccount $registrationCredential -AzureEnvironment AzureCloud -RegistrationSubscriptionID $subscriptionID
      ```

5. Po spuštění nástroje si Projděte výstup. Ověřte, jestli je stav správný pro přihlášení i pro požadavky na registraci. Výstup úspěšného ověření vypadá podobně jako v následujícím příkladu:

   ```powershell
   Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
   Checking Registration Requirements: OK
   Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
   Invoke-AzsRegistrationValidation Completed
   ```

### <a name="az-powershell"></a>[AZ PowerShell](#tab/az)

### <a name="prerequisites"></a>Požadavky

Vyžadují se tyto požadavky:

#### <a name="az-powershell-modules"></a>AZ PowerShell modules

Budete muset mít nainstalované moduly AZ PowerShellu. Pokyny najdete v tématu [instalace prostředí PowerShell AZ Preview Module](powershell-install-az-module.md).

#### <a name="azure-active-directory-aad-environment"></a>Prostředí Azure Active Directory (AAD)

- Identifikujte uživatelské jméno a heslo pro účet, který je vlastníkem předplatného Azure, které použijete u služby Azure Stack hub.  
- Identifikujte ID předplatného pro předplatné Azure, které budete používat.

### <a name="steps-to-validate-the-azure-registration"></a>Postup ověření registrace Azure

1. Otevřete příkazový řádek prostředí PowerShell se zvýšenými oprávněními a spusťte následující příkaz pro instalaci **AzsReadinessChecker**:

   ```powershell
   Install-Module -Name Az.BootStrapper -Force -AllowPrerelease
   Install-AzProfile -Profile 2019-03-01-hybrid -Force
   Install-Module -Name Microsoft.AzureStack.ReadinessChecker -AllowPrerelease
   ```

2. Z příkazového řádku PowerShellu spusťte následující příkaz, který nastavíte `$subscriptionID` jako předplatné Azure, které se má použít. Nahraďte `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` vlastním ID předplatného:

   ```powershell
   $subscriptionID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
   ```

3. Z příkazového řádku PowerShellu spusťte následující příkaz: 

   ```powershell
   Connect-AzAccount -subscription $subscriptionID
   ```

4. Z příkazového řádku PowerShellu spusťte následující příkaz, který spustí ověřování vašeho předplatného. Zadejte svého správce Azure AD a název tenanta Azure AD:

   ```powershell
   Invoke-AzsRegistrationValidation  -RegistrationSubscriptionID $subscriptionID
   ```

5. Po spuštění nástroje si Projděte výstup. Ověřte, jestli je stav správný pro přihlášení i pro požadavky na registraci. Výstup úspěšného ověření vypadá podobně jako v následujícím příkladu:

   ```powershell
   Invoke-AzsRegistrationValidation v1.2005.1269 started.
   Checking Registration Requirements: OK

   Log location (contains PII): C:\Users\[*redacted*]\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Report location (contains PII): C:\Users\[*redacted*]\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
   Invoke-AzsRegistrationValidation Completed
   ```
---

## <a name="report-and-log-file"></a>Soubor sestavy a protokolu

Pokaždé, když se ověřování spustí, protokoluje výsledky do **AzsReadinessChecker. log** a **AzsReadinessCheckerReport.js**. Umístění těchto souborů se zobrazí spolu s výsledky ověřování v prostředí PowerShell.

Tyto soubory vám můžou přispět ke sdílení stavu ověření před nasazením centra Azure Stack nebo prozkoumání problémů s ověřováním. Oba soubory uchovávají výsledky každé následné kontroly ověření. Sestava poskytne vašemu týmu nasazení potvrzení konfigurace identity. Soubor protokolu může pomoci týmu nasazení nebo podpory prozkoumat problémy s ověřením.

Ve výchozím nastavení jsou oba soubory zapisovány do `C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json` .  

- Použijte `-OutputPath <path>` parametr na konci příkazového řádku pro spuštění k určení jiného umístění sestavy.
- Pomocí `-CleanReport` parametru na konci příkazu Run můžete vymazat informace o předchozích spuštěních nástroje z **AzsReadinessCheckerReport.jsv**.

Další informace najdete v tématu [Sestava ověřování centra Azure Stack](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Selhání ověřování

Pokud kontrola ověření selže, zobrazí se podrobnosti o chybě v okně PowerShellu. Nástroj také protokoluje informace do souboru **AzsReadinessChecker. log** .

V následujících příkladech jsou uvedeny další informace o běžných chybách ověřování.

### <a name="user-must-be-an-owner-of-the-subscription"></a>Uživatel musí být vlastníkem předplatného.

```shell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail
Error Details for registration account admin@contoso.onmicrosoft.com:
The user admin@contoso.onmicrosoft.com is role(s) Reader for subscription 3f961d1c-d1fb-40c3-99ba-44524b56df2d. User must be an owner of the subscription to be used for registration.
Additional help URL https://aka.ms/AzsRemediateRegistration

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```

**Příčina** – účet není správcem předplatného Azure.

**Řešení** – použijte účet, který je správcem předplatného Azure, které se bude účtovat za využití z nasazení centra Azure Stack.

### <a name="expired-or-temporary-password"></a>Neplatné nebo dočasné heslo

```shell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail
Error Details for registration account admin@contoso.onmicrosoft.com:
Checking Registration failed with: Retrieving TenantId for subscription [subscription ID] using account admin@contoso.onmicrosoft.com failed with AADSTS50055: Force Change Password.
Trace ID: [Trace ID]
Correlation ID: [Correlation ID]
Timestamp: 2018-10-22 11:16:56Z: The remote server returned an error: (401) Unauthorized.

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```

**Příčina** – účet se nemůže přihlásit, protože heslo vypršelo nebo je dočasné.

**Řešení** – v PowerShellu spusťte následující příkaz a podle pokynů resetujte heslo.

```powershell
Login-AzureRMAccount
```

Dalším způsobem je přihlašovat se k [Azure Portal](https://portal.azure.com) jako vlastník účtu a uživatel bude nucen měnit heslo.

### <a name="unknown-user-type"></a>Neznámý typ uživatele  

```shell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail
Error Details for registration account admin@contoso.onmicrosoft.com:
Checking Registration failed with: Retrieving TenantId for subscription <subscription ID> using <account> failed with unknown_user_type: Unknown User Type

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```

**Příčina** – účet se nemůže přihlásit do zadaného prostředí Azure AD. V tomto příkladu je **AzureChinaCloud** zadáno jako **AzureEnvironment**.  

**Řešení** – potvrďte, že je účet platný pro zadané prostředí Azure. Spuštěním následujícího příkazu v PowerShellu ověřte, že je účet platný pro konkrétní prostředí:

```powershell
Login-AzureRmAccount -EnvironmentName AzureChinaCloud
```

## <a name="next-steps"></a>Další kroky

- [Ověřit identitu Azure](azure-stack-validate-identity.md)
- [Zobrazit sestavu připravenosti](azure-stack-validation-report.md)
- [Obecné pokyny k integraci centra Azure Stack](azure-stack-datacenter-integration.md)
