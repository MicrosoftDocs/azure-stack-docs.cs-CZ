---
title: Správa instančního objektu pro Azure Stack | Dokumentace Microsoftu
description: Popisuje, jak spravovat nový instanční objekt, který lze použít s řízením přístupu na základě role v Azure Resource Manageru pro správu přístupu k prostředkům.
services: azure-resource-manager
documentationcenter: na
author: sethmanheim
manager: femila
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/18/2018
ms.author: sethm
ms.lastreviewed: 12/18/2018
ms.openlocfilehash: 8d6548ada50a25350f622d7bc7460005f4abc401
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "64295123"
---
# <a name="provide-applications-access-to-azure-stack"></a>Poskytnutí přístupu aplikací do Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Když aplikace potřebuje přístup k nasazení nebo konfiguraci prostředků prostřednictvím Azure Resource Manageru ve službě Azure Stack, vytvoření instančního objektu, což je přihlašovacích údajů pro vaši aplikaci. Potom můžete delegovat pouze potřebná oprávnění pro tento instanční objekt.  

Například můžete mít nástroj pro správu konfigurace, která používá Azure Resource Manageru k inventarizaci prostředků Azure. V tomto scénáři můžete vytvořit instanční objekt služby, přidělit tuto roli Čtenář takového objektu služby a omezit nástroje pro správu konfigurace pro přístup jen pro čtení. 

Instanční objekty jsou upřednostňovány vůči spuštění aplikace pod svými přihlašovacími údaji, protože:

 - Můžete přiřadit oprávnění pro instanční objekt, který se liší od účtu oprávnění. Tato oprávnění jsou obvykle omezená přesně na to, co aplikace potřebuje dělat.
 - Není potřeba změnit přihlašovací údaje aplikace, pokud se změní vaše odpovědnosti.
 - Certifikát můžete použít k automatizaci ověřování při provádění bezobslužného skriptu.  

## <a name="getting-started"></a>Začínáme

V závislosti na tom, jak nasadíte Azure Stack začnete tím, že vytváření instančního objektu. Tento dokument popisuje vytváření instančního objektu pro:

- Azure Active Directory (Azure AD). Azure AD je více tenantů, cloudový adresář a služba pro správu identit. Azure AD můžete pomocí připojené služby Azure Stack.
- Active Directory Federation Services (AD FS). Služba AD FS poskytuje zjednodušenou zabezpečenou federaci identit a webových – možnosti jednotného přihlašování (SSO). Služba AD FS můžete použít s instancemi Azure Stack připojené a odpojené.

Jakmile vytvoříte instanční objekt služby, se používají sady kroků běžné služby AD FS a Azure Active Directory delegovat oprávnění na roli.

## <a name="manage-service-principal-for-azure-ad"></a>Správa instanční objekt služby pro službu Azure AD

Pokud jste nasadili Azure Stack se službou Azure Active Directory (Azure AD) jako služba pro správu identit, můžete vytvořit instanční objekty stejně jako pro Azure. Tato část ukazuje, jak k provedení kroků na portálu. Zkontrolujte, jestli máte [vyžaduje Azure AD permissions]((/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions) před zahájením.

### <a name="create-service-principal"></a>Vytvoření instančního objektu

V této části vytvoříte aplikaci (instanční objekt) ve službě Azure AD, která reprezentuje vaši aplikaci.

1. Přihlaste se ke svému účtu Azure prostřednictvím [webu Azure portal](https://portal.azure.com).
2. Vyberte **Azure Active Directory** > **registrace aplikací** > **registrace nové aplikace**
3. Zadejte název a URL aplikace. Vyberte buď **webovou aplikaci nebo API** nebo **nativní** pro typ aplikace, kterou chcete vytvořit. Po nastavení hodnot, vyberte **vytvořit**.

Vytvoříte instanční objekt služby pro vaši aplikaci.

### <a name="get-credentials"></a>Získat přihlašovací údaje

Při programovém přihlášení pomocí ID je pro vaši aplikaci a webové aplikace a rozhraní API, ověřovací klíč. K získání těchto hodnot použijte následující postup:

1. Z **registrace aplikací** ve službě Active Directory, vyberte svou aplikaci.

2. Zkopírujte **ID aplikace** a uložte ho v kódu aplikace. Aplikace v sekci ukázkové aplikace se tato hodnota označuje jako ID klienta.

     ![ID klienta](./media/azure-stack-create-service-principal/image12.png)
3. Chcete-li generovat ověřovací klíč pro webovou aplikaci / rozhraní API, vyberte **nastavení** > **klíče**. 

4. Zadejte popis klíče a jeho dobu platnosti. Až budete hotovi, vyberte **Uložit**.

Jakmile klíč uložíte, zobrazí se jeho hodnota. Zkopírujte tuto hodnotu do poznámkového bloku nebo jiného dočasného umístění, protože klíč nelze načíst později. Hodnotu klíče uveďte s ID aplikace, aby přihlásit jako aplikace. Hodnota klíče Store na místě, kde aplikace může načíst ji.

![uložený klíč](./media/azure-stack-create-service-principal/image15.png)

Jakmile budete hotovi, můžete aplikace přiřadit roli.

## <a name="manage-service-principal-for-ad-fs"></a>Správa instančního objektu služby AD FS

Pokud jste nasadili Azure Stack s Active Directory Federation Services (AD FS) jako služba pro správu identit, pomocí prostředí PowerShell k vytvoření instančního objektu, přiřazení role pro přístup a přihlaste se pomocí tuto identitu.

Vytvoření instančního objektu služby se službou AD FS můžete použít jednu ze dvou metod. Můžete:
 - [Vytvoření instančního objektu pomocí certifikátu](azure-stack-create-service-principals.md#create-a-service-principal-using-a-certificate)
 - [Vytvoření instančního objektu pomocí tajného klíče klienta](azure-stack-create-service-principals.md#create-a-service-principal-using-a-client-secret)

Úlohy pro správu služby AD FS instanční.

| Type | Akce |
| --- | --- |
| Certifikát služby AD FS | [Vytvoření](azure-stack-create-service-principals.md#create-a-service-principal-using-a-certificate) |
| Certifikát služby AD FS | [Aktualizace](azure-stack-create-service-principals.md#update-certificate-for-service-principal-for-ad-fs) |
| Certifikát služby AD FS | [odebrat](azure-stack-create-service-principals.md#remove-a-service-principal-for-ad-fs) |
| Tajný klíč klienta služby FS AD | [Vytvoření](azure-stack-create-service-principals.md#create-a-service-principal-using-a-client-secret) |
| Tajný klíč klienta služby FS AD | [Aktualizace](azure-stack-create-service-principals.md#create-a-service-principal-using-a-client-secret) |
| Tajný klíč klienta služby FS AD | [odebrat](azure-stack-create-service-principals.md#remove-a-service-principal-for-ad-fs) |

### <a name="create-a-service-principal-using-a-certificate"></a>Vytvoření instančního objektu pomocí certifikátu

Při vytváření instančního objektu při použití služby AD FS pro identitu, můžete použít certifikát.

#### <a name="certificate"></a>Certifikát

Certifikát je povinný.

**Požadavky na certifikát**

 - Zprostředkovatele kryptografických služeb (CSP) musí být zprostředkovatel starší verze klíče.
 - Formát certifikátu musí být v souboru PFX jako veřejné a soukromé klíče jsou požadovány. Windows servery používají soubory PFX, které obsahují soubor veřejného klíče (soubor certifikátu SSL) a přidružený soubor privátního klíče.
 - Pro produkční prostředí musí certifikát vydat z interní certifikační autority nebo veřejné certifikační autority. Pokud používáte z veřejné certifikační autority, můžete oprávnění součástí základní image operačního systému v rámci aplikace Microsoft důvěryhodné kořenové autoritě. Můžete najít na seznam v [Microsoft Trusted Root Certificate Program: Účastníci](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca).
 - Infrastruktury Azure stacku musí mít přístup k síti do publikované v certifikátu umístění seznamu odvolaných certifikátů (CRL) certifikační autority. Tento seznam odvolaných certifikátů musí být koncový bod HTTP.

#### <a name="parameters"></a>Parametry

Tyto informace se vyžaduje jako vstup pro automatizaci parametry:

|Parametr|Popis|Příklad:|
|---------|---------|---------|
|Název|Název pro účet hlavní název služby|MyAPP|
|ClientCertificates|Pole objektů certifikátu|X509 certifikátu|
|ClientRedirectUris<br>(Volitelné)|Identifikátor URI přesměrování aplikace|-|

#### <a name="use-powershell-to-create-a-service-principal"></a>Použití Powershellu k vytvoření instančního objektu

1. Otevřete relaci Windows Powershellu se zvýšenými oprávněními a spusťte následující rutiny:

   ```powershell  
    # Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
    $Creds = Get-Credential

    # Creating a PSSession to the ERCS PrivilegedEndpoint
    $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

    # If you have a managed certificate use the Get-Item command to retrieve your certificate from your certificate location.
    # If you don't want to use a managed certificate, you can produce a self signed cert for testing purposes: 
    # $Cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange
    $Cert = Get-Item "<YourCertificateLocation>"
    
    $ServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {New-GraphApplication -Name '<YourAppName>' -ClientCertificates $using:cert}
    $AzureStackInfo = Invoke-Command -Session $Session -ScriptBlock {Get-AzureStackStampInformation}
    $Session | Remove-PSSession

    # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. This is read from the AzureStackStampInformation output of the ERCS VM.
    $ArmEndpoint = $AzureStackInfo.TenantExternalEndpoints.TenantResourceManager

    # For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. This is read from the AzureStackStampInformation output of the ERCS VM.
    $GraphAudience = "https://graph." + $AzureStackInfo.ExternalDomainFQDN + "/"

    # TenantID for the stamp. This is read from the AzureStackStampInformation output of the ERCS VM.
    $TenantID = $AzureStackInfo.AADTenantID

    # Register an AzureRM environment that targets your Azure Stack instance
    Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

    # Set the GraphEndpointResourceId value
    Set-AzureRmEnvironment `
    -Name "AzureStackUser" `
    -GraphAudience $GraphAudience `
    -EnableAdfsAuthentication:$true

    Add-AzureRmAccount -EnvironmentName "AzureStackUser" `
    -ServicePrincipal `
    -CertificateThumbprint $ServicePrincipal.Thumbprint `
    -ApplicationId $ServicePrincipal.ClientId `
    -TenantId $TenantID

    # Output the SPN details
    $ServicePrincipal

   ```
   > [!Note]  
   > Pro účely ověření certifikátu podepsaného svým držitelem můžete vytvořit pomocí následujícím příkladu:

   ```powershell  
   $Cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<yourappname>" -KeySpec KeyExchange
   ```


2. Po dokončení automatizace, zobrazí požadované podrobnosti, které chcete použít hlavní název služby. Doporučujeme uložit pro pozdější použití.

   Příklad:

   ```shell
   ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
   ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
   Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
   ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
   PSComputerName        : azs-ercs01
   RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
   ```

### <a name="update-certificate-for-service-principal-for-ad-fs"></a>Aktualizovat certifikát pro instanční objekt služby AD FS

Pokud jste nasadili Azure Stack se službou AD FS, můžete použít PowerShell aktualizovat tajný klíč pro objekt služby.

Je skript spuštěn na virtuálním počítači ERCS z privileged koncového bodu.

#### <a name="parameters"></a>Parametry

Tyto informace se vyžaduje jako vstup pro automatizaci parametry:

|Parametr|Popis|Příklad:|
|---------|---------|---------|
|Název|Název pro účet hlavní název služby|MyAPP|
|ApplicationIdentifier|Jedinečný identifikátor|S-1-5-21-1634563105-1224503876-2692824315-2119|
|ClientCertificate|Pole objektů certifikátu|X509 certifikátu|

#### <a name="example-of-updating-service-principal-for-ad-fs"></a>Příklad aktualizuje instanční objekt služby pro službu AD FS

Tento příklad vytvoří certifikát podepsaný svým držitelem. Při spuštění rutiny v produkčním nasazení použijte [Get-Item](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Management/Get-Item) načíst objekt certifikátu pro certifikát, který chcete použít.

1. Otevřete relaci Windows Powershellu se zvýšenými oprávněními a spusťte následující rutiny:

     ```powershell
          # Creating a PSSession to the ERCS PrivilegedEndpoint
          $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

          # This produces a self signed cert for testing purposes. It is preferred to use a managed certificate for this.
          $NewCert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange

          $RemoveServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier  S-1-5-21-1634563105-1224503876-2692824315-2120 -ClientCertificates $NewCert}

          $Session | Remove-PSSession
     ```

2. Po dokončení automatizace, zobrazí aktualizovaný kryptografický otisk hodnota povolená pro ověření hlavního názvu služby.

     ```Shell  
          ClientId              : 
          Thumbprint            : AF22EE716909041055A01FE6C6F5C5CDE78948E9
          ApplicationName       : Azurestack-ThomasAPP-3e5dc4d2-d286-481c-89ba-57aa290a4818
          ClientSecret          : 
          RunspaceId            : a580f894-8f9b-40ee-aa10-77d4d142b4e5
     ```

### <a name="create-a-service-principal-using-a-client-secret"></a>Vytvoření instančního objektu pomocí tajného klíče klienta

Při vytváření instančního objektu při použití služby AD FS pro identitu, můžete použít certifikát. Privilegované koncový bod bude používat ke spouštění rutin.

Tyto skripty se spouštějí z privileged koncového bodu na virtuálním počítači ERCS. Další informace o privileged koncový bod, najdete v části [pomocí privilegovaných koncového bodu ve službě Azure Stack](azure-stack-privileged-endpoint.md).

#### <a name="parameters"></a>Parametry

Tyto informace se vyžaduje jako vstup pro automatizaci parametry:

| Parametr | Popis | Příklad: |
|----------------------|--------------------------|---------|
| Název | Název pro účet hlavní název služby | MyAPP |
| GenerateClientSecret | Vytvoření tajného kódu |  |

#### <a name="use-the-ercs-privilegedendpoint-to-create-the-service-principal"></a>Použít ERCS PrivilegedEndpoint k vytvoření instančního objektu služby

1. Otevřete relaci Windows Powershellu se zvýšenými oprávněními a spusťte následující rutiny:

     ```powershell  
      # Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
     $Creds = Get-Credential

     # Creating a PSSession to the ERCS PrivilegedEndpoint
     $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

     # Creating a SPN with a secre
     $ServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {New-GraphApplication -Name '<YourAppName>' -GenerateClientSecret}
     $AzureStackInfo = Invoke-Command -Session $Session -ScriptBlock {Get-AzureStackStampInformation}
     $Session | Remove-PSSession

     # Output the SPN details
     $ServicePrincipal
     ```

2. Po spuštění rutiny prostředí zobrazí požadované podrobnosti, které chcete použít hlavní název služby. Ujistěte se, že uložíte tajný klíč klienta.

     ```powershell  
     ApplicationIdentifier : S-1-5-21-1634563105-1224503876-2692824315-2623
     ClientId              : 8e0ffd12-26c8-4178-a74b-f26bd28db601
     Thumbprint            : 
     ApplicationName       : Azurestack-YourApp-6967581b-497e-4f5a-87b5-0c8d01a9f146
     ClientSecret          : 6RUZLRoBw3EebMDgaWGiowCkoko5_j_ujIPjA8dS
     PSComputerName        : 192.168.200.224
     RunspaceId            : 286daaa1-c9a6-4176-a1a8-03f543f90998
     ```

#### <a name="update-client-secret-for-a-service-principal-for-ad-fs"></a>Aktualizovat tajný kód klienta instančního objektu služby pro službu AD FS

Nový tajný kód klienta je automaticky generovány pomocí rutiny prostředí PowerShell.

Je skript spuštěn na virtuálním počítači ERCS z privileged koncového bodu.

##### <a name="parameters"></a>Parametry

Tyto informace se vyžaduje jako vstup pro automatizaci parametry:

| Parametr | Popis | Příklad: |
|-----------------------|-----------------------------------------------------------------------------------------------------------|------------------------------------------------|
| ApplicationIdentifier | Jedinečný identifikátor. | S-1-5-21-1634563105-1224503876-2692824315-2119 |
| ChangeClientSecret | Změní tajný kód klienta s dobou výměny 2880 minut, kde původní tajný kód je stále platný. |  |
| ResetClientSecret | Změňte tajný kód klienta okamžitě |  |

##### <a name="example-of-updating-a-client-secret-for-ad-fs"></a>Příklad aktualizuje tajný klíč klienta služby AD FS

V příkladu se používá **ResetClientSecret** parametr, který okamžitě změní tajný kód klienta.

1. Otevřete relaci Windows Powershellu se zvýšenými oprávněními a spusťte následující rutiny:

     ```powershell  
          # Creating a PSSession to the ERCS PrivilegedEndpoint
          $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

          # This produces a self signed cert for testing purposes. It is preferred to use a managed certificate for this.
          $NewCert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange

          $UpdateServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier  S-1-5-21-1634563105-1224503876-2692824315-2120 -ResetClientSecret}

          $Session | Remove-PSSession
     ```

2. Po dokončení automatizace, zobrazí nově vygenerovaný tajného klíče pro ověření hlavního názvu služby. Ujistěte se, že ukládáte nový tajný kód klienta.

     ```powershell  
          ApplicationIdentifier : S-1-5-21-1634563105-1224503876-2692824315-2120
          ClientId              :  
          Thumbprint            : 
          ApplicationName       : Azurestack-Yourapp-6967581b-497e-4f5a-87b5-0c8d01a9f146
          ClientSecret          : MKUNzeL6PwmlhWdHB59c25WDDZlJ1A6IWzwgv_Kn
          RunspaceId            : 6ed9f903-f1be-44e3-9fef-e7e0e3f48564
     ```

### <a name="remove-a-service-principal-for-ad-fs"></a>Odebrání instančního objektu pro službu AD FS

Pokud jste nasadili Azure Stack se službou AD FS, můžete použít PowerShell odstranit instančního objektu.

Je skript spuštěn na virtuálním počítači ERCS z privileged koncového bodu.

#### <a name="parameters"></a>Parametry

Tyto informace se vyžaduje jako vstup pro automatizaci parametry:

|Parametr|Popis|Příklad:|
|---------|---------|---------|
| Parametr | Popis | Příklad: |
| ApplicationIdentifier | Jedinečný identifikátor | S-1-5-21-1634563105-1224503876-2692824315-2119 |

> [!Note]  
> Chcete-li zobrazit seznam všech existujících objektů služby a jejich identifikátoru aplikace, můžete pomocí příkazu get-graphapplication.

#### <a name="example-of-removing-the-service-principal-for-ad-fs"></a>Příklad odebrání instanční objekt služby pro službu AD FS

```powershell  
     Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
     $Creds = Get-Credential

     # Creating a PSSession to the ERCS PrivilegedEndpoint
     $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

     $UpdateServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {Remove-GraphApplication -ApplicationIdentifier S-1-5-21-1634563105-1224503876-2692824315-2119}

     $Session | Remove-PSSession
```

## <a name="assign-a-role"></a>Přiřazení role

Pro přístup k prostředkům ve vašem předplatném, musíte přiřadit aplikace k roli. Rozhodněte, jakou roli představuje správná oprávnění pro aplikaci. Další informace o dostupných rolí, najdete v článku [RBAC: Předdefinované Roles]((/azure/role-based-access-control/built-in-roles).

Nastavit obor na úrovni předplatného, skupinu prostředků nebo prostředek. Oprávnění se dědí do oboru na nižších úrovních. Například přidáním aplikace k roli Čtenář pro skupinu prostředků znamená, že můžete přečíst, skupinu prostředků a všechny prostředky, které obsahuje.

1. Na portálu Azure Stack přejděte na úroveň oboru, který chcete přiřadit aplikaci. Například vyberte přiřazení role v oboru předplatného, **předplatná**. Místo toho můžete třeba vybrat skupinu prostředků nebo prostředek.

2. Vyberte konkrétní předplatné (skupinu prostředků nebo prostředek), přiřazení aplikace.

     ![Vyberte předplatné pro přiřazení](./media/azure-stack-create-service-principal/image16.png)

3. Vyberte **řízení přístupu (IAM)**.

     ![Vyberte přístup](./media/azure-stack-create-service-principal/image17.png)

4. Vyberte **přidat přiřazení role**.

5. Vyberte roli, kterou chcete přiřadit k aplikaci.

6. Vyhledávání pro vaši aplikaci a vyberte ji.

7. Vyberte **OK** k dokončení přiřazení role. Zobrazí se vaše aplikace v seznamu Uživatelé přiřazení k roli pro tento obor.

Teď, když máte vytvořený instanční objekt služby a přiřazenou roli, můžete začít používat to v rámci vaší aplikace pro přístup k prostředkům Azure Stack.  

## <a name="next-steps"></a>Další postup

[Přidání uživatelů pro AD FS](azure-stack-add-users-adfs.md)  
[Správa uživatelských oprávnění](azure-stack-manage-permissions.md)  
[Dokumentace k Azure Active Directory](https://docs.microsoft.com/azure/active-directory)  
[Active Directory Federation Services (AD FS)](https://docs.microsoft.com/windows-server/identity/active-directory-federation-services)
