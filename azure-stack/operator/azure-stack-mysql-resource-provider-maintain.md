---
title: Správa poskytovatele prostředků MySQL ve službě Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak můžete spravovat službu poskytovatele prostředků MySQL ve službě Azure Stack.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/06/2019
ms.author: jeffgilb
ms.reviewer: jiahan
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: 24e1e315a9b52693cb2b9ff26bf424ee8e9d4f3f
ms.sourcegitcommit: ccd86bd0862c45de1f6a4993f783ea2e186c187a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65172313"
---
# <a name="mysql-resource-provider-maintenance-operations"></a>Operace údržby poskytovatele prostředků MySQL

Poskytovatele prostředků MySQL běží na virtuálním počítači s uzamčená. Povolit operace údržby, budete muset aktualizovat zabezpečení virtuálních počítačů. Provedete to pomocí principu nejnižší úrovně oprávnění, mohou použít koncový bod Powershellu Just Enough Administration (JEA) DBAdapterMaintenance. Instalační balíček pro poskytovatele prostředků zahrnuje skript pro tuto operaci.

## <a name="update-the-virtual-machine-operating-system"></a>Aktualizace operačního systému virtuálního počítače

Protože poběží poskytovatele prostředků *uživatele* virtuální počítač, budete muset použít vyžadovaných oprav a aktualizací po jejich uvedení na trh. Můžete balíčky aktualizací Windows, které jsou k dispozici jako součást cyklu aktualizace a opravy aktualizací pro virtuální počítač.

Aktualizace poskytovatele virtuálního počítače pomocí jedné z následujících metod:

- Nainstalujte nejnovější balíček poskytovatele prostředků pomocí aktuálně opravené image jádru Windows serveru 2016.
- Instalace balíčku aktualizace Windows při instalaci nebo aktualizaci u poskytovatele prostředků.

## <a name="update-the-virtual-machine-windows-defender-definitions"></a>Aktualizace definic Windows Defenderu virtuálního počítače

K aktualizaci definic Defender, postupujte podle těchto kroků:

1. Stáhnout aktualizace definic Windows Defenderu z [definic Windows Defenderu](https://www.microsoft.com/en-us/wdsi/definitions).

    Na stránce definice posuňte se dolů "Ručně stáhnout a nainstalovat definice". Stáhněte si soubor 64-bit "Antivirus Windows Defenderu pro Windows 10 a Windows 8.1".

    Můžete taky použít [tento přímý odkaz](https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64) na stažení nebo spuštění souboru fpam fe.exe.

2. Otevřete relaci Powershellu ke koncovému bodu údržby MySQL resource provider adaptéru virtuálního počítače.

3. Zkopírujte soubor aktualizace definic do adaptéru poskytovatele prostředků virtuálního počítače pomocí relace koncového bodu údržby.

4. V relaci Powershellu údržby, spusťte _aktualizace DBAdapterWindowsDefenderDefinitions_ příkazu.

5. Po instalaci definice, doporučujeme vám odstranit soubor aktualizace definic pomocí _odebrat ItemOnUserDrive)_ příkazu.

**Ukázkový skript Powershellu pro aktualizace definic.**

Můžete upravit a spustit následující skript pro aktualizaci definice Defender. Hodnoty ve skriptu nahraďte hodnotami z vašeho prostředí.

```powershell
# Set credentials for the local admin on the resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "<local admin user password>" -AsPlainText -Force
$vmLocalAdminUser = "<local admin user name>"
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential `
    ($vmLocalAdminUser, $vmLocalAdminPass)

# Provide the public IP address for the adapter VM.
$databaseRPMachine  = "<RP VM IP address>"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"

# Download Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions.  
Invoke-WebRequest -Uri 'https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64' `
    -Outfile $localPathToDefenderUpdate  

# Create a session to the maintenance endpoint.
$session = New-PSSession -ComputerName $databaseRPMachine `
    -Credential $vmLocalAdminCreds -ConfigurationName DBAdapterMaintenance

# Copy the defender update file to the adapter virtual machine.
Copy-Item -ToSession $session -Path $localPathToDefenderUpdate `
     -Destination "User:\"

# Install the update definitions.
Invoke-Command -Session $session -ScriptBlock `
    {Update-AzSDBAdapterWindowsDefenderDefinition -DefinitionsUpdatePackageFile "User:\mpam-fe.exe"}

# Cleanup the definitions package file and session.
Invoke-Command -Session $session -ScriptBlock `
    {Remove-AzSItemOnUserDrive -ItemPath "User:\mpam-fe.exe"}
$session | Remove-PSSession

```

## <a name="secrets-rotation"></a>Otočení tajných kódů

*Tyto pokyny platí pouze pro integrované systémy Azure Stack.*

Při použití poskytovatele prostředků SQL nebo MySQL pomocí služby Azure Stack integrované systémy, je zodpovědný za otáčení následující tajemství infrastruktury poskytovatele prostředků k zajištění, že nevyprší platnost operátory Azure stacku:

- Externí certifikát SSL [zadali při nasazení](azure-stack-pki-certs.md).
- Prostředků poskytovatele virtuálních počítačů heslo místního správce účet zadaný během nasazení.
- Heslo diagnostických uživatele (dbadapterdiag) poskytovatele prostředků.

### <a name="powershell-examples-for-rotating-secrets"></a>Příklady prostředí PowerShell pro výměnu tajných kódů

**Změňte všechny tajné kódy ve stejnou dobu.**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DiagnosticsUserPassword $passwd `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd `  
    -VMLocalCredential $localCreds

```

**Diagnostické uživatel heslo změňte.**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DiagnosticsUserPassword  $passwd

```

**Změna hesla účtu místního Správce virtuálních počítačů.**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -VMLocalCredential $localCreds

```

**Změňte heslo certifikátu SSL.**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd

```

### <a name="secretrotationmysqlproviderps1-parameters"></a>SecretRotationMySQLProvider.ps1 parameters

|Parametr|Popis|
|-----|-----|
|AzCredential|Azure přihlašovací údaje účtu správce služby zásobníku.|
|CloudAdminCredential|Azure Stack cloudu domény účtu přihlašovací údaje správce.|
|PrivilegedEndpoint|Privilegované koncový bod pro přístup k Get AzureStackStampInformation.|
|DiagnosticsUserPassword|Diagnostika heslo k uživatelskému účtu.|
|VMLocalCredential|Účet místního správce na virtuálním počítači MySQLAdapter.|
|DefaultSSLCertificatePassword|Výchozí certifikát SSL (* pfx) heslo.|
|DependencyFilesLocalPath|Závislost soubory místní cestu.|
|     |     |

### <a name="known-issues"></a>Známé problémy

**Problém:**<br>
Protokoly pro rotaci tajné klíče se neshromažďují automaticky selže skript rotace tajných kódů při jejím spuštění.

**Alternativní řešení:**<br>
Použijte rutinu Get-AzsDBAdapterLogs ke shromažďování všech prostředků poskytovatele protokolů, včetně AzureStack.DatabaseAdapter.SecretRotation.ps1_*.log uložené v C:\Logs.

## <a name="collect-diagnostic-logs"></a>Shromažďování diagnostických protokolů

Shromažďování protokolů z uzamčená virtuálního počítače, můžete použít koncový bod DBAdapterDiagnostics Powershellu Just Enough Administration (JEA). Tento koncový bod poskytuje následující příkazy:

- **Get-AzsDBAdapterLog**. Tento příkaz vytvoří balíček zip diagnostické protokoly pro poskytovatele prostředků a uloží soubor na disku uživatelské relace. Můžete spustit tento příkaz bez parametrů a poslední 4 hodiny protokoly se shromažďují.

- **Remove-AzsDBAdapterLog**. Tento příkaz odebere existující balíčky protokolu v poskytovateli prostředků virtuálního počítače.

### <a name="endpoint-requirements-and-process"></a>Koncový bod požadavky a proces

Při instalaci nebo aktualizaci poskytovatele prostředků dbadapterdiag uživatelský účet je vytvořen. Tento účet budete používat ke shromažďování diagnostických protokolů.

>[!NOTE]
>Heslo účtu dbadapterdiag je stejný jako heslo pro místního správce na virtuálním počítači, který se vytvoří během poskytovatele nasazení nebo aktualizace.

Použít _DBAdapterDiagnostics_ příkazy, vytvořit vzdálené relace prostředí PowerShell k virtuálnímu počítači poskytovatele prostředků a spustit **Get-AzsDBAdapterLog** příkazu.

Nastavte časové rozpětí pro shromažďování protokolů pomocí **datum FromDate** a **ToDate** parametry. Pokud nechcete zadat jeden nebo oba tyto parametry, se používají následující výchozí hodnoty:

* Datum FromDate je čtyři hodiny před aktuálním časem.
* Do data je aktuální čas.

**Ukázkový skript Powershellu pro shromažďování protokolů.**

Tento skript ukazuje, jak shromažďovat diagnostické protokoly z poskytovatele prostředků virtuálního počítače.

```powershell
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP address>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<Enter Diagnostic password>'
$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, (ConvertTo-SecureString -String $diagnosticsUserPassword -AsPlainText -Force))
$session = New-PSSession -ComputerName $databaseRPMachineIP -Credential $diagCreds
        -ConfigurationName DBAdapterDiagnostics

# Sample that captures logs from the previous hour.
$fromDate = (Get-Date).AddHours(-1)
$dateNow = Get-Date
$sb = {param($d1,$d2) Get-AzSDBAdapterLog -FromDate $d1 -ToDate $d2}
$logs = Invoke-Command -Session $session -ScriptBlock $sb -ArgumentList $fromDate,$dateNow

# Copy the logs to the user drive.
$sourcePath = "User:\{0}" -f $logs
$destinationPackage = Join-Path -Path (Convert-Path '.') -ChildPath $logs
Copy-Item -FromSession $session -Path $sourcePath -Destination $destinationPackage

# Cleanup the logs.
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove-AzsDBAdapterLog}
# Close the session.
$session | Remove-PSSession

```

## <a name="next-steps"></a>Další postup

[Odebrat poskytovatele prostředků MySQL](azure-stack-mysql-resource-provider-remove.md)
