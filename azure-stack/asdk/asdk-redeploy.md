---
title: Znovu nasadit ASDK
description: Přečtěte si, jak Azure Stack Development Kit (ASDK) znovu nasadit.
author: myoungerman
ms.topic: article
ms.date: 02/12/2019
ms.author: v-myoung
ms.reviewer: misainat
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 7408bcb7317550d7093f97a4cfe10bbc17119467
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94543625"
---
# <a name="redeploy-the-asdk"></a>Znovu nasadit ASDK
V tomto článku se dozvíte, jak znovu nasadit Azure Stack Development Kit (ASDK) v neprodukčním prostředí. Vzhledem k tomu, že upgrade ASDK se nepodporuje, je potřeba ho kompletně znovu nasadit a přejít na novější verzi. ASDK můžete také znovu nasadit, kdykoli budete chtít začít od začátku.

> [!IMPORTANT]
> Upgrade ASDK na novou verzi se nepodporuje. Pokaždé, když chcete vyhodnotit novější verzi Azure Stack, je třeba znovu nasadit ASDK na hostitelský počítač ASDK.

## <a name="remove-azure-registration"></a>Odebrat registraci Azure 
Pokud jste instalaci ASDK zaregistrovali v Azure, měli byste před opětovným nasazením ASDK odebrat registrační prostředek. Znovu zaregistrujte ASDK a povolte dostupnost položek na webu Marketplace při opětovném nasazení ASDK. Pokud jste dosud nezaregistrovali ASDK s vaším předplatným Azure, můžete tuto část přeskočit.

K odebrání registračního prostředku použijte rutinu **Remove-AzsRegistration** pro zrušení registrace Azure Stack. Pak pomocí rutiny **Remove-AzResourceGroup** odstraňte skupinu prostředků Azure Stack z předplatného Azure:

1. Otevřete konzolu PowerShellu jako správce v počítači, který má přístup k privilegovanému koncovému bodu. Pro ASDK se jedná o hostitelský počítač ASDK.

2. Spusťte následující příkazy PowerShellu, abyste zrušili registraci instalace ASDK a odstranili skupinu prostředků **azurestack** z předplatného Azure:

   ```powershell    
   #Import the registration module that was downloaded with the GitHub tools
   Import-Module C:\AzureStack-Tools-az\Registration\RegisterWithAzure.psm1

   # Provide Azure subscription admin credentials
   Add-AzAccount

   # Provide ASDK admin credentials
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"

   # Unregister Azure Stack
   Remove-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint AzS-ERCS01
      -RegistrationName $RegistrationName

   # Remove the Azure Stack resource group
   Remove-AzResourceGroup -Name azurestack -Force
   ```

3. Po spuštění skriptu se zobrazí výzva, abyste se přihlásili ke svému předplatnému Azure i k místní instalaci ASDK.
4. Po dokončení skriptu byste měli vidět zprávy podobné následujícím příkladům:

    `De-Activating Azure Stack (this may take up to 10 minutes to complete).` `Your environment is now unable to syndicate items and is no longer reporting usage data.`
    `Remove registration resource from Azure...`
    `"Deleting the resource..." on target "/subscriptions/<subscription information>"`
    `********** End Log: Remove-AzsRegistration *********`



Z vašeho předplatného Azure by se teď mělo úspěšně odregistrovat Azure Stack. Je taky potřeba odstranit skupinu prostředků azurestack. Tato skupina prostředků je ta vytvořená při první registraci ASDK s Azure.

## <a name="deploy-the-asdk"></a>Nasazení rozhraní ASDK
Pokud chcete Azure Stack znovu nasadit, musíte začít od začátku, jak je popsáno níže. Postup se liší v závislosti na tom, jestli jste k instalaci ASDK použili skript instalačního programu Azure Stack (asdk-installer.ps1).

### <a name="redeploy-the-asdk-using-the-installer-script"></a>Znovu nasaďte ASDK pomocí skriptu instalačního programu
1. Otevřete na počítači s ASDK konzolu PowerShellu se zvýšenými oprávněními a přejděte do složky asdk-installer.ps1 v adresáři **AzureStack_Installer** umístěném na nesystémové jednotce. Spusťte skript a klikněte na **restartovat**.

   ![Spuštění skriptu asdk-installer.ps1](media/asdk-redeploy/1.png)

2. Vyberte základní operační systém (ne **Azure Stack** ) a klikněte na **Další**.

   ![Restartovat operační systém hostitele](media/asdk-redeploy/2.png)

3. Po restartování hostitele ASDK do základního operačního systému se přihlaste jako místní správce. Vyhledejte a odstraňte soubor **C:\CloudBuilder.vhdx** , který byl použit jako součást předchozího nasazení.

4. Opakujte stejné kroky jako při prvním [nasazení ASDK](asdk-install.md).

### <a name="redeploy-the-asdk-without-using-the-installer"></a>Opětovné nasazení ASDK bez použití instalačního programu
Pokud jste nepoužili skript asdk-installer.ps1 k instalaci ASDK, musíte před opětovným nasazením ASDK ručně znovu nakonfigurovat hostitelský počítač ASDK.

1. Spusťte nástroj Konfigurace systému spuštěním **msconfig.exe** na počítači s ASDK. Na kartě **spuštění** vyberte operační systém hostitelského počítače (ne Azure Stack), klikněte na **nastavit jako výchozí** a pak klikněte na **OK**. Po zobrazení výzvy klikněte na **Restartovat**.

      ![Nastavení konfigurace spouštění](media/asdk-redeploy/4.png)

2. Po restartování hostitele ASDK do základního operačního systému se přihlaste jako místní správce pro hostitelský počítač ASDK. Vyhledejte a odstraňte soubor **C:\CloudBuilder.vhdx** , který byl použit jako součást předchozího nasazení.

3. Opakujte stejné kroky jako při prvním [nasazení ASDK pomocí PowerShellu](asdk-deploy-powershell.md).


## <a name="next-steps"></a>Další kroky
[Úkoly nasazení po ASDK](asdk-post-deploy.md)




