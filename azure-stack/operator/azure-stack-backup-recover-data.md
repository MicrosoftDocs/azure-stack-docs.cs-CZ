---
title: Obnovení z závažné ztráty dat v centru Azure Stack
description: Naučte se obnovovat a obnovovat data infrastruktury v Azure Stack hub po závažných ztrátách dat.
author: justinha
ms.topic: article
ms.date: 10/23/2020
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 10/23/2020
ms.openlocfilehash: 35d55eec6c1311c39014f94f94fb04d39c7acb3b
ms.sourcegitcommit: 25f6211aa16308d50315872f647d840f402fa62e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2020
ms.locfileid: "92496414"
---
# <a name="recover-from-catastrophic-data-loss"></a>Obnovení z katastrofické ztráty dat

Služba Azure Stack hub spouští služby Azure ve vašem datovém centru a může běžet v prostředích jako malá, protože čtyři uzly nainstalované v jednom stojanu. Naproti tomu Azure běží ve více než 40 oblastech v několika datových centrech a v každé oblasti je víc zón. Prostředky uživatelů mohou zahrnovat několik serverů, stojanů, datových center a oblastí. Pomocí centra Azure Stack máte momentálně možnost nasadit celý Cloud do jednoho racku. Toto omezení zpřístupňuje Cloud riziku závažných událostí v datovém centru nebo selhání kvůli důležitým chybám produktu. Když dojde k výpadku, instance centra Azure Stack přejde do režimu offline. Všechna data mohou být neobnovitelné.

V závislosti na hlavní příčině ztráty dat možná budete muset opravit jednu službu infrastruktury nebo obnovit celou instanci centra Azure Stack. Dokonce je možné, že budete muset obnovit na jiný hardware ve stejném umístění nebo v jiném umístění.

Tento scénář řeší celou instalaci, pokud dojde k selhání a opětovnému nasazení privátního cloudu.

| Scénář                                                           | Ztráta dat                            | Požadavky                                                             |
|--------------------------------------------------------------------|--------------------------------------|----------------------------------------------------------------------------|
| Zotavení z závažné ztráty dat kvůli havárii nebo chybě produktu. | Všechna data infrastruktury a uživatelů a aplikací. | Může obnovit jiný výrobce OEM.<br/> Může obnovit na jinou generaci hardwaru.<br/> Můžete obnovit na jiný počet uzlů jednotky škálování.<br/> Uživatelská aplikace a data jsou chráněny odděleně od dat infrastruktury. |

## <a name="workflows"></a>Pracovní postupy

Cesta k ochraně centra Azure Stack začíná zálohováním infrastruktury a dat aplikace/tenanta samostatně. Tento dokument popisuje, jak chránit infrastrukturu. 

![Pracovní postup obnovení dat centra Azure Stack – nasazení](media/azure-stack-backup/azure-stack-backup-workflow1.png)

V nejhorších případech, kdy dojde ke ztrátě všech dat, je obnovení Azure Stackho centra proces obnovování dat infrastruktury jedinečných pro toto nasazení Azure Stackho centra a všech uživatelských dat. 

![Pracovní postup obnovení dat centra Azure Stack – opětovné nasazení](media/azure-stack-backup/azure-stack-backup-workflow2.png)

## <a name="restore"></a>Obnovení

Pokud dojde ke zhoršení ztrátám dat, ale hardware je stále použitelný, je nutné znovu nasadit centrum Azure Stack. Během opětovného nasazení můžete zadat umístění úložiště a přihlašovací údaje požadované pro přístup k zálohám. V tomto režimu není nutné zadávat služby, které je nutné obnovit. Infrastructure Backup Controller vloží stav roviny ovládacího prvku jako součást pracovního postupu nasazení.

Pokud dojde k havárii, která vykreslí nepoužitelný hardware, je možné znovu nasadit pouze na novém hardwaru. Opětovné nasazení může trvat několik týdnů, než se v datacentru seřadí a dorazí na náhradní hardware. Obnovení dat řídicí roviny je možné kdykoli. Obnovení se ale nepodporuje, pokud verze znovu nasazené instance je víc než jedna verze, která se používá při poslední záloze.

| Režim nasazení | Výchozí bod | Koncový bod                                                                                                                                                                                                     |
|-----------------|----------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Vyčistit instalaci   | Směrný Build | Výrobce OEM nasadí centrum Azure Stack a aktualizace na nejnovější podporovanou verzi.                                                                                                                                          |
| Režim obnovení   | Směrný Build | Výrobce OEM nasadí Azure Stack centrum v režimu obnovení a zpracovává požadavky na porovnání verzí na základě nejnovější dostupné zálohy. Výrobce OEM dokončí nasazení aktualizací na nejnovější podporovanou verzi. |

## <a name="data-in-backups"></a>Data v zálohách

Centrum Azure Stack podporuje typ nasazení s názvem režim Cloud Recovery. Tento režim se používá jenom v případě, že se rozhodnete pro obnovení centra Azure Stack po havárii nebo chybě produktu, že řešení není možné obnovit. Tento režim nasazení neobnovuje žádná uživatelská data uložená v řešení. Rozsah tohoto režimu nasazení je omezený na obnovení následujících dat:

 - Vstupy nasazení
 - Data služby interní identity
 - Konfigurace federovaného určení (nasazení ADFS)
 - Kořenové certifikáty používané interní certifikační autoritou
 - Azure Resource Manager konfigurační data uživatelů, jako jsou předplatná, plány, nabídky, skupiny prostředků, značky, kvóty úložiště, síťové kvóty a výpočetní prostředky.
 - Key Vault tajných klíčů a trezorů.
 - Přiřazení zásad RBAC a přiřazení rolí

Žádná z prostředků infrastruktury uživatele jako služba (IaaS) ani platforma jako služba (PaaS) se během nasazování neobnovuje. Mezi tyto ztráty patří virtuální počítače s IaaS, účty úložiště, objekty blob, tabulky, konfigurace sítě a tak dále. Účelem cloudového obnovení je zajistit, aby se operátoři a uživatelé mohli po dokončení nasazení znovu přihlásit k portálu. Uživatelům, kteří se přihlašují zpátky, se nezobrazují žádné prostředky. Uživatelé mají obnovené předplatné a společně s tím původní plány, nabídky a zásady definované správcem. Uživatelé, kteří se přihlašují zpátky do systému, pracují se stejnými omezeními, která jsou před haváriem způsobená původními řešeními. Po dokončení cloudového obnovení může operátor ručně obnovit hodnoty – přidat a RPs třetí strany a přidružená data.

## <a name="validate-backups"></a>Ověřit zálohy 

ASDK můžete použít k otestování zálohy a potvrzení, že jsou data platná a použitelná. Další informace najdete v tématu [použití ASDK k ověření zálohy Azure Stack](../asdk/asdk-validate-backup.md).

## <a name="next-steps"></a>Další kroky

Seznamte se s osvědčenými postupy pro [používání služby Infrastructure Backup](azure-stack-backup-best-practices.md).
