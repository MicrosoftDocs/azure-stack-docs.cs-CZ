---
title: Ověření certifikátů PKI Azure Stack hub
titleSuffix: Azure Stack Hub
description: Zjistěte, jak ověřit certifikáty PKI pro integrované systémy Azure Stack centra pomocí nástroje pro kontrolu připravenosti centra Azure Stack.
services: azure-stack
documentationcenter: ''
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 10/19/2020
ms.author: inhenkel
ms.reviewer: ppacent
ms.lastreviewed: 10/19/2020
ms.openlocfilehash: 201acbad11011731a8e7017d14b39be120e460d3
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94545755"
---
# <a name="validate-azure-stack-hub-pki-certificates"></a>Ověření certifikátů PKI Azure Stack hub

Nástroj pro kontrolu připravenosti centra Azure Stack popsaný v tomto článku je k dispozici v [Galerie prostředí PowerShell](https://aka.ms/AzsReadinessChecker). Pomocí tohoto nástroje ověřte, jestli jsou [vygenerované certifikáty infrastruktury veřejných klíčů (PKI)](azure-stack-get-pki-certs.md) vhodné k předběžnému nasazení. Ověřte certifikáty tím, že necháte dostatek času na testování a vystavování certifikátů v případě potřeby.

Nástroj pro kontrolu připravenosti provádí následující ověření certifikátu:

- **Analyzovat PFX**  
    Kontroluje platný soubor PFX, správné heslo a informace o tom, jestli jsou veřejné informace chráněné heslem.
- **Datum vypršení platnosti**  
    Kontroluje minimální platnost sedmi dní.
- **Algoritmus podpisu**  
    Kontroluje, zda algoritmus signatury není SHA1.
- **Privátní klíč**  
    Kontroluje, zda je přítomen soukromý klíč a je exportován s atributem místního počítače. 
- **Řetěz certifikátů**  
    Kontroluje, zda řetěz certifikátů není v pořádku, včetně kontroly certifikátů podepsaných svým držitelem.
- **Názvy DNS**  
    Kontroluje, jestli síť SAN obsahuje relevantní názvy DNS pro každý koncový bod nebo zda je k dispozici podpůrný zástupný znak.
- **Použití klíče**  
    Kontroluje, zda použití klíče obsahuje digitální podpis a zakódování klíče, a kontroluje, zda použití rozšířeného klíče obsahuje ověření serveru a ověření klienta.
- **Velikost klíče**  
    Kontroluje, zda je velikost klíče 2048 nebo větší.
- **Pořadí řetězců**  
    Kontroluje pořadí ostatních certifikátů, které ověřují, zda je pořadí správné.
- **Další certifikáty**  
    Zajistěte, aby žádné další certifikáty nebyly zabaleny do souboru PFX kromě odpovídajícího listového certifikátu a jeho řetězu.

> [!IMPORTANT]  
> Certifikát PKI je soubor PFX a heslo by mělo být považováno za citlivé informace.

## <a name="prerequisites"></a>Požadavky

Před ověřením certifikátů PKI pro nasazení centra Azure Stack musí systém splňovat následující požadavky:

- Kontrola připravenosti centra Microsoft Azure Stack.
- Certifikáty SSL se vyexportují podle [pokynů pro přípravu](azure-stack-prepare-pki-certs.md).
- DeploymentData.js.
- Windows 10 nebo Windows Server 2016.

## <a name="perform-core-services-certificate-validation"></a>Ověřování certifikátů základních služeb

Pomocí těchto kroků ověříte certifikáty PKI centra Azure Stack pro nasazení a rotaci tajných kódů:

1. Spuštěním následující rutiny nainstalujte **AzsReadinessChecker** z příkazového řádku PowerShellu (5,1 nebo vyšší):

    ```powershell  
        Install-Module Microsoft.AzureStack.ReadinessChecker -Force -AllowPrerelease
    ```

2. Vytvořte strukturu adresářů certifikátů. V následujícím příkladu se můžete změnit `<C:\Certificates\Deployment>` na cestu k novému adresáři podle vašeho výběru.
    ```powershell  
    New-Item C:\Certificates\Deployment -ItemType Directory
    
    $directories = 'ACSBlob', 'ACSQueue', 'ACSTable', 'Admin Extension Host', 'Admin Portal', 'ARM Admin', 'ARM Public', 'KeyVault', 'KeyVaultInternal', 'Public Extension Host', 'Public Portal'
    
    $destination = 'C:\Certificates\Deployment'
    
    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}
    ```
    
    > [!Note]  
    > AD FS a Graph jsou požadovány, pokud používáte AD FS jako systém identity. Například:
    >
    > ```powershell  
    > $directories = 'ACSBlob', 'ACSQueue', 'ACSTable', 'ADFS', 'Admin Extension Host', 'Admin Portal', 'ARM Admin', 'ARM Public', 'Graph', 'KeyVault', 'KeyVaultInternal', 'Public Extension Host', 'Public Portal'
    > ```
    
     - Své certifikáty umístěte do příslušných adresářů, které jste vytvořili v předchozím kroku. Například:  
        - `C:\Certificates\Deployment\ACSBlob\CustomerCertificate.pfx`
        - `C:\Certificates\Deployment\Admin Portal\CustomerCertificate.pfx`
        - `C:\Certificates\Deployment\ARM Admin\CustomerCertificate.pfx`

3. V okně PowerShellu změňte hodnoty `RegionName` `FQDN` a `IdentitySystem` odpovídající prostředí Azure Stack hub a spusťte následující rutinu:

    ```powershell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString 
    Invoke-AzsHubDeploymentCertificateValidation -CertificatePath C:\Certificates\Deployment -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD  
    ```

4. Zkontrolujte výstup a zajistěte, aby všechny certifikáty vyhověly všem testům. Například:

    ```powershell
    Invoke-AzsHubDeploymentCertificateValidation v1.2005.1286.272 started.
    Testing: KeyVaultInternal\KeyVaultInternal.pfx
    Thumbprint: E86699****************************4617D6
        PFX Encryption: OK
        Expiry Date: OK
        Signature Algorithm: OK
        DNS Names: OK
        Key Usage: OK
        Key Length: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        Chain Order: OK
        Other Certificates: OK
    Testing: ARM Public\ARMPublic.pfx
    Thumbprint: 8DC4D9****************************69DBAA
        PFX Encryption: OK
        Expiry Date: OK
        Signature Algorithm: OK
        DNS Names: OK
        Key Usage: OK
        Key Length: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        Chain Order: OK
        Other Certificates: OK
    Testing: Admin Portal\AdminPortal.pfx
    Thumbprint: 6F9055****************************4AC0EA
        PFX Encryption: OK
        Expiry Date: OK
        Signature Algorithm: OK
        DNS Names: OK
        Key Usage: OK
        Key Length: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        Chain Order: OK
        Other Certificates: OK
    Testing: Public Portal\PublicPortal.pfx


    Log location (contains PII): C:\Users\[*redacted*]\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    Report location (contains PII): C:\Users\[*redacted*]\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
    Invoke-AzsHubDeploymentCertificateValidation Completed

    ```

    Pokud chcete ověřit certifikáty pro jiné služby Azure Stack centra, změňte hodnotu pro ```-CertificateType``` . Například:

    ```powershell  
    # App Services
    Invoke-AzsHubAppServicesCertificateValidation -CertificatePath C:\Certificates\AppServices -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com

    # DBAdapter
    Invoke-AzsHubDBAdapterCertificateValidation -CertificatePath C:\Certificates\DBAdapter -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com

    # EventHubs
    Invoke-AzsHubEventHubsCertificateValidation -CertificatePath C:\Certificates\EventHubs -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com

    # IoTHub
    Invoke-AzsHubIoTHubCertificateValidation -CertificatePath C:\Certificates\IoTHub -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com
    ```
    Každá složka by měla obsahovat jeden soubor PFX pro daný typ certifikátu. Pokud má typ certifikátu požadavky na více certifikátů, jsou pro každý jednotlivý certifikát očekávány vnořené složky a rozlišující názvy. Následující kód ukazuje příklad struktury složky/certifikátu pro všechny typy certifikátů a odpovídající hodnotu pro ```-CertificateType``` a ```-CertificatePath``` .
    
    ```powershell  
    C:\>tree c:\SecretStore /A /F
        Folder PATH listing
        Volume serial number is 85AE-DF2E
        C:\SECRETSTORE
        \---AzureStack
            +---CertificateRequests
            \---Certificates
                +---AppServices         # Invoke-AzsCertificateValidation `
                |   +---API             #     -CertificateType AppServices `
                |   |       api.pfx     #     -CertificatePath C:\Certificates\AppServices
                |   |
                |   +---DefaultDomain
                |   |       wappsvc.pfx
                |   |
                |   +---Identity
                |   |       sso.pfx
                |   |
                |   \---Publishing
                |           ftp.pfx
                |
                +---DBAdapter           # Invoke-AzsCertificateValidation `
                |       dbadapter.pfx   #   -CertificateType DBAdapter `
                |                       #   -CertificatePath C:\Certificates\DBAdapter
                |
                +---Deployment          # Invoke-AzsCertificateValidation `
                |   +---ACSBlob         #   -CertificateType Deployment `
                |   |       acsblob.pfx #   -CertificatePath C:\Certificates\Deployment
                |   |
                |   +---ACSQueue
                |   |       acsqueue.pfx
               ./. ./. ./. ./. ./. ./. ./.    <- Deployment certificate tree trimmed.
                |   \---Public Portal
                |           portal.pfx
                |
                +---EventHubs           # Invoke-AzsCertificateValidation `
                |       eventhubs.pfx   #   -CertificateType EventHubs `
                |                       #   -CertificatePath C:\Certificates\EventHubs
                |
                \---IoTHub              # Invoke-AzsCertificateValidation `
                        iothub.pfx      #   -CertificateType IoTHub `
                                        #   -CertificatePath C:\Certificates\IoTHub
    ```

### <a name="known-issues"></a>Známé problémy

**Příznak** : testy se přeskočí.

**Příčina** : AzsReadinessChecker přeskočí některé testy, pokud není splněna závislost:

 - Další certifikáty se přeskočí, pokud řetěz certifikátů selhává.

    ```powershell  
    Testing: ACSBlob\singlewildcard.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: Fail
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: Skipped
    Details:
    The certificate records '*.east.azurestack.contoso.com' do not contain a record that is valid for '*.blob.east.azurestack.contoso.com'. Please refer to the documentation for how to create the required certificate file.
    The Other Certificates check was skipped because Cert Chain and/or DNS Names failed. Follow the guidance to remediate those issues and recheck. 

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
    Invoke-AzsCertificateValidation Completed
    ```

**Řešení** : postupujte podle pokynů k nástroji v části Podrobnosti v každé sadě testů pro každý certifikát.

## <a name="certificates"></a>Certifikáty

| Adresář | Certifikát |
| ---    | ----        |
| ACSBlob | `wildcard_blob_<region>_<externalFQDN>` |
| ACSQueue  |  `wildcard_queue_<region>_<externalFQDN>` |
| ACSTable  |  `wildcard_table_<region>_<externalFQDN>` |
| Hostitel rozšíření Správce  |  `wildcard_adminhosting_<region>_<externalFQDN>` |
| Portál pro správu  |  `adminportal_<region>_<externalFQDN>` |
| Správce ARM  |  `adminmanagement_<region>_<externalFQDN>` |
| Veřejné ARM  |  `management_<region>_<externalFQDN>` |
| KeyVault  |  `wildcard_vault_<region>_<externalFQDN>` |
| KeyVaultInternal  |  `wildcard_adminvault_<region>_<externalFQDN>` |
| Hostitel veřejného rozšíření  |  `wildcard_hosting_<region>_<externalFQDN>` |
| Veřejný portál  |  `portal_<region>_<externalFQDN>` |

## <a name="using-validated-certificates"></a>Použití ověřených certifikátů

Až AzsReadinessChecker vaše certifikáty ověří, budete připraveni je použít ve svém nasazení centra Azure Stack nebo pro otočení tajného kódu centra Azure Stack.

 - Pro nasazení proveďte zabezpečený přenos vašich certifikátů do nástroje pro vývoj nasazení, aby je bylo možné zkopírovat do hostitele nasazení, jak je uvedeno v [dokumentaci k požadavkům PKI centra Azure Stack](azure-stack-pki-certs.md).
 - Pro rotaci tajných klíčů můžete použít certifikáty k aktualizaci starých certifikátů pro koncové body veřejné infrastruktury Azure Stack centra, a to podle pokynů v [dokumentaci pro střídání tajných kódů centra Azure Stack](azure-stack-rotate-secrets.md).
 - V případě služeb PaaS Services můžete certifikáty použít k instalaci poskytovatelů prostředků SQL, MySQL a App Services v centru Azure Stack pomocí [přehledu nabízených služeb v dokumentaci ke službě Azure Stack hub](service-plan-offer-subscription-overview.md).

## <a name="next-steps"></a>Další kroky

[Integrace identit datacentra](azure-stack-integrate-identity.md)
