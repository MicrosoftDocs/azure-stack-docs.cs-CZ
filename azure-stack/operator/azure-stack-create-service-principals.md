---
title: Pomocí identity aplikace pro přístup k prostředkům
description: Popisuje, jak spravovat hlavní název služby, který lze použít s řízením přístupu na základě rolí, přihlášení a přístup k prostředkům.
services: azure-resource-manager
documentationcenter: na
author: BryanLa
manager: femila
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/25/2019
ms.author: bryanla
ms.lastreviewed: 06/20/2019
ms.openlocfilehash: 8c27948185df5f98926a3500db0981a1ccddc321
ms.sourcegitcommit: c9d11be7d27c73797bdf279d4fcabb7a22451541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2019
ms.locfileid: "67397316"
---
# <a name="use-an-app-identity-to-access-resources"></a>Pomocí identity aplikace pro přístup k prostředkům

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit (ASDK)*

Aplikace, kterou je potřeba nasadit nebo konfiguraci prostředků prostřednictvím Azure Resource Manageru, musí být reprezentována instančního objektu. Stejně jako uživatel je reprezentována hlavní název uživatele, hlavního názvu služby je typ objektu zabezpečení, který reprezentuje aplikaci. Služby, které poskytuje identitu pro vaši aplikaci, umožňuje delegovat pouze potřebná oprávnění pro tento instanční objekt.  

Jako příklad můžete mít aplikace pro správu konfigurace, která používá Azure Resource Manageru k inventarizaci prostředků Azure. V tomto scénáři můžete vytvořit instanční objekt služby, přidělit tuto roli Čtenář takového objektu služby a omezit použití konfigurace správy na oprávnění jen pro čtení. 

## <a name="overview"></a>Přehled

Podobně jako u objektu zabezpečení uživatele se instančního objektu musí poskytnout pověření při ověřování, které se skládají ze dvou prvků:

- **ID aplikace**, která se někdy označují jako ID klienta. Toto je identifikátor GUID, který jednoznačně identifikuje registrace aplikace ve vašem tenantovi Active Directory.
- A **tajný kód** přidružené k ID aplikace. Můžete buď vygenerovat tajný řetězec klienta (podobně jako heslo) nebo zadejte x X509 certifikát, (ta používá svůj veřejný klíč). 

Spuštění aplikace s identitou služby instančního objektu je vhodnější než pod službou uživatel instančního objektu protože:

 - Instanční objekt služby x X509 pomocí certifikátu pro **silnější pověření**.  
 - Můžete přiřadit **víc omezující oprávnění** instančnímu objektu služby. Tato oprávnění jsou obvykle omezené jenom jaké aplikace potřebuje pro výkon, označované jako *principu nejnižších možných oprávnění*.
 - Instanční objekt služby **účtech a oprávněních neměnit tak často,** jako přihlašovací údaje uživatele. Při změně oprávnění pro uživatele, požadavky na heslo diktování změnu nebo uživatel odejde ze společnosti.

Začněte tím, že vytvoříte novou registraci aplikace ve vašem adresáři, který vytvoří přidružené [instanční objekt](/azure/active-directory/develop/developer-glossary#service-principal-object) reprezentuje identitu aplikace v adresáři. Tento dokument popisuje proces vytváření a správa instanční objekt, v závislosti na adresář, který jste zvolili pro vaši instanci služby Azure Stack:

- Azure Active Directory (Azure AD). Azure AD je více tenantů, cloudový adresář a služba pro správu identit. Můžete použít Azure AD s připojenou instanci služby Azure Stack.
- Active Directory Federation Services (AD FS). Služba AD FS poskytuje zjednodušenou zabezpečenou federaci identit a webových – možnosti jednotného přihlašování (SSO). Služba AD FS můžete použít s instancemi Azure Stack připojené a odpojené.

Nejprve se dozvíte, jak spravovat hlavní název služby, pak přiřazení instančního objektu k roli, omezení jejich přístupu k prostředkům.

## <a name="manage-an-azure-ad-service-principal"></a>Správa instančního objektu služby Azure AD 

Pokud jste nasadili Azure Stack se službou Azure Active Directory (Azure AD) jako služba pro správu identit, můžete vytvořit instanční objekty stejně jako pro Azure. Tato část ukazuje, jak k provádění kroků na webu Azure portal. Zkontrolujte, jestli máte [požadovaná oprávnění Azure AD](/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions) před zahájením.

### <a name="create-a-service-principal-that-uses-a-client-secret-credential"></a>Vytvoření instančního objektu, který používá tajného kódu přihlašovacích údajů klienta

V této části zaregistrujete svoji aplikaci pomocí webu Azure portal, která vytváří instanční objekt ve vašem tenantovi Azure AD. V tomto příkladu se vytvoří instanční objekt s tajného kódu přihlašovacích údajů klienta, ale portál také podporuje X509 přihlašovacích údajů na základě certifikátů.

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) pomocí svého účtu Azure.
2. Vyberte **Azure Active Directory** > **registrace aplikací** > **registrace nové**.
3. Zadejte **název** pro aplikaci. 
4. Vyberte příslušné **podporovaných typů účtu**.
5. V části **identifikátor URI pro přesměrování**vyberte **webové** jako typ aplikace a (volitelně) zadejte identifikátor URI přesměrování, pokud ho vaše aplikace vyžaduje. 
6. Po nastavení hodnot, vyberte **zaregistrovat**. Registrace aplikace se vytvoří a **přehled** stránky se zobrazí.
7. Kopírovat **ID aplikace** pro použití v kódu aplikace. Tato hodnota se také označuje jako ID klienta.
8. Chcete-li vygenerovat tajný kód klienta, vyberte **certifikáty a tajné kódy** stránky. Vyberte **Nový tajný klíč klienta**.
9. Zadejte **popis** pro tajný klíč a **vyprší platnost** doby trvání. 
10. Až budete hotovi, vyberte **přidat**.
11. Hodnota tajného kódu se zobrazí. Zkopírujte a uložte tuto hodnotu v jiném umístění, protože není možné ho později. Tajný kód je možné zadat pomocí ID aplikace v klientské aplikaci během službu objektu zabezpečení přihlášení. 

    ![uložený klíč](./media/azure-stack-create-service-principal/create-service-principal-in-azure-stack-secret.png)

## <a name="manage-an-ad-fs-service-principal"></a>Správa instančního objektu služby AD FS

Pokud jste nasadili Azure Stack s Active Directory Federation Services (AD FS) jako vaše služba identity management, musíte použít PowerShell ke správě instanční objekt služby. Příklady jsou uvedeny níže pro správu pověření instančního objektu, ukázka obou X509 certifikát a tajný kód klienta.

Musí být spuštěny skripty se zvýšenými oprávněními ("Spustit jako správce") prostředí PowerShell konzoly, která otevře jiná relace k virtuálnímu počítači, který je hostitelem privileged koncového bodu pro vaši instanci služby Azure Stack. Po vytvoření privilegovaných koncový bod relace spustí další rutiny a správa instančního objektu. Další informace o privileged koncový bod, najdete v části [pomocí privilegovaných koncového bodu ve službě Azure Stack](azure-stack-privileged-endpoint.md).

### <a name="create-a-service-principal-that-uses-a-certificate-credential"></a>Vytvoření instančního objektu, který používá certifikát přihlašovacích údajů

Při vytváření certifikátu pro přihlašovací údaje instančního objektu služby, musí být splněny následující požadavky:

 - Zprostředkovatele kryptografických služeb (CSP) musí být zprostředkovatel starší verze klíče.
 - Formát certifikátu musí být v souboru PFX jako veřejné a soukromé klíče jsou požadovány. Windows servery používají soubory PFX, které obsahují soubor veřejného klíče (soubor certifikátu SSL) a přidružený soubor privátního klíče.
 - Pro produkční prostředí musí certifikát vydat z interní certifikační autority nebo veřejné certifikační autority. Pokud používáte z veřejné certifikační autority, můžete oprávnění součástí základní image operačního systému v rámci aplikace Microsoft důvěryhodné kořenové autoritě. Můžete najít na seznam v [Microsoft Trusted Root Certificate Program: Účastníci](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca).
 - Infrastruktury Azure stacku musí mít přístup k síti do publikované v certifikátu umístění seznamu odvolaných certifikátů (CRL) certifikační autority. Tento seznam odvolaných certifikátů musí být koncový bod HTTP.

Jakmile budete mít certifikát, pomocí níže uvedeného skriptu Powershellu registrace vaší aplikace a vytvoření instančního objektu. Použijete také objekt služby pro přihlášení k Azure. Následující zástupné symboly nahraďte vlastními hodnotami:

| Zástupný symbol | Popis | Příklad: |
| ----------- | ----------- | ------- |
| \<PepVM\> | Název privilegovaného koncového bodu virtuálního počítače ve vaší instanci služby Azure Stack. | "AzS-ERCS01" |
| \<YourCertificateLocation\> | Umístění vaší X509 certifikát v místním úložišti certifikátů. | "Cert:\CurrentUser\My\AB5A8A3533CC7AA2025BF05120117E06DE407B34" |
| \<YourAppName\> | Popisný název registrace nové aplikace | "Tento nástroj pro správu" |

1. Otevřete relaci Windows Powershellu se zvýšenými oprávněními a spusťte následující skript:

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
    Set-AzureRmEnvironment -Name "AzureStackUser" -GraphAudience $GraphAudience -EnableAdfsAuthentication:$true

    # Sign in using the new service principal identity
    $SpSignin = Connect-AzureRmAccount -Environment "AzureStackUser" `
    -ServicePrincipal `
    -CertificateThumbprint $SpObject.Thumbprint `
    -ApplicationId $SpObject.ClientId `
    -TenantId $TenantID

    # Output the service principal details
    $SpObject

   ```
   
2. Po dokončení skriptu se zobrazí informace o registraci aplikace, včetně přihlašovacích údajů instančního objektu. Jak je uvedeno, `ClientID` a `Thumbprint` se používají k přihlášení podle identity objektu zabezpečení služby. Po úspěšném přihlášení se použije identita instančního objektu služby pro následné ověřování a přístup k prostředkům spravovaným pomocí Azure Resource Manageru. 

   ```shell
   ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
   ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
   Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
   ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
   ClientSecret          :
   PSComputerName        : azs-ercs01
   RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
   ```

Ponechat otevřené, relace konzoly prostředí PowerShell, jak použít je s `ApplicationIdentifier` hodnotu v další části.

### <a name="update-a-service-principals-certificate-credential"></a>Aktualizace přihlašovacích údajů certifikátu objektu služby

Teď vytvoříte instanční objekt služby, v této části se dozvíte, jak do:

1. Vytvořit nový podepsaný svým držitelem X509 certifikát pro účely testování.
2. Aktualizace přihlašovacích údajů instančního objektu, stačí aktualizovat jeho **kryptografický otisk** vlastnost tak, aby odpovídaly novým certifikátem.

Aktualizujte certifikát přihlašovacích údajů pomocí Powershellu, použijte vlastní hodnoty pro následující zástupné symboly:

| Zástupný symbol | Popis | Příklad: |
| ----------- | ----------- | ------- |
| \<PepVM\> | Název privilegovaného koncového bodu virtuálního počítače ve vaší instanci služby Azure Stack. | "AzS-ERCS01" |
| \<YourAppName\> | Popisný název registrace nové aplikace | "Tento nástroj pro správu" |
| \<YourCertificateLocation\> | Umístění vaší X509 certifikát v místním úložišti certifikátů. | "Cert:\CurrentUser\My\AB5A8A3533CC7AA2025BF05120117E06DE407B34" |
| \<AppIdentifier\> | Identifikátor přiřazený k registraci aplikace | "S-1-5-21-1512385356-3796245103-1243299919-1356" |

1. Pomocí relaci Windows Powershellu se zvýšenými oprávněními spusťte následující rutiny:

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

2. Po dokončení skriptu se zobrazí informace o registraci aktualizovanou aplikaci, včetně hodnot kryptografický otisk nového certifikátu podepsaného svým držitelem.

     ```Shell  
     ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
     ClientId              : 
     Thumbprint            : AF22EE716909041055A01FE6C6F5C5CDE78948E9
     ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
     ClientSecret          : 
     PSComputerName        : azs-ercs01
     RunspaceId            : a580f894-8f9b-40ee-aa10-77d4d142b4e5
     ```

### <a name="create-a-service-principal-that-uses-client-secret-credentials"></a>Vytvoření instančního objektu, který používá tajného kódu přihlašovacích údajů klienta

> [!IMPORTANT]
> Pomocí tajného klíče klienta je méně bezpečné než použití x X509 certifikát přihlašovacích údajů. Nejen je ověřovací mechanismus méně bezpečná, ale také běžně vyžaduje vložení tajného klíče do zdrojový kód klientské aplikace. V důsledku toho aplikacích v produkčním prostředí, se důrazně doporučujeme používat certifikát přihlašovacích údajů.

Teď vytvořte další registraci aplikace, ale tentokrát zadejte pověření tajného kódu klienta. Na rozdíl od certifikát přihlašovacích údajů adresář má schopnost generovat tajného kódu přihlašovacích údajů klienta. Takže místo určení tajný kód klienta, můžete použít `-GenerateClientSecret` přepínač tak, aby žádosti, že nebudou generována. Následující zástupné symboly nahraďte vlastními hodnotami:

| Zástupný symbol | Popis | Příklad: |
| ----------- | ----------- | ------- |
| \<PepVM\> | Název privilegovaného koncového bodu virtuálního počítače ve vaší instanci služby Azure Stack. | "AzS-ERCS01" |
| \<YourAppName\> | Popisný název registrace nové aplikace | "Tento nástroj pro správu" |

1. Otevřete relaci Windows Powershellu se zvýšenými oprávněními a spusťte následující rutiny:

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
     Set-AzureRmEnvironment -Name "AzureStackUser" -GraphAudience $GraphAudience -EnableAdfsAuthentication:$true

     # Sign in using the new service principal identity
     $securePassword = $SpObject.ClientSecret | ConvertTo-SecureString -AsPlainText -Force
     $credential = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $SpObject.ClientId, $securePassword
     $SpSignin = Connect-AzureRmAccount -Environment "AzureStackUser" -ServicePrincipal -Credential $credential -TenantId $TenantID

     # Output the service principal details
     $SpObject
     ```

2. Po dokončení skriptu se zobrazí informace o registraci aplikace, včetně přihlašovacích údajů instančního objektu. Jak je ukázáno, `ClientID` kalkulačce `ClientSecret` se používají k přihlášení podle identity objektu zabezpečení služby. Po úspěšném přihlášení se použije identita instančního objektu služby pro následné ověřování a přístup k prostředkům spravovaným pomocí Azure Resource Manageru.

     ```shell  
     ApplicationIdentifier : S-1-5-21-1634563105-1224503876-2692824315-2623
     ClientId              : 8e0ffd12-26c8-4178-a74b-f26bd28db601
     Thumbprint            : 
     ApplicationName       : Azurestack-YourApp-6967581b-497e-4f5a-87b5-0c8d01a9f146
     ClientSecret          : 6RUWLRoBw3EebBLgaWGiowCkoko5_j_ujIPjA8dS
     PSComputerName        : azs-ercs01
     RunspaceId            : 286daaa1-c9a6-4176-a1a8-03f543f90998
     ```

Ponechat otevřené, relace konzoly prostředí PowerShell, jak použít je s `ApplicationIdentifier` hodnotu v další části.

### <a name="update-a-service-principals-client-secret"></a>Aktualizovat tajný kód klienta objektu služby

Aktualizace tajného kódu pověření klienta pomocí Powershellu, pomocí **ResetClientSecret** parametr, který okamžitě změní tajný kód klienta. Následující zástupné symboly nahraďte vlastními hodnotami:

| Zástupný symbol | Popis | Příklad: |
| ----------- | ----------- | ------- |
| \<PepVM\> | Název privilegovaného koncového bodu virtuálního počítače ve vaší instanci služby Azure Stack. | "AzS-ERCS01" |
| \<AppIdentifier\> | Identifikátor přiřazený k registraci aplikace | "S-1-5-21-1634563105-1224503876-2692824315-2623" |

1. Pomocí relaci Windows Powershellu se zvýšenými oprávněními spusťte následující rutiny:

     ```powershell
     # Create a PSSession to the PrivilegedEndpoint VM
     $Session = New-PSSession -ComputerName "<PepVM>" -ConfigurationName PrivilegedEndpoint -Credential $Creds

     # Use the privileged endpoint to update the client secret, used by the service principal associated with <AppIdentifier>
     $SpObject = Invoke-Command -Session $Session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier "<AppIdentifier>" -ResetClientSecret}
     $Session | Remove-PSSession

     # Output the updated service principal details
     $SpObject
     ```

2. Po dokončení skriptu se zobrazí informace o registraci aktualizovanou aplikaci, včetně nově generovaného klienta tajný klíč.

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

Nyní uvidíte postup registrace aplikace v adresáři a jeho přidružené instanční objekt, odebrat nebo odstranit pomocí Powershellu. 

Následující zástupné symboly nahraďte vlastními hodnotami:

| Zástupný symbol | Popis | Příklad: |
| ----------- | ----------- | ------- |
| \<PepVM\> | Název privilegovaného koncového bodu virtuálního počítače ve vaší instanci služby Azure Stack. | "AzS-ERCS01" |
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

Nebude žádný výstup vrácená z volání rutiny Remove-GraphApplication u privilegovaných koncového bodu, ale verbatim potvrzení výstup do konzoly se zobrazí při spuštění rutiny:

```shell
VERBOSE: Deleting graph application with identifier S-1-5-21-1634563105-1224503876-2692824315-2623.
VERBOSE: Remove-GraphApplication : BEGIN on AZS-ADFS01 on ADFSGraphEndpoint
VERBOSE: Application with identifier S-1-5-21-1634563105-1224503876-2692824315-2623 was deleted.
VERBOSE: Remove-GraphApplication : END on AZS-ADFS01 under ADFSGraphEndpoint configuration
```

## <a name="assign-a-role"></a>Přiřazení role

Přístup k prostředkům Azure tak, že uživatelé a aplikace se oprávnění prostřednictvím řízení přístupu na základě Role (RBAC). Umožnit aplikaci přístup k prostředkům ve vašem předplatném pomocí jeho objektu služby, je nutné *přiřadit* instanční objekt k *role* pro konkrétní *prostředků*. Nejdřív se rozhodněte, jakou roli představuje oprávnění *oprávnění* pro aplikaci. Další informace o dostupných rolí, najdete v článku [předdefinované role pro prostředky Azure](/azure/role-based-access-control/built-in-roles).

Typ prostředku, zvolíte také vytvoří *obor přístupu* pro instanční objekt. Nastavení oboru přístupu na předplatné, skupinu prostředků nebo úrovni prostředků. Oprávnění se dědí do oboru na nižších úrovních. Například přidáním aplikace k roli "Čtenář" pro skupinu prostředků, znamená, že můžete přečíst, skupinu prostředků a všechny prostředky, které obsahuje.

1. Přihlaste se k portálu odpovídající založené na adresáři, který jste zadali během Azure Stack instalace (na webu Azure portal pro Azure AD, nebo portál user portal pro službu AD FS, například Azure Stack). V tomto příkladu jsme ukazují uživatele přihlášení k portálu user portal pro Azure Stack.

   > [!NOTE]
   > Přidání přiřazení role pro daný prostředek, váš uživatelský účet musí patřit do role, která deklaruje `Microsoft.Authorization/roleAssignments/write` oprávnění. Například buď [vlastníka](/azure/role-based-access-control/built-in-roles#owner) nebo [správce uživatelských přístupů](/azure/role-based-access-control/built-in-roles#user-access-administrator) předdefinované role.  
2. Přejděte k prostředku, který chcete povolit službu instančního objektu pro přístup. V tomto příkladu přiřadit instanční objekt služby k roli v oboru předplatného, tak, že vyberete **předplatná**, pak konkrétní předplatné. Místo toho můžete třeba vybrat skupinu prostředků nebo určitým prostředkem, například virtuální počítač. 

     ![Vyberte předplatné pro přiřazení](./media/azure-stack-create-service-principal/select-subscription.png)

3. Vyberte **řízení přístupu (IAM)** stránku, což je univerzální přes všechny prostředky, které podporují RBAC.
4. Vyberte **+ přidat**
5. V části **Role**, vyberte roli, kterou chcete přiřadit k aplikaci.
6. V části **vyberte**, vyhledejte svoji aplikaci pomocí celé nebo jeho část název aplikace. Během registrace, název aplikace je generován jako *Azurestack -\<YourAppName\>-\<ClientId\>* . Například, pokud jste použili aplikaci název *počítači App2*a ClientId *2bbe67d8-3fdb-4b62-87cf-cc41dd4344ff* byl přiřazen při vytváření, úplný název by měl být  *Azurestack-App2-2bbe67d8-3fdb-4b62-87cf-cc41dd4344ff*. Můžete vyhledat přesný řetězec nebo částečně, jako například *Azurestack* nebo *Azurestack počítači App2*.
7. Až aplikaci najdete, vyberte ho a zobrazí se v rámci **Vybraní členové**.
8. Vyberte **Uložit** k dokončení přiřazení role. 

     [ ![Přiřazení role](media/azure-stack-create-service-principal/assign-role.png)](media/azure-stack-create-service-principal/assign-role.png#lightbox)

9. Až budete hotovi, aplikace se zobrazí v seznamu objektů zabezpečení přiřazené pro aktuální obor, pro danou roli.

     [ ![Přiřazené role](media/azure-stack-create-service-principal/assigned-role.png)](media/azure-stack-create-service-principal/assigned-role.png#lightbox)

Teď, když máte vytvořený instanční objekt služby a přiřazenou roli, můžete začít používat to v rámci vaší aplikace pro přístup k prostředkům Azure Stack.  

## <a name="next-steps"></a>Další postup

[Přidání uživatelů pro AD FS](azure-stack-add-users-adfs.md)  
[Správa uživatelských oprávnění](azure-stack-manage-permissions.md)  
[Dokumentace k Azure Active Directory](https://docs.microsoft.com/azure/active-directory)  
[Active Directory Federation Services (AD FS)](https://docs.microsoft.com/windows-server/identity/active-directory-federation-services)
