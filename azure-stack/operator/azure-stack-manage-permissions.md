---
title: Správa oprávnění k prostředkům na uživatele ve službě Azure Stack (Správce služeb a klientů) | Dokumentace Microsoftu
description: Jako správce služeb nebo tenanta zjistěte, jak spravovat oprávnění RBAC.
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
ms.openlocfilehash: 9fc4e6bb48c8cdd7508a1a85d193a22761c62e99
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/28/2019
ms.locfileid: "66268588"
---
# <a name="manage-role-based-access-control"></a>Správa řízení přístupu na základě rolí

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Uživatel ve službě Azure Stack může být čtečky, vlastníka nebo přispěvatele pro každou instanci tohoto předplatného, skupinu prostředků nebo služeb. Například uživatel A může mít oprávnění Čtenář předplatného k jednomu, ale máte oprávnění vlastníka pro virtuální počítač sedm.

 - Čtenář: Uživatel může vše zobrazit, ale nemůže provádět žádné změny.
 - Přispěvatel: Uživatel může spravovat všechno kromě přístupu k prostředkům.
 - Vlastník: Uživatel může spravovat všechno včetně přístupu k prostředkům.

## <a name="set-access-permissions-for-a-user"></a>Nastavte přístupová oprávnění pro uživatele

1. Přihlaste se pomocí účtu, který má oprávnění vlastníka prostředku, který chcete spravovat.
2. V okně pro prostředek, klikněte **přístup** ikonu ![](media/azure-stack-manage-permissions/image1.png).
3. V **uživatelé** okna, klikněte na tlačítko **role**.
4. V **role** okna, klikněte na tlačítko **přidat** přidání oprávnění pro uživatele.

## <a name="set-access-permissions-for-a-universal-group"></a>Nastavte přístupová oprávnění pro univerzální skupinu 

> [!Note]
> Lze použít pouze na služby Active Directory Federated Services (AD FS).

1. Přihlaste se pomocí účtu, který má oprávnění vlastníka prostředku, který chcete spravovat.
2. V okně pro prostředek, klikněte **přístup** ikonu ![](media/azure-stack-manage-permissions/image1.png).
3. V **uživatelé** okna, klikněte na tlačítko **role**.
4. V **role** okna, klikněte na tlačítko **přidat** přidání oprávnění pro univerzální skupiny skupiny služby Active Directory.

## <a name="next-steps"></a>Další postup

[Přidání tenanta Azure Stacku](azure-stack-add-new-user-aad.md)