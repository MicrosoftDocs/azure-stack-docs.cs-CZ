---
title: Integrace AD FS identity s vaším centrem Azure Stack hub | Microsoft Docs
description: Naučte se integrovat Azure Stack hub AD FS poskytovatele identity ke svému datovému centru AD FS.
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 05/10/2019
ms.author: patricka
ms.reviewer: thoroet
ms.lastreviewed: 05/10/2019
ms.openlocfilehash: 4d1ca3a04e838743983a7ed9d68fde5b1b189ff6
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75817376"
---
# <a name="integrate-ad-fs-identity-with-your-azure-stack-hub-datacenter"></a>Integrace AD FS identity s vaším datacenterm centra Azure Stack

Službu Azure Stack Hub můžete nasadit pomocí Azure Active Directory (Azure AD) nebo Active Directory Federation Services (AD FS) (AD FS) jako zprostředkovatele identity. Před nasazením centra Azure Stack je třeba provést výběr. V propojeném scénáři můžete zvolit Azure AD nebo AD FS. V případě odpojeného scénáře se podporuje pouze AD FS. V tomto článku se dozvíte, jak integrovat AD FS centra Azure Stack do AD FS datového centra.

> [!IMPORTANT]
> Zprostředkovatele identity nemůžete přepnout bez opětovného nasazení celého řešení centra Azure Stack.

## <a name="active-directory-federation-services-and-graph"></a>Active Directory Federation Services (AD FS) a graf

Nasazení pomocí AD FS umožňuje identitám v existující doménové struktuře služby Active Directory ověřování pomocí prostředků v centru Azure Stack. Tato existující doménová struktura služby Active Directory vyžaduje nasazení AD FS, aby bylo možné vytvořit AD FS federačního vztahu důvěryhodnosti.

Ověřování je jedna část identity. Pokud chcete spravovat řízení přístupu na základě role (RBAC) v centru Azure Stack, musí být nakonfigurovaná komponenta grafu. Když je delegovaný přístup k prostředku, komponenta grafu vyhledá uživatelský účet v existující doménové struktuře služby Active Directory pomocí protokolu LDAP.

![Architektura AD FS centra Azure Stack](media/azure-stack-integrate-identity/Azure-Stack-ADFS-architecture.png)

Stávající AD FS je služba tokenů zabezpečení (STS) účtu, která odesílá deklarace do centra Azure Stack AD FS (prostředek STS). V centru Azure Stack Automation vytvoří vztah důvěryhodnosti zprostředkovatele deklarací identity s koncovým bodem metadat pro existující AD FS.

U stávajících AD FS je nutné nakonfigurovat vztah důvěryhodnosti předávající strany. Tento krok není proveden automatizací a musí být nakonfigurován pomocí operátoru. Koncový bod VIP Azure Stack centra pro AD FS se dá vytvořit pomocí `https://adfs.<Region>.<ExternalFQDN>/`vzoru.

Konfigurace vztahu důvěryhodnosti předávající strany také vyžaduje, abyste nakonfigurovali pravidla transformace deklarace identity, která poskytuje Microsoft.

Pro konfiguraci grafu je nutné poskytnout účet služby, který má oprávnění ke čtení v existující službě Active Directory. Tento účet se vyžaduje jako vstup pro automatizaci k povolení scénářů RBAC.

Pro poslední krok je pro výchozí předplatné zprostředkovatele nakonfigurovaný nový vlastník. Tento účet má úplný přístup ke všem prostředkům, pokud se přihlásíte na portál pro správu centra Azure Stack.

Požadavky:

|Součást|Požadavek|
|---------|---------|
|Grafová databáze|Microsoft Active Directory 2012/2012 R2/2016|
|AD FS|Windows Server 2012/2012 R2/2016|

## <a name="setting-up-graph-integration"></a>Nastavení integrace grafu

Graf podporuje pouze integraci s jednou doménovou strukturou služby Active Directory. Pokud existuje více doménových struktur, bude k načtení uživatelů a skupin použita pouze doménová struktura zadaná v konfiguraci.

Jako vstupy pro parametry automatizace se vyžadují tyto informace:

|Parametr|Parametr listu nasazení|Popis|Příklad:|
|---------|---------|---------|---------|
|`CustomADGlobalCatalog`|AD FS plně kvalifikovaný název domény doménové struktury|Plně kvalifikovaný název domény cílové doménové struktury služby Active Directory, se kterou chcete integrovat|Contoso.com|
|`CustomADAdminCredentials`| |Uživatel s oprávněním ke čtení protokolu LDAP|YOURDOMAIN\graphservice|

### <a name="configure-active-directory-sites"></a>Konfigurace lokalit služby Active Directory

Pro nasazení služby Active Directory s více lokalitami nakonfigurujte nejbližší lokalitu služby Active Directory na nasazení centra Azure Stack. Konfigurace zabraňuje tomu, aby služba grafu Azure Stack hub přeložila dotazy pomocí serveru globálního katalogu ze vzdálené lokality.

Přidejte [síť veřejných virtuálních IP adres](azure-stack-network.md#public-vip-network) centra Azure Stack k lokalitě služby Active Directory nejbližší k centru Azure Stack. Řekněme například, že vaše služba Active Directory má dvě lokality: Seattle a Redmond. Pokud je v lokalitě Praha nasazený Azure Stack hub, přidáte do lokality služby Active Directory pro Seattle síť veřejnou virtuální IP adresu centra Azure Stack.

Další informace o lokalitách služby Active Directory najdete v tématu [navrhování topologie lokality](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology).

> [!Note]  
> Pokud se vaše služba Active Directory skládá z jedné lokality, můžete tento krok přeskočit. Pokud máte nakonfigurované nastavení catch-All Subnet, ověřte, že není součástí podsítě sítě veřejných VIP centra Azure Stack.

### <a name="create-user-account-in-the-existing-active-directory-optional"></a>Vytvoření uživatelského účtu v existující službě Active Directory (volitelné)

Volitelně můžete vytvořit účet pro službu Graph Service v existující službě Active Directory. Tento krok proveďte, pokud ještě nemáte účet, který chcete použít.

1. V existující službě Active Directory vytvořte následující uživatelský účet (doporučení):
   - **Uživatelské jméno**: graphservice
   - **Heslo**: použijte silné heslo a nakonfigurujte heslo tak, aby nikdy nevypršela platnost.

   Nevyžadují se žádná zvláštní oprávnění ani členství.

#### <a name="trigger-automation-to-configure-graph"></a>Spuštění automatizace pro konfiguraci grafu

Pro tento postup použijte počítač v síti datového centra, který může komunikovat s privilegovaným koncovým bodem v centru Azure Stack.

1. Otevřete relaci Windows PowerShellu se zvýšenými oprávněními (Spustit jako správce) a připojte se k IP adrese privilegovaného koncového bodu. K ověření použijte přihlašovací údaje pro **CloudAdmin** .

   ```powershell  
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Teď, když jste připojeni k privilegovanému koncovému bodu, spusťte následující příkaz: 

   ```powershell  
   Register-DirectoryService -CustomADGlobalCatalog contoso.com
   ```

   Po zobrazení výzvy zadejte pověření pro uživatelský účet, který chcete použít pro službu Graph Service (například graphservice). Vstup pro rutinu Register-DirectoryService musí být název doménové struktury nebo kořenová doména v doménové struktuře, nikoli žádná jiná doména v doménové struktuře.

   > [!IMPORTANT]
   > Počkejte, než se automaticky otevírané okno s přihlašovacími údaji (Get-Credential v privilegovaném koncovém bodu nepodporuje) a zadejte přihlašovací údaje účtu služby Graph.

3. Rutina **Register-DirectoryService** má volitelné parametry, které můžete použít v určitých situacích, kdy se nepovede existující ověření služby Active Directory. Po spuštění této rutiny ověří, že zadaná doména je kořenovou doménou, může být dostupný server globálního katalogu a že má zadaný účet udělený přístup pro čtení.

   |Parametr|Popis|
   |---------|---------|
   |`-SkipRootDomainValidation`|Určuje, že se místo Doporučené kořenové domény musí použít podřízená doména.|
   |`-Force`|Obchází všechny kontroly ověřování.|

#### <a name="graph-protocols-and-ports"></a>Protokoly a porty grafu

Služba Graph Service v centru Azure Stack používá následující protokoly a porty ke komunikaci s zapisovatelým serverem globálního katalogu (GC) a služba KDC (Key Distribution Center) (KDC), který může zpracovávat požadavky na přihlášení v cílové doménové struktuře služby Active Directory.

Služba Graph Service v centru Azure Stack používá ke komunikaci s cílovou službou Active Directory následující protokoly a porty:

|Typ|Port|Protocol (Protokol)|
|---------|---------|---------|
|LDAP|389|TCP & UDP|
|LDAP SSL|636|TCP|
|GC PROTOKOLU LDAP|3268|TCP|
|LDAP GC SSL|3269|TCP|

## <a name="setting-up-ad-fs-integration-by-downloading-federation-metadata"></a>Nastavení integrace AD FS stažením federačních metadat

Pro parametry automatizace se jako vstup vyžadují tyto informace:

|Parametr|Parametr listu nasazení|Popis|Příklad:|
|---------|---------|---------|---------|
|CustomAdfsName|Název poskytovatele AD FS|Název zprostředkovatele deklarací identity.<br>Toto zobrazení se zobrazí na AD FS cílové stránce.|Contoso|
|CustomAD<br>FSFederationMetadataEndpointUri|Identifikátor URI AD FS metadat|Odkaz federačních metadat| https:\//ad01.contoso.com/federationmetadata/2007-06/federationmetadata.xml |
|SigningCertificateRevocationCheck|není k dispozici|Volitelný parametr pro přeskočení kontroly CRL|Žádné|


### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack-hub"></a>Aktivace automatizace pro konfiguraci vztahu důvěryhodnosti zprostředkovatele deklarací v centru Azure Stack

Pro tento postup použijte počítač, který může komunikovat s privilegovaným koncovým bodem v centru Azure Stack. Očekávalo se, že certifikát používaný účtem **STS AD FS** důvěřuje Azure Stackm centru.

1. Otevřete relaci Windows PowerShellu se zvýšenými oprávněními a připojte se k privilegovanému koncovému bodu.

   ```powershell  
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Teď, když jste připojeni k privilegovanému koncovému bodu, spusťte následující příkaz pomocí parametrů odpovídajících vašemu prostředí:

   ```powershell  
   Register-CustomAdfs -CustomAdfsName Contoso -CustomADFSFederationMetadataEndpointUri https://win-SQOOJN70SGL.contoso.com/federationmetadata/2007-06/federationmetadata.xml
   ```

3. Spuštěním následujícího příkazu aktualizujte vlastníka výchozího předplatného poskytovatele pomocí parametrů, které jsou vhodné pro vaše prostředí:

   ```powershell  
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

## <a name="setting-up-ad-fs-integration-by-providing-federation-metadata-file"></a>Nastavení integrace AD FS poskytnutím souboru federačních metadat

Počínaje verzí 1807 použijte tuto metodu, pokud jsou splněné některé z následujících podmínek:

- Řetěz certifikátů se liší od AD FS ve srovnání se všemi ostatními koncovými body v centru Azure Stack.
- Neexistuje žádné síťové připojení ke stávajícímu AD FS serveru z instance AD FS centra Azure Stack.

Pro parametry automatizace se jako vstup vyžadují tyto informace:


|Parametr|Popis|Příklad:|
|---------|---------|---------|
|CustomAdfsName|Název zprostředkovatele deklarací identity. Toto zobrazení se zobrazí na AD FS cílové stránce.|Contoso|
|CustomADFSFederationMetadataFileContent|Obsah metadat|$using:federationMetadataFileContent|

### <a name="create-federation-metadata-file"></a>Vytvořit soubor federačních metadat

Pro následující postup musíte použít počítač, který má síťové připojení ke stávajícímu AD FS nasazení, které se staly účtem STS. Musí být nainstalované taky potřebné certifikáty.

1. Otevřete relaci Windows PowerShellu se zvýšenými oprávněními a spusťte následující příkaz s použitím parametrů odpovídajících vašemu prostředí:

   ```powershell  
    $url = "https://win-SQOOJN70SGL.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml"
    $webclient = New-Object System.Net.WebClient
    $webclient.Encoding = [System.Text.Encoding]::UTF8
    $metadataAsString = $webclient.DownloadString($url)
    Set-Content -Path c:\metadata.xml -Encoding UTF8 -Value $metadataAsString
   ```

2. Zkopírujte soubor metadat do počítače, který může komunikovat s privilegovaným koncovým bodem.

### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack-hub"></a>Aktivace automatizace pro konfiguraci vztahu důvěryhodnosti zprostředkovatele deklarací v centru Azure Stack

Pro tento postup použijte počítač, který může komunikovat s privilegovaným koncovým bodem v centru Azure Stack a má přístup k souboru metadat, který jste vytvořili v předchozím kroku.

1. Otevřete relaci Windows PowerShellu se zvýšenými oprávněními a připojte se k privilegovanému koncovému bodu.

   ```powershell  
   $federationMetadataFileContent = get-content c:\metadata.xml
   $creds=Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Teď, když jste připojeni k privilegovanému koncovému bodu, spusťte následující příkaz pomocí parametrů odpovídajících vašemu prostředí:

    ```powershell
    Register-CustomAdfs -CustomAdfsName Contoso -CustomADFSFederationMetadataFileContent $using:federationMetadataFileContent
    ```

3. Spuštěním následujícího příkazu aktualizujte vlastníka výchozího předplatného poskytovatele. Použijte parametry vhodné pro vaše prostředí.

   ```powershell  
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

   > [!Note]  
   > Když otočíte certifikát na existující AD FS (účet STS), musíte nastavit integraci AD FS znovu. Integraci je nutné nastavit i v případě, že je koncový bod metadat dostupný nebo byl nakonfigurován tak, že poskytuje soubor metadat.

## <a name="configure-relying-party-on-existing-ad-fs-deployment-account-sts"></a>Nakonfigurovat předávající stranu pro existující nasazení AD FS (účet STS)

Společnost Microsoft poskytuje skript, který konfiguruje vztah důvěryhodnosti předávající strany, včetně pravidel transformace deklarace identity. Použití skriptu je volitelné, protože příkazy můžete spustit ručně.

Pomocný skript si můžete stáhnout z [Azure Stack nástrojů centra](https://github.com/Azure/AzureStack-Tools/tree/vnext/DatacenterIntegration/Identity) na GitHubu.

Pokud se rozhodnete tyto příkazy spustit ručně, postupujte následovně:

1. Zkopírujte následující obsah do souboru. txt (například uložený jako c:\ClaimRules.txt) do instance AD FS svého datového centra nebo člena farmy:

   ```text
   @RuleTemplate = "LdapClaims"
   @RuleName = "Name claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"), query = ";userPrincipalName;{0}", param = c.Value);

   @RuleTemplate = "LdapClaims"
   @RuleName = "UPN claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"), query = ";userPrincipalName;{0}", param = c.Value);

   @RuleTemplate = "LdapClaims"
   @RuleName = "ObjectID claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid"]
   => issue(Type = "http://schemas.microsoft.com/identity/claims/objectidentifier", Issuer = c.Issuer, OriginalIssuer = c.OriginalIssuer, Value = c.Value, ValueType = c.ValueType);

   @RuleName = "Family Name and Given claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname", "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname"), query = ";sn,givenName;{0}", param = c.Value);

   @RuleTemplate = "PassThroughClaims"
   @RuleName = "Pass through all Group SID claims"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
   => issue(claim = c);

   @RuleTemplate = "PassThroughClaims"
   @RuleName = "Pass through all windows account name claims"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
   => issue(claim = c);
   ```

2. Ověřte, jestli je povolené ověřování na základě model Windows Forms pro extranet a intranet. Spuštěním následující rutiny můžete ověřit, jestli už je povolená:

   ```powershell  
   Get-AdfsAuthenticationProvider | where-object { $_.name -eq "FormsAuthentication" } | select Name, AllowedForPrimaryExtranet, AllowedForPrimaryIntranet
   ```

    > [!Note]  
    > Pro vaše nasazení AD FS můžou být zastaralá podporovaná řetězce uživatelského agenta Windows Integrated Authentication (WIA) a můžou vyžadovat aktualizaci pro podporu nejnovějších klientů. Další informace o aktualizaci řetězců uživatelského agenta podporovaného zařízením WIA najdete v článku [Konfigurace ověřování na základě intranetových formulářů pro zařízení, která nepodporují WIA](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-intranet-forms-based-authentication-for-devices-that-do-not-support-wia).<br><br>Postup pro povolení zásad ověřování na základě formulářů najdete v tématu [Konfigurace zásad ověřování](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-authentication-policies).

3. Chcete-li přidat vztah důvěryhodnosti předávající strany, spusťte následující příkaz prostředí Windows PowerShell na instanci AD FS nebo členu farmy. Nezapomeňte aktualizovat koncový bod AD FS a Ukázat na soubor vytvořený v kroku 1.

   **Pro AD FS 2016**

   ```powershell  
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true -AccessControlPolicyName "Permit everyone" -TokenLifeTime 1440
   ```

   **Pro AD FS 2012/2012 R2**

   ```powershell  
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true -TokenLifeTime 1440
   ```

   > [!IMPORTANT]  
   > K nakonfigurování autorizačních pravidel vystavování při použití Windows Serveru 2012 nebo 2012 R2 AD FS je nutné použít modul snap-in AD FS MMC.

4. Pokud k přístupu k centru Azure Stack používáte Internet Explorer nebo prohlížeč Microsoft Edge, je nutné ignorovat vazby tokenů. V opačném případě se pokusy o přihlášení nezdařily. Na AD FS instanci nebo členu farmy spusťte následující příkaz:

   > [!note]  
   > Tento krok se nedá použít, pokud používáte Windows Server 2012 nebo 2012 R2 AD FS. V takovém případě je bezpečné tento příkaz přeskočit a pokračovat v integraci.

   ```powershell  
   Set-AdfsProperties -IgnoreTokenBinding $true
   ```

## <a name="spn-creation"></a>Vytváření SPN

Existuje mnoho scénářů, které vyžadují použití hlavního názvu služby (SPN) pro ověřování. Následuje několik příkladů:

- Použití rozhraní příkazového řádku s AD FS nasazením centra Azure Stack
- Sada Management Pack nástroje System Center pro Azure Stack hub při nasazení s AD FS
- Poskytovatelé prostředků v Azure Stack hub při nasazení s AD FS
- Různé aplikace.
- Vyžadujete neinteraktivní přihlašování.

> [!Important]  
> AD FS podporuje pouze interaktivní přihlašovací relace. Pokud vyžadujete neinteraktivní přihlášení k automatizovanému scénáři, je nutné použít hlavní název služby (SPN).

Další informace o vytváření hlavního názvu služby (SPN) najdete v tématu [Vytvoření instančního objektu pro AD FS](azure-stack-create-service-principals.md).


## <a name="troubleshooting"></a>Řešení potíží

### <a name="configuration-rollback"></a>Vrácení změn konfigurace

Pokud dojde k chybě, která opustí prostředí ve stavu, ve kterém již nelze ověřit, je k dispozici možnost vrácení zpět.

1. Otevřete relaci Windows PowerShellu se zvýšenými oprávněními a spusťte následující příkazy:

   ```powershell  
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Pak spusťte následující rutinu:

   ```powershell  
   Reset-DatacenterIntegrationConfiguration
   ```

   Po spuštění akce vrácení zpět se všechny změny konfigurace vrátí zpět. Je možné pouze ověřování pomocí předdefinovaného **CloudAdmin** uživatele.

   > [!IMPORTANT]
   > Musíte nakonfigurovat původního vlastníka výchozího předplatného poskytovatele.

   ```powershell  
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "azurestackadmin@[Internal Domain]"
   ```

### <a name="collecting-additional-logs"></a>Shromažďování dalších protokolů

Pokud selže kterákoli z rutin, můžete shromažďovat další protokoly pomocí rutiny `Get-Azurestacklogs`.

1. Otevřete relaci Windows PowerShellu se zvýšenými oprávněními a spusťte následující příkazy:

   ```powershell  
   $creds = Get-Credential
   Enter-pssession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Pak spusťte následující rutinu:

   ```powershell  
   Get-AzureStackLog -OutputPath \\myworkstation\AzureStackLogs -FilterByRole ECE
   ```

## <a name="next-steps"></a>Další kroky

[Integrace externích řešení monitorování](azure-stack-integrate-monitor.md)
