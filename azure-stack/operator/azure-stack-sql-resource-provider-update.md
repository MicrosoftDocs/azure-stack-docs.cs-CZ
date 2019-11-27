---
title: Aktualizace poskytovatele prostředků Azure Stack SQL | Microsoft Docs
description: Přečtěte si, jak můžete aktualizovat poskytovatele prostředků Azure Stack SQL.
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
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 26ce99f87f1b0e1e379bad6276c88a8e6772c035
ms.sourcegitcommit: 284f5316677c9a7f4c300177d0e2a905df8cb478
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2019
ms.locfileid: "74465349"
---
# <a name="update-the-sql-resource-provider"></a>Aktualizovat poskytovatele prostředků SQL

*Platí pro: Azure Stack integrovaných systémů.*

Když se Azure Stack aktualizuje na nové sestavení, může být vydaný nový poskytovatel prostředků SQL. I když stávající poskytovatel prostředků i nadále funguje, doporučujeme aktualizovat na nejnovější sestavení co nejdříve. 

Od verze 1.1.33.0 verze poskytovatele prostředků SQL jsou aktualizace kumulativní a není nutné je instalovat v pořadí, v jakém byly vydány. Pokud začínáte z verze 1.1.24.0 nebo novější. Pokud například používáte verzi 1.1.24.0 poskytovatele prostředků SQL, můžete upgradovat na verzi 1.1.33.0 nebo novější, aniž byste museli nejdřív nainstalovat verzi 1.1.30.0. Pokud chcete zkontrolovat dostupné verze poskytovatele prostředků a verzi Azure Stack podporovaná, přečtěte si téma seznam verzí v tématu [nasazení požadavků poskytovatele prostředků](./azure-stack-sql-resource-provider-deploy.md#prerequisites).

Chcete-li aktualizovat poskytovatele prostředků, použijte skript *UpdateSQLProvider. ps1* . Tento skript je součástí stažení nového poskytovatele prostředků SQL. Proces aktualizace je podobný procesu použitému k [nasazení poskytovatele prostředků](./azure-stack-sql-resource-provider-deploy.md). Skript aktualizace používá stejné argumenty jako skript DeploySqlProvider. ps1 a budete muset zadat informace o certifikátu.

 > [!IMPORTANT]
 > Před upgradem poskytovatele prostředků si přečtěte poznámky k verzi, kde najdete informace o nových funkcích, opravách a známých problémech, které by mohly mít vliv na nasazení.

## <a name="update-script-processes"></a>Aktualizovat procesy skriptu

Skript *UpdateSQLProvider. ps1* vytvoří nový virtuální počítač (VM) s nejnovějším kódem poskytovatele prostředků.

> [!NOTE]
> Doporučujeme stáhnout si nejnovější image Windows serveru 2016 Core ze správy Marketplace. Pokud potřebujete nainstalovat aktualizaci, můžete do cesty místní závislosti umístit **jeden** balíček MSU. Pokud je v tomto umístění více než jeden soubor MSU, skript se nezdaří.

Po vytvoření nového virtuálního počítače pomocí skriptu *UpdateSQLProvider. ps1* migruje skript následující nastavení z původního virtuálního počítače poskytovatele:

* Informace o databázi
* informace o hostitelském serveru
* požadovaný záznam DNS

## <a name="update-script-parameters"></a>Aktualizovat parametry skriptu

Když spustíte skript prostředí PowerShell **UpdateSQLProvider. ps1** , můžete zadat následující parametry z příkazového řádku. Pokud ne, nebo pokud se nějaké ověření parametru nepodaří, budete vyzváni k zadání požadovaných parametrů.

| Název parametru | Popis | Komentář nebo výchozí hodnota |
| --- | --- | --- |
| **CloudAdminCredential** | Přihlašovací údaje pro správce cloudu, které jsou nezbytné pro přístup k privilegovanému koncovému bodu. | _Požadovanou_ |
| **AzCredential** | Přihlašovací údaje pro účet správce služby Azure Stack Použijte stejné přihlašovací údaje, které jste použili pro nasazení Azure Stack. | _Požadovanou_ |
| **VMLocalCredential** | Pověření pro účet místního správce virtuálního počítače poskytovatele prostředků SQL. | _Požadovanou_ |
| **PrivilegedEndpoint** | IP adresa nebo název DNS privilegovaného koncového bodu. |  _Požadovanou_ |
| **AzureEnvironment** | Prostředí Azure účtu správce služby, které jste použili pro nasazení Azure Stack. Vyžaduje se jenom pro nasazení Azure AD. Podporované názvy prostředí jsou **AzureCloud**, **AzureUSGovernment**nebo, pokud používáte Čína Azure AD **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | Do tohoto adresáře musíte taky vložit soubor Certificate. pfx. | _Volitelné pro jeden uzel, ale je povinný pro více uzlů_ |
| **DefaultSSLCertificatePassword** | Heslo pro certifikát. pfx. | _Požadovanou_ |
| **MaxRetryCount** | Počet pokusů o opakování všech operací, pokud dojde k selhání.| 2 |
| **RetryDuration** |Interval časového limitu mezi opakovanými pokusy (v sekundách). | 120 |
| **Odinstalace** | Odebere poskytovatele prostředků a všechny přidružené prostředky. | Ne |
| **DebugMode** | Zabraňuje automatickému vyčištění při selhání. | Ne |

## <a name="update-script-powershell-example"></a>Příklad aktualizace skriptu PowerShellu
> [!NOTE]
> Tento proces aktualizace se týká pouze Azure Stack integrovaných systémů.

Pokud aktualizujete verzi poskytovatele prostředků SQL na 1.1.33.0 nebo předchozí verze, budete muset v PowerShellu nainstalovat konkrétní verze AzureRm. zaváděcího nástroje a Azure Stack moduly. Pokud aktualizujete na poskytovatele prostředků SQL verze 1.1.47.0, tento krok se dá přeskočit.

```powershell
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
# Note that this might not be the most currently available version of Azure Stack PowerShell.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0
```

Následuje příklad použití skriptu *UpdateSQLProvider. ps1* , který můžete spustit z konzoly PowerShell se zvýšenými oprávněními. Nezapomeňte změnit informace o proměnné a hesla podle potřeby:  

```powershell
# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but this might have been changed at installation.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines.
$privilegedEndpoint = "AzS-ERCS01"

# Provide the Azure environment used for deploying Azure Stack. Required only for Azure AD deployments. Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using. 
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service administrator account (this can be Azure AD or AD FS).
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

## <a name="next-steps"></a>Další kroky

[Údržba poskytovatele prostředků SQL](azure-stack-sql-resource-provider-maintain.md)
