---
title: Konfigurace hybridní cloudové identity pro Azure a aplikace Azure Stack hub
description: Naučte se konfigurovat hybridní cloudovou identitu pro aplikace Azure a Azure Stack hub.
author: BryanLa
ms.service: azure-stack
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 813dbe66b2b2c1850430dbe167f0a14227856aee
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75881480"
---
# <a name="configure-hybrid-cloud-identity-for-azure-and-azure-stack-hub-applications"></a>Konfigurace hybridní cloudové identity pro Azure a Azure Stack aplikace centra

Naučte se konfigurovat hybridní cloudovou identitu pro aplikace Azure a Azure Stack hub.

Máte dvě možnosti, jak udělit přístup k vašim aplikacím v globálním centru Azure i Azure Stack.

 * Když Azure Stack hub má nepřetržité připojení k Internetu, můžete použít Azure Active Directory (Azure AD).
 * Když je rozbočovač Azure Stack odpojený od Internetu, můžete použít Azure Directory federovaného služby (AD FS).

Pomocí instančních objektů udělíte přístup k aplikacím centra Azure Stack pro nasazení nebo konfiguraci pomocí Azure Resource Manager v centru Azure Stack.

V tomto řešení sestavíte ukázkové prostředí pro:

> [!div class="checklist"]
> - Vytvoření hybridní identity v globálním centru Azure a Azure Stack
> - Načtěte token pro přístup k rozhraní API centra Azure Stack.

Pro kroky v tomto řešení musíte mít oprávnění operátora centra Azure Stack.

> [!Tip]  
> ![hybridní pillars.png](./media/solution-deployment-guide-cross-cloud-scaling/hybrid-pillars.png)  
> Centrum Microsoft Azure Stack je rozšířením Azure. Centrum Azure Stack přináší flexibilitu a inovace cloud computingu do místního prostředí. tím se umožní jenom hybridní cloud, který umožňuje vytvářet a nasazovat hybridní aplikace odkudkoli.  
> 
> Požadavky na [Návrh pro hybridní aplikace](overview-app-design-considerations.md) kontrolují pilíře kvality softwaru (umístění, škálovatelnost, dostupnost, odolnost, možnosti správy a zabezpečení) pro navrhování, nasazování a provozování hybridních aplikací. Pokyny k návrhu pomáhají při optimalizaci návrhu hybridní aplikace a minimalizaci výzev v produkčních prostředích.


## <a name="create-a-service-principal-for-azure-ad-in-the-portal"></a>Vytvoření instančního objektu pro službu Azure AD na portálu

Pokud jste nasadili Azure Stack hub pomocí Azure AD jako úložiště identit, můžete objekty služby vytvářet stejně jako v případě Azure. [Použití identity aplikace pro přístup k prostředkům](../operator/azure-stack-create-service-principals.md#manage-an-azure-ad-service-principal) ukazuje, jak provést kroky prostřednictvím portálu. Před zahájením se ujistěte, že máte [požadovaná oprávnění služby Azure AD](/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) .

## <a name="create-a-service-principal-for-ad-fs-using-powershell"></a>Vytvoření instančního objektu pro AD FS s využitím PowerShellu

Pokud jste nasadili Azure Stack centrum s AD FS, můžete k vytvoření instančního objektu použít PowerShell a přiřazovat roli pro přístup a přihlašovat se pomocí této identity z PowerShellu. [Použití identity aplikace pro přístup k prostředkům](../operator/azure-stack-create-service-principals.md#manage-an-ad-fs-service-principal) ukazuje, jak provést požadované kroky pomocí prostředí PowerShell.

## <a name="using-the-azure-stack-hub-api"></a>Použití rozhraní API centra Azure Stack

Řešení [API centra Azure Stack](../user/azure-stack-rest-api-use.md) vás provede procesem Načtení tokenu pro přístup k rozhraní api centra Azure Stack.

## <a name="connect-to-azure-stack-hub-using-powershell"></a>Připojení k centru Azure Stack pomocí PowerShellu

Rychlý Start, [který vám umožní začít pracovat s PowerShellem v centru Azure Stack](../operator/azure-stack-powershell-install.md) , vás provede kroky potřebnými k instalaci Azure PowerShell a připojení k instalaci centra Azure Stack.

### <a name="prerequisites"></a>Požadavky

Potřebujete, aby byla instalace centra Azure Stack připojená k Azure Active Directory s předplatným, ke kterému máte přístup. Pokud nemáte instalaci centra Azure Stack, můžete tyto pokyny použít k nastavení [vývojové sady Azure Stack hub](../asdk/asdk-install.md).

#### <a name="connect-to-azure-stack-hub-using-code"></a>Připojení k Azure Stack centru pomocí kódu

Pokud se chcete připojit k Azure Stack centru pomocí kódu, použijte rozhraní API pro Azure Resource Manager koncových bodů k získání koncových bodů ověřování a grafu pro vaši instalaci centra Azure Stack a pak ověřování pomocí požadavků REST. Ukázkovou klientskou aplikaci najdete na [GitHubu](https://github.com/shriramnat/HybridARMApplication).

>[!Note]
>Pokud sada Azure SDK pro váš jazyk, kterou si vyberete, nepodporuje profily rozhraní API Azure, sada SDK nemusí fungovat s Azure Stack hub. Další informace o profilech rozhraní API Azure najdete v článku [Správa profilů verzí rozhraní API](../user/azure-stack-version-profiles.md) .

## <a name="next-steps"></a>Další kroky

 - Další informace o tom, jak se identita zpracovává v centru Azure Stack, najdete v tématu [Architektura identity pro centrum Azure Stack](../operator/azure-stack-identity-architecture.md).
 - Další informace o vzorech cloudu Azure, najdete v článku [vzory návrhu v cloudu](https://docs.microsoft.com/azure/architecture/patterns).
