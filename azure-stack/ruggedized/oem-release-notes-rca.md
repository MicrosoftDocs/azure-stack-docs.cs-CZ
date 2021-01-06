---
title: Zpráva k vydání verze pro robustní výrobce OEM centra Azure Stack
description: Zpráva k vydání verze pro robustní výrobce OEM centra Azure Stack
author: sethmanheim
ms.topic: article
ms.date: 12/08/2020
ms.author: sethm
ms.reviewer: danlewi
ms.lastreviewed: 12/08/2020
ms.openlocfilehash: be7db92413bdc1223c8600a9e9fc0ff8c3fb4ba0
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2021
ms.locfileid: "97909697"
---
# <a name="azure-stack-hub-ruggedized-2008-oem-release-notes"></a>Zpráva k vydání verze 2008 OEM v centru Azure Stack

Tento článek obsahuje informace o vydaných verzích a verzích pro robustní centrum Azure Stack.

## <a name="overview"></a>Přehled

Tento dokument popisuje obsah služby Azure Stack hub narobustní aktualizace pro firmware a ovladače. Tato aktualizace zahrnuje vylepšení a opravy pro nejnovější vydání centra Azure Stack. Níže jsou uvedeny odkazy ke stažení:

* https://aka.ms/azshwpackage
* https://aka.ms/azshwmanifest

## <a name="baseline-and-document-history"></a>Základní a historii dokumentů

| Vydaná verze | Datum       | Popis změn         |
|---------|------------|--------------------------------|
| 2008    | 10/13/2020 | Aktualizace balíčků OEM 2.2.2010.5 |

## <a name="firmware"></a>Firmware

### <a name="bios"></a>Systému BIOS

| Verze vydaných verzí | Verze firmwaru | Změny |
|-----------------|------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---|---|
| 2008            | ve verzi 2.8.2            | Opravili jsme oborový problém, na kterém se 2.6.4 verze systému BIOS, prostřednictvím 2.8.1, kde se systém může během napájení v době, kdy se konfiguruje paměť, zobrazovat na spouštěcí obrazovce. Tento problém se týká konfigurace paměti DDR4 a zařízení NVDIMM-N.<br><br>Vylepšení, které řeší slabá místa zabezpečení (běžná ohrožení zabezpečení a rizika – CVE), jako je CVE-2020-0545, CVE-2020-0548 a CVE-2020-0549. |   |   |
| 2005            | 2.7.7            |                                                                                                                                                                                                                                                                                                                                                                                                               |   |   |
|                 |                  |                                                                                                                                                                                                                                                                                                                                                                                                               |   |   |

### <a name="idrac"></a>IDRAC

| Verze vydaných verzí | Verze firmwaru | Změny |
|-----------------|------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---|---|
| 2008            | 4.22.0.0         | 167411: Opravili jsme problém, který způsobil vynucené restartování systému při aktualizaci firmwaru prostřednictvím rozhraní Redfish API.<br><br>155376: Opravili jsme problém, který způsobil, že se iDRAC restart při shromažďování protokolů SupportAssist.<br><br>162778: v důsledku služby virtuální konzoly byl opraven stav s nedostatkem paměti pro iDRAC. |   |   |
| 2005            | 4.10.10.10       |                                                                                                                                                                                                                                                                                  |   |   |
|                 |                  |                                                                                                                                                                                                                                                                                  |   |   |

### <a name="nic-azure-stack-nodes"></a>NIC-Azure uzly zásobníku

| Verze vydaných verzí    | Verze firmwaru    | Změny                                                                                                          |
|--------------------|---------------------|------------------------------------------------------------------------------------------------------------------|
|     2008           |     14.27.60.08     | Opravili jsme kritický výraz pro firmware, který ukázal, že IRISC přestane reagovat kvůli init_hca čeká na vyřízení zámku toku časovače. |
|     2005           |     14.26.60.00     |                                                                                                                  |

### <a name="nic-hlh"></a>NIC – HLH

| Verze vydaných verzí    | Verze firmwaru    | Změny                                                                                                                                                                   |
|--------------------|---------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     2008           |     19.5.12         | Vyřešil se problém u adaptérů Intel (R) Ethernet X710 10Gb, ve kterých indikátor LED zařízení zůstane zapnutý po blikání, když je indikátor LED zařízení nastavený na režim blikání z nastavení zařízení F2. |
|     2005           |     19.0.12         |                                                                                                                                                                           |

### <a name="hba-azure-stack-node-capacity-drives"></a>HBA-Azure jednotky kapacity uzlu zásobníku

| Verze vydaných verzí    | Verze firmwaru    | Změny    |
|--------------------|---------------------|------------|
|     2008           |     16.17.01.00     |            |
|     2005           |     16.17.00.05     |            |

### <a name="hba-hlh-capacity-drives"></a>HBA – HLH jednotky kapacity

|     Verze vydaných verzí |     Verze firmwaru |     Změny                                                                                           |
|---------------------|----------------------|-------------------------------------------------------------------------------------------------------|
| 2005, 2008          | 25.5.7.0005          | Opravili jsme problém, kdy se kontrolér mohl při spuštění zablokovat kvůli neúplné konfiguraci nestálé paměti. |

### <a name="hba---boot-drives"></a>Jednotky pro spouštění HBA

| Verze vydaných verzí | Verze firmwaru | Změny                                                                                   |
|-----------------|------------------|-------------------------------------------------------------------------------------------|
| 2005, 2008      | 2.5.13.3024      | Pevná odlišnost PPID pro jednotky M. 2 VEDOUCÍho kontroleru na stránce inventáře hardwaru iDRAC |

### <a name="cpld"></a>CPLD

| Verze vydaných verzí | Verze firmwaru | Změny                                                                                                                                                                                                |
|-----------------|------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|     2008        |     9.0.6        | Řeší možné IDRAC zablokování během aktualizace firmwaru.<br> Bylo přidáno filtrování šumu signálu, aby se předešlo falešným chybám.<br> Změnila se mapa paměti hostitele, aby se zabránilo potenciálnímu zablokování portů front-USB. |
|     2005        |     1.0.6        |                                                                                                                                                                                                        |

### <a name="drive-fw"></a>Jednotka FW

| Verze vydaných verzí | Verze firmwaru | Změny                                |
|-----------------|------------------|----------------------------------------|
| 2008            | S402             | Povolí budoucí aktualizace firmwaru online. |
| 2005            | S401             |                                        |

## <a name="drivers"></a>Ovladače

### <a name="nic"></a>NIC

| Verze vydaných verzí | Verze firmwaru | Změny                                |
|-----------------|------------------|----------------------------------------|
| 2008            | 2.40.22511.0    |  |
| 2005            | 2.30.21713.0 |                                        |

### <a name="hba---capacity-drives"></a>Jednotky kapacity HBA

| Verze vydaných verzí | Verze firmwaru | Změny |
|-----------------|------------------|---------|
|  2005, 2008   |  2.51.25.02  |         |

### <a name="hba---boot-drives"></a>Jednotky pro spouštění HBA

| Verze vydaných verzí | Verze firmwaru | Změny |
|-----------------|------------------|---------|
|  2005, 2008   |  1.2.0.1051 |         |

### <a name="intel-chipset"></a>Chipset Intel

| Verze vydaných verzí | Verze firmwaru | Změny |
|-----------------|------------------|---------|
|  2005, 2008   | 10.1.18243.8188 |         |
