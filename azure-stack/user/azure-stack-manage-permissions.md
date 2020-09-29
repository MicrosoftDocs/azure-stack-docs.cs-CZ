---
title: Správa přístupu k prostředkům v Azure Stackovém centru pomocí řízení přístupu na základě role
description: Naučte se spravovat oprávnění řízení přístupu na základě role (RBAC) jako správce nebo tenanta v centru Azure Stack.
author: bryanla
ms.topic: article
ms.date: 09/13/2019
ms.author: bryanla
ms.reviewer: fiseraci
ms.lastreviewed: 03/11/2019
ms.openlocfilehash: 1002f45b51f4a70ddbadb8230df6e98f591fcbb6
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90573747"
---
# <a name="manage-access-to-resources-in-azure-stack-hub-with-role-based-access-control"></a>Správa přístupu k prostředkům v centru Azure Stack s řízením přístupu na základě role

Centrum Azure Stack podporuje řízení přístupu na základě role (RBAC), což je stejný [model zabezpečení pro správu přístupu](/azure/role-based-access-control/overview) , který Microsoft Azure používá. RBAC můžete použít ke správě uživatelů, skupin nebo přístupu aplikací k předplatným, prostředkům a službám.

## <a name="basics-of-access-management"></a>Základy správy přístupu

Řízení přístupu na základě role (RBAC) poskytuje jemně odstupňované řízení přístupu, které můžete použít k zabezpečení svého prostředí. Uživatelům dáte přesná oprávnění, která potřebují, přiřazením role RBAC v určitém oboru. Oborem přiřazení role může být předplatné, skupina prostředků nebo jeden prostředek. Podrobnější informace o správě přístupu najdete v tématu [Access Control na základě rolí v článku Azure Portal](/azure/role-based-access-control/overview) .

> [!NOTE]
> Když se Azure Stack centrum nasazuje pomocí Active Directory Federation Services (AD FS) jako poskytovatel identity, pro scénáře RBAC se podporují jenom univerzální skupiny.

### <a name="built-in-roles"></a>Vestavěné role

Centrum Azure Stack má tři základní role, které se dají použít pro všechny typy prostředků:

* **Vlastník**: může spravovat všechno, včetně přístupu k prostředkům.
* **Přispěvatel**: může spravovat všechno, s výjimkou přístupu k prostředkům.
* **Čtecí modul**: může zobrazit vše, ale nemůže provádět žádné změny.

### <a name="resource-hierarchy-and-inheritance"></a>Hierarchie a dědičnost prostředků

Centrum Azure Stack má následující hierarchii prostředků:

* Každé předplatné patří do jednoho adresáře.
* Každá skupina prostředků patří k jednomu předplatnému.
* Každý prostředek patří do jedné skupiny prostředků.

Přístup, který udělíte v nadřazeném oboru, je zděděný v podřízených oborech. Příklad:

* Roli **Čtenář** přiřadíte ke skupině Azure AD v oboru předplatného. Členové této skupiny mohou zobrazit všechny skupiny prostředků a prostředky v rámci předplatného.
* Roli **Přispěvatel** přiřadíte aplikaci v oboru skupiny prostředků. Aplikace může spravovat prostředky všech typů v této skupině prostředků, ale ne jiné skupiny prostředků v rámci předplatného.

### <a name="assigning-roles"></a>Přiřazování rolí

Uživateli můžete přiřadit více než jednu roli a Každá role může být přidružená k jinému oboru. Příklad:

* Přiřadíte TestUser-A roli **Čtenář** k předplatnému-1.
* Přiřadíte hodnotu TestUser-A **vlastníkem** role TestVM-1.

Článek [přiřazení rolí](/azure/role-based-access-control/role-assignments-portal) Azure poskytuje podrobné informace o zobrazení, přiřazení a odstraňování rolí.

## <a name="set-access-permissions-for-a-user"></a>Nastavení přístupových oprávnění pro uživatele

Následující postup popisuje, jak nakonfigurovat oprávnění pro uživatele.

1. Přihlaste se pomocí účtu, který má oprávnění vlastníka k prostředku, který chcete spravovat.
2. V levém navigačním podokně zvolte **Skupiny prostředků**.
3. Vyberte název skupiny prostředků, pro kterou chcete nastavit oprávnění.
4. V navigačním podokně skupiny prostředků vyberte **řízení přístupu (IAM)**.<BR> Zobrazení **přiřazení rolí** obsahuje seznam položek, které mají přístup ke skupině prostředků. Výsledky můžete filtrovat a seskupovat.
5. Na řádku nabídek **řízení přístupu** vyberte **Přidat**.
6. V podokně **Přidat oprávnění** :

   * Z rozevíracího seznamu **role** vyberte roli, kterou chcete přiřadit.
   * V rozevíracím seznamu **přiřadit přístup k** vyberte prostředek, který chcete přiřadit.
   * V adresáři vyberte uživatele, skupinu nebo aplikaci, ke kterým chcete udělit přístup. V adresáři můžete vyhledávat pomocí zobrazovaných názvů, e-mailových adres a identifikátorů objektů.

7. Vyberte **Uložit**.

## <a name="next-steps"></a>Další kroky

[Vytvoření instančních objektů](../operator/azure-stack-create-service-principals.md)
