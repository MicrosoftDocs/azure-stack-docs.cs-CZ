---
title: Operace údržby poskytovatele prostředků MySQL – centrum Azure Stack
description: Naučte se udržovat službu poskytovatele prostředků MySQL v Azure Stack hub.
author: bryanla
ms.topic: article
ms.date: 9/22/2020
ms.author: bryanla
ms.reviewer: jiahan
ms.lastreviewed: 01/11/2020
ms.openlocfilehash: 593ea0ca882b5aa7d57ff5a3267d35519be3b6f2
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "101840061"
---
# <a name="mysql-resource-provider-maintenance-operations-in-azure-stack-hub"></a>Operace údržby poskytovatele prostředků MySQL v centru Azure Stack

Poskytovatel prostředků MySQL běží na uzamčeném virtuálním počítači (VM). Chcete-li povolit operace údržby, je nutné aktualizovat zabezpečení virtuálního počítače. Pokud to chcete provést pomocí principu minimálního oprávnění (POLP), můžete použít PowerShell, který je dostatečně dostupný jako koncový bod pro správu JEA (). Instalační balíček poskytovatele prostředků obsahuje skript pro tuto operaci.

## <a name="update-the-vm-operating-system"></a>Aktualizace operačního systému virtuálního počítače

Vzhledem k tomu, že poskytovatel prostředků běží na *uživatelském* virtuálním počítači, musíte při jejich vydávání použít požadované opravy a aktualizace. Balíčky Windows Update, které jsou k dispozici jako součást cyklu patch-and-Update, můžete použít k instalaci aktualizací do virtuálního počítače.

Aktualizujte virtuální počítač poskytovatele pomocí jedné z následujících metod:

- Nainstalujte nejnovější balíček poskytovatele prostředků pomocí aktuálně opravené image virtuálního počítače.
- Během instalace nebo aktualizace poskytovatele prostředků nainstalujte balíček web Windows Update.

## <a name="update-the-vm-windows-defender-definitions"></a>Aktualizace definicí virtuálních počítačů v programu Windows Defender

Chcete-li aktualizovat definice Defenderu, postupujte takto:

1. Stáhněte aktualizaci definic programu Windows Defender z [definice programu Windows Defender](https://www.microsoft.com/en-us/wdsi/definitions).

    Na stránce definice přejděte dolů na možnost ručně stahovat a instalovat definice. Stáhněte si "Windows Defender AntiVirus for Windows 10 a Windows 8.1" 64-bit File.

    Případně můžete pomocí [tohoto přímého odkazu](https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64) stáhnout nebo spustit soubor fpam-fe.exe.

2. Otevřete relaci PowerShellu pro koncový bod údržby virtuálního počítače adaptéru poskytovatele prostředků MySQL.

3. Zkopírujte soubor aktualizací definic do virtuálního počítače adaptéru poskytovatele prostředků pomocí relace koncového bodu údržby.

4. V relaci PowerShellu údržby spusťte příkaz _Update-DBAdapterWindowsDefenderDefinitions_ .

5. Po instalaci definic doporučujeme odstranit soubor aktualizace definic pomocí příkazu _Remove-ItemOnUserDrive)_ .

**Příklad skriptu PowerShellu pro aktualizaci definic.**

Chcete-li aktualizovat definice Defenderu, můžete upravit a spustit následující skript. Nahraďte hodnoty ve skriptu hodnotami z vašeho prostředí.

```powershell
# Set credentials for the local admin on the resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString '<local admin user password>' -AsPlainText -Force
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

# Copy the defender update file to the adapter VM.
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

## <a name="secrets-rotation"></a>Rotace tajných kódů

*Tyto pokyny platí pouze pro integrované systémy Azure Stack hub.*

Pokud používáte poskytovatele prostředků SQL a MySQL s integrovanými systémy Azure Stack hub, zodpovídá operátor centra Azure Stack za účelem střídání následujících tajných kódů infrastruktury poskytovatele prostředků, aby se zajistilo, že nevyprší jejich platnost:

- [Při nasazení se zadal](azure-stack-pki-certs.md)externí certifikát SSL.
- Heslo účtu místního správce virtuálního počítače poskytovatele prostředků zadané během nasazování.
- Heslo pro uživatele diagnostiky poskytovatele prostředků (dbadapterdiag).
- (verze >= 1.1.47.0) Key Vault certifikát vygenerovaný během nasazení.

### <a name="powershell-examples-for-rotating-secrets"></a>Příklady prostředí PowerShell pro rotující tajné klíče

**Změnit všechny tajné klíče ve stejnou dobu:**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DiagnosticsUserPassword $passwd `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd `  
    -VMLocalCredential $localCreds `
    -KeyVaultPfxPassword $keyvaultCertPasswd
```

**Změna hesla diagnostiky uživatele:**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DiagnosticsUserPassword  $passwd
```

**Změňte heslo účtu místního správce virtuálního počítače:**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -VMLocalCredential $localCreds
```

**Změňte heslo certifikátu SSL:**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd
```

**Změňte heslo Key Vault certifikátu:**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -KeyVaultPfxPassword $keyvaultCertPasswd
```

### <a name="secretrotationmysqlproviderps1-parameters"></a>Parametry SecretRotationMySQLProvider.ps1

|Parametr|Popis|Komentář|
|-----|-----|-----|
|AzureEnvironment|Prostředí Azure účtu správce služby používaného pro nasazení centra Azure Stack. Vyžaduje se jenom pro nasazení Azure AD. Podporované názvy prostředí jsou **AzureCloud**, **AzureUSGovernment** nebo, pokud používáte Čína Azure Active Directory **AzureChinaCloud**.|Volitelné|
|AzCredential|Přihlašovací údaje účtu správce služby Azure Stack hub. Pokud účet, který používáte se službou AzCredential, vyžaduje vícefaktorové ověřování (MFA), skript se nezdaří.|Povinné|
|CloudAdminCredential|Přihlašovací údaje účtu domény správce cloudu Azure Stack hub.|Povinné|
|PrivilegedEndpoint|Privilegovaný koncový bod pro přístup k Get-AzureStackStampInformation.|Povinné|Volitelné|
|DiagnosticsUserPassword|Heslo uživatelského účtu diagnostiky|Volitelné|
|VMLocalCredential|Účet místního správce na virtuálním počítači s MySQLAdapter.|Volitelné|
|DefaultSSLCertificatePassword|Výchozí heslo certifikátu SSL (*. pfx)|Volitelné|
|DependencyFilesLocalPath|Místní cesta souborů závislosti|Volitelné|
|KeyVaultPfxPassword|Heslo použité k vygenerování certifikátu Key Vault pro databázový adaptér.|Volitelné|
|     |     |     |

### <a name="known-issues"></a>Známé problémy

**Problém:**<br>
Protokoly pro rotaci tajných klíčů nejsou automaticky shromažďovány, pokud při spuštění dojde k chybě skriptu pro otočení tajného klíče.

**Alternativní řešení:**<br>
Pomocí rutiny Get-AzsDBAdapterLogs Shromážděte všechny protokoly poskytovatele prostředků, včetně AzureStack.DatabaseAdapter.SecretRotation.ps1_ *. log, uložené v C:\Logs..

## <a name="collect-diagnostic-logs"></a>Shromážděte diagnostické protokoly.

::: moniker range=">= azs-2008"

Centrum Azure Stack má několik způsobů, jak shromažďovat, ukládat a odesílat diagnostické protokoly do podpora Microsoftu. Od verze 1.1.93 poskytovatel prostředků MySQL podporuje standardní způsob shromažďování protokolů z vašeho prostředí Azure Stack hub. Další informace najdete v tématu [shromažďování diagnostických protokolů](diagnostic-log-collection.md).

::: moniker-end

::: moniker range="< azs-2008"

Od verze 1.1.93 poskytovatel prostředků MySQL podporuje standardní způsob shromažďování protokolů z vašeho prostředí Azure Stack hub. Pokud používáte starší verzi, doporučujeme, abyste poskytovatele prostředků MySQL aktualizovali na nejnovější verzi.

Pokud chcete shromažďovat protokoly z uzamčeného virtuálního počítače, použijte PowerShellový koncový bod pro správu JEA (DBAdapterDiagnostics). Tento koncový bod nabízí následující příkazy:

- **Get-AzsDBAdapterLog**. Tento příkaz vytvoří balíček zip pro diagnostické protokoly poskytovatele prostředků a uloží soubor na jednotku uživatele relace. Tento příkaz můžete spustit bez parametrů a budou se shromažďovat poslední čtyři hodiny protokolů.

- **Remove-AzsDBAdapterLog**. Tento příkaz odebere existující balíčky protokolů na virtuálním počítači poskytovatele prostředků.

### <a name="endpoint-requirements-and-process"></a>Požadavky a procesy koncového bodu

Když se nainstaluje nebo aktualizuje poskytovatel prostředků, vytvoří se uživatelský účet dbadapterdiag. Tento účet použijete ke shromáždění diagnostických protokolů.

>[!NOTE]
>Heslo účtu dbadapterdiag je stejné jako heslo používané pro místního správce na virtuálním počítači, který se vytvořil během nasazení nebo aktualizace zprostředkovatele.

Pokud chcete použít příkazy _DBAdapterDiagnostics_ , vytvořte na virtuálním počítači poskytovatele prostředků vzdálenou relaci PowerShellu a spusťte příkaz **Get-AzsDBAdapterLog** .

Časové rozpětí pro shromažďování protokolů můžete nastavit **pomocí parametrů** **FromDate** a na více dní. Pokud nezadáte jeden nebo oba parametry, použijí se následující výchozí hodnoty:

* FromDate je čtyři hodiny před aktuálním časem.
* Aktuální čas je v aktuálním čase.

**Příklad skriptu PowerShellu pro shromažďování protokolů:**

Následující skript ukazuje, jak shromažďovat diagnostické protokoly z virtuálního počítače poskytovatele prostředků.

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

::: moniker-end

## <a name="configure-azure-diagnostics-extension-for-mysql-resource-provider"></a>Konfigurace rozšíření Azure Diagnostics pro poskytovatele prostředků MySQL

Ve výchozím nastavení je na virtuálním počítači adaptéru poskytovatele prostředků MySQL nainstalováno rozšíření Azure Diagnostics. Následující kroky ukazují, jak přizpůsobit rozšíření pro shromáždění protokolů provozní události poskytovatele prostředků MySQL a protokolů IIS pro účely řešení potíží a auditování.

1. Přihlaste se k portálu pro správu služby Azure Stack Hub.

2. V levém podokně vyberte **virtuální počítače** , vyhledejte virtuální počítač adaptéru poskytovatele prostředků MySQL a vyberte virtuální počítač.

3. V **nastavení diagnostiky** virtuálního počítače otevřete kartu **protokoly** a výběrem možnosti **vlastní** Přizpůsobte shromážděné protokoly událostí.
   
   ![Přejít na nastavení diagnostiky](media/azure-stack-mysql-resource-provider-maintain/mysqlrp-diagnostics-settings.png)

4. Přidejte **Microsoft-AzureStack-DatabaseAdapter/Operational \* !** pro shromáždění protokolů provozních událostí poskytovatele prostředků MySQL.

   ![Přidat protokoly událostí](media/azure-stack-mysql-resource-provider-maintain/mysqlrp-event-logs.png)

5. Chcete-li povolit shromažďování protokolů služby IIS, zaškrtněte **protokoly služby IIS** a **protokoly neúspěšných požadavků**.

   ![Přidat protokoly IIS](media/azure-stack-mysql-resource-provider-maintain/mysqlrp-iis-logs.png)

6. Nakonec výběrem možnosti **Uložit** uložte všechna nastavení diagnostiky.

Jakmile jsou protokoly událostí a kolekce protokolů služby IIS nakonfigurované pro poskytovatele prostředků MySQL, můžete protokoly najít v účtu úložiště systému s názvem **mysqladapterdiagaccount**.

Další informace o rozšíření Azure Diagnostics najdete v tématu [co je to Azure Diagnostics rozšíření](/azure/azure-monitor/platform/diagnostics-extension-overview).

## <a name="next-steps"></a>Další kroky

[Odebrání poskytovatele prostředků MySQL](azure-stack-mysql-resource-provider-remove.md)
