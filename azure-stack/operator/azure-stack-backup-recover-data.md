---
title: Zotavení z závažné ztráty dat v Azure Stack | Microsoft Docs
description: Naučte se, jak obnovit a obnovit data infrastruktury v Azure Stack po závažné ztrátě dat.
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
ms.openlocfilehash: b2671446594377833609032e27ff02b7c53c763c
ms.sourcegitcommit: 245a4054a52e54d5989d6148fbbe386e1b2aa49c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2019
ms.locfileid: "70974690"
---
# <a name="recover-from-catastrophic-data-loss"></a>Obnovit ze ztráty dat

*Platí pro: Azure Stack integrovaných systémů.*

Azure Stack spouští služby Azure ve vašem datovém centru a může běžet v prostředích tak, jak je to malé jako čtyři uzly nainstalované v jednom stojanu. Naproti tomu Azure běží ve více než 40 oblastech v několika datových centrech a v každé oblasti je víc zón. Prostředky uživatelů mohou zahrnovat několik serverů, stojanů, datových center a oblastí. V případě Azure Stack máte momentálně možnost nasadit celý Cloud pouze do jednoho stojanu. Toto omezení zpřístupňuje Cloud riziku závažných událostí v datovém centru nebo selhání kvůli důležitým chybám produktu. Když dojde k havárii, Azure Stack instance přejde do režimu offline. Všechna data mohou být neobnovitelné.

V závislosti na hlavní příčině ztráty dat možná budete muset opravit jednu službu infrastruktury nebo obnovit celou instanci Azure Stack. Dokonce je možné, že budete muset obnovit na jiný hardware ve stejném umístění nebo v jiném umístění.

Tento scénář řeší celou instalaci, pokud dojde k selhání a opětovnému nasazení privátního cloudu.

| Scénář                                                           | Ztráta dat                            | Požadavky                                                             |
|--------------------------------------------------------------------|--------------------------------------|----------------------------------------------------------------------------|
| Zotavení z závažné ztráty dat kvůli havárii nebo chybě produktu. | Všechna data infrastruktury a uživatelů a aplikací. | Uživatelská aplikace a data jsou chráněny odděleně od dat infrastruktury. |

## <a name="workflows"></a>Workflows

Cesta k ochraně Azure Start začíná zálohováním infrastruktury a dat aplikace/tenanta samostatně. Tento dokument popisuje, jak chránit infrastrukturu. 

![Azure Stack pracovní postup obnovení dat – nasazení](media/azure-stack-backup/azure-stack-backup-workflow1.png)

V nejhorších případech, kdy dojde ke ztrátě všech dat, obnovení Azure Stack je proces obnovování dat infrastruktury jedinečných pro toto nasazení Azure Stack a veškerá uživatelská data. 

![Azure Stack pracovní postup obnovení dat – opětovné nasazení](media/azure-stack-backup/azure-stack-backup-workflow2.png)

## <a name="restore"></a>Obnovit

Pokud dojde k závažné ztrátě dat, ale hardware je stále použitelný, je nutné znovu nasadit Azure Stack. Během opětovného nasazení můžete zadat umístění úložiště a přihlašovací údaje požadované pro přístup k zálohám. V tomto režimu není nutné zadávat služby, které je nutné obnovit. Infrastructure Backup Controller vloží stav roviny ovládacího prvku jako součást pracovního postupu nasazení.

Pokud dojde k havárii, která vykreslí nepoužitelný hardware, je možné znovu nasadit pouze na novém hardwaru. Opětovné nasazení může trvat několik týdnů, než se v datacentru seřadí a dorazí na náhradní hardware. Obnovení dat řídicí roviny je možné kdykoli. Obnovení se ale nepodporuje, pokud verze znovu nasazené instance je víc než jedna verze, která se používá při poslední záloze.

| Režim nasazení | Výchozí bod | Koncový bod                                                                                                                                                                                                     |
|-----------------|----------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Vyčistit instalaci   | Směrný Build | Výrobce OEM nasadí Azure Stack a aktualizace nejnovější podporované verze.                                                                                                                                          |
| Režim obnovení   | Směrný Build | Výrobce OEM nasadí Azure Stack do režimu obnovení a zpracovává požadavky na porovnání verzí na základě nejnovější dostupné zálohy. Výrobce OEM dokončí nasazení aktualizací na nejnovější podporovanou verzi. |

## <a name="data-in-backups"></a>Data v zálohách

Azure Stack podporuje typ nasazení nazvaný režim Cloud Recovery. Tento režim se používá jenom v případě, že se rozhodnete obnovit Azure Stack po havárii nebo chybě produktu, kterou řešení vykreslilo, je neobnovitelné. Tento režim nasazení neobnovuje žádná uživatelská data uložená v řešení. Rozsah tohoto režimu nasazení je omezený na obnovení následujících dat:

 - Vstupy nasazení
 - Interní data služby identity (nasazení ADFS).
 - Konfigurace federovaného určení (nasazení ADFS)
 - Kořenové certifikáty používané interní certifikační autoritou
 - Azure Resource Manager konfigurační data uživatelů, jako jsou předplatná, plány, nabídky, kvóty úložiště, síťové kvóty a výpočetní prostředky.
 - Key Vault tajných klíčů a trezorů.
 - Přiřazení zásad RBAC a přiřazení rolí =.

Žádná z prostředků infrastruktury uživatele jako služba (IaaS) ani platforma jako služba (PaaS) se během nasazování neobnovuje. Mezi tyto ztráty patří virtuální počítače s IaaS, účty úložiště, objekty blob, tabulky, konfigurace sítě a tak dále. Účelem cloudového obnovení je zajistit, aby se operátoři a uživatelé mohli po dokončení nasazení znovu přihlásit k portálu. Uživatelům, kteří se přihlašují zpátky, se nezobrazují žádné prostředky. Uživatelé mají obnovené předplatné a společně s tím původní plány, nabídky a zásady definované správcem. Uživatelé, kteří se přihlašují zpátky do systému, pracují se stejnými omezeními, která jsou před haváriem způsobená původními řešeními. Po dokončení cloudového obnovení může operátor ručně obnovit hodnoty – přidat a RPs třetí strany a přidružená data.

## <a name="next-steps"></a>Další postup

Seznamte se s osvědčenými postupy pro [používání služby Infrastructure Backup](azure-stack-backup-best-practices.md).
