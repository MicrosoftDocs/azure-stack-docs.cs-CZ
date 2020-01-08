---
title: Nastavení oprávnění k přístupu pomocí řízení přístupu na základě role | Microsoft Docs
description: Naučte se, jak nastavit přístupová oprávnění pomocí řízení přístupu na základě role (RBAC) v Azure Stack.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/23/2019
ms.author: justinha
ms.reviewer: thoroet
ms.lastreviewed: 12/23/2019
ms.openlocfilehash: ae5b167ca807fc1e28c13c553e13ea89c7e6fe49
ms.sourcegitcommit: dc3d0b77ee77742525fa0cd72d8547d25393022f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/26/2019
ms.locfileid: "75492782"
---
# <a name="set-access-permissions-using-role-based-access-control"></a>Nastavení oprávnění k přístupu pomocí řízení přístupu na základě role

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Uživatel ve službě Azure Stack může být čtenářem, vlastníkem nebo přispěvatelem každé instance předplatného, skupiny prostředků nebo služby. Například uživatel A může mít oprávnění čtenáře k jednomu předplatnému, ale má oprávnění vlastníka k virtuálnímu počítači 7.

 - Čtecí modul: uživatel může zobrazit vše, ale nemůže provádět žádné změny.
 - Přispěvatel: uživatel může spravovat všechno kromě přístupu k prostředkům.
 - Vlastník: uživatel může spravovat všechno, včetně přístupu k prostředkům.
 - Vlastní: uživatel má omezený konkrétní přístup k prostředkům.

 Další informace o vytvoření vlastní role najdete v tématu [vlastní role pro prostředky Azure](https://docs.microsoft.com/azure/role-based-access-control/custom-roles).

## <a name="set-access-permissions-for-a-user"></a>Nastavte přístupová oprávnění pro uživatele

1. Přihlaste se pomocí účtu, který má oprávnění vlastníka prostředku, který chcete spravovat.
2. V okně prostředku klikněte na ikonu **přístupu** ![](media/azure-stack-manage-permissions/image1.png).
3. V okně **Uživatelé** klikněte na **role**.
4. V okně **role** kliknutím na **Přidat** přidejte oprávnění pro uživatele.

## <a name="set-access-permissions-for-a-universal-group"></a>Nastavení přístupových oprávnění pro univerzální skupinu 

> [!Note]
> Platí jenom pro federované služby Active Directory (AD FS).

1. Přihlaste se pomocí účtu, který má oprávnění vlastníka prostředku, který chcete spravovat.
2. V okně prostředku klikněte na ikonu **přístupu** ![](media/azure-stack-manage-permissions/image1.png).
3. V okně **Uživatelé** klikněte na **role**.
4. V okně **role** kliknutím na **Přidat** přidejte oprávnění pro skupinu služby Active Directory univerzální skupiny.

## <a name="next-steps"></a>Další kroky

[Přidání tenanta Azure Stacku](azure-stack-add-new-user-aad.md)