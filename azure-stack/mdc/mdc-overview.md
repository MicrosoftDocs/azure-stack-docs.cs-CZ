---
title: Přehled datových center v modulárních |
description: Modulární datové centrum je přenosné a rychle nasazovatelné datacentrum, které je vhodné k podpoře rozsáhlých bezproblémových operací v dočasných a pevných příkazových příspěvcích.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: overview
ms.date: 01/13/2020
ms.reviewer: prchint
ms.lastreviewed: 01/13/2020
ms.openlocfilehash: d2803f1968be7a4a3e64f65f3d86a2dbb41aaf1c
ms.sourcegitcommit: e4e2cc6a68f02c3e856f58ca5ee51b3313c7ff8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92182962"
---
# <a name="modular-data-center-overview"></a>Přehled modulárních datových center 

Modulární datové centrum (MDC) je založené na centru Azure Stack. MDC je přenosné a rychle nasazovatelné datové centrum, které je vhodné k podpoře rozsáhlého boje proti operacím v dočasných a pevných příkazových příspěvcích.

Centrum Azure Stack je horizontální a vertikálně škálovatelné řešení, které poskytuje více tenantů, nativní hybridní cloudové funkce pro služby IaaS a PaaS pro prostředí na hraničních zařízeních a podporuje celou řadu modulárních scénářů pro dočasné a pevné příkazy post a Expeditionary. Centrum Azure Stack je integrované hardwarové a softwarové zařízení, které je komerčně dostupné v různých kapacitách na základě škálování přes jednotky škálování uzlů a je k dispozici s rozšířeními, Pro obecné účely včetně konfigurací s povoleným grafickým procesorem (GPU) a rozšiřitelným externím úložištěm.

## <a name="how-you-can-use-the-mdc"></a>Jak můžete použít MDC

Azure Stack dodává se čtyřmi základními principy, které se konzistentně týkají, a rozšiřuje možnosti Azure pro modulární hraniční scénáře. 

### <a name="develop-and-deliver-apps-with-a-common-devops-model-including-api-symmetry-with-azure"></a>Vývoj a doručování aplikací pomocí společného modelu DevOps, včetně rozhraní API symetrie s Azure

Konzistence mezi Azure a Azure Stack znamená řešení pro War stíhacích, která se nasazují pro podporu různých případů použití, a zabezpečená a udržovaná pomocí společné sady nástrojů, jako je Azure Key Vault pro správu klíčů a Azure Monitor pro monitorování a správu prostředků. Azure Stack se vzájemně spolupracují s místními daty, aplikacemi a nástroji pro DevOps a zabezpečené operace, jako je například Správa klíčů.

### <a name="deliver-azure-services-on-premises"></a>Místní doručování služeb Azure

Azure Stack se spouští v nevýhodných komunikačních prostředích (s riziky, která jsou v dosahu, zahlcená nebo zakázaná), stejně jako robustní komunikační prostředí a nezávisí na připojení k Azure za účelem provádění aplikací a povolení místních operací. 

### <a name="use-integrated-hardware-and-software-delivery-experience"></a>Použití integrovaného prostředí pro doručování hardwaru a softwaru

Modulární Edge vyžaduje škálu funkcí, které poskytují nejen standardní výpočetní služby a služby úložiště, ale také pokročilé funkce pro strojové učení, AI a analýzy. Možnost připojit se k zabezpečenému cloudu z modulárního okraje, pokud je to možné, nebo naopak, aby fungoval nezávisle na tom, jak je v prostředí austere, je důležité zajistit přístup k datům, která jsou potřebná pro rozhodování, v War Fighters.

### <a name="keep-your-datacenter-secure-and-available-with-hybrid-cloud-security-operations"></a>Udržujte své datacentrum zabezpečené a dostupné s hybridními operacemi zabezpečení cloudu

Nativní návrh cloudu Azure Stack odstraňuje provozní složitosti tradičních virtualizačních prostředí tím, že umožňuje správcům zvolit, kdy se má provést oprava a orchestrace celé operace prostřednictvím procesu instalace a prostředků infrastruktury pro správu Azure Stack sestavených nativně do systému.

Vzdálená pomocná Správa od společnosti Microsoft nebo nabídka spravovaná Microsoftem v poli pro Azure Stack zařízení je možné povolit tím, že uživatelům s příslušným přístupem prostřednictvím RBAC umožníte provádět akce prostřednictvím portálu pro správu, koncového bodu správy privilegovaného prostředí nebo rozhraní příkazového řádku. To umožňuje, aby se všechny opravy a další aktivity pro správu a monitorování prováděly Microsoftem. Azure Stack je pole, které lze aktualizovat, prostřednictvím portálu pro zabezpečené správy Command-Line nebo pomocí příkazů rozhraní příkazového řádku (CLI), které používají aktualizace zabezpečení a aktualizace funkcí IaaS a PaaS, aby se zajistila Komerční parita v Azure, kde je to vhodné z místních nebo vzdálených sítí. 

## <a name="benefits-of-using-the-mdc"></a>Výhody použití MDC

MDC podporuje konzistentní prostředí s Azure v nevýhodných komunikačních prostředích:
 - Statická a modulární a rychle zaváděná datová centra s Azure Cloud Services umožňují používat rozsáhlé analytické aplikace v modulárních provozních centrech (TOC).
 - Modulární hraniční nabídky Azure zachovávají konzistenci mezi cloudem a hraničním přístupem k podpoře IaaS primitivních elementů, jako jsou virtuální počítače, úložiště a virtuální sítě.
 - Azure Active Directory a podpora řízení přístupu na základě rolí
 - Společná rozhraní pro správu
 - Symetrie rozhraní API a podpora Microsoftu, třetích stran a open source nástrojů DevOps
 - Správa a monitorování prostřednictvím služby Azure Log Analytics a Azure Security Center
 - Povolením hybridního cloudu můžete přinést flexibilitu cloud computingu pro vaše místní prostředí a hraniční zařízení.<br>Další možnosti:
     - Používejte kód a spouštějte nativní cloudové aplikace konzistentně napříč Azure a místními prostředími.
     - Spouštějte tradiční virtualizované úlohy s volitelnými připojeními ke službám Azure.
     - Přeneste data do cloudu nebo je udržujte v datacentru svrchovan, abyste zachovali dodržování předpisů.
     - Spusťte hardwarově akcelerované strojové učení, kontejnery nebo virtualizované úlohy, a to vše na inteligentních hraničních zařízeních.

## <a name="next-steps"></a>Další kroky

[Plánování kapacity centra Azure Stack](../operator/azure-stack-capacity-planning-overview.md)
