---
title: Použití identity aplikace pro přístup k prostředkům | Microsoft Docs
description: Naučte se spravovat instanční objekt, který se dá použít s řízením přístupu na základě rolí pro přihlašování a přístup k prostředkům.
services: azure-stack
documentationcenter: na
author: BryanLa
manager: femila
ms.service: azure-stack
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/06/2019
ms.author: bryanla
ms.lastreviewed: 11/06/2019
ms.openlocfilehash: 7110febfa58fb1d31cde5f0ae1b4df659f567956
ms.sourcegitcommit: 8203490cf3ab8a8e6d39b137c8c31e3baec52298
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/07/2019
ms.locfileid: "73712737"
---
# <a name="use-an-app-identity-to-access-resources"></a>Použití identity aplikace pro přístup k prostředkům

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit (ASDK)*

Aplikace, která potřebuje nasadit nebo nakonfigurovat prostředky prostřednictvím Azure Resource Manager, musí být reprezentovaná instančním objektem. Stejně jako uživatel je reprezentován objektem zabezpečení uživatele, instanční objekt je typ objektu zabezpečení, který představuje aplikaci. Instanční objekt poskytuje identitu vaší aplikace, což vám umožní delegovat jenom potřebná oprávnění k danému instančnímu objektu.  

Můžete mít například aplikaci pro správu konfigurace, která používá Azure Resource Manager k inventarizaci prostředků Azure. V tomto scénáři můžete vytvořit instanční objekt, udělit tomuto instančnímu objektu roli Čtenář a omezit tak aplikaci pro správu konfigurace jenom na přístup jen pro čtení.

## <a name="overview"></a>Přehled

Jako instanční objekt musí instanční objekt při ověřování prezentovat přihlašovací údaje. Toto ověření se skládá ze dvou prvků:

- **ID aplikace**, někdy označované jako ID klienta. Toto je identifikátor GUID, který jedinečně identifikuje registraci aplikace v tenantovi služby Active Directory.
- **Tajný kód** PŘIDRUŽENÝ k ID aplikace Můžete buď vygenerovat řetězec tajného klíče klienta (podobně jako heslo), nebo zadat certifikát x509 (který používá jeho veřejný klíč).

Spuštění aplikace pod identitou instančního objektu je vhodnější pro jeho spuštění pod objektem zabezpečení uživatele z těchto důvodů:

 - Instanční objekt může použít certifikát x509 pro **silnější přihlašovací údaje**.  
 - Instančnímu objektu můžete přiřadit **více omezující oprávnění** . Obvykle jsou tato oprávnění omezená jenom na to, co aplikace potřebuje, označované jako *Princip nejnižších oprávnění*.
 - Přihlašovací údaje instančního objektu **a oprávnění se nemění tak často** jako přihlašovací údaje uživatele. Například když se změní zodpovědnosti uživatele, požadavky na heslo dostanou změnu nebo uživatel odejde ze společnosti.

Začnete vytvořením nové registrace aplikace ve vašem adresáři, která vytvoří přidružený [objekt instančního objektu](/azure/active-directory/develop/developer-glossary#service-principal-object) , který bude představovat identitu aplikace v adresáři. Tento dokument popisuje proces vytváření a správy instančního objektu v závislosti na adresáři, který jste zvolili pro vaši instanci Azure Stack:

- Azure Active Directory (Azure AD). Azure AD je víceklientské cloudové adresáře a služba pro správu identit. Službu Azure AD můžete použít s připojenou instancí Azure Stack.
- Active Directory Federation Services (AD FS) (AD FS). AD FS poskytuje zjednodušenou, zabezpečenou federaci identit a možnosti jednotného přihlašování k webu (SSO). AD FS můžete použít s připojenými i odpojenými Azure Stack instancemi.

Nejprve se dozvíte, jak spravovat instanční objekt, a pak přiřazení instančního objektu k roli a omezení jeho přístupu k prostředkům.

## <a name="manage-an-azure-ad-service-principal"></a>Správa instančního objektu služby Azure AD

Pokud jste nasadili Azure Stack se službou Azure AD jako služba pro správu identit, můžete objekty služby vytvářet stejně jako v případě Azure. V této části se dozvíte, jak provádět kroky prostřednictvím Azure Portal. Než začnete, ověřte, že máte [požadovaná oprávnění služby Azure AD](/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions) .

### <a name="create-a-service-principal-that-uses-a-client-secret-credential"></a>Vytvoření instančního objektu, který používá pověření tajného klíče klienta

V této části zaregistrujete aplikaci pomocí Azure Portal, která vytvoří objekt instančního objektu ve vašem tenantovi služby Azure AD. V tomto příkladu se instanční objekt vytvoří s přihlašovacími údaji klienta, ale portál podporuje také pověření x509 založené na certifikátu.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí svého účtu Azure.
2. Vyberte **Azure Active Directory** > **Registrace aplikací** > **Nová registrace**.
3. Zadejte **název** aplikace.
4. Vyberte příslušné **podporované typy účtů**.
5. V části **identifikátor URI pro přesměrování**vyberte jako typ aplikace **Web** a (volitelně) zadejte identifikátor URI přesměrování, pokud to vaše aplikace vyžaduje.
6. Po nastavení hodnot vyberte **Registrovat**. Vytvoří se registrace aplikace a zobrazí se stránka s **přehledem** .
7. Zkopírujte **ID aplikace** pro použití v kódu aplikace. Tato hodnota se také označuje jako ID klienta.
8. Pokud chcete vygenerovat tajný klíč klienta, vyberte stránku **certifikáty & tajných** kódů. Vyberte **Nový tajný klíč klienta**.
9. Zadejte **Popis** tajného kódu a dobu **platnosti** .
10. Až budete hotovi, vyberte **Přidat**.
11. Zobrazí se hodnota tajného klíče. Zkopírujte a uložte tuto hodnotu do jiného umístění, protože ji později nelze načíst. Tajný kód v klientské aplikaci během přihlašování instančního objektu zadáte pomocí ID aplikace.

    ![Uložený klíč v tajných klíčích klienta](./media/azure-stack-create-service-principal/create-service-principal-in-azure-stack-secret.png)

## <a name="manage-an-ad-fs-service-principal"></a>Správa instančního objektu služby AD FS

Pokud jste nasadili Azure Stack s AD FS jako službu pro správu identit, musíte ke správě instančního objektu použít PowerShell. Níže jsou uvedené příklady pro správu přihlašovacích údajů instančního objektu, demonstrující certifikát x509 a tajný klíč klienta.

Skripty musí být spuštěné v konzole PowerShellu se zvýšenými oprávněními ("spustit jako správce"), která otevře další relaci k virtuálnímu počítači, který je hostitelem privilegovaného koncového bodu pro vaši instanci Azure Stack. Jakmile je vytvořená relace privilegovaného koncového bodu, spustí další rutiny a bude spravovat instanční objekt. Další informace o privilegovaném koncovém bodu najdete v tématu [použití privilegovaného koncového bodu v Azure Stack](azure-stack-privileged-endpoint.md).

### <a name="create-a-service-principal-that-uses-a-certificate-credential"></a>Vytvoření instančního objektu, který používá pověření certifikátu

Při vytváření certifikátu pro přihlašovací údaje instančního objektu musí být splněné tyto požadavky:

 - V případě produkčního prostředí se certifikát musí vystavit buď z interní certifikační autority, nebo z veřejné certifikační autority. Pokud používáte veřejnou certifikační autoritu, musíte jako součást programu Microsoft Trusted root Authority zahrnout autoritu v základní imagi operačního systému. Úplný seznam můžete najít v [programu Microsoft Trusted Root Certificate Program: účastníci](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca). Příklad vytvoření testovacího certifikátu podepsaného svým držitelem se také zobrazí později během [aktualizace přihlašovacích údajů certifikátu instančního objektu](#update-a-service-principals-certificate-credential). 
 - Zprostředkovatel kryptografických služeb musí být zadaný jako zprostředkovatel klíče zprostředkovatele kryptografických služeb (CSP) pro starší verze společnosti Microsoft.
 - Formát certifikátu musí být v souboru PFX, protože jsou vyžadovány veřejné i privátní klíče. Servery Windows používají soubory. pfx, které obsahují soubor veřejného klíče (soubor certifikátu SSL) a přidružený soubor privátního klíče.
 - Vaše infrastruktura Azure Stack musí mít síťový přístup k umístění seznamu odvolaných certifikátů (CRL) certifikační autority publikovaného v certifikátu. Tento seznam odvolaných certifikátů musí být koncovým bodem HTTP.

Po použití certifikátu použijte níže uvedený skript PowerShellu k registraci aplikace a Vytvoření instančního objektu. Instanční objekt se používá také k přihlášení do Azure. Následující zástupné symboly nahraďte vlastními hodnotami:

| Zástupný symbol | Popis | Příklad: |
| ----------- | ----------- | ------- |
| \<PepVM\> | Název virtuálního počítače privilegovaného koncového bodu na instanci Azure Stack. | "AzS-ERCS01" |
| \<YourCertificateLocation\> | Umístění certifikátu x509 v místním úložišti certifikátů. | "CERT: \ CurrentUser\My\AB5A8A3533CC7AA2025BF05120117E06DE407B34" |
| \<soubor YourAppName\> | Popisný název nové registrace aplikace | "Nástroj pro správu" |

1. Otevřete relaci Windows PowerShellu se zvýšenými oprávněními a spusťte následující skript:

   ```powershell  
    # Sign in to PowerShell interactively, using credentials that have access to the VM running the Privileged Endpoint (typically <domain>\cloudadmin)
    $Creds = Get-Credential

    # Create a PSSession to the Privileged Endpoint VM
    $Session = New-PSSession -ComputerName "<PepVm>" -ConfigurationName PrivilegedEndpoint -Credential $Creds

    # Use the Get-Item cmdlet to retrieve your certificate.
    # If you don't want to use a managed certificate, you can produce a self signed cert for testing purposes: 
    # $Cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange
    $Cert = Get-Item "<YourCertificateLocation>"
    
    # Use the privileged endpoint to create the new app registration (and service principal object)
    $SpObject = Invoke-Command -Session $Session -ScriptBlock {New-GraphApplication -Name "<YourAppName>" -ClientCertificates $using:cert}
    $AzureStackInfo = Invoke-Command -Session $Session -ScriptBlock {Get-AzureStackStampInformation}
    $Session | Remove-PSSession

    # Using the stamp info for your Azure Stack instance, populate the following variables:
    # - AzureRM endpoint used for Azure Resource Manager operations 
    # - Audience for acquiring an OAuth token used to access Graph API 
    # - GUID of the directory tenant
    $ArmEndpoint = $AzureStackInfo.TenantExternalEndpoints.TenantResourceManager
    $GraphAudience = "https://graph." + $AzureStackInfo.ExternalDomainFQDN + "/"
    $TenantID = $AzureStackInfo.AADTenantID

    # Register and set an AzureRM environment that targets your Azure Stack instance
    Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint $ArmEndpoint

    # Sign in using the new service principal identity
    $SpSignin = Connect-AzureRmAccount -Environment "AzureStackUser" `
    -ServicePrincipal `
    -CertificateThumbprint $SpObject.Thumbprint `
    -ApplicationId $SpObject.ClientId `
    -TenantId $TenantID

    # Output the service principal details
    $SpObject

   ```
   
2. Po dokončení skriptu se zobrazí informace o registraci aplikace, včetně přihlašovacích údajů instančního objektu. Jak je znázorněno, `ClientID` a `Thumbprint` se používají k přihlášení pod identitou instančního objektu. Po úspěšném přihlášení se identita instančního objektu použije pro další autorizaci a přístup k prostředkům, které spravuje Azure Resource Manager.

   ```shell
   ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
   ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
   Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
   ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
   ClientSecret          :
   PSComputerName        : azs-ercs01
   RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
   ```

Udržujte relaci konzoly PowerShellu otevřenou, jak ji použijete s hodnotou `ApplicationIdentifier` v další části.

### <a name="update-a-service-principals-certificate-credential"></a>Aktualizovat přihlašovací údaje certifikátu objektu služby

Teď, když jste vytvořili instanční objekt, vám v této části ukážeme, jak:

1. Vytvořte nový certifikát x509 podepsaný svým držitelem pro testování.
2. Aktualizujte přihlašovací údaje instančního objektu tak, že aktualizujete jeho vlastnost **kryptografického otisku** tak, aby odpovídala novému certifikátu.

Aktualizujte přihlašovací údaje certifikátu pomocí PowerShellu a nahraďte vlastní hodnoty pro následující zástupné symboly:

| Zástupný symbol | Popis | Příklad: |
| ----------- | ----------- | ------- |
| \<PepVM\> | Název virtuálního počítače privilegovaného koncového bodu na instanci Azure Stack. | "AzS-ERCS01" |
| \<soubor YourAppName\> | Popisný název nové registrace aplikace | "Nástroj pro správu" |
| \<YourCertificateLocation\> | Umístění certifikátu x509 v místním úložišti certifikátů. | "CERT: \ CurrentUser\My\AB5A8A3533CC7AA2025BF05120117E06DE407B34" |
| \<AppIdentifier\> | Identifikátor přiřazený k registraci aplikace | "S-1-5-21-1512385356-3796245103-1243299919-1356" |

1. Pomocí relace Windows PowerShellu se zvýšenými oprávněními spusťte následující rutiny:

     ```powershell
     # Create a PSSession to the PrivilegedEndpoint VM
     $Session = New-PSSession -ComputerName "<PepVM>" -ConfigurationName PrivilegedEndpoint -Credential $Creds

     # Create a self-signed certificate for testing purposes. 
     $NewCert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange
     # In production, use Get-Item and a managed certificate instead.
     # $Cert = Get-Item "<YourCertificateLocation>"

     # Use the privileged endpoint to update the certificate thumbprint, used by the service principal associated with <AppIdentifier>
     $SpObject = Invoke-Command -Session $Session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier "<AppIdentifier>" -ClientCertificates $using:NewCert}
     $Session | Remove-PSSession

     # Output the updated service principal details
     $SpObject
     ```

2. Po dokončení skriptu se zobrazí aktualizované informace o registraci aplikace, včetně hodnoty kryptografického otisku nového certifikátu podepsaného svým držitelem.

     ```Shell  
     ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
     ClientId              : 
     Thumbprint            : AF22EE716909041055A01FE6C6F5C5CDE78948E9
     ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
     ClientSecret          : 
     PSComputerName        : azs-ercs01
     RunspaceId            : a580f894-8f9b-40ee-aa10-77d4d142b4e5
     ```

### <a name="create-a-service-principal-that-uses-client-secret-credentials"></a>Vytvoření instančního objektu, který používá pověření tajného klíče klienta

> [!IMPORTANT]
> Použití tajného klíče klienta je méně bezpečné než použití přihlašovacích údajů certifikátu x509. Mechanismus ověřování je méně bezpečný, ale obvykle vyžaduje vložení tajného klíče do zdrojového kódu klientské aplikace. V případě produkčních aplikací důrazně doporučujeme použít přihlašovací údaje certifikátu.

Teď vytvoříte jinou registraci aplikace, ale tentokrát určíte přihlašovací údaje klientského klíče. Na rozdíl od přihlašovacích údajů certifikátu má adresář možnost generovat přihlašovací údaje klientského klíče. Místo určení tajného klíče klienta použijete přepínač `-GenerateClientSecret` k vyžádání žádosti, aby se vygeneroval. Následující zástupné symboly nahraďte vlastními hodnotami:

| Zástupný symbol | Popis | Příklad: |
| ----------- | ----------- | ------- |
| \<PepVM\> | Název virtuálního počítače privilegovaného koncového bodu na instanci Azure Stack. | "AzS-ERCS01" |
| \<soubor YourAppName\> | Popisný název nové registrace aplikace | "Nástroj pro správu" |

1. Otevřete relaci Windows PowerShellu se zvýšenými oprávněními a spusťte následující rutiny:

     ```powershell  
     # Sign in to PowerShell interactively, using credentials that have access to the VM running the Privileged Endpoint (typically <domain>\cloudadmin)
     $Creds = Get-Credential

     # Create a PSSession to the Privileged Endpoint VM
     $Session = New-PSSession -ComputerName "<PepVM>" -ConfigurationName PrivilegedEndpoint -Credential $Creds

     # Use the privileged endpoint to create the new app registration (and service principal object)
     $SpObject = Invoke-Command -Session $Session -ScriptBlock {New-GraphApplication -Name "<YourAppName>" -GenerateClientSecret}
     $AzureStackInfo = Invoke-Command -Session $Session -ScriptBlock {Get-AzureStackStampInformation}
     $Session | Remove-PSSession

     # Using the stamp info for your Azure Stack instance, populate the following variables:
     # - AzureRM endpoint used for Azure Resource Manager operations 
     # - Audience for acquiring an OAuth token used to access Graph API 
     # - GUID of the directory tenant
     $ArmEndpoint = $AzureStackInfo.TenantExternalEndpoints.TenantResourceManager
     $GraphAudience = "https://graph." + $AzureStackInfo.ExternalDomainFQDN + "/"
     $TenantID = $AzureStackInfo.AADTenantID

     # Register and set an AzureRM environment that targets your Azure Stack instance
     Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint $ArmEndpoint

     # Sign in using the new service principal identity
     $securePassword = $SpObject.ClientSecret | ConvertTo-SecureString -AsPlainText -Force
     $credential = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $SpObject.ClientId, $securePassword
     $SpSignin = Connect-AzureRmAccount -Environment "AzureStackUser" -ServicePrincipal -Credential $credential -TenantId $TenantID

     # Output the service principal details
     $SpObject
     ```

2. Po dokončení skriptu se zobrazí informace o registraci aplikace, včetně přihlašovacích údajů instančního objektu. Jak je uvedeno, `ClientID` a vygenerované `ClientSecret` se používají k přihlašování pod identitou instančního objektu. Po úspěšném přihlášení se identita instančního objektu použije pro další autorizaci a přístup k prostředkům, které spravuje Azure Resource Manager.

     ```shell  
     ApplicationIdentifier : S-1-5-21-1634563105-1224503876-2692824315-2623
     ClientId              : 8e0ffd12-26c8-4178-a74b-f26bd28db601
     Thumbprint            : 
     ApplicationName       : Azurestack-YourApp-6967581b-497e-4f5a-87b5-0c8d01a9f146
     ClientSecret          : 6RUWLRoBw3EebBLgaWGiowCkoko5_j_ujIPjA8dS
     PSComputerName        : azs-ercs01
     RunspaceId            : 286daaa1-c9a6-4176-a1a8-03f543f90998
     ```

Udržujte relaci konzoly PowerShellu otevřenou, jak ji použijete s hodnotou `ApplicationIdentifier` v další části.

### <a name="update-a-service-principals-client-secret"></a>Aktualizace tajného kódu klienta objektu služby

Aktualizujte pověření tajného klíče klienta pomocí prostředí PowerShell pomocí parametru **ResetClientSecret** , který okamžitě změní tajný klíč klienta. Následující zástupné symboly nahraďte vlastními hodnotami:

| Zástupný symbol | Popis | Příklad: |
| ----------- | ----------- | ------- |
| \<PepVM\> | Název virtuálního počítače privilegovaného koncového bodu na instanci Azure Stack. | "AzS-ERCS01" |
| \<AppIdentifier\> | Identifikátor přiřazený k registraci aplikace | "S-1-5-21-1634563105-1224503876-2692824315-2623" |

1. Pomocí relace Windows PowerShellu se zvýšenými oprávněními spusťte následující rutiny:

     ```powershell
     # Create a PSSession to the PrivilegedEndpoint VM
     $Session = New-PSSession -ComputerName "<PepVM>" -ConfigurationName PrivilegedEndpoint -Credential $Creds

     # Use the privileged endpoint to update the client secret, used by the service principal associated with <AppIdentifier>
     $SpObject = Invoke-Command -Session $Session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier "<AppIdentifier>" -ResetClientSecret}
     $Session | Remove-PSSession

     # Output the updated service principal details
     $SpObject
     ```

2. Po dokončení skriptu se zobrazí aktualizované informace o registraci aplikace, včetně nově generovaného tajného klíče klienta.

     ```shell  
     ApplicationIdentifier : S-1-5-21-1634563105-1224503876-2692824315-2623
     ClientId              : 8e0ffd12-26c8-4178-a74b-f26bd28db601
     Thumbprint            : 
     ApplicationName       : Azurestack-YourApp-6967581b-497e-4f5a-87b5-0c8d01a9f146
     ClientSecret          : MKUNzeL6PwmlhWdHB59c25WDDZlJ1A6IWzwgv_Kn
     PSComputerName        : azs-ercs01
     RunspaceId            : 6ed9f903-f1be-44e3-9fef-e7e0e3f48564
     ```

### <a name="remove-a-service-principal"></a>Odebrání instančního objektu

Teď se dozvíte, jak odebrat nebo odstranit registraci aplikace z adresáře a její přidružený objekt instančního objektu pomocí PowerShellu. 

Následující zástupné symboly nahraďte vlastními hodnotami:

| Zástupný symbol | Popis | Příklad: |
| ----------- | ----------- | ------- |
| \<PepVM\> | Název virtuálního počítače privilegovaného koncového bodu na instanci Azure Stack. | "AzS-ERCS01" |
| \<AppIdentifier\> | Identifikátor přiřazený k registraci aplikace | "S-1-5-21-1634563105-1224503876-2692824315-2623" |

```powershell  
# Sign in to PowerShell interactively, using credentials that have access to the VM running the Privileged Endpoint (typically <domain>\cloudadmin)
$Creds = Get-Credential

# Create a PSSession to the PrivilegedEndpoint VM
$Session = New-PSSession -ComputerName "<PepVM>" -ConfigurationName PrivilegedEndpoint -Credential $Creds

# OPTIONAL: Use the privileged endpoint to get a list of applications registered in AD FS
$AppList = Invoke-Command -Session $Session -ScriptBlock {Get-GraphApplication}

# Use the privileged endpoint to remove the application and associated service principal object for <AppIdentifier>
Invoke-Command -Session $Session -ScriptBlock {Remove-GraphApplication -ApplicationIdentifier "<AppIdentifier>"}
```

Z volání rutiny Remove-GraphApplication v privilegovaném koncovém bodě se nevrátí žádný výstup, ale během provádění rutiny uvidíte doslovné výstup potvrzení do konzoly:

```shell
VERBOSE: Deleting graph application with identifier S-1-5-21-1634563105-1224503876-2692824315-2623.
VERBOSE: Remove-GraphApplication : BEGIN on AZS-ADFS01 on ADFSGraphEndpoint
VERBOSE: Application with identifier S-1-5-21-1634563105-1224503876-2692824315-2623 was deleted.
VERBOSE: Remove-GraphApplication : END on AZS-ADFS01 under ADFSGraphEndpoint configuration
```

## <a name="assign-a-role"></a>Přiřazení role

Přístup k prostředkům Azure uživatelům a aplikacím je autorizovaný prostřednictvím Access Control na základě rolí (RBAC). Pokud chcete aplikaci dovolit přístup k prostředkům ve vašem předplatném pomocí instančního objektu, musíte instanční objekt *přiřadit* *roli* pro konkrétní *prostředek*. Nejdřív se rozhodněte, která role představuje správná *oprávnění* pro aplikaci. Další informace o dostupných rolích najdete v tématu [předdefinované role pro prostředky Azure](/azure/role-based-access-control/built-in-roles).

Typ prostředku, který zvolíte, taky vytvoří *obor přístupu* pro instanční objekt. Rozsah přístupu můžete nastavit na úrovni předplatného, skupiny prostředků nebo prostředku. Oprávnění jsou zděděna na nižší úrovně rozsahu. Například přidání aplikace do role čtenář pro skupinu prostředků znamená, že může číst skupinu prostředků a všechny prostředky, které obsahuje.

1. Přihlaste se k příslušnému portálu na základě adresáře, který jste zadali během Azure Stack instalace (například Azure Portal pro Azure AD nebo Azure Stack User Portal pro AD FS). V tomto příkladu se zobrazuje uživatel přihlášený k portálu Azure Stack User Portal.

   > [!NOTE]
   > Chcete-li přidat přiřazení rolí pro daný prostředek, musí váš uživatelský účet patřit do role, která deklaruje oprávnění `Microsoft.Authorization/roleAssignments/write`. Můžete například použít předdefinované role [vlastníka](/azure/role-based-access-control/built-in-roles#owner) nebo [Správce přístupu uživatele](/azure/role-based-access-control/built-in-roles#user-access-administrator) .  
2. Přejděte k prostředku, pro který chcete, aby instanční objekt povolil přístup. V tomto příkladu přiřaďte instanční objekt k roli v oboru předplatného tak, že vyberete **odběry**a pak konkrétní předplatné. Místo toho můžete vybrat skupinu prostředků nebo konkrétní prostředek, jako je třeba virtuální počítač.

     ![Výběr předplatného pro přiřazení](./media/azure-stack-create-service-principal/select-subscription.png)

3. Vyberte stránku **Access Control (IAM)** , která je univerzální napříč všemi prostředky, které podporují RBAC.
4. Vybrat **+ Přidat**
5. V části **role**vyberte roli, kterou chcete aplikaci přiřadit.
6. V části **Vybrat**vyhledejte aplikaci pomocí úplného nebo částečného názvu aplikace. Během registrace se název aplikace vygeneruje jako *Azurestack-\<soubor yourappname\>-\<ClientId\>* . Pokud jste například použili název aplikace *app2*a ClientID *2bbe67d8-3fdb-4b62-87cf-cc41dd4344ff* byl během vytváření přiřazen, bude mít úplný název *Azurestack-app2-2bbe67d8-3fdb-4b62-87cf-cc41dd4344ff*. Můžete vyhledat přesný řetězec nebo část, jako je například *Azurestack* nebo *Azurestack-app2*.
7. Jakmile aplikaci najde, vyberte ji a zobrazí se v části **Vybraní členové**.
8. Kliknutím na **Uložit** dokončete přiřazení role.

     [Přiřazení role ![](media/azure-stack-create-service-principal/assign-role.png)](media/azure-stack-create-service-principal/assign-role.png#lightbox)

9. Po dokončení se aplikace zobrazí v seznamu objektů zabezpečení přiřazených k aktuálnímu oboru pro danou roli.

     [Přiřazená role ![](media/azure-stack-create-service-principal/assigned-role.png)](media/azure-stack-create-service-principal/assigned-role.png#lightbox)

Teď, když jste vytvořili instanční objekt a přiřadili roli, můžete tento instanční objekt v rámci aplikace začít používat pro přístup k prostředkům Azure Stack.  

## <a name="next-steps"></a>Další kroky

[Přidání uživatelů pro AD FS](azure-stack-add-users-adfs.md)  
[Správa uživatelských oprávnění](azure-stack-manage-permissions.md)  
[Dokumentace k Azure Active Directory](https://docs.microsoft.com/azure/active-directory)  
[Active Directory Federation Services (AD FS)](https://docs.microsoft.com/windows-server/identity/active-directory-federation-services)
