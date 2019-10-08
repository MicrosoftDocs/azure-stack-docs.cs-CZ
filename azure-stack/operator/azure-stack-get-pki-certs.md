---
title: Generovat žádosti o podepsání certifikátu pro Azure Stack | Microsoft Docs
description: Naučte se generovat žádosti o podepsání certifikátu pro Azure Stack certifikáty PKI v Azure Stack integrovaných systémech.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2019
ms.author: justinha
ms.reviewer: ppacent
ms.lastreviewed: 09/10/2019
ms.openlocfilehash: 365f727f7e07c697dc2fd3cfe2a5c1bea5b68409
ms.sourcegitcommit: 451cfaa24b349393f36ae9d646d4d311a14dd1fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2019
ms.locfileid: "72019257"
---
# <a name="generate-certificate-signing-requests-for-azure-stack"></a>Generovat žádosti o podepsání certifikátu pro Azure Stack

Pomocí nástroje pro kontrolu připravenosti na Azure Stack můžete vytvořit žádosti o podepsání certifikátů, které jsou vhodné pro Azure Stack nasazení. Certifikáty by se měly požadovat, vygenerovat a ověřit dostatečně včas k testování před nasazením. Nástroj můžete získat [z Galerie prostředí PowerShell](https://aka.ms/AzsReadinessChecker).

K vyžádání následujících certifikátů můžete použít nástroj pro kontrolu připravenosti na Azure Stack (AzsReadinessChecker):

- **Standardní požadavky na certifikáty** podle [vygenerování žádosti o podepsání certifikátu](azure-stack-get-pki-certs.md#generate-certificate-signing-requests)
- **Platform-as-a-Service**: Pro certifikáty můžete požádat o názvy PaaS (platforma jako služba), jak je uvedeno v [Azure Stack požadavky na certifikát infrastruktury veřejných klíčů – volitelné certifikáty PaaS](azure-stack-pki-certs.md#optional-paas-certificates).

## <a name="prerequisites"></a>Požadavky

Před vygenerováním jakýchkoli zástupců pro certifikáty PKI pro nasazení Azure Stack musí systém splňovat následující požadavky:

- Kontrola připravenosti na Microsoft Azure Stack
- Atributy certifikátu:
  - Název oblasti
  - Externí plně kvalifikovaný název domény (FQDN)
  - Subject
- Windows 10 nebo Windows Server 2016 nebo novější

  > [!NOTE]  
  > Když obdržíte své certifikáty zpátky od certifikační autority, kroky v části [příprava Azure Stack certifikátů PKI](azure-stack-prepare-pki-certs.md) se musí dokončit ve stejném systému!

## <a name="generate-certificate-signing-requests"></a>Generování žádostí o podepsání certifikátu

Pomocí těchto kroků Připravte a ověřte Azure Stack certifikáty PKI:

1. Nainstalujte AzsReadinessChecker z příkazového řádku PowerShellu (5,1 nebo vyšší) spuštěním následující rutiny:

    ```powershell  
        Install-Module Microsoft.AzureStack.ReadinessChecker
    ```

2. Deklarujte **Předmět** jako seřazený slovník. Příklad:

    ```powershell  
    $subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"}
    ```

    > [!note]  
    > Pokud je zadán běžný název (CN), bude tento název přepsán prvním názvem DNS žádosti o certifikát.

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

5. Deklarujete **název oblasti** a **externí plně kvalifikovaný název domény** , který je určený pro nasazení Azure Stack.

    ```powershell
    $regionName = 'east'
    $externalFQDN = 'azurestack.contoso.com'
    ```

    > [!note]  
    > `<regionName>.<externalFQDN>` je základem, na kterém jsou vytvořeny všechny názvy externích názvů DNS v Azure Stack v tomto příkladu by byl portál `portal.east.azurestack.contoso.com`.  

6. Vygenerujte žádosti o podepsání certifikátu pro každý název DNS:

    ```powershell  
    New-AzsCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ```

    Pokud chcete zahrnout služby PaaS Services, zadejte přepínač ```-IncludePaaS```.

7. Případně pro vývojová/testovací prostředí vygenerujte jednu žádost o certifikát s více alternativními názvy subjektu Add **-RequestType SingleCSR** parametr a Value (nedoporučuje**se** pro produkční prostředí):

    ```powershell  
    New-AzsCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -RequestType SingleCSR -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ```

    Pokud chcete zahrnout služby PaaS Services, zadejte přepínač ```-IncludePaaS```.

8. Zkontrolujte výstup:

    ```powershell  
    New-AzsCertificateSigningRequest v1.1809.1005.1 started.

    CSR generating for following SAN(s): dns=*.east.azurestack.contoso.com&dns=*.blob.east.azurestack.contoso.com&dns=*.queue.east.azurestack.contoso.com&dns=*.table.east.azurestack.cont
    oso.com&dns=*.vault.east.azurestack.contoso.com&dns=*.adminvault.east.azurestack.contoso.com&dns=portal.east.azurestack.contoso.com&dns=adminportal.east.azurestack.contoso.com&dns=ma
    nagement.east.azurestack.contoso.com&dns=adminmanagement.east.azurestack.contoso.com*dn2=*.adminhosting.east.azurestack.contoso.com@dns=*.hosting.east.azurestack.contoso.com
    Present this CSR to your Certificate Authority for Certificate Generation: C:\Users\username\Documents\AzureStackCSR\wildcard_east_azurestack_contoso_com_CertRequest_20180405233530.req
    Certreq.exe output: CertReq: Request Created

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    New-AzsCertificateSigningRequest Completed
    ```

9. Odeslat **. Soubor požadavku** vygenerovaný vaší certifikační autoritou (buď interní, nebo veřejný). Výstupní adresář **New-AzsCertificateSigningRequest** obsahuje oddělení služeb zákazníkům, které je nutné odeslat certifikační autoritě. Tento adresář obsahuje také podřízený adresář, který obsahuje soubory INF použité během generování žádosti o certifikát. Ujistěte se, že certifikační autorita generuje certifikáty pomocí vygenerované žádosti, která splňuje [Azure Stack požadavky PKI](azure-stack-pki-certs.md).

## <a name="next-steps"></a>Další kroky

[Příprava Azure Stack certifikátů PKI](azure-stack-prepare-pki-certs.md)
