---
title: Aktualizace poskytovatele prostředků MySQL v Azure Stack hub
description: Naučte se aktualizovat poskytovatele prostředků MySQL centra Azure Stack v centru Azure Stack.
author: bryanla
ms.topic: article
ms.date: 9/22/2020
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 01/11/2020
ms.openlocfilehash: 33c7d66b7a8b0d352bbf9227cec11adf7cd001fe
ms.sourcegitcommit: a745662c7a5a18f135accf3f70d8508b57e83e2b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/22/2020
ms.locfileid: "97737738"
---
# <a name="update-the-mysql-resource-provider-in-azure-stack-hub"></a>Aktualizace poskytovatele prostředků MySQL v Azure Stack hub

> [!IMPORTANT]
> Před aktualizací poskytovatele prostředků si přečtěte poznámky k verzi, kde najdete informace o nových funkcích, opravách a známých problémech, které by mohly mít vliv na nasazení. Zpráva k vydání verze také určuje minimální verzi centra Azure Stack požadovaná pro poskytovatele prostředků.

Při aktualizaci Azure Stackch sestavení centra se může uvolnit nový adaptér poskytovatele prostředků MySQL. I když existující adaptér funguje i nadále, doporučujeme aktualizovat na nejnovější sestavení co nejdříve.

  |Podporovaná verze centra Azure Stack|Verze MySQL RP|Windows Server, na kterém běží služba RP
  |-----|-----|-----|
  |2008, 2005|[MySQL RP verze 1.1.93.1](https://aka.ms/azshmysqlrp11931)|Microsoft AzureStack Add-on RP Windows Server
  |2005, 2002, 1910|[MySQL RP verze 1.1.47.0](https://aka.ms/azurestackmysqlrp11470)|Windows Server 2016 Datacenter – jádro serveru|
  |1908|[MySQL RP verze 1.1.33.0](https://aka.ms/azurestackmysqlrp11330)|Windows Server 2016 Datacenter – jádro serveru|
  |     |     |     |

Aktualizace poskytovatele prostředků MySQL je kumulativní. Při aktualizaci ze starší verze můžete přímo aktualizovat na nejnovější verzi. 

Chcete-li aktualizovat poskytovatele prostředků, použijte skript **UpdateMySQLProvider.ps1** . Použijte účet služby s právy místního správce a je **vlastníkem** předplatného. Skript aktualizace je součástí stažení poskytovatele prostředků. 

Proces aktualizace je podobný procesu použitému k [nasazení poskytovatele prostředků](./azure-stack-mysql-resource-provider-deploy.md). Skript aktualizace používá stejné argumenty jako skript DeployMySqlProvider.ps1 a budete muset zadat informace o certifikátu.

## <a name="update-script-processes"></a>Aktualizovat procesy skriptu

Skript **UpdateMySQLProvider.ps1** vytvoří nový virtuální počítač s nejnovější imagí operačního systému, nasadí nejnovější kód poskytovatele prostředků a migruje nastavení z původního poskytovatele prostředků na nového poskytovatele prostředků.

>[!NOTE]
>Doporučujeme, abyste si stáhli nejnovější bitovou kopii Windows serveru 2016 Core Image nebo Windows serveru Microsoft AzureStack z webu Marketplace Management. Pokud potřebujete nainstalovat aktualizaci, můžete do cesty místní závislosti umístit **jeden** balíček MSU. Pokud je v tomto umístění více než jeden soubor MSU, skript se nezdaří.

Když skript *UpdateMySQLProvider.ps1* vytvoří nový virtuální počítač, skript migruje následující nastavení z původního virtuálního počítače poskytovatele prostředků:

* informace o databázi
* informace o hostitelském serveru
* požadovaný záznam DNS

## <a name="update-script-parameters"></a>Aktualizovat parametry skriptu 
Při spuštění skriptu **UpdateMySQLProvider.ps1** PowerShell zadejte z příkazového řádku následující parametry. Pokud ne, nebo pokud se nějaké ověření parametru nepodaří, budete vyzváni k zadání požadovaných parametrů.

| Název parametru | Popis | Komentář nebo výchozí hodnota | 
| --- | --- | --- | 
| **CloudAdminCredential** | Přihlašovací údaje pro správce cloudu, které jsou nezbytné pro přístup k privilegovanému koncovému bodu. | _Povinné_ | 
| **AzCredential** | Přihlašovací údaje pro účet správce služby Azure Stack hub. Použijte stejné přihlašovací údaje, které jste použili k nasazení centra Azure Stack. Pokud účet, který používáte se službou AzCredential, vyžaduje vícefaktorové ověřování (MFA), skript se nezdaří. | _Povinné_ | 
| **VMLocalCredential** |Přihlašovací údaje pro účet místního správce virtuálního počítače poskytovatele prostředků SQL. | _Povinné_ | 
| **PrivilegedEndpoint** | IP adresa nebo název DNS privilegovaného koncového bodu. |  _Povinné_ | 
| **AzureEnvironment** | Prostředí Azure účtu správce služby používaného pro nasazení centra Azure Stack. Vyžaduje se jenom pro nasazení Azure AD. Podporované názvy prostředí jsou **AzureCloud**, **AzureUSGovernment** nebo, pokud používáte Čína Azure AD **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | Soubor certifikátu. pfx musí být umístěný i v tomto adresáři. | _Volitelné_ (_povinné_ pro více uzlů) | 
| **DefaultSSLCertificatePassword** | Heslo pro certifikát. pfx. | _Povinné_ | 
| **MaxRetryCount** | Počet pokusů o opakování všech operací, pokud dojde k selhání.| 2 | 
| **RetryDuration** | Interval časového limitu mezi opakovanými pokusy (v sekundách). | 120 | 
| **Odinstalace** | Odebrání poskytovatele prostředků a všech přidružených prostředků (viz následující poznámky). | Ne | 
| **DebugMode** | Zabraňuje automatickému vyčištění při selhání. | Ne | 
| **AcceptLicense** | Přeskočí výzvu k přijetí licence GPL.  (https://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | | 

## <a name="update-script-example"></a>Příklad aktualizace skriptu

Pokud aktualizujete verzi poskytovatele prostředků MySQL na 1.1.33.0 nebo předchozí verze, budete muset v PowerShellu nainstalovat konkrétní verze modulů AzureRm. zaváděcího nástroje a Azure Stack hub. 

Pokud aktualizujete poskytovatele prostředků MySQL na verzi 1.1.47.0 nebo novější, můžete tento krok přeskočit. Skript nasazení automaticky stáhne a nainstaluje potřebné moduly PowerShellu pro vás do cesty C:\Program Files\SqlMySqlPsh. 

>[!NOTE]
>Pokud složka C:\Program Files\SqlMySqlPsh už existuje se staženým modulem PowerShellu, doporučujeme před spuštěním skriptu aktualizace vyčistit tuto složku. Tím se zajistí stažení a použití správné verze modulu PowerShellu.

```powershell 
# Run the following scripts when updating to version 1.1.33.0 only.
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module.
# Note that this might not be the most currently available version of Azure Stack Hub PowerShell.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0
```

> [!NOTE]
> V odpojeném scénáři je nutné stáhnout požadované moduly prostředí PowerShell a zaregistrovat úložiště ručně v rámci požadavků. Další informace můžete získat v [nasazení poskytovatele prostředků MySQL](azure-stack-mysql-resource-provider-deploy.md) .

Následující příklad ukazuje skript *UpdateMySQLProvider.ps1* , který můžete spustit z konzoly PowerShellu se zvýšenými oprávněními. Nezapomeňte změnit informace o proměnné a hesla podle potřeby:

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

# For version 1.1.47.0 or later, the PowerShell modules used by the RP deployment are placed in C:\Program Files\SqlMySqlPsh
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
