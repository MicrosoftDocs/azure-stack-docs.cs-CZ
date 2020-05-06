---
title: Konfigurace hybridní cloudové identity pro Azure a aplikace Azure Stack hub
description: Naučte se konfigurovat hybridní cloudovou identitu pro aplikace Azure a Azure Stack hub.
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: f3c952695d430befa69c4b3dc2ffaf7787ac8b31
ms.sourcegitcommit: c263a86d371192e8ef2b80ced2ee0a791398cfb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2020
ms.locfileid: "82847159"
---
# <a name="configure-hybrid-cloud-identity-for-azure-and-azure-stack-hub-apps"></a>Konfigurace hybridní cloudové identity pro Azure a aplikace Azure Stack hub

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
> ![Hybrid-Pillars. png](./media/solution-deployment-guide-cross-cloud-scaling/hybrid-pillars.png)  
> Centrum Microsoft Azure Stack je rozšířením Azure. Centrum Azure Stack přináší flexibilitu a inovace cloud computingu do místního prostředí. tím se umožní jenom hybridní cloud, který umožňuje vytvářet a nasazovat hybridní aplikace odkudkoli.  
> 
> Články [týkající se návrhu hybridní aplikace](overview-app-design-considerations.md) prověří pilíře kvality softwaru (umístění, škálovatelnost, dostupnost, odolnost, možnosti správy a zabezpečení) pro navrhování, nasazování a provozování hybridních aplikací. Pokyny k návrhu pomáhají při optimalizaci návrhu hybridní aplikace a minimalizaci výzev v produkčních prostředích.

## <a name="create-a-service-principal-for-azure-ad-in-the-portal"></a>Vytvoření instančního objektu pro službu Azure AD na portálu

Pokud jste nasadili Azure Stack hub pomocí Azure AD jako úložiště identit, můžete objekty služby vytvářet stejně jako v případě Azure. [Použití identity aplikace pro přístup k prostředkům](../operator/azure-stack-create-service-principals.md#manage-an-azure-ad-service-principal) ukazuje, jak provést kroky prostřednictvím portálu. Před zahájením se ujistěte, že máte [požadovaná oprávnění služby Azure AD](/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) .

## <a name="create-a-service-principal-for-ad-fs-using-powershell"></a>Vytvoření instančního objektu pro AD FS s využitím PowerShellu

Pokud jste nasadili Azure Stack centrum s AD FS, můžete k vytvoření instančního objektu použít PowerShell a přiřazovat roli pro přístup a přihlašovat se pomocí této identity z PowerShellu. [Použití identity aplikace pro přístup k prostředkům](../operator/azure-stack-create-service-principals.md#manage-an-ad-fs-service-principal) ukazuje, jak provést požadované kroky pomocí prostředí PowerShell.

## <a name="using-the-azure-stack-hub-api"></a>Použití rozhraní API centra Azure Stack

Řešení [API centra Azure Stack](../user/azure-stack-rest-api-use.md) vás provede procesem Načtení tokenu pro přístup k rozhraní api centra Azure Stack.

## <a name="connect-to-azure-stack-hub-using-powershell"></a>Připojení k centru Azure Stack pomocí PowerShellu

Rychlý Start, [který vám umožní začít pracovat s PowerShellem v centru Azure Stack](../operator/azure-stack-powershell-install.md) , vás provede kroky potřebnými k instalaci Azure PowerShell a připojení k instalaci centra Azure Stack.

### <a name="prerequisites"></a>Požadavky

Budete potřebovat instalaci centra Azure Stack připojenou ke službě Azure AD s předplatným, ke kterému máte přístup. Pokud nemáte instalaci centra Azure Stack, můžete použít tyto pokyny k nastavení [Azure Stack Development Kit (ASDK)](../asdk/asdk-install.md).

#### <a name="connect-to-azure-stack-hub-using-code"></a>Připojení k Azure Stack centru pomocí kódu

Pokud se chcete připojit k Azure Stack centru pomocí kódu, použijte rozhraní API pro Azure Resource Manager koncových bodů k získání koncových bodů ověřování a grafu pro instalaci centra Azure Stack. Pak proveďte ověření pomocí požadavků REST. Ukázkovou klientskou aplikaci najdete na [GitHubu](https://github.com/shriramnat/HybridARMApplication).

>[!Note]
>Pokud sada Azure SDK pro váš jazyk, kterou si vyberete, nepodporuje profily rozhraní API Azure, sada SDK nemusí fungovat s Azure Stack hub. Další informace o profilech rozhraní API Azure najdete v článku [Správa profilů verzí rozhraní API](../user/azure-stack-version-profiles.md) .

## <a name="next-steps"></a>Další kroky

- Další informace o tom, jak se identita zpracovává v centru Azure Stack, najdete v tématu [Architektura identity pro centrum Azure Stack](../operator/azure-stack-identity-architecture.md).
- Další informace o vzorech cloudu Azure najdete v tématu [vzory návrhu cloudu](https://docs.microsoft.com/azure/architecture/patterns).
