---
title: Aktualizace poskytovatele prostředků MySQL v Azure Stack hub
description: Naučte se aktualizovat poskytovatele prostředků MySQL centra Azure Stack v centru Azure Stack.
author: bryanla
ms.topic: article
ms.date: 1/22/2020
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 01/11/2020
ms.openlocfilehash: a617efab5516fb2f6bad96e36c99f1b84852237c
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77698636"
---
# <a name="update-the-mysql-resource-provider-in-azure-stack-hub"></a>Aktualizace poskytovatele prostředků MySQL v Azure Stack hub

Při aktualizaci Azure Stackch sestavení centra se může uvolnit nový adaptér poskytovatele prostředků MySQL. I když existující adaptér funguje i nadále, doporučujeme aktualizovat na nejnovější sestavení co nejdříve.

Počínaje verzí 1.1.33.0 verze poskytovatele prostředků MySQL budou aktualizace kumulativní a nemusíte je instalovat v pořadí, ve kterém byly vydané, pokud začínáte z verze 1.1.24.0 nebo novější. Pokud například používáte verzi 1.1.24.0 poskytovatele prostředků MySQL, můžete upgradovat na verzi 1.1.33.0 nebo novější, aniž byste museli nejdřív nainstalovat verzi 1.1.30.0. Pokud chcete zkontrolovat dostupné verze poskytovatele prostředků a verzi centra Azure Stack, na které jsou podporované, přečtěte si téma seznam verzí v tématu [nasazení požadavků poskytovatele prostředků](./azure-stack-mysql-resource-provider-deploy.md#prerequisites).

Chcete-li aktualizovat poskytovatele prostředků, použijte skript **UpdateMySQLProvider. ps1** . Proces se podobá procesu použitému k instalaci poskytovatele prostředků, jak je popsáno v části nasazení poskytovatele prostředků v tomto článku. Skript je součástí stahování poskytovatele prostředků. 

 > [!IMPORTANT]
 > Před upgradem poskytovatele prostředků si přečtěte poznámky k verzi, kde najdete informace o nových funkcích, opravách a známých problémech, které by mohly mít vliv na nasazení.

## <a name="update-script-processes"></a>Aktualizovat procesy skriptu

Skript **UpdateMySQLProvider. ps1** vytvoří nový virtuální počítač s nejnovějším kódem poskytovatele prostředků a migruje nastavení z původního virtuálního počítače do nového virtuálního počítače. Mezi nastavení, která se migrují, patří databáze a informace o hostitelském serveru a potřebný záznam DNS.

>[!NOTE]
>Doporučujeme stáhnout si nejnovější image Windows serveru 2016 Core ze správy Marketplace. Pokud potřebujete nainstalovat aktualizaci, můžete do cesty místní závislosti umístit **jeden** balíček MSU. Pokud je v tomto umístění více než jeden soubor MSU, skript se nezdaří.

Skript vyžaduje použití stejných argumentů, které jsou popsány pro skript DeployMySqlProvider. ps1. Uveďte taky certifikát.  


## <a name="update-script-parameters"></a>Aktualizovat parametry skriptu 
Když spustíte skript prostředí PowerShell **UpdateMySQLProvider. ps1** , zadejte z příkazového řádku následující parametry. Pokud ne, nebo pokud se nějaké ověření parametru nepodaří, budete vyzváni k zadání požadovaných parametrů.

| Název parametru | Popis | Komentář nebo výchozí hodnota | 
| --- | --- | --- | 
| **CloudAdminCredential** | Přihlašovací údaje pro správce cloudu, které jsou nezbytné pro přístup k privilegovanému koncovému bodu. | _Požadovanou_ | 
| **AzCredential** | Přihlašovací údaje pro účet správce služby Azure Stack hub. Použijte stejné přihlašovací údaje jako při nasazení centra Azure Stack. | _Požadovanou_ | 
| **VMLocalCredential** |Přihlašovací údaje pro účet místního správce virtuálního počítače poskytovatele prostředků SQL. | _Požadovanou_ | 
| **PrivilegedEndpoint** | IP adresa nebo název DNS privilegovaného koncového bodu. |  _Požadovanou_ | 
| **AzureEnvironment** | Prostředí Azure účtu správce služby používaného pro nasazení centra Azure Stack. Vyžaduje se jenom pro nasazení Azure AD. Podporované názvy prostředí jsou **AzureCloud**, **AzureUSGovernment**nebo, pokud používáte Čína Azure AD **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | Soubor certifikátu. pfx musí být umístěný i v tomto adresáři. | _Volitelné_ (_povinné_ pro více uzlů) | 
| **DefaultSSLCertificatePassword** | Heslo pro certifikát. pfx. | _Požadovanou_ | 
| **MaxRetryCount** | Počet pokusů o opakování všech operací, pokud dojde k selhání.| 2 | 
| **RetryDuration** | Interval časového limitu mezi opakovanými pokusy (v sekundách). | 120 | 
| **Odinstalace** | Odebrání poskytovatele prostředků a všech přidružených prostředků (viz následující poznámky). | Ne | 
| **DebugMode** | Zabraňuje automatickému vyčištění při selhání. | Ne | 
| **AcceptLicense** | Přeskočí výzvu k přijetí licence GPL.  (https://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | | 

## <a name="update-script-example"></a>Příklad aktualizace skriptu

> [!NOTE] 
> Proces aktualizace platí jenom pro integrované systémy.

Pokud aktualizujete verzi poskytovatele prostředků MySQL na 1.1.33.0 nebo předchozí verze, budete muset v PowerShellu nainstalovat konkrétní verze modulů AzureRm. zaváděcího nástroje a Azure Stack hub. Pokud aktualizujete poskytovatele prostředků MySQL na verzi 1.1.47.0, skript nasazení bude automaticky stahovat a instalovat potřebné moduly PowerShellu pro vás do cesty C:\Program Files\SqlMySqlPsh.

```powershell 
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
# Note that this might not be the most currently available version of Azure Stack Hub PowerShell.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0
```

> [!NOTE]
> V odpojeném scénáři je nutné stáhnout požadované moduly prostředí PowerShell a zaregistrovat úložiště ručně v rámci požadavků. Další informace můžete získat v [nasazení poskytovatele prostředků MySQL](azure-stack-mysql-resource-provider-deploy.md) .

Následující příklad ukazuje skript *UpdateMySQLProvider. ps1* , který můžete spustit z konzoly PowerShellu se zvýšenými oprávněními. Nezapomeňte změnit informace o proměnné a hesla podle potřeby:

```powershell 
# Use the NetBIOS name for the Azure Stack Hub domain. On the Azure Stack Hub SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack" 

# For integrated systems, use the IP address of one of the ERCS VMs.
$privilegedEndpoint = "AzS-ERCS01" 

# Provide the Azure environment used for deploying Azure Stack Hub. Required only for Azure AD deployments. Supported environment names are AzureCloud, AzureUSGovernment, or AzureChinaCloud. 
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted. 
$tempDir = 'C:\TEMP\MYSQLRP' 

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com" 
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass) 
 
# Set credentials for the new resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass) 
 
# And the cloudadmin credential required for privileged endpoint access.
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
.$tempDir\UpdateMySQLProvider.ps1 -AzCredential $AdminCreds ` 
-VMLocalCredential $vmLocalAdminCreds ` 
-CloudAdminCredential $cloudAdminCreds ` 
-PrivilegedEndpoint $privilegedEndpoint ` 
-AzureEnvironment $AzureEnvironment `
-DefaultSSLCertificatePassword $PfxPass ` 
-DependencyFilesLocalPath $tempDir\cert ` 
-AcceptLicense 
```  

Po dokončení skriptu aktualizace poskytovatele prostředků zavřete aktuální relaci PowerShellu.

## <a name="next-steps"></a>Další kroky
[Udržovat poskytovatele prostředků MySQL](azure-stack-mysql-resource-provider-maintain.md)
