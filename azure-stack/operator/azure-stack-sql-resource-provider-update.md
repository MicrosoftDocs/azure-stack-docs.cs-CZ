---
title: Aktualizace poskytovatele prostředků SQL centra Azure Stack
titleSuffix: Azure Stack Hub
description: Naučte se aktualizovat poskytovatele prostředků SQL centra Azure Stack.
author: bryanla
ms.topic: article
ms.date: 8/19/2020
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 60d9ce421ce4cdede89dd9f0fa9ff4ee4746d039
ms.sourcegitcommit: 69cfff119ab425d0fbb71e38d1480d051fc91216
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91572853"
---
# <a name="update-the-sql-resource-provider"></a>Aktualizace poskytovatele prostředků SQL

> [!IMPORTANT]
> Před aktualizací poskytovatele prostředků si přečtěte poznámky k verzi, kde najdete informace o nových funkcích, opravách a známých problémech, které by mohly mít vliv na nasazení. Zpráva k vydání verze také určuje minimální verzi centra Azure Stack požadovaná pro poskytovatele prostředků.

Nový poskytovatel prostředků SQL může být vydaný, když se Azure Stack centrum aktualizuje na nové sestavení. I když stávající poskytovatel prostředků i nadále funguje, doporučujeme aktualizovat na nejnovější sestavení co nejdříve.

|Podporovaná verze centra Azure Stack|Verze SQL RP|Windows Server, na kterém běží služba RP
  |-----|-----|-----|
  |2005|[SQL RP verze 1.1.93.0](https://aka.ms/azshsqlrp11930)|POUZE interní doplněk Microsoft AzureStack RP – Windows Server
  |2005, 2002, 1910|[SQL RP verze 1.1.47.0](https://aka.ms/azurestacksqlrp11470)|Windows Server 2016 Datacenter – jádro serveru|
  |1908|[SQL RP verze 1.1.33.0](https://aka.ms/azurestacksqlrp11330)|Windows Server 2016 Datacenter – jádro serveru|
  |     |     |     |

Aktualizace poskytovatele prostředků SQL je kumulativní. Při aktualizaci ze starší verze můžete přímo aktualizovat na nejnovější verzi. 

Chcete-li aktualizovat poskytovatele prostředků, použijte skript **UpdateSQLProvider.ps1** . Použijte účet služby s právy místního správce a je **vlastníkem** předplatného. Tento skript aktualizace je součástí stažení poskytovatele prostředků. 

Proces aktualizace je podobný procesu použitému k [nasazení poskytovatele prostředků](./azure-stack-sql-resource-provider-deploy.md). Skript aktualizace používá stejné argumenty jako skript DeploySqlProvider.ps1 a budete muset zadat informace o certifikátu.

## <a name="update-script-processes"></a>Aktualizovat procesy skriptu

Skript **UpdateSQLProvider.ps1** vytvoří nový virtuální počítač s nejnovější imagí operačního systému, nasadí nejnovější kód poskytovatele prostředků a migruje nastavení z původního poskytovatele prostředků na nového poskytovatele prostředků. 

> [!NOTE]
>Doporučujeme, abyste si stáhli nejnovější bitovou kopii Windows serveru 2016 Core Image nebo Windows serveru Microsoft AzureStack z webu Marketplace Management. Pokud potřebujete nainstalovat aktualizaci, můžete do cesty místní závislosti umístit **jeden** balíček MSU. Pokud je v tomto umístění více než jeden soubor MSU, skript se nezdaří.

Když skript *UpdateSQLProvider.ps1* vytvoří nový virtuální počítač, skript migruje následující nastavení z původního virtuálního počítače poskytovatele prostředků:

* informace o databázi
* informace o hostitelském serveru
* požadovaný záznam DNS

## <a name="update-script-parameters"></a>Aktualizovat parametry skriptu

Při spuštění skriptu **UpdateSQLProvider.ps1** PowerShellu můžete zadat následující parametry z příkazového řádku. Pokud ne, nebo pokud se nějaké ověření parametru nepodaří, budete vyzváni k zadání požadovaných parametrů.

| Název parametru | Description | Komentář nebo výchozí hodnota |
| --- | --- | --- |
| **CloudAdminCredential** | Přihlašovací údaje pro správce cloudu, které jsou nezbytné pro přístup k privilegovanému koncovému bodu. | _Požadováno_ |
| **AzCredential** | Přihlašovací údaje pro účet správce služby Azure Stack hub. Použijte stejné přihlašovací údaje, které jste použili k nasazení centra Azure Stack. | _Požadováno_ |
| **VMLocalCredential** | Přihlašovací údaje pro účet místního správce virtuálního počítače poskytovatele prostředků SQL. | _Požadováno_ |
| **PrivilegedEndpoint** | IP adresa nebo název DNS privilegovaného koncového bodu. |  _Požadováno_ |
| **AzureEnvironment** | Prostředí Azure účtu správce služby, které jste použili k nasazení centra Azure Stack. Vyžaduje se jenom pro nasazení Azure AD. Podporované názvy prostředí jsou **AzureCloud**, **AzureUSGovernment**nebo, pokud používáte Čína Azure AD **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | Do tohoto adresáře musíte taky vložit soubor Certificate. pfx. | _Volitelné pro jeden uzel, ale je povinný pro více uzlů_ |
| **DefaultSSLCertificatePassword** | Heslo pro certifikát. pfx. | _Požadováno_ |
| **MaxRetryCount** | Počet pokusů o opakování všech operací, pokud dojde k selhání.| 2 |
| **RetryDuration** |Interval časového limitu mezi opakovanými pokusy (v sekundách). | 120 |
| **Odinstalace** | Odebere poskytovatele prostředků a všechny přidružené prostředky. | Ne |
| **DebugMode** | Zabraňuje automatickému vyčištění při selhání. | Ne |

## <a name="update-script-powershell-example"></a>Příklad aktualizace skriptu PowerShellu

Pokud aktualizujete verzi poskytovatele prostředků SQL na 1.1.33.0 nebo předchozí verze, budete muset v PowerShellu nainstalovat konkrétní verze modulů AzureRm. zaváděcího nástroje a Azure Stack hub. 

Pokud aktualizujete poskytovatele prostředků SQL na verzi 1.1.47.0 nebo novější, můžete tento krok přeskočit. Skript nasazení automaticky stáhne a nainstaluje potřebné moduly PowerShellu pro vás do cesty C:\Program Files\SqlMySqlPsh.

>[!NOTE]
>Pokud složka C:\Program Files\SqlMySqlPsh už existuje se staženým modulem PowerShellu, doporučujeme před spuštěním skriptu aktualizace vyčistit tuto složku. Tím se zajistí stažení a použití správné verze modulu PowerShellu.

```powershell
# Run the following scripts when updating to version 1.1.33.0 only.
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureStack module.
# Note that this might not be the most currently available version of Azure Stack Hub PowerShell.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0
```

> [!NOTE]
> V odpojeném scénáři je nutné stáhnout požadované moduly prostředí PowerShell a zaregistrovat úložiště ručně v rámci požadavků. Další informace můžete získat v [nasazení poskytovatele prostředků SQL](azure-stack-sql-resource-provider-deploy.md) .

Následuje příklad použití skriptu *UpdateSQLProvider.ps1* , který můžete spustit z konzoly PowerShellu se zvýšenými oprávněními. Nezapomeňte změnit informace o proměnné a hesla podle potřeby:  

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

# For version 1.1.47.0 or later, the PowerShell modules used by the RP deployment are placed in C:\Program Files\SqlMySqlPsh
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
