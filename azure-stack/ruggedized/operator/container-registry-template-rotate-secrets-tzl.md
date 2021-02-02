---
title: Otočení tajných kódů registru kontejneru v centru Azure Stack | Microsoft Docs
titleSuffix: Azure Stack Hub
description: Naučte se, jak na rozbočovači Azure Stack otočit tajné klíče registru kontejneru.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 2/1/2021
ms.author: mabrigg
ms.reviewer: chasat
ms.lastreviewed: 12/17/2019
ms.openlocfilehash: 3bfde6e55883e124379353302e77a439c4de0505
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99245632"
---
# <a name="rotate-container-registry-secrets-in-azure-stack-hub"></a>Otočení tajných kódů registru kontejneru v centru Azure Stack

Vaši uživatelé centra Azure Stack můžou pro nasazení šablony registru kontejneru otáčet tajné klíče (certifikáty, uživatelské jméno a heslo). Můžete spustit skript, který naplní nové tajné hodnoty v Microsoft Azure Key Vault a **znovu nasadí** existující instanci šablony registru kontejneru. Střídání tajných kódů nevyžadují nové nasazení.

## <a name="prerequisites-for-the-user"></a>Předpoklady pro uživatele

 - Uživatel bude muset mít nainstalované moduly PowerShellu Azure Stack hub. Další informace najdete v tématu [instalace PowerShellu pro Azure Stack](../../operator/azure-stack-powershell-install.md?toc=https%3A%2F%2Fdocs.microsoft.com%2Fazure-stack%2Fuser%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fazure-stack%2Fbreadcrumb%2Ftoc.json).

 - Získejte tajná klíčová okna pro šablonu registru kontejneru. Pro přístup k registru Docker můžete použít nový certifikát SSL nebo novou kombinaci uživatelského jména a hesla.

 - Získejte skripty, které se nacházejí v nástroji `\registry\scripts` po stažení souboru zip z úložiště GitHub [msazurestackworkloads/azurestack-Galerie](https://github.com/msazurestackworkloads/azurestack-gallery/archive/master.zip) .

## <a name="import-new-secrets-into-key-vault"></a>Import nových tajných kódů do Key Vault

Podle následujících pokynů nastavte nové tajné kódy v Key Vault.

### <a name="set-updated-registry-user-password-for-existing-username"></a>Nastavte aktualizované heslo uživatele registru pro existující uživatelské jméno.

1.  Otevřete příkazový řádek prostředí PowerShell se zvýšenými oprávněními a potom ho spusťte `Import-Module .\\pre-reqs.ps1` ze složky skripty.

2.  Pokud chcete aktualizovat hodnotu stávajícího uživatele registru, spusťte rutinu:

    ```powershell  
    Set-RegistryAccessSecret -KeyVaultName newregkv `
        -RegistryUserName <username> `
        -RegistryUserPassword <newpassword> `
        -SkipExistCheck $true
    ```
    Například rutina vrátí následující výstup:

    ```powershell  
    PS C:\azurestack-gallery-master\registry\Scripts> Set-RegistryAccessSecret -KeyVaultName newregkv `
        -RegistryUserName admin `
        -RegistryUserPassword password1 `
        -SkipExistCheck $true 
    
    Check if key vault secret name (admin) exists.
    Creating key vault secret name (admin) as it does not exist.
    ```

1.  Pokud chcete ověřit, jestli se pro tento záznam zadala nová hodnota, otevřete příkazový řádek PowerShellu se zvýšenými oprávněními a spusťte následující rutinu:

    ```powershell  
    Get-AzureKeyVaultSecret -VaultName newregkv -Name admin -IncludeVersions
    ```
    Například rutina vrátí následující výstup:
    ```powershell  
    PS C:\azurestack-gallery-master\registry\Scripts> Get-AzureKeyVaultSecret -VaultName newregkv -Name admin -IncludeVersions
    
    
    Vault Name   : newregkv
    Name         : admin
    Version      : 2a1495372c474cc890c888518f02b19f
    Id           : https://newregkv.vault.shanghai.azurestack.corp.microsoft.com:443/secrets/
                   admin/2a1495372c474cc890c888518f02b19f
    Enabled      : True
    Expires      : 
    Not Before   : 
    Created      : 12/18/2019 7:05:56 PM
    Updated      : 12/18/2019 7:05:56 PM
    Content Type : 
    Tags         : 
    
    Vault Name   : newregkv
    Name         : admin
    Version      : 3fd65c1719c74997984648de18a1fa0e
    Id           : https://newregkv.vault.shanghai.azurestack.corp.microsoft.com:443/secrets/
                   admin/3fd65c1719c74997984648de18a1fa0e
    Enabled      : True
    Expires      : 
    Not Before   : 
    Created      : 12/17/2019 5:05:56 AM
    Updated      : 12/17/2019 5:05:56 AM
    Content Type : user credentials
    Tags         : 
    ```

### <a name="set-new-registry-username-and-password"></a>Nastavení nového uživatelského jména a hesla v registru

1.  Otevřete příkazový řádek PowerShellu se zvýšenými oprávněními a `Import-Module .\pre-reqs.ps1` ze složky skripty.

2.  Pokud chcete vytvořit nový tajný klíč pro nové uživatelské jméno a heslo, otevřete příkazový řádek prostředí PowerShell se zvýšenými oprávněními a spusťte následující rutinu:

    ```powershell  
    Set-RegistryAccessSecret -KeyVaultName newregkv `
        -RegistryUserName <newusername> `
        -RegistryUserPassword <newpassword> 
    ```
    Například rutina vrátí následující výstup:
    ```powershell  
    PS C:\azurestack-gallery-master\registry\Scripts> Set-RegistryAccessSecret -KeyVaultName newregkv `
        -RegistryUserName admin1 `
        -RegistryUserPassword password1
    
    Check if key vault secret name (admin1) exists.
    Creating key vault secret name (admin1) as it does not exist. 
    ```

1.  Pokud chcete ověřit, jestli se vytvořil nový tajný klíč, otevřete příkazový řádek PowerShellu se zvýšenými oprávněními a spusťte následující rutinu:

    ```powershell  
    Get-AzureKeyVaultSecret -VaultName \<KeyVaultName> -Name \<username>
    ```

    Například rutina vrátí následující výstup:

    ```powershell  
    PS C:\azurestack-gallery-master\registry\Scripts> Get-AzureKeyVaultSecret -VaultName newregkv -Name admin1
    
    
    Vault Name   : newregkv
    Name         : admin1
    Version      : 2ae9a7239f4044be82ca9d1e9b80e85a
    Id           : https://newregkv.vault.shanghai.azurestack.corp.microsoft.com:443/secrets/admin1/2ae9a7239f4044be82ca9d1e9b80e85a
    Enabled      : True
    Expires      : 
    Not Before   : 
    Created      : 12/18/2019 11:28:18 PM
    Updated      : 12/18/2019 11:28:18 PM
    Content Type : user credentials
    Tags         : 
    ```

> [!Important]  
> Pokud vytváříte nový tajný klíč (kombinaci uživatelského jména a hesla), bude nutné odstranit starý Key Vault tajný klíč. Pokud znovu nasadíte stávající šablonu registru kontejnerů, aniž byste museli odstranit starý tajný klíč, budou platné kombinace původní a nové uživatelské jméno a heslo platné pro přihlášení do registru.

### <a name="update-the-ssl-certificate-for-existing-key-vault-secret"></a>Aktualizuje certifikát SSL pro existující Key Vault tajný klíč.

1. Otevřete příkazový řádek prostředí PowerShell se zvýšenými oprávněními a spusťte následující rutinu:

    ```powershell
    Set-CertificateSecret -KeyVaultName \<keyvaultname> `
     -CertificateSecretName \<originalsecretnameforcertificate> `
    Set-CertificateSecret -KeyVaultName <keyvaultname> `
        -CertificateSecretName <originalsecretnameforcertificate> `
        -CertificateFilePath <pathtonewcertificate> `
        -CertificatePassword <certificatepassword> `
        -SkipExistCheck $true
    ```

    Například rutina vrátí následující výstup:

    ```powershell  
    PS C:\azurestack-gallery-master\registry\Scripts> Set-CertificateSecret -KeyVaultName newregkv `
        -CertificateSecretName containersecret `
        -CertificateFilePath C:\crinstall\shanghairegcertnew.pfx `
        -CertificatePassword <certificatepassword> `
        -SkipExistCheck $true
    Check if key vault secret name (containersecret) exists.
    Creating key vault secret name (containersecret) as it does not exist.
    ----------------------------------------------------------------
    PFX KeyVaultResourceId       : /subscriptions/997da68a-xxxx-xxxx-ad3d-ffeac81b02dc/resourceGroups/newregreg/providers/Microsoft.KeyVault/vaults/newregkv
    PFX KeyVaultSecretUrl        : https://newregkv.vault.shanghai.azurestack.corp.microsoft.com:443/secrets/containersecret/a07ece6b9914408e8f20c516e15b66c9
    PFX Certificate Thumbprint   : 31810AA7FEF1173188691FB3F47208E5389FBA61
    ---------------------------------------------------------------- 
    ```

1.  Hodnoty vytvořené touto funkcí budete používat při opětovném nasazení existující šablony registru kontejnerů.

2.  Pokud chcete ověřit, jestli se vytvořila nová verze existujícího tajného kódu, otevřete příkazový řádek PowerShellu se zvýšenými oprávněními a spusťte následující rutinu:

    ```powershell  
    Get-AzureKeyVaultSecret -VaultName <KeyVaultName> -Name <secretname>
    ```

    Například rutina vrátí následující výstup:

    ```powershell  
    PS C:\azurestack-gallery-master\registry\Scripts> Get-AzureKeyVaultSecret -VaultName newregkv -Name containersecret -IncludeVersions
    
    
    Vault Name   : newregkv
    Name         : containersecret
    Version      : a07ece6b9914408e8f20c516e15b66c9
    Id           : https://newregkv.vault.shanghai.azurestack.corp.microsoft.com:443/secrets/containersecret/a07ece6b9914408e8f20c516e15b66c9
    Enabled      : True
    Expires      : 
    Not Before   : 
    Created      : 12/18/2019 11:46:28 PM
    Updated      : 12/18/2019 11:46:28 PM
    Content Type : 
    Tags         : 
    
    Vault Name   : newregkv
    Name         : containersecret
    Version      : 0199c7ec1d8d41bb9ddff0f39dca9931
    Id           : https://newregkv.vault.shanghai.azurestack.corp.microsoft.com:443/secrets/containersecret/0199c7ec1d8d41bb9ddff0f39dca9931
    Enabled      : True
    Expires      : 
    Not Before   : 
    Created      : 12/17/2019 5:06:03 AM
    Updated      : 12/17/2019 5:06:03 AM
    Content Type : pfx
    Tags         : 
    ```

### <a name="set-a-new-ssl-certificate-for-the-container-registry-template"></a>Nastavení nového certifikátu SSL pro šablonu registru kontejneru

1.  Otevřete příkazový řádek prostředí PowerShell se zvýšenými oprávněními a spusťte následující rutinu:

    ```powershell  
    Set-CertificateSecret -KeyVaultName <keyvaultname> `
        -CertificateSecretName <newsecretnameforcertificate> `
        -CertificateFilePath <pathtonewcertificate> `
        -CertificatePassword <certificatepassword>
    ```

    Například rutina vrátí následující výstup:

    ```powershell  
    PS C:\azurestack-gallery-master\registry\Scripts>    Set-CertificateSecret -KeyVaultName newregkv `
        -CertificateSecretName containersecret121719 `
        -CertificateFilePath C:\crinstall\shanghairegcertnew.pfx `
        -CertificatePassword <certificatepassword> 
    Check if key vault secret name (containersecret121719) exists.
    Creating key vault secret name (containersecret121719) as it does not exist.
    ----------------------------------------------------------------
    PFX KeyVaultResourceId       : /subscriptions/997da68a-xxxx-xxxx-ad3d-ffeac81b02dc/resourc
    eGroups/newregreg/providers/Microsoft.KeyVault/vaults/newregkv
    PFX KeyVaultSecretUrl        : https://newregkv.vault.shanghai.azurestack.corp.microsoft.c
    om:443/secrets/containersecret121719/bb2cfe4df7bc4fbe854a00799afa8566
    PFX Certificate Thumbprint   : 31810AA7FEF1173188691FB3F47208E5389FBA61 
    ```

## <a name="redeploy-existing-container-registry-template"></a>Znovu nasadit stávající šablonu registru kontejnerů

1. Otevřete portál Azure Stack hub User Portal.

2.  Přejděte do skupiny prostředků, kterou je nasazený virtuální počítač šablony registru kontejneru.

    ![Snímek obrazovky zobrazující skupinu prostředků, ve které je nasazený virtuální počítač šablony registru kontejneru.](./media/container-registry-template-rotating-secrets-tzl/image1.png)

3. Vyberte nasazení v části **nasazení**.

    ![Snímek obrazovky zobrazující nasazení vybrané na stránce nasazení](./media/container-registry-template-rotating-secrets-tzl/image2.png)

4.  Při prvním střídání tajných klíčů vyberte původní nasazení. Pokud se nejedná o první střídání tajných klíčů, vyberte nejnovější nasazení a pak vyberte znovu **nasadit**.

    ![Snímek obrazovky se zvýrazněnou stránkou ' Přehled ' s popisem akce ' znovu nasadit '.](./media/container-registry-template-rotating-secrets-tzl/image3.png)

5.  V části **nasadit šablonu řešení** vyberte **použít existující skupinu prostředků** a vyberte skupinu prostředků, která se použila k původnímu nasazení šablony registru kontejneru. Aby bylo opětovné nasazení úspěšné, musí používat stejnou skupinu prostředků.

    ![Snímek obrazovky zobrazující stránky nasadit šablonu řešení a parametry](./media/container-registry-template-rotating-secrets-tzl/image4.png)

6.  V **parametrech** ověřte, zda parametry odpovídají původnímu nasazení. Bude nutné přidat ID a tajný klíč objektu instančního objektu služby.

    - Pokud pouze přenášíte uživatelské jméno a heslo pro službu registru, stačí pouze přidat hlavní parametry služby.

    - Pokud certifikát přenášíte, budete muset zadat nové hodnoty pro PFXKeyVaultSecretURL a PFXThumbprint, které byly vydány jako výstup z nastavení nových tajných klíčů.

    ![Šablona registru kontejneru](./media/container-registry-template-rotating-secrets-tzl/image5.png)

7.  Vyberte **OK** a pak **vytvořit**. Opětovné nasazení bude pokračovat. Funkce registru budou během opětovného nasazení nadále fungovat.

    - Pokud předáváte uživatelské jméno a heslo, budete po dokončení opětovného nasazení muset znovu provést ověření do registru.

    - Pokud certifikát předáváte, neměli byste mít vliv na přístup k registru. To předpokládá, že používáte certifikát od poskytovatele důvěryhodného certifikátu. Pokud používáte privátní certifikát, bude nutné tento certifikát nainstalovat na klienty, aby nedošlo ke ztrátě přístupu.

## <a name="next-steps"></a>Další kroky

[Přehled Azure Stack Marketplace](../../operator/azure-stack-marketplace.md)
