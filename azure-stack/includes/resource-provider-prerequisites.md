---
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: include
ms.date: 12/09/2019
ms.reviewer: bryanla
ms.lastreviewed: 12/09/2019
ms.openlocfilehash: 2a980becc3bf749987759f90a9ab84050d033a04
ms.sourcegitcommit: 81e2d627c9dc4cc365deb4a0e0674b5ab3a7efbf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2020
ms.locfileid: "92297861"
---
Pokud jste už nainstalovali poskytovatele prostředků, pravděpodobně jste dokončili následující požadavky a tuto část můžete přeskočit. V opačném případě tyto kroky proveďte, než budete pokračovat: 

1. Pokud jste to ještě neudělali, [Zaregistrujte svou instanci centra Azure Stack v Azure](../operator/azure-stack-registration.md). Tento krok je nutný, protože se budete připojovat k webu Marketplace a stahovat z něj položky z Azure.

2. Pokud nejste obeznámeni s funkcí **správy Marketplace** portálu pro správu centra Azure Stack, přečtěte si téma [stažení položek z webu Marketplace z Azure a publikování do centra Azure Stack](../operator/azure-stack-download-azure-marketplace-item.md). Tento článek vás provede procesem stahování položek z Azure do tržiště centra Azure Stack. Zahrnuje jak připojené, tak odpojené scénáře. Pokud je vaše instance centra Azure Stack odpojená nebo je připojená částečně, existují další předpoklady, které je potřeba provést v přípravě na instalaci.

3. Aktualizujte si domovský adresář Azure Active Directory (Azure AD). Počínaje sestavou Build 1910 musí být v tenantovi domovského adresáře zaregistrovaná nová aplikace. Tato aplikace umožní rozbočovači Azure Stack úspěšně vytvořit a zaregistrovat novější poskytovatele prostředků (jako Event Hubs, IoT Hub a další) s vaším klientem služby Azure AD. Jedná se o jednorázovou akci, kterou je třeba provést po upgradu na Build 1910 nebo novější. Pokud tento krok není dokončený, instalace poskytovatele prostředků Marketplace se nezdaří. 

   - Po úspěšné aktualizaci instance centra Azure Stack na 1910 nebo vyšší, postupujte podle [pokynů pro klonování a stahování úložiště Azure Stack nástrojů centra](../operator/azure-stack-powershell-download.md). 
   - Potom postupujte podle pokynů pro [aktualizaci domovského adresáře Azure AD centra Azure Stack (po instalaci aktualizací nebo nových poskytovatelů prostředků)](https://github.com/Azure/AzureStack-Tools/tree/master/Identity#updating-the-azure-stack-aad-home-directory-after-installing-updates-or-new-resource-providers). 