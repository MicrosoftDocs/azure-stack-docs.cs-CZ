---
title: Vytvoření vlastní role pro registraci Azure Stack
titleSuffix: Azure Stack
description: Naučte se vytvořit vlastní roli, abyste se vyhnuli použití globálního správce pro Azure Stack registraci.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2019
ms.author: patricka
ms.reviewer: rtiberiu
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: 0cfbec17b2aef1f6a14615d4b69d8a5e9347e913
ms.sourcegitcommit: 284f5316677c9a7f4c300177d0e2a905df8cb478
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2019
ms.locfileid: "74465417"
---
# <a name="create-a-custom-role-for-azure-stack-registration"></a>Vytvoření vlastní role pro registraci Azure Stack

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

> [!WARNING]
> Nejedná se o funkci stav zabezpečení. Použijte ho v situacích, kdy chcete, aby omezení zabránila náhodným změnám v předplatném Azure. Když má uživatel delegovaná práva k této vlastní roli, má uživatel práva k úpravám oprávnění a oprávnění ke zvýšení oprávnění. Přiřaďte pouze uživatele, kterým důvěřujete, k vlastní roli.

Během registrace Azure Stack se musíte přihlásit pomocí účtu služby Azure Active Directory (Azure AD). Účet vyžaduje následující oprávnění služby Azure AD a oprávnění pro předplatné Azure:

* **Oprávnění k registraci aplikace ve vašem Tenantovi Azure AD:** Správci mají oprávnění k registraci aplikace. Oprávnění pro uživatele je globální nastavení pro všechny uživatele v tenantovi. Pokud chcete zobrazit nebo změnit nastavení, přečtěte si téma [Vytvoření aplikace a instančního objektu služby Azure AD, který má přístup k prostředkům](/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

    Nastavení *uživatel může registrovat aplikace* musí být nastavené na **Ano** , pokud chcete povolit registraci Azure Stack uživatelského účtu. Pokud je nastavení registrace aplikací nastaveno na **ne**, nemůžete použít uživatelský účet k registraci Azure Stack – musíte použít globální účet správce.

* **Sada dostatečných oprávnění k předplatnému Azure:** Uživatelé patřící do role vlastníka mají dostatečná oprávnění. U ostatních účtů můžete sadu oprávnění přiřadit tak, že přiřadíte vlastní roli, jak je uvedeno v následujících oddílech.

Místo použití účtu, který má oprávnění vlastníka v předplatném Azure, můžete vytvořit vlastní roli pro přiřazení oprávnění k uživatelskému účtu s méně oprávněními. Tento účet pak můžete použít k registraci Azure Stack.

## <a name="create-a-custom-role-using-powershell"></a>Vytvoření vlastní role pomocí prostředí PowerShell

Pokud chcete vytvořit vlastní roli, musíte mít oprávnění `Microsoft.Authorization/roleDefinitions/write` pro všechny `AssignableScopes`, jako je například [vlastník](/azure/role-based-access-control/built-in-roles#owner) nebo [Správce přístupu uživatelů](/azure/role-based-access-control/built-in-roles#user-access-administrator). K zjednodušení vytvoření vlastní role použijte následující šablonu JSON. Šablona vytvoří vlastní roli, která umožňuje požadovaný přístup pro čtení a zápis pro Azure Stack registraci.

1. Vytvořte soubor JSON. Například `C:\CustomRoles\registrationrole.json`.
2. Přidejte do souboru následující kód JSON. `<SubscriptionID>` nahraďte ID vašeho předplatného Azure.

    ```json
    {
      "Name": "Azure Stack registration role",
      "Id": null,
      "IsCustom": true,
      "Description": "Allows access to register Azure Stack",
      "Actions": [
        "Microsoft.Resources/subscriptions/resourceGroups/write",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.AzureStack/registrations/*",
        "Microsoft.AzureStack/register/action",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Authorization/roleAssignments/write",
        "Microsoft.Authorization/roleAssignments/delete",
        "Microsoft.Authorization/permissions/read"
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

Po vytvoření vlastní role registrace přiřaďte roli uživatelskému účtu, který se použije k registraci Azure Stack.

1. Přihlaste se pomocí účtu s dostatečným oprávněním k předplatnému Azure, abyste mohli delegovat práva, jako je například [vlastník](/azure/role-based-access-control/built-in-roles#owner) nebo [Správce přístupu uživatelů](/azure/role-based-access-control/built-in-roles#user-access-administrator).
2. V **předplatných**vyberte **řízení přístupu (IAM) > přidat přiřazení role**.
3. V části **role**vyberte vlastní vytvořenou roli: *Azure Stack registrační roli*.
4. Vyberte uživatele, které chcete přiřadit roli.
5. Vyberte **Save (Uložit** ) a přiřaďte vybrané uživatele k roli.

    ![Vyberte uživatele, které chcete přiřadit k vlastní roli v Azure Portal](media/azure-stack-registration-role/assign-role.png)

Další informace o používání vlastních rolí najdete v tématu [Správa přístupu pomocí RBAC a Azure Portal](/azure/role-based-access-control/role-assignments-portal).

## <a name="next-steps"></a>Další kroky

[Registrace služby Azure Stack v Azure](azure-stack-registration.md)
