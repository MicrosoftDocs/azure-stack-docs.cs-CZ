---
title: Registrace centra Azure Stack s Azure
titleSuffix: Azure Stack Hub
description: Naučte se registrovat integrované systémy Azure Stack hub pomocí Azure, abyste si mohli stáhnout Azure Marketplace položky a nastavit vytváření sestav dat.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 04/06/2020
ms.author: inhenkel
ms.reviewer: avishwan
ms.lastreviewed: 03/04/2019
zone_pivot_groups: state-connected-disconnected
ms.openlocfilehash: e8a8d2f156d2608db01a652225540a73722f16fc
ms.sourcegitcommit: c263a86d371192e8ef2b80ced2ee0a791398cfb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2020
ms.locfileid: "82848213"
---
# <a name="register-azure-stack-hub-with-azure"></a>Registrace centra Azure Stack s Azure

Zaregistrujte Azure Stack hub pomocí Azure, abyste si mohli stáhnout Azure Marketplace položky z Azure a nastavovat sestavy pro Commerce data zpět společnosti Microsoft. Po registraci centra Azure Stack se využívání oznamuje službě Azure Commerce a v rámci ID předplatného Azure, které se používá k registraci, se vám zobrazí.

Informace v tomto článku popisují registraci Azure Stack integrovaných systémů centra s Azure. Informace o registraci ASDK s Azure najdete v tématu [registrace centra Azure Stack](../asdk/asdk-register.md) v dokumentaci k ASDK.

> [!IMPORTANT]  
> K podpoře kompletních funkcí centra Azure Stack, včetně položek nabídky na webu Marketplace, se vyžaduje registrace. Pokud se nezaregistrujete při použití modelu fakturace s průběžnými platbami, budete mít porušení licenčních podmínek centra Azure Stack. Další informace o modelech licencování centra Azure Stack najdete na [stránce Jak koupit](https://azure.microsoft.com/overview/azure-stack/how-to-buy/).

## <a name="prerequisites"></a>Požadavky

Před registrací proveďte následující předpoklady:

- Ověřte přihlašovací údaje.
- Nastavte režim jazyka PowerShell.
- Nainstalujte PowerShell pro centrum Azure Stack.
- Stáhněte si nástroje Azure Stack hub.
- Určete model fakturace.
- Určete jedinečný název registrace.

### <a name="verify-your-credentials"></a>Ověření přihlašovacích údajů

Před registrací centra Azure Stack s Azure musíte mít:

- ID předplatného pro předplatné Azure. Pro registraci jsou podporovány pouze odběry služeb EA, CSP nebo CSP sdílené služby. CSP musí rozhodnout, jestli se má [použít předplatné CSP nebo APSS](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription).<br><br>ID získáte tak, že se přihlásíte k Azure a kliknete na **všechny služby**. Pak v kategorii **Obecné** vyberte **předplatná**, klikněte na předplatné, které chcete použít, a v části **Essentials** můžete najít ID předplatného. Jako osvědčený postup použijte samostatné odběry pro produkční a vývojové nebo testovací prostředí. 

  > [!Note]  
  > V současné době se nepodporují předplatné pro Německo Cloud.

- Uživatelské jméno a heslo pro účet, který je vlastníkem předplatného.

- Uživatelský účet musí mít přístup k předplatnému Azure a mít oprávnění k vytváření aplikací identity a instančních objektů v adresáři přidruženém k tomuto předplatnému. K registraci centra Azure Stack v Azure doporučujeme použít správu s minimálními oprávněními. Další informace o tom, jak vytvořit definici vlastní role, která omezuje přístup k vašemu předplatnému k registraci, najdete v tématu [Vytvoření registrační role pro centrum Azure Stack](azure-stack-registration-role.md).

- Zaregistrovali jste poskytovatele prostředků centra Azure Stack (podrobnosti najdete v části věnované poskytovateli prostředků centra Azure Stack registru).

Po registraci se oprávnění globálního správce Azure Active Directory (Azure AD) nevyžadují. Některé operace ale můžou vyžadovat přihlašovací údaje globálního správce (například skript instalačního programu poskytovatele prostředků nebo novou funkci, která vyžaduje udělení oprávnění). Můžete buď dočasně obnovit oprávnění globálního správce účtu, nebo použít samostatný účet globálního správce, který je vlastníkem *výchozího předplatného poskytovatele*.

Uživatel, který registruje centrum Azure Stack, je vlastníkem instančního objektu ve službě Azure AD. Registraci centra Azure Stack může změnit jenom uživatel, který zaregistroval centrum Azure Stack. Pokud se uživatel bez role správce, který není vlastníkem instančního objektu služby, pokusí zaregistrovat nebo znovu zaregistrovat Azure Stack centra, může docházet přes odpověď 403. Odpověď 403 indikuje, že uživatel nemá dostatečná oprávnění k dokončení operace.

Pokud nemáte předplatné Azure, které tyto požadavky splňuje, můžete si [tady vytvořit bezplatný účet Azure](https://azure.microsoft.com/free/?b=17.06). Při registraci centra Azure Stack se neúčtují žádné náklady na vaše předplatné Azure.

> [!NOTE]
> Pokud máte více než jedno centrum Azure Stack, je osvědčeným postupem, jak každé centrum Azure Stack registrovat ke svému vlastnímu předplatnému. Díky tomu je snazší sledovat využití.

### <a name="set-the-powershell-language-mode"></a>Nastavení režimu jazyka PowerShell

Pro úspěšné registraci centra Azure Stack musí být režim jazyka PowerShell nastavený na **FullLanguageMode**.  Pokud chcete ověřit, jestli je aktuální jazykový režim nastavený na Full, otevřete okno PowerShellu se zvýšenými oprávněními a spusťte následující rutiny PowerShellu:

```powershell  
$ExecutionContext.SessionState.LanguageMode
```

Ujistěte se, že výstup vrátí **FullLanguageMode**. Pokud se vrátí jiný jazykový režim, musí být registrace spuštěná na jiném počítači nebo musí být nastavená na **FullLanguageMode** , aby bylo možné pokračovat.

### <a name="install-powershell-for-azure-stack-hub"></a>Instalace PowerShellu pro centrum Azure Stack

K registraci v Azure použijte nejnovější prostředí PowerShell pro Azure Stack hub.

Pokud není nejnovější verze ještě nainstalovaná, přečtěte si téma [instalace PowerShellu pro Azure Stack hub](azure-stack-powershell-install.md).

### <a name="download-the-azure-stack-hub-tools"></a>Stažení nástrojů centra Azure Stack

Úložiště GitHub nástrojů Azure Stack hub obsahuje moduly PowerShellu, které podporují funkce centra Azure Stack, včetně funkcí registrace. Během procesu registrace je potřeba importovat a používat modul **RegisterWithAzure. psm1** PowerShell (najdete ho v úložišti nástrojů centra Azure Stack) a zaregistrovat svou instanci služby Azure Stack hub v Azure.

Abyste měli jistotu, že používáte nejnovější verzi, odstraňte všechny existující verze nástrojů centra Azure Stack a Stáhněte si [nejnovější verzi z GitHubu](azure-stack-powershell-download.md) před registrací v Azure.

### <a name="determine-your-billing-model"></a>Určení modelu fakturace
::: zone pivot="state-connected"
 Připojené nasazení umožňuje rozbočovači Azure Stack připojit se k Internetu a k Azure. Jako úložiště identit můžete také použít službu Azure AD nebo Active Directory Federation Services (AD FS) (AD FS) a vybírat ze dvou modelů fakturace: průběžné platby nebo využití kapacity. Účtovací model se určí později při spuštění registračního skriptu.
::: zone-end

::: zone pivot="state-disconnected"
 Odpojené nasazení umožňuje používat centrum Azure Stack bez připojení k Internetu. V případě odpojeného nasazení budete omezeni na AD FS úložiště identit a fakturační model založený na kapacitě. Účtovací model se určí později při spuštění registračního skriptu.
::: zone-end

### <a name="determine-your-unique-registration-name"></a>Určení jedinečného názvu registrace

Po spuštění registračního skriptu je nutné zadat jedinečný název registrace. Snadný způsob, jak přidružit předplatné centra Azure Stack k registraci Azure, je použití **ID cloudu**Azure Stack hub.

> [!NOTE]
> Registrace centra Azure Stack pomocí fakturačního modelu založeného na kapacitě bude muset změnit jedinečný název při opětovné registraci po vypršení platnosti tohoto ročního předplatného, pokud [neodstraníte registraci s vypršenou platností](#renew-or-change-registration) a znovu zaregistrujete do Azure.

Pokud chcete zjistit ID cloudu pro nasazení centra Azure Stack, otevřete PowerShell jako správce v počítači, který má přístup k privilegovanému koncovému bodu, spusťte následující příkazy a pak zaznamenejte hodnotu **CloudID** :

```powershell
Run: Enter-PSSession -ComputerName <privileged endpoint computer name> -ConfigurationName PrivilegedEndpoint
Run: Get-AzureStackStampInformation
```

::: zone pivot="state-connected"
## <a name="register-with-pay-as-you-use-billing"></a>Registrace pomocí fakturace s průběžnými platbami

Pomocí těchto kroků zaregistrujete Azure Stack centrum s Azure s využitím modelu fakturace s průběžnými platbami podle aktuálního využití.

> [!Note]  
> Všechny tyto kroky je nutné spustit z počítače, který má přístup k privilegovanému koncovému bodu (PEP). Podrobnosti o PEP najdete v tématu [použití privilegovaného koncového bodu v centru Azure Stack](azure-stack-privileged-endpoint.md).

Připojená prostředí mají přístup k Internetu a k Azure. V těchto prostředích je potřeba zaregistrovat poskytovatele prostředků centra Azure Stack v Azure a potom nakonfigurovat model fakturace.

1. Pokud chcete zaregistrovat poskytovatele prostředků centra Azure Stack v Azure, spusťte PowerShell ISE jako správce a použijte následující rutiny PowerShellu s parametrem **Environment** nastaveným na příslušný typ předplatného Azure (viz níže uvedené parametry).

2. Přidejte účet Azure, který jste použili k registraci centra Azure Stack. Pokud chcete účet přidat, spusťte rutinu **Add-AzureRmAccount** . Zobrazí se výzva k zadání přihlašovacích údajů k účtu Azure a možná budete muset použít dvojúrovňové ověřování na základě konfigurace vašeho účtu.

   ```powershell
   Add-AzureRmAccount -EnvironmentName "<environment name>"
   ```

   | Parametr | Popis |  
   |-----|-----|
   | EnvironmentName | Název prostředí Azure Cloud Subscription. Podporované názvy prostředí jsou **AzureCloud**, **AzureUSGovernment**, nebo pokud používáte čínské předplatné Azure **AzureChinaCloud**.  |

   >[!Note]
   > Pokud vaše relace vyprší, vaše heslo se změnilo nebo chcete jednoduše přepnout účty, spusťte následující rutinu ještě před přihlášením pomocí rutiny Add-AzureRmAccount:`Remove-AzureRmAccount-Scope Process`

3. Máte-li více předplatných, spusťte následující příkaz a vyberte ten, který chcete použít:  

   ```powershell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

4. Spuštěním následujícího příkazu zaregistrujte poskytovatele prostředků centra Azure Stack ve vašem předplatném Azure:

   ```powershell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Spusťte PowerShell ISE jako správce a přejděte do **registrační** složky v adresáři **AzureStack-Tools-Master** , který jste vytvořili při stahování nástrojů centra Azure Stack. Importujte modul **RegisterWithAzure. psm1** pomocí prostředí PowerShell:

   ```powershell  
   Import-Module .\RegisterWithAzure.psm1
   ```

6. Potom ve stejné relaci prostředí PowerShell se ujistěte, že jste přihlášeni ke správnému kontextu Azure PowerShell. Tento kontext by představoval účet Azure, který byl dříve použit k registraci poskytovatele prostředků služby Azure Stack hub. PowerShell ke spuštění:

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
   Další informace o rutině Set-AzsRegistration najdete v [referenčních](#registration-reference)informacích k registraci.

   Proces trvá od 10 do 15 minut. Až se příkaz dokončí, zobrazí se zpráva **"vaše prostředí je teď zaregistrované a aktivované pomocí zadaných parametrů."**

## <a name="register-with-capacity-billing"></a>Registrace s fakturací kapacity

Pomocí těchto kroků zaregistrujete Azure Stack centrum s Azure pomocí modelu fakturace kapacity.

> [!Note]  
> Všechny tyto kroky je nutné spustit z počítače, který má přístup k privilegovanému koncovému bodu (PEP). Podrobnosti o PEP najdete v tématu [použití privilegovaného koncového bodu v centru Azure Stack](azure-stack-privileged-endpoint.md).

Připojená prostředí mají přístup k Internetu a k Azure. V těchto prostředích je potřeba zaregistrovat poskytovatele prostředků centra Azure Stack v Azure a potom nakonfigurovat model fakturace.

1. Pokud chcete zaregistrovat poskytovatele prostředků centra Azure Stack v Azure, spusťte PowerShell ISE jako správce a použijte následující rutiny PowerShellu s parametrem **Environment** nastaveným na příslušný typ předplatného Azure (viz níže uvedené parametry).

2. Přidejte účet Azure, který jste použili k registraci centra Azure Stack. Pokud chcete účet přidat, spusťte rutinu **Add-AzureRmAccount** . Zobrazí se výzva k zadání přihlašovacích údajů k účtu Azure a možná budete muset použít dvojúrovňové ověřování na základě konfigurace vašeho účtu.

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

4. Spuštěním následujícího příkazu zaregistrujte poskytovatele prostředků centra Azure Stack ve vašem předplatném Azure:

   ```powershell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Spusťte PowerShell ISE jako správce a přejděte do **registrační** složky v adresáři **AzureStack-Tools-Master** , který jste vytvořili při stahování nástrojů centra Azure Stack. Importujte modul **RegisterWithAzure. psm1** pomocí prostředí PowerShell:

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
   
   Další informace o rutině Set-AzsRegistration najdete v [referenčních](#registration-reference)informacích k registraci.
::: zone-end

::: zone pivot="state-disconnected"
## <a name="register-with-capacity-billing"></a>Registrace s fakturací kapacity

Pokud registrujete Azure Stack hub v odpojeném prostředí (bez připojení k Internetu), musíte získat registrační token z prostředí služby Azure Stack hub. Pak použijte tento token na počítači, který se může připojit k Azure a má nainstalovaný PowerShell pro Azure Stack hub.  

### <a name="get-a-registration-token-from-the-azure-stack-hub-environment"></a>Získání registračního tokenu z prostředí Azure Stack hub

1. Spusťte PowerShell ISE jako správce a přejděte do **registrační** složky v adresáři **AzureStack-Tools-Master** , který jste vytvořili při stahování nástrojů centra Azure Stack. Importujte modul **RegisterWithAzure. psm1** :  

   ```powershell  
   Import-Module .\RegisterWithAzure.psm1
   ```

2. Pokud chcete získat registrační token, spusťte následující rutiny PowerShellu:  

   ```Powershell
   $FilePathForRegistrationToken = "$env:SystemDrive\RegistrationToken.txt"
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $YourCloudAdminCredential -UsageReportingEnabled:$False -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```
   Další informace o rutině Get-AzsRegistrationToken najdete v [referenčních](#registration-reference)informacích k registraci.

   > [!Tip]  
   > Registrační token je uložený v souboru určeném pro *$FilePathForRegistrationToken*. Můžete změnit cestu k souboru FilePath nebo filename podle vašeho uvážení.

3. Uložte tento registrační token pro použití na počítači připojeném k Azure. Můžete zkopírovat soubor nebo text z *$FilePathForRegistrationToken*.

### <a name="connect-to-azure-and-register"></a>Připojení k Azure a registrace

V počítači, který je připojený k Internetu, proveďte stejný postup, abyste importovali modul RegisterWithAzure. psm1 a přihlásili se ke správnému kontextu Azure PowerShellu. Pak zavolejte Register-AzsEnvironment. Zadejte registrační token, který se má zaregistrovat v Azure. Pokud registrujete více než jednu instanci centra Azure Stack s použitím stejného ID předplatného Azure, zadejte jedinečný název registrace.

Potřebujete svůj registrační token a jedinečný název tokenu.

1. Spusťte PowerShell ISE jako správce a přejděte do **registrační** složky v adresáři **AzureStack-Tools-Master** , který jste vytvořili při stahování nástrojů centra Azure Stack. Importujte modul **RegisterWithAzure. psm1** :  

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

Potřebujete svůj registrační token a jedinečný název tokenu.

1. Spusťte PowerShell ISE jako správce a přejděte do **registrační** složky v adresáři **AzureStack-Tools-Master** , který jste vytvořili při stahování nástrojů centra Azure Stack. Importujte modul **RegisterWithAzure. psm1** :  

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

### <a name="create-an-activation-resource-in-azure-stack-hub"></a>Vytvoření prostředku aktivace v centru Azure Stack

Vraťte se do prostředí Azure Stack hub se souborem nebo textem z aktivačního klíče vytvořeným z get-AzsActivationKey. V dalším kroku vytvořte prostředek aktivace v Azure Stack hub pomocí tohoto aktivačního klíče. Pokud chcete vytvořit prostředek aktivace, spusťte následující rutiny PowerShellu:

  ```Powershell
  $ActivationKey = "<activation key>"
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```

Volitelně můžete pomocí rutiny Get-Content odkazovat na soubor, který obsahuje registrační token:

  ```Powershell
  $ActivationKey = Get-Content -Path '<Path>\<Activation Key File>'
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```
::: zone-end

## <a name="verify-azure-stack-hub-registration"></a>Ověřit registraci centra Azure Stack

Pomocí dlaždice **Správa oblastí** můžete ověřit, zda byla registrace centra Azure Stack úspěšná. Tato dlaždice je k dispozici na výchozím řídicím panelu na portálu pro správu. Stav lze zaregistrovat nebo není zaregistrován. Pokud je zaregistrované, zobrazí se také ID předplatného Azure, které jste použili k registraci centra Azure Stack spolu se skupinou prostředků registrace a názvem.

1. Přihlaste se k [portálu pro správu centra Azure Stack](https://adminportal.local.azurestack.external).

2. Z řídicího panelu vyberte **Správa oblastí**.

3. Vyberte **Vlastnosti**. Toto okno zobrazuje stav a podrobnosti vašeho prostředí. Stav lze **zaregistrovat**, **není zaregistrováno**nebo **vypršela jeho platnost**.

    [![Dlaždice správy oblastí na portálu pro správu centra Azure Stack](media/azure-stack-registration/admin1sm.png "Dlaždice správy oblastí")](media/azure-stack-registration/admin1.png#lightbox)

    Pokud jsou zaregistrované, zahrnují tyto vlastnosti:
    
    - **ID předplatného registrace**: ID předplatného Azure zaregistrované a přidružené k rozbočovači Azure Stack.
    - **Skupina prostředků registrace**: Skupina prostředků Azure v souvisejícím předplatném, které obsahuje prostředky centra Azure Stack.

4. K zobrazení registračních prostředků centra Azure Stack můžete použít Azure Portal a ověřit, zda byla registrace úspěšná. Přihlaste se k [Azure Portal](https://portal.azure.com) pomocí účtu přidruženého k předplatnému, které jste použili k registraci centra Azure Stack. Vyberte **všechny prostředky**, povolte zaškrtávací políčko **Zobrazit skryté typy** a vyberte název registrace.

5. Pokud se registrace nezdařila, je nutné znovu provést registraci pomocí následujících [kroků](#change-the-subscription-you-use) pro vyřešení problému.  

Případně můžete pomocí funkce správy Marketplace ověřit, jestli byla registrace úspěšná. Pokud se v okně Správa Marketplace zobrazí seznam položek Marketplace, vaše Registrace proběhla úspěšně. V odpojených prostředích ale nevidíte položky Marketplace ve správě Marketplace.

> [!NOTE]
> Po dokončení registrace se už nebude zobrazovat aktivní upozornění pro neregistraci. V Azure Stack vydaných verzí centra před 1904 se v odpojených scénářích zobrazí zpráva ve správě Marketplace s výzvou k registraci a aktivaci centra Azure Stack, a to i v případě, že jste se zaregistrovali úspěšně. Tato zpráva se nezobrazuje ve verzi 1904 a novější.

## <a name="renew-or-change-registration"></a>Obnovit nebo změnit registraci

::: zone pivot="state-connected"
Registraci je potřeba aktualizovat nebo obnovit v následujících případech:

- Po obnovení ročního předplatného založeného na kapacitě.
- Když změníte model fakturace.
- Při škálování změn (přidávání nebo odebírání uzlů) pro účely fakturace na základě kapacity.

### <a name="change-the-subscription-you-use"></a>Změna předplatného, které používáte

Pokud chcete změnit předplatné, které používáte, musíte nejdřív spustit rutinu **Remove-AzsRegistration** a pak se ujistit, že jste přihlášeni ke správnému kontextu Azure PowerShell. Pak spusťte rutinu **set-AzsRegistration** se všemi změněnými parametry, včetně `<billing model>`:

  ```powershell  
  Remove-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -RegistrationName $RegistrationName
  Set-AzureRmContext -SubscriptionId $NewSubscriptionId
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel <billing model> -RegistrationName $RegistrationName
  ```

### <a name="change-the-billing-model-or-how-to-offer-features"></a>Změna modelu fakturace nebo nabízení funkcí

Pokud chcete změnit model fakturace nebo jak nabízet funkce pro vaši instalaci, můžete zavolat funkci registrace a nastavit nové hodnoty. Nemusíte nejdřív odebrat aktuální registraci:

  ```powershell  
  Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel <billing model> -RegistrationName $RegistrationName
  ```
::: zone-end

::: zone pivot="state-disconnected"
Registraci je potřeba aktualizovat nebo obnovit v následujících případech:

- Po obnovení ročního předplatného založeného na kapacitě.
- Když změníte model fakturace.
- Při škálování změn (přidávání nebo odebírání uzlů) pro účely fakturace na základě kapacity.

### <a name="remove-the-activation-resource-from-azure-stack-hub"></a>Odebrat prostředek aktivace z centra Azure Stack

Nejdřív je potřeba odebrat prostředek aktivace z centra Azure Stack a pak prostředek registrace v Azure.  

Pokud chcete odebrat prostředek aktivace v centru Azure Stack, spusťte v prostředí Azure Stack hub následující rutiny PowerShellu:  

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

### <a name="re-register-using-connected-steps"></a>Znovu zaregistrovat pomocí připojených kroků

Pokud změníte fakturační model z kapacity fakturace v odpojeném stavu na vyúčtování spotřeby v připojeném stavu, znovu se zaregistrujete podle [kroků připojeného modelu](azure-stack-registration.md?pivots=state-connected#change-the-billing-model-or-how-to-offer-features). 

>[!Note] 
>Nemění se tím model vaší identity, jenom účetní mechanizmus a službu AD FS budete používat jako zdroj identity.

### <a name="re-register-using-disconnected-steps"></a>Znovu zaregistrovat pomocí odpojených kroků

Nyní jste zcela zrušili registraci v odpojeném scénáři a kroky pro registraci Azure Stackho centra prostředí v odpojeném scénáři se musí zopakovat.
::: zone-end

### <a name="disable-or-enable-usage-reporting"></a>Zakázat nebo povolit vytváření sestav využití

U prostředí Azure Stack hub, která používají model fakturace kapacity, vypněte vytváření sestav využití pomocí parametru **UsageReportingEnabled** pomocí rutiny **set-AzsRegistration** nebo **Get-AzsRegistrationToken** . Služba Azure Stack hub hlásí metriky využití ve výchozím nastavení. Obsluha použití kapacity, která využívá nebo podporuje odpojené prostředí, musí vypnout vytváření sestav využití.

::: zone pivot="state-connected"
Spusťte následující rutiny PowerShellu:

   ```powershell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel Capacity
      -RegistrationName $RegistrationName
   ```
::: zone-end
::: zone pivot="state-disconnected"
1. Pokud chcete změnit registrační token, spusťte následující rutiny PowerShellu:  

   ```Powershell
   $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential -UsageReportingEnabled:$False
   $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```

   > [!Tip]  
   > Registrační token je uložený v souboru určeném pro *$FilePathForRegistrationToken*. Můžete změnit cestu k souboru FilePath nebo filename podle vašeho uvážení.

2. Uložte tento registrační token, který se použije na počítači připojeném k Azure. Můžete zkopírovat soubor nebo text z *$FilePathForRegistrationToken*.
::: zone-end

## <a name="move-a-registration-resource"></a>Přesunutí prostředku registrace

Přesunutí prostředku registrace mezi skupinami prostředků v rámci stejného předplatného **je** podporováno pro všechna prostředí. Přesun registračního prostředku mezi předplatnými se ale podporuje jenom pro zprostředkovatele CSP, pokud oba odběry řeší stejné ID partnera. Další informace o přesouvání prostředků do nové skupiny prostředků najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](/azure/azure-resource-manager/resource-group-move-resources).

> [!IMPORTANT]
> Aby se zabránilo nechtěnému odstranění prostředků registrace na portálu, registrační skript automaticky přidá ke zdroji zámek. Před přesunutím nebo odstraněním tohoto zámku je nutné tento zámek odebrat. Doporučuje se přidat zámek k registračnímu prostředku, abyste zabránili nechtěnému odstranění.

## <a name="registration-reference"></a>Registrační odkaz

### <a name="set-azsregistration"></a>Set-AzsRegistration

Můžete použít **set-AzsRegistration** k registraci centra Azure Stack s Azure a povolit nebo zakázat nabídku položek na webu Marketplace a vytváření sestav o využití.

K provedení rutiny potřebujete:

- Globální předplatné Azure libovolného typu.
- Chcete být přihlášeni k Azure PowerShell pomocí účtu, který je vlastníkem nebo přispěvatelem daného předplatného.

```powershell
Set-AzsRegistration [-PrivilegedEndpointCredential] <PSCredential> [-PrivilegedEndpoint] <String> [[-AzureContext]
    <PSObject>] [[-ResourceGroupName] <String>] [[-ResourceGroupLocation] <String>] [[-BillingModel] <String>]
    [-MarketplaceSyndicationEnabled] [-UsageReportingEnabled] [[-AgreementNumber] <String>] [[-RegistrationName]
    <String>] [<CommonParameters>]
```

| Parametr | Typ | Popis |
|-------------------------------|--------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| PrivilegedEndpointCredential | PSCredential | Přihlašovací údaje použité pro [přístup k privilegovanému koncovému bodu](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). Uživatelské jméno má formát **AzureStackDomain\CloudAdmin**. |
| PrivilegedEndpoint | Řetězec | Předem nakonfigurovaná Vzdálená konzola PowerShellu, která poskytuje možnosti, jako je shromažďování protokolů a další úkoly po nasazení. Další informace najdete v článku [použití privilegovaného koncového bodu](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint) . |
| AzureContext | PSObject |  |
| ResourceGroupName | Řetězec |  |
| ResourceGroupLocation | Řetězec |  |
| BillingModel | Řetězec | Model fakturace, který používá vaše předplatné. Povolené hodnoty pro tento parametr jsou: Capacity, PayAsYouUse a vývoj. |
| MarketplaceSyndicationEnabled | Pravda/nepravda | Určuje, jestli je na portálu dostupná funkce správy Marketplace. Nastavte na hodnotu true, pokud se registruje s připojením k Internetu. Nastavte na hodnotu false, pokud se registruje v odpojených prostředích. U odpojených registrací se dá [Nástroj pro offline syndikaci](azure-stack-download-azure-marketplace-item.md?pivots=state-disconnected) použít ke stažení položek Marketplace. |
| UsageReportingEnabled | Pravda/nepravda | Služba Azure Stack hub hlásí metriky využití ve výchozím nastavení. Obsluha použití kapacity, která využívá nebo podporuje odpojené prostředí, musí vypnout vytváření sestav využití. Povolené hodnoty pro tento parametr jsou: true, false. |
| AgreementNumber | Řetězec | Číslo smlouvy EA, pod kterou byla objednána SKU kapacity pro tento Azure Stack. |
| Registrace | Řetězec | Pokud spouštíte registrační skript ve více než jedné instanci centra Azure Stack s použitím stejného ID předplatného Azure, nastavte pro registraci jedinečný název. Parametr má výchozí hodnotu **AzureStackRegistration**. Pokud však použijete stejný název na více než jedné instanci centra Azure Stack, skript se nezdařil. |

### <a name="get-azsregistrationtoken"></a>Get-AzsRegistrationToken

Get-AzsRegistrationToken vygeneruje registrační token ze vstupních parametrů.

```powershell  
Get-AzsRegistrationToken [-PrivilegedEndpointCredential] <PSCredential> [-PrivilegedEndpoint] <String>
    [-BillingModel] <String> [[-TokenOutputFilePath] <String>] [-UsageReportingEnabled] [[-AgreementNumber] <String>]
    [<CommonParameters>]
```
| Parametr | Typ | Popis |
|-------------------------------|--------------|-------------|
| PrivilegedEndpointCredential | PSCredential | Přihlašovací údaje použité pro [přístup k privilegovanému koncovému bodu](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). Uživatelské jméno má formát **AzureStackDomain\CloudAdmin**. |
| PrivilegedEndpoint | Řetězec |  Předem nakonfigurovaná Vzdálená konzola PowerShellu, která poskytuje možnosti, jako je shromažďování protokolů a další úkoly po nasazení. Další informace najdete v článku [použití privilegovaného koncového bodu](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint) . |
| AzureContext | PSObject |  |
| ResourceGroupName | Řetězec |  |
| ResourceGroupLocation | Řetězec |  |
| BillingModel | Řetězec | Model fakturace, který používá vaše předplatné. Povolené hodnoty pro tento parametr jsou: Capacity, PayAsYouUse a vývoj. |
| MarketplaceSyndicationEnabled | Pravda/nepravda |  |
| UsageReportingEnabled | Pravda/nepravda | Služba Azure Stack hub hlásí metriky využití ve výchozím nastavení. Obsluha použití kapacity, která využívá nebo podporuje odpojené prostředí, musí vypnout vytváření sestav využití. Povolené hodnoty pro tento parametr jsou: true, false. |
| AgreementNumber | Řetězec |  |

## <a name="registration-failures"></a>Selhání registrace

Při pokusu o registraci centra Azure Stack se může zobrazit jedna z následujících chyb:

- Nepovedlo se načíst povinné informace `$hostName`o hardwaru pro. Zkontrolujte fyzického hostitele a připojení a pak zkuste znovu spustit registraci.

- Nelze se připojit `$hostName` k a získat informace o hardwaru. Zkontrolujte fyzického hostitele a připojení a pak zkuste znovu spustit registraci.

   Příčina: obvykle se snažíme z hostitelů získat podrobnosti o hardwaru, jako je UUID, BIOS a CPU, aby se pokusily o aktivaci a nebylo možné se připojit k fyzickému hostiteli.

- Identifikátor cloudu`GUID`[] je již zaregistrován. Použití identifikátorů cloudu se znovu nepovoluje.

   Příčina: k tomu dochází, pokud je vaše prostředí Azure Stack už zaregistrované. Pokud chcete své prostředí znovu zaregistrovat k jinému předplatnému nebo modelu fakturace, postupujte podle kroků pro [obnovení nebo změnu registrace](#renew-or-change-registration).

- Při pokusu o přístup ke správě Marketplace dojde k chybě při pokusu o zasyndikátování produktů.

   Příčina: k tomu obvykle dochází, když Azure Stack hub nemůže získat přístup k registračnímu prostředku. Jedním z běžných důvodů je, že když se tenant adresáře pro předplatné Azure změní, resetuje registraci. Pokud jste změnili tenanta adresáře pro předplatné, nemůžete získat přístup k webu centra Azure Stack nebo k využití sestav. Chcete-li tento problém vyřešit, je nutné provést novou registraci.
::: zone pivot="state-disconnected"
- Správa na webu Marketplace stále žádá o registraci a aktivaci centra Azure Stack, a to i v případě, že jste už zaregistrovali své razítko pomocí odpojeného procesu.

   Příčina: Jedná se o známý problém pro odpojená prostředí a vyžaduje, abyste [ověřili stav registrace](#verify-azure-stack-hub-registration). Pro použití správy Marketplace použijte [Nástroj offline](azure-stack-download-azure-marketplace-item.md?pivots=state-disconnected).
::: zone-end

## <a name="next-steps"></a>Další kroky

[Stažení položek z Marketplace z Azure](azure-stack-download-azure-marketplace-item.md)