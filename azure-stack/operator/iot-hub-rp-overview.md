---
title: Přehled IoT Hub v centru Azure Stack
description: Přečtěte si o poskytovateli prostředků IoT Hub v centru Azure Stack a rozdílech od verze IoT Hub hostovaného v Azure.
author: yiyiguo
ms.author: yiygu
ms.service: azure-stack
ms.topic: how-to
ms.date: 1/6/2020
ms.openlocfilehash: adf6635fbdcf62398218a8e6ad0ce061900e0059
ms.sourcegitcommit: 0e3296fb27b9dabbc2569bf85656c4c7b1d58ba9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93049779"
---
# <a name="iot-hub-on-azure-stack-hub-overview"></a>Přehled IoT Hub v centru Azure Stack

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

IoT Hub v centru Azure Stack vám umožní vytvářet hybridní řešení IoT. IoT Hub je spravovaná služba, která slouží jako centrální Centrum zpráv pro obousměrnou komunikaci mezi aplikací IoT a zařízeními, která spravuje. IoT Hub v centru Azure Stack můžete použít k vytváření řešení IoT se spolehlivou a zabezpečenou komunikací mezi zařízeními IoT a back-Prem řešení. 

## <a name="differences-between-azure-iot-hub-and-iot-hub-on-azure-stack-hub"></a>Rozdíly mezi Azure IoT Hub a IoT Hub v Azure Stack hub

| Doporučené | Azure IoT Hub | IoT Hub v centru Azure Stack |
|-|-|-|
| Měřítko | https://docs.microsoft.com/azure/iot-hub/iot-hub-scaling | Podporují se centra IoT v S2 a S3.|
| Aktualizace služby | Automaticky | Ruční |

## <a name="iot-hub-throttling"></a>Omezení IoT Hub

Omezení a kvóty IoT Hub v Azure Stack centra a SKU standardu S2 a S3 jsou stejné jako IoT Hub v Azure. Další podrobnosti najdete v tématu [kvóty a omezení pro Azure IoT Hub](/azure/iot-hub/iot-hub-devguide-quotas-throttling#quotas-and-throttling) .

## <a name="next-steps"></a>Další kroky

K přípravě na instalaci IoT Hub v centru Azure Stack si projděte dokument [požadavků](iot-hub-rp-prerequisites.md) .

Pokud se chcete dozvědět víc o rozdílech mezi připojeným a odpojeným centrem Azure Stack, Projděte si téma [Azure Stack model připojení](azure-stack-connection-models.md).
