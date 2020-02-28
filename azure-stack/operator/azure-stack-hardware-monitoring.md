---
title: Monitorovat stav hardwaru centra Azure Stack
description: Naučte se monitorovat stav hardwarových komponent centra Azure Stack.
author: sethmanheim
ms.topic: conceptual
ms.date: 02/24/2020
ms.author: sethm
ms.reviewer: thoroet
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: dc7be7e80f6277c6788c9b572ad2d74031d811f1
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77699724"
---
# <a name="monitor-azure-stack-hub-hardware-components"></a>Monitorovat hardwarové součásti centra Azure Stack

Systém Azure Stackho centra stavů a monitorování sleduje stav subsystému úložiště a v případě potřeby vyvolává výstrahy. Systém stavu a monitorování může také vyvolat výstrahy pro následující hardwarové komponenty:

- Systémové ventilátory
- Systémová teplota
- Zdroj napájení
- procesory
- Paměť
- Spouštěcí jednotky

> [!NOTE]
> Před povolením této funkce je nutné ověřit u svého hardwarového partnera, že jsou připravené. K povolení této funkce v řadiči pro správu základní desky vám poskytne váš hardwarový partner také podrobný postup.

## <a name="snmp-listener-scenario"></a>Scénář naslouchacího procesu SNMP

Naslouchací proces SNMP v3 je spuštěn na všech třech instancích ERCS na portu TCP 162. Řadič pro správu základní desky (BMC) musí být nakonfigurovaný tak, aby odesílal depeše protokolu SNMP do naslouchacího procesu centra Azure Stack. Tři PEP IP adresy můžete získat z portálu pro správu tak, že otevřete zobrazení vlastnosti oblasti.

Odesílání depeší do naslouchacího procesu vyžaduje ověření a musí používat stejné přihlašovací údaje jako přístup k základnímu řadiči pro správu základní desky.

Při přijetí depeše SNMP na kterékoli ze tří ERCS instancí na portu TCP 162 se identifikátor OID spáruje interně a vyvolá se výstraha. Systém Azure Stack a monitorování stavu centra a systému přijímá pouze identifikátory OID definované hardwarovým partnerem. Pokud je identifikátor OID pro Azure Stack hub neznámý, neshoduje se s výstrahou.

Jakmile je poškozená součást nahrazena, pošle se událost z řadiče pro správu základní desky do naslouchacího procesu SNMP, který indikuje změnu stavu a výstraha se automaticky zavře v Azure Stack hub.

> [!NOTE]
> Existující výstrahy se při nahrazení celého uzlu nebo základní desky automaticky nezavřou. Totéž platí i v případě, že dojde ke ztrátě konfigurace řadiče pro správu základní desky. například kvůli obnovení továrního nastavení.

## <a name="next-steps"></a>Další kroky

[Integrace brány firewall](azure-stack-firewall.md)
