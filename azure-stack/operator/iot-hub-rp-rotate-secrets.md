---
title: Postup při střídání tajných kódů pro IoT Hub v poskytovateli prostředků centra Azure Stack
description: Naučte se, jak otočit tajné klíče pro IoT Hub v poskytovateli prostředků Azure Stack hub.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 11/15/2020
ms.reviewer: bryanla
ms.lastreviewed: 11/15/2020
ms.openlocfilehash: d693b73794b71ec32816f2e79370e2f836b00e7b
ms.sourcegitcommit: 8c745b205ea5a7a82b73b7a9daf1a7880fd1bee9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95517547"
---
# <a name="how-to-rotate-secrets-for-iot-hub-on-azure-stack-hub"></a>Postup při střídání tajných kódů pro IoT Hub v centru Azure Stack

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

V tomto článku se dozvíte, jak otočit tajné klíče používané poskytovatelem prostředků IoT Hub.

## <a name="overview-and-prerequisites"></a>Přehled a požadavky

[!INCLUDE [prereqs](../includes/resource-provider-va-rotate-secrets-prereqs.md)]

## <a name="prepare-a-new-tls-certificate"></a>Příprava nového certifikátu TLS

[!INCLUDE [prepare](../includes/resource-provider-va-rotate-secrets-prepare.md)]

## <a name="rotate-secrets"></a>Otočit tajné klíče

[!INCLUDE [rotate](../includes/resource-provider-va-rotate-secrets-rotate.md)]

## <a name="troubleshooting"></a>Řešení potíží

Rotace tajného klíče by měla být úspěšně dokončena bez chyb. Pokud na portálu pro správu nastanou některé z následujících podmínek, [otevřete žádost o podporu](azure-stack-manage-basics.md#where-to-get-support) :

   - Problémy s ověřováním, včetně potíží s připojením k poskytovateli prostředků IoT Hub.
   - Nepovedlo se upgradovat poskytovatele prostředků nebo upravit parametry konfigurace.
   - Metriky využití nejsou zobrazovány.
   - Nevygenerované platby.
   - K zálohování nedochází.

## <a name="next-steps"></a>Další kroky

Podrobnosti o rotaci tajných kódů infrastruktury centra Azure Stack najdete [v centru Azure Stack v oblasti otočení tajných](azure-stack-rotate-secrets.md)kódů.