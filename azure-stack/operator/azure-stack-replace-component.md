---
title: Výměna hardwarové součásti na uzlu jednotky škálování Azure Stack | Microsoft Docs
description: Přečtěte si, jak nahradit hardwarovou součást v Azure Stack integrovaném systému.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2019
ms.author: thoroet
ms.lastreviewed: 07/18/2019
ms.openlocfilehash: 4cb8da451743bc6a8e15c57aacf28f0aa83258c9
ms.sourcegitcommit: 4f3e161e7632c8a6e3d41946b09f22b5bdb08d36
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68413144"
---
# <a name="replace-a-hardware-component-on-an-azure-stack-scale-unit-node"></a>Výměna hardwarové součásti na uzlu jednotky škálování Azure Stack

*Platí pro: Azure Stack integrovaných systémů*

Tento článek popisuje obecný proces nahrazení hardwarových komponent, které nejsou Hot-swaped. Skutečné kroky náhrady se liší v závislosti na dodavateli hardwaru OEM (Original Equipment Manufacturer). Podrobné pokyny, které jsou specifické pro váš Azure Stack integrovaný systém, najdete v dokumentaci k umístění jednotky v poli dodavatele.

> [!CAUTION]  
> Úroveň firmwaru je zásadní pro úspěch operace popsané v tomto článku. Chybějící tento krok může vést k nestabilitě systému, poklesu výkonu, vláknům zabezpečení nebo zabránit automatizaci Azure Stack k nasazení operačního systému. Při nahrazování hardwaru vždy projděte dokumentaci k vašemu hardwarovému partnerovi, aby se zajistilo, že aplikovaný firmware odpovídá verzi OEM zobrazené na [portálu Azure Stack pro správu](azure-stack-updates.md).

| Hardwarový partner | Oblast | URL |
|------------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Cisco | Vše | [Příručka k operačnímu systému Cisco Integrated System for Microsoft Azure Stack](https://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/azure-stack/b_Azure_Stack_Operations_Guide_4-0/b_Azure_Stack_Operations_Guide_4-0_chapter_00.html#concept_wks_t1q_wbb)<br><br>[Poznámky k verzi integrovaného systému Cisco pro Microsoft Azure Stack](https://www.cisco.com/c/en/us/support/servers-unified-computing/ucs-c-series-rack-mount-ucs-managed-server-software/products-release-notes-list.html) |
| Dell EMC | Vše | [Cloud pro Microsoft Azure Stack 14G (vyžaduje se účet a přihlášení)](https://support.emc.com/downloads/44615_Cloud-for-Microsoft-Azure-Stack-14G)<br><br>[Cloud pro Microsoft Azure Stack 13G (vyžaduje se účet a přihlášení)](https://support.emc.com/downloads/42238_Cloud-for-Microsoft-Azure-Stack-13G) |
| Fujitsu | JAPONSKO | [Oddělení podpory spravované služby Fujitsu (vyžaduje se účet a přihlášení)](https://eservice.fujitsu.com/supportdesk-web/) |
|  | EVROPA, STŘEDNÍ VÝCHOD A AFRIKA | [Společnosti Fujitsu podporují IT produkty a systémy](https://support.ts.fujitsu.com/IndexContact.asp?lng=COM&ln=no&LC=del) |
|  | EVROPA | [Fujitsu MySupport (vyžaduje se účet a přihlášení)](https://support.ts.fujitsu.com/IndexMySupport.asp) |
| HPE | Vše | [HPE pro Microsoft Azure Stack](http://www.hpe.com/info/MASupdates) |
| Lenovo | Vše | [Nejlepší recepty ThinkAgile SXM](https://datacentersupport.lenovo.com/us/en/solutions/ht505122)
| Wortmann |  | [Balíček OEM/firmware](https://drive.terracloud.de/dl/fiTdTb66mwDAJWgUXUW8KNsd/OEM)<br>[dokumentace k Terra Azure Stack (včetně jednotky FRU)](https://drive.terracloud.de/dl/fiWGZwCySZSQyNdykXCFiVCR/TerraAzSDokumentation)

Mezi součásti, které nejsou Hot-swap, patří následující:

- VČETNĚ
- Rezident
- Karta pro správu základní desky/řadiče pro správu základní desky (BMC)/video
- Řadič disku/adaptér hostitelské sběrnice (HBA)/backplane
- Síťový adaptér (NIC)
- Disk s operačním systémem *
- Datové jednotky (jednotky, které nepodporují Hot swap, například karty doplňků PCI-e) *

\* Tyto součásti můžou podporovat Hot swap, ale můžou se lišit v závislosti na implementaci od dodavatele. Podrobné pokyny najdete v dokumentaci k prostředí FRU dodavatele OEM.

Následující vývojový diagram znázorňuje proces obecného procesu FRU, který nahradí nehotou hardwarovou součást, kterou nelze vyměnit za horkou.

![Vývojový diagram znázorňující tok nahrazení součástí](media/azure-stack-replace-component/replacecomponentflow.PNG)

* Tato akce se nemusí vyžadovat na základě fyzické podmínky hardwaru.

\* * Jestli dodavatel hardwaru výrobce OEM provede nahrazení komponenty a aktualizace firmwaru se může lišit v závislosti na vaší smlouvě o podpoře.

## <a name="review-alert-information"></a>Kontrola informací o výstrahách

Systém Azure Stack stav a monitorování sleduje stav síťových adaptérů a datových jednotek kontrolovaných Prostory úložiště s přímým přístupem. Nesleduje další hardwarové součásti. U všech ostatních hardwarových součástí se výstrahy vyvolají v řešení monitorování hardwaru specifického pro dodavatele, které běží na hostiteli životního cyklu hardwaru.  

## <a name="component-replacement-process"></a>Proces nahrazení součásti

Následující kroky obsahují podrobný přehled procesu nahrazení komponent. Neprovádějte tyto kroky, aniž byste odkazovali na dokumentaci k FRU dodávané výrobcem OEM.

1. K bezproblémovému vypnutí uzlu jednotky škálování použijte akci vypnutí. Tato akce se nemusí vyžadovat na základě fyzické podmínky hardwaru.

2. V nepravděpodobném případě se akce vypnutí nezdařila, [](azure-stack-node-actions.md#drain) pomocí akce vyprázdnění umístěte uzel jednotky škálování do režimu údržby. Tato akce se nemusí vyžadovat na základě fyzické podmínky hardwaru.

   > [!NOTE]  
   > V každém případě je možné zakázat a vypnout pouze jeden uzel ve stejnou dobu, aniž by došlo k přerušení S2D (Prostory úložiště s přímým přístupem).

3. Po rozchodu uzlu jednotky škálování v režimu údržby použijte akci [](azure-stack-node-actions.md#scale-unit-node-actions) vypínání. Tato akce se nemusí vyžadovat na základě fyzické podmínky hardwaru.

   > [!NOTE]  
   > V nepravděpodobném případě akce vypnutí nefunguje, místo toho použijte webové rozhraní řadiče pro správu základní desky (BMC).

4. Nahraďte poškozenou hardwarovou součást. Bez ohledu na to, jestli dodavatel hardwaru OEM provede nahrazení součástí, se může lišit v závislosti na vaší smlouvě o podpoře.  
5. Aktualizujte firmware. Použijte proces aktualizace firmwaru specifický pro dodavatele pomocí hostitele životního cyklu životního cyklu a ujistěte se, že nahrazená hardwarová součást má použitu schválenou úroveň firmwaru. Bez ohledu na to, jestli dodavatel hardwaru OEM provede tento krok, se může v závislosti na vaší smlouvě o podpoře lišit.  
6. Pomocí akce [opravit](azure-stack-node-actions.md#scale-unit-node-actions) přeneste uzel jednotky škálování zpátky do jednotky škálování.
7. Pomocí privilegovaného koncového bodu [Ověřte stav opravy virtuálního disku](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair-using-the-privileged-endpoint). S novými datovými jednotkami může úplná úloha opravy úložiště trvat několik hodin v závislosti na zatížení systému a spotřebovaném prostoru.
8. Po dokončení akce opravy ověřte, zda byly všechny aktivní výstrahy automaticky uzavřeny.

## <a name="next-steps"></a>Další postup

- Informace o nahrazení fyzického disku s výměnou za provozu najdete v tématu [Výměna disku](azure-stack-replace-disk.md).
- Informace o nahrazení fyzického uzlu najdete v tématu [nahrazení uzlu jednotky škálování](azure-stack-replace-node.md).
