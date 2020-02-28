---
title: Aktualizace poskytovatele prostředků SQL centra Azure Stack
titleSuffix: Azure Stack Hub
description: Naučte se aktualizovat poskytovatele prostředků SQL centra Azure Stack.
author: bryanla
ms.topic: article
ms.date: 11/11/2019
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 43099dfb4bfb2ffe50e6c8ccdfc05633ff4f3a6e
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77697157"
---
# <a name="update-the-sql-resource-provider"></a>Aktualizovat poskytovatele prostředků SQL

Nový poskytovatel prostředků SQL může být vydaný, když se Azure Stack centrum aktualizuje na nové sestavení. I když stávající poskytovatel prostředků i nadále funguje, doporučujeme aktualizovat na nejnovější sestavení co nejdříve.

Od verze 1.1.33.0 verze poskytovatele prostředků SQL jsou aktualizace kumulativní a nemusíte je instalovat v pořadí, ve kterém byly vydané, pokud začínáte z verze 1.1.24.0 nebo novější. Například pokud používáte 1.1.24.0 verze poskytovatele prostředků SQL, můžete upgradovat na verzi 1.1.33.0 nebo novější, aniž byste museli nejdřív nainstalovat verzi 1.1.30.0. Pokud chcete zkontrolovat dostupné verze poskytovatele prostředků a verzi centra Azure Stack, na které jsou podporované, přečtěte si téma seznam verzí v tématu [nasazení požadavků poskytovatele prostředků](./azure-stack-sql-resource-provider-deploy.md#prerequisites).

Chcete-li aktualizovat poskytovatele prostředků, použijte skript *UpdateSQLProvider. ps1* . Použijte účet služby s právy místního správce a je **vlastníkem** předplatného. Tento skript je součástí stažení nového poskytovatele prostředků SQL. Proces aktualizace je podobný procesu použitému k [nasazení poskytovatele prostředků](./azure-stack-sql-resource-provider-deploy.md). Skript aktualizace používá stejné argumenty jako skript DeploySqlProvider. ps1 a budete muset zadat informace o certifikátu.

 > [!IMPORTANT]
 > Před upgradem poskytovatele prostředků si přečtěte poznámky k verzi, kde najdete informace o nových funkcích, opravách a známých problémech, které by mohly mít vliv na nasazení.

## <a name="update-script-processes"></a>Aktualizovat procesy skriptu

Skript *UpdateSQLProvider. ps1* vytvoří nový virtuální počítač (VM) s nejnovějším kódem poskytovatele prostředků.

> [!NOTE]
> Doporučujeme stáhnout si nejnovější image Windows serveru 2016 Core ze správy Marketplace. Pokud potřebujete nainstalovat aktualizaci, můžete do cesty místní závislosti umístit **jeden** balíček MSU. Pokud je v tomto umístění více než jeden soubor MSU, skript se nezdaří.

Po vytvoření nového virtuálního počítače pomocí skriptu *UpdateSQLProvider. ps1* migruje skript následující nastavení z původního virtuálního počítače poskytovatele:

* informace o databázi
* informace o hostitelském serveru
* požadovaný záznam DNS

## <a name="update-script-parameters"></a>Aktualizovat parametry skriptu

Když spustíte skript prostředí PowerShell **UpdateSQLProvider. ps1** , můžete zadat následující parametry z příkazového řádku. Pokud ne, nebo pokud se nějaké ověření parametru nepodaří, budete vyzváni k zadání požadovaných parametrů.

| Název parametru | Popis | Komentář nebo výchozí hodnota |
| --- | --- | --- |
| **CloudAdminCredential** | Přihlašovací údaje pro správce cloudu, které jsou nezbytné pro přístup k privilegovanému koncovému bodu. | _Požadovanou_ |
| **AzCredential** | Přihlašovací údaje pro účet správce služby Azure Stack hub. Použijte stejné přihlašovací údaje, které jste použili k nasazení centra Azure Stack. | _Požadovanou_ |
| **VMLocalCredential** | Přihlašovací údaje pro účet místního správce virtuálního počítače poskytovatele prostředků SQL. | _Požadovanou_ |
| **PrivilegedEndpoint** | IP adresa nebo název DNS privilegovaného koncového bodu. |  _Požadovanou_ |
| **AzureEnvironment** | Prostředí Azure účtu správce služby, které jste použili k nasazení centra Azure Stack. Vyžaduje se jenom pro nasazení Azure AD. Podporované názvy prostředí jsou **AzureCloud**, **AzureUSGovernment**nebo, pokud používáte Čína Azure AD **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | Do tohoto adresáře musíte taky vložit soubor Certificate. pfx. | _Volitelné pro jeden uzel, ale je povinný pro více uzlů_ |
| **DefaultSSLCertificatePassword** | Heslo pro certifikát. pfx. | _Požadovanou_ |
| **MaxRetryCount** | Počet pokusů o opakování všech operací, pokud dojde k selhání.| 2 |
| **RetryDuration** |Interval časového limitu mezi opakovanými pokusy (v sekundách). | 120 |
| **Odinstalace** | Odebere poskytovatele prostředků a všechny přidružené prostředky. | Ne |
| **DebugMode** | Zabraňuje automatickému vyčištění při selhání. | Ne |

## <a name="update-script-powershell-example"></a>Příklad aktualizace skriptu PowerShellu
> [!NOTE]
> Tento proces aktualizace platí jenom pro integrované systémy Azure Stack hub.

Pokud aktualizujete verzi poskytovatele prostředků SQL na 1.1.33.0 nebo předchozí verze, budete muset v PowerShellu nainstalovat konkrétní verze modulů AzureRm. zaváděcího nástroje a Azure Stack hub. Pokud aktualizujete na poskytovatele prostředků SQL verze 1.1.47.0, skript nasazení automaticky stáhne a nainstaluje potřebné moduly PowerShellu pro vás do cesty C:\Program Files\SqlMySqlPsh.

```powershell
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureStack module.
# Note that this might not be the most currently available version of Azure Stack Hub PowerShell.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0
```

> [!NOTE]
> V odpojeném scénáři je nutné stáhnout požadované moduly prostředí PowerShell a zaregistrovat úložiště ručně v rámci požadavků. Další informace můžete získat v [nasazení poskytovatele prostředků SQL](azure-stack-sql-resource-provider-deploy.md) .

Následuje příklad použití skriptu *UpdateSQLProvider. ps1* , který můžete spustit z konzoly PowerShell se zvýšenými oprávněními. Nezapomeňte změnit informace o proměnné a hesla podle potřeby:  

```powershell
# Use the NetBIOS name for the Azure Stack Hub domain. On the Azure Stack Hub SDK, the default is AzureStack but this might have been changed at installation.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS VMs.
$privilegedEndpoint = "AzS-ERCS01"

# Provide the Azure environment used for deploying Azure Stack Hub. Required only for Azure AD deployments. Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud, or AzureUSGovernment depending which Azure subscription you're using.
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (this can be Azure AD or AD FS).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# Add the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# For version 1.1.47.0, the PowerShell modules used by the RP deployment are placed in C:\Program Files\SqlMySqlPsh
# The deployment script adds this path to the system $env:PSModulePath to ensure correct modules are used.
$rpModulePath = Join-Path -Path $env:ProgramFiles -ChildPath 'SqlMySqlPsh'
$env:PSModulePath = $env:PSModulePath + ";" + $rpModulePath

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\UpdateSQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -AzureEnvironment $AzureEnvironment `
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert

 ```

Po dokončení skriptu aktualizace poskytovatele prostředků zavřete aktuální relaci PowerShellu.

## <a name="next-steps"></a>Další kroky

[Údržba poskytovatele prostředků SQL](azure-stack-sql-resource-provider-maintain.md)
