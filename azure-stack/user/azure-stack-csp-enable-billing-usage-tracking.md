---
title: Umožněte vašemu poskytovateli cloudového řešení spravovat předplatné centra Azure Stack.
description: Naučte se, jak umožněte poskytovateli Cloud Solution Provider (CSP) spravovat předplatné centra Azure Stack.
author: sethmanheim
ms.topic: article
ms.date: 01/22/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 05/20/2019
ms.openlocfilehash: 95cc91a2f9443271b0e89d6dbf22a67137924627
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77704297"
---
# <a name="let-your-cloud-solution-provider-manage-your-azure-stack-hub-subscription"></a>Umožněte vašemu poskytovateli cloudového řešení spravovat předplatné centra Azure Stack.

Pokud používáte Azure Stack hub s poskytovatelem Cloud Solution Provider (CSP), můžete se rozhodnout spravovat vlastní předplatné pro přístup k prostředkům v Azure a v centru Azure Stack. Můžete také povolit poskytovateli spravovat vaše předplatné. V tomto článku se dozvíte, jak:

* Poskytněte poskytovateli služeb přístup k vašemu předplatnému.
* Ujistěte se, že poskytovatel služeb může spravovat vaši službu.

> [!NOTE]
> Pokud CSP nespravuje váš účet a přeskočíte následující postup, CSP nemůže spravovat vaše předplatné centra Azure Stack.

## <a name="manage-your-subscription-with-a-csp"></a>Správa předplatného pomocí CSP

Přidejte CSP jako **uživatele** do svého předplatného.

1. Přidejte své CSP jako uživatele typu host s rolí **uživatele** do vašeho adresáře tenanta. Nápovědu k přidání uživatele najdete v tématu [Přidání nových uživatelů do Azure Active Directory](/azure/active-directory/add-users-azure-active-directory).

2. Zprostředkovatel CSP vytvoří pro vás místní předplatné centra Azure Stack. Jste připraveni začít používat centrum Azure Stack.

3. Váš CSP by měl vytvořit prostředek v rámci vašeho předplatného, aby ověřil, že může spravovat i prostředky. Například mohou [vytvořit virtuální počítač s Windows pomocí portálu Azure Stack hub](azure-stack-quick-windows-portal.md).

## <a name="let-the-csp-manage-your-subscription-using-rbac-rights"></a>Umožněte CSP spravovat vaše předplatné pomocí práv RBAC.

Přidejte zprostředkovatele CSP jako **vlastníka** k vašemu předplatnému.

1. Přidejte svého CSP jako uživatele typu Host do adresáře tenanta. Informace o přidání uživatele najdete v tématu [Přidání nových uživatelů do Azure Active Directory](/azure/active-directory/add-users-azure-active-directory).

2. Přidejte roli **vlastníka** k hostovanému uživateli CSP. Informace o přidání uživatele CSP k předplatnému najdete v tématu [použití Access Control na základě rolí ke správě přístupu k prostředkům předplatného Azure](/azure/role-based-access-control/role-assignments-portal). Zprostředkovatel CSP vytvoří pro vás místní předplatné centra Azure Stack. Jste připraveni začít používat centrum Azure Stack.
3. Váš CSP by měl vytvořit prostředek ve vašem předplatném, aby ověřil, že může spravovat vaše prostředky.

## <a name="next-steps"></a>Další kroky

* Další informace o tom, jak načíst informace o využití prostředků z centra Azure Stack, najdete [v tématu využití a fakturace v centru Azure Stack](../operator/azure-stack-billing-and-chargeback.md).
