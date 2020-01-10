---
title: Ověření identity Azure pro centrum Azure Stack | Microsoft Docs
description: K ověření identity Azure použijte kontrolu připravenosti centra Azure Stack.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/24/2019
ms.author: patricka
ms.reviewer: unknown
ms.lastreviewed: 03/23/2019
ms.openlocfilehash: e38e0462bc9b30783ff0932a16e2e997f64df0fd
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75812888"
---
# <a name="validate-azure-identity"></a>Ověření identit Azure

Pomocí nástroje pro kontrolu připravenosti centra Azure Stack (**AzsReadinessChecker**) ověřte, že Azure Active Directory (Azure AD) je připravená k použití s Azure Stackm rozbočovačem. Než začnete s nasazením centra Azure Stack, ověřte svoje řešení Azure identity.  

Kontrola připravenosti ověřuje:

- Azure Active Directory (Azure AD) jako poskytovatel identity pro centrum Azure Stack.
- Účet Azure AD, který se chystáte použít, se může přihlásit jako globální správce Azure Active Directory.

Ověření zajišťuje, aby vaše prostředí bylo připravené Azure Stack centra pro ukládání informací o uživatelích, aplikacích, skupinách a instančních objektech z centra Azure Stack ve službě Azure AD.

## <a name="get-the-readiness-checker-tool"></a>Získat nástroj pro kontrolu připravenosti

Z [Galerie prostředí PowerShell](https://aka.ms/AzsReadinessChecker)si stáhněte nejnovější verzi nástroje pro kontrolu připravenosti centra Azure Stack (AzsReadinessChecker).  

## <a name="prerequisites"></a>Požadavky

Vyžadují se tyto požadavky:

**Počítač, ve kterém se nástroj spouští:**

- Windows 10 nebo Windows Server 2016 s připojením k Internetu.
- PowerShell 5,1 nebo novější. Pokud chcete zkontrolovat verzi, spusťte následující příkaz PowerShellu a pak zkontrolujte **Hlavní** verzi a **dílčí** verze:  

  ```powershell
  $PSVersionTable.PSVersion
  ```

- [PowerShell nakonfigurovaný pro centrum Azure Stack](azure-stack-powershell-install.md).
- Nejnovější verze nástroje pro [kontrolu připravenosti centra Microsoft Azure Stack](https://aka.ms/AzsReadinessChecker) .

**Azure Active Directory prostředí:**

- Identifikujte účet Azure AD, který se má použít pro centrum Azure Stack, a ujistěte se, že se jedná o Azure Active Directory globálního správce.
- Identifikujte název tenanta Azure AD. Název tenanta musí být primární název domény pro váš Azure Active Directory. například **contoso.onmicrosoft.com**.
- Identifikujte prostředí Azure, které budete používat. Podporované hodnoty parametru název prostředí jsou **AzureCloud**, **AzureChinaCloud**nebo **AzureUSGovernment**v závislosti na předplatném Azure, které používáte.

## <a name="steps-to-validate-azure-identity"></a>Postup ověření identity Azure

1. V počítači, který splňuje požadavky, otevřete příkazový řádek PowerShell se zvýšenými oprávněními a spusťte následující příkaz pro instalaci **AzsReadinessChecker**:  

   ```powershell
   Install-Module Microsoft.AzureStack.ReadinessChecker -Force
   ```

2. Z příkazového řádku PowerShellu spusťte následující příkaz, který nastaví **$serviceAdminCredential** jako správce služby pro vašeho TENANTA Azure AD.  Nahraďte **serviceadmin\@contoso.onmicrosoft.com** názvem vašeho účtu a tenanta:

   ```powershell
   $serviceAdminCredential = Get-Credential serviceadmin@contoso.onmicrosoft.com -Message "Enter credentials for service administrator of Azure Active Directory tenant"
   ```

3. Z příkazového řádku PowerShellu spusťte následující příkaz, který spustí ověřování vaší služby Azure AD:

   - Zadejte hodnotu názvu prostředí pro **AzureEnvironment**. Podporované hodnoty parametru název prostředí jsou **AzureCloud**, **AzureChinaCloud**nebo **AzureUSGovernment**v závislosti na předplatném Azure, které používáte.
   - Nahraďte **contoso.onmicrosoft.com** názvem vašeho tenanta Azure Active Directory.

   ```powershell
   Invoke-AzsAzureIdentityValidation -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment <environment name> -AADDirectoryTenantName contoso.onmicrosoft.com
   ```

4. Po spuštění nástroje si Projděte výstup. Pro požadavky na instalaci potvrďte, že stav je **OK** . Úspěšné ověření vypadá jako na následujícím obrázku:

   ```powershell
   Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
   Starting Azure Identity Validation

   Checking Installation Requirements: OK

   Finished Azure Identity Validation

   Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
   Invoke-AzsAzureIdentityValidation Completed
   ```

## <a name="report-and-log-file"></a>Soubor sestavy a protokolu

Pokaždé, když se ověřování spustí, protokoluje výsledky do **AzsReadinessChecker. log** a **AzsReadinessCheckerReport. JSON**. Umístění těchto souborů se zobrazí s výsledky ověřování v prostředí PowerShell.

Tyto soubory vám můžou přispět ke sdílení stavu ověření před nasazením centra Azure Stack nebo prozkoumání problémů s ověřováním. Oba soubory uchovávají výsledky každé následné kontroly ověření. Sestava poskytne vašemu týmu nasazení potvrzení konfigurace identity. Soubor protokolu může pomoci týmu nasazení nebo podpory prozkoumat problémy s ověřením.

Ve výchozím nastavení jsou oba soubory zapisovány do **C:\Users\<username > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.JSON**.  

- K určení jiného umístění sestavy použijte parametr **-OutputPath** ***&lt;Path&gt;*** na konci příkazového řádku run.
- Pomocí parametru **-CleanReport** na konci příkazu Run můžete vymazat informace o předchozích spuštěních nástroje ze **AzsReadinessCheckerReport. JSON**.

Další informace najdete v tématu [Sestava ověřování centra Azure Stack](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Selhání ověřování

Pokud kontrola ověření selže, zobrazí se podrobnosti o chybě v okně PowerShellu. Nástroj také protokoluje informace do souboru AzsReadinessChecker. log.

V následujících příkladech jsou uvedeny pokyny k běžným chybám ověření.

### <a name="expired-or-temporary-password"></a>Neplatné nebo dočasné heslo

```powershell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: Fail
Error Details for Service Administrator Account admin@contoso.onmicrosoft.com
The password for account  has expired or is a temporary password that needs to be reset before continuing. Run Login-AzureRMAccount, login with  credentials and follow the prompts to reset.
Additional help URL https://aka.ms/AzsRemediateAzureIdentity

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```

**Příčina** : účet se nemůže přihlásit, protože vypršela platnost hesla nebo je dočasná.

**Řešení** – v PowerShellu spusťte následující příkaz a pak postupujte podle pokynů pro resetování hesla:

```powershell
Login-AzureRMAccount
```

Případně se přihlaste k [Azure Portal](https://portal.azure.com) jako vlastník účtu a uživatel bude nucen měnit heslo.

### <a name="unknown-user-type"></a>Neznámý typ uživatele 
 
```powershell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: Fail
Error Details for Service Administrator Account admin@contoso.onmicrosoft.com
Unknown user type detected. Check the account  is valid for AzureChinaCloud
Additional help URL https://aka.ms/AzsRemediateAzureIdentity

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```

**Příčina** : účet se nemůže přihlásit k určenému Azure Active Directory (**AADDirectoryTenantName**). V tomto příkladu je **AzureChinaCloud** zadáno jako **AzureEnvironment**.

**Řešení** – potvrďte, že je účet platný pro zadané prostředí Azure. Spuštěním následujícího příkazu v PowerShellu ověřte, že je účet platný pro konkrétní prostředí:

```powershell
Login-AzureRmAccount -EnvironmentName AzureChinaCloud
```

### <a name="account-is-not-an-administrator"></a>Účet není správcem.

```powershell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: Fail
Error Details for Service Administrator Account admin@contoso.onmicrosoft.com
The Service Admin account you entered 'admin@contoso.onmicrosoft.com' is not an administrator of the Azure Active Directory tenant 'contoso.onmicrosoft.com'.
Additional help URL https://aka.ms/AzsRemediateAzureIdentity

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```

**Příčina** – i když se účet může úspěšně přihlásit, účet není správce Azure Active Directory (**AADDirectoryTenantName**).  

**Řešení** – Přihlaste se k [Azure Portal](https://portal.azure.com) jako vlastník účtu, klikněte na **Azure Active Directory**, pak na **Uživatelé**a pak **Vyberte uživatel**, **role adresáře**a pak ověřte, že je uživatel **globálním správcem**. Pokud je účet **uživatel**, vyhledejte **Azure Active Directory** > **vlastní názvy domén**a potvrďte, že název, který jste zadali pro **AADDirectoryTenantName** , je označený jako primární název domény pro tento adresář. V tomto příkladu, který je **contoso.onmicrosoft.com**.

Azure Stack hub vyžaduje, aby byl název domény primárním názvem domény.

## <a name="next-steps"></a>Další kroky

[Ověření registrace v Azure](azure-stack-validate-registration.md)  
[Zobrazit sestavu připravenosti](azure-stack-validation-report.md)  
[Obecné pokyny k integraci centra Azure Stack](azure-stack-datacenter-integration.md)  
