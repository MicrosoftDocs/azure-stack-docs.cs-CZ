---
title: Vygenerovat žádosti o podepsání certifikátu pro centrum Azure Stack
description: Naučte se generovat žádosti o podepsání certifikátu pro Azure Stack centrálních certifikátů PKI v integrovaných systémech centra Azure Stack.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 10/19/2020
ms.author: inhenkel
ms.reviewer: ppacent
ms.lastreviewed: 10/19/2020
ms.openlocfilehash: 1b7737f387ea1ea3afc913116642605fa54818a6
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94543711"
---
# <a name="generate-certificate-signing-requests-for-azure-stack-hub"></a>Vygenerovat žádosti o podepsání certifikátu pro centrum Azure Stack

Nástroj pro kontrolu připravenosti centra Azure Stack můžete použít k vytvoření žádostí o podepsání certifikátů, které jsou vhodné pro nasazení centra Azure Stack. Certifikáty by se měly požadovat, vygenerovat a ověřit dostatečně včas k testování před nasazením. Nástroj můžete získat [z Galerie prostředí PowerShell](https://aka.ms/AzsReadinessChecker).

K vyžádání následujících certifikátů můžete použít nástroj pro kontrolu připravenosti centra Azure Stack (AzsReadinessChecker):

- **Standardní požadavky na certifikáty** podle [vygenerování žádosti o podepsání certifikátu pro nová nasazení](azure-stack-get-pki-certs.md#generate-certificate-signing-requests-for-new-deployments).
- **Požadavky na obnovení certifikátu** podle [vygenerování žádosti o podepsání certifikátu pro obnovení certifikátu](azure-stack-get-pki-certs.md#generate-certificate-signing-requests-for-certificate-renewal).
- **Platforma jako služba** : pro certifikáty můžete požádat o názvy PaaS (platforma jako služba), které jsou uvedené v [požadavcích na certifikát infrastruktury veřejných klíčů služby Azure Stack hub – volitelné certifikáty PaaS](azure-stack-pki-certs.md#optional-paas-certificates).

## <a name="prerequisites"></a>Požadavky

Před generováním jakýchkoli zástupců pro certifikáty PKI pro nasazení Azure Stackho centra by měl systém splňovat následující požadavky:

- Kontrola připravenosti centra Microsoft Azure Stack
- Atributy certifikátu:
  - Název oblasti
  - Externí plně kvalifikovaný název domény (FQDN)
  - Předmět
- Windows 10 nebo Windows Server 2016 nebo novější

  > [!NOTE]  
  > Když obdržíte své certifikáty zpátky od certifikační autority, musíte provést kroky v části [Příprava certifikátů PKI ve službě Azure Stack hub](azure-stack-prepare-pki-certs.md) .

## <a name="generate-certificate-signing-requests-for-new-deployments"></a>Generování žádostí o podepsání certifikátu pro nová nasazení

Pomocí těchto kroků Připravte žádosti o podepsání certifikátu pro nové certifikáty PKI centra Azure Stack:

1. Nainstalujte AzsReadinessChecker z příkazového řádku PowerShellu (5,1 nebo vyšší) spuštěním následující rutiny:

    ```powershell  
        Install-Module Microsoft.AzureStack.ReadinessChecker
    ```

2. Deklarovat **Předmět** Například:

    ```powershell  
    $subject = "C=US,ST=Washington,L=Redmond,O=Microsoft,OU=Azure Stack Hub"
    ```

    > [!NOTE]  
    > Pokud je zadán běžný název (CN), bude nakonfigurován na každou žádost o certifikát. Pokud je parametr CN vynechán, bude na žádost o certifikát nakonfigurován první název DNS služby Azure Stack hub.

3. Deklarujete výstupní adresář, který již existuje. Například:

    ```powershell  
    $outputDirectory = "$ENV:USERPROFILE\Documents\AzureStackCSR"
    ```

4. Deklarovat systém identit

    Azure Active Directory (Azure AD):

    ```powershell
    $IdentitySystem = "AAD"
    ```

    Active Directory Federation Services (AD FS) (AD FS):

    ```powershell
    $IdentitySystem = "ADFS"
    ```
    > [!NOTE]  
    > Parametr je vyžadován pouze pro nasazení CertificateType.

5. Deklarujete **název oblasti** a **externí plně kvalifikovaný název domény** , který je určený pro nasazení centra Azure Stack.

    ```powershell
    $regionName = 'east'
    $externalFQDN = 'azurestack.contoso.com'
    ```

    > [!NOTE]  
    > `<regionName>.<externalFQDN>` vychází z nich, na základě kterých jsou vytvořeny všechny názvy externích DNS v centru Azure Stack. V tomto příkladu by byl portál `portal.east.azurestack.contoso.com` .  

6. Generování žádostí o podepsání certifikátu pro nasazení:

    ```powershell  
    New-AzsHubDeploymentCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ```

    Chcete-li generovat žádosti o certifikát pro jiné služby Azure Stack centra, změňte hodnotu pro `-CertificateType` . Například:

    ```powershell  
    # App Services
    New-AzsHubAppServicesCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory

    # DBAdapter
    New-AzsHubDbAdapterCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory

    # EventHubs
    New-AzsHubEventHubsCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory

    # IoTHub
    New-AzsHubIoTHubCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory
    ```

7. Případně pro vývojová/testovací prostředí vygenerujte jednu žádost o certifikát s více alternativními názvy subjektu Add **-RequestType SingleCSR** parametr a Value (nedoporučuje **se** pro produkční prostředí):

    ```powershell  
    New-AzsHubDeploymentCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -RequestType SingleCSR -subject $subject -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ```

8.  Zkontrolujte výstup:

    ```powershell  
    Starting Certificate Request Process for Deployment
    CSR generating for following SAN(s): *.adminhosting.east.azurestack.contoso.com,*.adminvault.east.azurestack.contoso.com,*.blob.east.azurestack.contoso.com,*.hosting.east.azurestack.contoso.com,*.queue.east.azurestack.contoso.com,*.table.east.azurestack.contoso.com,*.vault.east.azurestack.contoso.com,adminmanagement.east.azurestack.contoso.com,adminportal.east.azurestack.contoso.com,management.east.azurestack.contoso.com,portal.east.azurestack.contoso.com
    Present this CSR to your Certificate Authority for Certificate Generation: C:\Users\[*redacted*]\Documents\AzureStackCSR\Deployment_east_azurestack_contoso_com_SingleCSR_CertRequest_20200710165538.req
    Certreq.exe output: CertReq: Request Created
    ```

9.  Odeslat **. Soubor požadavku** vygenerovaný vaší certifikační autoritou (buď interní, nebo veřejný). Výstupní adresář **New-AzsCertificateSigningRequest** obsahuje oddělení služeb zákazníkům, které je nutné odeslat certifikační autoritě. Tento adresář obsahuje také podřízený adresář, který obsahuje soubory INF použité během generování žádosti o certifikát. Ujistěte se, že certifikační autorita generuje certifikáty pomocí vygenerované žádosti, která splňuje [požadavky infrastruktury PKI centra Azure Stack](azure-stack-pki-certs.md).

## <a name="generate-certificate-signing-requests-for-certificate-renewal"></a>Generovat žádosti o podepsání certifikátu pro obnovení certifikátu

Pomocí těchto kroků Připravte žádosti o podepsání certifikátů na obnovení stávajících certifikátů PKI Azure Stack hub:

1. Nainstalujte AzsReadinessChecker z příkazového řádku PowerShellu (5,1 nebo vyšší) spuštěním následující rutiny:

    ```powershell  
        Install-Module Microsoft.AzureStack.ReadinessChecker -Force -AllowPrerelease
    ```

2. Deklarujte **stampEndpoint** ve formě regionname.domain.com systému Azure Stack hub. Například (Pokud je adresa portálu klienta centra Azure Stack <code> https://</code> <code>portal.east.azurestack.contoso.com</code> ):

    ```powershell  
    $stampEndpoint = 'east.azurestack.contoso.com'
    ```

    > [!NOTE]  
    > Pro výše uvedený systém centra Azure Stack se vyžaduje připojení HTTPS.
    > Nástroj pro kontrolu připravenosti bude používat stampendpoint (oblast a doména) k vytvoření ukazatele na existující certifikáty, které jsou vyžadovány typem certifikátu, např. k předvedení portálu pro certifikáty nasazení, takže portal.east.azurestack.contoso.com se používá při klonování certifikátů, a to pro aplikační služby sso.appservices.east.azurestack.contoso.com atd. Certifikát vázaný k vypočítanému koncovému bodu se použije ke klonování atributů, jako je například předmět, délka klíče, algoritmus podpisu.  Pokud chcete změnit některý z těchto atributů, použijte místo toho postup pro [vygenerování žádosti o podepsání certifikátu pro nová nasazení](azure-stack-get-pki-certs.md#generate-certificate-signing-requests-for-new-deployments) .

3. Deklarujete výstupní adresář, který již existuje. Například:

    ```powershell  
    $outputDirectory = "$ENV:USERPROFILE\Documents\AzureStackCSR"
    ```

4. Generování žádostí o podepsání certifikátu pro nasazení:

    ```powershell  
    New-AzsHubDeploymentCertificateSigningRequest -StampEndpoint $stampEndpoint -OutputRequestPath $OutputDirectory
    ```

    Pokud chcete generovat žádosti o certifikát pro jiné služby Azure Stack centra, použijte:

    ```powershell  
    # App Services
    New-AzsHubAppServicesCertificateSigningRequest -StampEndpoint $stampEndpoint -OutputRequestPath $OutputDirectory

    # DBAdapter
    New-AzsHubDBAdapterCertificateSigningRequest -StampEndpoint $stampEndpoint -OutputRequestPath $OutputDirectory

    # EventHubs
    New-AzsHubEventHubsCertificateSigningRequest -StampEndpoint $stampEndpoint -OutputRequestPath $OutputDirectory

    # IoTHub
    New-AzsHubIotHubCertificateSigningRequest -StampEndpoint $stampEndpoint -OutputRequestPath $OutputDirectory
    ```

5. Případně pro vývojová/testovací prostředí vygenerujte jednu žádost o certifikát s více alternativními názvy subjektu Add **-RequestType SingleCSR** parametr a Value (nedoporučuje **se** pro produkční prostředí):

    ```powershell  
    New-AzsHubDeploymentCertificateSigningRequest -StampEndpoint $stampendpoint -OutputRequestPath $OutputDirectory -RequestType SingleCSR
    ```

6.  Zkontrolujte výstup:

    ```powershell  
    Querying StampEndpoint portal.east.azurestack.contoso.com for existing certificate
    Starting Certificate Request Process for Deployment
    CSR generating for following SAN(s): *.adminhosting.east.azurestack.contoso.com,*.adminvault.east.azurestack.contoso.com,*.blob.east.azurestack.contoso.com,*.hosting.east.azurestack.contoso.com,*.queue.east.azurestack.contoso.com,*.table.east.azurestack.contoso.com,*.vault.east.azurestack.contoso.com,adminmanagement.east.azurestack.contoso.com,adminportal.east.azurestack.contoso.com,management.east.azurestack.contoso.com,portal.east.azurestack.contoso.com
    Present this CSR to your Certificate Authority for Certificate Generation: C:\Users\[*redacted*]\Documents\AzureStackCSR\Deployment_east_azurestack_contoso_com_SingleCSR_CertRequest_20200710122723.req
    Certreq.exe output: CertReq: Request Created
    ```

7.  Odeslat **. Soubor požadavku** vygenerovaný vaší certifikační autoritou (buď interní, nebo veřejný). Výstupní adresář **New-AzsCertificateSigningRequest** obsahuje oddělení služeb zákazníkům, které je nutné odeslat certifikační autoritě. Tento adresář obsahuje také podřízený adresář, který obsahuje soubory INF použité během generování žádosti o certifikát. Ujistěte se, že certifikační autorita generuje certifikáty pomocí vygenerované žádosti, která splňuje [požadavky infrastruktury PKI centra Azure Stack](azure-stack-pki-certs.md).

## <a name="next-steps"></a>Další kroky

[Příprava certifikátů PKI Azure Stack hub](azure-stack-prepare-pki-certs.md)
