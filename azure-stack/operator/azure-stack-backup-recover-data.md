---
title: Zotavení z závažné ztráty dat v Azure Stack pomocí služby Infrastructure Backup | Microsoft Docs
description: Když závažná chyba způsobí, že Azure Stack selže, může vaše data infrastruktury při opětovném vytváření nasazení Azure Stack obnovit.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: 2ECE8580-0BDE-4D4A-9120-1F6771F2E815
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 11/05/2018
ms.openlocfilehash: b6b6796f5d47189499e01c94b9c988dbf03091bb
ms.sourcegitcommit: f6ea6daddb92cbf458f9824cd2f8e7e1bda9688e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2019
ms.locfileid: "68494000"
---
# <a name="recover-from-catastrophic-data-loss"></a>Obnovit ze ztráty dat

*Platí pro: Azure Stack integrovaných systémů.*

Azure Stack spouští služby Azure ve vašem datovém centru a může běžet v prostředích tak, jak je to malé jako čtyři uzly nainstalované v jednom stojanu. Naproti tomu Azure běží ve více než 40 oblastech v několika datových centrech a v každé oblasti je víc zón. Prostředky uživatelů mohou zahrnovat několik serverů, stojanů, datových center a oblastí. V případě Azure Stack máte momentálně možnost nasadit celý Cloud pouze do jednoho stojanu. Tím se váš Cloud zveřejňuje rizikem závažných událostí v datovém centru nebo selhání z důvodu důležitých chyb produktu. Když dojde k havárii, Azure Stack instance přejde do režimu offline. Všechna data mohou být neobnovitelné.

V závislosti na hlavní příčině ztráty dat možná budete muset opravit jednu službu infrastruktury nebo obnovit celou instanci Azure Stack. Dokonce je možné, že budete muset obnovit na jiný hardware ve stejném umístění nebo v jiném umístění.

Tento scénář se věnuje zotavení celé instalace v případě selhání zařízení a opětovnému nasazení privátního cloudu.

| Scénář                                                           | Ztráta dat                            | Požadavky                                                             |
|--------------------------------------------------------------------|--------------------------------------|----------------------------------------------------------------------------|
| Zotavení z závažné ztráty dat kvůli havárii nebo chybě produktu | Všechna data infrastruktury a uživatelů a aplikací | Uživatelská aplikace a data jsou chráněny odděleně od dat infrastruktury. |

## <a name="workflows"></a>Workflows

Cesta k ochraně Azure Start začíná zálohováním infrastruktury a dat aplikace/tenanta samostatně. Tento dokument popisuje, jak chránit infrastrukturu. 

![Počáteční nasazení Azure Stack](media/azure-stack-backup/azure-stack-backup-workflow1.png)

V nejhorších případech, kdy dojde ke ztrátě všech dat, obnovení Azure Stack je proces obnovování dat infrastruktury jedinečných pro toto nasazení Azure Stack a veškerá uživatelská data. 

![Znovu nasadit Azure Stack](media/azure-stack-backup/azure-stack-backup-workflow2.png)

## <a name="restore"></a>Obnovit

Pokud dojde k závažné ztrátě dat, ale hardware je stále použitelný, vyžaduje se opětovné nasazení Azure Stack. Během opětovného nasazení můžete zadat umístění úložiště a přihlašovací údaje požadované pro přístup k zálohám. V tomto režimu není nutné zadávat služby, které je nutné obnovit. Infrastructure Backup Controller vloží stav roviny ovládacího prvku jako součást pracovního postupu nasazení.

Pokud dojde k havárii, která vykreslí nepoužitý hardware, je možné opětovné nasazení provést pouze na novém hardwaru. Opětovné nasazení může trvat několik týdnů, než se v datacentru seřadí a dorazí na náhradní hardware. Obnovení dat řídicí roviny je možné kdykoli. Obnovení ale není podporované, pokud verze znovu nasazené instance je víc než jedna verze, která se používá při poslední záloze. 

| Režim nasazení | Výchozí bod | Koncový bod                                                                                                                                                                                                     |
|-----------------|----------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Vyčistit instalaci   | Směrný Build | Výrobce OEM nasadí Azure Stack a aktualizace nejnovější podporované verze.                                                                                                                                          |
| Režim obnovení   | Směrný Build | Výrobce OEM nasadí Azure Stack do režimu obnovení a zpracovává požadavky na porovnání verzí na základě nejnovější dostupné zálohy. Výrobce OEM dokončí nasazení aktualizací na nejnovější podporovanou verzi. |

## <a name="data-in-backups"></a>Data v zálohách

Azure Stack podporuje typ nasazení nazvaný režim Cloud Recovery. Tento režim se používá jenom v případě, že se rozhodnete obnovit Azure Stack po havárii nebo chybě produktu, kterou řešení vykreslilo, je neobnovitelné. Tento režim nasazení neobnovuje žádná uživatelská data uložená v řešení. Rozsah tohoto režimu nasazení je omezený na obnovení následujících dat:

 - Vstupy nasazení
 - Data služby interní identity (nasazení ADFS)
 - Konfigurace federovaného určení (nasazení ADFS)
 - Kořenové certifikáty používané interní certifikační autoritou
 - Azure Resource Manager konfigurační data uživatelů, jako jsou předplatná, plány, nabídky a kvóty pro úložiště, síťové a výpočetní prostředky
 - Tajné kódy trezoru klíčů a trezory
 - Přiřazení zásad RBAC a přiřazení rolí 

Žádná z prostředků infrastruktury uživatele jako služba (IaaS) ani platforma jako služba (PaaS) se během nasazování neobnovuje. To znamená, že dojde ke ztrátě IaaS virtuálních počítačů, účtů úložiště, objektů blob, tabulek, konfigurace sítě atd. Účelem cloudového obnovení je zajistit, aby se operátoři a uživatelé mohli po dokončení nasazení přihlásit zpátky na portál. Uživatelům, kteří se přihlašují znovu, se nezobrazí žádné prostředky. Uživatelé mají obnovené předplatné a společně s tím původní plány a nabízí zásady definované správcem. Uživatelé, kteří se přihlašují zpátky do systému, budou pracovat se stejnými omezeními, která jsou zavedená v původním řešení před havárií. Po dokončení cloudového obnovení může operátor ručně obnovit hodnoty – přidat a RPs třetí strany a přidružená data.

## <a name="next-steps"></a>Další postup

Seznamte se s osvědčenými postupy pro [používání služby Infrastructure Backup](azure-stack-backup-best-practices.md).
