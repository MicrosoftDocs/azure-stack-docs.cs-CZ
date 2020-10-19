---
title: Příloha sítě MDC (modulární datacentrum) – Příloha
description: Dodatky pro MDC sítě.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: conceptual
ms.date: 12/30/2019
ms.lastreviewed: 12/30/2019
ms.openlocfilehash: 10de658f7e5f8621d44baaa16f2181f58f47811e
ms.sourcegitcommit: e4e2cc6a68f02c3e856f58ca5ee51b3313c7ff8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92182967"
---
# <a name="mdc-network-appendix"></a>MDC síť – dodatek

V příloze najdete parametry zařízení a informace o identitě pro MDC hardware.

## <a name="technical-device-parameters"></a>Parametry technického zařízení

### <a name="cisco-93360yc-fx2--cisco-9348gc-fxp"></a>Cisco 93360YC-FX2 & Cisco 9348GC-FXP

| **Funkce** | **Cisco Nexus 93360YC – FX2** | **Cisco Nexus 9348GC – FXP** |
|---|---|---|
| Porty | 96 x 1/10/25-GB a 12 × 40/100 GB/s QSFP28 porty | 48 x 1 – GBASE-T porty, 4 x 1/10/25 GB/s SFP28 porty a 2 x 40/100 QSFP28 porty |
| Podporované rychlosti | 1/10/25 GB/s v downlinks, 40/100-GB/s, užitečných podporované porty, 97-108:4x10/25G | 100 až 1 MB/s a rychlosti 1 GB/s |
| Procesor | Čtyři jádra | Čtyři jádra |
| Systémová paměť | Až 24 GB | 24 GB |
| Jednotka SSD | 128 GB | 128 GB |
| Vyrovnávací paměť systému | 40 MB | 40 MB |
| Porty pro správu | Dva porty: jeden RJ-45 a jeden SFP + | Dva porty: jeden RJ-45 a jeden SFP + |
| Porty USB | Jednu | Jednu |
| Sériové porty RS-232 | Jednu | Jednu |
| Napájení (až do dvou) | 1200 W střídavý proud (AC), 1200 W TVK/HVDC | 350 W AC, 440 W DC |
| Typický výkon (AC) | 404 W | 178 W |
| Maximální výkon (AC) | 900 W | 287 W |
| Vstupní napětí (AC) | 100 v až 240 V | 100 v až 240 V |
| Vstupní napětí (střídavý proud s vysokým napětím [TVK]) | 100 v až 277 V | 90 v až 305 V |
| Vstupní napětí (DC) | – 40 v do – 72 V | \-36 v až-72 V |
| Vstupní napětí (řadič domény s vysokým napětím [HVDC]) | – 240V do – 380V | 192 v až 400 V |
| Frekvence (AC) | 50 Hz až 60 Hz | 50 Hz až 60 Hz |
| Fans | Tři zásobníky ventilátoru | Tři |
| Tok dat | Příjem a výfuk na straně portu | Příjem a výfuk na straně portu |
| Fyzické rozměry | 3,38 x 17,41 x 24.14 v. (8,59 x 44,23 × 61,31 cm) | 1,72 x 17,3 × 19,7 v. |
| (H × W × D) | | (4,4 x 43,9 × 49,9 cm) |
| Akustické | 76,7 dBA na 40% rychlosti ventilátoru, 88,7 dBA při 70% rychlosti ventilátoru a 97,4 dBA při 100% rychlosti ventilátoru | 67,5 dBA na 50% rychlosti ventilátoru, 73,2 dBA při 70% rychlosti ventilátoru a 81,6 dBA při 100% rychlosti ventilátoru |
| RoHS dodržování předpisů | Ano | Ano |
| MTBF | 320 040 hodin | 257 860 hodin |
| Minimální obrázek ACI | ACI-N9KDK9-14.1.2 | ACI-N9KDK9-13.0 |
| Minimální bitová kopie NX-OS | NXOS-9.3 (1) | NXOS-703I 7.1 |

### <a name="juniper-mx204"></a>Juniper MX204

|  |  |  |
|--|--|--|
| Rozložení | Kapacita systému | 3 Tbps |
|        | Orientace slotu | Není k dispozici |
|        | Připojení | Přední nebo středový |
| Fyzická specifikace | Rozměry (š × H × D) | 17,45 "x 8,71" x 24,5 "(44,3 x 22,1 x 62,2 cm) |
|                        | Váha plně načtena | 130 kg |
|                        | Váha – Nenačteno | 52 kg 23.6 kg |
| Modul směrování | Výchozí paměť | 2x16 MB nebo úložiště flash; 64 GB DDR4 paměti RAM; 2x50 GB SSD |
|                | Počet jader | 6 jader |
| Redundance | Komponenty | Zdroje napájení, zdroje, ventilátory |
| Prostředí | Tok vzduchu | Vedle sebe |
|               | Provozní teplota | 32 ° až 115 ° F (0 ° až 46 °c) na úrovni moře |
|               | Provozní vlhkost | 5% až 90% |
|               | Provozní nadmořská výška | 10 000 ft (3048 m) |
| Certifikace | NEBS | • GR 1089 – Core EMC a elektrická bezpečnost |
|                | | • Společná síť pro vazbu (CBN) |
|                | | • Národní elektrický kód (NEC) |
|                | | • GR – 63 – základní fyzická ochrana |

### <a name="dell-emc-s4148f-on"></a>Dell EMC S4148F – ON

Funkce:

- 48 10 Gigabit Ethernet SFP + porty
- 6 40 Gigabit Ethernet QSFP + porty
- Jedna konzola RJ45/port pro správu s RS232 signalizací 
- Jeden port RJ45 Micro-USB-B Console 
- Jeden port RJ45 10/100/1000Base-T Management Ethernet 

Tématech

- Velikost: 
  - 1 RU, 1,75 "(h) × 17" (w) × 18 "(d) (4,4 cm (h) × 43,1 cm (w) x 45,7 cm (d)) 
  - S4112:1.7 "(h) x 8.28" (w) × 18 "(d) (4,125 cm (h) × 20,9 cm (w) x 45 cm (d) 
- Zdroj napájení: 100 – 240 VAC 50/60 Hz
- Zdroj napájení (DC), který se vztahuje na S4412: hodnocený-40 VDC na-72 VDC
- Maximální aktuální vykreslování na systém: 6A/5A na 100/120V AC; 3A/2.5 A na 200/240V AC
- S4112:2A/1.7 A na 100/120V AC; 1A/0,8 A na 200/240V AC. 
- S4112 (DC):-40V/5A,-48V/4.2 A,-72V/2.8 A 

Maximální počet provozních specifikací:
- Provozní teplota: 41 ° až 104 ° F (5 ° až 40 ° C)
- Provozní vlhkost: 5% až 85% (RH), nekondenzující 

Maximální počet neprovozních specifikací:
- Teplota úložiště:-40 ° až 149 °F (-40 ° C až 65 ° C) 
- Vlhkost úložiště: od 5 do 95% (RH), nekondenzující


## <a name="identity"></a>Identita

Všechna síťová zařízení jsou navržená tak, aby fungovala se službou terminálový přístup Controller Access-Control System (TACACS). TACACS poskytuje centralizované prostředí identity. Server TACACS musí být dodán jako součást počáteční konfigurace.

Všimněte si, že server TACACS není k dispozici, zařízení se vrátí k místním identitám uvedeným níže. Během normálního provozu jsou tyto účty zakázané.


Následující uživatelské účty se ukončí podle zařízení:

| Zařízení | Uživatelské jméno | Výchozí heslo |
|---------|--------------|----------------------|
| Hrana 1 | kořen | Přiřazeno zákazníkem |
| Hrana 2 | Kořenová složka | Přiřazeno zákazníkem |
| Mandát 1 | Kořenová složka | Přiřazeno zákazníkem |
| Mandát 2 | Kořenová složka | Přiřazeno zákazníkem |
| Řadič BMC | Kořenová složka | Přiřazeno zákazníkem |
| PDU 1 | kořen | Přiřazeno zákazníkem |
| PDU 2 | kořen | Přiřazeno zákazníkem |
| Sér | Kořenová složka | Přiřazeno zákazníkem |
| Avocent | Správce kořenového & | Přiřazeno zákazníkem |
