---
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: include
ms.date: 12/09/2019
ms.reviewer: bryanla
ms.lastreviewed: 12/09/2019
ms.openlocfilehash: e1e2c3c6c3d1149b2cd034c2f5e4abfdf26e712f
ms.sourcegitcommit: dd53af1b0fc2390de162d41e3d59545d1baad1a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2020
ms.locfileid: "80423979"
---
Pokud jste už nainstalovali poskytovatele prostředků z webu centra Azure Stack hub, pravděpodobně jste dokončili běžné požadavky a můžete tuto část přeskočit. V opačném případě nejprve proveďte následující předpoklady: 

1. Pokud jste to ještě neudělali, [Zaregistrujte svou instanci centra Azure Stack v Azure](../operator/azure-stack-registration.md). Tento krok je nutný, protože se budete připojovat k webu Marketplace a stahovat z něj položky z Azure.

2. Pokud nejste obeznámeni s funkcí **správy Marketplace** portálu pro správu centra Azure Stack, přečtěte si téma [stažení položek z webu Marketplace z Azure a publikování do centra Azure Stack](../operator/azure-stack-download-azure-marketplace-item.md). Tento článek vás provede procesem stahování položek z Azure do tržiště centra Azure Stack. Zahrnuje jak připojené, tak odpojené scénáře. Pokud je vaše instance centra Azure Stack odpojená nebo je připojená částečně, existují další předpoklady, které je potřeba provést v přípravě na instalaci.

3. Aktualizujte si domovský adresář Azure Active Directory (Azure AD). Počínaje verzí Build 1910 se nová aplikace poskytovatele prostředků nasazení (DRP) musí použít k registraci vašeho tenanta domovského adresáře. Tato aplikace umožní DRP úspěšně vytvořit a zaregistrovat poskytovatele prostředků. Pokud tento krok není dokončený, instalace poskytovatele prostředků se nezdaří. 

   - Po úspěšné aktualizaci instance centra Azure Stack na 1910 nebo vyšší, postupujte podle [pokynů pro klonování a stahování úložiště Azure Stack nástrojů centra](../operator/azure-stack-powershell-download.md). 
   - Potom postupujte podle pokynů pro [aktualizaci domovského adresáře Azure AD centra Azure Stack (po instalaci aktualizací nebo nových poskytovatelů prostředků)](https://github.com/Azure/AzureStack-Tools/tree/master/Identity#updating-the-azure-stack-aad-home-directory-after-installing-updates-or-new-resource-providers). 