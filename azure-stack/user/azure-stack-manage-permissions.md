---
title: Správa přístupu k prostředkům v Azure Stack pomocí řízení přístupu na základě role | Microsoft Docs
description: Naučte se spravovat oprávnění řízení přístupu na základě role (RBAC) jako správce nebo tenanta v Azure Stack.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2019
ms.author: bryanla
ms.reviewer: fiseraci
ms.lastreviewed: 03/11/2019
ms.openlocfilehash: a784da0d16f6ec92a105d9360430f4e8da2817ef
ms.sourcegitcommit: 245a4054a52e54d5989d6148fbbe386e1b2aa49c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2019
ms.locfileid: "70974932"
---
# <a name="manage-access-to-resources-in-azure-stack-with-role-based-access-control"></a>Správa přístupu k prostředkům v Azure Stack pomocí řízení přístupu na základě role

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Azure Stack podporuje řízení přístupu na základě role (RBAC), stejné [model zabezpečení pro správu přístupu](/azure/role-based-access-control/overview) používající Microsoft Azure. RBAC můžete použít ke správě uživatelů, skupin nebo přístupu aplikací k předplatným, prostředkům a službám.

## <a name="basics-of-access-management"></a>Základní informace o řízení přístupu

Řízení přístupu na základě role (RBAC) poskytuje jemně odstupňované řízení přístupu, které můžete použít k zabezpečení svého prostředí. Uživatelům dáte přesná oprávnění, která potřebují, přiřazením role RBAC v určitém oboru. Obor přiřazení role může být předplatné, skupinu prostředků nebo jediný prostředek. Podrobnější informace o správě přístupu najdete v tématu [Access Control na základě rolí v článku Azure Portal](/azure/role-based-access-control/overview) .

> [!NOTE]
> Když Azure Stack nasadíte pomocí Active Directory Federation Services (AD FS) jako poskytovatele identity, pro scénáře RBAC se podporují jenom univerzální skupiny.

### <a name="built-in-roles"></a>Vestavěné role

Azure Stack má tři základní role, které můžete použít na všechny typy prostředků:

* **Vlastník**: může spravovat všechno, včetně přístupu k prostředkům.
* **Přispěvatel**: může spravovat všechno, s výjimkou přístupu k prostředkům.
* **Čtecí modul**: může zobrazit vše, ale nemůže provádět žádné změny.

### <a name="resource-hierarchy-and-inheritance"></a>Hierarchie prostředků a dědičnost

Azure Stack má následující hierarchie prostředků:

* Každé předplatné patří k jednomu adresáři.
* Každé skupině prostředků patří k jednomu předplatnému.
* Každý prostředek patří do jedné skupiny prostředků.

V podřízené obory dědí přístup, který udělíte v nadřazeném oboru. Příklad:

* Můžete přiřadit **čtečky** role ke skupině Azure AD v oboru předplatného. Členové této skupiny můžou zobrazit každou skupinu prostředků a prostředků v předplatném.
* Roli **Přispěvatel** přiřadíte aplikaci v oboru skupiny prostředků. Aplikace může spravovat prostředky všech typů v této skupině prostředků, ale ne jiné skupiny prostředků v rámci předplatného.

### <a name="assigning-roles"></a>Přiřazení rolí

Můžete přiřadit více než jednu roli pro uživatele a každou roli je možné přidružit jiný obor. Příklad:

* Přiřadíte TestUser-A roli **Čtenář** k předplatnému-1.
* Přiřadíte hodnotu TestUser-A **vlastníkem** role TestVM-1.

Azure [přiřazení rolí](/azure/role-based-access-control/role-assignments-portal) článek obsahuje podrobné informace o zobrazení, přiřazování a odstranění rolí.

## <a name="set-access-permissions-for-a-user"></a>Nastavte přístupová oprávnění pro uživatele

Následující kroky popisují, jak nakonfigurovat oprávnění pro uživatele.

1. Přihlaste se pomocí účtu, který má oprávnění vlastníka prostředku, který chcete spravovat.
2. V levém navigačním podokně zvolte **Skupiny prostředků**.
3. Zvolte název, který chcete nastavit oprávnění pro skupinu prostředků.
4. V navigačním podokně skupiny prostředků zvolte **řízení přístupu (IAM)** .<BR> **Přiřazení rolí** zobrazení obsahuje seznam položek, které mají přístup do skupiny prostředků. Můžete filtrovat a seskupení výsledků.
5. Na řádku nabídek **řízení přístupu** vyberte **Přidat**.
6. V podokně **Přidat oprávnění** :

   * Zvolte roli, kterou chcete přiřadit z **Role** rozevíracího seznamu.
   * Vyberte prostředek, kterou chcete přiřadit z **přiřadit přístup k** rozevíracího seznamu.
   * V adresáři vyberte uživatele, skupinu nebo aplikaci, ke kterým chcete udělit přístup. V adresáři můžete vyhledávat pomocí zobrazovaných názvů, e-mailových adres a identifikátorů objektů.

7. Vyberte **Uložit**.

## <a name="next-steps"></a>Další postup

[Vytvoření instančních objektů](../operator/azure-stack-create-service-principals.md)
