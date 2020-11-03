---
title: Monitorování stavu hardwaru v centru Azure Stack
description: Naučte se monitorovat stav hardwarových komponent centra Azure Stack.
author: sethmanheim
ms.topic: conceptual
ms.date: 10/01/2020
ms.author: sethm
ms.reviewer: thoroet
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: 6eb455dbcf30b1d77818b13d1fc7db6b6b11c6f9
ms.sourcegitcommit: 62fc0592fdec706ade2b14e685448256ad0b4fe9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2020
ms.locfileid: "93239559"
---
# <a name="monitor-azure-stack-hub-hardware-components"></a>Monitorovat hardwarové součásti centra Azure Stack

Systém Azure Stackho centra stavů a monitorování sleduje stav subsystému úložiště a v případě potřeby vyvolává výstrahy. Systém stavu a monitorování může také vyvolat výstrahy pro následující hardwarové komponenty:

- Systémové ventilátory
- Systémová teplota
- Zdroj napájení
- CPUs
- Paměť
- Spouštěcí jednotky

> [!NOTE]
> Než tuto funkci povolíte, musíte ověřit u svého hardwarového partnera, že jsou připravené. K povolení této funkce v řadiči pro správu základní desky vám poskytne váš hardwarový partner taky podrobný postup. Šifrování uživatele v řadiči pro správu základní desky musí být nastavené na AES pro Build 2005 a novější. 

## <a name="snmp-listener-scenario"></a>Scénář naslouchacího procesu SNMP

Naslouchací proces SNMP v3 je spuštěn na všech třech instancích ERCS na portu TCP 162. Řadič pro správu základní desky musí být nakonfigurovaný tak, aby odesílal depeše SNMP na naslouchací proces centra Azure Stack. Tři PEP IP adresy můžete získat z portálu pro správu tak, že otevřete zobrazení vlastnosti oblasti.

Odesílání depeší do naslouchacího procesu vyžaduje ověření a musí používat stejné přihlašovací údaje jako přístup k základnímu řadiči pro správu základní desky.

Při přijetí depeše SNMP na kterékoli ze tří ERCS instancí na portu TCP 162 se identifikátor OID spáruje interně a vyvolá se výstraha. Systém Azure Stack a monitorování stavu centra a systému přijímá pouze identifikátory OID definované hardwarovým partnerem. Pokud je identifikátor OID pro Azure Stack hub neznámý, neshoduje se s výstrahou.

Jakmile je poškozená součást nahrazena, pošle se událost z řadiče pro správu základní desky do naslouchacího procesu SNMP, který indikuje změnu stavu. Výstraha se pak automaticky zavře v Azure Stackovém centru.

> [!NOTE]
> Existující výstrahy se nezavřou automaticky, když se nahradí celý uzel nebo základní deska. Totéž platí i v případě, že dojde ke ztrátě konfigurace řadiče pro správu základní desky. například kvůli obnovení továrního nastavení.

## <a name="next-steps"></a>Další kroky

[Integrace brány firewall](azure-stack-firewall.md)
