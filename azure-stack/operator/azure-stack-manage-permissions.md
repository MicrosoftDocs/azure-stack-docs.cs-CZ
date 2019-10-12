---
title: Nastavení oprávnění k přístupu pomocí řízení přístupu na základě role | Microsoft Docs
description: Naučte se, jak nastavit přístupová oprávnění pomocí řízení přístupu na základě role (RBAC) v Azure Stack.
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
ms.date: 05/16/2019
ms.author: patricka
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: fa4e836a2c7cd5b59a6234a05efcc1cface12620
ms.sourcegitcommit: a6d47164c13f651c54ea0986d825e637e1f77018
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72277052"
---
# <a name="set-access-permissions-using-role-based-access-control"></a>Nastavení oprávnění k přístupu pomocí řízení přístupu na základě role

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Uživatel v Azure Stack může být čtenářem, vlastníkem nebo přispěvatelem pro každou instanci předplatného, skupiny prostředků nebo služby. Například uživatel A může mít oprávnění čtenáře k jednomu předplatnému, ale má oprávnění vlastníka k virtuálnímu počítači 7.

 - Čtecí modul: uživatel může zobrazit vše, ale nemůže provádět žádné změny.
 - Přispěvatel: uživatel může spravovat všechno kromě přístupu k prostředkům.
 - Vlastník: uživatel může spravovat všechno, včetně přístupu k prostředkům.

## <a name="set-access-permissions-for-a-user"></a>Nastavení přístupových oprávnění pro uživatele

1. Přihlaste se pomocí účtu, který má oprávnění vlastníka k prostředku, který chcete spravovat.
2. V okně prostředku klikněte na ikonu **přístupu** ![](media/azure-stack-manage-permissions/image1.png).
3. V okně **Uživatelé** klikněte na **role**.
4. V okně **role** kliknutím na **Přidat** přidejte oprávnění pro uživatele.

## <a name="set-access-permissions-for-a-universal-group"></a>Nastavení přístupových oprávnění pro univerzální skupinu 

> [!Note]
> Platí jenom pro federované služby Active Directory (AD FS).

1. Přihlaste se pomocí účtu, který má oprávnění vlastníka k prostředku, který chcete spravovat.
2. V okně prostředku klikněte na ikonu **přístupu** ![](media/azure-stack-manage-permissions/image1.png).
3. V okně **Uživatelé** klikněte na **role**.
4. V okně **role** kliknutím na **Přidat** přidejte oprávnění pro skupinu služby Active Directory univerzální skupiny.

## <a name="next-steps"></a>Další kroky

[Přidat tenanta Azure Stack](azure-stack-add-new-user-aad.md)