---
title: Reference k rutině Start-AzsReadinessChecker
description: Nápovědu k rutinám PowerShellu pro modul kontroly připravenosti centra Azure Stack
author: IngridAtMicrosoft
ms.topic: reference
ms.date: 03/04/2020
ms.author: inhenkel
ms.reviewer: unknown
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: 0b81915cd7a95bede1edcbfa0f84ea8a5f38513d
ms.sourcegitcommit: 1fa0140481a483e5c27f602386fe1fae77ad29f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78366056"
---
# <a name="start-azsreadinesschecker-cmdlet-reference"></a>Reference k rutině Start-AzsReadinessChecker

Modul: **Microsoft. AzureStack. ReadinessChecker**

Tento modul obsahuje jenom jednu rutinu. Rutina provádí jednu nebo více funkcí předběžného nasazení nebo předobsluhy, které jsou Azure Stack centra.

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

Rutina **Start-AzsReadinessChecker** ověřuje certifikáty, účty Azure, předplatná Azure a Azure Active Directory (Azure AD). Před nasazením centra Azure Stack nebo před Azure Stack akce obsluhy centra, jako je například rotace tajných klíčů, spusťte ověřování. Rutina se dá použít taky ke generování žádostí o podepsání certifikátu pro certifikáty infrastruktury a volitelně i k PaaS certifikátům. Nakonec může rutina znovu zabalit certifikáty PFX k nápravě běžných problémů s balíčkem.

## <a name="examples"></a>Příklady

### <a name="example-generate-certificate-signing-request"></a>Příklad: vygenerování žádosti o podepsání certifikátu

```powershell
$regionName = 'east'
$externalFQDN = 'azurestack.contoso.com'
$subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"}
Start-AzsReadinessChecker -regionName $regionName -externalFQDN $externalFQDN -subject $subjectHash -IdentitySystem ADFS -requestType MultipleCSR
```

V tomto příkladu `Start-AzsReadinessChecker` vygeneruje více žádostí o podepsání certifikátů, které jsou vhodné pro AD FS Azure Stack nasazení centra s názvem oblasti **východ** a externím plně kvalifikovaným názvem domény **azurestack.contoso.com**.

### <a name="example-validate-certificates"></a>Příklad: ověření certifikátů

```powershell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
```

V tomto příkladu je vyžadováno heslo PFX pro zabezpečení a `Start-AzsReadinessChecker` kontroluje **certifikáty** relativních složek pro certifikáty platné pro nasazení Azure AD s názvem oblasti **východ** a s externím plně kvalifikovaným názvem domény **azurestack.contoso.com**.

### <a name="example-validate-certificates-with-deployment-data-deployment-and-support"></a>Příklad: ověření certifikátů s daty nasazení (nasazení a podpora)

```powershell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -DeploymentDataJSONPath .\deploymentdata.json
```

V tomto příkladu nasazení a podpory se vyžaduje heslo PFX pro zabezpečení a `Start-AzsReadinessChecker` kontroluje **certifikáty** relativní složky pro certifikáty platné pro nasazení, kde se identita, oblast a externí plně kvalifikovaný název domény čtou ze souboru JSON dat nasazení vygenerovaného pro nasazení.

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

V tomto příkladu je zatřiďovací tabulka vytvořená s cestami a hesly pro každý PaaS certifikát. Certifikáty je možné vynechat. `Start-AzsReadinessChecker` kontroluje, zda všechny cesty PFX existují a ověřují je pomocí oblasti **východ** a externího plně kvalifikovaného názvu domény **azurestack.contoso.com**.

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

V tomto příkladu je zatřiďovací tabulka vytvořená s cestami a hesly pro každý PaaS certifikát. Certifikáty je možné vynechat. `Start-AzsReadinessChecker` kontroluje, zda všechny cesty PFX existují, a ověří je pomocí oblasti a externího plně kvalifikovaného názvu domény načteného ze souboru JSON dat nasazení vygenerovaného pro nasazení.

### <a name="example-validate-azure-identity"></a>Příklad: ověření identity Azure

```powershell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment "<environment name>" -AzureDirectoryTenantName azurestack.contoso.com
```

V tomto příkladu jsou pro zabezpečení vyžadovány přihlašovací údaje účtu správce služby a `Start-AzsReadinessChecker` kontroluje, že účet Azure a Azure AD jsou platné pro nasazení služby Azure AD s názvem adresáře klienta **azurestack.contoso.com**.

### <a name="example-validate-azure-identity-with-deployment-data-deployment-support"></a>Příklad: ověření identity Azure pomocí dat nasazení (podpora nasazení)

```PowerShell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -DeploymentDataJSONPath .\contoso-deploymentdata.json
```

V tomto příkladu jsou pro zabezpečení vyžadovány přihlašovací údaje účtu správce služby a `Start-AzsReadinessChecker` kontroluje, zda je účet Azure a služba Azure AD platné pro nasazení služby Azure AD, kde **AzureCloud** a **tenant** jsou čteny ze souboru JSON dat nasazení vygenerovaného pro nasazení.

### <a name="example-validate-azure-registration"></a>Příklad: Ověření registrace Azure

```powershell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -AzureEnvironment "<environment name>"
```

V tomto příkladu jsou pro zabezpečení vyžadovány přihlašovací údaje vlastníka předplatného a `Start-AzsReadinessChecker` potom provádí ověřování proti danému účtu a předplatnému, aby bylo zajištěno, že je možné ho použít pro registraci centra Azure Stack.

### <a name="example-validate-azure-registration-with-deployment-data-deployment-team"></a>Příklad: Ověření registrace Azure pomocí dat nasazení (tým nasazení)

```powershell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID>"
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -DeploymentDataJSONPath .\contoso-deploymentdata.json
```

V tomto příkladu jsou pro zabezpečení vyžadovány přihlašovací údaje vlastníka předplatného a `Start-AzsReadinessChecker` potom provádí ověřování proti danému účtu a předplatnému, aby bylo zajištěno, že je možné ho použít pro registraci Azure Stack centra, kde jsou další podrobnosti čteny ze souboru JSON dat nasazení vygenerovaného pro nasazení.

### <a name="example-importexport-pfx-package"></a>Příklad: import/export balíčku PFX

```powershell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx
```

V tomto příkladu je pro zabezpečení vyžadováno heslo PFX. Soubor SSL. pfx se naimportuje do úložiště certifikátů místního počítače, znovu se exportuje se stejným heslem a uloží se jako Ssl_new. pfx. Tento postup se používá v případě, že příznaky ověřování certifikátů, u kterých privátní klíč nemá nastaven atribut **místního počítače** , jsou přerušeny, jsou v souboru PFX k dispozici důležité certifikáty, nebo je řetěz certifikátů v nesprávném pořadí.

### <a name="example-view-validation-report-deployment-and-support"></a>Příklad: zobrazení sestavy ověření (nasazení a podpora)

```powershell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json
```

V tomto příkladu tým nasazení nebo podpory obdrží sestavu připravenosti od zákazníka (Contoso) a pomocí `Start-AzsReadinessChecker` zobrazí stav provedených ověřování contoso.

### <a name="example-view-validation-report-summary-for-certificate-validation-only-deployment-and-support"></a>Příklad: zobrazení souhrnu sestavy ověření pouze pro ověření certifikátu (nasazení a podpora)

```powershell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json -ReportSections Certificate -Summary
```

V tomto příkladu tým nasazení nebo podpory obdrží zprávu připravenost od zákazníka (Contoso) a používá `Start-AzsReadinessChecker` k zobrazení souhrnného stavu provedených provedení ověření certifikátu contoso.

## <a name="required-parameters"></a>Požadované parametry

### <a name="-regionname"></a>– RegionName

Určuje název oblasti nasazení centra Azure Stack.

|  |  |
|----------------------------|--------------|
|Zadejte:                       |Řetězec        |
|Poziční                   |Jmenovanou         |
|Výchozí hodnota:              |Žádná          |
|Přijmout vstup kanálu:      |False         |
|Přijmout zástupné znaky: |False         |

### <a name="-fqdn"></a>-FQDN

Určuje externí plně kvalifikovaný název domény pro nasazení centra Azure Stack a také aliasy jako **ExternalFQDN** a **ExternalDomainName**.

|  |  |
|----------------------------|--------------|
|Zadejte:                       |Řetězec        |
|Poziční                   |Jmenovanou         |
|Výchozí hodnota:              |ExternalFQDN, ExternalDomainName |
|Přijmout vstup kanálu:      |False         |
|Přijmout zástupné znaky: |False         |

### <a name="-identitysystem"></a>-IdentitySystem

Určuje Azure Stack platné hodnoty, AAD nebo ADFS v rámci nasazení centra pro Azure Active Directory a federované služby Active Directory v uvedeném pořadí.

|  |  |
|----------------------------|--------------|
|Zadejte:                       |Řetězec        |
|Poziční                   |Jmenovanou         |
|Výchozí hodnota:              |Žádná          |
|Platné hodnoty:               |AAD, ADFS  |
|Přijmout vstup kanálu:      |False         |
|Přijmout zástupné znaky: |False         |

### <a name="-pfxpassword"></a>-PfxPassword

Určuje heslo přidružené k souborům certifikátů PFX.

|  |  |
|----------------------------|---------|
|Zadejte:                       |SecureString |
|Poziční                   |Jmenovanou    |
|Výchozí hodnota:              |Žádná     |
|Přijmout vstup kanálu:      |False    |
|Přijmout zástupné znaky: |False    |

### <a name="-paascertificates"></a>-PaaSCertificates

Určuje tabulku hash obsahující cesty a hesla pro PaaS certifikátů.

|  |  |
|----------------------------|---------|
|Zadejte:                       |Hashtable |
|Poziční                   |Jmenovanou    |
|Výchozí hodnota:              |Žádná     |
|Přijmout vstup kanálu:      |False    |
|Přijmout zástupné znaky: |False    |

### <a name="-deploymentdatajsonpath"></a>-DeploymentDataJSONPath

Určuje konfigurační soubor JSON Azure Stackch dat nasazení centra. Tento soubor je vygenerovaný pro nasazení.

|  |  |
|----------------------------|---------|
|Zadejte:                       |Řetězec   |
|Poziční                   |Jmenovanou    |
|Výchozí hodnota:              |Žádná     |
|Přijmout vstup kanálu:      |False    |
|Přijmout zástupné znaky: |False    |

### <a name="-pfxpath"></a>-PfxPath

Určuje cestu k problematickému certifikátu, který vyžaduje, aby se opravila rutina import/export, jak je uvedeno v tomto nástroji v části ověření certifikátu.

|  |  |
|----------------------------|---------|
|Zadejte:                       |Řetězec   |
|Poziční                   |Jmenovanou    |
|Výchozí hodnota:              |Žádná     |
|Přijmout vstup kanálu:      |False    |
|Přijmout zástupné znaky: |False    |

### <a name="-exportpfxpath"></a>-ExportPFXPath  

Určuje cílovou cestu pro výsledný soubor PFX z rutiny Import/export.  

|  |  |
|----------------------------|---------|
|Zadejte:                       |Řetězec   |
|Poziční                   |Jmenovanou    |
|Výchozí hodnota:              |Žádná     |
|Přijmout vstup kanálu:      |False    |
|Přijmout zástupné znaky: |False    |

### <a name="-subject"></a>– Předmět

Určuje seřazený slovník předmětu pro generování žádosti o certifikát.

|  |  |
|----------------------------|---------|
|Zadejte:                       |Kolekce OrderedDictionary   |
|Poziční                   |Jmenovanou    |
|Výchozí hodnota:              |Žádná     |
|Přijmout vstup kanálu:      |False    |
|Přijmout zástupné znaky: |False    |

### <a name="-requesttype"></a>– RequestType

Určuje typ sítě SAN žádosti o certifikát. Platné hodnoty jsou **MultipleCSR**, **SingleCSR**.

- **MultipleCSR** vygeneruje více žádostí o certifikát, jednu pro každou službu.
- **SingleCSR** vygeneruje jednu žádost o certifikát pro všechny služby.

|  |  |
|----------------------------|---------|
|Zadejte:                       |Řetězec   |
|Poziční                   |Jmenovanou    |
|Výchozí hodnota:              |Žádná     |
|Platné hodnoty:               |'MultipleCSR','SingleCSR' |
|Přijmout vstup kanálu:      |False    |
|Přijmout zástupné znaky: |False    |

### <a name="-outputrequestpath"></a>-OutputRequestPath

Určuje cílovou cestu pro soubory žádostí o certifikát. Adresář již musí existovat.

|  |  |
|----------------------------|---------|
|Zadejte:                       |Řetězec   |
|Poziční                   |Jmenovanou    |
|Výchozí hodnota:              |Žádná     |
|Přijmout vstup kanálu:      |False    |
|Přijmout zástupné znaky: |False    |

### <a name="-aadserviceadministrator"></a>-AADServiceAdministrator

Určuje správce služby Azure AD, který se má použít pro nasazení centra Azure Stack.

|  |  |
|----------------------------|---------|
|Zadejte:                       |PSCredential   |
|Poziční                   |Jmenovanou    |
|Výchozí hodnota:              |Žádná     |
|Přijmout vstup kanálu:      |False    |
|Přijmout zástupné znaky: |False    |

### <a name="-aaddirectorytenantname"></a>-AADDirectoryTenantName

Určuje název služby Azure AD, který se má použít pro nasazení centra Azure Stack.

|  |  |
|----------------------------|---------|
|Zadejte:                       |Řetězec   |
|Poziční                   |Jmenovanou    |
|Výchozí hodnota:              |Žádná     |
|Přijmout vstup kanálu:      |False    |
|Přijmout zástupné znaky: |False    |

### <a name="-azureenvironment"></a>– AzureEnvironment

Určuje instanci služeb Azure, které obsahují účty, adresáře a odběry, které se mají použít pro nasazení a registraci centra Azure Stack.

|  |  |
|----------------------------|---------|
|Zadejte:                       |Řetězec   |
|Poziční                   |Jmenovanou    |
|Výchozí hodnota:              |Žádná     |
|Platné hodnoty:               |'AzureCloud','AzureChinaCloud','AzureUSGovernment' |
|Přijmout vstup kanálu:      |False    |
|Přijmout zástupné znaky: |False    |

### <a name="-registrationaccount"></a>-RegistrationAccount

Určuje registrační účet, který se má použít pro registraci centra Azure Stack.

|  |  |
|----------------------------|---------|
|Zadejte:                       |Řetězec   |
|Poziční                   |Jmenovanou    |
|Výchozí hodnota:              |Žádná     |
|Přijmout vstup kanálu:      |False    |
|Přijmout zástupné znaky: |False    |

### <a name="-registrationsubscriptionid"></a>-RegistrationSubscriptionID

Určuje ID předplatného registrace, které se má použít pro registraci centra Azure Stack.

|  |  |
|----------------------------|---------|
|Zadejte:                       |identifikátor GUID     |
|Poziční                   |Jmenovanou    |
|Výchozí hodnota:              |Žádná     |
|Přijmout vstup kanálu:      |False    |
|Přijmout zástupné znaky: |False    |

### <a name="-reportpath"></a>-ReportPath

Určuje cestu k sestavě připravenosti, výchozí hodnota je aktuální adresář a výchozí název sestavy.

|  |  |
|----------------------------|---------|
|Zadejte:                       |Řetězec   |
|Poziční                   |Jmenovanou    |
|Výchozí hodnota:              |Vše      |
|Přijmout vstup kanálu:      |False    |
|Přijmout zástupné znaky: |False    |

## <a name="optional-parameters"></a>Volitelné parametry

### <a name="-certificatepath"></a>-CertificatePath

Určuje cestu, pod kterou jsou přítomny pouze složky certifikátů, které jsou vyžadovány.

Požadované složky pro nasazení centra Azure Stack se systémem Azure AD identity System jsou:

- ACSBlob, ACSQueue, ACSTable, portál pro správu, správce ARM, Public, ARM, Trezor klíčů, KeyVaultInternal, veřejný portál

Požadované složky pro nasazení centra Azure Stack se systémem Active Directory Federation Services (AD FS) identity:

- ACSBlob, ACSQueue, ACSTable, ADFS, portál pro správu, správce ARM, veřejný procesor, graf, Trezor klíčů, KeyVaultInternal, veřejný portál

|  |  |
|----------------------------|---------|
|Zadejte:                       |Řetězec   |
|Poziční                   |Jmenovanou    |
|Výchozí hodnota:              |.\Certificates |
|Přijmout vstup kanálu:      |False    |
|Přijmout zástupné znaky: |False    |

### <a name="-includepaas"></a>-IncludePaaS  

Určuje, jestli se mají do žádostí o certifikát přidat PaaS služby nebo názvy hostitelů.

|  |  |
|----------------------------|------------------|
|Zadejte:                       |Přepínací parametr   |
|Poziční                   |Jmenovanou             |
|Výchozí hodnota:              |False             |
|Přijmout vstup kanálu:      |False             |
|Přijmout zástupné znaky: |False             |

### <a name="-reportsections"></a>-ReportSections

Určuje, jestli se má zobrazit jenom souhrn sestavy, vynechává se podrobnosti.

|  |  |
|----------------------------|---------|
|Zadejte:                       |Řetězec   |
|Poziční                   |Jmenovanou    |
|Výchozí hodnota:              |Vše      |
|Platné hodnoty:               |"Certificate", "AzureRegistration", "AzureIdentity", "Jobs", "All" |
|Přijmout vstup kanálu:      |False    |
|Přijmout zástupné znaky: |False    |

### <a name="-summary"></a>-Summary

Určuje, jestli se má zobrazit jenom souhrn sestavy, vynechává se podrobnosti.

|  |  |
|----------------------------|------------------|
|Zadejte:                       |Přepínací parametr   |
|Poziční                   |Jmenovanou             |
|Výchozí hodnota:              |False             |
|Přijmout vstup kanálu:      |False             |
|Přijmout zástupné znaky: |False             |

### <a name="-cleanreport"></a>-CleanReport

Odebere předchozí spuštění a historii ověření a zapíše ověření do nové sestavy.

|  |  |
|----------------------------|------------------|
|Zadejte:                       |Přepínací parametr   |
|Hromad                    |CF                |
|Poziční                   |Jmenovanou             |
|Výchozí hodnota:              |False             |
|Přijmout vstup kanálu:      |False             |
|Přijmout zástupné znaky: |False             |

### <a name="-outputpath"></a>– OutputPath

Určuje vlastní cestu k uložení sestavy JSON připravenosti a podrobného souboru protokolu. Pokud cesta ještě neexistuje, příkaz se pokusí vytvořit adresář.

|  |  |
|----------------------------|------------------|
|Zadejte:                       |Řetězec            |
|Poziční                   |Jmenovanou             |
|Výchozí hodnota:              |$ENV: TEMP\AzsReadinessChecker  |
|Přijmout vstup kanálu:      |False             |
|Přijmout zástupné znaky: |False             |

### <a name="-confirm"></a>– Potvrzení

Před spuštěním rutiny zobrazí výzvu k potvrzení.

|  |  |
|----------------------------|------------------|
|Zadejte:                       |Přepínací parametr   |
|Hromad                    |CF                |
|Poziční                   |Jmenovanou             |
|Výchozí hodnota:              |False             |
|Přijmout vstup kanálu:      |False             |
|Přijmout zástupné znaky: |False             |

### <a name="-whatif"></a>-WhatIf

Ukazuje, co se stane, když se rutina spustí. Rutina není spuštěná.

|  |  |
|----------------------------|------------------|
|Zadejte:                       |Přepínací parametr   |
|Hromad                    |Wi                |
|Poziční                   |Jmenovanou             |
|Výchozí hodnota:              |False             |
|Přijmout vstup kanálu:      |False             |
|Přijmout zástupné znaky: |False             |
