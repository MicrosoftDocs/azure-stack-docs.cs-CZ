---
title: Správa služby Key Vault ve službě Azure Stack pomocí prostředí PowerShell | Dokumentace Microsoftu
description: Další informace o správě služby Key Vault ve službě Azure Stack pomocí prostředí PowerShell
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 22B62A3B-B5A9-4B8C-81C9-DA461838FAE5
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2019
ms.author: sethm
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: 613fec37e1677698e72ff09d3ffdc35502a57de5
ms.sourcegitcommit: c755c7eac0f871960f9290591421cf5990b9e734
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/09/2019
ms.locfileid: "65506079"
---
# <a name="manage-key-vault-in-azure-stack-using-powershell"></a>Správa služby Key Vault ve službě Azure Stack pomocí Powershellu

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Může spravovat službu Key Vault ve službě Azure Stack powershellu. Další informace o použití rutin Key Vault prostředí PowerShell:

* Vytvoření trezoru klíčů
* Store a spravovat kryptografické klíče a tajné kódy.
* Autorizace uživatelů nebo aplikací k vyvolání operací v trezoru.

>[!NOTE]
>Rutiny Key Vault prostředí PowerShell popsaných v tomto článku jsou uvedené v Azure PowerShell SDK.

## <a name="prerequisites"></a>Požadavky

* Můžete musí přihlásit k odběru nabídky, která zahrnuje službu Azure Key Vault.
* [Instalace Powershellu pro Azure Stack](../operator/azure-stack-powershell-install.md).
* [Konfigurace uživatele služby Azure Stack Powershellu prostředí](azure-stack-powershell-configure-user.md).

## <a name="enable-your-tenant-subscription-for-key-vault-operations"></a>Povolení předplatného tenanta pro operace služby Key Vault

Předtím, než můžete použít všechny operace služby key vault, musíte zajistit, že vaše předplatné tenanta je povoleno pro operace trezoru. Pokud chcete ověřit, zda jsou povoleny operace trezoru, spusťte následující příkaz:

```powershell  
Get-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault | ft -Autosize
```

Pokud vaše předplatné je povolená pro operace trezoru, výstup zobrazuje **RegistrationState** je **registrované** pro všechny typy prostředků trezoru klíčů.

![Stav registrace trezoru klíčů](media/azure-stack-key-vault-manage-powershell/image1.png)

Pokud operace trezoru nejsou povoleny, vydejte následující příkaz pro registraci ve vašem předplatném služby Key Vault:

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault
```

Pokud registrace je úspěšná, vrátí se následující výstup:

![Registrovat](media/azure-stack-key-vault-manage-powershell/image2.png)

Při vyvolání příkazů služby key vault k chybě může dojít, jako například "předplatné není zaregistrované používání oboru názvů"Microsoft.KeyVault"." Pokud dojde k chybě, zkontrolujte, že jste povolili poskytovatele prostředků služby Key Vault podle předchozích pokynů.

## <a name="create-a-key-vault"></a>Vytvořte trezor klíčů

Než vytvoříte trezor klíčů, vytvořte skupinu prostředků, tak, aby všechny prostředky vztahující se k trezoru klíčů existovat ve skupině prostředků. Chcete-li vytvořit novou skupinu prostředků, použijte následující příkaz:

```powershell
New-AzureRmResourceGroup -Name "VaultRG" -Location local -verbose -Force
```

![Nová skupina prostředků](media/azure-stack-key-vault-manage-powershell/image3.png)

Teď pomocí **New-AzureRMKeyVault** příkaz pro vytvoření služby key vault ve skupině prostředků, kterou jste vytvořili dříve. Tento příkaz načte tři povinné parametry: název skupiny prostředků, název trezoru klíčů a zeměpisná poloha.

Spusťte následující příkaz pro vytvoření trezoru klíčů:

```powershell
New-AzureRmKeyVault -VaultName "Vault01" -ResourceGroupName "VaultRG" -Location local -verbose
```

![Nový trezor klíčů](media/azure-stack-key-vault-manage-powershell/image4.png)

Výstup tohoto příkazu zobrazuje vlastnosti trezoru klíčů, který jste vytvořili. Když aplikace přistupuje k tomuto trezoru, musíte použít **identifikátor URI trezoru** vlastnost, která je `https://vault01.vault.local.azurestack.external` v tomto příkladu.

### <a name="active-directory-federation-services-ad-fs-deployment"></a>Nasazení služby Active Directory Federation Services (AD FS)

V nasazení služby AD FS může se zobrazit tato upozornění: "Nejsou nastavené zásady přístupu. Žádný uživatel nebo aplikace nemá přístupová oprávnění k použití trezoru." Chcete-li vyřešit tento problém, nastavení zásad přístupu pro trezor s použitím [Set-AzureRmKeyVaultAccessPolicy](#authorize-an-application-to-use-a-key-or-secret) příkaz:

```powershell
# Obtain the security identifier(SID) of the active directory user
$adUser = Get-ADUser -Filter "Name -eq '{Active directory user name}'"
$objectSID = $adUser.SID.Value

# Set the key vault access policy
Set-AzureRmKeyVaultAccessPolicy -VaultName "{key vault name}" -ResourceGroupName "{resource group name}" -ObjectId "{object SID}" -PermissionsToKeys {permissionsToKeys} -PermissionsToSecrets {permissionsToSecrets} -BypassObjectIdValidation
```

## <a name="manage-keys-and-secrets"></a>Správa klíčů a tajných kódů

Po vytvoření trezoru pomocí těchto kroků můžete vytvořit a spravovat klíče a tajné klíče v trezoru.

### <a name="create-a-key"></a>Vytvoření klíče

Použití **Add-AzureKeyVaultKey** příkaz pro vytvoření nebo import softwarově chráněný klíč v trezoru klíčů:

```powershell
Add-AzureKeyVaultKey -VaultName "Vault01" -Name "Key01" -verbose -Destination Software
```

**Cílové** parametr se používá k určení, že klíč je software, které jsou chráněné. Po úspěšném vytvoření klíče, příkaz vypíše podrobnosti vytvořený klíč.

![Nový klíč](media/azure-stack-key-vault-manage-powershell/image5.png)

Vytvořený klíč teď můžete odkazovat pomocí jeho identifikátoru URI. Pokud vytvoříte nebo import klíče, který má stejný název jako existující klíč původní klíč se aktualizuje hodnoty zadané v nový klíč. Můžete získat přístup k předchozí verzi pomocí identifikátoru URI specifické pro verzi klíče. Příklad:

* Použití `https://vault10.vault.local.azurestack.external:443/keys/key01` vždy získáte aktuální verzi.
* Použití `https://vault010.vault.local.azurestack.external:443/keys/key01/d0b36ee2e3d14e9f967b8b6b1d38938a` získat tuto konkrétní verzi.

### <a name="get-a-key"></a>Získání klíče

Použití **Get-AzureKeyVaultKey** příkaz přečíst klíč a jeho podrobnosti:

```powershell
Get-AzureKeyVaultKey -VaultName "Vault01" -Name "Key01"
```

### <a name="create-a-secret"></a>Vytvoření tajného kódu

Použití **Set-AzureKeyVaultSecret** příkaz, který umožňuje vytvořit nebo aktualizovat tajný klíč v trezoru. Tajný klíč je vytvořen, pokud ještě neexistuje. Pokud již existuje, je vytvořena nová verze tajného klíče:

```powershell
$secretvalue = ConvertTo-SecureString "User@123" -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName "Vault01" -Name "Secret01" -SecretValue $secretvalue
```

![Vytvoření tajného kódu](media/azure-stack-key-vault-manage-powershell/image6.png)

### <a name="get-a-secret"></a>Získání tajného kódu

Použití **Get-AzureKeyVaultSecret** příkaz pro čtení tajného klíče v trezoru klíčů. Tento příkaz může vrátit všechny nebo konkrétní verzi tajného kódu:

```powershell
Get-AzureKeyVaultSecret -VaultName "Vault01" -Name "Secret01"
```

Po vytvoření klíče a tajné kódy, můžete povolit externí aplikací k jejich použití.

## <a name="authorize-an-application-to-use-a-key-or-secret"></a>Autorizujte aplikaci pro použití klíče nebo tajného klíče

Použití **Set-AzureRmKeyVaultAccessPolicy** příkaz autorizace aplikace pro přístup k klíče nebo tajného klíče v trezoru klíčů.

V následujícím příkladu je název trezoru *ContosoKeyVault* a má ID klienta aplikace, které chcete autorizovat *8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed*. Abyste aplikaci autorizovali, spusťte následující příkaz. Volitelně můžete zadat **PermissionsToKeys** parametr pro nastavení oprávnění pro uživatele, aplikace nebo skupinu zabezpečení.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign
```

Pokud chcete povolit tu samou aplikaci pro čtení tajných klíčů v trezoru, spusťte následující rutinu:

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300 -PermissionsToKeys Get
```

## <a name="next-steps"></a>Další postup

* [Nasazení virtuálního počítače s heslem, uložených v Key Vault](azure-stack-key-vault-deploy-vm-with-secret.md)
* [Nasazení virtuálního počítače pomocí certifikátu uloženého ve službě Key Vault](azure-stack-key-vault-push-secret-into-vm.md)
