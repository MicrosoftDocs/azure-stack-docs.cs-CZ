---
title: Ověření certifikátů infrastruktury veřejných klíčů Azure Stack pro nasazení Azure Stack integrovaných systémů | Microsoft Docs
description: Popisuje ověření Azure Stack certifikátů PKI pro Azure Stack integrovaných systémů. Zahrnuje použití nástroje pro kontrolu certifikátů Azure Stack.
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
ms.openlocfilehash: 3823aa73d58af48c662690aa0d8e8a21180b4ed6
ms.sourcegitcommit: d159652f50de7875eb4be34c14866a601a045547
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72283233"
---
# <a name="validate-azure-stack-pki-certificates"></a>Ověření Azure Stack certifikátů PKI

Nástroj pro kontrolu připravenosti na Azure Stack popsaný v tomto článku je k dispozici v [Galerie prostředí PowerShell](https://aka.ms/AzsReadinessChecker). Pomocí tohoto nástroje můžete ověřit, jestli jsou [vygenerované certifikáty PKI](azure-stack-get-pki-certs.md) vhodné k předběžnému nasazení. Ověřte certifikáty tím, že necháte dostatek času na testování a vystavování certifikátů v případě potřeby.

Nástroj pro kontrolu připravenosti provádí následující ověření certifikátu:

- **Přečíst PFX**  
    Kontroluje platný soubor PFX, správné heslo a informace o tom, jestli nejsou veřejné informace chráněné heslem. 
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
- **Žádný profil**  
    Kontroluje, zda může nový uživatel načíst data PFX bez načtení profilu uživatele, mimicking chování účtů gMSA během obsluhy certifikátu.

> [!IMPORTANT]  
> Certifikát PKI je soubor PFX a heslo by mělo být považováno za citlivé informace.

## <a name="prerequisites"></a>Požadavky

Před ověřením certifikátů PKI pro nasazení Azure Stack musí systém splňovat následující požadavky:

- Kontrola připravenosti na Microsoft Azure Stack
- Certifikáty SSL se vyexportují podle [pokynů pro přípravu](azure-stack-prepare-pki-certs.md) .
- DeploymentData. JSON
- Windows 10 nebo Windows Server 2016

## <a name="perform-core-services-certificate-validation"></a>Provést ověření certifikátu základní služby

Pomocí těchto kroků Připravte a ověřte Azure Stack certifikáty PKI pro nasazení a pro rotaci tajných klíčů:

1. Nainstalujte **AzsReadinessChecker** z příkazového řádku PowerShellu (5,1 nebo vyšší) spuštěním následující rutiny:

    ```powershell  
        Install-Module Microsoft.AzureStack.ReadinessChecker -force 
    ```

2. Vytvořte strukturu adresářů certifikátů. V následujícím příkladu můžete změnit `<c:\certificates>` na novou cestu k adresáři podle vašeho výběru.
    ```powershell  
    New-Item C:\Certificates -ItemType Directory
    
    $directories = 'ACSBlob', 'ACSQueue', 'ACSTable', 'Admin Extension Host', 'Admin Portal', 'ARM Admin', 'ARM Public', 'KeyVault', 'KeyVaultInternal', 'Public Extension Host', 'Public Portal'
    
    $destination = 'c:\certificates'
    
    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}
    ```
    
    > [!Note]  
    > AD FS a Graph jsou požadovány, pokud používáte AD FS jako systém identity. Příklad:
    >
    > ```powershell  
    > $directories = 'ACSBlob', 'ACSQueue', 'ACSTable', 'ADFS', 'Admin Extension Host', 'Admin Portal', 'ARM Admin', 'ARM Public', 'Graph', 'KeyVault', 'KeyVaultInternal', 'Public Extension Host', 'Public Portal'
    > ```
    
     - Své certifikáty umístěte do příslušných adresářů, které jste vytvořili v předchozím kroku. Příklad:  
        - `c:\certificates\ACSBlob\CustomerCertificate.pfx`
        - `c:\certificates\Admin Portal\CustomerCertificate.pfx`
        - `c:\certificates\ARM Admin\CustomerCertificate.pfx`

3. V okně PowerShellu změňte hodnoty **RegionName** a **plně kvalifikovaný název domény** , které jsou vhodné pro Azure Stack prostředí, a spusťte následující příkaz:

    ```powershell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString 

    Invoke-AzsCertificateValidation -CertificatePath c:\certificates -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD  
    ```

4. Ověřte výstup a všechny certifikáty předejte všechny testy. Příklad:

```powershell
Invoke-AzsCertificateValidation v1.1809.1005.1 started.
Testing: ARM Public\ssl.pfx
Thumbprint: 7F6B27****************************E9C35A
    PFX Encryption: OK
    Signature Algorithm: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Parse PFX: OK
    Private Key: OK
    Cert Chain: OK
    Chain Order: OK
    Other Certificates: OK
Testing: Admin Extension Host\ssl.pfx
Thumbprint: A631A5****************************35390A
    PFX Encryption: OK
    Signature Algorithm: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Parse PFX: OK
    Private Key: OK
    Cert Chain: OK
    Chain Order: OK
    Other Certificates: OK
Testing: Public Extension Host\ssl.pfx
Thumbprint: 4DBEB2****************************C5E7E6
    PFX Encryption: OK
    Signature Algorithm: OK
    DNS Names: OK
    Key Usage: OK
    Key Size: OK
    Parse PFX: OK
    Private Key: OK
    Cert Chain: OK
    Chain Order: OK
    Other Certificates: OK

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsCertificateValidation Completed
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

## <a name="perform-platform-as-a-service-certificate-validation"></a>Provést ověření certifikátu platformy jako služby

Pomocí těchto kroků můžete připravit a ověřit Azure Stack certifikáty PKI pro certifikáty Platform as a Service (PaaS), pokud se plánují nasazení SQL/MySQL nebo App Services.

1.  Nainstalujte **AzsReadinessChecker** z příkazového řádku PowerShellu (5,1 nebo vyšší) spuštěním následující rutiny:

    ```powershell  
      Install-Module Microsoft.AzureStack.ReadinessChecker -force
    ```

2.  Vytvořte vnořenou zatřiďovací tabulku obsahující cesty a heslo ke každému PaaS certifikátu, který vyžaduje ověření. V okně PowerShellu se spustí:

    ```powershell  
        $PaaSCertificates = @{
        'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        }
    ```

3.  Zahajte ověřování změnou hodnot **RegionName** a **plně kvalifikovaného názvu domény** tak, aby odpovídaly vašemu prostředí Azure Stack. Pak spusťte:

    ```powershell  
    Invoke-AzsCertificateValidation -PaaSCertificates $PaaSCertificates -RegionName east -FQDN azurestack.contoso.com 
    ```
4.  Ověřte, že výstup a všechny certifikáty přecházejí všemi testy.

    ```powershell
    Invoke-AzsCertificateValidation v1.0 started.
    Thumbprint: 95A50B****************************FA6DDA
        Signature Algorithm: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Thumbprint: EBB011****************************59BE9A
        Signature Algorithm: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Thumbprint: 76AEBA****************************C1265E
        Signature Algorithm: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: OK
    Thumbprint: 8D6CCD****************************DB6AE9
        Signature Algorithm: OK
        Parse PFX: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: OK
        Key Usage: OK
        Key Size: OK
    ```

## <a name="certificates"></a>Certifikáty

| Službě | Certifikát |
| ---    | ----        |
| acsBlob | wildcard_blob_ @ no__t-0region > _ @ no__t-1externalFQDN > |
| ACSQueue  |  wildcard_queue_ @ no__t-0region > _ @ no__t-1externalFQDN > |
| ACSTable  |  wildcard_table_ @ no__t-0region > _ @ no__t-1externalFQDN > |
| Hostitel rozšíření Správce  |  wildcard_adminhosting_ @ no__t-0region > _ @ no__t-1externalFQDN > |
| Portál pro správu  |  adminportal_ @ no__t-0region > _ @ no__t-1externalFQDN > |
| Správce ARM  |  adminmanagement_ @ no__t-0region > _ @ no__t-1externalFQDN > |
| Veřejné ARM  |  management_ @ no__t-0region > _ @ no__t-1externalFQDN > |
| KeyVault  |  wildcard_vault_ @ no__t-0region > _ @ no__t-1externalFQDN > |
| KeyVaultInternal  |  wildcard_adminvault_ @ no__t-0region > _ @ no__t-1externalFQDN > |
| Hostitel veřejného rozšíření  |  wildcard_hosting_ @ no__t-0region > _ @ no__t-1externalFQDN > |
| Veřejný portál  |  portal_ @ no__t-0region > _ @ no__t-1externalFQDN > |

## <a name="using-validated-certificates"></a>Použití ověřených certifikátů

Po ověření certifikátů nástrojem AzsReadinessChecker jste připraveni je použít ve svém nasazení Azure Stack nebo pro Azure Stackovou rotaci. 

 - Pro nasazení proveďte zabezpečený přenos vašich certifikátů do nástroje pro vývoj nasazení, aby je bylo možné zkopírovat do hostitele nasazení, jak je uvedeno v [dokumentaci Azure Stack požadavky PKI](azure-stack-pki-certs.md).
 - Pro rotaci tajných klíčů můžete použít certifikáty k aktualizaci starých certifikátů pro koncové body veřejné infrastruktury Azure Stack prostředí, a to podle pokynů v [dokumentaci Azure Stack pro střídání tajných klíčů](azure-stack-rotate-secrets.md).
 - V případě služeb PaaS Services můžete certifikáty použít k instalaci poskytovatelů prostředků SQL, MySQL a App Services v Azure Stack podle [přehledu nabídky služby v dokumentaci Azure Stack](service-plan-offer-subscription-overview.md).

## <a name="next-steps"></a>Další kroky

[Integrace identit datacentra](azure-stack-integrate-identity.md)
