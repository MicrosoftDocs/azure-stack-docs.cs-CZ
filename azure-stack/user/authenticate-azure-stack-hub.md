---
title: Jak získat ověřovací informace pro centrum Azure Stack
description: Informace o tom, jak získat ověřovací informace pro centrum Azure Stack
author: mattbriggs
ms.topic: how-to
ms.date: 04/20/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/13/2019
ms.openlocfilehash: ba55e6b5a6025d93f4e630d17fa9af731460105b
ms.sourcegitcommit: 32834e69ef7a804c873fd1de4377d4fa3cc60fb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661096"
---
# <a name="get-authentication-information-for-azure-stack-hub"></a>Získat informace o ověřování pro centrum Azure Stack

Aby se ověřilo, že Azure Stack hub vyžaduje, abyste zadali ID předplatného, ID tenanta a umístění a Správce prostředků koncový bod centra Azure Stack. Tyto hodnoty můžete získat z [správce prostředkůho koncového bodu centra Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles-ruby?view=azs-1910#the-azure-stack-hub-resource-manager-endpoint) pro centrum Azure Stack. Můžete je také získat pomocí kroků v tomto článku.

## <a name="values-needed-to-authenticate"></a>Hodnoty potřebné k ověření

Potřebujete tyto informace:

-   **ID předplatného**  

    ID předplatného se používá pro přístup k nabídkám v centru Azure Stack.

-   **ID tenanta**

    Adresář je kontejner, který obsahuje informace o uživatelích, aplikacích, skupinách a instančních objektech. Tenant adresáře je organizace, jako je například Microsoft nebo vaše společnost.

-   **Umístění**

    Umístění, nebo oblast, je sada Datacenter nasazených v hraničním prostředí definovaném latencí a připojená přes vyhrazenou síť s nízkou latencí. Pomocí centra Azure Stack může vaše umístění zahrnovat místní datové centrum, nikoli oblast Azure.

-   **Koncový bod Správce prostředků centra Azure Stack**

    Microsoft Azure Správce prostředků je rozhraní pro správu, které správcům umožňuje nasazovat, spravovat a monitorovat prostředky Azure. Azure Resource Manager může tyto úlohy v jedné operaci zpracovat jako skupinu, nikoli jednotlivě.

## <a name="get-the-subscription-id"></a>Získat ID předplatného

Získání ID předplatného:

1.  Přihlaste se k portálu pro uživatele centra Azure Stack.

2.  Vyberte **všechny služby**.

    > ![ID tenanta ID odběru informací o ověřování centra Azure Stack](./media/authenticate-azure-stack-hub/azure-stack-hub-auth-info.png)

3.  Vyberte **Předplatná**.

4.  Vyberte předplatné, které chcete použít.

5.  Z **přehledu**zkopírujte **ID předplatného** .

## <a name="get-the-tenant-id"></a>Získat ID tenanta

Získání ID tenanta:

1.  Přihlaste se k portálu pro uživatele centra Azure Stack.

2.  Najeďte myší na své uživatelské jméno v pravém horním rohu okna.

3.  **ID adresáře** je ID tenanta.

## <a name="get-the-azure-resource-manager-endpoint"></a>Získat Azure Resource Manager koncový bod

Azure Resource Manager koncový bod je koncový bod metadat pro službu nasazení a správu pro centrum Azure Stack. Poskytuje vrstvu pro správu, která umožňuje vytvářet, aktualizovat a odstraňovat prostředky v předplatném Azure.

Pro integrovaný systém je adresa URL pro Azure Resource Manager koncový bod:<br>`https://management.<location>.<fqdn>`

Pokud chcete získat koncový bod metadat, který odkazuje na vlastnosti, jako je koncový bod galerie, koncový bod grafu, koncový bod portálu, přihlašovací koncový bod a cílové skupiny, adresa URL:`<ResourceManager>/metadata/endpoints?api-version=1.0`

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o použití [Správce prostředků centra Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles?view=azs-1910) v centru Azure Stack.
