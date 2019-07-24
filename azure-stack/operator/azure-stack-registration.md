---
title: Registrace Azure pro integrované systémy Azure Stack | Microsoft Docs
description: Popisuje proces registrace Azure pro nasazení ve více uzlech Azure Stack připojení k Azure.
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
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: avishwan
ms.lastreviewed: 03/04/2019
ms.openlocfilehash: 3fd84e5c294c2cdcfa942aeaf9c2daf9f9245891
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68418211"
---
# <a name="register-azure-stack-with-azure"></a>Registrace Azure Stack s využitím Azure

Registrace služby Azure Stack v Azure vám umožní stahovat z Azure položky z marketplace a nastavit odesílání sestav dat o obchodech zpět do Microsoftu. Po registraci Azure Stack se využití nahlásí službě Azure Commerce a zobrazí se pod ID předplatného fakturace Azure, které se používá k registraci.

Informace v tomto článku popisují registraci Azure Stack integrovaných systémů s Azure. Informace o registraci ASDK s Azure najdete v tématu [registrace Azure Stack](../asdk/asdk-register.md) v dokumentaci k ASDK.

> [!IMPORTANT]  
> K podpoře úplných funkcí Azure Stack, včetně položek nabídky na webu Marketplace, je nutná registrace. Pokud se nezaregistrujete při použití modelu fakturace s průběžnými platbami, budete mít porušení licenčních podmínek Azure Stack. Další informace o Azure Stack modelech licencování najdete na [stránce Jak koupit](https://azure.microsoft.com/overview/azure-stack/how-to-buy/).

## <a name="prerequisites"></a>Požadavky

Před registrací budete potřebovat toto:

 - Ověření přihlašovacích údajů
 - Nastavení režimu jazyka PowerShell
 - Instalace PowerShellu pro Azure Stack
 - Stažení nástrojů pro Azure Stack
 - Určení scénáře registrace

### <a name="verify-your-credentials"></a>Ověření přihlašovacích údajů

Před registrací Azure Stack s Azure musíte mít:

- ID předplatného pro předplatné Azure. Pro registraci jsou podporovány pouze odběry služeb EA, CSP nebo CSP sdílené služby. CSP musí rozhodnout, jestli se má [použít předplatné CSP nebo APSS](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription).<br><br>ID získáte tak, že se přihlásíte k Azure a kliknete na **všechny služby**. Pak v kategorii **Obecné** vyberte předplatná, klikněte na předplatné, které chcete použít, a v části **Essentials** můžete najít ID předplatného.

  > [!Note]  
  > V současné době se nepodporují předplatná pro Německo Cloud.

- Uživatelské jméno a heslo pro účet, který je vlastníkem předplatného.

- Uživatelský účet musí mít přístup k předplatnému Azure a mít oprávnění k vytváření aplikací identity a instančních objektů v adresáři přidruženém k tomuto předplatnému. Doporučujeme, abyste v Azure zaregistrovali Azure Stack s využitím správy s minimálními oprávněními. Další informace o tom, jak vytvořit definici vlastní role, která omezuje přístup k vašemu předplatnému k registraci, najdete v tématu [Vytvoření registrační role pro Azure Stack](azure-stack-registration-role.md).

- Zaregistrovali jste poskytovatele prostředků Azure Stack (podrobnosti najdete v části věnované poskytovateli prostředků Azure Stack v tomto registru).

Po registraci se nepožaduje oprávnění globálního správce Azure Active Directory. Některé operace ale můžou vyžadovat přihlašovací údaje globálního správce. Například skript instalačního programu poskytovatele prostředků nebo nová funkce vyžadující udělené oprávnění. Můžete buď dočasně obnovit oprávnění globálního správce účtu, nebo použít samostatný účet globálního správce, který je vlastníkem *výchozího předplatného poskytovatele*.

Uživatel, který registruje Azure Stack, je vlastníkem instančního objektu v Azure Active Directory. Registraci Azure Stack může změnit jenom uživatel, který zaregistroval Azure Stack. Pokud se uživatel bez role správce, který není vlastníkem instančního objektu služby, pokusí zaregistrovat nebo znovu zaregistrovat Azure Stack, může dojít k odpovědi 403. Odpověď 403 indikuje, že uživatel nemá dostatečná oprávnění k dokončení operace.

Pokud nemáte předplatné Azure, které tyto požadavky splňuje, můžete si [tady vytvořit bezplatný účet Azure](https://azure.microsoft.com/free/?b=17.06). Registrace Azure Stack nevzniká žádné náklady na vaše předplatné Azure.

> [!NOTE]
> Pokud máte více než jeden Azure Stack, je osvědčeným postupem, jak každou Azure Stack zaregistrovat do svého vlastního předplatného. To vám usnadní sledování využití.

### <a name="powershell-language-mode"></a>Režim jazyka PowerShell

Aby bylo možné úspěšně zaregistrovat Azure Stack, musí být režim jazyka PowerShell nastaven na **FullLanguageMode**.  Pokud chcete ověřit, jestli je aktuální jazykový režim nastavený na Full, otevřete okno PowerShellu se zvýšenými oprávněními a spusťte následující rutiny PowerShellu:

```powershell  
$ExecutionContext.SessionState.LanguageMode
```

Ujistěte se, že výstup vrátí **FullLanguageMode**. Pokud se vrátí jiný jazykový režim, musí být registrace spuštěná na jiném počítači nebo musí být nastavená na **FullLanguageMode** , aby bylo možné pokračovat.

### <a name="install-powershell-for-azure-stack"></a>Instalace PowerShellu pro Azure Stack

K registraci v Azure použijte nejnovější prostředí PowerShell pro Azure Stack.

Pokud není nejnovější verze ještě nainstalovaná, přečtěte si téma [instalace PowerShellu pro Azure Stack](azure-stack-powershell-install.md).

### <a name="download-the-azure-stack-tools"></a>Stažení nástrojů pro Azure Stack

Úložiště GitHub pro Azure Stack Tools obsahuje moduly prostředí PowerShell, které podporují funkce Azure Stack. včetně funkce registrace. Během procesu registrace je potřeba importovat a používat modul **RegisterWithAzure. psm1** PowerShell, který se nachází v úložišti nástrojů Azure Stack k registraci vaší instance Azure Stack v Azure.

Abyste měli jistotu, že používáte nejnovější verzi, měli byste před registrací v Azure odstranit všechny existující verze nástrojů Azure Stack a [Stáhnout nejnovější verzi z GitHubu](azure-stack-powershell-download.md) .

### <a name="determine-your-registration-scenario"></a>Určení scénáře registrace

Vaše nasazení Azure Stack může být *připojeno* nebo *Odpojeno*.

 - **Připojen**  
 Připojeno znamená, že jste nasadili Azure Stack, aby se mohl připojit k Internetu a k Azure. Pro úložiště identity buď máte Azure Active Directory (Azure AD) nebo Active Directory Federation Services (AD FS) (AD FS). V případě připojeného nasazení si můžete vybrat ze dvou modelů fakturace: průběžné platby nebo na základě kapacity.
    - [Registrace propojeného Azure Stack s Azure s využitím modelu fakturace s průběžnými **platbami podle aktuálního využití**](#register-connected-with-pay-as-you-go-billing)
    - [Registrace připojeného Azure Stack s Azure pomocí modelu fakturace **kapacity**](#register-connected-with-capacity-billing)

 - **Propojení**  
 Díky možnosti nasazení odpojeno od Azure můžete nasadit a používat Azure Stack bez připojení k Internetu. V případě odpojeného nasazení ale budete omezeni na AD FS úložiště identit a model fakturace na základě kapacity.
    - [Registrace odpojených Azure Stack pomocí modelu fakturace **kapacity**](#register-disconnected-with-capacity-billing)

### <a name="determine-a-unique-registration-name-to-use"></a>Určení jedinečného názvu registrace, který se má použít 
Když zaregistrujete Azure Stack s Azure, musíte zadat jedinečný název registrace. Snadný způsob, jak přidružit předplatné Azure Stack k registraci Azure, je použití vašeho Azure Stackho **cloudového ID**. 

> [!NOTE]
> Azure Stack registrace pomocí modelu fakturace založeného na kapacitě bude muset po vypršení platnosti [registrace](azure-stack-registration.md#change-the-subscription-you-use) a opětovné registraci v Azure změnit jedinečný název při opětovné registraci po uplynutí těchto ročních předplatných.

Pokud chcete zjistit ID cloudu pro nasazení Azure Stack, otevřete PowerShell jako správce v počítači, který má přístup k privilegovanému koncovému bodu, spusťte následující příkazy a zaznamenejte hodnotu **CloudID** : 

```powershell
Run: Enter-PSSession -ComputerName <privileged endpoint computer name> -ConfigurationName PrivilegedEndpoint
Run: Get-AzureStackStampInformation 
```

## <a name="register-connected-with-pay-as-you-go-billing"></a>Zaregistrujte se k platbám s průběžnými platbami

Pomocí těchto kroků zaregistrujete Azure Stack s Azure s využitím modelu fakturace s průběžnými platbami podle aktuálního využití.

> [!Note]  
> Všechny tyto kroky je nutné spustit z počítače, který má přístup k privilegovanému koncovému bodu (PEP). Podrobnosti o PEP najdete v tématu [použití privilegovaného koncového bodu v Azure Stack](azure-stack-privileged-endpoint.md).

Připojená prostředí mají přístup k Internetu a k Azure. V těchto prostředích je potřeba zaregistrovat poskytovatele prostředků Azure Stack s Azure a potom nakonfigurovat model fakturace.

1. Pokud chcete zaregistrovat poskytovatele prostředků Azure Stack v Azure, spusťte PowerShell ISE jako správce a použijte následující rutiny PowerShellu s parametrem **Environment** nastaveným na příslušný typ předplatného Azure (viz níže uvedené parametry).

2. Přidejte účet Azure, který používáte k registraci Azure Stack. Pokud chcete účet přidat, spusťte rutinu **Add-AzureRmAccount** . Zobrazí se výzva k zadání přihlašovacích údajů k účtu Azure a možná budete muset použít dvojúrovňové ověřování na základě konfigurace vašeho účtu.

   ```powershell
   Add-AzureRmAccount -EnvironmentName "<environment name>"
   ```

   | Parametr | Popis |  
   |-----|-----|
   | EnvironmentName | Název prostředí Azure Cloud Subscription. Podporované názvy prostředí jsou **AzureCloud**, **AzureUSGovernment**, nebo pokud používáte čínské předplatné Azure **AzureChinaCloud**.  |

3. Máte-li více předplatných, spusťte následující příkaz a vyberte ten, který chcete použít:  

   ```powershell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. Spuštěním následujícího příkazu zaregistrujte poskytovatele prostředků Azure Stack v předplatném Azure:

   ```powershell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Spusťte PowerShell ISE jako správce a přejděte do **registrační** složky v adresáři **AzureStack-Tools-Master** , který jste vytvořili při stahování nástrojů Azure Stack. Importujte modul **RegisterWithAzure. psm1** pomocí prostředí PowerShell:

   ```powershell  
   Import-Module .\RegisterWithAzure.psm1
   ```

6. Potom ve stejné relaci prostředí PowerShell ověřte, že jste přihlášeni ke správnému kontextu Azure PowerShell. Toto je účet Azure, který byl dříve použit k registraci poskytovatele prostředků Azure Stack. PowerShell ke spuštění:

   ```powershell  
   Connect-AzureRmAccount -Environment "<environment name>"
   ```

   | Parametr | Popis |  
   |-----|-----|
   | EnvironmentName | Název prostředí Azure Cloud Subscription. Podporované názvy prostředí jsou **AzureCloud**, **AzureUSGovernment**, nebo pokud používáte čínské předplatné Azure **AzureChinaCloud**.  |

7. Ve stejné relaci prostředí PowerShell spusťte rutinu **set-AzsRegistration** . PowerShell ke spuštění:  

   ```powershell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel PayAsYouUse `
      -RegistrationName $RegistrationName
   ```
   Další informace o rutině Set-AzsRegistration najdete v referenčních informacích k [registraci](#registration-reference).

   Proces trvá od 10 do 15 minut. Až se příkaz dokončí, zobrazí se zpráva **"vaše prostředí je teď zaregistrované a aktivované pomocí zadaných parametrů."**

## <a name="register-connected-with-capacity-billing"></a>Zaregistrovat se k fakturaci kapacity

Pomocí těchto kroků zaregistrujete Azure Stack s Azure s využitím modelu fakturace s průběžnými platbami podle aktuálního využití.

> [!Note]  
> Všechny tyto kroky je nutné spustit z počítače, který má přístup k privilegovanému koncovému bodu (PEP). Podrobnosti o PEP najdete v tématu [použití privilegovaného koncového bodu v Azure Stack](azure-stack-privileged-endpoint.md).

Připojená prostředí mají přístup k Internetu a k Azure. V těchto prostředích je potřeba zaregistrovat poskytovatele prostředků Azure Stack s Azure a potom nakonfigurovat model fakturace.

1. Pokud chcete zaregistrovat poskytovatele prostředků Azure Stack v Azure, spusťte PowerShell ISE jako správce a použijte následující rutiny PowerShellu s parametrem **Environment** nastaveným na příslušný typ předplatného Azure (viz níže uvedené parametry).

2. Přidejte účet Azure, který používáte k registraci Azure Stack. Pokud chcete účet přidat, spusťte rutinu **Add-AzureRmAccount** . Zobrazí se výzva k zadání přihlašovacích údajů k účtu Azure a možná budete muset použít dvojúrovňové ověřování na základě konfigurace vašeho účtu.

   ```powershell  
   Connect-AzureRmAccount -Environment "<environment name>"
   ```

   | Parametr | Popis |  
   |-----|-----|
   | EnvironmentName | Název prostředí Azure Cloud Subscription. Podporované názvy prostředí jsou **AzureCloud**, **AzureUSGovernment**, nebo pokud používáte čínské předplatné Azure **AzureChinaCloud**.  |

3. Máte-li více předplatných, spusťte následující příkaz a vyberte ten, který chcete použít:  

   ```powershell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. Spuštěním následujícího příkazu zaregistrujte poskytovatele prostředků Azure Stack v předplatném Azure:

   ```powershell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Spusťte PowerShell ISE jako správce a přejděte do **registrační** složky v adresáři **AzureStack-Tools-Master** , který jste vytvořili při stahování nástrojů Azure Stack. Importujte modul **RegisterWithAzure. psm1** pomocí prostředí PowerShell:

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
   > Pomocí parametru UsageReportingEnabled pro rutinu **set-AzsRegistration** můžete zakázat funkci generování sestav využití nastavením parametru na hodnotu false. 
   
   Další informace o rutině Set-AzsRegistration najdete v referenčních informacích k [registraci](#registration-reference).

## <a name="register-disconnected-with-capacity-billing"></a>Registrovat odpojeno pomocí fakturace kapacity

Pokud zaregistrujete Azure Stack v odpojeném prostředí (bez připojení k Internetu), musíte získat registrační token z prostředí Azure Stack a pak tento token použít na počítači, který se může připojit k Azure a má PowerShell pro Azure Stack instalovat.  

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Získání registračního tokenu z Azure Stackho prostředí

1. Spusťte PowerShell ISE jako správce a přejděte do **registrační** složky v adresáři **AzureStack-Tools-Master** , který jste vytvořili při stahování nástrojů Azure Stack. Importujte modul **RegisterWithAzure. psm1** :  

   ```powershell  
   Import-Module .\RegisterWithAzure.psm1
   ```

2. Pokud chcete získat registrační token, spusťte následující rutiny PowerShellu:  

   ```Powershell
   $FilePathForRegistrationToken = "$env:SystemDrive\RegistrationToken.txt"
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $YourCloudAdminCredential -UsageReportingEnabled:$False -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```
   Další informace o rutině Get-AzsRegistrationToken najdete v referenčních informacích k [registraci](#registration-reference).

   > [!Tip]  
   > Registrační token je uložený v souboru určeném pro *$FilePathForRegistrationToken*. Můžete změnit cestu k souboru FilePath nebo filename podle vašeho uvážení.

3. Uložte tento registrační token, který se použije na počítači připojeném k Azure. Můžete zkopírovat soubor nebo text z $FilePathForRegistrationToken.

### <a name="connect-to-azure-and-register"></a>Připojení k Azure a registrace

V počítači, který je připojený k Internetu, proveďte stejný postup, abyste importovali modul RegisterWithAzure. psm1 a přihlásili se ke správnému kontextu Azure PowerShellu. Pak zavolejte Register-AzsEnvironment. Zadejte registrační token, který se má zaregistrovat v Azure. Pokud registrujete více než jednu instanci Azure Stack pomocí stejného ID předplatného Azure, zadejte jedinečný název registrace.

Budete potřebovat registrační token a jedinečný název tokenu.

1. Spusťte PowerShell ISE jako správce a přejděte do **registrační** složky v adresáři **AzureStack-Tools-Master** , který jste vytvořili při stahování nástrojů Azure Stack. Importujte modul **RegisterWithAzure. psm1** :  

   ```powershell  
   Import-Module .\RegisterWithAzure.psm1
   ```

2. Pak spusťte následující rutiny PowerShellu:  

  ```powershell  
  $RegistrationToken = "<Your Registration Token>"
  $RegistrationName = "<unique-registration-name>"
  Register-AzsEnvironment -RegistrationToken $RegistrationToken -RegistrationName $RegistrationName
  ```

Volitelně můžete použít rutinu Get-Content k ukázání na soubor, který obsahuje registrační token.

Budete potřebovat registrační token a jedinečný název tokenu.

1. Spusťte PowerShell ISE jako správce a přejděte do **registrační** složky v adresáři **AzureStack-Tools-Master** , který jste vytvořili při stahování nástrojů Azure Stack. Importujte modul **RegisterWithAzure. psm1** :  

  ```powershell  
  Import-Module .\RegisterWithAzure.psm1
  ```

2. Pak spusťte následující rutiny PowerShellu:  

  ```powershell  
  $RegistrationToken = Get-Content -Path '<Path>\<Registration Token File>'
  Register-AzsEnvironment -RegistrationToken $RegistrationToken -RegistrationName $RegistrationName
  ```

  > [!Note]  
  > Uložte název prostředku registrace a registrační token pro budoucí referenci.

### <a name="retrieve-an-activation-key-from-azure-registration-resource"></a>Načtení aktivačního klíče z registračního prostředku Azure

Dále je potřeba načíst aktivační klíč z registračního prostředku vytvořeného v Azure během Register-AzsEnvironment.

Aktivační klíč získáte spuštěním následujících rutin PowerShellu:  

  ```Powershell
  $RegistrationResourceName = "<unique-registration-name>"
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName -KeyOutputFilePath $KeyOutputFilePath
  ```

  > [!Tip]  
  > Aktivační klíč je uložený v souboru určeném pro *$KeyOutputFilePath*. Můžete změnit cestu k souboru FilePath nebo filename podle vašeho uvážení.

### <a name="create-an-activation-resource-in-azure-stack"></a>Vytvoření prostředku aktivace v Azure Stack

Vraťte se do prostředí Azure Stack se souborem nebo textem z aktivačního klíče vytvořeným z get-AzsActivationKey. V dalším kroku vytvoříte prostředek aktivace v Azure Stack pomocí tohoto aktivačního klíče. Pokud chcete vytvořit prostředek aktivace, spusťte následující rutiny PowerShellu: 

  ```Powershell
  $ActivationKey = "<activation key>"
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```

Volitelně můžete pomocí rutiny Get-Content odkazovat na soubor, který obsahuje registrační token:

  ```Powershell
  $ActivationKey = Get-Content -Path '<Path>\<Activation Key File>'
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```

## <a name="verify-azure-stack-registration"></a>Ověřit registraci Azure Stack

Pomocí dlaždice **Správa oblastí** můžete ověřit, že byla registrace Azure Stack úspěšná. Tato dlaždice je k dispozici na výchozím řídicím panelu na portálu pro správu. Stav lze zaregistrovat nebo není zaregistrován. Pokud je zaregistrované, zobrazí se také ID předplatného Azure, které jste použili k registraci svého Azure Stack spolu se skupinou prostředků registrace a názvem.

1. Přihlaste se k [portálu pro správu Azure Stack](https://adminportal.local.azurestack.external).

2. Z řídicího panelu vyberte **Správa oblastí**.

3. Vyberte **vlastnosti**. Toto okno zobrazuje stav a podrobnosti vašeho prostředí. Stav lze **zaregistrovat**, **není**zaregistrováno nebo **vypršela jeho platnost**.

    [![Dlaždice správy oblastí](media/azure-stack-registration/admin1sm.png "Dlaždice správy oblastí")](media/azure-stack-registration/admin1.png#lightbox)

    Pokud jsou zaregistrované, zahrnují tyto vlastnosti:
    
    - **ID odběru registrace**: ID předplatného Azure, které je zaregistrované a přidružené k Azure Stack
    - **Skupina prostředků registrace**: Skupina prostředků Azure v souvisejícím předplatném, které obsahuje prostředky Azure Stack.

4. K zobrazení registrací aplikací Azure Stack použijte Azure Portal. Přihlaste se k Azure Portal pomocí účtu přidruženého k předplatnému, které jste použili k registraci Azure Stack. Přepněte na tenanta přidruženého k Azure Stack.
5. Přejděte na **Azure Active Directory > Registrace aplikací > zobrazení všech aplikací**.

    ![Registrace aplikací](media/azure-stack-registration/app-registrations.png)

    Registrace aplikací Azure Stack jsou s předponou **Azure Stack**.

Případně můžete pomocí funkce správy Marketplace ověřit, jestli byla registrace úspěšná. Pokud se v okně Správa Marketplace zobrazí seznam položek Marketplace, vaše Registrace proběhla úspěšně. V odpojených prostředích ale nebudete moct zobrazit položky Marketplace ve správě Marketplace.

> [!NOTE]
> Po dokončení registrace se už nebude zobrazovat aktivní upozornění pro neregistraci. V případě odpojených scénářů se ve správě Marketplace zobrazí zpráva s výzvou k registraci a aktivaci Azure Stack, a to i v případě, že jste úspěšně zaregistrovali.

## <a name="renew-or-change-registration"></a>Obnovit nebo změnit registraci

### <a name="renew-or-change-registration-in-connected-environments"></a>Obnovení nebo změna registrace v připojených prostředích

Registraci budete muset aktualizovat nebo obnovit v následujících případech:

- Po obnovení ročního předplatného založeného na kapacitě.
- Když změníte model fakturace.
- Při škálování změn (přidávání nebo odebírání uzlů) pro účely fakturace na základě kapacity.

#### <a name="change-the-subscription-you-use"></a>Změna předplatného, které používáte

Pokud chcete změnit předplatné, které používáte, musíte nejdřív spustit rutinu **Remove-AzsRegistration** , ověřit, že jste přihlášeni ke správnému kontextu Azure PowerShell a nakonec spustit rutinu **set-AzsRegistration** s případnými změněnými parametry. zahrnutí `<billing model>`:

  ```powershell  
  Remove-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -RegistrationName $RegistrationName
  Set-AzureRmContext -SubscriptionId $NewSubscriptionId
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel <billing model> -RegistrationName $RegistrationName
  ```

#### <a name="change-the-billing-model-or-how-to-offer-features"></a>Změna modelu fakturace nebo nabízení funkcí

Pokud chcete změnit model fakturace nebo jak nabízet funkce pro vaši instalaci, můžete zavolat funkci registrace a nastavit nové hodnoty. Nemusíte nejdřív odebrat aktuální registraci:

  ```powershell  
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel <billing model> -RegistrationName $RegistrationName
  ```

### <a name="renew-or-change-registration-in-disconnected-environments"></a>Obnovení nebo změna registrace v odpojených prostředích

Registraci budete muset aktualizovat nebo obnovit v následujících případech:

- Po obnovení ročního předplatného založeného na kapacitě.
- Když změníte model fakturace.
- Při škálování změn (přidávání nebo odebírání uzlů) pro účely fakturace na základě kapacity.

#### <a name="remove-the-activation-resource-from-azure-stack"></a>Odebrat prostředek aktivace z Azure Stack

Nejdřív je potřeba odebrat prostředek aktivace z Azure Stack a pak prostředek registrace v Azure.  

Pokud chcete odebrat prostředek aktivace v Azure Stack, spusťte ve svém Azure Stack prostředí následující rutiny PowerShellu:  

  ```Powershell
  Remove-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  ```

Chcete-li odebrat registrační prostředek v Azure, ujistěte se, že jste na počítači připojeném k Azure, přihlaste se ke správnému kontextu Azure PowerShell a spusťte příslušné rutiny prostředí PowerShell, jak je popsáno níže.

Můžete použít registrační token použitý k vytvoření prostředku:  

  ```Powershell
  $RegistrationToken = "<registration token>"
  Unregister-AzsEnvironment -RegistrationToken $RegistrationToken
  ```

Můžete také použít název registrace:

  ```Powershell
  $RegistrationName = "AzureStack-<unique-registration-name>"
  Unregister-AzsEnvironment -RegistrationName $RegistrationName
  ```

### <a name="re-register-using-disconnected-steps"></a>Znovu zaregistrovat pomocí odpojených kroků

Nyní jste zcela zrušili registraci v odpojeném scénáři a kroky pro registraci Azure Stackho prostředí v odpojeném scénáři se musí zopakovat.

### <a name="disable-or-enable-usage-reporting"></a>Zakázat nebo povolit vytváření sestav využití

U Azure Stack prostředí, která používají model fakturace kapacity, vypněte vytváření sestav využití s parametrem **UsageReportingEnabled** pomocí rutiny **set-AzsRegistration** nebo **Get-AzsRegistrationToken** . Ve výchozím nastavení Azure Stack sestavy metriky využití. Používání funkce vytváření sestav vyžaduje, aby operátoři s využitím kapacity nebo podpora odpojeného prostředí vypnuli.

#### <a name="with-a-connected-azure-stack"></a>S připojeným Azure Stack

   ```powershell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel Capacity
      -RegistrationName $RegistrationName
   ```

#### <a name="with-a-disconnected-azure-stack"></a>S odpojeným Azure Stack

1. Pokud chcete změnit registrační token, spusťte následující rutiny PowerShellu:  

   ```Powershell
   $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential -UsageReportingEnabled:$False
   $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```

   > [!Tip]  
   > Registrační token je uložený v souboru určeném pro *$FilePathForRegistrationToken*. Můžete změnit cestu k souboru FilePath nebo filename podle vašeho uvážení.

2. Uložte tento registrační token, který se použije na počítači připojeném k Azure. Můžete zkopírovat soubor nebo text z $FilePathForRegistrationToken.

## <a name="move-a-registration-resource"></a>Přesunutí prostředku registrace
Přesunutí prostředku registrace mezi skupinami prostředků v rámci stejného předplatného **je** podporováno pro všechna prostředí. Přesun registračního prostředku mezi předplatnými se ale podporuje jenom pro zprostředkovatele CSP, pokud oba odběry řeší stejné ID partnera. Další informace o přesouvání prostředků do nové skupiny prostředků najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources)předplatného.

## <a name="registration-reference"></a>Registrační odkaz

### <a name="set-azsregistration"></a>Set-AzsRegistration

Pomocí Set-AzsRegistration můžete zaregistrovat Azure Stack s Azure a povolit nebo zakázat nabídku položek na webu Marketplace a vytváření sestav o využití.

K provedení rutiny potřebujete:
- Globální předplatné Azure libovolného typu.
- Musíte být také přihlášení k Azure PowerShell s účtem, který je vlastníkem nebo přispěvatelem daného předplatného.

```powershell
Set-AzsRegistration [-PrivilegedEndpointCredential] <PSCredential> [-PrivilegedEndpoint] <String> [[-AzureContext]
    <PSObject>] [[-ResourceGroupName] <String>] [[-ResourceGroupLocation] <String>] [[-BillingModel] <String>]
    [-MarketplaceSyndicationEnabled] [-UsageReportingEnabled] [[-AgreementNumber] <String>] [[-RegistrationName]
    <String>] [<CommonParameters>]
```

| Parametr | type | Popis |
|-------------------------------|--------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| PrivilegedEndpointCredential | PSCredential | Přihlašovací údaje použité pro [přístup k privilegovanému koncovému bodu](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). Uživatelské jméno má formát **AzureStackDomain\CloudAdmin**. |
| PrivilegedEndpoint | Řetězec | Předem nakonfigurovaná Vzdálená konzola PowerShellu, která poskytuje možnosti, jako je shromažďování protokolů a další úkoly po nasazení. Další informace najdete v článku [použití privilegovaného koncového bodu](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint) . |
| AzureContext | PSObject |  |
| ResourceGroupName | Řetězec |  |
| ResourceGroupLocation | Řetězec |  |
| BillingModel | Řetězec | Model fakturace, který používá vaše předplatné. Povolené hodnoty pro tento parametr jsou: Kapacita, PayAsYouUse a vývoj. |
| MarketplaceSyndicationEnabled | True nebo False | Určuje, jestli je na portálu dostupná funkce správy Marketplace. Nastavte na hodnotu true, pokud se registruje s připojením k Internetu. Nastavte na hodnotu false, pokud se registruje v odpojených prostředích. U odpojených registrací se dá [Nástroj pro offline syndikaci](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario) použít ke stažení položek Marketplace. |
| UsageReportingEnabled | True nebo False | Ve výchozím nastavení Azure Stack sestavy metriky využití. Používání funkce vytváření sestav vyžaduje, aby operátoři s využitím kapacity nebo podpora odpojeného prostředí vypnuli. Povolené hodnoty pro tento parametr jsou: True, false. |
| AgreementNumber | Řetězec |  |
| registrace | Řetězec | Pokud používáte registrační skript ve více než jedné instanci Azure Stack s použitím stejného ID předplatného Azure, nastavte pro registraci jedinečný název. Parametr má výchozí hodnotu **AzureStackRegistration**. Pokud však použijete stejný název na více než jedné instanci Azure Stack, skript se nezdařil. |

### <a name="get-azsregistrationtoken"></a>Get-AzsRegistrationToken

Get-AzsRegistrationToken vygeneruje registrační token ze vstupních parametrů.

```powershell  
Get-AzsRegistrationToken [-PrivilegedEndpointCredential] <PSCredential> [-PrivilegedEndpoint] <String>
    [-BillingModel] <String> [[-TokenOutputFilePath] <String>] [-UsageReportingEnabled] [[-AgreementNumber] <String>]
    [<CommonParameters>]
```
| Parametr | type | Popis |
|-------------------------------|--------------|-------------|
| PrivilegedEndpointCredential | PSCredential | Přihlašovací údaje použité pro [přístup k privilegovanému koncovému bodu](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). Uživatelské jméno má formát **AzureStackDomain\CloudAdmin**. |
| PrivilegedEndpoint | Řetězec |  Předem nakonfigurovaná Vzdálená konzola PowerShellu, která poskytuje možnosti, jako je shromažďování protokolů a další úkoly po nasazení. Další informace najdete v článku [použití privilegovaného koncového bodu](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint) . |
| AzureContext | PSObject |  |
| ResourceGroupName | Řetězec |  |
| ResourceGroupLocation | Řetězec |  |
| BillingModel | Řetězec | Model fakturace, který používá vaše předplatné. Povolené hodnoty pro tento parametr jsou: Kapacita, PayAsYouUse a vývoj. |
| MarketplaceSyndicationEnabled | True nebo False |  |
| UsageReportingEnabled | True nebo False | Ve výchozím nastavení Azure Stack sestavy metriky využití. Používání funkce vytváření sestav vyžaduje, aby operátoři s využitím kapacity nebo podpora odpojeného prostředí vypnuli. Povolené hodnoty pro tento parametr jsou: True, false. |
| AgreementNumber | Řetězec |  |

## <a name="registration-failures"></a>Selhání registrace

Při pokusu o registraci vašeho Azure Stack se může zobrazit jedna z následujících chyb:
1. Nepovedlo se načíst povinné informace o hardwaru pro $hostName. Zkontrolujte fyzického hostitele a připojení a pak zkuste znovu spustit registraci.

2. Nejde se připojit k $hostName získat informace o hardwaru – zkontrolujte prosím fyzického hostitele a připojení a zkuste znovu spustit registraci.

> Příčina: obvykle se snažíme z hostitelů získat podrobnosti o hardwaru, jako je UUID, BIOS a CPU, aby se pokusily o aktivaci a nebylo možné z důvodu neschopnosti připojit se k fyzickému hostiteli.

Při pokusu o přístup ke správě Marketplace dojde k chybě při pokusu o zasyndikátování produktů. 
> Příčina: k tomu obvykle dochází, když Azure Stack nedokáže získat přístup k registračnímu prostředku. Jedním z běžných důvodů je, že když se tenant adresáře pro předplatné Azure změní, resetuje registraci. Pokud jste změnili tenanta adresáře pro předplatné, nemůžete získat přístup k Azure Stack Marketplace nebo sestavě. Chcete-li tento problém vyřešit, je nutné provést novou registraci.

Správa Marketplace se stále zeptá, abyste zaregistrovali a aktivovali Azure Stack, i když jste razítko už zaregistrovali pomocí odpojeného procesu. 
> Příčina: Jedná se o známý problém pro odpojená prostředí. Stav registrace můžete ověřit pomocí následujících [kroků](azure-stack-registration.md#verify-azure-stack-registration). Aby bylo možné používat správu na webu Marketplace, budete muset použít [offline nástroj](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario). 

## <a name="next-steps"></a>Další postup

[Stažení položek z Marketplace z Azure](azure-stack-download-azure-marketplace-item.md)
