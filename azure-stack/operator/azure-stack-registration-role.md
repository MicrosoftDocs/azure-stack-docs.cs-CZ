---
title: Vytvoření vlastní role pro registraci centra Azure Stack
titleSuffix: Azure Stack Hub
description: Naučte se vytvořit vlastní roli, abyste se vyhnuli použití globálního správce pro registraci centra Azure Stack.
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 03/27/2020
ms.author: inhenkel
ms.reviewer: rtiberiu
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: 599191a33334e8d38989abb4e293c7361855acfa
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "80367782"
---
# <a name="create-a-custom-role-for-azure-stack-hub-registration"></a>Vytvoření vlastní role pro registraci centra Azure Stack

> [!WARNING]
> Nejedná se o funkci stav zabezpečení. Použijte ho v situacích, kdy chcete, aby omezení zabránila náhodným změnám v předplatném Azure. Když má uživatel delegovaná práva k této vlastní roli, má uživatel práva k úpravám oprávnění a oprávnění ke zvýšení oprávnění. Přiřaďte pouze uživatele, kterým důvěřujete, k vlastní roli.

Během registrace centra Azure Stack se musíte přihlásit pomocí účtu služby Azure Active Directory (Azure AD). Účet vyžaduje následující oprávnění služby Azure AD a oprávnění pro předplatné Azure:

* **Oprávnění k registraci aplikace ve vašem Tenantovi Azure AD:** Správci mají oprávnění k registraci aplikace. Oprávnění pro uživatele je globální nastavení pro všechny uživatele v tenantovi. Pokud chcete zobrazit nebo změnit nastavení, přečtěte si téma [Vytvoření aplikace a instančního objektu služby Azure AD, který má přístup k prostředkům](/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

    Nastavení *uživatel může registrovat aplikace* musí být nastavené na **Ano** , aby se povolil uživatelský účet k registraci centra Azure Stack. Pokud je nastavení registrace aplikací nastaveno na **ne**, nemůžete použít uživatelský účet k registraci centra Azure Stack – musíte použít účet globálního správce.

* **Sada dostatečných oprávnění k předplatnému Azure:** Uživatelé patřící do role vlastníka mají dostatečná oprávnění. U ostatních účtů můžete sadu oprávnění přiřadit tak, že přiřadíte vlastní roli, jak je uvedeno v následujících oddílech.

Místo použití účtu, který má oprávnění vlastníka v předplatném Azure, můžete vytvořit vlastní roli pro přiřazení oprávnění k uživatelskému účtu s méně oprávněními. Tento účet pak můžete použít k registraci centra Azure Stack.

## <a name="create-a-custom-role-using-powershell"></a>Vytvoření vlastní role pomocí prostředí PowerShell

Pokud chcete `Microsoft.Authorization/roleDefinitions/write` vytvořit vlastní roli, musíte mít oprávnění pro všechny `AssignableScopes`, jako je [vlastník](/azure/role-based-access-control/built-in-roles#owner) nebo [Správce přístupu uživatelů](/azure/role-based-access-control/built-in-roles#user-access-administrator). K zjednodušení vytvoření vlastní role použijte následující šablonu JSON. Šablona vytvoří vlastní roli, která umožňuje požadovaný přístup pro čtení a zápis pro registraci centra Azure Stack.

1. Vytvořte soubor JSON. Například `C:\CustomRoles\registrationrole.json`.
2. Přidejte do souboru následující kód JSON. `<SubscriptionID>` nahraďte ID vašeho předplatného Azure.

    ```json
    {
      "Name": "Azure Stack Hub registration role",
      "Id": null,
      "IsCustom": true,
      "Description": "Allows access to register Azure Stack Hub",
      "Actions": [
        "Microsoft.Resources/subscriptions/resourceGroups/write",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.AzureStack/registrations/*",
        "Microsoft.AzureStack/register/action",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Authorization/roleAssignments/write",
        "Microsoft.Authorization/roleAssignments/delete",
        "Microsoft.Authorization/permissions/read",
        "Microsoft.Authorization/locks/read",
        "Microsoft.Authorization/locks/write
      ],
      "NotActions": [
      ],
      "AssignableScopes": [
        "/subscriptions/<SubscriptionID>"
      ]
    }
    ```

3. V PowerShellu se připojte k Azure a použijte Azure Resource Manager. Po zobrazení výzvy proveďte ověření pomocí účtu s dostatečnými oprávněními, jako je [vlastník](/azure/role-based-access-control/built-in-roles#owner) nebo [Správce přístupu uživatele](/azure/role-based-access-control/built-in-roles#user-access-administrator).

    ```azurepowershell
    Connect-AzureRmAccount
    ```

4. Chcete-li vytvořit vlastní roli, použijte **příkaz New-AzureRmRoleDefinition** určující soubor šablony JSON.

    ``` azurepowershell
    New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\registrationrole.json"
    ```

## <a name="assign-a-user-to-registration-role"></a>Přiřazení uživatele k registrační roli

Po vytvoření vlastní role registrace přiřaďte roli uživatelskému účtu, který se použije k registraci centra Azure Stack.

1. Přihlaste se pomocí účtu s dostatečným oprávněním k předplatnému Azure, abyste mohli delegovat práva, jako je například [vlastník](/azure/role-based-access-control/built-in-roles#owner) nebo [Správce přístupu uživatelů](/azure/role-based-access-control/built-in-roles#user-access-administrator).
2. V **předplatných**vyberte **řízení přístupu (IAM) > přidat přiřazení role**.
3. V části **role**vyberte vlastní vytvořenou roli: *role registrace centra Azure Stack*.
4. Vyberte uživatele, které chcete přiřadit roli.
5. Vyberte **Save (Uložit** ) a přiřaďte vybrané uživatele k roli.

    ![Vyberte uživatele, které chcete přiřadit k vlastní roli v Azure Portal](media/azure-stack-registration-role/assign-role.png)

Další informace o používání vlastních rolí najdete v tématu [Správa přístupu pomocí RBAC a Azure Portal](/azure/role-based-access-control/role-assignments-portal).

## <a name="next-steps"></a>Další kroky

[Registrace centra Azure Stack s Azure](azure-stack-registration.md)
