---
title: Správa Key Vault v centru Azure Stack pomocí PowerShellu
description: Naučte se spravovat Key Vault v centru Azure Stack pomocí PowerShellu.
author: sethmanheim
ms.topic: article
ms.date: 04/29/2020
ms.author: sethm
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: f1cdb082accdef3590bd737a39add61b1ebbc8bb
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94546306"
---
# <a name="manage-key-vault-in-azure-stack-hub-using-powershell"></a>Správa Key Vault v centru Azure Stack pomocí PowerShellu

Tento článek popisuje, jak vytvořit a spravovat Trezor klíčů v Azure Stackovém centru pomocí PowerShellu. Naučíte se, jak používat rutiny Key Vault PowerShellu k těmto akcím:

* Vytvořte trezor klíčů.
* Ukládejte a spravujte kryptografické klíče a tajné kódy.
* Autorizovat uživatele nebo aplikace k vyvolání operací v trezoru.

>[!NOTE]
>Rutiny Key Vault PowerShellu popsané v tomto článku jsou uvedené v sadě SDK pro Azure PowerShell.

## <a name="prerequisites"></a>Požadavky

* Musíte se přihlásit k odběru nabídky, která zahrnuje službu Azure Key Vault.
* [Nainstalujte PowerShell pro centrum Azure Stack](../operator/powershell-install-az-module.md).
* [Nakonfigurujte prostředí PowerShell centra Azure Stack](azure-stack-powershell-configure-user.md).

## <a name="enable-your-tenant-subscription-for-key-vault-operations"></a>Povolení předplatného tenanta pro Key Vault operací

Než budete moct na Trezor klíčů vydávat nějaké operace, musíte zajistit, aby bylo u vašeho předplatného pro operace trezoru povolené. Chcete-li ověřit, zda jsou povoleny operace trezoru klíčů, spusťte následující příkaz:

```powershell  
Get-AzResourceProvider -ProviderNamespace Microsoft.KeyVault | ft -Autosize
```

Pokud je u vašeho předplatného povolené operace trezoru, **zobrazí se ve** výstupu **RegistrationState** pro všechny typy prostředků trezoru klíčů.

![Stav registrace trezoru klíčů v PowerShellu](media/azure-stack-key-vault-manage-powershell/image1.png)

Pokud nejsou povolené operace trezoru, vydejte následující příkaz k registraci služby Key Vault ve vašem předplatném:

```powershell
Register-AzResourceProvider -ProviderNamespace Microsoft.KeyVault
```

Pokud je registrace úspěšná, vrátí se následující výstup:

![Úspěšná registrace trezoru klíčů v PowerShellu](media/azure-stack-key-vault-manage-powershell/image2.png)

Když vyvoláte příkazy trezoru klíčů, může se zobrazit chyba, například "předplatné není zaregistrované pro používání oboru názvů" Microsoft. klíčů trezor ". Pokud se zobrazí chyba, potvrďte, že jste povolili poskytovatele prostředků Key Vault podle předchozích pokynů.

## <a name="create-a-key-vault"></a>Vytvořte trezor klíčů.

Před vytvořením trezoru klíčů vytvořte skupinu prostředků, aby všechny prostředky týkající se trezoru klíčů existovaly ve skupině prostředků. Pomocí následujícího příkazu vytvořte novou skupinu prostředků:

```powershell
New-AzResourceGroup -Name "VaultRG" -Location local -verbose -Force
```

![Nová skupina prostředků vygenerovaná v PowerShellu](media/azure-stack-key-vault-manage-powershell/image3.png)

Nyní pomocí rutiny **New-AzKeyVault** vytvořte Trezor klíčů ve skupině prostředků, kterou jste vytvořili dříve. Tento příkaz přečte tři povinné parametry: název skupiny prostředků, název trezoru klíčů a geografické umístění.

Spuštěním následujícího příkazu vytvořte Trezor klíčů:

```powershell
New-AzKeyVault -VaultName "Vault01" -ResourceGroupName "VaultRG" -Location local -verbose
```

![Nový trezor klíčů vygenerovaný v PowerShellu](media/azure-stack-key-vault-manage-powershell/image4.png)

Výstup tohoto příkazu zobrazuje vlastnosti trezoru klíčů, který jste vytvořili. Když aplikace přistupuje k tomuto trezoru, musí použít vlastnost **identifikátor URI trezoru** , která je `https://vault01.vault.local.azurestack.external` v tomto příkladu.

### <a name="active-directory-federation-services-ad-fs-deployment"></a>Nasazení Active Directory Federation Services (AD FS) (AD FS)

V nasazení AD FS se může zobrazit toto upozornění: zásada přístupu není nastavená. Žádný uživatel nebo aplikace nemá přístupová oprávnění k používání tohoto trezoru. Pokud chcete tento problém vyřešit, nastavte pro trezor zásady přístupu pomocí příkazu [**set-AzKeyVaultAccessPolicy**](#authorize-an-app-to-use-a-key-or-secret) :

```powershell
# Obtain the security identifier(SID) of the active directory user
$adUser = Get-ADUser -Filter "Name -eq '{Active directory user name}'"
$objectSID = $adUser.SID.Value

# Set the key vault access policy
Set-AzKeyVaultAccessPolicy -VaultName "{key vault name}" -ResourceGroupName "{resource group name}" -ObjectId "{object SID}" -PermissionsToKeys {permissionsToKeys} -PermissionsToSecrets {permissionsToSecrets} -BypassObjectIdValidation
```

## <a name="manage-keys-and-secrets"></a>Správa klíčů a tajných kódů

Po vytvoření trezoru použijte tento postup k vytvoření a správě klíčů a tajných klíčů v trezoru.

### <a name="create-a-key"></a>Vytvoření klíče

Pomocí rutiny **Add-AzureKeyVaultKey** vytvořte nebo importujte klíč chráněný softwarem v trezoru klíčů:

```powershell
Add-AzureKeyVaultKey -VaultName "Vault01" -Name "Key01" -verbose -Destination Software
```

`-Destination`Parametr se používá k určení, zda je klíč chráněný softwarem. Po úspěšném vytvoření klíče vypíše příkaz Podrobnosti vytvořeného klíče.

![Nový klíč trezoru klíčů vygenerovaný v PowerShellu](media/azure-stack-key-vault-manage-powershell/image5.png)

Nyní můžete odkazovat na vytvořený klíč pomocí jeho identifikátoru URI. Pokud vytvoříte nebo importujete klíč, který má stejný název jako existující klíč, původní klíč se aktualizuje s hodnotami zadanými v novém klíči. K předchozí verzi můžete přistupovat pomocí identifikátoru URI, který je specifický pro danou verzi klíče. Například:

* `https://vault10.vault.local.azurestack.external:443/keys/key01`K získání aktuální verze vždycky použijte.
* Použijte `https://vault010.vault.local.azurestack.external:443/keys/key01/d0b36ee2e3d14e9f967b8b6b1d38938a` k získání této konkrétní verze.

### <a name="get-a-key"></a>Získat klíč

Pomocí rutiny **Get-AzureKeyVaultKey** si přečtěte klíč a jeho podrobnosti:

```powershell
Get-AzureKeyVaultKey -VaultName "Vault01" -Name "Key01"
```

### <a name="create-a-secret"></a>Vytvoření tajného klíče

K vytvoření nebo aktualizaci tajného klíče v trezoru použijte rutinu **set-AzureKeyVaultSecret** . Tajný kód se vytvoří, pokud ještě neexistuje. Pokud už existuje, vytvoří se nová verze tajného klíče:

```powershell
$secretvalue = ConvertTo-SecureString "User@123" -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName "Vault01" -Name "Secret01" -SecretValue $secretvalue
```

![Vytvoření tajného klíče v PowerShellu](media/azure-stack-key-vault-manage-powershell/image6.png)

### <a name="get-a-secret"></a>Získat tajný kód

Pomocí rutiny **Get-AzureKeyVaultSecret** si přečtete tajný klíč v trezoru klíčů. Tento příkaz může vracet všechny nebo jen konkrétní verze tajného kódu:

```powershell
Get-AzureKeyVaultSecret -VaultName "Vault01" -Name "Secret01"
```

Po vytvoření klíčů a tajných kódů můžete pro jejich používání povolit externí aplikace.

## <a name="authorize-an-app-to-use-a-key-or-secret"></a>Autorizace aplikace pro použití klíče nebo tajného klíče

Použijte rutinu **set-AzKeyVaultAccessPolicy** k autorizaci aplikace pro přístup ke klíči nebo tajnému klíči v trezoru klíčů.

V následujícím příkladu je název trezoru **ContosoKeyVault** a aplikace, kterou chcete autorizovat, má ID klienta **8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed**. K autorizaci aplikace spusťte následující příkaz. Můžete také zadat parametr **PermissionsToKeys** pro nastavení oprávnění pro uživatele, aplikaci nebo skupinu zabezpečení.

Pokud používáte Set-AzKeyvaultAccessPolicy pro službu ADFS nakonfigurovanou Azure Stack centrální prostředí, měl by se zadat parametr BypassObjectIdValidation.

```powershell
Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign -BypassObjectIdValidation
```

Pokud chcete, aby stejná aplikace při čtení tajných klíčů v trezoru, spusťte následující rutinu:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300 -PermissionsToKeys Get -BypassObjectIdValidation
```

## <a name="next-steps"></a>Další kroky

* [Nasazení virtuálního počítače s heslem uloženým v Key Vault](azure-stack-key-vault-deploy-vm-with-secret.md)
* [Nasazení virtuálního počítače s certifikátem uloženým v Key Vault](azure-stack-key-vault-push-secret-into-vm.md)
