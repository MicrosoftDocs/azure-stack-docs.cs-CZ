---
title: Automatizace Azure Stack ověřování pomocí PowerShellu
titleSuffix: Azure Stack Hub
description: Naučte se automatizovat Azure Stack ověřování pomocí PowerShellu.
author: mattbriggs
ms.topic: tutorial
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 82edb40be312093b101040d8ec386569a00cd748
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97874757"
---
# <a name="automate-azure-stack-hub-validation-with-powershell"></a>Automatizace ověřování centra Azure Stack pomocí PowerShellu

Ověřování jako služba (VaaS) poskytuje možnost automatizovat spouštění testů pomocí skriptu **RunVaaSAutomation.ps1** .

Tento skript se dá použít k těmto akcím:

> [!div class="checklist"]
> * Nainstalovat požadavky.
> * Nainstalujte a spusťte místního agenta.
> * Spusťte testy VaaS v testovacích průchodech, ověřování řešení a pracovních postupech pro ověření balíčku.
> * Vykázat výsledky testů.

Následující odkazy obsahují informace o tom, jak spouštět testy prostřednictvím portálu VaaS. Před použitím skriptu byste měli získat informace o požadovaných parametrech a jejich hodnotách:

* Pracovní postup SolutionValidation: [ověření nového řešení centra Azure Stack](azure-stack-vaas-validate-solution-new.md)
* Pracovní postup PackageValidation: [ověření balíčků OEM](azure-stack-vaas-validate-oem-package.md)
* Pracovní postup TestPass: [Plánování testu](azure-stack-vaas-schedule-test-pass.md)

## <a name="download-the-automation-scripts"></a>Stažení skriptů pro automatizaci

1. Otevřete příkazový řádek PowerShell se zvýšenými oprávněními.

2. Spusťte následující skript pro stažení skriptu automatizace:

```powershell
# Review and update the $RootFolder parameter
$RootFolder = "c:\VaaS"

if (-not(Test-Path($RootFolder))) {
    mkdir $RootFolder
}
Invoke-WebRequest -Uri https://storage.azurestackvalidation.com/packages/Microsoft.VaaS.Scripts.latest.nupkg -OutFile "$RootFolder\RunVaaSAutomation.zip"
Expand-Archive -Path "$RootFolder\RunVaaSAutomation.zip" -DestinationPath "$RootFolder\RunVaaSAutomation" -Force
Set-Location "$RootFolder\RunVaaSAutomation"
```

## <a name="launch-the-solution-validation-workflow"></a>Spustit pracovní postup ověření řešení

Informace o tom, jak spustit pracovní postup ověření řešení prostřednictvím portálu VaaS, najdete v tématu [ověření balíčků OEM](azure-stack-vaas-validate-oem-package.md).

Spusťte následující skript s příslušnými hodnotami parametrů:

```powershell
# Review and update the following parameters
$VaaSAccountUserName = ""
$VaaSAccountPassword = ""
$VaaSAccountTenantId = ""
$ServiceAdminUserName = ""
$ServiceAdminPassword = ""
$TenantAdminUserName  = ""
$TenantAdminPassword  = ""
$CloudAdminUserName   = ""
$CloudAdminPassword   = ""
$SolutionName   = ''
$ProjectName    = ''
$DiagnosticsStorageConnection=''
$VaaSProject_SolutionValidation_Configuration='Min' # enter 'Min' or 'Max'

# No need to modify the following lines
Import-Module .\VaaSAutomation.psm1 -verbose -force
$stampInfo = Get-StampInfo -cloudAdminUserName $CloudAdminUserName -cloudAdminPassword $CloudAdminPassword -retryCount 3 -retryPeriod 30 -outputFolder "."
$AgentName = "$((Get-WmiObject win32_computersystem).DNSHostName).$((Get-WmiObject win32_computersystem).Domain)".ToLower()
$VaaSAccountCredentail = New-Object System.Management.Automation.PSCredential ($VaaSAccountUserName, (ConvertTo-SecureString $VaaSAccountPassword -AsPlainText -Force))

$testParameters = @{}
$projectParameters = @{
    "Configuration" = $VaaSProject_SolutionValidation_Configuration;
}
$scriptParameters = @{
    'VaaSAccountCredentail' = $VaaSAccountCredentail;
    'VaaSAccountTenantId' = $VaaSAccountTenantId;
    'VaaSSolutionName' = $SolutionName;
    'VaaSProjectType' = 'SolutionValidation';
    'VaaSProjectName' = $ProjectName;
    'VaaSProjectParameterHashTable'= $projectParameters;
    'VaaSTestFilter' = 'Test';
    'VaaSTestFilterValue' = '';
    'VaaSTestParameterHashTable'= $testParameters;
    'VaaSOnPremAgentName'= $AgentName;
    'MaxScriptWaitTimeInHours'=48;
    'ServiceAdminUserName' = $ServiceAdminUserName;
    'ServiceAdminPassword' = $ServiceAdminPassword;
    'TenantAdminUserName' = $TenantAdminUserName;
    'TenantAdminPassword' = $TenantAdminPassword;
    'CloudAdminUserName' = $CloudAdminUserName;
    'CloudAdminPassword' = $CloudAdminPassword;
    'DiagnosticsStorageConnection' = $DiagnosticsStorageConnection;
}
& .\RunVaaSAutomation.ps1 @scriptParameters
```

## <a name="launch-package-validation-workflow"></a>Spustit ověřovací pracovní postup balíčku

Informace o tom, jak spustit pracovní postup ověření balíčku prostřednictvím portu VaaS, najdete v tématu [ověření balíčků OEM](azure-stack-vaas-validate-oem-package.md).

Spusťte následující skript s příslušnými hodnotami parametrů:

```powershell
# Review and update the following parameters
$VaaSAccountUserName = ""
$VaaSAccountPassword = ""
$VaaSAccountTenantId = ""
$ServiceAdminUserName = ""
$ServiceAdminPassword = ""
$TenantAdminUserName  = ""
$TenantAdminPassword  = ""
$CloudAdminUserName   = ""
$CloudAdminPassword   = ""
$SolutionName   = ''
$ProjectName    = ''
$DiagnosticsStorageConnection=''
$VaaSProject_PackageValidation_PackageBlobUri=''
$VaaSProject_PackageValidation_RequireDigitalSignature = "false" # enter 'true' or 'false' string
$VaaSProject_PackageValidation_LocalPathtoAzureStackUpdatePkgs = ""
$VaaSProject_PackageValidation_LocalPathtoOEMUpdatePkgs = ""

# No need to modify the following lines
Import-Module .\VaaSAutomation.psm1 -verbose -force
$stampInfo = Get-StampInfo -cloudAdminUserName $CloudAdminUserName -cloudAdminPassword $CloudAdminPassword -retryCount 3 -retryPeriod 30 -outputFolder "."
$AgentName = "$((Get-WmiObject win32_computersystem).DNSHostName).$((Get-WmiObject win32_computersystem).Domain)".ToLower()
$VaaSAccountCredentail = New-Object System.Management.Automation.PSCredential ($VaaSAccountUserName, (ConvertTo-SecureString $VaaSAccountPassword -AsPlainText -Force))

$testParameters = @{
    "RequireDigitalSignature" = $VaaSProject_PackageValidation_RequireDigitalSignature;
    "LocalPathtoAzureStackUpdatePkgs" = $VaaSProject_PackageValidation_LocalPathtoAzureStackUpdatePkgs;
    "LocalPathtoOEMUpdatePkgs" = $VaaSProject_PackageValidation_LocalPathtoOEMUpdatePkgs;
}

$projectParameters = @{
    "PackageBlobUri" = $VaaSProject_PackageValidation_PackageBlobUri;
}
$scriptParameters = @{
    'VaaSAccountCredentail' = $VaaSAccountCredentail;
    'VaaSAccountTenantId' = $VaaSAccountTenantId;
    'VaaSSolutionName' = $SolutionName;
    'VaaSProjectType' = 'PackageValidation';
    'VaaSProjectName' = $ProjectName;
    'VaaSProjectParameterHashTable' = $projectParameters;
    'VaaSTestFilter' = 'Test';
    'VaaSTestFilterValue' = '';
    'VaaSTestParameterHashTable'= $testParameters;
    'VaaSOnPremAgentName'= $AgentName;
    'MaxScriptWaitTimeInHours'=96;
    'ServiceAdminUserName' = $ServiceAdminUserName;
    'ServiceAdminPassword' = $ServiceAdminPassword;
    'TenantAdminUserName' = $TenantAdminUserName;
    'TenantAdminPassword' = $TenantAdminPassword;
    'CloudAdminUserName' = $CloudAdminUserName;
    'CloudAdminPassword' = $CloudAdminPassword;
    'DiagnosticsStorageConnection' = $DiagnosticsStorageConnection;
}
& .\RunVaaSAutomation.ps1 @scriptParameters
```

## <a name="launch-the-test-pass-workflow"></a>Spuštění pracovního postupu test Pass

Informace o tom, jak spustit pracovní postup test Pass přes VaaS port, najdete v tématu [Plánování testu](azure-stack-vaas-schedule-test-pass.md).

Spusťte následující skript s příslušnými hodnotami parametrů:

```powershell
# Review and update the following parameters
$VaaSAccountUserName = ""
$VaaSAccountPassword = ""
$VaaSAccountTenantId = ""
$ServiceAdminUserName = ""
$ServiceAdminPassword = ""
$TenantAdminUserName  = ""
$TenantAdminPassword  = ""
$CloudAdminUserName   = ""
$CloudAdminPassword   = ""
$SolutionName   = ''
$ProjectName    = ''
$DiagnosticsStorageConnection=''

# No need to modify the following lines
# The following code is an example of running the "Cloud Simulation Engine" test
Import-Module .\VaaSAutomation.psm1 -verbose -force
$stampInfo = Get-StampInfo -cloudAdminUserName $CloudAdminUserName -cloudAdminPassword $CloudAdminPassword -retryCount 3 -retryPeriod 30 -outputFolder "."
$AgentName = "$((Get-WmiObject win32_computersystem).DNSHostName).$((Get-WmiObject win32_computersystem).Domain)".ToLower()
$VaaSAccountCredentail = New-Object System.Management.Automation.PSCredential ($VaaSAccountUserName, (ConvertTo-SecureString $VaaSAccountPassword -AsPlainText -Force))

$VaaSTestFilter='Test'
$VaaSTestFilterValue='cloudsimulation'
$testParameters = @{
    'ServiceAdminUser' = $ServiceAdminUserName;
    'ServiceAdminPassword' = $ServiceAdminPassword;
    'TenantAdminUser' = $TenantAdminUserName;
    'TenantAdminPassword' = $TenantAdminPassword;
    'CloudAdminUser' = $CloudAdminUserName;
    'CloudAdminPassword' = $CloudAdminPassword;
    'DomainFQDN' = "";
    'DomainAdminUser' = "";
    'DomainAdminPassword' = "";
    'TenantId' = $stampInfo.AadTenantId;
    'ExternalFqdn' = $stampInfo.ExternalDomainFQDN;
    'NumberOfNodes' = "$($stampInfo.NumberOfNodes)";
    'Region' = $stampInfo.RegionName;
    'RunDurationInHours' = 24;
    'EnableSuBR' = "false";
    'Faults' = "";
    'Resources' = "";
    'FaultControllerSettings' = "default";
    'DiagnosticsStorageConnection' = $diagnosticsStorageConnection;
    'DiagnosticsContainerName' = "$(New-Guid).ToString().ToLower()";
    'MaxFiddlerCaptureSizeInMB' = "0";
    'PackageHashCode' = "";
}

$projectParameters = @{}

$scriptParameters = @{
    'VaaSAccountCredentail' = $VaaSAccountCredentail;
    'VaaSAccountTenantId' = $VaaSAccountTenantId
    'VaaSSolutionName' = $SolutionName;
    'VaaSProjectType' = 'TestPass';
    'VaaSProjectName' = $ProjectName;
    'VaaSProjectParameterHashTable'= $projectParameters;
    'VaaSTestFilter'= $VaaSTestFilter;
    'VaaSTestFilterValue' = $VaaSTestFilterValue;
    'VaaSTestParameterHashTable'= $testParameters;
    'VaaSOnPremAgentName' = $AgentName;
    'MaxScriptWaitTimeInHours' = 24;
    'ServiceAdminUserName' = $ServiceAdminUserName;
    'ServiceAdminPassword' = $ServiceAdminPassword;
    'TenantAdminUserName' = $TenantAdminUserName;
    'TenantAdminPassword' = $TenantAdminPassword;
    'CloudAdminUserName' = $CloudAdminUserName;
    'CloudAdminPassword' = $CloudAdminPassword;
    'DiagnosticsStorageConnection' = $DiagnosticsStorageConnection;
}
& .\RunVaaSAutomation.ps1 @scriptParameters
```

## <a name="parameter-table"></a>Tabulka parametrů

Další informace najdete v tématu [společné parametry pracovních postupů](azure-stack-vaas-parameters.md).

| Parametr | Popis |
| --- | --- |
| VaaSAccountUserName | Vaše uživatelské jméno VaaS pro portál VaaS. |
| VaaSAccountPassword | Vaše VaaS heslo pro portál VaaS. |
| VaaSAccountTenantId | Identifikátor GUID vašeho tenanta VaaS |
| ServiceAdminUserName | Váš účet správce služby Azure Stack hub.  |
| ServiceAdminPassword | Vaše heslo služby centra Azure Stack.  |
| TenantAdminUserName | Správce primárního klienta.  |
| TenantAdminPassword | Heslo pro primárního klienta.  |
| CloudAdminUserName | Uživatelské jméno správce cloudu.  |
| CloudAdminPassword | Heslo pro správce cloudu.  |
| SolutionName | Název řešení VaaS |
| ProjectName | Název pracovního postupu VaaS. |
| DiagnosticsStorageConnection | Adresa URL SAS na účet Azure Storage, do kterého budou při spuštění testu kopírovány diagnostické protokoly. Pokyny k vygenerování adresy URL SAS najdete v tématu [generování připojovacího řetězce pro diagnostiku](azure-stack-vaas-parameters.md). |

## <a name="review-the-results"></a>Kontrola výsledků

Protokoly a sestavy testů jsou uloženy v aktuální pracovní složce. 

Další možnosti najdete v tématu [monitorování a Správa testů na portálu VaaS](azure-stack-vaas-monitor-test.md).

## <a name="next-steps"></a>Další kroky

Pokud se chcete dozvědět víc o PowerShellu v centru Azure Stack, přečtěte si nejnovější moduly.

- [Modul Azure Stack hub](/powershell/azure/azure-stack/overview?view=azurestackps-1.6.0)
