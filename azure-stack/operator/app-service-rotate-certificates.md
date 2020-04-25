---
title: Otočení App Service v tajných klíčích a certifikátech centra Azure Stack
description: Naučte se, jak pomocí Azure App Service v centru Azure Stack otáčet tajné klíče a certifikáty, které používá.
author: apwestgarth
ms.topic: article
ms.date: 04/09/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 04/09/2020
ms.openlocfilehash: 39431c7e217410f7bae78fe114595ec6c90399b5
ms.sourcegitcommit: b185ab34c4c799892948536dd6d1d1b2fc31174e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/24/2020
ms.locfileid: "82150248"
---
# <a name="rotate-app-service-on-azure-stack-hub-secrets-and-certificates"></a>Otočení App Service v tajných klíčích a certifikátech centra Azure Stack

Tyto pokyny platí pouze pro Azure App Service v centru Azure Stack. V centralizovaném postupu pro rotaci tajných klíčů pro Azure Stack centrum není zahrnutá rotace Azure App Service v tajných klíčích centra Azure Stack. Operátoři můžou monitorovat platnost tajných kódů v rámci systému, datum, kdy se data naposledy aktualizovala, a zbývající čas do vypršení platnosti tajných klíčů.

> [!Important]
> Operátoři neobdrží výstrahy pro vypršení platnosti tajného kódu na řídicím panelu centra Azure Stack, protože Azure App Service v Azure Stack hub není integrován do služby upozorňování centra Azure Stack. Obsluha musí pravidelně monitorovat své tajné klíče pomocí Azure App Service v Azure Stack prostředí pro správu centra na portálu Azure Stack správce centra.

Tento dokument obsahuje postup pro otočení následujících tajných kódů:

* Šifrovací klíče používané v rámci Azure App Service v centru Azure Stack.
* Přihlašovací údaje pro připojení k databázi, které používá Azure App Service v centru Azure Stack k interakci s databázemi hostování a měření.
* Certifikáty, které používá Azure App Service v centru Azure Stack k zabezpečení koncových bodů a rotace certifikátů aplikací identity v Azure Active Directory (Azure AD) nebo Active Directory Federation Services (AD FS) (AD FS).
* Přihlašovací údaje systému pro Azure App Service v rolích infrastruktury centra Azure Stack

## <a name="rotate-encryption-keys"></a>Otočení šifrovacích klíčů

Pokud chcete otočit šifrovací klíče používané v Azure App Service v centru Azure Stack, proveďte následující kroky:

1. Na portálu Azure Stack centra pro správu přejdete na prostředí pro správu App Service.

1. Přejděte do možnosti nabídky **tajných klíčů** .

1. V části šifrovací klíče vyberte tlačítko **otočit** .

1. Výběrem **OK** spusťte postup otočení.

1. Šifrovací klíče se otočí a všechny instance rolí se aktualizují. Operátory mohou kontrolovat stav procedury pomocí tlačítka **stav** .

## <a name="rotate-connection-strings"></a>Otočení připojovacích řetězců

Chcete-li aktualizovat přihlašovací údaje pro připojovací řetězec databáze pro App Service hostování a měření databází, proveďte následující kroky:

1. Na portálu Azure Stack centra pro správu přejdete na prostředí pro správu App Service.

1. Přejděte do možnosti nabídky **tajných klíčů** .

1. V části připojovací řetězce vyberte tlačítko **otočit** .

1. Zadejte uživatelské jméno a **heslo** **SA SQL** a výběrem **OK** spusťte postup otočení.

1. Přihlašovací údaje se otočí v rámci Azure App Service instancí rolí. Operátory mohou kontrolovat stav procedury pomocí tlačítka **stav** .

## <a name="rotate-certificates"></a>Rotace certifikátů

Pokud chcete otočit certifikáty používané v Azure App Service v centru Azure Stack, proveďte následující kroky:

1. Na portálu Azure Stack centra pro správu přejdete na prostředí pro správu App Service.

1. Přejděte do možnosti nabídky **tajných klíčů** .

1. V části certifikáty vyberte tlačítko **otočit** .

1. Zadejte **soubor certifikátu** a příslušné **heslo** pro certifikáty, které chcete otočit, a vyberte **OK**.

1. Certifikáty se v Azure App Service v instancích rolí centra Azure Stack otáčejí podle požadavků. Operátory mohou kontrolovat stav procedury pomocí tlačítka **stav** .

Když se certifikát aplikace identity otočí, musí se taky aktualizovat odpovídající aplikace ve službě Azure AD nebo AD FS s novým certifikátem.

> [!IMPORTANT]
> Po rotaci se nepovedlo aktualizovat aplikaci identity pomocí nového certifikátu, aby se pro Azure Functions přerušilo prostředí uživatelského portálu, aby uživatelé nemohli používat nástroje pro vývojáře v KUDU a aby správci nemohli spravovat sady škálování na úrovni pracovního procesu z prostředí App Service správy.

### <a name="rotate-credential-for-the-azure-ad-identity-application"></a>Otočit přihlašovací údaje pro aplikaci identit Azure AD

Aplikace identity se vytvoří pomocí operátoru před nasazením Azure App Service v centru Azure Stack. Pokud je ID aplikace neznámé, můžete ji zjistit pomocí těchto kroků:

1. Přejít na **portál pro správu centra Azure Stack**.

1. Přejdete na **předplatná** a vyberte **výchozí předplatné poskytovatele**.

1. Vyberte **Access Control (IAM)** a vyberte **App Service** aplikace.

1. Poznamenejte si **ID aplikace**, tato hodnota je ID aplikace identity, která se musí v Azure AD aktualizovat.

Pokud chcete otočit certifikát pro aplikaci ve službě Azure AD, postupujte podle těchto kroků:

1. Přejít na **Azure Portal** a přihlaste se pomocí globálního správce, který se používá k nasazení centra Azure Stack.

1. Přejděte na **Azure Active Directory** a přejděte na **Registrace aplikací**.

1. Vyhledejte **ID aplikace**a pak zadejte ID aplikace identity.

1. Vyberte aplikaci a potom vyhledejte **certifikáty & tajných**kódů.

1. Vyberte **Odeslat certifikát** a nahrajte nový certifikát pro aplikaci identity pomocí jednoho z následujících typů souborů:. CER,. pem,. CRT.

1. Ověřte shodu **otisku prstu** , která je uvedená v možnosti správy App Service na portálu Azure Stack centrum pro správu.

1. Odstraňte starý certifikát.

### <a name="rotate-certificate-for-ad-fs-identity-application"></a>Otočit certifikát pro aplikaci AD FS identity

Aplikace identity se vytvoří pomocí operátoru před nasazením Azure App Service v centru Azure Stack. Pokud je ID objektu aplikace neznámé, použijte k jeho zjištění následující postup:

1. Přejít na **portál pro správu centra Azure Stack**.

1. Přejdete na **předplatná** a vyberte **výchozí předplatné poskytovatele**.

1. Vyberte možnost **Access Control (IAM)** a vyberte **AzureStack-AppService-<guid> ** Application.

1. Poznamenejte si **ID objektu**, tato hodnota je ID instančního objektu, který se musí aktualizovat v AD FS.

K otočení certifikátu aplikace v AD FS musíte mít přístup k privilegovanému koncovému bodu (PEP). Pak aktualizujete přihlašovací údaje certifikátu pomocí PowerShellu a nahradíte vlastní hodnoty pro následující zástupné symboly:

| Zástupný symbol | Popis | Příklad |
| ----------- | ----------- | ------- |
| `<PepVM>` | Název virtuálního počítače privilegovaného koncového bodu na instanci centra Azure Stack. | "AzS-ERCS01" |
| `<CertificateFileLocation>` | Umístění certifikátu x509 na disku | "d:\certs\sso.cer" |
| `<ApplicationObjectId>` | Identifikátor přiřazený k aplikaci identity | "S-1-5-21-401916501-2345862468-1451220656-1451" |

1. Otevřete relaci Windows PowerShellu se zvýšenými oprávněními a spusťte následující skript:

    ```powershell
    # Sign in to PowerShell interactively, using credentials that have access to the VM running the Privileged Endpoint
    $Creds = Get-Credential

    # Create a new Certificate object from the identity application certificate exported as .cer file
    $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2("<CertificateFileLocation>")

    # Create a new PSSession to the PrivelegedEndpoint VM
    $Session = New-PSSession -ComputerName "<PepVm>" -ConfigurationName PrivilegedEndpoint -Credential $Creds

    # Use the privileged endpoint to update the certificate thumbprint, used by the service principal associated with the App Service identity application
    $SpObject = Invoke-Command -Session $Session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier "<ApplicationObjectId>" -ClientCertificates $using:Cert}
    $Session | Remove-PSSession

    # Output the updated service principal details
    $SpObject

    ```

1. Po dokončení skriptu se zobrazí aktualizované informace o registraci aplikace, včetně hodnoty kryptografického otisku certifikátu.

    ```shell
    ApplicationIdentifier : S-1-5-21-401916501-2345862468-1451220656-1451
    ClientId              : 
    Thumbprint            : FDAA679BF9EDDD0CBB581F978457A37BFD73CA3B
    ApplicationName       : Azurestack-AppService-d93601c2-1ec0-4cac-8d1c-8ccde63ef308
    ClientSecret          : 
    PSComputerName        : AzS-ERCS01
    RunspaceId            : cb471c79-a0d3-40ec-90ba-89087d104510
    ```

## <a name="rotate-system-credentials"></a>Otočit systémová pověření

K otočení systémových přihlašovacích údajů používaných v Azure App Service v centru Azure Stack proveďte následující kroky:

1. Na portálu Azure Stack centra pro správu přejdete na prostředí pro správu App Service.

1. Přejděte do možnosti nabídky **tajných klíčů** .

1. V části systémové přihlašovací údaje vyberte tlačítko **otočit** .

1. Vyberte **Rozsah** systémových přihlašovacích údajů, které se chystáte otočit. Operátory se můžou rozhodnout pro otočení systémových přihlašovacích údajů pro všechny role nebo jednotlivé role.

1. Zadejte **nové uživatelské jméno místního správce** a nové **heslo**. Pak potvrďte **heslo** a vyberte **OK**.

1. Přihlašovací údaje se v rámci odpovídajících Azure App Service v instanci role centra Azure Stack otáčejí podle požadavků. Operátory mohou kontrolovat stav procedury pomocí tlačítka **stav** .
