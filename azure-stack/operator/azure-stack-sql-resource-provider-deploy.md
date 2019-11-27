---
title: Používání databází SQL na Azure Stack | Microsoft Docs
description: Přečtěte si, jak můžete nasadit databáze SQL jako službu v Azure Stack a rychlé kroky pro nasazení SQL Serverho adaptéru poskytovatele prostředků.
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
ms.date: 10/02/2019
ms.lastreviewed: 03/18/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.openlocfilehash: 4eb2936afc271016974440f77690c804f0cbcb09
ms.sourcegitcommit: 284f5316677c9a7f4c300177d0e2a905df8cb478
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2019
ms.locfileid: "74465303"
---
# <a name="deploy-the-sql-server-resource-provider-on-azure-stack"></a>Nasazení poskytovatele prostředků SQL Server v Azure Stack

K zveřejnění databáze SQL jako služby pro Azure Stack použijte poskytovatele prostředků Azure Stack SQL Server. Poskytovatel prostředků SQL se spouští jako služba na virtuálním počítači se systémem Windows Server 2016 Server Core (VM).

> [!IMPORTANT]
> Pouze poskytovatel prostředků je podporován k vytváření položek na serverech, které jsou hostiteli SQL nebo MySQL. Položky vytvořené na hostitelském serveru, které nejsou vytvořené poskytovatelem prostředků, můžou vést k neshodě stavu.

## <a name="prerequisites"></a>Požadavky

Aby bylo možné nasadit poskytovatele prostředků Azure Stack SQL, je nutné, aby bylo provedeno několik požadavků. Pokud chcete tyto požadavky splnit, proveďte v počítači, který má přístup k VIRTUÁLNÍmu počítači privilegovaného koncového bodu, následující kroky:

- Pokud jste to ještě neudělali, [zaregistrujte Azure Stack](azure-stack-registration.md) s Azure, abyste si mohli stáhnout položky Azure Marketplace.
- Moduly Azure a Azure Stack PowerShellu musíte nainstalovat do systému, kde budete spouštět tuto instalaci. Tento systém musí být bitová kopie systému Windows 10 nebo Windows Server 2016 s nejnovější verzí modulu .NET Runtime. Azure Stack najdete v tématu věnovaném [instalaci PowerShellu](./azure-stack-powershell-install.md).
- Přidejte požadovaný virtuální počítač se systémem Windows Server Core do webu Azure Stack Marketplace stažením **základní image Windows server 2016 Datacenter-Server** .
- Stáhněte si binární soubor poskytovatele prostředků SQL a potom spusťte samočinný extrahování a extrahujte obsah do dočasného adresáře. Poskytovatel prostředků má minimálně odpovídající sestavení Azure Stack.

  |Minimální verze Azure Stack|Verze SQL RP|
  |-----|-----|
  |Verze 1910 (1.1910.0.58)|[SQL RP verze 1.1.47.0](https://aka.ms/azurestacksqlrp11470)| 
  |Verze 1808 (1.1808.0.97)|[SQL RP verze 1.1.33.0](https://aka.ms/azurestacksqlrp11330)|  
  |Verze 1808 (1.1808.0.97)|[SQL RP verze 1.1.30.0](https://aka.ms/azurestacksqlrp11300)|  
  |Verze 1804 (1.0.180513.1)|[SQL RP verze 1.1.24.0](https://aka.ms/azurestacksqlrp11240)  
  |     |     |

> [!IMPORTANT]
> Před nasazením poskytovatele prostředků SQL verze 1.1.47.0 by měl být systém Azure Stack upgradovaný na verzi 1910 Update nebo novější. Verze poskytovatele prostředků SQL 1.1.47.0 v předchozích nepodporovaných verzích Azure Stack nefunguje.

- Ujistěte se, že jsou splněné předpoklady pro integraci Datacenter:

    |Požadavek|Odkaz|
    |-----|-----|
    |Podmíněné předávání DNS je nastaveno správně.|[Integrace Azure Stack Datacenter – DNS](azure-stack-integrate-dns.md)|
    |Příchozí porty pro poskytovatele prostředků jsou otevřené.|[Integrace Azure Stack Datacenter – příchozí porty a protokoly](azure-stack-integrate-endpoints.md#ports-and-protocols-inbound)|
    |Subjekt certifikátu PKI a síť SAN jsou nastavené správně.|[Povinné předpoklady PKI pro nasazení Azure Stack](azure-stack-pki-certs.md#mandatory-certificates)<br>[Požadavky na certifikát PaaS nasazení Azure Stack](azure-stack-pki-certs.md#optional-paas-certificates)|
    |     |     |

### <a name="certificates"></a>Certifikáty

_Pouze pro instalace integrovaných systémů_. Je nutné zadat certifikát PKI SQL PaaS popsaný v části volitelné certifikáty PaaS v tématu [Azure Stack nasazení infrastruktury veřejných klíčů](./azure-stack-pki-certs.md#optional-paas-certificates). Soubor. pfx umístěte do umístění zadaného parametrem **DependencyFilesLocalPath** . Neposkytněte certifikát pro systémy ASDK.

## <a name="deploy-the-sql-resource-provider"></a>Nasazení poskytovatele prostředků SQL

Po instalaci všech požadovaných součástí můžete spustit skript **DeploySqlProvider. ps1** pro nasazení poskytovatele prostředků SQL. Skript DeploySqlProvider. ps1 je extrahován jako součást binárního souboru poskytovatele prostředků SQL, který jste stáhli pro vaši verzi Azure Stack.

 > [!IMPORTANT]
 > Před nasazením poskytovatele prostředků si přečtěte poznámky k verzi, kde najdete informace o nových funkcích, opravách a známých problémech, které by mohly mít vliv na nasazení.
 
Pokud chcete nasadit poskytovatele prostředků SQL, otevřete **nové** okno prostředí PowerShell se zvýšenými oprávněními (ne PowerShell ISE) a přejděte do adresáře, do kterého jste extrahovali binární soubory poskytovatele prostředků SQL. Doporučujeme použít nové okno prostředí PowerShell, aby nedocházelo k potenciálním problémům způsobeným moduly prostředí PowerShell, které jsou již načteny.

Spusťte skript DeploySqlProvider. ps1, který dokončí následující úlohy:

- Nahraje certifikáty a jiné artefakty do účtu úložiště v Azure Stack.
- Publikuje balíčky galerie, takže můžete nasadit databáze SQL pomocí galerie.
- Publikuje balíček galerie pro nasazení hostitelských serverů.
- Nasadí virtuální počítač pomocí image Windows serveru 2016 Core, kterou jste stáhli, a potom nainstaluje poskytovatele prostředků SQL.
- Zaregistruje místní záznam DNS, který se mapuje na váš virtuální počítač poskytovatele prostředků.
- Zaregistruje poskytovatele prostředků s místní Azure Resource Manager pro účet operátora.

> [!NOTE]
> Po spuštění nasazení poskytovatele prostředků SQL se vytvoří skupina prostředků **System. Local. sqladapter** . Dokončení požadovaných nasazení do této skupiny prostředků může trvat až 75 minut.

### <a name="deploysqlproviderps1-parameters"></a>DeploySqlProvider. ps1 – parametry

Z příkazového řádku můžete zadat následující parametry. Pokud ne, nebo pokud se nějaké ověření parametru nepodaří, budete vyzváni k zadání požadovaných parametrů.

| Název parametru | Popis | Komentář nebo výchozí hodnota |
| --- | --- | --- |
| **CloudAdminCredential** | Přihlašovací údaje pro správce cloudu, které jsou nezbytné pro přístup k privilegovanému koncovému bodu. | _Požadovanou_ |
| **AzCredential** | Přihlašovací údaje pro účet správce služby Azure Stack Použijte stejné přihlašovací údaje, které jste použili pro nasazení Azure Stack. | _Požadovanou_ |
| **VMLocalCredential** | Pověření pro účet místního správce virtuálního počítače poskytovatele prostředků SQL. | _Požadovanou_ |
| **PrivilegedEndpoint** | IP adresa nebo název DNS privilegovaného koncového bodu. |  _Požadovanou_ |
| **AzureEnvironment** | Prostředí Azure účtu správce služby používaného pro nasazení Azure Stack. Vyžaduje se jenom pro nasazení Azure AD. Podporované názvy prostředí jsou **AzureCloud**, **AzureUSGovernment**nebo, pokud používáte Čína Azure Active Directory **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | V případě pouze integrovaných systémů musí být soubor Certificate. pfx umístěn v tomto adresáři. Volitelně můžete zkopírovat jeden web Windows Update balíček MSU zde. | _Volitelné_ (_povinné_ pro integrované systémy) |
| **DefaultSSLCertificatePassword** | Heslo pro certifikát. pfx. | _Požadovanou_ |
| **MaxRetryCount** | Počet pokusů o opakování všech operací, pokud dojde k selhání.| 2 |
| **RetryDuration** | Interval časového limitu mezi opakovanými pokusy (v sekundách). | 120 |
| **Odinstalace** | Odebere poskytovatele prostředků a všechny přidružené prostředky (viz následující poznámky). | Ne |
| **DebugMode** | Zabraňuje automatickému vyčištění při selhání. | Ne |

## <a name="deploy-the-sql-resource-provider-using-a-custom-script"></a>Nasazení poskytovatele prostředků SQL pomocí vlastního skriptu

Pokud nasazujete 1.1.33.0 nebo předchozí verze poskytovatele prostředků SQL, musíte do PowerShellu nainstalovat konkrétní verze AzureRm. zaváděcího nástroje a Azure Stack moduly. Pokud nasazujete poskytovatele prostředků SQL verze 1.1.47.0, tento krok se dá přeskočit.

```powershell
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
# Note that this might not be the most currently available version of Azure Stack PowerShell
Install-Module -Name AzureRm.BootStrapper -RequiredVersion 0.5.0 -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0
```

Chcete-li při nasazování poskytovatele prostředků eliminovat jakoukoli ruční konfiguraci, můžete přizpůsobit následující skript.  

Podle potřeby můžete změnit výchozí informace o účtu a hesla pro nasazení Azure Stack.

```powershell
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

Po dokončení instalačního skriptu poskytovatele prostředků aktualizujte prohlížeč, abyste se ujistili, že se vám zobrazí nejnovější aktualizace.

## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Ověření nasazení pomocí portálu Azure Stack

Pomocí následujících kroků můžete ověřit, jestli je poskytovatel prostředků SQL úspěšně nasazený.

1. Přihlaste se k portálu pro správu jako správce služby.
2. Vyberte **skupiny prostředků**.
3. Vyberte **umístění System.\<\>** skupinu prostředků. sqladapter.
4. Na stránce Souhrn pro skupinu prostředků by se neměla nasazovat žádná neúspěšná nasazení.
      ![ověřit nasazení poskytovatele prostředků SQL](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)
5. Nakonec vyberte **virtuální počítače** na portálu pro správu, abyste ověřili, že se virtuální počítač poskytovatele prostředků SQL úspěšně vytvořil a běží.

## <a name="next-steps"></a>Další kroky

[Přidat hostitelské servery](azure-stack-sql-resource-provider-hosting-servers.md)
