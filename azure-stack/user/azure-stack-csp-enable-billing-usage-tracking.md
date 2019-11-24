---
title: Umožněte vašemu poskytovateli cloudového řešení spravovat vaše předplatné Azure Stack | Microsoft Docs
description: Naučte se, jak umožnit vašemu poskytovateli Cloud Solution Provider (CSP) spravovat Azure Stack předplatné za vás.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 05/20/2019
ms.openlocfilehash: a11846feb852a44a5ae526e9c060ca1626bbe245
ms.sourcegitcommit: b2d19e12a50195bb8925879ee75c186c9604f313
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71961635"
---
# <a name="let-your-cloud-solution-provider-manage-your-azure-stack-subscription"></a>Umožněte vašemu poskytovateli cloudového řešení spravovat Azure Stack předplatné.

*Platí pro: integrované systémy Azure Stack*

Pokud používáte Azure Stack s poskytovatelem Cloud Solution Provider (CSP), můžete se rozhodnout spravovat vlastní předplatné pro přístup k prostředkům v Azure a v Azure Stack. Můžete také povolit poskytovateli spravovat vaše předplatné. V tomto článku se dozvíte, jak:

* Poskytněte poskytovateli služeb přístup k vašemu předplatnému.
* Ujistěte se, že poskytovatel služeb může spravovat vaši službu.

> [!NOTE]
> Pokud CSP nespravuje váš účet a přeskočíte následující postup, CSP nemůže spravovat vaše Azure Stack předplatné za vás.

## <a name="manage-your-subscription-with-a-csp"></a>Správa předplatného pomocí CSP

Přidejte CSP jako **uživatele** do svého předplatného.

1. Přidejte své CSP jako uživatele typu host s rolí **uživatele** do vašeho adresáře tenanta. Nápovědu k přidání uživatele najdete v tématu [Přidání nových uživatelů do Azure Active Directory](/azure/active-directory/add-users-azure-active-directory).

2. CSP vytvoří místní předplatné Azure Stack za vás. Jste připraveni začít používat Azure Stack.

3. Váš CSP by měl vytvořit prostředek v rámci vašeho předplatného, aby ověřil, že může spravovat i prostředky. Například mohou [vytvořit virtuální počítač s Windows pomocí portálu Azure Stack](azure-stack-quick-windows-portal.md).

## <a name="let-the-csp-manage-your-subscription-using-rbac-rights"></a>Umožněte CSP spravovat vaše předplatné pomocí práv RBAC.

Přidejte zprostředkovatele CSP jako **vlastníka** k vašemu předplatnému.

1. Přidejte svého CSP jako uživatele typu Host do adresáře tenanta. Informace o přidání uživatele najdete v tématu [Přidání nových uživatelů do Azure Active Directory](/azure/active-directory/add-users-azure-active-directory).

2. Přidejte roli **vlastníka** k hostovanému uživateli CSP. Informace o přidání uživatele CSP k předplatnému najdete v tématu [použití Access Control na základě rolí ke správě přístupu k prostředkům předplatného Azure](/azure/role-based-access-control/role-assignments-portal). CSP vytvoří místní předplatné Azure Stack za vás. Jste připraveni začít používat Azure Stack.
3. Váš CSP by měl vytvořit prostředek ve vašem předplatném, aby ověřil, že může spravovat vaše prostředky.

## <a name="next-steps"></a>Další kroky

* Další informace o tom, jak načíst informace o využití prostředků z Azure Stack, najdete [v tématu využití a fakturace v Azure Stack](../operator/azure-stack-billing-and-chargeback.md).
