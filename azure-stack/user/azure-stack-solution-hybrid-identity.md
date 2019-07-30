---
title: Konfigurace hybridní cloudové identity pomocí Azure a aplikací Azure Stack | Microsoft Docs
description: Naučte se konfigurovat hybridní cloudovou identitu pomocí aplikací Azure a Azure Stack.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/26/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 06/26/2019
ms.openlocfilehash: bc614cb514b9d35523749944486aa375bb8fce9c
ms.sourcegitcommit: 35b13ea6dc0221a15cd0840be796f4af5370ddaf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2019
ms.locfileid: "68603001"
---
# <a name="configure-hybrid-cloud-identity-for-azure-and-azure-stack-applications"></a>Konfigurace hybridní cloudové identity pro aplikace Azure a Azure Stack

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Naučte se konfigurovat hybridní cloudovou identitu pro aplikace Azure a Azure Stack.

Máte dvě možnosti, jak udělit přístup k vašim aplikacím v globálním prostředí Azure i Azure Stack.

 * Pokud Azure Stack má nepřetržité připojení k Internetu, můžete použít Azure Active Directory (Azure AD).
 * Pokud je Azure Stack odpojená od Internetu, můžete použít Azure Directory federovaného služby (AD FS).

Pomocí instančních objektů udělíte přístup k aplikacím Azure Stack pro nasazení nebo konfiguraci pomocí Azure Resource Manager v Azure Stack.

V tomto řešení sestavíte ukázkové prostředí pro:

> [!div class="checklist"]
> - Vytvoření hybridní identity v globálním Azure a Azure Stack
> - Načtěte token pro přístup k rozhraní Azure Stack API.

Pro kroky v tomto řešení musíte mít oprávnění Azure Stack operator.

> [!Tip]  
> ![hybridní pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack je rozšířením Azure. Azure Stack přináší flexibilitu a inovace cloud computingu do místního prostředí a umožňuje jenom hybridní cloud, který umožňuje vytvářet a nasazovat hybridní aplikace odkudkoli.  
> 
> Požadavky na [Návrh pro hybridní aplikace](azure-stack-edge-pattern-overview.md) kontrolují pilíře kvality softwaru (umístění, škálovatelnost, dostupnost, odolnost, možnosti správy a zabezpečení) pro navrhování, nasazování a provozování hybridních aplikací. Pokyny k návrhu pomáhají při optimalizaci návrhu hybridní aplikace a minimalizaci výzev v produkčních prostředích.


## <a name="create-a-service-principal-for-azure-ad-in-the-portal"></a>Vytvoření instančního objektu pro službu Azure AD na portálu

Pokud jste nasadili Azure Stack s využitím Azure AD jako úložiště identit, můžete objekty služby vytvářet stejně jako v případě Azure. [Použití identity aplikace pro přístup k prostředkům](../operator/azure-stack-create-service-principals.md#manage-an-azure-ad-service-principal) ukazuje, jak provést kroky prostřednictvím portálu. Před zahájením se ujistěte, že máte [požadovaná oprávnění služby Azure AD](/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) .

## <a name="create-a-service-principal-for-ad-fs-using-powershell"></a>Vytvoření instančního objektu pro AD FS s využitím PowerShellu

Pokud jste nasadili Azure Stack s AD FS, můžete k vytvoření instančního objektu použít PowerShell, přiřadit roli pro přístup a přihlásit se pomocí této identity z PowerShellu. [Použití identity aplikace pro přístup k prostředkům](../operator/azure-stack-create-service-principals.md#manage-an-ad-fs-service-principal) ukazuje, jak provést požadované kroky pomocí prostředí PowerShell.

## <a name="using-the-azure-stack-api"></a>Používání rozhraní Azure Stack API

Řešení [Azure Stack API](azure-stack-rest-api-use.md) vás provede procesem Načtení tokenu pro přístup k rozhraní Azure Stack API.

## <a name="connect-to-azure-stack-using-powershell"></a>Připojení k Azure Stack pomocí PowerShellu

Rychlý Start [k zprovoznění prostředí PowerShell v Azure Stack](../operator/azure-stack-powershell-install.md) vás provede kroky potřebnými k instalaci Azure PowerShell a připojení k instalaci Azure Stack.

### <a name="prerequisites"></a>Požadavky

Potřebujete instalaci Azure Stack připojenou k Azure Active Directory s předplatným, ke kterému máte přístup. Pokud nemáte instalaci Azure Stack, můžete k nastavení [Azure Stack Development Kit](../asdk/asdk-install.md)použít tyto pokyny.

#### <a name="connect-to-azure-stack-using-code"></a>Připojení k Azure Stack pomocí kódu

Pokud se chcete připojit k Azure Stack pomocí kódu, použijte rozhraní API pro Azure Resource Manager koncových bodů k získání koncových bodů pro ověřování a grafy pro instalaci Azure Stack a pak ověřte pomocí požadavků REST. Ukázkovou klientskou aplikaci najdete na [GitHubu](https://github.com/shriramnat/HybridARMApplication).

>[!Note]
>Pokud sada Azure SDK pro váš jazyk, kterou si vyberete, nepodporuje profily rozhraní API Azure, sada SDK nemusí v Azure Stack fungovat. Další informace o profilech rozhraní API Azure najdete v článku [Správa profilů verzí rozhraní API](azure-stack-version-profiles.md) .

## <a name="next-steps"></a>Další postup

 - Další informace o tom, jak se identita zpracovává v Azure Stack, najdete v tématu [Architektura identity pro Azure Stack](../operator/azure-stack-identity-architecture.md).
 - Další informace o vzorech cloudu Azure, najdete v článku [vzory návrhu v cloudu](https://docs.microsoft.com/azure/architecture/patterns).
