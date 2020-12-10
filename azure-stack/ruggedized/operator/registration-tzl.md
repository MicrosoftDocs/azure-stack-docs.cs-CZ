---
title: Registrace centra Azure Stack s Azure
description: Naučte se registrovat centra Azure Stack s Azure, abyste si mohli stáhnout Azure Marketplace položky a nastavit vytváření sestav dat.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/20/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 12/20/2019
ms.openlocfilehash: 94de29942f49aa7151de201bfa00d99625a1fee8
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96939600"
---
# <a name="register-azure-stack-hub-with-azure"></a>Registrace centra Azure Stack s Azure

Pokud chcete nastavit syndikaci na webu Marketplace, musíte po dokončení nasazení zaregistrovat a aktivovat vaše modulární datacentrum (MDC) nebo centrum Azure Stack v závislosti na centru Azure Stack. Pomocí syndikace Marketplace správce naplní místní web centra Azure Stack s obrázky staženými z Azure Marketplace.

Pro systémy, které se budou připojovat ke cloudu Azure a také pro systémy, které budou odpojeny, se vyžaduje registrace.

## <a name="overview"></a>Přehled

Proces registrace se skládá z následujících kroků:

1. Exportujte registrační token z centra Azure Stack.
2. Registrace centra Azure Stack s Azure
3. Exportujte aktivační klíč z Azure.
4. Aktivujte centrum Azure Stack.

Pokud je systém připojený ke cloudu Azure, můžete všechny čtyři kroky spustit jediným skriptem. Pokud je systém odpojený, správce musí provádět jednotlivé kroky zvlášť.

> [!NOTE]
> Pro MDC musí být každý ze systémů centra Azure Stack zaregistrován samostatně. Proto je třeba provést tři tyto pokyny třikrát.

## <a name="prerequisites"></a>Předpoklady

Před registrací potřebujete tyto předpoklady:

- Ověřte přihlašovací údaje.
- Nastavte režim jazyka PowerShell.
- Nainstalujte PowerShell pro centrum Azure Stack.
- Instalace modulu nástrojů Azure Stackového centra
- Určete svůj scénář registrace.

### <a name="verify-your-credentials"></a>Ověření přihlašovacích údajů

Před registrací centra Azure Stack s Azure musíte mít:

- ID předplatného pro předplatné Azure.  

    > [!Note]  
    > Předplatná Azure jsou přidružená ke cloudovým prostředím Azure (Azure Commercial, Azure pro vládu atd.). Tím se určuje, ke kterému cloudu se připojíte, abyste měli přístup k obsahu Marketplace.
    > 
    > Předplatné, které používáte pro registraci, musí být schválené pro služby JEDI Services. Tím se zajistí, že zařízení, které zaregistrujete, bude mít nárok na neomezené využití prostředků bez nahlášení využití do Azure. Pokud chcete schválit vaše předplatné, pošlete e-mail na azshregistration@microsoft.com ID předplatného, které je potřeba schválit společně Azure Stack s MDCm nebo modulárním datovým centrem () centra, které zaregistrujete.

- Uživatelské jméno a heslo pro účet, který je vlastníkem předplatného. 
- Uživatelský účet musí mít přístup k předplatnému Azure a mít oprávnění k vytváření aplikací identity a instančních objektů v adresáři přidruženém k tomuto předplatnému. K registraci centra Azure Stack v Azure doporučujeme použít správu s minimálními oprávněními. Další informace o tom, jak vytvořit definici vlastní role, která omezuje přístup k vašemu předplatnému k registraci, najdete v tématu [Vytvoření registrační role pro centrum Azure Stack](../../operator/azure-stack-registration-role.md).
- Zaregistrujte poskytovatele prostředků centra Azure Stack (podrobnosti najdete v následujících částech).

Po registraci se nevyžaduje oprávnění globálního správce služby Azure Active Directory (Azure AD). Některé operace ale můžou vyžadovat přihlašovací údaje globálního správce (například skript instalačního programu poskytovatele prostředků nebo novou funkci, která vyžaduje udělení oprávnění). Můžete buď dočasně obnovit oprávnění globálního správce účtu, nebo použít samostatný účet globálního správce, který je vlastníkem *výchozího předplatného poskytovatele*.

Uživatel, který registruje centrum Azure Stack, je vlastníkem instančního objektu ve službě Azure AD. Registraci centra Azure Stack může změnit jenom uživatel, který zaregistroval centrum Azure Stack. Pokud se uživatel bez role správce, který není vlastníkem instančního objektu služby, pokusí zaregistrovat nebo znovu zaregistrovat Azure Stack centrum, může se zobrazit odpověď 403. Odpověď 403 indikuje, že uživatel nemá dostatečná oprávnění k dokončení operace.

Při registraci centra Azure Stack se neúčtují žádné náklady na vaše předplatné Azure.

### <a name="powershell-language-mode"></a>Režim jazyka PowerShell

Pro úspěšné registraci centra Azure Stack musí být režim jazyka PowerShell nastavený na **FullLanguageMode**. Pokud chcete ověřit, jestli je aktuální jazykový režim nastavený na Full, otevřete okno PowerShellu se zvýšenými oprávněními a spusťte následující rutiny PowerShellu:

```powershell  
$ExecutionContext.SessionState.LanguageMode
```

Ujistěte se, že výstup vrátí **FullLanguageMode**. Pokud se vrátí jiný jazykový režim, musí být registrace spuštěná na jiném počítači, nebo než budete pokračovat, musíte nastavit jazykový režim na **FullLanguageMode** .

### <a name="install-powershell-for-azure-stack-hub"></a>Instalace PowerShellu pro centrum Azure Stack

K registraci v Azure použijte nejnovější prostředí PowerShell pro Azure Stack hub.

Pokud není nejnovější verze ještě nainstalovaná, přečtěte si téma [instalace PowerShellu pro Azure Stack hub](../../operator/azure-stack-powershell-install.md).

### <a name="install-the-azure-stack-hub-tools-module"></a>Instalace modulu nástrojů Azure Stackového centra

[Modul nástrojů centra Azure Stack](https://www.powershellgallery.com/packages/azs.tools.admin/0.1.0) obsahuje moduly PowerShellu, které podporují funkce centra Azure Stack, včetně funkcí registrace. Během procesu registrace musíte importovat a používat dílčí modul **RegisterWithAzure. psm1** k registraci vaší instance centra Azure Stack v Azure.

Instalace nejnovějšího nástroje centra Azure Stack:

1. Otevřete příkazový řádek PowerShell se zvýšenými oprávněními.
2. Spusťte následující rutinu:

    ```powershell  
        Install-Module -Name Azs.Tools.Admin
    ```

### <a name="determine-your-registration-scenario"></a>Určení scénáře registrace

Vaše nasazení centra Azure Stack se může *připojit* nebo *Odpojit*.

- **Připojeno**: připojeno znamená, že jste nasadili Azure Stack hub, aby se mohl připojit k Internetu a k Azure. Pro své úložiště identit můžete buď Azure AD, nebo Active Directory Federation Services (AD FS) (AD FS).

- **Odpojeno**: s možností nasazení odpojeno od Azure můžete nasadit a používat centrum Azure Stack bez připojení k Internetu.

### <a name="determine-a-unique-registration-name-to-use"></a>Určení jedinečného názvu registrace, který se má použít

Když zaregistrujete Azure Stack centrum s Azure, musíte zadat jedinečný název registrace. Snadný způsob, jak přidružit předplatné centra Azure Stack k registraci Azure, je použití **ID cloudu** Azure Stack hub.

Pokud chcete zjistit ID cloudu pro nasazení centra Azure Stack, otevřete PowerShell jako správce v počítači, který má přístup k privilegovanému koncovému bodu, spusťte následující příkazy a pak zaznamenejte hodnotu **CloudID** :

```PowerShell
Enter-PSSession -ComputerName <privileged endpoint computer name> -ConfigurationName PrivilegedEndpoint
Get-AzureStackStampInformation
```

## <a name="registration-and-activation-for-systems-connected-to-the-azure-cloud"></a>Registrace a aktivace pro systémy připojené ke cloudu Azure

Pomocí následujících kroků zaregistrujete systém Azure Stack hub, který má připojení k Azure.

> [!NOTE]  
> Všechny tyto kroky je nutné spustit z počítače, který má přístup k privilegovanému koncovému bodu (PEP). Podrobnosti o PEP najdete v tématu [použití privilegovaného koncového bodu v centru Azure Stack](../../operator/azure-stack-privileged-endpoint.md).

Připojená prostředí mají přístup k Internetu a k Azure. V případě těchto prostředí Zaregistrujte poskytovatele prostředků centra Azure Stack v Azure a potom nakonfigurujte model fakturace.

1. Pokud chcete zaregistrovat poskytovatele prostředků centra Azure Stack v Azure, spusťte PowerShell ISE jako správce a použijte následující rutiny PowerShellu s parametrem **Environment** nastaveným na příslušný typ předplatného Azure (viz následující parametry).

2. Přidejte účet Azure, který jste použili k registraci centra Azure Stack. Pokud chcete účet přidat, spusťte rutinu **Add-AzureRmAccount** . Zobrazí se výzva k zadání přihlašovacích údajů k účtu Azure a možná budete muset použít dvojúrovňové ověřování na základě konfigurace vašeho účtu.

   ```powershell
   Add-AzureRmAccount -EnvironmentName "<environment name>"
   ```

   | Parametr | Popis |  
   |-----|-----|
   | EnvironmentName | Název prostředí Azure Cloud Subscription. Podporované názvy prostředí jsou **AzureCloud** nebo **AzureUSGovernment**.  |

   >[!NOTE]
   > Pokud vaše relace vyprší, vaše heslo se změnilo nebo chcete přepnout účty, spusťte následující rutinu ještě před přihlášením pomocí rutiny **Add-AzureRmAccount**: **Remove-AzureRmAccount-Scope Process**.

3. Ve stejné relaci prostředí PowerShell se ujistěte, že jste přihlášeni ke správnému kontextu Azure PowerShell. Tento kontext je účet Azure, který byl dříve použit k registraci poskytovatele prostředků služby Azure Stack hub:

   ```powershell  
   Connect-AzureRmAccount -Environment "<environment name>"
   ```

   | Parametr | Popis |  
   |-----|-----|
   | EnvironmentName | Název prostředí Azure Cloud Subscription. Podporované názvy prostředí jsou **AzureCloud** nebo **AzureUSGovernment**.  |

4. Máte-li více předplatných, spusťte následující příkaz a vyberte ten, který chcete použít:

   ```powershell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

5. Spuštěním následujícího příkazu zaregistrujte poskytovatele prostředků centra Azure Stack v rámci vašeho předplatného Azure:

   ```powershell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

   Případně můžete zaregistrovat poskytovatele prostředků centra Azure Stack v předplatném Azure pomocí Azure Portal. Přihlaste se k Azure Portal pomocí účtu přidruženého k předplatnému Azure. V části **kategorie**  >  **Obecné**  >  **odběry** vyberte ID předplatného, které potřebujete k registraci poskytovatele prostředků centra Azure Stack. V levém podokně přejděte na **Nastavení**  >  **poskytovatelé prostředků**. Vyberte poskytovatele prostředků **Microsoft. AzureStack** a vyberte **zaregistrovat**.

   ![Zaregistrovat poskytovatele prostředků centra Azure Stack](./media/registration-tzl/register-azure-resource-provider-portal.png)


6. Ve stejné relaci prostředí PowerShell spusťte rutinu **set-AzsRegistration** :

   ```powershell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $msAssetTag = "Enter the value printed on the product"
   $RegistrationName = "<unique-registration-name>"

   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel Custom `
      -RegistrationName $RegistrationName `
      -msAssetTag $msAssetTagName `
      -UsageReportingEnabled: $false
   ```
   Štítek MS Asset ( `msAssetTag` ) je povinný pro vlastní registraci modelu fakturace a tiskne se na produktu.
    
   Proces trvá od 10 do 15 minut. Po dokončení příkazu se zobrazí zpráva. **Vaše prostředí je teď zaregistrované a aktivované pomocí zadaných parametrů.**

## <a name="registration-and-activation-for-systems-not-connected-to-the-azure-cloud"></a>Registrace a aktivace pro systémy nepřipojené ke cloudu Azure 

Pokud registrujete Azure Stack hub v odpojeném prostředí (bez připojení k Internetu), proveďte následující kroky:

1. Získejte registrační token z prostředí Azure Stack hub.

2. Připojte se k Azure a zaregistrujte se pomocí registračního tokenu z kroku 1. Použijte počítač, který se může připojit k Azure a má nainstalovaný PowerShell pro Azure Stack hub.

3. Načte aktivační klíč z registrace Azure.

4. Aktivujte centrum Azure Stack pomocí aktivačního klíče z Azure.

### <a name="get-a-registration-token-from-the-azure-stack-hub-environment"></a>Získání registračního tokenu z prostředí Azure Stack hub

Získejte registrační token z prostředí Azure Stack hub. Pak použijte tento token na počítači, který se může připojit k Azure a obsahuje PowerShell pro Azure Stack centrum a Azure Stack nástrojů centra pro instalaci.

1. Registrační token získáte spuštěním následujících rutin PowerShellu z příkazového řádku se zvýšenými oprávněními:

   ```PowerShell
    $FilePathForRegistrationToken = "$env:SystemDrive\RegistrationToken.txt" 
    
    $RegistrationToken = Get-AzsRegistrationToken `
      -PrivilegedEndpointCredential $YourCloudAdminCredential `
      -UsageReportingEnabled:$False `
      -PrivilegedEndpoint $YourPrivilegedEndpoint `
      -BillingModel Custom -msAssetTag '<MS Asset tag>' `
      -TokenOutputFilePath $FilePathForRegistrationToken 
   ```

   > [!TIP]  
   > Registrační token je uložený v souboru určeném parametrem `$FilePathForRegistrationToken` . Cestu nebo název souboru můžete podle potřeby změnit.

2. Uložte tento registrační token pro použití na počítači připojeném k Azure. Můžete zkopírovat soubor nebo text z `$FilePathForRegistrationToken` .

### <a name="connect-to-azure-and-register"></a>Připojení k Azure a registrace

V počítači, který je připojený k Internetu, se přihlaste ke správnému kontextu Azure PowerShell. Pak zavolejte **Register-AzsEnvironment**. Zadejte registrační token, který se má zaregistrovat v Azure. Pokud registrujete více než jednu instanci centra Azure Stack s použitím stejného ID předplatného Azure, zadejte jedinečný název registrace.

Potřebujete svůj registrační token a jedinečný název tokenu.

1. Z příkazového řádku PowerShellu se zvýšenými oprávněními spusťte následující rutiny a ujistěte se, že používáte správné předplatné.

    ```powershell  
    Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
    ```

2. Pak spusťte následující rutiny PowerShellu:

    ```powershell  
    $RegistrationToken = "<Your Registration Token>"
    $RegistrationName = "<unique-registration-name>"
    Register-AzsEnvironment -RegistrationToken $RegistrationToken -RegistrationName $RegistrationName
    ```

3. Volitelně můžete použít rutinu **Get-Content** k ukázání na soubor, který obsahuje registrační token.

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

> [!NOTE]  
> Uložte název prostředku registrace a registrační token pro budoucí referenci.

### <a name="retrieve-an-activation-key-from-azure-registration-resource"></a>Načtení aktivačního klíče z registračního prostředku Azure

V dalším kroku načtěte aktivační klíč z registračního prostředku vytvořeného v Azure během **Register-AzsEnvironment**.

Aktivační klíč získáte spuštěním následujících rutin PowerShellu:

```PowerShell
$RegistrationResourceName = "<unique-registration-name>"
$KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
$ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName -KeyOutputFilePath $KeyOutputFilePath
```

> [!TIP]  
> Aktivační klíč je uložen v souboru určeném parametrem `$KeyOutputFilePath` . Cestu nebo název souboru můžete podle potřeby změnit.

### <a name="create-an-activation-resource-in-azure-stack-hub"></a>Vytvoření prostředku aktivace v centru Azure Stack

Vraťte se do prostředí Azure Stack hub se souborem nebo textem z aktivačního klíče vytvořeným pomocí **Get-AzsActivationKey**. V dalším kroku vytvořte aktivační prostředek v Azure Stack hub pomocí tohoto aktivačního klíče. Pokud chcete vytvořit prostředek aktivace, spusťte následující rutiny PowerShellu:

```PowerShell
$ActivationKey = "<activation key>"
New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
```

Volitelně můžete pomocí rutiny **Get-Content** odkazovat na soubor, který obsahuje registrační token:

```PowerShell
$ActivationKey = Get-Content -Path '<Path>\<Activation Key File>'
New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
```

## <a name="verify-azure-stack-hub-registration"></a>Ověřit registraci centra Azure Stack

Pomocí dlaždice **Správa oblastí** můžete ověřit, zda byla registrace centra Azure Stack úspěšná. Tato dlaždice je k dispozici na výchozím řídicím panelu na portálu pro správu. Stav lze zaregistrovat nebo není zaregistrován. Pokud je zaregistrované, zobrazí se také ID předplatného Azure, které jste použili k registraci centra Azure Stack spolu se skupinou prostředků registrace a názvem.

1. Přihlaste se k portálu správce centra Azure Stack ( `https://adminportal.local.azurestack.external` ).

2. Z řídicího panelu vyberte **Správa oblastí**.

3. Vyberte **Vlastnosti**. Toto okno zobrazuje stav a podrobnosti vašeho prostředí. Stav lze **zaregistrovat**, **není zaregistrováno** nebo **vypršela jeho platnost**.

   [![Dlaždice správy oblastí na portálu pro správu centra Azure Stack](media/registration-tzl/admin1sm.png "Dlaždice správy oblastí")](media/registration-tzl/admin1.png#lightbox)

   Pokud jsou zaregistrované, zahrnují tyto vlastnosti:

    - **ID předplatného registrace**: ID předplatného Azure zaregistrované a přidružené k rozbočovači Azure Stack.
    - **Skupina prostředků registrace**: Skupina prostředků Azure v souvisejícím předplatném, které obsahuje prostředky centra Azure Stack.

4. K zobrazení registračních prostředků centra Azure Stack můžete použít Azure Portal a ověřit, zda byla registrace úspěšná. Přihlaste se k [Azure Portal](https://portal.azure.com/) pomocí účtu přidruženého k předplatnému, které jste použili k registraci centra Azure Stack. Vyberte **všechny prostředky**, zaškrtněte políčko **Zobrazit skryté typy** a potom vyberte název registrace.

5. Pokud se registrace nezdařila, je nutné znovu provést registraci změnou [předplatného, které používáte](https://docs.microsoft.com/azure-stack/operator/azure-stack-registration#change-the-subscription-you-use) k vyřešení problému.

Případně můžete pomocí funkce správy Marketplace ověřit, jestli byla registrace úspěšná. Pokud se v okně **Správa Marketplace** zobrazí seznam položek Marketplace, vaše Registrace proběhla úspěšně. V odpojených prostředích ale nevidíte položky Marketplace ve správě Marketplace.

> [!NOTE]
> Po dokončení registrace se už nezobrazí aktivní upozornění pro neregistraci.

> [!NOTE]
> Chybová zpráva **[InvalidRegistrationToken]: BillingModel Custom není v rámci předplatného podporovaná, protože** předplatné, které používáte pro registraci, nebylo schválené pro Azure Stack v centru k zaznamenání nebo MDC použití. Kontaktujte prosím azshregistration@microsoft.com , aby bylo předplatné schválené spolu s typem produktu (Azure Stack nebo MDCm centrem), které zaregistrujete.

## <a name="next-steps"></a>Další kroky

[Základy správy centra Azure Stack](../../operator/azure-stack-manage-basics.md)  
