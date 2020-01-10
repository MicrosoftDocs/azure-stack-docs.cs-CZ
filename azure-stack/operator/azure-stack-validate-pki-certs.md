---
title: Ověření certifikátů infrastruktury veřejných klíčů Azure Stack hub pro nasazení integrovaných systémů Azure Stack hub | Microsoft Docs
description: Popisuje, jak ověřit certifikáty PKI centra Azure Stack pro integrované systémy služby Azure Stack hub. Zahrnuje použití nástroje pro kontrolu certifikátů ve službě Azure Stack hub.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: ppacent
ms.lastreviewed: 01/08/2019
ms.openlocfilehash: 23225b21d1dc3074c69cefa2af23a99b634a7a73
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75812854"
---
# <a name="validate-azure-stack-hub-pki-certificates"></a>Ověření certifikátů PKI Azure Stack hub

Nástroj pro kontrolu připravenosti centra Azure Stack popsaný v tomto článku je k dispozici v [Galerie prostředí PowerShell](https://aka.ms/AzsReadinessChecker). Pomocí tohoto nástroje můžete ověřit, jestli jsou [vygenerované certifikáty PKI](azure-stack-get-pki-certs.md) vhodné k předběžnému nasazení. Ověřte certifikáty tím, že necháte dostatek času na testování a vystavování certifikátů v případě potřeby.

Nástroj pro kontrolu připravenosti provádí následující ověření certifikátu:

- **Analyzovat PFX**  
    Kontroluje platný soubor PFX, správné heslo a informace o tom, jestli jsou veřejné informace chráněné heslem. 
- **Datum vypršení platnosti**  
    Kontroluje minimální platnost 7 dní. 
- **Algoritmus podpisu**  
    Kontroluje, zda algoritmus signatury není SHA1.
- **Privátní klíč**  
    Kontroluje, zda je přítomen soukromý klíč a je exportován s atributem místního počítače. 
- **Řetěz certifikátů**  
    Kontroluje, zda řetěz certifikátů není v pořádku, včetně kontroly certifikátů podepsaných svým držitelem.
- **Názvy DNS**  
    Kontroluje, jestli síť SAN obsahuje relevantní názvy DNS pro každý koncový bod, nebo pokud je k dispozici podpůrný zástupný znak.
- **Použití klíče**  
    Kontroluje, zda použití klíče obsahuje digitální podpis a šifrování klíče a rozšířené použití klíče obsahuje ověření serveru a ověřování klientů.
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

- Kontrola připravenosti centra Microsoft Azure Stack
- Certifikáty SSL se vyexportují podle [pokynů pro přípravu](azure-stack-prepare-pki-certs.md) .
- DeploymentData.json
- Windows 10 nebo Windows Server 2016

## <a name="perform-core-services-certificate-validation"></a>Provést ověření certifikátu základní služby

Pomocí těchto kroků Připravte a ověřte certifikáty PKI centra Azure Stack pro nasazení a rotaci tajných kódů:

1. Nainstalujte **AzsReadinessChecker** z příkazového řádku PowerShellu (5,1 nebo vyšší) spuštěním následující rutiny:

    ```powershell  
        Install-Module Microsoft.AzureStack.ReadinessChecker -force 
    ```

2. Vytvořte strukturu adresářů certifikátů. V následujícím příkladu můžete `<C:\Certificates\Deployment>` změnit na novou cestu k adresáři podle vašeho výběru.
    ```powershell  
    New-Item C:\Certificates\Deployment -ItemType Directory
    
    $directories = 'ACSBlob', 'ACSQueue', 'ACSTable', 'Admin Extension Host', 'Admin Portal', 'ARM Admin', 'ARM Public', 'KeyVault', 'KeyVaultInternal', 'Public Extension Host', 'Public Portal'
    
    $destination = 'C:\Certificates\Deployment'
    
    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}
    ```
    
    > [!Note]  
    > AD FS a Graph jsou požadovány, pokud používáte AD FS jako systém identity. Příklad:
    >
    > ```powershell  
    > $directories = 'ACSBlob', 'ACSQueue', 'ACSTable', 'ADFS', 'Admin Extension Host', 'Admin Portal', 'ARM Admin', 'ARM Public', 'Graph', 'KeyVault', 'KeyVaultInternal', 'Public Extension Host', 'Public Portal'
    > ```
    
     - Své certifikáty umístěte do příslušných adresářů, které jste vytvořili v předchozím kroku. Příklad:  
        - `C:\Certificates\Deployment\ACSBlob\CustomerCertificate.pfx`
        - `C:\Certificates\Deployment\Admin Portal\CustomerCertificate.pfx`
        - `C:\Certificates\Deployment\ARM Admin\CustomerCertificate.pfx`

3. V okně PowerShellu změňte hodnoty **RegionName** a **plně kvalifikovaný název domény** , které jsou vhodné pro prostředí centra Azure Stack hub, a spusťte následující příkaz:

    ```powershell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString 
    Invoke-AzsCertificateValidation -CertificateType Deployment -CertificatePath C:\Certificates\Deployment -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD  
    ```

4. Ověřte výstup a všechny certifikáty předejte všechny testy. Příklad:

    ```powershell
    Invoke-AzsCertificateValidation v1.1912.1082.37 started.
    Testing: KeyVaultInternal\adminvault.pfx
    Thumbprint: B1CB76****************************565B99
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
    Testing: ARM Public\management.pfx
    Thumbprint: 44A35E****************************36052A
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
    Testing: Admin Portal\adminportal.pfx
    Thumbprint: 3F5E81****************************9EBF9A
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
    Testing: Public Portal\portal.pfx

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
    Invoke-AzsCertificateValidation Completed
    ```

    Pokud chcete ověřit certifikáty pro jiné služby Azure Stack centra, změňte hodnotu ```-CertificateType```. Příklad:

    ```powershell  
    # App Services
    Invoke-AzsCertificateValidation -CertificateType AppServices -CertificatePath C:\Certificates\AppServices -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com

    # DBAdapter
    Invoke-AzsCertificateValidation -CertificateType DBAdapter -CertificatePath C:\Certificates\DBAdapter -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com

    # EventHub
    Invoke-AzsCertificateValidation -CertificateType EventHubs -CertificatePath C:\Certificates\EventHub -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com

    # IoTHub
    Invoke-AzsCertificateValidation -CertificateType IoTHub -CertificatePath C:\Certificates\IoTHub -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com
    ```
Každá složka by měla obsahovat jeden soubor PFX pro daný typ certifikátu, pokud má typ certifikátu pro každý jednotlivý certifikát požadavky na více certifikátů, jsou očekávány i citlivé názvy.  Následující kód ukazuje příklad struktury složky/certifikátu pro všechny typy certifikátů a odpovídající hodnotu pro ```-CertificateType``` a ```-CertificatePath```.
    
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
                |   |       API. pfx # – CertificatePath C:\Certificates\AppServices
                |   |
                |   +---DefaultDomain
                |   |       wappsvc. pfx
                |   |
                |   A---identity
                |   |       SSO. pfx
                |   |
                |   \-– publikování
                |           FTP. pfx
                |
                +---DBAdapter           # Invoke-AzsCertificateValidation `
                |       dbadapter.pfx   #   -CertificateType DBAdapter `
                |                       #   -CertificatePath C:\Certificates\DBAdapter
                |
                +---Deployment          # Invoke-AzsCertificateValidation `
                |   +---ACSBlob         #   -CertificateType Deployment `
                |   |       acsblob. pfx # – CertificatePath C:\Certificates\Deployment
                |   |
                |   +---ACSQueue
                |   |       acsqueue. pfx
               ./. ./. ./. ./. ./. ./. ./.    <- Deployment certificate tree trimmed.
                |   \---Public Portal
                |           portal.pfx
                |
                +---EventHub            # Invoke-AzsCertificateValidation `
                |       eventhub.pfx    #   -CertificateType EventHub `
                |                       #   -CertificatePath C:\Certificates\EventHub
                |
                \---IoTHub              # Invoke-AzsCertificateValidation `
                        iothub.pfx      #   -CertificateType IoTHub `
                                        #   -CertificatePath C:\Certificates\IoTHub
    ```
### <a name="known-issues"></a>Známé problémy

**Příznak**: testy se přeskočí.

**Příčina**: AzsReadinessChecker přeskočí některé testy, pokud není splněna závislost:

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
    Detailed log can be found C:\AzsReadinessChecker\CertificateValidation\CertChecker.log

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
    Invoke-AzsCertificateValidation Completed
    ```

**Řešení**: postupujte podle pokynů k nástroji v části Podrobnosti v každé sadě testů pro každý certifikát.

## <a name="certificates"></a>Certifikáty

| Adresář | Certifikát |
| ---    | ----        |
| acsBlob | wildcard_blob_\<oblasti > _\<externalFQDN > |
| ACSQueue  |  wildcard_queue_\<region>_\<externalFQDN> |
| ACSTable  |  wildcard_table_\<region>_\<externalFQDN> |
| Hostitel rozšíření Správce  |  wildcard_adminhosting_\<region>_\<externalFQDN> |
| Portál pro správu  |  adminportal_\<region>_\<externalFQDN> |
| Správce ARM  |  adminmanagement_\<region>_\<externalFQDN> |
| Veřejné ARM  |  management_\<region>_\<externalFQDN> |
| KeyVault  |  wildcard_vault_\<oblasti > _\<externalFQDN > |
| KeyVaultInternal  |  wildcard_adminvault_\<oblasti > _\<externalFQDN > |
| Hostitel veřejného rozšíření  |  wildcard_hosting_\<region>_\<externalFQDN> |
| Veřejný portál  |  portal_\<region>_\<externalFQDN> |

## <a name="using-validated-certificates"></a>Použití ověřených certifikátů

Po ověření vašich certifikátů nástrojem AzsReadinessChecker jste připraveni je použít ve svém nasazení centra Azure Stack nebo pro otočení tajného kódu centra Azure Stack. 

 - Pro nasazení proveďte zabezpečený přenos vašich certifikátů do nástroje pro vývoj nasazení, aby je bylo možné zkopírovat do hostitele nasazení, jak je uvedeno v [dokumentaci k požadavkům PKI centra Azure Stack](azure-stack-pki-certs.md).
 - Pro rotaci tajných klíčů můžete použít certifikáty k aktualizaci starých certifikátů pro koncové body veřejné infrastruktury Azure Stack centra, a to podle pokynů v [dokumentaci pro střídání tajných kódů centra Azure Stack](azure-stack-rotate-secrets.md).
 - V případě služeb PaaS Services můžete certifikáty použít k instalaci poskytovatelů prostředků SQL, MySQL a App Services v centru Azure Stack pomocí [přehledu nabízených služeb v dokumentaci ke službě Azure Stack hub](service-plan-offer-subscription-overview.md).

## <a name="next-steps"></a>Další kroky

[Integrace identit datacentra](azure-stack-integrate-identity.md)
