---
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: include
ms.date: 12/09/2019
ms.reviewer: bryanla
ms.lastreviewed: 12/09/2019
ms.openlocfilehash: d84b6a7d7f4ec1777b8e58e8cafc9ba64726393f
ms.sourcegitcommit: c263a86d371192e8ef2b80ced2ee0a791398cfb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2020
ms.locfileid: "82847847"
---
Pokud jste už nainstalovali poskytovatele prostředků, pravděpodobně jste dokončili následující požadavky a tuto část můžete přeskočit. V opačném případě dokončete tyto operace, než budete pokračovat: 

1. Pokud jste to ještě neudělali, [Zaregistrujte svou instanci centra Azure Stack v Azure](../operator/azure-stack-registration.md). Tento krok je nutný, protože se budete připojovat k webu Marketplace a stahovat z něj položky z Azure.

2. Pokud nejste obeznámeni s funkcí **správy Marketplace** portálu pro správu centra Azure Stack, přečtěte si téma [stažení položek z webu Marketplace z Azure a publikování do centra Azure Stack](../operator/azure-stack-download-azure-marketplace-item.md). Tento článek vás provede procesem stahování položek z Azure do tržiště centra Azure Stack. Zahrnuje jak připojené, tak odpojené scénáře. Pokud je vaše instance centra Azure Stack odpojená nebo je připojená částečně, existují další předpoklady, které je potřeba provést v přípravě na instalaci.

3. Aktualizujte si domovský adresář Azure Active Directory (Azure AD). Počínaje verzí Build 1910 se nová aplikace poskytovatele prostředků nasazení (DRP) musí použít k registraci vašeho tenanta domovského adresáře. Tato aplikace umožní DRP úspěšně vytvořit a zaregistrovat poskytovatele prostředků. Pokud tento krok není dokončený, instalace poskytovatele prostředků se nezdaří. 

   - Po úspěšné aktualizaci instance centra Azure Stack na 1910 nebo vyšší, postupujte podle [pokynů pro klonování a stahování úložiště Azure Stack nástrojů centra](../operator/azure-stack-powershell-download.md). 
   - Potom postupujte podle pokynů pro [aktualizaci domovského adresáře Azure AD centra Azure Stack (po instalaci aktualizací nebo nových poskytovatelů prostředků)](https://github.com/Azure/AzureStack-Tools/tree/master/Identity#updating-the-azure-stack-aad-home-directory-after-installing-updates-or-new-resource-providers). 