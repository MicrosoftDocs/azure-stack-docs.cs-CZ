---
title: Správa oprávnění k prostředkům na uživatele ve službě Azure Stack (Správce služeb a klientů) | Dokumentace Microsoftu
description: Jako správce služeb nebo tenanta zjistěte, jak spravovat oprávnění RBAC.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 96708a3c88b593f0d423b227d43c356ef38c1515
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985194"
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

