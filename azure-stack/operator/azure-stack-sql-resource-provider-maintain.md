---
title: Údržba poskytovatele prostředků SQL na Azure Stack | Microsoft Docs
description: Přečtěte si, jak můžete spravovat službu poskytovatele prostředků SQL na Azure Stack.
services: azure-stack
documentationCenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/06/2019
ms.author: mabrigg
ms.reviewer: jiahan
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: 1fa83cbbdba011646016e1e0b0fd5bef3b8d0baf
ms.sourcegitcommit: a8379358f11db1e1097709817d21ded0231503eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2019
ms.locfileid: "70377281"
---
# <a name="sql-resource-provider-maintenance-operations"></a>Operace údržby poskytovatele prostředků SQL

Poskytovatel prostředků SQL běží na uzamčeném virtuálním počítači. Chcete-li povolit operace údržby, je nutné aktualizovat zabezpečení virtuálního počítače. Pokud to chcete provést pomocí objektu zabezpečení s nejnižšími oprávněními, můžete použít PowerShell, který je [dostatečný pro správu (JEA)](https://docs.microsoft.com/powershell/scripting/learn/remoting/jea/overview) koncového bodu *DBAdapterMaintenance*. Instalační balíček poskytovatele prostředků obsahuje skript pro tuto operaci.

## <a name="patching-and-updating"></a>Oprava a aktualizace

Poskytovatel prostředků SQL není obsluhovaná jako součást Azure Stack, protože se jedná o doplňkovou komponentu. Společnost Microsoft v případě potřeby poskytuje aktualizace poskytovateli prostředků SQL. Po vydání aktualizovaného adaptéru SQL je k dispozici skript pro instalaci aktualizace. Tento skript vytvoří nový virtuální počítač poskytovatele prostředků a migruje do nového virtuálního počítače stav starého virtuálního počítače poskytovatele. Další informace najdete v tématu [aktualizace poskytovatele prostředků SQL](azure-stack-sql-resource-provider-update.md).

### <a name="provider-virtual-machine"></a>Virtuální počítač poskytovatele

Vzhledem k tomu, že poskytovatel prostředků běží na virtuálním počítači *uživatele* , musíte při jejich vydávání použít požadované opravy a aktualizace. Balíčky Windows Update, které jsou k dispozici jako součást cyklu patch-and-Update, můžete použít k instalaci aktualizací do virtuálního počítače.

## <a name="updating-sql-credentials"></a>Aktualizují se přihlašovací údaje SQL.

Zodpovídáte za vytváření a udržování účtů sysadmin na vašich SQL serverech. Poskytovatel prostředků potřebuje účet s těmito oprávněními ke správě databází pro uživatele, ale nepotřebuje přístup k datům uživatelů. Pokud potřebujete aktualizovat hesla sysadmin na serverech SQL, můžete použít rozhraní Správce poskytovatele prostředků ke změně uloženého hesla. Tato hesla se ukládají do Key Vault vaší instance Azure Stack.

Chcete-li upravit nastavení, vyberte **Procházet** &gt; **prostředky** &gt; pro správu **přihlašovacích** **serverů SQL hostující servery** &gt; a vyberte uživatelské jméno. Tato změna se musí provést nejdříve v instanci SQL (a v případě potřeby všechny repliky). V části **Nastavení**vyberte **heslo**.

![Aktualizace hesla správce](./media/azure-stack-sql-rp-deploy/sqlrp-update-password.PNG)

## <a name="secrets-rotation"></a>Rotace tajných kódů

*Tyto pokyny platí jenom pro Azure Stack integrované systémy.*

Pokud používáte poskytovatele prostředků SQL a MySQL s Azure Stack integrovanými systémy, zodpovídá Azure Stack operátor za rotaci následujících tajných kódů infrastruktury poskytovatele prostředků, aby se zajistilo, že jejich platnost nevyprší:

- [Při nasazení se zadal](azure-stack-pki-certs.md)externí certifikát SSL.
- Heslo účtu místního správce virtuálního počítače poskytovatele prostředků zadané během nasazování.
- Heslo pro uživatele diagnostiky poskytovatele prostředků (dbadapterdiag).

### <a name="powershell-examples-for-rotating-secrets"></a>Příklady prostředí PowerShell pro rotující tajné klíče

**Změňte všechna tajná klíčová okna současně.**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DiagnosticsUserPassword $passwd `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd  `
    -VMLocalCredential $localCreds
```

**Změňte heslo pro uživatele diagnostiky.**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DiagnosticsUserPassword  $passwd
```

**Změňte heslo účtu místního správce virtuálního počítače.**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -VMLocalCredential $localCreds
```

**Změňte heslo certifikátu SSL.**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd
```

### <a name="secretrotationsqlproviderps1-parameters"></a>SecretRotationSQLProvider. ps1 – parametry

|Parametr|Popis|
|-----|-----|
|AzCredential|Azure Stack přihlašovací údaje účtu správce služby.|
|CloudAdminCredential|Azure Stack přihlašovací údaje účtu domény správce cloudu.|
|PrivilegedEndpoint|Privilegovaný koncový bod pro přístup k Get-AzureStackStampInformation.|
|DiagnosticsUserPassword|Heslo uživatelského účtu diagnostiky|
|VMLocalCredential|Účet místního správce na virtuálním počítači s MySQLAdapter.|
|DefaultSSLCertificatePassword|Výchozí heslo certifikátu SSL (* PFX)|
|DependencyFilesLocalPath|Místní cesta souborů závislosti|
|     |     |

### <a name="known-issues"></a>Známé problémy

**Problém**: Protokoly rotace tajných kódů.<br>
Protokoly pro rotaci tajných kódů nejsou automaticky shromažďovány, pokud při spuštění dojde k chybě vlastního skriptu při střídání tajných klíčů.

**Alternativní řešení**:<br>
Pomocí rutiny Get-AzsDBAdapterLogs Shromážděte všechny protokoly poskytovatele prostředků, včetně AzureStack. DatabaseAdapter. SecretRotation. ps1 _*. log uloženého v C:\Logs.

## <a name="update-the-virtual-machine-operating-system"></a>Aktualizace operačního systému virtuálního počítače

K aktualizaci operačního systému virtuálního počítače použijte jednu z následujících metod.

- Nainstalujte nejnovější balíček poskytovatele prostředků pomocí aktuálně opravené image Windows Server 2016 Core.
- Instalace balíčku web Windows Update během instalace nástroje nebo aktualizace na poskytovatele prostředků.

## <a name="update-the-virtual-machine-windows-defender-definitions"></a>Aktualizace definicí programu Windows Defender pro virtuální počítače

Aktualizace definic programu Windows Defender:

1. Stáhněte aktualizaci definic programu Windows Defender z [definice programu Windows Defender](https://www.microsoft.com/en-us/wdsi/definitions).

   Na stránce aktualizace definic se posuňte dolů na možnost ručně stahovat a instalovat definice. Stáhněte si "Windows Defender AntiVirus for Windows 10 a Windows 8.1" 64-bit File.

   Případně můžete použít [tento přímý odkaz](https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64) ke stažení nebo spuštění souboru fpam-FE. exe.

2. Vytvořte relaci PowerShellu pro koncový bod údržby virtuálního počítače adaptéru poskytovatele prostředků SQL.

3. Zkopírujte soubor aktualizace definic do virtuálního počítače pomocí relace koncového bodu údržby.

4. V relaci PowerShellu údržby spusťte příkaz *Update-DBAdapterWindowsDefenderDefinitions* .

5. Po instalaci definic doporučujeme odstranit soubor aktualizace definic pomocí příkazu *Remove-ItemOnUserDrive* .

**Příklad skriptu PowerShellu pro aktualizaci definic.**

Chcete-li aktualizovat definice Defenderu, můžete upravit a spustit následující skript. Nahraďte hodnoty ve skriptu hodnotami z vašeho prostředí.

```powershell
# Set credentials for local admin on the resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "<local admin user password>" -AsPlainText -Force
$vmLocalAdminUser = "<local admin user name>"
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential `
    ($vmLocalAdminUser, $vmLocalAdminPass)

# Provide the public IP address for the adapter VM.
$databaseRPMachine  = "<RP VM IP address>"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"

# Download the Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions.
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

## <a name="collect-diagnostic-logs"></a>Shromažďovat diagnostické protokoly

Pokud chcete shromažďovat protokoly z uzamčeného virtuálního počítače, můžete použít PowerShell, který je stačí pro JEA ( *DBAdapterDiagnostics*) koncového bodu. Tento koncový bod nabízí následující příkazy:

- **Get-AzsDBAdapterLog**. Tento příkaz vytvoří balíček zip pro diagnostické protokoly poskytovatele prostředků a uloží soubor na jednotku uživatele relace. Tento příkaz můžete spustit bez parametrů a budou se shromažďovat poslední čtyři hodiny protokolů.
- **Remove-AzsDBAdapterLog**. Tento příkaz odebere existující balíčky protokolů na virtuálním počítači poskytovatele prostředků.

### <a name="endpoint-requirements-and-process"></a>Požadavky a procesy koncového bodu

Když se nainstaluje nebo aktualizuje poskytovatel prostředků, vytvoří se uživatelský účet **dbadapterdiag** . Tento účet použijete ke shromáždění diagnostických protokolů.

>[!NOTE]
>Heslo účtu dbadapterdiag je stejné jako heslo použité pro místního správce na virtuálním počítači, který je vytvořený během nasazení nebo aktualizace zprostředkovatele.

Pokud chcete použít příkazy *DBAdapterDiagnostics* , vytvořte vzdálenou relaci PowerShellu k virtuálnímu počítači poskytovatele prostředků a spusťte příkaz **Get-AzsDBAdapterLog** .

Časové rozpětí pro shromažďování protokolů můžete nastavit **pomocí parametrů** **FromDate** a na více dní. Pokud nezadáte jeden nebo oba parametry, použijí se následující výchozí hodnoty:

- FromDate je čtyři hodiny před aktuálním časem.
- Aktuální čas je v aktuálním čase.

**Příklad skriptu PowerShellu pro shromažďování protokolů.**

Následující skript ukazuje, jak shromažďovat diagnostické protokoly z virtuálního počítače poskytovatele prostředků.

```powershell
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP address>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<Enter Diagnostic password>'

$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, (ConvertTo-SecureString -String $diagnosticsUserPassword -AsPlainText -Force))
$session = New-PSSession -ComputerName $databaseRPMachineIP -Credential $diagCreds `
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
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove- AzsDBAdapterLog }
# Close the session.
$session | Remove-PSSession
```

## <a name="next-steps"></a>Další kroky

[Přidání SQL Server hostitelských serverů](azure-stack-sql-resource-provider-hosting-servers.md)
