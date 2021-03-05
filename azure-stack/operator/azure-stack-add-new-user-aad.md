---
title: Přidání nového uživatelského účtu centra Azure Stack v Azure Active Directory
description: Naučte se, jak vytvořit uživatelský účet v Azure Active Directory, abyste mohli prozkoumat portál User Portal.
author: BryanLa
ms.topic: article
ms.date: 05/20/2019
ms.author: bryanla
ms.reviewer: thoroet
ms.lastreviewed: 09/17/2019
ms.openlocfilehash: 57b79c62de98380c97050f6c484cd5ba1b6323fd
ms.sourcegitcommit: ccc4ee05d71496653b6e27de1bb12e4347e20ba4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102231366"
---
# <a name="add-a-new-azure-stack-hub-user-account-in-azure-active-directory-azure-ad"></a>Přidání nového uživatelského účtu centra Azure Stack v Azure Active Directory (Azure AD)

Než budete moct testovat nabídky a plány a vytvářet prostředky, budete potřebovat uživatelský účet. Uživatelský účet vytvoříte v tenantovi Azure AD pomocí Azure Portal nebo PowerShellu.

## <a name="create-user-account-using-the-azure-portal"></a>Vytvoření uživatelského účtu pomocí Azure Portal

Abyste mohli Azure Portal používat, musíte mít předplatné Azure.

1. Přihlaste se k [Azure](https://portal.azure.com).
2. V levém navigačním panelu vyberte **Active Directory** a přejděte do adresáře, který chcete použít pro Azure Stack hub (nebo vytvořte nový).
3. Vyberte **Azure Active Directory**  >  **Uživatelé**  >  **Nový uživatel**.

    ![Uživatelé – stránka všichni uživatelé s vybraným novým uživatelem](media/azure-stack-add-new-user-aad/new-user-all-users.png)

4. Na stránce **uživatel** vyplňte požadované informace.

    ![Přidání nového uživatele, stránky uživatele s informacemi o uživateli](media/azure-stack-add-new-user-aad/new-user-user.png)

   - **Název (povinné)**: jméno a příjmení nového uživatele. Například Marie Parker.
   - **Uživatelské jméno (povinné)**: uživatelské jméno nového uživatele. Například, mary@contoso.com.
       Část domény uživatelského jména musí používat počáteční výchozí název domény, <_názevvašídomény_>. onmicrosoft.com, nebo vlastní název domény, jako je contoso.com. Další informace o tom, jak vytvořit vlastní název domény, najdete v tématu [Postup přidání vlastního názvu domény do Azure AD](/azure/active-directory/fundamentals/add-custom-domain).
   - **Profil**: Volitelně můžete přidat další informace o uživateli. Později můžete také přidat informace o uživateli. Další informace o přidání informací o uživateli najdete v tématu [jak přidat nebo změnit informace o profilu uživatele](/azure/active-directory/fundamentals/active-directory-users-profile-azure-portal).
   - **Role adresáře**: vyberte možnost **uživatel**.

5. Zaškrtněte políčko **Zobrazit heslo** a zkopírujte automaticky vygenerované heslo, které jste zadali v poli **heslo** . Toto heslo budete potřebovat pro počáteční proces přihlášení.

6. Vyberte **Vytvořit**.

    Uživatel se vytvoří a přidá se do vašeho tenanta Azure AD.

7. Přihlaste se k Azure Portal pomocí nového účtu. Po zobrazení výzvy změňte heslo.
8. Přihlaste se k `https://portal.local.azurestack.external` novému účtu, abyste viděli portál User Portal.

## <a name="create-a-user-account-using-powershell"></a>Vytvoření uživatelského účtu pomocí prostředí PowerShell

Pokud nemáte předplatné Azure, nemůžete použít Azure Portal k přidání uživatelského účtu klienta. V takovém případě můžete místo toho použít modul Azure AD pro prostředí Windows PowerShell.

> [!NOTE]
> Pokud k nasazení ASDK používáte účet Microsoft, nemůžete k vytvoření účtu tenanta použít Azure AD PowerShell.

1. Nainstalujte **64** [Pomocníka pro přihlášení ke službám Microsoft Online Services pro odborníky na IT RTW](https://www.microsoft.com/download/details.aspx?id=28177).

2. Nainstalujte Modul Microsoft Azure AD pro Windows PowerShell pomocí těchto kroků:

    - Otevřete příkazový řádek Windows PowerShellu se zvýšenými oprávněními (spusťte Windows PowerShell jako správce).
    - Spusťte příkaz **install-Module MSOnline** .
    - Pokud budete vyzváni k instalaci poskytovatele NuGet, vyberte **Y** a **Zadejte**.
    - Pokud budete vyzváni k instalaci modulu z PSGallery, vyberte **Y** a **Zadejte**.

3. Spusťte následující rutiny:

    ```powershell
    # Provide the Azure AD credential you use to deploy the ASDK.

            $msolcred = get-credential

    # Add a user account "Tenant Admin <username>@<yourdomainname>" with the initial password "<password>".

            connect-msolservice -credential $msolcred
            $user = new-msoluser -DisplayName "Tenant Admin" -UserPrincipalName <username>@<yourdomainname> -Password <password>
            Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType User -RoleMemberObjectId $user.ObjectId

    ```

1. Přihlaste se k Azure pomocí nového účtu. Po zobrazení výzvy změňte heslo.
2. Přihlaste se k `https://portal.local.azurestack.external` novému účtu, abyste viděli portál User Portal.

## <a name="next-steps"></a>Další kroky

[Přidání uživatelů centra Azure Stack v AD FS](azure-stack-add-users-adfs.md)
