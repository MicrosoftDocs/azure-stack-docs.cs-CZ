---
title: Registrace ASDKu s využitím Azure
description: Naučte se, jak zaregistrovat Azure Stack Development Kit (ASDK) s Azure a povolit syndikaci a vytváření sestav o využití na webu Marketplace.
author: justinha
ms.topic: article
ms.date: 06/14/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 06/14/2019
ms.openlocfilehash: 66fb60c8fbb19bf125ff143393308b9f755119b6
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76873545"
---
# <a name="register-the-asdk-with-azure"></a>Registrace ASDKu s využitím Azure

Instalaci Azure Stack Development Kit (ASDK) můžete zaregistrovat v Azure a stáhnout z Azure položky Marketplace a nastavovat pro ně sestavy obchodních dat zpět společnosti Microsoft. K podpoře úplných funkcí Azure Stack, včetně syndikace webu Marketplace, se vyžaduje registrace. K otestování důležitých Azure Stackch funkcí, jako jsou syndikace na webu Marketplace a vytváření sestav využití, je nutná registrace. Po registraci Azure Stack se využití oznamuje službě Azure Commerce. Můžete ji zobrazit v rámci předplatného, které jste použili k registraci. ASDK uživatelé se ale neúčtují za použití sestav.

Pokud ASDK nezaregistrujete, může se zobrazit výstražné upozornění **vyžadované při aktivaci** , které vás upozorní na registraci vaší ASDK. Jde o očekávané chování.

## <a name="prerequisites"></a>Požadavky

Než použijete tyto pokyny k registraci ASDK s Azure, ujistěte se, že jste nainstalovali Azure Stack PowerShell a stáhli Azure Stack nástroje, jak je popsáno v článku o [konfiguraci po nasazení](asdk-post-deploy.md) .

V počítači, který se používá k registraci ASDK v Azure, musí být také nastaven režim jazyka PowerShell na **FullLanguage** . Chcete-li ověřit, zda je aktuální jazykový režim nastaven na hodnotu Full, otevřete okno prostředí PowerShell se zvýšenými oprávněními a spusťte následující příkazy prostředí PowerShell:

```powershell  
$ExecutionContext.SessionState.LanguageMode
```

Ujistěte se, že výstup vrátí **FullLanguage**. Pokud se vrátí jiný jazykový režim, musí se registrace spustit v jiném počítači nebo v režimu jazyka nastaveném na **FullLanguage** , než budete pokračovat.

Účet služby Azure AD použitý k registraci vyžaduje přístup k předplatnému Azure a má oprávnění k vytváření aplikací identity a instančních objektů v adresáři přidruženém k tomuto předplatnému. Doporučujeme, abyste se Azure Stack s Azure zaregistrovali tak, že [vytvoříte účet služby, který se použije pro registraci, a](../operator/azure-stack-registration-role.md) nepoužijete přihlašovací údaje globálního správce.

## <a name="register-the-asdk"></a>Zaregistrovat ASDK

Pomocí těchto kroků zaregistrujete ASDK s Azure.

> [!NOTE]
> Všechny tyto kroky je nutné spustit z počítače, který má přístup k privilegovanému koncovému bodu. Pro ASDK se jedná o hostitelský počítač ASDK.

1. Otevřete konzolu PowerShellu jako správce.  

2. Spusťte následující příkazy PowerShellu k registraci instalace ASDK pomocí Azure. Přihlaste se k ID předplatného Azure pro fakturaci i k místní instalaci ASDK. Pokud ještě nemáte ID předplatného Azure, můžete [si tady vytvořit bezplatný účet Azure](https://azure.microsoft.com/free/?b=17.06). Registrace Azure Stack nevzniká žádné náklady na vaše předplatné Azure.<br><br>Pokud spustíte rutinu **set-AzsRegistration** , nastavte pro registraci jedinečný název. Parametr **registrace** má výchozí hodnotu **AzureStackRegistration**. Pokud však použijete stejný název na více než jedné instanci Azure Stack, skript se nezdaří.

    ```powershell  
    # Add the Azure cloud subscription environment name. 
    # Supported environment names are AzureCloud, AzureChinaCloud, or AzureUSGovernment depending which Azure subscription you're using.
    Add-AzureRmAccount -EnvironmentName "<environment name>"

    # Register the Azure Stack resource provider in your Azure subscription
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

    # Import the registration module that was downloaded with the GitHub tools
    Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

    # If you have multiple subscriptions, run the following command to select the one you want to use:
    # Get-AzureRmSubscription -SubscriptionID "<subscription ID>" | Select-AzureRmSubscription
    
    # Register Azure Stack
    $AzureContext = Get-AzureRmContext
    $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
    $RegistrationName = "<unique-registration-name>"
    Set-AzsRegistration `
    -PrivilegedEndpointCredential $CloudAdminCred `
    -PrivilegedEndpoint AzS-ERCS01 `
    -BillingModel Development `
    -RegistrationName $RegistrationName `
    -UsageReportingEnabled:$true
    ```

3. Po dokončení skriptu by se měla zobrazit tato zpráva: **vaše prostředí je teď zaregistrované a aktivované pomocí zadaných parametrů.**

    ![Vaše prostředí je teď zaregistrované.](media/asdk-register/1.PNG)

## <a name="register-in-disconnected-environments"></a>Registrovat v odpojených prostředích

Pokud zaregistrujete Azure Stack v odpojeném prostředí (bez připojení k Internetu), musíte získat registrační token z prostředí Azure Stack a pak tento token použít na počítači, který se může připojit k Azure a zaregistrovat a vytvořit aktivaci. prostředek pro prostředí ASDK

 > [!IMPORTANT]
 > Než použijete tyto pokyny k registraci Azure Stack, ujistěte se, že jste nainstalovali PowerShell pro Azure Stack a stáhli Azure Stack nástroje, jak je popsáno v článku o [konfiguraci po nasazení](asdk-post-deploy.md) na hostitelském počítači ASDK i v počítači s přístupem k Internetu, který se používá pro připojení k Azure a registraci.

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Získání registračního tokenu z Azure Stackho prostředí

Na hostitelském počítači ASDK spusťte PowerShell jako správce a přejděte do **registrační** složky v adresáři **AzureStack-Tools-Master** , který jste vytvořili při stahování nástrojů Azure Stack. Pomocí následujících příkazů PowerShellu Importujte modul **RegisterWithAzure. psm1** a pak pomocí rutiny **Get-AzsRegistrationToken** Získejte registrační token:  

   ```powershell  
   # Import the registration module that was downloaded with the GitHub tools
   Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

   # Create registration token
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
   # File path to save the token. This example saves the file as C:\RegistrationToken.txt.
   $FilePathForRegistrationToken = "$env:SystemDrive\RegistrationToken.txt"
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $CloudAdminCred `
   -UsageReportingEnabled:$false `
   -PrivilegedEndpoint AzS-ERCS01 `
   -BillingModel Development `
   -MarketplaceSyndicationEnabled:$false `
   -TokenOutputFilePath $FilePathForRegistrationToken
   ```

Uložte tento registrační token pro použití na počítači připojeném k Internetu. Můžete zkopírovat soubor nebo text ze souboru vytvořeného parametrem `$FilePathForRegistrationToken`.

### <a name="connect-to-azure-and-register"></a>Připojení k Azure a registrace

Na počítači připojeném k Internetu pomocí následujících příkazů PowerShellu naimportujte modul **RegisterWithAzure. psm1** a pak pomocí rutiny **Register-AzsEnvironment** zaregistrujete v Azure pomocí registračního tokenu, který jste právě vytvořili, a názvu jedinečné registrace:  

  ```powershell  
  # Add the Azure cloud subscription environment name. 
  # Supported environment names are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
  Add-AzureRmAccount -EnvironmentName "<environment name>"

  # If you have multiple subscriptions, run the following command to select the one you want to use:
  # Get-AzureRmSubscription -SubscriptionID "<subscription ID>" | Select-AzureRmSubscription

  # Register the Azure Stack resource provider in your Azure subscription
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  # Register with Azure
  # This example uses the C:\RegistrationToken.txt file.
  $registrationToken = Get-Content -Path "$env:SystemDrive\RegistrationToken.txt"
  $RegistrationName = "<unique-registration-name>"
  Register-AzsEnvironment -RegistrationToken $registrationToken `
  -RegistrationName $RegistrationName
  ```

Alternativně můžete použít rutinu **Get-Content** k ukázání na soubor, který obsahuje registrační token:

  ```powershell  
  # Add the Azure cloud subscription environment name. 
  # Supported environment names are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
  Add-AzureRmAccount -EnvironmentName "<environment name>"

  # If you have multiple subscriptions, run the following command to select the one you want to use:
  # Get-AzureRmSubscription -SubscriptionID "<subscription ID>" | Select-AzureRmSubscription

  # Register the Azure Stack resource provider in your Azure subscription
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  # Register with Azure 
  # This example uses the C:\RegistrationToken.txt file.
  $registrationToken = Get-Content -Path "$env:SystemDrive\RegistrationToken.txt"
  Register-AzsEnvironment -RegistrationToken $registrationToken `
  -RegistrationName $RegistrationName
  ```

Po dokončení registrace by se měla zobrazit zpráva podobná **této: vaše Azure Stack prostředí je teď zaregistrované v Azure.**

> [!IMPORTANT]
> Nezavírejte **okno** prostředí PowerShell.

Uložte registrační token a název prostředku registrace pro budoucí referenci.

### <a name="retrieve-an-activation-key-from-the-azure-registration-resource"></a>Načtení aktivačního klíče z prostředku registrace Azure

Pořád používáte počítač připojený k Internetu **a stejné okno konzoly PowerShellu a**načetli jste aktivační klíč z registračního prostředku vytvořeného při registraci v Azure.

Aktivační klíč získáte tak, že spustíte následující příkazy PowerShellu. Použijte stejnou jedinečnou hodnotu názvu registrace, kterou jste zadali při registraci v Azure v předchozím kroku:  

  ```Powershell
  $RegistrationResourceName = "<unique-registration-name>"
  # File path to save the activation key. This example saves the file as C:\ActivationKey.txt.
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName `
  -KeyOutputFilePath $KeyOutputFilePath
  ```

### <a name="create-an-activation-resource-in-azure-stack"></a>Vytvoření prostředku aktivace v Azure Stack

Vraťte se do prostředí Azure Stack se souborem nebo textem z aktivačního klíče vytvořeným z **Get-AzsActivationKey**. Spuštěním následujících příkazů PowerShellu vytvořte v Azure Stack prostředek aktivace pomocí tohoto aktivačního klíče:   

  ```Powershell
  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1
  
  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  $ActivationKey = "<activation key>"
  New-AzsActivationResource -PrivilegedEndpointCredential $CloudAdminCred `
  -PrivilegedEndpoint AzS-ERCS01 `
  -ActivationKey $ActivationKey
  ```

Alternativně můžete použít rutinu **Get-Content** k ukázání na soubor, který obsahuje registrační token:

  ```Powershell
  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  # This example uses the C:\ActivationKey.txt file.
  $ActivationKey = Get-Content -Path "$env:SystemDrive\Activationkey.txt"
  New-AzsActivationResource -PrivilegedEndpointCredential $CloudAdminCred `
  -PrivilegedEndpoint AzS-ERCS01 `
  -ActivationKey $ActivationKey
  ```

Po dokončení aktivace by se měla zobrazit zpráva podobná: **vašemu prostředí se dokončil proces registrace a aktivace.**

## <a name="verify-the-registration-was-successful"></a>Ověření úspěšné registrace

Pomocí dlaždice **Správa oblastí** můžete ověřit, že byla registrace Azure Stack úspěšná. Tato dlaždice je k dispozici na výchozím řídicím panelu na portálu pro správu.

1. Přihlaste se k [portálu správce Azure Stack](https://adminportal.local.azurestack.external).

2. Z řídicího panelu vyberte **Správa oblastí**.

    [![Dlaždice správy oblastí na portálu Azure Stack správce](media/asdk-register/admin1sm.png "Dlaždice správy oblastí")](media/asdk-register/admin1.png#lightbox)

3. Vyberte **Vlastnosti**. Toto okno zobrazuje stav a podrobnosti vašeho prostředí. Stav lze **zaregistrovat** nebo **není zaregistrován**. Pokud je zaregistrované, zobrazí se také ID předplatného Azure, které jste použili k registraci Azure Stack, spolu se skupinou prostředků registrace a názvem.

## <a name="move-a-registration-resource"></a>Přesunutí prostředku registrace
Přesunutí prostředku registrace mezi skupinami prostředků v rámci stejného předplatného **je** podporováno. Další informace o přesouvání prostředků do nové skupiny prostředků najdete v tématu [Přesunutí prostředků do nové skupiny prostředků nebo předplatného](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).


## <a name="next-steps"></a>Další kroky

- [Přidat položku webu Marketplace centra Azure Stack](../operator/azure-stack-marketplace.md)
