---
title: Integrace fyzického auditu zařízení s vaším centrem Azure Stackho centra
description: Přečtěte si, jak integrovat auditování přístupu fyzických zařízení k vašemu datovému centru Azure Stack.
author: PatAltimore
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: patricka
ms.reviewer: thoroet
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: 2bfef311e8a57187aef9e8af2c4f110f8dd54ba2
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97870966"
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

[Zásady obsluhy](azure-stack-servicing-policy.md)
