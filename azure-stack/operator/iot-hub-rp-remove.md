---
title: Postup odebrání IoT Hub v centru Azure Stack
description: Pokyny k odinstalaci IoT Hub v centru Azure Stack
author: yiyiguo
ms.author: yiygu
ms.service: azure-stack
ms.topic: how-to
ms.date: 1/6/2020
ms.openlocfilehash: 4095336f6a18441512e8c1052051d6e1608d904a
ms.sourcegitcommit: 0e3296fb27b9dabbc2569bf85656c4c7b1d58ba9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93049783"
---
# <a name="how-to-remove-iot-hub-on-azure-stack-hub"></a>Postup odebrání IoT Hub v centru Azure Stack

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

Tento článek poskytuje informace o tom, jak odebrat IoT Hub poskytovatele prostředků v centru Azure Stack. Tento proces trvá přibližně 37 minut.

> [!WARNING]
> Po odinstalování IoT Hub se **_všechna zařízení a data odstraní_** . Operaci **nejde** obnovit.

## <a name="uninstalling-iot-hub"></a>Odinstalace IoT Hub

1) Přejít na **web Marketplace Management** . IoT Hub bude v seznamu a bude označen jako nainstalovaný. Klikněte na **IoT Hub** .

    [![Seznam poskytovatelů prostředků](../operator/media/iot-hub-rp-remove/uninstall1.png)](../operator/media/iot-hub-rp-remove/uninstall1.png#lightbox)

2) Klikněte na **odinstalovat** pod **IoT Hub** , zadejte název poskytovatele prostředků **Microsoft. iothub** a potom klikněte na tlačítko **odinstalovat** pod ním.

    [![Odinstalace IoT Hub a potvrzení](../operator/media/iot-hub-rp-remove/uninstall2.png)](../operator/media/iot-hub-rp-remove/uninstall2.png#lightbox)

3) Počkejte na dokončení odinstalace. Než se znovu pokusíte o instalaci IoT Hub, počkejte aspoň 10 minut.

>[!IMPORTANT]
>Závislosti (např. Centrum událostí **) nebude** odinstalováno. Pokud chcete odinstalovat/odebrat všechny závislosti z webu Marketplace, budete je muset udělat samostatně.

## <a name="next-steps"></a>Další kroky

Pokud ho chcete přeinstalovat, přečtěte si téma [instalace poskytovatele prostředků IoT Hub v části připojené Azure Stack](iot-hub-rp-install.md).
