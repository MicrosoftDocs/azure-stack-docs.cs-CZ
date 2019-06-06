---
title: Umožní spravovat předplatné služby Azure Stack poskytovatele cloudových služeb | Dokumentace Microsoftu
description: Zjistěte, jak chcete, aby vaše cloudové služby Provider (CSP) spravovat předplatné služby Azure Stack pro vás.
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
ms.date: 05/20/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 05/20/2019
ms.openlocfilehash: 0dc162dd1d4021323f1bf80ad4f89fc721e575a9
ms.sourcegitcommit: 7f39bdc83717c27de54fe67eb23eb55dbab258a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2019
ms.locfileid: "66691987"
---
# <a name="let-your-cloud-service-provider-manage-your-azure-stack-subscription"></a>Umožní spravovat předplatné služby Azure Stack poskytovatele cloudových služeb

*Platí pro: Integrované systémy Azure Stack*

Pokud používáte Azure Stack se Cloud Service Provider (CSP), můžete ke správě svého vlastního předplatného pro přístup k prostředkům v Azure a ve službě Azure Stack. Můžete taky umožnit zprostředkovateli spravovat své předplatné pro vás. Tento článek vám ukáže, jak do:

* Udělit přístup k vaší službě poskytovatele do vašeho předplatného.
* Zkontrolujte, že poskytovatel služeb můžete spravovat vaši službu.

> [!NOTE]
> Pokud zprostředkovatel kryptografických služeb se správou vašeho účtu a můžete přeskočit následující kroky, zprostředkovatel kryptografických služeb nemůže spravovat předplatné služby Azure Stack pro vás.

## <a name="manage-your-subscription-with-a-cloud-service-provider"></a>Správa předplatného pro poskytovatele cloudových služeb

Přidat zprostředkovatele kryptografických služeb jako **uživatele** do vašeho předplatného.

1. Přidat jako uživatele typu Host pomocí poskytovatel cloudových služeb **uživatele** role do svého tenanta adresáře. Nápovědu k přidání uživatele najdete v tématu [přidání nových uživatelů do služby Azure Active Directory](/azure/active-directory/add-users-azure-active-directory).

2. Poskytovatel CSP vytvoří místní předplatnému služby Azure Stack pro vás. Jste připraveni začít používat Azure Stack.
3. Poskytovatel cloudových služeb by měl vytvořit prostředek v rámci vašeho předplatného, chcete-li ověřit, že vaše prostředky spravovat. Například může [vytvoření virtuálního počítače s Windows pomocí portálu Azure Stack](azure-stack-quick-windows-portal.md).

## <a name="let-the-cloud-service-provider-manage-your-subscription-using-rbac-rights"></a>Umožňují poskytovateli cloudové služby, spravovat své předplatné pomocí oprávnění RBAC

Přidat zprostředkovatele kryptografických služeb jako **vlastníka** do vašeho předplatného.

1. Přidání poskytovatel cloudových služeb jako uživatel typu Host do tenanta adresáře. Nápovědu k přidání uživatele najdete v tématu [přidání nových uživatelů do služby Azure Active Directory](/azure/active-directory/add-users-azure-active-directory).

2. Přidat **vlastníka** role pro uživatele typu Host CSP. Nápovědu k přidání uživatele CSP ke svému předplatnému, naleznete v tématu [Use Role-Based řízení přístupu ke správě přístupu k prostředkům předplatného Azure](/azure/role-based-access-control/role-assignments-portal). Poskytovatel CSP vytvoří místní předplatnému služby Azure Stack pro vás. Jste připraveni začít používat Azure Stack.
3. Poskytovatel cloudových služeb by měl vytvořit prostředek v rámci vašeho předplatného, chcete-li ověřit, že můžou spravovat vaše prostředky.

## <a name="next-steps"></a>Další postup

* Další informace o tom, jak načíst informace o využití prostředků ze služby Azure Stack, najdete v článku [využití a fakturace ve službě Azure Stack](../operator/azure-stack-billing-and-chargeback.md).
