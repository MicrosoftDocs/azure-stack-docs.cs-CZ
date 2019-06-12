---
title: Integrované systémy pro Azure registrace pro Azure Stack | Dokumentace Microsoftu
description: Popisuje proces registrace služby Azure pro nasazení na víc uzlů Azure stacku Azure připojené.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2019
ms.author: mabrigg
ms.reviewer: avishwan
ms.lastreviewed: 03/04/2019
ms.openlocfilehash: 94eb107450271722af773bc96bec7dfeb12ff52e
ms.sourcegitcommit: e51cdc84a09250e8fa701bb2cb09de38d7de2c07
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/11/2019
ms.locfileid: "66836719"
---
# <a name="register-azure-stack-with-azure"></a>Registrace Azure Stack s Azure

Registrace služby Azure Stack v Azure vám umožní stahovat z Azure položky z marketplace a nastavit odesílání sestav dat o obchodech zpět do Microsoftu. Po dokončení registrace Azure Stack, využití se oznamuje službě Azure commerce a vy vidíte v rámci Azure fakturační ID předplatného použité pro registraci.

Informace v tomto článku popisuje registrace integrované systémy Azure Stack s Azure. Informace o registraci ASDK s využitím Azure najdete v tématu [registrace Azure Stack](../asdk/asdk-register.md) v dokumentaci k ASDK.

> [!IMPORTANT]  
> Chcete-li podporovat všechny funkce služby Azure Stack, včetně nabídka položek na webu Marketplace je nutná registrace. Kromě toho budete v rozporu se licenční podmínky, pokud nezaregistrujete při použití modelu fakturace platit jako využití služby Azure Stack. Další informace o službě Azure Stack licenční modely, najdete v tématu [jak koupit](https://azure.microsoft.com/overview/azure-stack/how-to-buy/).

## <a name="prerequisites"></a>Požadavky

Předtím, než zaregistrujete, potřebujete následující na místě:

 - Ověřit vaše přihlašovací údaje
 - Nastavte jazykový režim prostředí PowerShell
 - Instalace PowerShellu pro Azure Stack
 - Stáhněte si nástroje Azure Stack
 - Určit váš scénář registrace

### <a name="verify-your-credentials"></a>Ověřit vaše přihlašovací údaje

Před zaregistrováním služby Azure Stack s Azure, musíte mít:

- ID předplatného pro předplatné Azure. Pouze EA, CSP nebo CSP sdílené služby, který odběry podporují registrace. Poskytovatelé CSP se muset rozhodnout, jestli se má [použít jiné předplatné, CSP nebo APSS](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription).<br><br>K získání ID, přihlaste se k Azure, klikněte na tlačítko **všechny služby**. Potom v části **Obecné** vyberte **předplatná**, klikněte na předplatné, které chcete použít, a v části **Essentials** najdete ID předplatného.

  > [!Note]  
  > Německo Cloudová předplatná se momentálně nepodporují.

- Uživatelské jméno a heslo pro účet, který je vlastníkem předplatného.

- Uživatelský účet musí mít přístup k předplatnému Azure a máte oprávnění k vytváření aplikací identity a instanční objekty v adresáři spojený s tímto předplatným. Doporučujeme registraci Azure Stack v Azure pomocí správy s minimálními oprávněními. Další informace o tom, jak vytvořit definice vlastních rolí, která omezuje přístup k vašemu předplatnému pro registraci najdete v tématu [umožňuje vytvořit roli registrace pro Azure Stack](azure-stack-registration-role.md).

- Zaregistrovat poskytovatele prostředků služby Azure Stack (viz následující část zaregistrovat poskytovatele prostředků Azure Stack podrobnosti).

Po registraci není potřeba oprávnění globálního správce Azure Active Directory. Některé operace však může vyžadovat přihlašovací údaje globálního správce. Například skript instalační program zprostředkovatele prostředků nebo nová funkce vyžaduje oprávnění bylo uděleno. Můžete dočasně obnovit oprávnění globálního správce účtu, nebo použít samostatné globální správce účtu, který je vlastníkem *výchozí předplatné poskytovatele*.

Uživatel, který registruje Azure Stack je vlastníkem instanční objekt v Azure Active Directory. Registrace Azure Stack můžete upravit jenom uživatel, který zaregistroval Azure Stack. Pokud uživatel bez oprávnění správce, který není vlastníkem objektu registrace služby pokusí zaregistrovat nebo znovu zaregistrovat Azure Stack, můžou setkat 403 odpovědi. 403 odpovědi vyplývá, že uživatel nemá dostatečná oprávnění k dokončení operace.

Pokud nemáte předplatné Azure, které splňuje tyto požadavky, můžete si [vytvořit bezplatný účet Azure zde](https://azure.microsoft.com/free/?b=17.06). Registrace Azure Stack se neúčtují žádné poplatky na vaše předplatné Azure.

> [!NOTE]
> Pokud máte více než jeden Azure Stack, osvědčeným postupem je registrace jednotlivých Azure Stack do svého vlastního předplatného. To usnadní vám ke sledování využití.

### <a name="powershell-language-mode"></a>Režim jazyka prostředí PowerShell

Zajištění úspěšné registrace Azure Stack, musí být nastavena na režim jazyka PowerShell **FullLanguageMode**.  Pokud chcete ověřit, že aktuální režim jazyka nastaven na úplné, otevřete okno Powershellu se zvýšenými oprávněními a spusťte následující rutiny Powershellu:

```powershell  
$ExecutionContext.SessionState.LanguageMode
```

Zkontrolujte výstup vrací **FullLanguageMode**. Pokud je vrácena jiných režim jazyka, registrace musí být spuštěn na jiném počítači nebo režim jazyka musí být nastavena na **FullLanguageMode** než budete pokračovat.

### <a name="install-powershell-for-azure-stack"></a>Instalace PowerShellu pro Azure Stack

Použijte nejnovější PowerShell pro službu Azure Stack registraci v Azure.

Pokud ještě není nainstalovaný nejnovější verzi, přečtěte si téma [instalace Powershellu pro Azure Stack](azure-stack-powershell-install.md).

### <a name="download-the-azure-stack-tools"></a>Stáhněte si nástroje Azure Stack

Úložiště GitHub Azure Stack nástroje obsahuje moduly Powershellu, které podporují funkce služby Azure Stack. včetně registrace funkcí. Během procesu registrace budete muset importovat a používat **RegisterWithAzure.psm1** modul prostředí PowerShell, nalezen v úložišti Azure Stack nástroje pro registraci vaší instance služby Azure Stack v Azure.

Pokud chcete mít jistotu, že používáte nejnovější verzi, byste měli odstranit všechny existující verze nástroje Azure Stack a [stáhnout nejnovější verzi z Githubu](azure-stack-powershell-download.md) před registrací s Azure.

### <a name="determine-your-registration-scenario"></a>Určit váš scénář registrace

Nasazení Azure Stack může být *připojené* nebo *odpojení*.

 - **Připojení**  
 Připojení znamená, že jste nasadili Azure Stack, tak, aby se může připojit k Internetu a do Azure. Buď je Azure Active Directory (Azure AD) nebo Active Directory Federation Services (AD FS) pro úložiště identit. U připojených nasazení, můžete si vybrat z dva modely fakturace: platit jako využití nebo na základě kapacity.
    - [Zaregistrovat Azure s využitím propojené služby Azure Stack **platit jako využití** model fakturace](#register-connected-with-pay-as-you-go-billing)
    - [Zaregistrovat Azure s využitím propojené služby Azure Stack **kapacity** model fakturace](#register-connected-with-capacity-billing)

 - **Odpojení**  
 S odpojené od možnost nasazení v Azure, můžete nasadit a používat Azure Stack bez připojení k Internetu. Odpojené nasazení jste ale omezeni na úložiště identity služby AD FS a model fakturace založená na kapacitě.
    - [Registrace odpojeného pomocí služby Azure Stack **kapacity** model fakturace ](#register-disconnected-with-capacity-billing)

### <a name="determine-a-unique-registration-name-to-use"></a>Určení názvu jedinečným registračním použití 
Při registraci služby Azure Stack v Azure, je nutné zadat název jedinečný registrace. Snadný způsob, jak přidružit Azure registraci předplatného Azure Stack je použití služby Azure Stack **ID cloudu**. 

> [!NOTE]
> Azure Stack registrace pomocí modelu fakturace na základě kapacity bude nutné změnit jedinečný název, při registraci znovu po vypršení platnosti těchto ročních předplatných, pokud jste [odstranit vypršela platnost registrace](azure-stack-registration.md#change-the-subscription-you-use) a znovu zaregistrovat. Azure.

Pokud chcete zjistit ID cloudu pro vaše nasazení Azure Stack, otevřete PowerShell jako správce na počítači, který můžete přístup k privilegovaným koncový bod, spusťte následující příkazy a zaznamenejte **CloudID** hodnotu: 

```powershell
Run: Enter-PSSession -ComputerName <privileged endpoint computer name> -ConfigurationName PrivilegedEndpoint
Run: Get-AzureStackStampInformation 
```

## <a name="register-connected-with-pay-as-you-go-billing"></a>Registrace připojených s průběžnými platbami fakturace

Tyto kroky použijte k registraci ve službě Azure s využitím fakturace modelu plateb jako využití služby Azure Stack.

> [!Note]  
> Všechny tyto kroky musíte spustit na počítači, který má přístup k privilegovaným koncový bod (období). Podrobnosti o období najdete v tématu [pomocí privilegovaných koncového bodu ve službě Azure Stack](azure-stack-privileged-endpoint.md).

Propojené prostředí můžete přístup k Internetu a z Azure. U těchto prostředí budete muset v Azure, zaregistrujte poskytovatele prostředků služby Azure Stack a potom nakonfigurujte váš model fakturace.

1. Registraci poskytovatele prostředků služby Azure Stack v Azure, spusťte prostředí PowerShell ISE jako správce a pomocí následujících rutin prostředí PowerShell s **EnvironmentName** parametr nastaven na typ příslušné předplatné Azure (viz níže uvedené parametry).

2. Přidáte účet Azure, který používáte k registraci Azure Stack. Chcete-li přidat účet, spusťte **Add-AzureRmAccount** rutiny. Zobrazí se výzva k zadání přihlašovacích údajů k účtu Azure a budete muset použít 2 ověřování na základě konfigurace vašeho účtu.

   ```powershell
   Add-AzureRmAccount -EnvironmentName "<environment name>"
   ```

   | Parametr | Popis |  
   |-----|-----|
   | EnvironmentName | Název prostředí předplatného cloudu Azure. Názvy prostředí podporované jsou **AzureCloud**, **AzureUSGovernment**, nebo pokud se používá k předplatnému Azure Čína **AzureChinaCloud**.  |

3. Pokud máte více předplatných, spuštěním následujícího příkazu vyberte předplatné, že které chcete použít:  

   ```powershell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. Spuštěním následujícího příkazu zaregistrujte poskytovatele prostředků služby Azure Stack ve vašem předplatném Azure:

   ```powershell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Spusťte ISE Powershellu jako správce a přejděte do **registrace** složky **AzureStack-Tools-master** adresář vytvořený při stažení nástroje Azure Stack. Import **RegisterWithAzure.psm1** modulu pomocí prostředí PowerShell:

   ```powershell  
   Import-Module .\RegisterWithAzure.psm1
   ```

6. V dalším kroku ve stejné relaci Powershellu, ujistěte se, že jste přihlášeni k správný kontext Azure Powershellu. Toto je účet Azure, která byla použita dříve zaregistrovat poskytovatele prostředků služby Azure Stack. Prostředí PowerShell pro spuštění:

   ```powershell  
   Connect-AzureRmAccount -Environment "<environment name>"
   ```

   | Parametr | Popis |  
   |-----|-----|
   | EnvironmentName | Název prostředí předplatného cloudu Azure. Názvy prostředí podporované jsou **AzureCloud**, **AzureUSGovernment**, nebo pokud se používá k předplatnému Azure Čína **AzureChinaCloud**.  |

7. Ve stejné relaci prostředí PowerShell, spusťte **Set-AzsRegistration** rutiny. Prostředí PowerShell pro spuštění:  

   ```powershell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel PayAsYouUse `
      -RegistrationName $RegistrationName
   ```
   Další informace o rutině Set-AzsRegistration najdete v tématu [Reference k registraci](#registration-reference).

   Tento proces trvá 10 až 15 minut. Po dokončení příkazu se zobrazí zpráva **"prostředí je teď zaregistrované a aktivovat pomocí zadaných parametrů."**

## <a name="register-connected-with-capacity-billing"></a>Registrace připojených s využitím fakturace kapacity

Tyto kroky použijte k registraci ve službě Azure s využitím fakturace modelu plateb jako využití služby Azure Stack.

> [!Note]  
> Všechny tyto kroky musíte spustit na počítači, který má přístup k privilegovaným koncový bod (období). Podrobnosti o období najdete v tématu [pomocí privilegovaných koncového bodu ve službě Azure Stack](azure-stack-privileged-endpoint.md).

Propojené prostředí můžete přístup k Internetu a z Azure. U těchto prostředí budete muset v Azure, zaregistrujte poskytovatele prostředků služby Azure Stack a potom nakonfigurujte váš model fakturace.

1. Registraci poskytovatele prostředků služby Azure Stack v Azure, spusťte prostředí PowerShell ISE jako správce a pomocí následujících rutin prostředí PowerShell s **EnvironmentName** parametr nastaven na typ příslušné předplatné Azure (viz níže uvedené parametry).

2. Přidáte účet Azure, který používáte k registraci Azure Stack. Chcete-li přidat účet, spusťte **Add-AzureRmAccount** rutiny. Zobrazí se výzva k zadání přihlašovacích údajů k účtu Azure a budete muset použít 2 ověřování na základě konfigurace vašeho účtu.

   ```powershell  
   Connect-AzureRmAccount -Environment "<environment name>"
   ```

   | Parametr | Popis |  
   |-----|-----|
   | EnvironmentName | Název prostředí předplatného cloudu Azure. Názvy prostředí podporované jsou **AzureCloud**, **AzureUSGovernment**, nebo pokud se používá k předplatnému Azure Čína **AzureChinaCloud**.  |

3. Pokud máte více předplatných, spuštěním následujícího příkazu vyberte předplatné, že které chcete použít:  

   ```powershell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. Spuštěním následujícího příkazu zaregistrujte poskytovatele prostředků služby Azure Stack ve vašem předplatném Azure:

   ```powershell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Spusťte ISE Powershellu jako správce a přejděte do **registrace** složky **AzureStack-Tools-master** adresář vytvořený při stažení nástroje Azure Stack. Import **RegisterWithAzure.psm1** modulu pomocí prostředí PowerShell:

   ```powershell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -AgreementNumber <EA agreement number> `
      -BillingModel Capacity `
      -RegistrationName $RegistrationName
   ```
   > [!Note]  
   > Zakážete generování sestav s parametrem UsageReportingEnabled pro použití **Set-AzsRegistration** rutiny nastavením parametru na hodnotu false. 
   
   Další informace o rutině Set-AzsRegistration najdete v tématu [Reference k registraci](#registration-reference).

## <a name="register-disconnected-with-capacity-billing"></a>Registrace odpojeného s využitím fakturace kapacity

Pokud při registraci služby Azure Stack v odpojeném prostředí (bez připojení k Internetu), musíte k získání tokenu registrace z prostředí Azure Stack a pak pomocí tohoto tokenu v počítači, který se můžete připojit k Azure a nabízí prostředí PowerShell pro Azure Stack nainstalovat.  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Získání tokenu registrace z prostředí Azure Stack

1. Spusťte ISE Powershellu jako správce a přejděte do **registrace** složky **AzureStack-Tools-master** adresář vytvořený při stažení nástroje Azure Stack. Import **RegisterWithAzure.psm1** modul:  

   ```powershell  
   Import-Module .\RegisterWithAzure.psm1
   ```

2. Registrační token získáte spuštěním následující rutiny prostředí PowerShell:  

   ```Powershell
   $FilePathForRegistrationToken = "$env:SystemDrive\RegistrationToken.txt"
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $YourCloudAdminCredential -UsageReportingEnabled:$False -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```
   Další informace o rutině Get-AzsRegistrationToken najdete v tématu [Reference k registraci](#registration-reference).

   > [!Tip]  
   > Registrační token se uloží do souboru určeného pro *$FilePathForRegistrationToken*. Cesta k souboru nebo název souboru můžete změnit na vašem uvážení.

3. Uložit tento registrační token pro použití v Azure připojené počítače. Z $FilePathForRegistrationToken můžete zkopírovat soubor nebo text.

### <a name="connect-to-azure-and-register"></a>Připojení k Azure a registrace

Na počítači, který je připojený k Internetu proveďte stejné kroky pro import modulu RegisterWithAzure.psm1 a přihlaste se k správný kontext Azure Powershellu. Potom zavolejte AzsEnvironment registru. Zadejte token registrace k registraci ve službě Azure. Pokud registrujete více než jednu instanci služby Azure Stack pomocí stejného ID předplatného Azure, zadejte název jedinečné registrace.

Budete potřebovat registrační token a jedinečný název tokenu.

1. Spusťte ISE Powershellu jako správce a přejděte do **registrace** složky **AzureStack-Tools-master** adresář vytvořený při stažení nástroje Azure Stack. Import **RegisterWithAzure.psm1** modul:  

   ```powershell  
   Import-Module .\RegisterWithAzure.psm1
   ```

2. Potom spuštěním následující rutiny prostředí PowerShell:  

  ```powershell  
  $RegistrationToken = "<Your Registration Token>"
  $RegistrationName = "<unique-registration-name>"
  Register-AzsEnvironment -RegistrationToken $RegistrationToken -RegistrationName $RegistrationName
  ```

Volitelně můžete použít rutinu Get-obsah tak, aby odkazoval na soubor, který obsahuje registrační token.

Budete potřebovat registrační token a jedinečný název tokenu.

1. Spusťte ISE Powershellu jako správce a přejděte do **registrace** složky **AzureStack-Tools-master** adresář vytvořený při stažení nástroje Azure Stack. Import **RegisterWithAzure.psm1** modul:  

  ```powershell  
  Import-Module .\RegisterWithAzure.psm1
  ```

2. Potom spuštěním následující rutiny prostředí PowerShell:  

  ```powershell  
  $RegistrationToken = Get-Content -Path '<Path>\<Registration Token File>'
  Register-AzsEnvironment -RegistrationToken $RegistrationToken -RegistrationName $RegistrationName
  ```

  > [!Note]  
  > Uložte název prostředku registrace a registrační token pro budoucí použití.

### <a name="retrieve-an-activation-key-from-azure-registration-resource"></a>Načtení aktivační kód z prostředku registrace služby Azure

Dále je třeba načíst z registrace prostředku v Azure vytvořit během registrace AzsEnvironment aktivační kód.

Aktivační klíč získáte spuštěním následující rutiny prostředí PowerShell:  

  ```Powershell
  $RegistrationResourceName = "<unique-registration-name>"
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName -KeyOutputFilePath $KeyOutputFilePath
  ```

  > [!Tip]  
  > Aktivační kód je uložen v souboru určeném pro *$KeyOutputFilePath*. Cesta k souboru nebo název souboru můžete změnit na vašem uvážení.

### <a name="create-an-activation-resource-in-azure-stack"></a>Vytvoření prostředku Aktivace ve službě Azure Stack

Vraťte se do prostředí Azure Stack pomocí souboru nebo text od aktivace klíče vytvořeného z Get-AzsActivationKey. Dále vytvořte prostředek aktivace ve službě Azure Stack pomocí této aktivační kód. K aktivaci prostředků vytvoříte spuštěním následující rutiny prostředí PowerShell: 

  ```Powershell
  $ActivationKey = "<activation key>"
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```

Volitelně můžete použít rutinu Get-obsah tak, aby odkazoval na soubor, který obsahuje registrační token:

  ```Powershell
  $ActivationKey = Get-Content -Path '<Path>\<Activation Key File>'
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```

## <a name="verify-azure-stack-registration"></a>Ověření registrace Azure Stack

Můžete použít **Správa oblastí** dlaždici a ověřte, že Azure Stack registrace byla úspěšná. Tato dlaždice není k dispozici na výchozí řídicí panel portálu pro správu. Stav může být registrováno nebo není zaregistrovaný. Pokud je zaregistrován, také ukazuje, ID předplatného Azure, který jste použili k registraci společně s registrace skupiny prostředků a název služby Azure Stack.

1. Přihlaste se k [portál pro správu služby Azure Stack](https://adminportal.local.azurestack.external).

2. Z řídicího panelu, vyberte **Správa oblastí**.

3. Vyberte **vlastnosti**. Toto okno zobrazuje stav a podrobnosti o vašem prostředí. Stav může být **registrované**, **Neregistrovaný**, nebo **neplatné**.

    [![Dlaždice oblasti správy](media/azure-stack-registration/admin1sm.png "dlaždice oblasti správy")](media/azure-stack-registration/admin1.png#lightbox)

    Pokud je zaregistrován, vlastnosti patří:
    
    - **ID registrace předplatného**: Registrované ID předplatného Azure a přidružené ke službě Azure Stack
    - **Skupina prostředků registrace**: Skupina prostředků Azure v rámci přidruženého předplatného obsahující prostředky služby Azure Stack.

4. Zobrazit registrace aplikací služby Azure Stack pomocí webu Azure portal. Přihlaste se k webu Azure portal pomocí účtu přidružené k předplatnému, že kterou jste použili k registraci Azure Stack. Přepnutí na tenanta přidruženého k Azure Stack.
5. Přejděte do **Azure Active Directory > Registrace aplikací > Zobrazit všechny aplikace**.

    ![Registrace aplikací](media/azure-stack-registration/app-registrations.png)

    Registrace aplikace Azure Stack mají předponu **Azure Stack**.

Alternativně můžete ověřit, jestli registrace byla úspěšná pomocí funkce pro správu webu Marketplace. Pokud se zobrazí seznam položek z marketplace v okně správy webu Marketplace, vaše registrace byla úspěšná. Ale v odpojených prostředích, nebudete moci zobrazit položky marketplace v Marketplace správu.

> [!NOTE]
> Po dokončení registrace se už zobrazí aktivní upozornění pro registraci není. V odpojených scénářů zobrazí se zprávy v Marketplace správu s výzvou k registraci a aktivaci služby Azure Stack, i v případě, že jste úspěšně zaregistrovali.

## <a name="renew-or-change-registration"></a>Prodloužení nebo změnit registrace

### <a name="renew-or-change-registration-in-connected-environments"></a>Prodloužení nebo změnit registrace v připojených prostředích

Budete muset aktualizovat nebo obnovit vaše registrace v následujících případech:

- Po obnovení roční předplatné založená na kapacitě.
- Pokud změníte model fakturace.
- Při vertikálním navýšení změny (přidávat nebo odebírat uzly) pro fakturaci na základě kapacity.

#### <a name="change-the-subscription-you-use"></a>Změnit předplatné, které používáte

Pokud chcete změnit předplatné používáte, je třeba nejprve spustit **odebrat AzsRegistration** rutiny, zajistěte jste přihlášeni do správného kontextu prostředí Azure PowerShell a nakonec spusťte **Set AzsRegistration**  se všechny změněné parametry, včetně `<billing model>`:

  ```powershell  
  Remove-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -RegistrationName $RegistrationName
  Set-AzureRmContext -SubscriptionId $NewSubscriptionId
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel <billing model> -RegistrationName $RegistrationName
  ```

#### <a name="change-the-billing-model-or-how-to-offer-features"></a>Změňte model fakturace nebo jak nabídnout funkce

Pokud chcete změnit model fakturace nebo jak nabízí funkce pro vaši instalaci, můžete volat funkci registrace k nastavení nových hodnot. Není nutné nejprve odebrat aktuální registrace:

  ```powershell  
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel <billing model> -RegistrationName $RegistrationName
  ```

### <a name="renew-or-change-registration-in-disconnected-environments"></a>Prodloužení nebo změnit registrace v odpojených prostředích

Budete muset aktualizovat nebo obnovit vaše registrace v následujících případech:

- Po obnovení roční předplatné založená na kapacitě.
- Pokud změníte model fakturace.
- Při vertikálním navýšení změny (přidávat nebo odebírat uzly) pro fakturaci na základě kapacity.

#### <a name="remove-the-activation-resource-from-azure-stack"></a>Odebrání prostředku Aktivace služby Azure Stack

Nejdřív muset odebrat aktivace prostředků ze služby Azure Stack a potom prostředku registrace v Azure.  

Pokud chcete odebrat aktivace prostředků ve službě Azure Stack, spusťte následující rutiny prostředí PowerShell v prostředí Azure Stack:  

  ```Powershell
  Remove-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  ```

V dalším kroku se odebrat prostředek registrace v Azure, ujistěte se v Azure připojené počítače, přihlaste se k správný kontext Azure Powershellu a spusťte odpovídající rutiny prostředí PowerShell, jak je popsáno níže.

Registrační token použitý k vytvoření prostředku můžete použít:  

  ```Powershell
  $RegistrationToken = "<registration token>"
  Unregister-AzsEnvironment -RegistrationToken $RegistrationToken
  ```

Nebo můžete použít název registrace:

  ```Powershell
  $RegistrationName = "AzureStack-<unique-registration-name>"
  Unregister-AzsEnvironment -RegistrationName $RegistrationName
  ```

### <a name="re-register-using-disconnected-steps"></a>Proveďte registraci znovu pomocí odpojené kroky

Jste nyní zcela neregistrovaná v odpojeném scénáři a nutné opakovat kroky pro registraci prostředí Azure Stack v odpojeném scénář.

### <a name="disable-or-enable-usage-reporting"></a>Zakázání nebo povolení generování sestav o využívání

Pro prostředí Azure Stack, které používají model fakturace kapacity vypnout používání vytváření sestav pomocí **UsageReportingEnabled** buď pomocí parametru **Set-AzsRegistration** nebo  **Get-AzsRegistrationToken** rutiny. Azure Stack sestavy metriky využití ve výchozím nastavení. Operátory se používá kapacity nebo podpora odpojené prostředí je potřeba vypnout generování sestav o využívání.

#### <a name="with-a-connected-azure-stack"></a>Pomocí připojené služby Azure Stack

   ```powershell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel Capacity
      -RegistrationName $RegistrationName
   ```

#### <a name="with-a-disconnected-azure-stack"></a>Odpojené Azure Stack

1. Chcete-li změnit registračního tokenu, spusťte následující rutiny prostředí PowerShell:  

   ```Powershell
   $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential -UsageReportingEnabled:$False
   $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```

   > [!Tip]  
   > Registrační token se uloží do souboru určeného pro *$FilePathForRegistrationToken*. Cesta k souboru nebo název souboru můžete změnit na vašem uvážení.

2. Uložit tento registrační token pro použití v Azure připojené počítače. Z $FilePathForRegistrationToken můžete zkopírovat soubor nebo text.

## <a name="move-a-registration-resource"></a>Přesunutí prostředku registrace
Přesunutí prostředku registrace mezi skupinami prostředků ve stejném předplatném **je** podporovány pro všechna prostředí. Ale přesunutí prostředku registrace mezi předplatnými je podporováno pouze pro zprostředkovatele kryptografických služeb pokud obě předplatná přeložit na stejné ID partnera. Další informace o přesunutí prostředků do nové skupiny prostředků, najdete v části [přesunutí prostředků do nové skupiny prostředků nebo předplatného](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).

## <a name="registration-reference"></a>Reference k registraci

### <a name="set-azsregistration"></a>Set-AzsRegistration

Set-AzsRegistration můžete použít k registraci Azure Stack v Azure a povolení nebo zakázání nabídka položek v marketplace a generování sestav o využívání.

Pokud chcete spustit rutinu, budete potřebovat:
- Předplatné Azure globální libovolného typu.
- Musíte také být přihlášeni k Azure Powershellu pomocí účtu, který je vlastníkem nebo přispěvatelem předplatného.

```powershell
Set-AzsRegistration [-PrivilegedEndpointCredential] <PSCredential> [-PrivilegedEndpoint] <String> [[-AzureContext]
    <PSObject>] [[-ResourceGroupName] <String>] [[-ResourceGroupLocation] <String>] [[-BillingModel] <String>]
    [-MarketplaceSyndicationEnabled] [-UsageReportingEnabled] [[-AgreementNumber] <String>] [[-RegistrationName]
    <String>] [<CommonParameters>]
```

| Parametr | Typ | Popis |
|-------------------------------|--------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| PrivilegedEndpointCredential | PSCredential | Pověření používaná k [přístup k privilegovaným koncový bod](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). Uživatelské jméno je ve formátu **AzureStackDomain\CloudAdmin**. |
| PrivilegedEndpoint | String | Předem nakonfigurované vzdálenou konzolu Powershellu, který vám poskytne funkce, jako je shromažďování protokolů a další příspěvek úlohy nasazení. Další informace najdete [pomocí privilegovaných koncového bodu](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint) článku. |
| AzureContext | PSObject |  |
| ResourceGroupName | String |  |
| ResourceGroupLocation | String |  |
| BillingModel | String | Model fakturace předplatného používá. Povolené hodnoty tohoto parametru jsou: Kapacita, PayAsYouUse a vývoj. |
| MarketplaceSyndicationEnabled | True nebo False | Určuje, zda funkce pro správu webu marketplace je k dispozici na portálu. Nastavte na hodnotu true, pokud se registrace s připojením k Internetu. Nastavte na hodnotu false, pokud se registrace v odpojených prostředích. Pro odpojený registrace [nástroj v režimu offline syndikace](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario) lze použít pro stažení položek z marketplace. |
| UsageReportingEnabled | True nebo False | Azure Stack sestavy metriky využití ve výchozím nastavení. Operátory se používá kapacity nebo podpora odpojené prostředí je potřeba vypnout generování sestav o využívání. Povolené hodnoty tohoto parametru jsou: Hodnota TRUE, False. |
| AgreementNumber | String |  |
| RegistrationName | String | Nastavit jedinečný název pro registraci, pokud používáte skript registrace na více než jednu instanci služby Azure Stack pomocí stejného předplatného Azure ID. Parametr má výchozí hodnotu **AzureStackRegistration**. Pokud však použijete stejný název ve více než jednu instanci služby Azure Stack, skript selže. |

### <a name="get-azsregistrationtoken"></a>Get-AzsRegistrationToken

Get-AzsRegistrationToken vygeneruje registrační token ze vstupních parametrů.

```powershell  
Get-AzsRegistrationToken [-PrivilegedEndpointCredential] <PSCredential> [-PrivilegedEndpoint] <String>
    [-BillingModel] <String> [[-TokenOutputFilePath] <String>] [-UsageReportingEnabled] [[-AgreementNumber] <String>]
    [<CommonParameters>]
```
| Parametr | Typ | Popis |
|-------------------------------|--------------|-------------|
| PrivilegedEndpointCredential | PSCredential | Pověření používaná k [přístup k privilegovaným koncový bod](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). Uživatelské jméno je ve formátu **AzureStackDomain\CloudAdmin**. |
| PrivilegedEndpoint | String |  Předem nakonfigurované vzdálenou konzolu Powershellu, který vám poskytne funkce, jako je shromažďování protokolů a další příspěvek úlohy nasazení. Další informace najdete [pomocí privilegovaných koncového bodu](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint) článku. |
| AzureContext | PSObject |  |
| ResourceGroupName | String |  |
| ResourceGroupLocation | String |  |
| BillingModel | String | Model fakturace předplatného používá. Povolené hodnoty tohoto parametru jsou: Kapacita, PayAsYouUse a vývoj. |
| MarketplaceSyndicationEnabled | True nebo False |  |
| UsageReportingEnabled | True nebo False | Azure Stack sestavy metriky využití ve výchozím nastavení. Operátory se používá kapacity nebo podpora odpojené prostředí je potřeba vypnout generování sestav o využívání. Povolené hodnoty tohoto parametru jsou: Hodnota TRUE, False. |
| AgreementNumber | String |  |

## <a name="registration-failures"></a>Selhání registrace

Může se zobrazit jeden z u chyb dole při pokusu o registraci ze služby Azure Stack:
1. Nelze načíst informace o hardwaru povinné pro $hostName. Zaškrtněte fyzického hostitele a možnosti připojení a poté zkuste znovu spustit registrace.

2. Nelze se připojit k $hostName k získání informací o hardwaru – prosím zkontrolujte fyzického hostitele a připojení a zkuste znovu spusťte registraci.

> Příčina: to je obvykle vzhledem k tomu, pokuste se získat podrobnosti o hardwaru, jako je například UUID a procesoru a systému Bios od hostitelů, aby se zkusil jsme nebyli schopni se připojit k fyzickým hostitelem lokalizovat.

Při pokusu o přístup k webu Marketplace správu, dojde k chybě při pokusu o zajistěte syndikaci produktů. 
> Příčina: tomu obvykle dojde, když nelze získat přístup k prostředku registrace Azure Stack. Jeden z běžných důvodů to je, že při změně tenanta adresáře Azure předplatné obnoví registrace. Azure Stack marketplace nebo v sestavě využití nelze přistupovat, pokud jste změnili adresáře tenanta předplatného. Budete muset znovu zaregistrovat, pokud chcete tento problém vyřešit.

Marketplace správu stále žádostí o registraci a aktivaci služby Azure Stack i v případě, že jste již registrováni razítko pomocí procesu odpojené. 
> Příčina: jde o známý problém pro odpojené prostředí. Stav registrace můžete ověřit pomocí následujících [tyto kroky](azure-stack-registration.md#verify-azure-stack-registration). Chcete-li použít správu webu Marketplace, budete muset použít [nástroj v režimu offline](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario). 

## <a name="next-steps"></a>Další postup

[Stažení položek z marketplace z Azure](azure-stack-download-azure-marketplace-item.md)
