---
title: Použití databází SQL v Azure stacku | Dokumentace Microsoftu
description: Zjistěte, jak nasadit SQL databáze jako služba v Azure stacku a rychlé kroky pro nasazení adaptéru poskytovatele prostředků SQL serveru.
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
ms.date: 03/29/2019
ms.lastreviewed: 03/18/2019
ms.author: mabrigg
ms.reviewer: jiahan
ms.openlocfilehash: 1e4b8bddaa5bfa164e413ee7a20071fe6a0cc831
ms.sourcegitcommit: 23816ec68f67f3ac51f78de925b7631590743a29
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2019
ms.locfileid: "66835067"
---
# <a name="deploy-the-sql-server-resource-provider-on-azure-stack"></a>Nasazení poskytovatele prostředků SQL serveru ve službě Azure Stack

Používání poskytovatele prostředků Azure Stack SQL serveru k vystavení databáze SQL jako služba Azure Stack. Poskytovatele prostředků SQL běží jako služba na virtuálním počítači (VM) systému Windows Server 2016 Server Core.

> [!IMPORTANT]
> Vytvoření položek na serverech, daný hostitel SQL nebo MySQL se podporuje jenom poskytovatele prostředků. Neodpovídající stavu může dojít položky vytvořené na hostitelském serveru, které nebyly vytvořeny pomocí zprostředkovatele prostředků.

## <a name="prerequisites"></a>Požadavky

Existuje několik předpokladů, které musí být splněné před nasazením poskytovatele prostředků Azure Stack SQL. Budou odpovídat vašim požadavkům, proveďte následující kroky na počítači s přístupem k privilegovaným koncového bodu virtuálního počítače:

- Pokud jste tak již neučinili, [registrace Azure Stack](azure-stack-registration.md) s Azure, takže si můžete stáhnout položky Azure marketplace.
- Je nutné nainstalovat moduly Azure a Azure Stack Powershellu v systému, kde budete spouštět tuto instalaci. Tento systém musí být bitovou kopii systému Windows 10 nebo Windows Server 2016 s nejnovější verzí modulu .NET runtime. Zobrazit [instalace Powershellu pro Azure Stack](./azure-stack-powershell-install.md).
- Přidejte požadované jádra serveru systému Windows virtuální počítač na webu Marketplace služby Azure Stack stažením **systému Windows Server 2016 Datacenter - jádra serveru** bitové kopie.
- Stáhnout poskytovatele prostředků SQL binární a pak spusťte Self-Extractor extrahujte obsah do dočasného adresáře. Poskytovatel prostředků má minimální odpovídající Azure Stack sestavení.

  |Minimální verze služby Azure Stack|Verze poskytovatele prostředků SQL|
  |-----|-----|
  |Verze. 1808 (1.1808.0.97)|[SQL RP verze 1.1.33.0](https://aka.ms/azurestacksqlrp11330)|  
  |Verze. 1808 (1.1808.0.97)|[SQL RP verze 1.1.30.0](https://aka.ms/azurestacksqlrp11300)|  
  |Verzi 1804 (1.0.180513.1)|[SQL RP verze 1.1.24.0](https://aka.ms/azurestacksqlrp11240)  
  |     |     |

- Ujistěte se, že jsou splněné požadavky na integraci datacenter:

    |Požadavek|Referenční informace|
    |-----|-----|
    |Podmíněné předávání DNS je nastavena správně.|[Integrace datových center Azure Stack – DNS](azure-stack-integrate-dns.md)|
    |Jsou otevřené příchozí porty pro poskytovatele prostředků.|[Integrace datových center Azure Stack – příchozí porty a protokoly](azure-stack-integrate-endpoints.md#ports-and-protocols-inbound)|
    |Subjekt certifikátu PKI a po síti SAN jsou správně nastavené.|[Požadavky nasazení Azure Stack povinné infrastruktury veřejných KLÍČŮ](azure-stack-pki-certs.md#mandatory-certificates)<br>[Požadavky nasazení certifikátu PaaS Azure Stack](azure-stack-pki-certs.md#optional-paas-certificates)|
    |     |     |

### <a name="certificates"></a>Certifikáty

_Pro integrované systémy instalace pouze_. Je nutné zadat certifikát SQL PaaS PKI, který je popsaný v části volitelné PaaS certifikáty z [požadavky na infrastrukturu veřejných KLÍČŮ nasazení Azure Stack](./azure-stack-pki-certs.md#optional-paas-certificates). Umístěte soubor .pfx do umístění určeného proměnnou **DependencyFilesLocalPath** parametru. Pro systémy ASDK neposkytují certifikát.

## <a name="deploy-the-sql-resource-provider"></a>Nasazení poskytovatele prostředků SQL

Po instalaci všech požadavků, můžete spustit **DeploySqlProvider.ps1** skriptu pro nasazení poskytovatele prostředků SQL. Jako součást binární poskytovatele prostředků SQL, který jste stáhli pro vaši verzi sady Azure Stack je extrahován DeploySqlProvider.ps1 skriptu.

 > [!IMPORTANT]
 > Před nasazením poskytovatele prostředků, přečtěte si poznámky k verzi pro další informace o nové funkce, opravy a známých problémech, které můžou ovlivnit nasazení.
 
Chcete-li nasazení poskytovatele prostředků SQL, otevřete **nové** se zvýšenými oprávněními okno prostředí PowerShell (není prostředí PowerShell ISE) a změn do adresáře, které jste extrahovali binární soubory poskytovatele prostředků SQL. Doporučujeme použít nové okno prostředí PowerShell, aby potenciální potíže způsobené aktivitami moduly Powershellu, které jsou už načteny.

Spusťte skript DeploySqlProvider.ps1, který dokončí následující úkoly:

- Certifikáty a další artefakty nahraje do účtu úložiště ve službě Azure Stack.
- Galerie balíčků publikuje, abyste mohli nasadit databází SQL Database pomocí galerie.
- Publikuje balíček Galerie pro nasazení hostitelských serverů.
- Nasadí virtuální počítač pomocí základní image Windows serveru 2016 jste stáhli a poté nainstaluje poskytovatele prostředků SQL.
- Zaregistruje místní záznam DNS, který se mapuje na zprostředkovateli prostředků virtuálního počítače.
- Zaregistruje poskytovatele prostředků s místní Azure Resource Manageru pro účet operátor.

> [!NOTE]
> Při spuštění nasazení poskytovatele prostředků SQL, **system.local.sqladapter** se vytvoří skupina prostředků. Může trvat až 75 minut na dokončení požadovaných nasazení do této skupiny prostředků.

### <a name="deploysqlproviderps1-parameters"></a>Parametry DeploySqlProvider.ps1

Můžete zadat následující parametry z příkazového řádku. Pokud ne, nebo pokud libovolný parametr ověření nezdaří, budete vyzváni k poskytnutí požadovaných parametrů.

| Název parametru | Popis | Komentář nebo výchozí hodnotu |
| --- | --- | --- |
| **CloudAdminCredential** | Přihlašovací údaje pro správce cloudu potřebné pro přístup k privilegovaným koncový bod. | _Požadováno_ |
| **AzCredential** | Přihlašovací údaje pro účet správce služby Azure Stack. Použijte stejné přihlašovací údaje, které jste použili k nasazení Azure Stack. | _Požadováno_ |
| **VMLocalCredential** | Přihlašovací údaje pro účet místního správce poskytovatele prostředků SQL virtuálního počítače. | _Požadováno_ |
| **PrivilegedEndpoint** | IP adresa nebo název DNS privileged koncového bodu. |  _Požadováno_ |
| **AzureEnvironment** | Prostředí Azure účet správce služby, který se používá pro nasazení Azure Stack. Vyžaduje se jenom pro nasazení služby Azure AD. Názvy prostředí podporované jsou **AzureCloud**, **AzureUSGovernment**, nebo pokud používáte Azure Active Directory Čína, **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | Pro integrované systémy pouze váš soubor PFX certifikátu musí být umístěna v tomto adresáři. Volitelně můžete zkopírovat jeden balíček Windows Update MSU tady. | _Volitelné_ (_povinné_ pro integrované systémy) |
| **DefaultSSLCertificatePassword** | Heslo pro certifikát PFX. | _Požadováno_ |
| **MaxRetryCount** | Počet pokusů, které chcete opakovat každé operace, pokud dojde k selhání.| 2 |
| **RetryDuration** | Časový interval mezi opakovanými pokusy, během několika sekund. | 120 |
| **Odinstalace** | Odebere poskytovatele prostředků a všechny související prostředky (viz následující poznámky). | Ne |
| **Režim DebugMode** | Zabraňuje automatickému čištění při selhání. | Ne |

## <a name="deploy-the-sql-resource-provider-using-a-custom-script"></a>Nasazení poskytovatele prostředků SQL pomocí vlastního skriptu

Chcete-li odstranit všechny ruční konfigurace při nasazení poskytovatele prostředků, můžete přizpůsobit následující skript.  

Podle potřeby pro vaše nasazení Azure Stack, změňte výchozí informace o účtu a hesla.


```powershell
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
# Note that this might not be the most currently available version of Azure Stack PowerShell
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines
$privilegedEndpoint = "AzS-ERCS01"

# Provide the Azure environment used for deploying Azure Stack. Required only for Azure AD deployments. Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using. 
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account can be Azure Active Directory or Active Directory Federation Services.
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new resource provider VM local administrator account.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# Add the cloudadmin credential that's required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Clear the existing login information from the Azure PowerShell context.
Clear-AzureRMContext -Scope CurrentUser -Force
Clear-AzureRMContext -Scope Process -Force

# Change to the directory folder where you extracted the installation files. Do not provide a certificate on ASDK!
. $tempDir\DeploySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -AzureEnvironment $AzureEnvironment `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert

 ```

Po dokončení skriptu instalace poskytovatele prostředků, aktualizujte svůj prohlížeč, abyste měli jistotu, že vidíte nejnovější aktualizace.

## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Ověření nasazení pomocí portálu Azure Stack

Můžete použít následující postup ověření úspěšném nasazení poskytovatele prostředků SQL.

1. Přihlaste se k portálu pro správu jako správce služeb.
2. Vyberte **skupiny prostředků**.
3. Vyberte **systému.\< umístění\>.sqladapter** skupinu prostředků.
4. Na stránce Souhrn pro skupinu prostředků přehled měla by existovat žádná selhání nasazení.
      ![Ověření nasazení poskytovatele prostředků SQL](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)
5. Nakonec vyberte **virtuálních počítačů** v portálu pro správu k ověření poskytovatele prostředků SQL virtuální počítač byl úspěšně vytvořen a běží.

## <a name="next-steps"></a>Další postup

[Přidat hostitelské servery](azure-stack-sql-resource-provider-hosting-servers.md)
