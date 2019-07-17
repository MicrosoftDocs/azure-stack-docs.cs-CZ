---
title: Konfigurace hybridní Cloudová identita s aplikacemi Azure a Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak nakonfigurovat hybridní Cloudová identita s aplikacemi Azure a Azure Stack.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: solution
ms.date: 06/26/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 06/26/2019
ms.openlocfilehash: 3ed0c109e0253fe6d710801dbc30de04c0b5a6e5
ms.sourcegitcommit: 2a4cb9a21a6e0583aa8ade330dd849304df6ccb5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2019
ms.locfileid: "68286827"
---
# <a name="configure-hybrid-cloud-identity-for-azure-and-azure-stack-applications"></a>Konfigurace hybridní cloudové identity pro aplikace Azure a Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Zjistěte, jak nakonfigurovat hybridní cloudové identity pro vaše aplikace Azure a Azure Stack.

Máte dvě možnosti pro poskytování přístupu k aplikacím v globální Azure a Azure Stack.

 * Pokud má Azure Stack nepřetržité připojení k Internetu, můžete použít Azure Active Directory (Azure AD).
 * Když Azure Stack je připojený k Internetu, můžete použít Azure Directory Federated Services (AD FS).

Udělení přístupu k aplikacím Azure Stack pro nasazení nebo konfigurací pomocí Azure Resource Manageru ve službě Azure Stack pomocí instančních objektů.

V tomto řešení vytvoříte ukázkové prostředí:

> [!div class="checklist"]
> - Vytvoření hybridní identity v globální službě Azure Stack a Azure
> - Získat token pro přístup k rozhraní API služby Azure Stack.

Musíte mít oprávnění operátor Azure stacku kroky v tomto řešení.

> [!Tip]  
> ![hybridní pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack je rozšířením Azure. Azure Stack přináší flexibilitu a inovace cloud computingu do místního prostředí, povolení ten jediný hybridní cloud, který umožňuje vytvářet a nasazovat hybridní aplikace kdekoli.  
> 
> Tento článek [aspekty návrhu pro hybridní aplikace](azure-stack-edge-pattern-overview.md) kontroly pro navrhování, nasazování a provozování hybridní pilířů kvality softwaru (umístění, škálovatelnost, dostupnost, odolnost, možnosti správy a zabezpečení) aplikace. Aspekty návrhu při optimalizaci návrhu hybridních aplikací, minimalizovat problémy v produkčním prostředí.


## <a name="create-a-service-principal-for-azure-ad-in-the-portal"></a>Vytvoření instančního objektu služby pro službu Azure AD na portálu

Pokud jste nasadili Azure Stack jako úložiště identit pomocí Azure AD, můžete vytvořit instanční objekty stejně jako pro Azure. [Pomocí identity aplikace pro přístup k prostředkům](../operator/azure-stack-create-service-principals.md#manage-an-azure-ad-service-principal) se dozvíte, jak k provádění kroků na portálu. Ujistěte se, že máte [požadovaná oprávnění Azure AD](/azure/azure-resource-manager/resource-group-create-service-principal-portal#required-permissions) před zahájením.

## <a name="create-a-service-principal-for-ad-fs-using-powershell"></a>Vytvoření instančního objektu služby AD FS pomocí Powershellu

Pokud jste nasadili Azure Stack se službou AD FS, slouží k vytvoření instančního objektu, přiřazení role pro přístup a přihlásit z Powershellu pomocí tohoto identity prostředí PowerShell. [Pomocí identity aplikace pro přístup k prostředkům](../operator/azure-stack-create-service-principals.md#manage-an-ad-fs-service-principal) ukazuje, jak provést požadované kroky pomocí Powershellu.

## <a name="using-the-azure-stack-api"></a>Pomocí služby Azure Stack rozhraní API

[Rozhraní API služby Azure Stack](azure-stack-rest-api-use.md) řešení vás provede procesem načítání tokenu pro přístup k rozhraní API služby Azure Stack.

## <a name="connect-to-azure-stack-using-powershell"></a>Připojení ke službě Azure Stack pomocí Powershellu

Rychlý Start [pro uvedení do provozu pomocí prostředí PowerShell ve službě Azure Stack](../operator/azure-stack-powershell-install.md) vás provede kroky potřebné k instalaci prostředí Azure PowerShell a připojte se k instalaci sady Azure Stack.

### <a name="prerequisites"></a>Požadavky

Budete potřebovat připojení k Azure Active Directory s předplatným, máte přístup k instalaci Azure Stack. Pokud nemáte k dispozici při instalaci Azure Stack, můžete použít tyto pokyny k nastavení [Azure Stack Development Kit](../asdk/asdk-install.md).

#### <a name="connect-to-azure-stack-using-code"></a>Připojení ke službě Azure Stack pomocí kódu

Pro připojení ke službě Azure Stack pomocí kódu, použijte k získání ověřování a koncových bodů grafu pro instalaci sady Azure Stack a pak provést ověření pomocí požadavky REST koncových bodů rozhraní API Azure Resource Manageru. Ukázková klientská aplikace můžete najít na [Githubu](https://github.com/shriramnat/HybridARMApplication).

>[!Note]
>Pokud sada Azure SDK pro svůj jazyk podporuje profily rozhraní API Azure, SDK nemusí fungovat s Azure Stack. Další informace o profilech rozhraní API Azure, najdete v článku [Správa profilů verzí API](azure-stack-version-profiles.md) článku.

## <a name="next-steps"></a>Další kroky

 - Další informace o zpracování identity ve službě Azure Stack, najdete v článku [architektury identit pro službu Azure Stack](../operator/azure-stack-identity-architecture.md).
 - Další informace o vzorech cloudu Azure, najdete v článku [vzory návrhu v cloudu](https://docs.microsoft.com/azure/architecture/patterns).
