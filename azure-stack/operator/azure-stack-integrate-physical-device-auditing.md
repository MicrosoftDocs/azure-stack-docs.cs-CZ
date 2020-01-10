---
title: Integrace fyzického auditu zařízení do centra Azure Stack hub | Microsoft Docs
description: Přečtěte si, jak integrovat auditování přístupu fyzických zařízení k vašemu datovému centru Azure Stack.
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 06/10/2019
ms.author: patricka
ms.reviewer: thoroet
ms.lastreviewed: 06/10/2019
keywords: ''
ms.openlocfilehash: 48d81975457587bd9c18122d891f8e9e08fbc6eb
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75817886"
---
# <a name="integrate-physical-device-auditing-with-your-azure-stack-hub-datacenter"></a>Integrace fyzického auditu zařízení s vaším centrem Azure Stackho centra

Všechna fyzická zařízení v centru Azure Stack, jako jsou řadiče pro správu základní desky (BMC) a přepínače sítě, generují protokoly auditu. Protokoly auditu můžete integrovat do svého celkového řešení auditování. Vzhledem k tomu, že se zařízení liší v různých Azure Stack dodavatelích hardwaru výrobce OEM, požádejte dodavatele o dokumentaci o integraci auditu. V níže uvedených částech najdete obecné informace o auditování fyzických zařízení v centru Azure Stack.  

## <a name="physical-device-access-auditing"></a>Auditování přístupu fyzických zařízení

Všechna fyzická zařízení v centru Azure Stack podporují použití rozhraní TACACS nebo RADIUS. Podpora zahrnuje přístup k řadiči pro správu základní desky (BMC) a síťovým přepínačům.

Řešení centra Azure Stack nejsou dodávána s integrovaným protokolem RADIUS nebo TACACS. Řešení však byla ověřena tak, aby podporovala používání stávajících řešení RADIUS nebo TACACS dostupných na trhu.

Pouze pro poloměr byl ověřen protokol MSCHAPv2. To představuje nejbezpečnější implementaci pomocí protokolu RADIUS. Projděte si dodavatele hardwaru OEM a povolte TACAS nebo RADIUS v zařízeních, která jsou součástí vašeho řešení centra Azure Stack.

## <a name="syslog-forwarding-for-network-devices"></a>Předávání syslogu pro síťová zařízení

Všechna fyzická síťová zařízení v centru Azure Stack podporují zprávy syslog. Řešení centra Azure Stack se nedodává se serverem syslog. Zařízení se ale ověřila tak, aby podporovala odesílání zpráv na stávající řešení syslog dostupná na trhu.

Cílová adresa syslog je volitelný parametr, který se shromažďuje pro nasazení, ale dá se taky přidat po nasazení. Pokud chcete nakonfigurovat předávání syslogu na vašich síťových zařízeních, obraťte se na dodavatele hardwaru OEM.

## <a name="next-steps"></a>Další kroky

[Zásady údržby](azure-stack-servicing-policy.md)
