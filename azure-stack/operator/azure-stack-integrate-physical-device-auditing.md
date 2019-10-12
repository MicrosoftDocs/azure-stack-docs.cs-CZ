---
title: Integrace fyzického auditu zařízení s Azure Stack Datacenter | Microsoft Docs
description: Naučte se integrovat auditování přístupu fyzických zařízení k vašemu Azure Stack datacentra.
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
ms.openlocfilehash: b5fa17b3913db7ebec210fc3bf986bac6414368e
ms.sourcegitcommit: a6d47164c13f651c54ea0986d825e637e1f77018
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72277220"
---
# <a name="integrate-physical-device-auditing-with-your-azure-stack-datacenter"></a>Integrace fyzického auditu zařízení s Azure Stack Datacenter

Všechna fyzická zařízení v Azure Stack, jako jsou řadiče pro správu základní desky (BMC) a přepínače sítě, generují protokoly auditu. Protokoly auditu můžete integrovat do svého celkového řešení auditování. Vzhledem k tomu, že se zařízení liší v různých Azure Stack dodavatelích hardwaru OEM, požádejte dodavatele o dokumentaci o integraci auditu. V následujících částech najdete obecné informace o auditování fyzických zařízení v Azure Stack.  

## <a name="physical-device-access-auditing"></a>Auditování přístupu fyzických zařízení

Všechna fyzická zařízení v Azure Stack podporují použití rozhraní TACACS nebo RADIUS. Podpora zahrnuje přístup k řadiči pro správu základní desky (BMC) a síťovým přepínačům.

Azure Stack řešení se nedodává s integrovaným protokolem RADIUS nebo TACACS. Řešení však byla ověřena tak, aby podporovala používání stávajících řešení RADIUS nebo TACACS dostupných na trhu.

Pouze pro poloměr byl ověřen protokol MSCHAPv2. To představuje nejbezpečnější implementaci pomocí protokolu RADIUS. Projděte si dodavatele hardwaru OEM a povolte TACAS nebo RADIUS v zařízeních, která jsou součástí vašeho řešení Azure Stack.

## <a name="syslog-forwarding-for-network-devices"></a>Předávání syslogu pro síťová zařízení

Všechna fyzická síťová zařízení v Azure Stack podporují zprávy syslog. Azure Stack řešení se nedodává se serverem syslog. Zařízení se ale ověřila tak, aby podporovala odesílání zpráv na stávající řešení syslog dostupná na trhu.

Cílová adresa syslog je volitelný parametr, který se shromažďuje pro nasazení, ale dá se taky přidat po nasazení. Pokud chcete nakonfigurovat předávání syslogu na vašich síťových zařízeních, obraťte se na dodavatele hardwaru OEM.

## <a name="next-steps"></a>Další kroky

[Zásady údržby](azure-stack-servicing-policy.md)
