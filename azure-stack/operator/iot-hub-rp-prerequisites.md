---
title: Předpoklady pro instalaci IoT Hub v centru Azure Stack
description: Přečtěte si o požadovaných požadavcích před instalací poskytovatele prostředků IoT Hub v centru Azure Stack.
author: yiyiguo
ms.author: yiygu
ms.service: azure-stack
ms.topic: how-to
ms.date: 1/6/2020
ms.openlocfilehash: 953f8df1ce76b5765ba0c200ea9f2a78c7ee72b6
ms.sourcegitcommit: e432e7f0a790bd6419987cbb5c5f3811e2e7a4a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102515650"
---
# <a name="prerequisites-for-installing-iot-hub-on-azure-stack-hub"></a>Předpoklady pro instalaci IoT Hub v centru Azure Stack

[!INCLUDE [preview-banner](../includes/iot-hub-preview.md)]

Aby bylo možné nainstalovat IoT Hub do centra Azure Stack, je nutné provést následující požadované součásti. K dokončení všech kroků **může být vyžadováno několik dní nebo týdnů doby realizace** .

> [!IMPORTANT]
> Tyto požadavky předpokládají, že jste už nasadili aspoň Azure Stack integrovaný systém se 4 uzly, ale **číslo buildu 1.2005.6.53** nebo vyšší. IoT Hub poskytovatel prostředků není podporován v Azure Stack hub Development Kit (ASDK).

## <a name="common-prerequisites"></a>Společné požadavky

[!INCLUDE [Common RP prerequisites](../includes/resource-provider-prerequisites.md)]

## <a name="dependency-prerequisites"></a>Požadavky závislosti

1. Stáhněte a [nainstalujte Event Hubs](event-hubs-rp-install.md) z webu Marketplace. Nasazení Event Hubs musí nastat před zahájením nasazení IoT Hub.
2. Pro rychlejší stažení a instalaci IoT Hub Stáhněte si před stažením IoT Hub balíčku následující závislé položky z webu Marketplace. V opačném případě se IoT Hub nasazení pokusí stáhnout závislé balíčky:
    * Rozšíření vlastních skriptů
    * Konfigurace požadovaného stavu prostředí PowerShell
    * Bezplatná licence: SQL Server 2016 SP2 Express v systému Windows Server 2016
    * Rozšíření SQL IaaS
    * Azure Stack Add-On RP Windows Server
3. Před pokračováním v nasazení IoT Hub počkejte alespoň 10 minut po úspěšné instalaci Event Hubs.

## <a name="certificate-requirements"></a>Požadavky na certifikáty

1. Pořízení certifikátu TLS/SSL infrastruktury veřejných klíčů (PKI) pro IoT Hub. Pole **subjektu** certifikátu musí splňovat následující vzor pojmenování: `CN=*.mgmtiothub.<region>.<fqdn>` . Úplný seznam podrobných požadavků najdete v tématu [požadavky na certifikát PKI](azure-stack-pki-certs.md) .

   ![Příklad certifikátu IoT Hub](media\iot-hub-rp-prerequisites\certificate.png)

2. Nezapomeňte zkontrolovat [certifikát ověřit](azure-stack-validate-pki-certs.md). V článku se dozvíte, jak připravit a ověřit certifikáty, které používáte pro poskytovatele prostředků IoT Hub. 

## <a name="dns-configuration-requirements"></a>Požadavky na konfiguraci DNS
 
Aby IoT Hub správně fungovala v síti Azure Stack hub, musí správce sítě nakonfigurovat DNS. V nástroji pro správu DNS Najděte nastavení podmíněného předávání DNS a přidejte pravidlo podmíněného předávání, které umožní provoz pro: `<region>.cloudapp.<externaldomainname>` . Například, `ussouth.cloudapp.contoso.com`.

## <a name="next-steps"></a>Další kroky

V dalším kroku nainstalujte do [připojeného Azure Stack](iot-hub-rp-install.md)poskytovatele prostředků IoT Hub.
