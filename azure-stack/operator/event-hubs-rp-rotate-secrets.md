---
title: Postup při střídání tajných kódů pro Event Hubs v poskytovateli prostředků centra Azure Stack
description: Naučte se, jak otočit tajné klíče pro Event Hubs v poskytovateli prostředků Azure Stack hub.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 10/15/2020
ms.reviewer: jfggdl
ms.lastreviewed: 10/15/2020
ms.openlocfilehash: fe238576814d1aae5d7469e9438f8921f912f0ea
ms.sourcegitcommit: f56a5b287c90b2081ae111385c8b7833931d4059
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2020
ms.locfileid: "97349434"
---
# <a name="how-to-rotate-secrets-for-event-hubs-on-azure-stack-hub"></a>Postup při střídání tajných kódů pro Event Hubs v centru Azure Stack

V tomto článku se dozvíte, jak otočit tajné klíče používané poskytovatelem prostředků Event Hubs.

## <a name="overview-and-prerequisites"></a>Přehled a požadavky

[!INCLUDE [prereqs](../includes/resource-provider-va-rotate-secrets-prereqs.md)]

## <a name="prepare-a-new-tls-certificate"></a>Příprava nového certifikátu TLS

[!INCLUDE [prepare](../includes/resource-provider-va-rotate-secrets-prepare.md)]

## <a name="rotate-secrets"></a>Otočit tajné klíče

[!INCLUDE [rotate](../includes/resource-provider-va-rotate-secrets-rotate.md)]

## <a name="troubleshooting"></a>Řešení potíží

Rotace tajného klíče by měla být úspěšně dokončena bez chyb. Pokud na portálu pro správu nastanou některé z následujících podmínek, [otevřete žádost o podporu](azure-stack-manage-basics.md#where-to-get-support) :

   - Problémy s ověřováním, včetně potíží s připojením k poskytovateli prostředků Event Hubs.
   - Nepovedlo se upgradovat poskytovatele prostředků nebo upravit parametry konfigurace.
   - Metriky využití nejsou zobrazovány.
   - Nevygenerované platby.
   - K zálohování nedochází.

## <a name="next-steps"></a>Další kroky

Podrobnosti o rotaci tajných kódů infrastruktury centra Azure Stack najdete [v centru Azure Stack v oblasti otočení tajných](azure-stack-rotate-secrets.md)kódů.