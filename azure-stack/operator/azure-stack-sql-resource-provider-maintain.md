---
title: Operace údržby poskytovatele prostředků SQL
titleSuffix: Azure Stack Hub
description: Přečtěte si o operacích údržby poskytovatele prostředků SQL na rozbočovači Azure Stack.
author: bryanla
ms.topic: article
ms.date: 9/22/2020
ms.author: bryanla
ms.reviewer: jiahan
ms.lastreviewed: 01/11/2020
ms.openlocfilehash: 212d6c89bedc6f46eb6db2cbf735fa61ea0ad909
ms.sourcegitcommit: 1465bca8b7f87ea6f24faf47e86c2ba497943b28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2021
ms.locfileid: "98103092"
---
# <a name="sql-resource-provider-maintenance-operations"></a>Operace údržby poskytovatele prostředků SQL

Poskytovatel prostředků SQL běží na uzamčeném virtuálním počítači (VM). Chcete-li povolit operace údržby, je nutné aktualizovat zabezpečení virtuálního počítače. Pokud to chcete provést pomocí objektu s nejnižšími oprávněními, použijte [prostředí PowerShell, které stačí pro správu DBAdapterMaintenance (JEA)](/powershell/scripting/learn/remoting/jea/overview) koncového bodu.  Instalační balíček poskytovatele prostředků obsahuje skript pro tuto akci.

## <a name="patching-and-updating"></a>Oprava a aktualizace

Poskytovatel prostředků SQL není součástí služby Azure Stack hub, protože se jedná o doplňkovou komponentu. Společnost Microsoft v případě potřeby poskytuje aktualizace poskytovateli prostředků SQL. Po vydání aktualizovaného adaptéru SQL je k dispozici skript pro instalaci aktualizace. Tento skript vytvoří nový virtuální počítač poskytovatele prostředků a migruje do nového virtuálního počítače stav starého virtuálního počítače poskytovatele. Další informace najdete v tématu [aktualizace poskytovatele prostředků SQL](azure-stack-sql-resource-provider-update.md).

### <a name="provider-vm"></a>Virtuální počítač poskytovatele

Vzhledem k tomu, že poskytovatel prostředků běží na *uživatelském* virtuálním počítači, musíte při jejich vydávání použít požadované opravy a aktualizace. Balíčky Windows Update, které jsou k dispozici jako součást cyklu patch-and-Update, použijte k instalaci aktualizací do virtuálního počítače.

## <a name="updating-sql-credentials"></a>Aktualizují se přihlašovací údaje SQL.

Zodpovídáte za vytváření a udržování účtů sysadmin na vašich SQL serverech. Poskytovatel prostředků potřebuje účet s těmito oprávněními ke správě databází pro uživatele, ale nepotřebuje přístup k datům uživatelů. Pokud potřebujete aktualizovat hesla sysadmin na serverech SQL, můžete použít rozhraní Správce poskytovatele prostředků ke změně uloženého hesla. Tato hesla se ukládají v Key Vault vaší instance centra Azure Stack.

Chcete-li upravit nastavení, vyberte **Procházet** &gt; **prostředky pro správu** &gt; přihlašovacích **serverů SQL hostující servery** &gt;  a vyberte uživatelské jméno. Tato změna se musí provést nejdříve v instanci SQL (a v případě potřeby všechny repliky). V části **Nastavení** vyberte **heslo**.

![Aktualizace hesla správce SQL](./media/azure-stack-sql-rp-deploy/sql-rp-update-password.png)

## <a name="secrets-rotation"></a>Rotace tajných kódů

*Tyto pokyny platí pouze pro integrované systémy Azure Stack hub.*

Pokud používáte poskytovatele prostředků SQL a MySQL s integrovanými systémy Azure Stack hub, zodpovídá operátor centra Azure Stack za účelem střídání následujících tajných kódů infrastruktury poskytovatele prostředků, aby se zajistilo, že nevyprší jejich platnost:

- [Při nasazení se zadal](azure-stack-pki-certs.md)externí certifikát SSL.
- Heslo účtu místního správce virtuálního počítače poskytovatele prostředků zadané během nasazování.
- Heslo pro uživatele diagnostiky poskytovatele prostředků (dbadapterdiag).
- (verze >= 1.1.47.0) Key Vault certifikát vygenerovaný během nasazení.

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
    -VMLocalCredential $localCreds `
    -KeyVaultPfxPassword $keyvaultCertPasswd
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

**Změňte heslo certifikátu Key Vault.**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -KeyVaultPfxPassword $keyvaultCertPasswd
```

### <a name="secretrotationsqlproviderps1-parameters"></a>Parametry SecretRotationSQLProvider.ps1

|Parametr|Popis|Komentář|
|-----|-----|-----|
|AzureEnvironment|Prostředí Azure účtu správce služby používaného pro nasazení centra Azure Stack. Vyžaduje se jenom pro nasazení Azure AD. Podporované názvy prostředí jsou **AzureCloud**, **AzureUSGovernment** nebo, pokud používáte Čína Azure Active Directory **AzureChinaCloud**.|Volitelné|
|AzCredential|Přihlašovací údaje účtu správce služby Azure Stack hub. Pokud účet, který používáte se službou AzCredential, vyžaduje vícefaktorové ověřování (MFA), skript se nezdaří.|Povinné|
|CloudAdminCredential|Přihlašovací údaje účtu domény správce cloudu Azure Stack hub.|Povinné|
|PrivilegedEndpoint|Privilegovaný koncový bod pro přístup k Get-AzureStackStampInformation.|Povinné|
|DiagnosticsUserPassword|Heslo uživatelského účtu diagnostiky|Volitelné|
|VMLocalCredential|Účet místního správce na virtuálním počítači s MySQLAdapter.|Volitelné|
|DefaultSSLCertificatePassword|Výchozí heslo certifikátu SSL (*. pfx)|Volitelné|
|DependencyFilesLocalPath|Místní cesta souborů závislosti|Volitelné|
|KeyVaultPfxPassword|Heslo použité k vygenerování certifikátu Key Vault pro databázový adaptér.|Volitelné|
|     |     |     |

### <a name="known-issues"></a>Známé problémy

**Problém**:<br>
Protokoly rotace tajných kódů. Protokoly pro rotaci tajných kódů nejsou shromažďovány automaticky, pokud se vlastní skript pro střídání tajných klíčů při spuštění nezdařil.

**Alternativní řešení**:<br>
Pomocí rutiny Get-AzsDBAdapterLogs Shromážděte všechny protokoly poskytovatele prostředků, včetně AzureStack.DatabaseAdapter.SecretRotation.ps1_ *. log, uložené v C:\Logs..

## <a name="update-the-vm-operating-system"></a>Aktualizace operačního systému virtuálního počítače

K aktualizaci operačního systému virtuálního počítače použijte jednu z následujících metod.

- Nainstalujte nejnovější balíček poskytovatele prostředků pomocí aktuálně opravené image virtuálního počítače.
- Instalace balíčku web Windows Update během instalace nástroje nebo aktualizace na poskytovatele prostředků.

## <a name="update-the-vm-windows-defender-definitions"></a>Aktualizace definicí virtuálních počítačů v programu Windows Defender

Aktualizace definic programu Windows Defender:

1. Stáhněte aktualizaci definic programu Windows Defender z části [aktualizace služby Security Intelligence pro program Windows Defender](https://www.microsoft.com/wdsi/definitions).

   Na stránce aktualizace definic se posuňte dolů k části Ruční stažení aktualizace. Stáhněte si "Windows Defender AntiVirus for Windows 10 a Windows 8.1" 64-bit File.

   [Tento přímý odkaz](https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64) můžete použít také ke stažení nebo spuštění souboru fpam-fe.exe.

2. Vytvořte relaci PowerShellu pro koncový bod údržby virtuálního počítače adaptéru poskytovatele prostředků SQL.

3. Zkopírujte soubor aktualizace definic do virtuálního počítače pomocí relace koncového bodu údržby.

4. V relaci PowerShellu údržby spusťte příkaz *Update-DBAdapterWindowsDefenderDefinitions* .

5. Po instalaci definic doporučujeme odstranit soubor aktualizací definic pomocí příkazu *Remove-ItemOnUserDrive* .

**Příklad skriptu PowerShellu pro aktualizaci definic**

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

# Download the Windows Defender update definitions file from https://www.microsoft.com/wdsi/definitions.
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

## <a name="collect-diagnostic-logs"></a>Shromážděte diagnostické protokoly.

::: moniker range=">= azs-2008"

Centrum Azure Stack má několik způsobů, jak shromažďovat, ukládat a odesílat diagnostické protokoly do podpora Microsoftu. Od verze 1.1.93 poskytovatel prostředků SQL podporuje standardní způsob shromažďování protokolů z vašeho prostředí Azure Stack hub. Další informace najdete v tématu [shromažďování diagnostických protokolů](diagnostic-log-collection.md).

::: moniker-end

::: moniker range="< azs-2008"

Od verze 1.1.93 poskytovatel prostředků SQL podporuje standardní způsob shromažďování protokolů z vašeho prostředí Azure Stack hub. Pokud používáte starší verzi, doporučuje se aktualizovat poskytovatele prostředků SQL na nejnovější verzi.

Pokud chcete shromažďovat protokoly z uzamčeného virtuálního počítače, použijte PowerShellový koncový bod pro správu JEA ( *DBAdapterDiagnostics*). Tento koncový bod nabízí následující příkazy:

- **Get-AzsDBAdapterLog**. Tento příkaz vytvoří balíček zip pro diagnostické protokoly poskytovatele prostředků a uloží soubor na jednotku uživatele relace. Tento příkaz můžete spustit bez parametrů a budou se shromažďovat poslední čtyři hodiny protokolů.
- **Remove-AzsDBAdapterLog**. Tento příkaz odebere existující balíčky protokolů na virtuálním počítači poskytovatele prostředků.

### <a name="endpoint-requirements-and-process"></a>Požadavky a procesy koncového bodu

Když se nainstaluje nebo aktualizuje poskytovatel prostředků, vytvoří se uživatelský účet **dbadapterdiag** . Tento účet použijete ke shromáždění diagnostických protokolů.

>[!NOTE]
>Heslo účtu dbadapterdiag je stejné jako heslo používané pro místního správce na virtuálním počítači, který se vytvořil během nasazení nebo aktualizace zprostředkovatele.

Pokud chcete použít příkazy *DBAdapterDiagnostics* , vytvořte na virtuálním počítači poskytovatele prostředků vzdálenou relaci PowerShellu a spusťte příkaz **Get-AzsDBAdapterLog** .

Časové rozpětí pro shromažďování protokolů můžete nastavit **pomocí parametrů** **FromDate** a na více dní. Pokud nezadáte jeden nebo oba parametry, použijí se následující výchozí hodnoty:

- FromDate je čtyři hodiny před aktuálním časem.
- Aktuální čas je v aktuálním čase.

**Příklad skriptu PowerShellu pro shromažďování protokolů**

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

# Clean up the logs.
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove-AzsDBAdapterLog}
# Close the session.
$session | Remove-PSSession
```

::: moniker-end

## <a name="configure-azure-diagnostics-extension-for-sql-resource-provider"></a>Konfigurace rozšíření Azure Diagnostics pro poskytovatele prostředků SQL
Ve výchozím nastavení je na virtuálním počítači adaptéru poskytovatele prostředků SQL nainstalované rozšíření Azure Diagnostics. Následující kroky ukazují, jak přizpůsobit rozšíření pro shromáždění protokolů provozní události poskytovatele prostředků SQL a protokolů IIS pro účely řešení potíží a auditování.

1. Přihlaste se k portálu pro správu služby Azure Stack Hub.

2. V levém podokně vyberte **virtuální počítače** , vyhledejte virtuální počítač adaptéru poskytovatele prostředků SQL a vyberte virtuální počítač.

3. V **nastavení diagnostiky** virtuálního počítače otevřete kartu **protokoly** a výběrem možnosti **vlastní** Přizpůsobte shromážděné protokoly událostí.
![Přejít na nastavení diagnostiky](media/azure-stack-sql-resource-provider-maintain/sqlrp-diagnostics-settings.png)

4. Přidejte **Microsoft-AzureStack-DatabaseAdapter/Operational \* !* _ shromažďovat protokoly provozních událostí poskytovatele prostředků SQL.
![Přidat protokoly událostí](media/azure-stack-sql-resource-provider-maintain/sqlrp-event-logs.png)

5. Pokud chcete povolit shromažďování protokolů IIS, zaškrtněte _ *IIS protokoly** a **protokoly chybných žádostí**.
![Přidat protokoly IIS](media/azure-stack-sql-resource-provider-maintain/sqlrp-iis-logs.png)

6. Nakonec výběrem možnosti **Uložit** uložte všechna nastavení diagnostiky.

Jakmile jsou protokoly událostí a kolekce protokolů služby IIS nakonfigurované pro poskytovatele prostředků SQL, můžete protokoly najít v účtu úložiště systému s názvem **sqladapterdiagaccount**.

Další informace o rozšíření Azure Diagnostics najdete v tématu [co je to Azure Diagnostics rozšíření](/azure/azure-monitor/platform/diagnostics-extension-overview).

## <a name="next-steps"></a>Další kroky

[Přidání SQL Server hostitelských serverů](azure-stack-sql-resource-provider-hosting-servers.md)
