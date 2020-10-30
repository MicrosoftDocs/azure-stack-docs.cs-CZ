---
title: Přehled IoT Hub v centru Azure Stack
description: Přečtěte si o poskytovateli prostředků IoT Hub v centru Azure Stack.
author: yiyiguo
ms.author: yiygu
ms.service: azure-stack
ms.topic: how-to
ms.date: 12/12/2019
ms.openlocfilehash: 098168947a28214a3955a1961c80a2c06825126b
ms.sourcegitcommit: 296c95cad20ed62bdad0d27f1f5246bfc1c81d5e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93064714"
---
# <a name="overview-of-iot-hub-on-azure-stack-hub"></a>Přehled IoT Hub v centru Azure Stack

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

IoT Hub v centru Azure Stack vám umožní vytvářet hybridní řešení IoT. IoT Hub je spravovaná služba, která slouží jako centrální Centrum zpráv pro obousměrnou komunikaci mezi aplikací IoT a zařízeními, která spravuje. IoT Hub v centru Azure Stack můžete použít k vytváření řešení IoT se spolehlivou a zabezpečenou komunikací mezi zařízeními IoT a back-Prem řešení.

## <a name="features"></a>Funkce

| Doporučené | IoT Hub v Azure | IoT Hub ve verzi Preview centra Azure Stack (S2/S3) |
|-|-|-|
|Telemetrie ze zařízení do cloudu| ✔ | ✔ |
|Zasílání zpráv z cloudu na zařízení| ✔ | ✔ |
|Identita vázaná na zařízení| ✔ | ✔ |
|Směrování zpráv <sub>1</sub><sub>, 4</sub>| ✔ | ✔ |
|Protokoly HTTP, AMQP, MQTT| ✔ | ✔ |
|Architektura s více tenanty| ✔ | ✔ |
|Monitorování a diagnostika| ✔ | ✔ |
|Zasílání zpráv z cloudu na zařízení| ✔ | ✔ |
|Správa zařízení, vyzdvojení zařízení, nevlákenný modul| ✔ | ✔ |
|Dvojitá oznámení, události životního cyklu zařízení| ✔ | ✔ |
|Nasazení Edge vrstev| ✔ | odkud |
|Portál pro správu <sub>2</sub>| ✘ | ✔ |
|Rotace tajného kódu <sub>2</sub>| ✘ | ✔ |
|Správa kapacity <sub>2</sub>| ✘ | ✔ |
|Zálohování & obnovení <sub>3</sub>| ✘ | ✘ |
|DeviceConnected, DeviceDisconnected, ASC <sub>4</sub>| ✔ | ✘ |
|Konfigurace modulu zařízení| ✔ | odkud |
|Streamování zařízení, technologie IoT Plug and Play, úlohy, nahrávání souborů <sub>5</sub>| ✔ | ✘ |
|Monitorování stavu připojení zařízení pomocí Event Grid <sub>4</sub>| ✔ | ✘ |
|Převzetí služeb při selhání <sub>6</sub>| ✔ | ✘ |

<sub>1</sub> je omezeno na předdefinované koncové body, Event Hubs a úložiště. Service Bus není k dispozici v centru Azure Stack.  
<sub>2</sub> pro operátory ke správě IoT Hub v Ash.  
<sub>3</sub> záloha je k dispozici ve verzi Preview. Obnovení bude podporováno v GA.  
<sub>4</sub> závisí na dalších službách, které nejsou k dispozici v Azure Stack hub.  
<sub>5</sub> v plánu, který se má přenést do centra Azure Stack.  
<sub>6</sub> nelze použít na rozbočovači Azure Stack.  

## <a name="api-available-for-iot-hub-on-azure-stack-hub"></a>Rozhraní API je dostupné pro IoT Hub v centru Azure Stack

|Rozhraní API|IoT Hub v centru Azure Stack|
|-|-|
|Použít konfiguraci na zařízení| ✔ |
| Vytvoření konfigurace | ✔ |
| Odstranit konfiguraci | ✔ |
| Čtení konfigurace | ✔ |
|Konfigurace – mnoho čtení| ✔ |
|Použít službu konfigurace|  ✔ |
|Aktualizace konfigurace|  ✔ |
|Metoda přímého vyvolání zařízení|  ✔ |
|GetDeviceAndModuleInScope|  ✔ |
|GetDevicesAndModulesInScope| ✔ |
|Zrušit registraci zařízení| ✔ |
|Získat zařízení| ✔ |
|Aktualizovat modul na vlákna| ✔ |
|D2C získat dvojitou| ✔ |
|Importovat zařízení| ✔ |
|Získat dvojitou| ✔ |
|Zrušit registraci modulu| ✔ |
|Aktualizace zařízení| ✔ |
|Aktualizovat modul| ✔ |
|Dotazování na zařízení| ✔ |
|Exportovat zařízení| ✔ |
|Zálohování a obnovení – ADM| ✔ |
|Nahradit dvojitou proměnnou| ✔ |
|Zálohování a obnovení – DSS| ✔ |
|D2C oznamování DesiredProperties| ✔ |
|D2C patch ReportedProperties| ✔ |
|Získat nevlákenný modul| ✔ |
|Modul D2C získat dvojitou| ✔ |
|Získat modul| ✔ |
|Modul D2C informování DesiredProperties| ✔ |
|Modul D2C patch ReportedProperties| ✔ |
|Metoda Direct vyvolat modul| ✔ |
|Aktualizovat dvojitě| ✔ |
|Operace hromadného zařízení| ✔ |
|Telemetrie zařízení do cloudu| ✔ |
|Registrovat zařízení| ✔ |
|Registrovat modul| ✔ |
|Nahradit modul s dvojitou proměnnou| ✔ |
|GenericAuthentication| ✔ |
|Získat zařízení| ✔ |
|Změna přesunu nebo role oddílu| ✔ |

## <a name="differences-between-iot-hub-on-azure-cloud-and-iot-hub-on-azure-stack"></a>Rozdíly mezi IoT Hub v cloudu Azure a IoT Hub na Azure Stack

| Aspekt | IoT Hub v cloudu | IoT Hub v zásobníku |
|-|-|-|
| Spotřeba zpráv | https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-messages-read-builtin |Ve výchozím nastavení jsou zprávy směrovány do integrovaného koncového bodu s přístupem ke službě (zprávy/události), které jsou kompatibilní s Event Hubs. V cloudu Azure můžete ke zprávám z koncového bodu přistupovat pomocí IoT Hub připojovacího řetězce nebo připojovacího řetězce centra událostí. V Azure Stackovém centru se však podporuje pouze připojovací řetězec centra událostí. |

## <a name="next-steps"></a>Další kroky

Pokud ve vašem předplatném není IoT Hub k dispozici, spolupracujte se svým správcem a [nainstalujte IoT Hub do poskytovatele prostředků služby Azure Stack hub](../operator/iot-hub-rp-overview.md).

Informace o tom, jak používat IoT Hub, najdete [v dokumentaci k Azure IoT Hub](/azure/iot-hub/).

