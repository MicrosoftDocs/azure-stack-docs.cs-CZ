---
title: Reference k rutině Start-AzsReadinessChecker | Microsoft Docs
description: Nápovědu k rutinám PowerShellu pro modul kontroly připravenosti na Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: 7d0e9c6914ce8748d842c9addf040355e3dc1cb1
ms.sourcegitcommit: aefcf9c61bd8089a0aaa569af7643e5e15f4947c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68991707"
---
# <a name="start-azsreadinesschecker-cmdlet-reference"></a>Reference k rutině Start-AzsReadinessChecker

Čipu **Microsoft.AzureStack.ReadinessChecker**

Tento modul obsahuje jenom jednu rutinu. Rutina provádí jednu nebo více funkcí předběžného nasazení nebo předběžného obsluhy pro Azure Stack.

## <a name="syntax"></a>Syntaxe

```powershell
Start-AzsReadinessChecker
       [-CertificatePath <String>]
       -PfxPassword <SecureString>
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       [-CertificatePath <String>]
       -PfxPassword <SecureString>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -PaaSCertificates <Hashtable>
       -DeploymentDataJSONPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -PaaSCertificates <Hashtable>
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       -Subject <OrderedDictionary>
       -RequestType <String>
       [-IncludePaaS]
       -OutputRequestPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -PfxPassword <SecureString>
       -PfxPath <String>
       -ExportPFXPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -AADServiceAdministrator <PSCredential>
       -AADDirectoryTenantName <String>
       -IdentitySystem <String>
       -AzureEnvironment <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -AADServiceAdministrator <PSCredential>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -RegistrationAccount <PSCredential>
       -RegistrationSubscriptionID <Guid>
       -AzureEnvironment <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -RegistrationAccount <PSCredential>
       -RegistrationSubscriptionID <Guid>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -ReportPath <String>
       [-ReportSections <String>]
       [-Summary]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

## <a name="description"></a>Popis

Rutina **Start-AzsReadinessChecker** ověřuje certifikáty, účty Azure, předplatná Azure a Azure Active Directory. Před nasazením Azure Stack nebo před Azure Stack akcí údržby, jako je například střídání tajných klíčů, spusťte ověřování. Rutina se dá použít taky ke generování žádostí o podepsání certifikátu pro certifikáty infrastruktury a volitelně i k PaaS certifikátům. Nakonec může rutina znovu zabalit certifikáty PFX k nápravě běžných problémů s balíčkem.

## <a name="examples"></a>Příklady

### <a name="example-generate-certificate-signing-request"></a>Příklad: vygenerování žádosti o podepsání certifikátu

```powershell
$regionName = 'east'
$externalFQDN = 'azurestack.contoso.com'
$subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"}
Start-AzsReadinessChecker -regionName $regionName -externalFQDN $externalFQDN -subject $subjectHash -IdentitySystem ADFS -requestType MultipleCSR
```

V tomto příkladu `Start-AzsReadinessChecker` vygeneruje více žádostí o podepsání certifikátů, které jsou vhodné pro AD FS Azure Stack nasazení, s názvem oblasti **východ** a s externím plně kvalifikovaným názvem domény **azurestack.contoso.com**.

### <a name="example-validate-certificates"></a>Příklad: ověření certifikátů

```powershell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
```

V tomto příkladu je vyžadováno heslo PFX pro zabezpečení a `Start-AzsReadinessChecker` kontroluje **certifikáty** relativních složek pro certifikáty platné pro nasazení AAD s názvem oblasti **východ** a externím plně kvalifikovaným názvem domény **azurestack.contoso.com.** .

### <a name="example-validate-certificates-with-deployment-data-deployment-and-support"></a>Příklad: ověření certifikátů s daty nasazení (nasazení a podpora)

```powershell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -DeploymentDataJSONPath .\deploymentdata.json
```

V tomto příkladu nasazení a podpory se vyžaduje heslo PFX pro zabezpečení a `Start-AzsReadinessChecker` kontroluje **certifikáty** relativních složek pro certifikáty platné pro nasazení, kde se identita, oblast a externí plně kvalifikovaný název domény čtou z nasazení. pro nasazení byl vygenerován datový soubor JSON.

### <a name="example-validate-paas-certificates"></a>Příklad: ověření certifikátů PaaS

```powershell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates -RegionName east -FQDN azurestack.contoso.com
```

V tomto příkladu je zatřiďovací tabulka vytvořená s cestami a hesly pro každý PaaS certifikát. Certifikáty je možné vynechat. `Start-AzsReadinessChecker`kontroluje, zda všechny cesty PFX existují, a ověřuje je pomocí oblasti **východ** a externího plně kvalifikovaného názvu domény **azurestack.contoso.com**.

### <a name="example-validate-paas-certificates-with-deployment-data"></a>Příklad: ověření certifikátů PaaS pomocí dat nasazení

```powershell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates -DeploymentDataJSONPath .\deploymentdata.json
```

V tomto příkladu je zatřiďovací tabulka vytvořená s cestami a hesly pro každý PaaS certifikát. Certifikáty je možné vynechat. `Start-AzsReadinessChecker`kontroluje, zda všechny cesty PFX existují, a ověřuje je pomocí oblasti a externího plně kvalifikovaného názvu domény načtené ze souboru JSON dat nasazení vygenerovaného pro nasazení.

### <a name="example-validate-azure-identity"></a>Příklad: ověření identity Azure

```powershell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment "<environment name>" -AzureDirectoryTenantName azurestack.contoso.com
```

V tomto příkladu jsou pro zabezpečení vyžadovány přihlašovací údaje účtu správce služby a `Start-AzsReadinessChecker` kontroluje, že účet Azure a Azure Active Directory jsou platné pro nasazení AAD s názvem adresáře klienta **azurestack.contoso.com.** .

### <a name="example-validate-azure-identity-with-deployment-data-deployment-support"></a>Příklad: ověření identity Azure pomocí dat nasazení (podpora nasazení)

```PowerShell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -DeploymentDataJSONPath .\contoso-deploymentdata.json
```

V tomto příkladu jsou pro zabezpečení vyžadovány přihlašovací údaje účtu správce služby a `Start-AzsReadinessChecker` kontrolují, zda je účet Azure a Azure Active Directory platný pro nasazení AAD, kde se **AzureCloud** a **tenant** přečtou. ze souboru JSON dat nasazení vygenerovaného pro nasazení.

### <a name="example-validate-azure-registration"></a>Příklad: Ověření registrace Azure

```powershell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -AzureEnvironment "<environment name>"
```

V tomto příkladu jsou pro zabezpečení vyžadovány přihlašovací údaje vlastníka předplatného `Start-AzsReadinessChecker` a potom provádí ověřování proti danému účtu a předplatnému, aby bylo možné je použít k registraci Azure Stack.

### <a name="example-validate-azure-registration-with-deployment-data-deployment-team"></a>Příklad: Ověření registrace Azure pomocí dat nasazení (tým nasazení)

```powershell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID>"
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -DeploymentDataJSONPath .\contoso-deploymentdata.json
```

V tomto příkladu jsou pro zabezpečení vyžadovány přihlašovací údaje vlastníka předplatného `Start-AzsReadinessChecker` a potom provádí ověřování proti danému účtu a předplatnému, aby bylo možné je použít k registraci Azure Stack, kde se další podrobnosti čtou z pro nasazení se vygeneroval soubor JSON pro data nasazení.

### <a name="example-importexport-pfx-package"></a>Příklad: import/export balíčku PFX

```powershell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx
```

V tomto příkladu je pro zabezpečení vyžadováno heslo PFX. Soubor SSL. pfx se naimportuje do úložiště certifikátů místního počítače, znovu se vyexportuje se stejným heslem a uloží se jako Ssl_new. pfx. Tento postup se používá v případě, že ověření certifikátu označilo příznak, že privátní klíč nemá nastaven atribut **místního počítače** , že je řetěz certifikátů poškozený, že se v něm nachází důležité certifikáty nebo že řetěz certifikátů je v nesprávném pořadí.

### <a name="example-view-validation-report-deployment-and-support"></a>Příklad: zobrazení sestavy ověření (nasazení a podpora)

```powershell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json
```

V tomto příkladu tým nasazení nebo podpory obdrží sestavu připravenosti od zákazníka (Contoso) a použije `Start-AzsReadinessChecker` se k zobrazení stavu provedených ověření, které provádí společnost Contoso.

### <a name="example-view-validation-report-summary-for-certificate-validation-only-deployment-and-support"></a>Příklad: zobrazení souhrnu sestavy ověření pouze pro ověření certifikátu (nasazení a podpora)

```powershell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json -ReportSections Certificate -Summary
```

V tomto příkladu tým nasazení nebo podpory obdrží zprávu o připravenosti od zákazníka (Contoso) a používá `Start-AzsReadinessChecker` nástroj k zobrazení souhrnného stavu provedených provedení ověření certifikátu contoso.

## <a name="required-parameters"></a>Požadované parametry

### <a name="-regionname"></a>– RegionName

Určuje název oblasti nasazení Azure Stack.

|  |  |
|----------------------------|--------------|
|Zadejte:                       |Řetězec        |
|Pozice:                   |Jmenovanou         |
|Výchozí hodnota:              |Žádné          |
|Přijmout vstup kanálu:      |False         |
|Přijmout zástupné znaky: |False         |

### <a name="-fqdn"></a>-FQDN

Určuje externí plně kvalifikovaný název domény pro nasazení Azure Stack a také aliasy jako **ExternalFQDN** a **ExternalDomainName**.

|  |  |
|----------------------------|--------------|
|Zadejte:                       |Řetězec        |
|Pozice:                   |Jmenovanou         |
|Výchozí hodnota:              |ExternalFQDN, ExternalDomainName |
|Přijmout vstup kanálu:      |False         |
|Přijmout zástupné znaky: |False         |

### <a name="-identitysystem"></a>-IdentitySystem

Určuje Azure Stack platných hodnot systému identity nasazení, AAD nebo ADFS pro Azure Active Directory a federované služby Active Directory v uvedeném pořadí.

|  |  |
|----------------------------|--------------|
|Zadejte:                       |Řetězec        |
|Pozice:                   |Jmenovanou         |
|Výchozí hodnota:              |Žádné          |
|Platné hodnoty:               |AAD, ADFS  |
|Přijmout vstup kanálu:      |False         |
|Přijmout zástupné znaky: |False         |

### <a name="-pfxpassword"></a>-PfxPassword

Určuje heslo přidružené k souborům certifikátů PFX.

|  |  |
|----------------------------|---------|
|Zadejte:                       |SecureString |
|Pozice:                   |Jmenovanou    |
|Výchozí hodnota:              |Žádné     |
|Přijmout vstup kanálu:      |False    |
|Přijmout zástupné znaky: |False    |

### <a name="-paascertificates"></a>-PaaSCertificates

Určuje tabulku hash obsahující cesty a hesla pro PaaS certifikátů.

|  |  |
|----------------------------|---------|
|Zadejte:                       |Hashtable |
|Pozice:                   |Jmenovanou    |
|Výchozí hodnota:              |Žádné     |
|Přijmout vstup kanálu:      |False    |
|Přijmout zástupné znaky: |False    |

### <a name="-deploymentdatajsonpath"></a>-DeploymentDataJSONPath

Určuje konfigurační soubor JSON Azure Stackch dat nasazení. Tento soubor je vygenerovaný pro nasazení.

|  |  |
|----------------------------|---------|
|Zadejte:                       |Řetězec   |
|Pozice:                   |Jmenovanou    |
|Výchozí hodnota:              |Žádné     |
|Přijmout vstup kanálu:      |False    |
|Přijmout zástupné znaky: |False    |

### <a name="-pfxpath"></a>-PfxPath

Určuje cestu k problematickému certifikátu, který vyžaduje, aby se opravila rutina import/export, jak je uvedeno v tomto nástroji v části ověření certifikátu.

|  |  |
|----------------------------|---------|
|Zadejte:                       |Řetězec   |
|Pozice:                   |Jmenovanou    |
|Výchozí hodnota:              |Žádné     |
|Přijmout vstup kanálu:      |False    |
|Přijmout zástupné znaky: |False    |

### <a name="-exportpfxpath"></a>-ExportPFXPath  

Určuje cílovou cestu pro výsledný soubor PFX z rutiny Import/export.  

|  |  |
|----------------------------|---------|
|Zadejte:                       |Řetězec   |
|Pozice:                   |Jmenovanou    |
|Výchozí hodnota:              |Žádné     |
|Přijmout vstup kanálu:      |False    |
|Přijmout zástupné znaky: |False    |

### <a name="-subject"></a>– Předmět

Určuje seřazený slovník předmětu pro generování žádosti o certifikát.

|  |  |
|----------------------------|---------|
|Zadejte:                       |Kolekce OrderedDictionary   |
|Pozice:                   |Jmenovanou    |
|Výchozí hodnota:              |Žádné     |
|Přijmout vstup kanálu:      |False    |
|Přijmout zástupné znaky: |False    |

### <a name="-requesttype"></a>– RequestType

Určuje typ sítě SAN žádosti o certifikát. Platné hodnoty jsou **MultipleCSR**, **SingleCSR**.

- **MultipleCSR** vygeneruje více žádostí o certifikát, jednu pro každou službu.
- **SingleCSR** vygeneruje jednu žádost o certifikát pro všechny služby.

|  |  |
|----------------------------|---------|
|Zadejte:                       |Řetězec   |
|Pozice:                   |Jmenovanou    |
|Výchozí hodnota:              |Žádné     |
|Platné hodnoty:               |'MultipleCSR','SingleCSR' |
|Přijmout vstup kanálu:      |False    |
|Přijmout zástupné znaky: |False    |

### <a name="-outputrequestpath"></a>-OutputRequestPath

Určuje cílovou cestu pro soubory žádostí o certifikát. Adresář již musí existovat.

|  |  |
|----------------------------|---------|
|Zadejte:                       |Řetězec   |
|Pozice:                   |Jmenovanou    |
|Výchozí hodnota:              |Žádné     |
|Přijmout vstup kanálu:      |False    |
|Přijmout zástupné znaky: |False    |

### <a name="-aadserviceadministrator"></a>-AADServiceAdministrator

Určuje správce služby Azure Active Directory, který se má použít pro Azure Stack nasazení.

|  |  |
|----------------------------|---------|
|Zadejte:                       |PSCredential   |
|Pozice:                   |Jmenovanou    |
|Výchozí hodnota:              |Žádné     |
|Přijmout vstup kanálu:      |False    |
|Přijmout zástupné znaky: |False    |

### <a name="-aaddirectorytenantname"></a>-AADDirectoryTenantName

Určuje Azure Active Directory název, který se má použít pro Azure Stack nasazení.

|  |  |
|----------------------------|---------|
|Zadejte:                       |Řetězec   |
|Pozice:                   |Jmenovanou    |
|Výchozí hodnota:              |Žádné     |
|Přijmout vstup kanálu:      |False    |
|Přijmout zástupné znaky: |False    |

### <a name="-azureenvironment"></a>– AzureEnvironment

Určuje instanci služeb Azure, které obsahují účty, adresáře a odběry, které se mají použít pro Azure Stack nasazení a registraci.

|  |  |
|----------------------------|---------|
|Zadejte:                       |Řetězec   |
|Pozice:                   |Jmenovanou    |
|Výchozí hodnota:              |Žádné     |
|Platné hodnoty:               |'AzureCloud','AzureChinaCloud','AzureUSGovernment' |
|Přijmout vstup kanálu:      |False    |
|Přijmout zástupné znaky: |False    |

### <a name="-registrationaccount"></a>-RegistrationAccount

Určuje registrační účet, který se má použít pro Azure Stack registraci.

|  |  |
|----------------------------|---------|
|Zadejte:                       |Řetězec   |
|Pozice:                   |Jmenovanou    |
|Výchozí hodnota:              |Žádné     |
|Přijmout vstup kanálu:      |False    |
|Přijmout zástupné znaky: |False    |

### <a name="-registrationsubscriptionid"></a>-RegistrationSubscriptionID

Určuje ID předplatného registrace, které se má použít pro Azure Stack registraci.

|  |  |
|----------------------------|---------|
|Zadejte:                       |Guid     |
|Pozice:                   |Jmenovanou    |
|Výchozí hodnota:              |Žádné     |
|Přijmout vstup kanálu:      |False    |
|Přijmout zástupné znaky: |False    |

### <a name="-reportpath"></a>-ReportPath

Určuje cestu k sestavě připravenosti, výchozí hodnota je aktuální adresář a výchozí název sestavy.

|  |  |
|----------------------------|---------|
|Zadejte:                       |Řetězec   |
|Pozice:                   |Jmenovanou    |
|Výchozí hodnota:              |Vše      |
|Přijmout vstup kanálu:      |False    |
|Přijmout zástupné znaky: |False    |

## <a name="optional-parameters"></a>Volitelné parametry

### <a name="-certificatepath"></a>-CertificatePath

Určuje cestu, pod kterou jsou přítomny pouze složky certifikátů, které jsou vyžadovány.

Požadované složky pro nasazení Azure Stack se systémem Azure Active Directory identity System:

ACSBlob, ACSQueue, ACSTable, portál pro správu, správce ARM, Public, ARM, Trezor klíčů, KeyVaultInternal, veřejný portál

Požadovaná složka pro nasazení Azure Stack se systémem Active Directory Federation Services (AD FS) identity je:

ACSBlob, ACSQueue, ACSTable, ADFS, portál pro správu, správce ARM, veřejný procesor, graf, Trezor klíčů, KeyVaultInternal, veřejný portál

|  |  |
|----------------------------|---------|
|Zadejte:                       |Řetězec   |
|Pozice:                   |Jmenovanou    |
|Výchozí hodnota:              |.\Certificates |
|Přijmout vstup kanálu:      |False    |
|Přijmout zástupné znaky: |False    |

### <a name="-includepaas"></a>-IncludePaaS  

Určuje, jestli se mají do žádostí o certifikát přidat PaaS služby nebo názvy hostitelů.

|  |  |
|----------------------------|------------------|
|Zadejte:                       |Přepínací parametr   |
|Pozice:                   |Jmenovanou             |
|Výchozí hodnota:              |False             |
|Přijmout vstup kanálu:      |False             |
|Přijmout zástupné znaky: |False             |

### <a name="-reportsections"></a>-ReportSections

Určuje, jestli se má zobrazit jenom souhrn sestavy, vynechává se podrobnosti.

|  |  |
|----------------------------|---------|
|Zadejte:                       |Řetězec   |
|Pozice:                   |Jmenovanou    |
|Výchozí hodnota:              |Vše      |
|Platné hodnoty:               |"Certificate", "AzureRegistration", "AzureIdentity", "Jobs", "All" |
|Přijmout vstup kanálu:      |False    |
|Přijmout zástupné znaky: |False    |

### <a name="-summary"></a>-Summary

Určuje, jestli se má zobrazit jenom souhrn sestavy, vynechává se podrobnosti.

|  |  |
|----------------------------|------------------|
|Zadejte:                       |Přepínací parametr   |
|Pozice:                   |Jmenovanou             |
|Výchozí hodnota:              |False             |
|Přijmout vstup kanálu:      |False             |
|Přijmout zástupné znaky: |False             |

### <a name="-cleanreport"></a>-CleanReport

Odebere předchozí spuštění a historii ověření a zapíše ověření do nové sestavy.

|  |  |
|----------------------------|------------------|
|Zadejte:                       |Přepínací parametr   |
|Hromad                    |CF                |
|Pozice:                   |Jmenovanou             |
|Výchozí hodnota:              |False             |
|Přijmout vstup kanálu:      |False             |
|Přijmout zástupné znaky: |False             |

### <a name="-outputpath"></a>– OutputPath

Určuje vlastní cestu k uložení sestavy JSON připravenosti a podrobného souboru protokolu. Pokud cesta ještě neexistuje, příkaz se pokusí vytvořit adresář.

|  |  |
|----------------------------|------------------|
|Zadejte:                       |Řetězec            |
|Pozice:                   |Jmenovanou             |
|Výchozí hodnota:              |$ENV: TEMP\AzsReadinessChecker  |
|Přijmout vstup kanálu:      |False             |
|Přijmout zástupné znaky: |False             |

### <a name="-confirm"></a>– Potvrzení

Před spuštěním rutiny zobrazí výzvu k potvrzení.

|  |  |
|----------------------------|------------------|
|Zadejte:                       |Přepínací parametr   |
|Hromad                    |CF                |
|Pozice:                   |Jmenovanou             |
|Výchozí hodnota:              |False             |
|Přijmout vstup kanálu:      |False             |
|Přijmout zástupné znaky: |False             |

### <a name="-whatif"></a>-WhatIf

Ukazuje, co se stane, když se rutina spustí. Rutina není spuštěna.

|  |  |
|----------------------------|------------------|
|Zadejte:                       |Přepínací parametr   |
|Hromad                    |Wi                |
|Pozice:                   |Jmenovanou             |
|Výchozí hodnota:              |False             |
|Přijmout vstup kanálu:      |False             |
|Přijmout zástupné znaky: |False             |
