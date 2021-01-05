---
title: Nastavení oprávnění k přístupu pomocí řízení přístupu na základě role
description: Naučte se, jak nastavit přístupová oprávnění pomocí řízení přístupu na základě role (RBAC) v centru Azure Stack.
author: PatAltimore
ms.topic: article
ms.date: 12/23/2019
ms.author: patricka
ms.reviewer: thoroet
ms.lastreviewed: 12/23/2019
ms.openlocfilehash: 852aa0283e3bd88a196c4bcf9e56951142bc4d60
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97870116"
---
# <a name="set-access-permissions-using-role-based-access-control"></a>Nastavení oprávnění k přístupu pomocí řízení přístupu na základě role

Uživatel v centru Azure Stack může být čtenářem, vlastníkem nebo přispěvatelem pro každou instanci předplatného, skupiny prostředků nebo služby. Například uživatel A může mít oprávnění čtenáře k jednomu předplatnému, ale má oprávnění vlastníka k virtuálnímu počítači 7.

 - Čtecí modul: uživatel může zobrazit vše, ale nemůže provádět žádné změny.
 - Přispěvatel: uživatel může spravovat všechno kromě přístupu k prostředkům.
 - Vlastník: uživatel může spravovat všechno, včetně přístupu k prostředkům.
 - Vlastní: uživatel má omezený konkrétní přístup k prostředkům.

 Další informace o vytvoření vlastní role najdete v tématu [vlastní role pro prostředky Azure](/azure/role-based-access-control/custom-roles).

## <a name="set-access-permissions-for-a-user"></a>Nastavení přístupových oprávnění pro uživatele

1. Přihlaste se pomocí účtu, který má oprávnění vlastníka k prostředku, který chcete spravovat.
2. V okně pro daný prostředek klikněte na ikonu **přístupu** ![ . ikona přístupu je osnovou vedoucího a plece dvou lidí. ](media/azure-stack-manage-permissions/image1.png)
3. V okně **Uživatelé** klikněte na **role**.
4. V okně **role** kliknutím na **Přidat** přidejte oprávnění pro uživatele.

## <a name="set-access-permissions-for-a-universal-group"></a>Nastavení přístupových oprávnění pro univerzální skupinu 

> [!Note]
> Platí jenom pro federované služby Active Directory (AD FS).

1. Přihlaste se pomocí účtu, který má oprávnění vlastníka k prostředku, který chcete spravovat.
2. V okně pro daný prostředek klikněte na ikonu **přístupu** ![ . ikona přístupu je osnovou vedoucího a plece dvou lidí. ](media/azure-stack-manage-permissions/image1.png)
3. V okně **Uživatelé** klikněte na **role**.
4. V okně **role** kliknutím na **Přidat** přidejte oprávnění pro skupinu služby Active Directory univerzální skupiny.

## <a name="next-steps"></a>Další kroky

[Přidat tenanta centra Azure Stack](azure-stack-add-new-user-aad.md)
