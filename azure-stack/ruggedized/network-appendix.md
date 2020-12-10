---
title: Příloha "robustní sítě v centru Azure Stack
description: Přílohy pro robustní sítě centra Azure Stack.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: conceptual
ms.date: 10/14/2020
ms.lastreviewed: 10/14/2020
ms.openlocfilehash: 2a5acdf50a310fe2bda89e4472c38af2c75f9487
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96939488"
---
# <a name="azure-stack-hub-ruggedized-network-appendix"></a>Síť s robustní sítí v centru Azure Stack – dodatek

Příloha poskytuje parametry zařízení a informace o identitě pro robustní hardware Azure Stack hub.

## <a name="technical-device-parameters"></a>Parametry technického zařízení

### <a name="dell-s5048f-on"></a>Dell S5048F-ON

Porty: 

- 48 Gigabit Ethernet SFP28 porty s rychlostí 25 – ethernetové. 
- Šest portů gigabitového Ethernetu 100 – QSFP28. 
- Jedna konzola RJ45/port pro správu s RS232 signalizací.
- Jeden z volitelných portů konzoly typu Micro USB B. 
- Jeden port 10/100/1000 sítě Ethernet, který se používá jako port pro správu. 
- Jeden USB typ portu pro externí velkokapacitní úložiště. 

Tématech

- Velikost: 1RU, 1.72 "(h) x 17.1" (w) × 18 "(d) (4.4 (h) x 43.4 (w) x 45.7 (d) cm). 
- Váha: 22 kg (9,98 kg) 
- ISO 7779 A-vážená hladina akustického tlaku: 59.6 dBA na 73.4 °F (23 oC).  
- Zdroj napájení: 100 – 240 VAC 50/60 Hz. 
- Maximální tepelný výstup: 1956 BTU/h. 
- Maximální aktuální vykreslování na systém: 
  - 5.73 a/4.8 A na 100/120V střídavě aktuální (AC). 
  - 2.87 a/2.4 A na 200/240V AC. 
- Maximální spotřeba energie: 573 wattů (AC). 
- Typická spotřeba energie: 288 wattů (AC) se zatížením všech vláken.  
- Maximální počet provozních specifikací: 
  - Provozní teplota: 32 ° až 113 °F (0 ° až 45 °C). 
  - Provozní vlhkost: 10% až 90% (RH), nekondenzující se. 
  - Čerstvé ovzduší kompatibilní s 45 °C. 
- Maximální počet neprovozních specifikací: 
  - Teplota úložiště: – 40 ° až 158 °F (– 40 ° až 70 °C). 
  - Vlhkost úložiště: od 5 do 95% (RH), nekondenzující.

### <a name="dell-3048-on"></a>Dell 3048 – ZAPNUTo

Porty:

- 48 porty 1000BASE-T pro počet řádků.  
- Čtyři linky s frekvencí 10-GbE SFP + porty. 
- Jedna konzola RJ45/port pro správu s RS232 signalizací.  

Tématech

- Velikost: jedna RU, 1.71 "(h) × 17.09" (w) × 12.6 "(d) (4.4 (h) × 43.4 (w) x 32.0 (d) s cm).  
- Váha: 12,8 kg (5,84 kg) s jedním zdrojem napájení, 14,8 kg (6,74 kg) se dvěma zdroji napájení. 
- ISO 7779 A-vážená hladina akustického tlaku: \< 36 DBA na 78.8 °f (26 oC). 
- Zdroj napájení: 90 – 264 VAC 50/60 Hz. 
  1) Dopředný tok přes AC.  
  2) Reverzní tok na AC. 
- Maximální tepelný výstup: 290 BTU/h. 
- Maximální aktuální vykreslování na systém:  
  - \<1A v 100/120V VAC. 
  - \<0.5 a v 200/240 VAC.  
- Maximální spotřeba energie: 87 W. 
- Typická spotřeba energie: 65 wattů. 
- Maximální počet provozních specifikací:  
  - Provozní teplota: 32 ° až 113 °F (0 ° až 45 °C).  
  - Provozní vlhkost: 5% až 85% (RH), nekondenzující se.    
  - Provozní nadlimitní hodnota: 0 ft až 10 000 ft nad úrovní moře.  
- Maximální počet neprovozních specifikací: 
  - Teplota úložiště: – 40 ° až 158 °F (– 40 ° až 70 °C). 
  - Vlhkost úložiště: od 5 do 95% (RH), nekondenzující.   

## <a name="identity"></a>Identita

Všechna síťová zařízení jsou navržená tak, aby fungovala se službou terminálový přístup Controller Access-Control System (TACACS). TACACS poskytuje centralizované prostředí identity. Server TACACS musí být dodán jako součást počáteční konfigurace.

Všimněte si, že server TACACS není k dispozici, zařízení se vrátí k místním identitám uvedeným níže. Během normálního provozu jsou tyto účty zakázané.

Následující uživatelské účty se ukončí podle zařízení:

| Zařízení | Uživatelské jméno | Výchozí heslo     |
|--------|----------|----------------------|
| Mandát 1  | Kořenová složka     | Přiřazeno zákazníkem |
| Mandát 2  | Kořenová složka     | Přiřazeno zákazníkem |
| Řadič BMC    | Kořenová složka     | Přiřazeno zákazníkem |
