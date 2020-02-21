---
title: Vygenerovat žádosti o podepsání certifikátu pro centrum Azure Stack
description: Naučte se generovat žádosti o podepsání certifikátu pro Azure Stack centrálních certifikátů PKI v integrovaných systémech centra Azure Stack.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 09/10/2019
ms.author: inhenkel
ms.reviewer: ppacent
ms.lastreviewed: 09/10/2019
ms.openlocfilehash: f0620162767ea69c8c8221f13bd698f003424a58
ms.sourcegitcommit: 97806b43314d306e0ddb15847c86be2c92ae001e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2020
ms.locfileid: "77509938"
---
# <a name="generate-certificate-signing-requests-for-azure-stack-hub"></a>Vygenerovat žádosti o podepsání certifikátu pro centrum Azure Stack

Nástroj pro kontrolu připravenosti centra Azure Stack můžete použít k vytvoření žádostí o podepsání certifikátů, které jsou vhodné pro nasazení centra Azure Stack. Certifikáty by se měly požadovat, vygenerovat a ověřit dostatečně včas k testování před nasazením. Nástroj můžete získat [z Galerie prostředí PowerShell](https://aka.ms/AzsReadinessChecker).

K vyžádání následujících certifikátů můžete použít nástroj pro kontrolu připravenosti centra Azure Stack (AzsReadinessChecker):

- **Standardní požadavky na certifikáty** podle [vygenerování žádosti o podepsání certifikátu](azure-stack-get-pki-certs.md#generate-certificate-signing-requests)
- **Platforma jako služba**: pro certifikáty můžete požádat o názvy PaaS (platforma jako služba), které jsou uvedené v [požadavcích na certifikát infrastruktury veřejných klíčů služby Azure Stack hub – volitelné certifikáty PaaS](azure-stack-pki-certs.md#optional-paas-certificates).

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

## <a name="generate-certificate-signing-requests"></a>Generování žádostí o podepsání certifikátu

Pomocí těchto kroků můžete připravit a ověřit certifikáty PKI Azure Stack hub:

1. Nainstalujte AzsReadinessChecker z příkazového řádku PowerShellu (5,1 nebo vyšší) spuštěním následující rutiny:

    ```powershell  
        Install-Module Microsoft.AzureStack.ReadinessChecker
    ```

2. Deklarovat **Předmět** Příklad:

    ```powershell  
    $subject = "C=US,ST=Washington,L=Redmond,O=Microsoft,OU=Azure Stack Hub"
    ```

    > [!NOTE]  
    > Pokud je zadán běžný název (CN), bude nakonfigurován na každou žádost o certifikát. Pokud je parametr CN vynechán, bude na žádost o certifikát nakonfigurován první název DNS služby Azure Stack hub.

3. Deklarujete výstupní adresář, který již existuje. Příklad:

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
    > `<regionName>.<externalFQDN>` tvoří základ, na kterém jsou vytvořeny všechny názvy externích DNS v centru Azure Stack. V tomto příkladu by byl portál `portal.east.azurestack.contoso.com`.  

6. Generování žádostí o podepsání certifikátu pro nasazení:

    ```powershell  
    New-AzsCertificateSigningRequest -certificateType Deployment -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ```

    Chcete-li generovat žádosti o certifikát pro jiné služby Azure Stack centra, změňte hodnotu `-CertificateType`. Příklad:

    ```powershell  
    # App Services
    New-AzsCertificateSigningRequest -certificateType AppServices -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory

    # DBAdapter
    New-AzsCertificateSigningRequest -certificateType DBAdapter -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory

    # EventHubs
    New-AzsCertificateSigningRequest -certificateType EventHubs -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory

    # IoTHub
    New-AzsCertificateSigningRequest -certificateType IoTHub -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory
    ```

7. Případně pro vývojová/testovací prostředí vygenerujte jednu žádost o certifikát s více alternativními názvy subjektu Add **-RequestType SingleCSR** parametr a Value (nedoporučuje**se** pro produkční prostředí):

    ```powershell  
    New-AzsCertificateSigningRequest -certificateType Deployment -RegionName $regionName -FQDN $externalFQDN -RequestType SingleCSR -subject $subject -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ```

8.  Zkontrolujte výstup:

    ```powershell  
    New-AzsCertificateSigningRequest v1.1912.1082.37 started.
    Starting Certificate Request Process for Deployment
    CSR generating for following SAN(s): *.adminhosting.east.azurestack.contoso.com,*.adminvault.east.azurestack.contoso.com,*.blob.east.azurestack.contoso.com,*.hosting.east.azurestack.contoso.com,*.queue.east.azurestack.contoso.com,*.table.east.azurestack.contoso.com,*.vault.east.azurestack.contoso.com,adminmanagement.east.azurestack.contoso.com,adminportal.east.azurestack.contoso.com,management.east.azurestack.contoso.com,portal.east.azurestack.contoso.com
    Present this CSR to your Certificate Authority for Certificate Generation: C:\Users\checker\Documents\AzureStackCSR\wildcard_adminhosting_east_azurestack_contoso_com_CertRequest_20191219140359.req
    Certreq.exe output: CertReq: Request Created

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    New-AzsCertificateSigningRequest Completed
    ```

9.  Odeslat **. Soubor požadavku** vygenerovaný vaší certifikační autoritou (buď interní, nebo veřejný). Výstupní adresář **New-AzsCertificateSigningRequest** obsahuje oddělení služeb zákazníkům, které je nutné odeslat certifikační autoritě. Tento adresář obsahuje také podřízený adresář, který obsahuje soubory INF použité během generování žádosti o certifikát. Ujistěte se, že certifikační autorita generuje certifikáty pomocí vygenerované žádosti, která splňuje [požadavky infrastruktury PKI centra Azure Stack](azure-stack-pki-certs.md).

## <a name="next-steps"></a>Další kroky

[Příprava certifikátů PKI Azure Stack hub](azure-stack-prepare-pki-certs.md)
