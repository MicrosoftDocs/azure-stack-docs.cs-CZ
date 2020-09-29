---
title: Požadavky pro instalaci Event Hubs v centru Azure Stack
description: Přečtěte si o požadovaných požadavcích před instalací poskytovatele prostředků Event Hubs v centru Azure Stack.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 12/09/2019
ms.reviewer: jfggdl
ms.lastreviewed: 12/09/2019
ms.openlocfilehash: 6b081f5285daf5f6963c7626a62993f9ea8587c4
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90572200"
---
# <a name="prerequisites-for-installing-event-hubs-on-azure-stack-hub"></a>Předpoklady pro instalaci Event Hubs v centru Azure Stack

[!INCLUDE [preview-banner](../includes/event-hubs-preview.md)]

Aby bylo možné nainstalovat Event Hubs do centra Azure Stack, je nutné provést následující požadované součásti. K dokončení všech kroků **může být vyžadováno několik dní nebo týdnů doby realizace** .

> [!IMPORTANT]
> Tyto požadavky předpokládají, že jste už nasadili aspoň Azure Stack integrovaný systém centra pro 4 uzly. Event Hubs poskytovatel prostředků není podporován na Azure Stack Development Kit (ASDK).

> [!IMPORTANT]
> Event Hubs vyžaduje verzi buildu centra Azure Stack 2002 nebo vyšší. Všimněte si, že Azure Stack sestavení centra jsou přírůstková. Například pokud máte nainstalovanou [verzi 1910](./release-notes.md?view=azs-1910#1910-build-reference) , musíte nejdřív upgradovat na [2002](./release-notes.md?view=azs-2002#2002-build-reference)a pak na 2005. To znamená, že nemůžete přeskočit sestavení mezi.

## <a name="common-prerequisites"></a>Společné požadavky

[!INCLUDE [Common RP prerequisites](../includes/marketplace-resource-provider-prerequisites.md)]

## <a name="event-hubs-prerequisites"></a>Event Hubs předpoklady

1. Opatřete si certifikáty SSL infrastruktury veřejných klíčů (PKI) pro Event Hubs. Alternativní název subjektu (SAN) musí splňovat následující vzor pojmenování: `CN=*.eventhub.<region>.<fqdn>` . Je možné zadat název subjektu, ale při zpracování certifikátů ho Event Hubs nepoužívá. Použije se jenom alternativní název subjektu. Úplný seznam podrobných požadavků najdete v tématu [požadavky na certifikát PKI](azure-stack-pki-certs.md) .  

   ![Ukázkový certifikát](media/event-hubs-rp-prerequisites/certificate-example.png)

   > [!NOTE]
   > **Soubory PFX musí být chráněné heslem**. Heslo bude požádáno později během instalace.

2. Nezapomeňte zkontrolovat [certifikát ověřit](azure-stack-validate-pki-certs.md). V článku se dozvíte, jak připravit a ověřit certifikáty, které používáte pro poskytovatele prostředků Event Hubs. 

## <a name="next-steps"></a>Další kroky

Dále [nainstalujte poskytovatele prostředků Event Hubs](event-hubs-rp-install.md).
