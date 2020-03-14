---
title: Nasazení poskytovatele prostředků SQL Server
titleSuffix: Azure Stack Hub
description: Naučte se, jak nasadit poskytovatele prostředků SQL Server v centru Azure Stack.
author: bryanla
ms.topic: article
ms.date: 10/02/2019
ms.lastreviewed: 03/18/2019
ms.author: bryanla
ms.reviewer: xiao
ms.openlocfilehash: ff351dcef91491e6d52aa61ff25f282968c963fe
ms.sourcegitcommit: 20d10ace7844170ccf7570db52e30f0424f20164
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79294734"
---
# <a name="deploy-the-sql-server-resource-provider-on-azure-stack-hub"></a>Nasazení poskytovatele prostředků SQL Server v centru Azure Stack

Použijte poskytovatele prostředků SQL Server centra Azure Stack k vystavování databází SQL jako služby centra pro Azure Stack. Poskytovatel prostředků SQL se spouští jako služba na virtuálním počítači se systémem Windows Server 2016 Server Core (VM).

> [!IMPORTANT]
> Pouze poskytovatel prostředků je podporován k vytváření položek na serverech, které jsou hostiteli SQL nebo MySQL. Položky vytvořené na hostitelském serveru, které nejsou vytvořené poskytovatelem prostředků, můžou vést k neshodě stavu.

## <a name="prerequisites"></a>Předpoklady

Aby bylo možné nasadit poskytovatele prostředků SQL centra Azure Stack, je nutné, aby bylo k dispozici několik požadavků. Pokud chcete tyto požadavky splnit, proveďte v počítači, který má přístup k VIRTUÁLNÍmu počítači privilegovaného koncového bodu, následující kroky:

- Pokud jste to ještě neudělali, [zaregistrujte Azure Stack centrum](azure-stack-registration.md) s Azure, abyste si mohli stáhnout Azure Marketplace položky.

- Přidejte požadovaný virtuální počítač s Windows serverem do centra Azure Stack hub tak, že stáhnete hlavní bitovou kopii **Windows serveru 2016 Datacenter-Server** .

- Stáhněte si binární soubor poskytovatele prostředků SQL a potom spusťte samočinný extrahování a extrahujte obsah do dočasného adresáře. Poskytovatel prostředků má minimálně odpovídající sestavení centra Azure Stack.

  |Minimální verze centra Azure Stack|Verze SQL RP|
  |-----|-----|
  |Verze 1910 (1.1910.0.58)|[SQL RP verze 1.1.47.0](https://aka.ms/azurestacksqlrp11470)|
  |Verze 1808 (1.1808.0.97)|[SQL RP verze 1.1.33.0](https://aka.ms/azurestacksqlrp11330)|  
  |Verze 1808 (1.1808.0.97)|[SQL RP verze 1.1.30.0](https://aka.ms/azurestacksqlrp11300)|  
  |Verze 1804 (1.0.180513.1)|[SQL RP verze 1.1.24.0](https://aka.ms/azurestacksqlrp11240)  
  |     |     |

> [!IMPORTANT]
> Před nasazením poskytovatele prostředků SQL verze 1.1.47.0 by měl být systém centra Azure Stack upgradován na verzi 1910 Update nebo novější. Verze poskytovatele prostředků SQL 1.1.47.0 v předchozích nepodporovaných verzích centra Azure Stack nefunguje.

- Ujistěte se, že jsou splněné předpoklady pro integraci Datacenter:

    |Požadavek|Referenční informace|
    |-----|-----|
    |Podmíněné předávání DNS je nastaveno správně.|[Integrace centrálního centra Azure Stack – DNS](azure-stack-integrate-dns.md)|
    |Příchozí porty pro poskytovatele prostředků jsou otevřené.|[Integrace Datacenter centra Azure Stack – příchozí porty a protokoly](azure-stack-integrate-endpoints.md#ports-and-protocols-inbound)|
    |Subjekt certifikátu PKI a síť SAN jsou nastavené správně.|[Povinné předpoklady PKI pro nasazení centra Azure Stack](azure-stack-pki-certs.md#mandatory-certificates)<br>[PaaS požadavky na certifikát nasazení centra Azure Stack](azure-stack-pki-certs.md#optional-paas-certificates)|
    |     |     |

V odpojeném scénáři proveďte následující kroky a stáhněte požadované moduly PowerShellu a zaregistrujte úložiště ručně.

1. Přihlaste se k počítači s připojením k Internetu a pomocí následujících skriptů si stáhněte moduly PowerShellu.

```powershell
Import-Module -Name PowerShellGet -ErrorAction Stop
Import-Module -Name PackageManagement -ErrorAction Stop

# path to save the packages, c:\temp\azs1.6.0 as an example here
$Path = "c:\temp\azs1.6.0"
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.6.0
```

2. Stažené balíčky pak zkopírujete do zařízení USB.

3. Přihlaste se k odpojené pracovní stanici a zkopírujte balíčky ze zařízení USB do umístění v pracovní stanici.

4. Zaregistrujte toto umístění jako místní úložiště.

```powershell
# requires -Version 5
# requires -RunAsAdministrator
# requires -Module PowerShellGet
# requires -Module PackageManagement

$SourceLocation = "C:\temp\azs1.6.0"
$RepoName = "azs1.6.0"

Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation -InstallationPolicy Trusted

New-Item -Path $env:ProgramFiles -name "SqlMySqlPsh" -ItemType "Directory"
```

### <a name="certificates"></a>Certifikáty

_Pouze pro instalace integrovaných systémů_. Musíte zadat certifikát PKI SQL PaaS, který je popsaný v části volitelné certifikáty PaaS v tématu [požadavky na infrastrukturu veřejných klíčů nasazení v centru Azure Stack](./azure-stack-pki-certs.md#optional-paas-certificates). Soubor. pfx umístěte do umístění zadaného parametrem **DependencyFilesLocalPath** . Neposkytněte certifikát pro systémy ASDK.

## <a name="deploy-the-sql-resource-provider"></a>Nasazení poskytovatele prostředků SQL

Po dokončení instalace všech požadovaných součástí spusťte skript **DeploySqlProvider. ps1** z počítače, který má Azure Stack přístup ke koncovému bodu správy prostředků Azure Resource admin a s privilegovaným koncovým bodem pro nasazení poskytovatele prostředků SQL. Skript DeploySqlProvider. ps1 je extrahován jako součást binárního souboru poskytovatele prostředků SQL, který jste stáhli pro vaši verzi centra Azure Stack.

 > [!IMPORTANT]
 > Před nasazením poskytovatele prostředků si přečtěte poznámky k verzi, kde najdete informace o nových funkcích, opravách a známých problémech, které by mohly mít vliv na nasazení.

Pokud chcete nasadit poskytovatele prostředků SQL, otevřete **nové** okno prostředí PowerShell se zvýšenými oprávněními (ne PowerShell ISE) a přejděte do adresáře, do kterého jste extrahovali binární soubory poskytovatele prostředků SQL. Doporučujeme použít nové okno prostředí PowerShell, aby nedocházelo k potenciálním problémům způsobeným moduly prostředí PowerShell, které jsou již načteny.

Spusťte skript DeploySqlProvider. ps1, který dokončí následující úlohy:

- Nahraje certifikáty a jiné artefakty do účtu úložiště v Azure Stackovém centru.
- Publikuje balíčky galerie, takže můžete nasadit databáze SQL pomocí galerie.
- Publikuje balíček galerie pro nasazení hostitelských serverů.
- Nasadí virtuální počítač pomocí image Windows serveru 2016 Core, kterou jste stáhli, a potom nainstaluje poskytovatele prostředků SQL.
- Zaregistruje místní záznam DNS, který se mapuje na váš virtuální počítač poskytovatele prostředků.
- Zaregistruje poskytovatele prostředků s místní Azure Resource Manager pro účet operátora.

> [!NOTE]
> Po spuštění nasazení poskytovatele prostředků SQL se vytvoří skupina prostředků **System. Local. sqladapter** . Dokončení požadovaných nasazení do této skupiny prostředků může trvat až 75 minut. Neměli byste umístit žádné další prostředky do skupiny prostředků **System. Local. sqladapter** .

### <a name="deploysqlproviderps1-parameters"></a>DeploySqlProvider. ps1 – parametry

Z příkazového řádku můžete zadat následující parametry. Pokud ne, nebo pokud se nějaké ověření parametru nepodaří, budete vyzváni k zadání požadovaných parametrů.

| Název parametru | Popis | Komentář nebo výchozí hodnota |
| --- | --- | --- |
| **CloudAdminCredential** | Přihlašovací údaje pro správce cloudu, které jsou nezbytné pro přístup k privilegovanému koncovému bodu. | _Požadovanou_ |
| **AzCredential** | Přihlašovací údaje pro účet správce služby Azure Stack hub. Použijte stejné přihlašovací údaje, které jste použili k nasazení centra Azure Stack. | _Požadovanou_ |
| **VMLocalCredential** | Přihlašovací údaje pro účet místního správce virtuálního počítače poskytovatele prostředků SQL. | _Požadovanou_ |
| **PrivilegedEndpoint** | IP adresa nebo název DNS privilegovaného koncového bodu. |  _Požadovanou_ |
| **AzureEnvironment** | Prostředí Azure účtu správce služby používaného pro nasazení centra Azure Stack. Vyžaduje se jenom pro nasazení Azure AD. Podporované názvy prostředí jsou **AzureCloud**, **AzureUSGovernment**nebo, pokud používáte Čína Azure Active Directory **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | V případě pouze integrovaných systémů musí být soubor Certificate. pfx umístěn v tomto adresáři. Volitelně můžete zkopírovat jeden web Windows Update balíček MSU zde. | _Volitelné_ (_povinné_ pro integrované systémy) |
| **DefaultSSLCertificatePassword** | Heslo pro certifikát. pfx. | _Požadovanou_ |
| **MaxRetryCount** | Počet pokusů o opakování všech operací, pokud dojde k selhání.| 2 |
| **RetryDuration** | Interval časového limitu mezi opakovanými pokusy (v sekundách). | 120 |
| **Odinstalace** | Odebere poskytovatele prostředků a všechny přidružené prostředky (viz následující poznámky). | Ne |
| **DebugMode** | Zabraňuje automatickému vyčištění při selhání. | Ne |

## <a name="deploy-the-sql-resource-provider-using-a-custom-script"></a>Nasazení poskytovatele prostředků SQL pomocí vlastního skriptu

Pokud nasazujete 1.1.33.0 nebo předchozí verze poskytovatele prostředků SQL, budete muset v PowerShellu nainstalovat konkrétní verze AzureRm. zaváděcího nástroje a modulu Azure Stack hub. Pokud nasazujete poskytovatele prostředků SQL verze 1.1.47.0, skript nasazení automaticky stáhne a nainstaluje potřebné moduly PowerShellu pro vás do cesty C:\Program Files\SqlMySqlPsh.

```powershell
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureStack module
# Note that this might not be the most currently available version of Azure Stack Hub PowerShell
Install-Module -Name AzureRm.BootStrapper -RequiredVersion 0.5.0 -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0
```

> [!NOTE]
> V odpojeném scénáři je nutné stáhnout požadované moduly prostředí PowerShell a zaregistrovat úložiště ručně v rámci požadavků.

Chcete-li při nasazování poskytovatele prostředků eliminovat jakoukoli ruční konfiguraci, můžete přizpůsobit následující skript. Podle potřeby změňte výchozí informace o účtu a hesla pro nasazení centra Azure Stack.

```powershell
# Use the NetBIOS name for the Azure Stack Hub domain. On the Azure Stack Hub SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS VMs
$privilegedEndpoint = "AzS-ERCS01"

# Provide the Azure environment used for deploying Azure Stack Hub. Required only for Azure AD deployments. Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud, or AzureUSGovernment depending which Azure subscription you're using.
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account can be Azure Active Directory or Active Directory Federation Services.
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new resource provider VM local admin account.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# Add the cloudadmin credential that's required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# For version 1.1.47.0, the PowerShell modules used by the RP deployment are placed in C:\Program Files\SqlMySqlPsh
# The deployment script adds this path to the system $env:PSModulePath to ensure correct modules are used.
$rpModulePath = Join-Path -Path $env:ProgramFiles -ChildPath 'SqlMySqlPsh'
$env:PSModulePath = $env:PSModulePath + ";" + $rpModulePath 

# Change to the directory folder where you extracted the installation files. Don't provide a certificate on ASDK!
. $tempDir\DeploySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -AzureEnvironment $AzureEnvironment `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert

 ```

Po dokončení instalačního skriptu poskytovatele prostředků aktualizujte prohlížeč, abyste se ujistili, že vidíte nejnovější aktualizace a zavřete aktuální relaci PowerShellu.

## <a name="verify-the-deployment-using-the-azure-stack-hub-portal"></a>Ověření nasazení pomocí portálu Azure Stack hub

Pomocí následujících kroků můžete ověřit, jestli je poskytovatel prostředků SQL úspěšně nasazený.

1. Přihlaste se k portálu pro správu jako správce služby.
2. Vyberte **skupiny prostředků**.
3. Vyberte **umístění System.\<\>** skupinu prostředků. sqladapter.
4. Na stránce Souhrn pro skupinu prostředků by se neměla nasazovat žádná neúspěšná nasazení.

    ![Ověření nasazení poskytovatele prostředků SQL na portálu pro správu centra Azure Stack](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)

5. Nakonec vyberte **virtuální počítače** na portálu pro správu, abyste ověřili, že se virtuální počítač poskytovatele prostředků SQL úspěšně vytvořil a běží.

## <a name="next-steps"></a>Další kroky

[Přidat hostitelské servery](azure-stack-sql-resource-provider-hosting-servers.md)
