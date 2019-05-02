---
title: Ověření certifikátů infrastruktury veřejných klíčů v Azure stacku pro Azure Stack integrované systémy nasazení | Dokumentace Microsoftu
description: Popisuje způsob ověřování certifikátů Azure Stack infrastruktury veřejných KLÍČŮ pro integrované systémy Azure Stack. Nástroj prerequisite Checker certifikátu Azure Stack se věnuje.
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
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: ppacent
ms.lastreviewed: 01/08/2019
ms.openlocfilehash: abf2d10c50271217220d9dc218b4d50f7411d867
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2019
ms.locfileid: "64984531"
---
# <a name="validate-azure-stack-pki-certificates"></a>Ověření certifikátů infrastruktury veřejných KLÍČŮ Azure Stack

Nástroj prerequisite Checker připravenosti Azure Stack popsaných v tomto článku je k dispozici [z Galerie prostředí PowerShell](https://aka.ms/AzsReadinessChecker). Nástroj můžete použít k ověření, který [vygenerovat certifikáty PKI](azure-stack-get-pki-certs.md) jsou vhodné pro před nasazením. Ověření certifikátů se zaškrtnutým políčkem dostatek času k testování a v případě potřeby znovu vystavit certifikáty.

Nástroj prerequisite Checker připravenosti provádí následující ověření certifikátu:

- **Přečtěte si PFX**  
    Vyhledá platný soubor PFX a heslo správné, a určuje, zda informace o veřejném není chráněn heslem. 
- **Algoritmus podpisu**  
    Ověří, že algoritmus podpisu není SHA1.
- **Privátní klíč**  
    Ověří, že privátní klíč je k dispozici a s atributem místního počítače exportu. 
- **Řetěz certifikátů**  
    Řetěz certifikátů kontroly je beze změny, včetně kontrolu certifikáty podepsané svým držitelem.
- **Názvy DNS**  
    Zkontroluje, síť SAN obsahuje odpovídající názvy DNS pro každý koncový bod, nebo pokud podporu zástupných znaků je k dispozici.
- **Použití klíče**  
    Zkontroluje, jestli použití klíče obsahuje digitální podpis a šifrování klíče a rozšířené použití klíče obsahuje ověření klienta a ověřování serveru.
- **Velikost klíče**  
    Kontroluje, zda je velikost klíče 2048 nebo větší.
- **Pořadí v řetězci**  
    Ověří pořadí dalších certifikátů ověřuje, že pořadí je správná.
- **Další certifikáty**  
    Ujistěte se, že žádné další certifikáty byla zabalena v souboru PFX než relevantní listového certifikátu a jeho řetězce.
- **Žádný profil.**  
    Ověří, že nového uživatele můžete načíst PFX data bez načtení profilu uživatele tak napodobuje chování účty gMSA během obsluhy certifikátu.

> [!IMPORTANT]  
> Certifikát PKI je soubor PFX a heslo, které mají být považována za citlivé informace.

## <a name="prerequisites"></a>Požadavky

Váš systém by měl splňovat následující požadavky před ověřením certifikáty PKI pro nasazení služby Azure Stack:

- Kontrola připravenosti Microsoft Azure Stack
- Certifikáty SSL exportovat následující [pokyny k přípravě](azure-stack-prepare-pki-certs.md)
- DeploymentData.json
- Windows 10 nebo Windows Server 2016

## <a name="perform-core-services-certificate-validation"></a>Provést základní ověřování certifikátu služby

K přípravě a k ověřování certifikátů Azure Stack infrastruktury veřejných KLÍČŮ pro nasazení a tajného kódu otočení, proveďte následující kroky:

1. Nainstalujte **AzsReadinessChecker** z příkazového řádku Powershellu (5.1 nebo novější), spuštěním následující rutiny:

    ```powershell  
        Install-Module Microsoft.AzureStack.ReadinessChecker -force 
    ```

2. Vytvoření certifikátu adresářovou strukturu. V následujícím příkladu můžete změnit `<c:\certificates>` na novou cestu k adresáři podle vašeho výběru.
    ```powershell  
    New-Item C:\Certificates -ItemType Directory
    
    $directories = 'ACSBlob', 'ACSQueue', 'ACSTable', 'Admin Extension Host', 'Admin Portal', 'ARM Admin', 'ARM Public', 'KeyVault', 'KeyVaultInternal', 'Public Extension Host', 'Public Portal'
    
    $destination = 'c:\certificates'
    
    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}
    ```
    
    > [!Note]  
    > Pokud používáte službu AD FS jako vašeho systému identit, se vyžadují služby AD FS a graf. Příklad:
    >
    > ```powershell  
    > $directories = 'ACSBlob', 'ACSQueue', 'ACSTable', 'ADFS', 'Admin Extension Host', 'Admin Portal', 'ARM Admin', 'ARM Public', 'Graph', 'KeyVault', 'KeyVaultInternal', 'Public Extension Host', 'Public Portal'
    > ```
    
     - Vaše certifikáty umístíte v příslušné adresáře vytvořené v předchozím kroku. Příklad:  
        - `c:\certificates\ACSBlob\CustomerCertificate.pfx`
        - `c:\certificates\Admin Portal\CustomerCertificate.pfx`
        - `c:\certificates\ARM Admin\CustomerCertificate.pfx`

3. V okně Powershellu změnit hodnoty **RegionName** a **plně kvalifikovaný název domény** vhodnými pro prostředí Azure Stack a spusťte následující příkaz:

    ```powershell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString 

    Invoke-AzsCertificateValidation -CertificatePath c:\certificates -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD  
    ```

4. Zkontrolujte výstup a všechny jejich certifikáty projít všemi testy. Příklad:

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

**Příznak**: Testy se přeskočí.

**Příčina:** Pokud není splněná závislost, přeskočí AzsReadinessChecker určité testy:

 - Další certifikáty se přeskočí, pokud selže řetěz certifikátů.

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

**Řešení:** Postupujte podle pokynů nástroje v části Podrobnosti každého sadu testů pro každý certifikát.

## <a name="perform-platform-as-a-service-certificate-validation"></a>Provedení platformy jako ověřování certifikátu služby

Pomocí těchto kroků můžete připravit a ověřit certifikáty infrastruktury veřejných KLÍČŮ Azure Stack pro platformu jako službu (PaaS) certifikáty, pokud máte v plánu nasazení SQL nebo MySQL nebo App Services.

1.  Nainstalujte **AzsReadinessChecker** z příkazového řádku Powershellu (5.1 nebo novější), spuštěním následující rutiny:

    ```powershell  
      Install-Module Microsoft.AzureStack.ReadinessChecker -force
    ```

2.  Vytvoření vnořených zatřiďovací tabulku obsahující cesty a heslo pro každý certifikát PaaS nutnosti ověření. V okně prostředí PowerShell, spusťte:

    ```powershell  
        $PaaSCertificates = @{
        'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
        }
    ```

3.  Změna hodnot **RegionName** a **plně kvalifikovaný název domény** tak, aby odpovídaly vaším prostředím Azure Stack spusťte ověření. Potom následujícím příkazem:

    ```powershell  
    Invoke-AzsCertificateValidation -PaaSCertificates $PaaSCertificates -RegionName east -FQDN azurestack.contoso.com 
    ```
4.  Zkontrolujte výstup a že všechny certifikáty projít všemi testy.

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

| Adresář | Certifikát |
| ---    | ----        |
| acsBlob | wildcard_blob_\<region>_\<externalFQDN> |
| ACSQueue  |  wildcard_queue_\<region>_\<externalFQDN> |
| ACSTable  |  wildcard_table_\<region>_\<externalFQDN> |
| Admin Extension Host  |  wildcard_adminhosting_\<region>_\<externalFQDN> |
| Portál pro správu  |  adminportal_\<region>_\<externalFQDN> |
| ARM správce  |  adminmanagement_\<region>_\<externalFQDN> |
| Veřejné ARM  |  management_\<region>_\<externalFQDN> |
| KeyVault  |  wildcard_vault_\<region>_\<externalFQDN> |
| KeyVaultInternal  |  wildcard_adminvault_\<region>_\<externalFQDN> |
| Public Extension Host  |  wildcard_hosting_\<region>_\<externalFQDN> |
| Veřejný portál  |  portal_\<region>_\<externalFQDN> |

## <a name="using-validated-certificates"></a>Použití ověřených certifikátů

Jakmile vaše certifikáty mají byl ověřen voláním AzsReadinessChecker, jste připraveni k jejich použití ve vašem nasazení služby Azure Stack nebo pro rotaci tajných kódů služby Azure Stack. 

 - Pro nasazení, bezpečně přenést vaše certifikáty pro pracovníka nasazení tak, aby je mohli zkopírovat do nasazení hostitele, jak je uvedeno v [dokumentaci k požadavkům Azure Stack infrastruktury veřejných KLÍČŮ](azure-stack-pki-certs.md).
 - Pro tajné otočení, můžete použít certifikáty k aktualizaci staré certifikáty pro prostředí Azure Stack infrastruktury veřejných koncových bodů podle [dokumentace ke službě Azure Stack tajný klíč otočení](azure-stack-rotate-secrets.md).
 - Pro služby PaaS, můžete použít certifikáty k instalaci SQL, MySQL a poskytovatele prostředků App Services ve službě Azure Stack podle [přehled nabízených služeb v dokumentaci k Azure Stack](azure-stack-offer-services-overview.md).

## <a name="next-steps"></a>Další postup

[Integrace identit datového centra](azure-stack-integrate-identity.md)
