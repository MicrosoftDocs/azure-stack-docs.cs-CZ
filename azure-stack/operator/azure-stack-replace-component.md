---
title: Výměna hardwarové součásti na uzlu jednotky škálování centra Azure Stack
titleSuffix: Azure Stack Hub
description: Naučte se, jak nahradit hardwarovou součást v integrovaném systému Azure Stack hub.
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
ms.openlocfilehash: 715bd2b3bb5797ff43272a7b721edd03bb2c1b4c
ms.sourcegitcommit: ce01b2cd114ca8ab5b70c6311b66c58ceb054469
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/13/2020
ms.locfileid: "75924396"
---
# <a name="replace-a-hardware-component-on-an-azure-stack-hub-scale-unit-node"></a>Výměna hardwarové součásti na uzlu jednotky škálování centra Azure Stack

Tento článek popisuje obecný proces nahrazení hardwarových komponent, které nejsou Hot-swaped. Skutečné kroky náhrady se liší v závislosti na dodavateli hardwaru OEM (Original Equipment Manufacturer). Podrobné pokyny, které jsou specifické pro váš integrovaný systém Azure Stack hub, najdete v dokumentaci k umístění jednotky v poli dodavatele.

> [!CAUTION]  
> Úroveň firmwaru je zásadní pro úspěch operace popsané v tomto článku. Chybějící tento krok může vést k nestabilitě systému, poklesu výkonu, vláknům zabezpečení nebo zabrání automatizaci centra Azure Stack v nasazení operačního systému. Při nahrazování hardwaru vždy projděte dokumentaci k vašemu hardwarovému partnerovi, aby se zajistilo, že aplikovaný firmware odpovídá verzi OEM zobrazené na [portálu pro správu centra Azure Stack](azure-stack-updates.md).

| Hardwarový partner | Region (Oblast) | Adresa URL |
|------------------|--------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Cisco | Všechno | [Příručka k provoznímu systému Cisco Integrated System for Microsoft Azure Stack hub](https://www.cisco.com/c/en/us/td/docs/unified_computing/ucs/azure-stack/b_Azure_Stack_Operations_Guide_4-0/b_Azure_Stack_Operations_Guide_4-0_chapter_00.html#concept_wks_t1q_wbb)<br><br>[Poznámky k verzi integrovaného systému Cisco pro Centrum Microsoft Azure Stack](https://www.cisco.com/c/en/us/support/servers-unified-computing/ucs-c-series-rack-mount-ucs-managed-server-software/products-release-notes-list.html) |
| Dell EMC | Všechno | [Cloud pro Microsoft Azure Stack hub 14G (účet a přihlášení požadováno)](https://support.emc.com/downloads/44615_Cloud-for-Microsoft-Azure-Stack-14G)<br><br>[Cloud pro Microsoft Azure Stack hub 13G (účet a přihlášení požadováno)](https://support.emc.com/downloads/42238_Cloud-for-Microsoft-Azure-Stack-13G) |
| Fujitsu | Japonsko | [Oddělení podpory spravované služby Fujitsu (účet a přihlášení je povinné)](https://eservice.fujitsu.com/supportdesk-web/) |
|  | EVROPA, STŘEDNÍ VÝCHOD A AFRIKA | [Společnosti Fujitsu podporují IT produkty a systémy](https://support.ts.fujitsu.com/IndexContact.asp?lng=COM&ln=no&LC=del) |
|  | EU | [Fujitsu MySupport (vyžaduje se účet a přihlášení)](https://support.ts.fujitsu.com/IndexMySupport.asp) |
| HPE | Všechno | [HPE pro Centrum Microsoft Azure Stack](http://www.hpe.com/info/MASupdates) |
| Lenovo | Všechno | [Nejlepší recepty ThinkAgile SXM](https://datacentersupport.lenovo.com/us/en/solutions/ht505122)
| Wortmann |  | [Balíček OEM/firmware](https://aka.ms/AA6z600)<br>[dokumentace centra Terra Azure Stack (včetně jednotky FRU)](https://aka.ms/aa6zktc)

Mezi součásti, které nejsou Hot-swapem, patří tyto položky:

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

\* * Jestli dodavatel hardwaru OEM provede nahrazení komponenty a aktualizuje firmware v závislosti na vaší smlouvě o podpoře.

## <a name="review-alert-information"></a>Kontrola informací o výstrahách

Systém Azure Stackho centra stavů a monitorování sleduje stav síťových adaptérů a datových jednotek řízených Prostory úložiště s přímým přístupem. Nesleduje jiné hardwarové součásti. U všech ostatních hardwarových součástí se výstrahy vyvolají v řešení monitorování hardwaru specifického pro dodavatele, které běží na hostiteli životního cyklu hardwaru.  

## <a name="component-replacement-process"></a>Proces nahrazení součásti

Následující kroky obsahují podrobný přehled procesu nahrazení komponent. Neprovádějte tyto kroky bez odkazování na dokumentaci k prostředí FRU dodávané výrobcem OEM.

1. K bezproblémovému vypnutí uzlu jednotky škálování použijte akci vypnutí. Tato akce se nemusí vyžadovat na základě fyzické podmínky hardwaru.

2. V nepravděpodobném případě se akce vypnutí nezdařila, pomocí akce [vyprázdnění](azure-stack-node-actions.md#drain) umístěte uzel jednotky škálování do režimu údržby. Tato akce se nemusí vyžadovat na základě fyzické podmínky hardwaru.

   > [!NOTE]  
   > V každém případě je možné zakázat a vypnout pouze jeden uzel ve stejnou dobu, aniž by došlo k přerušení S2D (Prostory úložiště s přímým přístupem).

3. Po rozchodu uzlu jednotky škálování v režimu údržby použijte akci [vypínání](azure-stack-node-actions.md#scale-unit-node-actions) . Tato akce se nemusí vyžadovat na základě fyzické podmínky hardwaru.

   > [!NOTE]  
   > V nepravděpodobném případě akce vypnutí nefunguje, místo toho použijte webové rozhraní řadiče pro správu základní desky (BMC).

4. Nahraďte poškozenou hardwarovou součást. Bez ohledu na to, jestli dodavatel hardwaru OEM může nahradit součást, se může lišit v závislosti na vaší smlouvě o podpoře.  
5. Aktualizujte firmware. Použijte proces aktualizace firmwaru specifický pro dodavatele pomocí hostitele životního cyklu životního cyklu a ujistěte se, že nahrazená hardwarová součást má použitu schválenou úroveň firmwaru. Bez ohledu na to, jestli se Váš dodavatel hardwaru OEM může tento krok lišit v závislosti na vaší smlouvě o podpoře.  
6. Pomocí akce [opravit](azure-stack-node-actions.md#scale-unit-node-actions) přeneste uzel jednotky škálování zpátky do jednotky škálování.
7. Pomocí privilegovaného koncového bodu [Ověřte stav opravy virtuálního disku](azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair-using-the-privileged-endpoint). S novými datovými jednotkami může úplná úloha opravy úložiště trvat několik hodin v závislosti na zatížení systému a spotřebovaném prostoru.
8. Po dokončení akce opravy ověřte, zda byly všechny aktivní výstrahy automaticky uzavřeny.

## <a name="next-steps"></a>Další kroky

- Informace o nahrazení fyzického disku s výměnou za provozu najdete v tématu [Výměna disku](azure-stack-replace-disk.md).
- Informace o nahrazení fyzického uzlu najdete v tématu [nahrazení uzlu jednotky škálování](azure-stack-replace-node.md).
