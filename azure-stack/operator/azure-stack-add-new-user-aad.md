---
title: Přidat nový účet tenanta centra Azure Stack v Azure Active Directory | Microsoft Docs
description: Naučte se, jak vytvořit účet tenanta v ASDK, abyste mohli prozkoumat portál tenanta.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: a75d5c88-5b9e-4e9a-a6e3-48bbfa7069a7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/20/2019
ms.author: patricka
ms.reviewer: thoroet
ms.lastreviewed: 09/17/2018
ms.openlocfilehash: 46bb8fc18453da5775ff0b9a3b1df12cdc21ebfc
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75812310"
---
# <a name="add-a-new-azure-stack-hub-tenant-account-in-azure-active-directory"></a>Přidat nový účet tenanta centra Azure Stack v Azure Active Directory


Po [nasazení Azure Stack Development Kit](../asdk/asdk-install.md)budete potřebovat uživatelský účet tenanta, abyste mohli prozkoumat portál tenanta a testovat své nabídky a plány. Účet tenanta můžete vytvořit pomocí Azure Portal nebo pomocí prostředí PowerShell.



## <a name="create-an-azure-stack-hub-tenant-account-by-using-the-azure-portal"></a>Vytvoření účtu tenanta Azure Stackového centra pomocí Azure Portal

Abyste mohli Azure Portal používat, musíte mít předplatné Azure.

1. Přihlaste se k [Azure](https://portal.azure.com).
2. V levém navigačním panelu vyberte **Active Directory** a přejděte do adresáře, který chcete použít pro Azure Stack hub (nebo vytvořte nový).
3. Vyberte **Azure Active Directory** > **Uživatelé** > **novém uživateli**.

    ![Uživatelé – všichni uživatelé stránce zvýrazněnou nového uživatele](media/azure-stack-add-new-user-aad/new-user-all-users.png)

4. Na stránce **uživatel** vyplňte požadované informace.

    ![Přidání nového uživatele, uživatel stránka s informace o uživateli](media/azure-stack-add-new-user-aad/new-user-user.png)

   - **Název (povinné)** : jméno a příjmení nového uživatele. Například Mary Parker.
   - **Uživatelské jméno (povinné)** : uživatelské jméno nového uživatele. Například, mary@contoso.com.
       Součást domény uživatelské jméno musí používat buď počáteční výchozí název domény, <_názevvašídomény_>. onmicrosoft.com, nebo vlastní název domény, třeba contoso.com. Další informace o tom, jak vytvořit vlastní název domény, najdete v tématu [Postup přidání vlastního názvu domény do Azure AD](/azure/active-directory/fundamentals/add-custom-domain).
   - **Profil**: Volitelně můžete přidat další informace o uživateli. Později můžete také přidat informace o uživateli. Další informace o přidání informací o uživateli najdete v tématu [jak přidat nebo změnit informace o profilu uživatele](/azure/active-directory/fundamentals/active-directory-users-profile-azure-portal).
   - **Role adresáře**: vyberte možnost **uživatel**.

5. Zaškrtněte políčko **Zobrazit heslo** a zkopírujte automaticky vygenerované heslo, které jste zadali v poli **heslo** . Toto heslo budete potřebovat pro počáteční proces přihlášení.

6. Vyberte **Vytvořit**.

    Uživatel je vytvořen a přidán do vašeho tenanta Azure AD.

7. Přihlaste se k Azure Portal pomocí nového účtu. Po zobrazení výzvy změňte heslo.
8. Přihlaste se k `https://portal.local.azurestack.external` pomocí nového účtu, abyste viděli portál tenanta.

## <a name="create-an-azure-stack-hub-user-account-using-powershell"></a>Vytvoření uživatelského účtu centra Azure Stack pomocí prostředí PowerShell

Pokud nemáte předplatné Azure, nemůžete použít Azure Portal k přidání uživatelského účtu klienta. V takovém případě můžete místo toho použít modul Azure AD pro prostředí Windows PowerShell.

> [!NOTE]
> Pokud k nasazení ASDK používáte účet Microsoft, nemůžete k vytvoření účtu tenanta použít Azure AD PowerShell.

1. Nainstalujte **64** [Pomocníka pro přihlášení ke službám Microsoft Online Services pro odborníky na IT RTW](https://go.microsoft.com/fwlink/p/?LinkId=286152).

2. Nainstalujte Modul Microsoft Azure AD pro Windows PowerShell pomocí těchto kroků:

    - Otevřete příkazový řádek Windows PowerShellu se zvýšenými oprávněními (spusťte Windows PowerShell jako správce).
    - Spusťte příkaz **install-Module MSOnline** .
    - Pokud budete vyzváni k instalaci poskytovatele NuGet, vyberte **Y** a **Zadejte**.
    - Pokud budete vyzváni k instalaci modulu z PSGallery, vyberte **Y** a **Zadejte**.

3. Spusťte následující rutiny:

    ```powershell
    # Provide the Azure AD credential you use to deploy the ASDK.

            $msolcred = get-credential

    # Add a tenant account "Tenant Admin <username>@<yourdomainname>" with the initial password "<password>".

            connect-msolservice -credential $msolcred
            $user = new-msoluser -DisplayName "Tenant Admin" -UserPrincipalName <username>@<yourdomainname> -Password <password>
            Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType User -RoleMemberObjectId $user.ObjectId

    ```

1. Přihlaste se k Azure pomocí nového účtu. Po zobrazení výzvy změňte heslo.
2. Přihlaste se k `https://portal.local.azurestack.external` pomocí nového účtu, abyste viděli portál tenanta.

## <a name="next-steps"></a>Další kroky

[Přidání uživatelů centra Azure Stack v AD FS](azure-stack-add-users-adfs.md)
