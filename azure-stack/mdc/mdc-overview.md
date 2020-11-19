---
title: Přehled modulárního datacentra |
description: Modulární datacentrum Azure je přenosné a rychle nasazovatelné datové centrum, které je vhodné k podpoře rozsáhlých bezproblémových operací v dočasných a pevných příkazových příspěvcích.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: overview
ms.date: 11/06/2020
ms.reviewer: prchint
ms.lastreviewed: 11/06/2020
ms.openlocfilehash: eabafd31d25a59cd43daa1a0779afed0a80f8354
ms.sourcegitcommit: 25b234330df4e753ed2dd480c208ec88cd90234c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2020
ms.locfileid: "94924312"
---
# <a name="modular-datacenter-overview"></a>Přehled modulárního datacentra

Modulární datacentrum Azure (MDC) je založené na centru Azure Stack. MDC je přenosné a rychle nasazovatelné datové centrum, které je vhodné k podpoře rozsáhlého boje proti operacím v dočasných a pevných příkazových příspěvcích.

Centrum Azure Stack je horizontální a vertikálně škálovatelné řešení, které poskytuje víceklientské možnosti hybridního cloudu pro infrastrukturu jako službu (IaaS) a PaaS (Platform as a Service) pro prostředí na hraničních zařízeních. Centrum Azure Stack podporuje celou řadu modulárních scénářů pro dočasné a pevné příkazy post a Expeditionary síly.

Centrum Azure Stack je integrované hardwarové a softwarové zařízení, které je komerčně dostupné v různých kapacitách na základě škálování přes jednotky škály uzlů. Centrum Azure Stack je k dispozici s rozšířeními, která zahrnují konfigurace s podporou grafického procesoru (GPU) pro obecné účely a rozšiřitelné externí úložiště.

## <a name="use-mdc"></a>Použití MDC

Azure Stack dodává se čtyřmi základními principy, které se konzistentně týkají, a rozšiřuje možnosti Azure pro modulární hraniční scénáře.

### <a name="develop-and-deliver-apps-with-a-common-devops-model-including-api-symmetry-with-azure"></a>Vývoj a doručování aplikací pomocí společného modelu DevOps, včetně rozhraní API symetrie s Azure

Konzistence mezi Azure a Azure Stack znamená, že řešení se vyvíjí jednou, nasazená tak, aby podporovala různé případy použití a byla zabezpečená a udržovaná pomocí společné sady nástrojů. Mezi příklady nástrojů patří Azure Key Vault pro správu klíčů a Azure Monitor pro monitorování a správu prostředků. Azure Stack se vzájemně spolupracují s místními daty, aplikacemi a nástroji pro DevOps a zabezpečené operace, jako je například Správa klíčů.

### <a name="deliver-azure-services-on-premises"></a>Místní doručování služeb Azure

Azure Stack běží v nevýhodných komunikačních prostředích a robustních komunikačních prostředích. Azure Stack není závislý na připojení k Azure, aby mohla provádět aplikace, a umožňuje místní operace.

### <a name="use-integrated-hardware-and-software-delivery-experience"></a>Použití integrovaného prostředí pro doručování hardwaru a softwaru

Modulární Edge vyžaduje škálu funkcí, které poskytují výpočetní služby a služby úložiště a také pokročilé funkce pro strojové učení, AI a analýzy. Možnost připojit se k zabezpečenému cloudu z modulárního okraje, pokud je to možné nebo naopak naopak nezávisle na tom, aby fungovala v austere prostředích, je zásadní pro poskytování přístupu k datům potřebným pro rozhodování.

### <a name="keep-your-datacenter-secure-and-available-with-hybrid-cloud-security-operations"></a>Udržujte své datacentrum zabezpečené a dostupné s hybridními operacemi zabezpečení cloudu

Cloudový nativní návrh Azure Stack odstraňuje provozní složitosti tradičních virtualizačních prostředí. Správci můžou zvolit, kdy se má provést oprava a orchestrace celé operace prostřednictvím procesu instalace a prostředků infrastruktury správy Azure Stack sestavených nativně do systému.

Vzdálená pomocná Správa od společnosti Microsoft nebo nabídka spravovaná Microsoftem v poli pro Azure Stack zařízení může být povolená tím, že uživatelům s příslušným přístupem prostřednictvím řízení přístupu na základě role (RBAC) provede akce prostřednictvím portálu pro správu, privilegovaného koncového bodu správy nebo rozhraní příkazového řádku (CLI). Tato funkce umožňuje, aby se všechny opravy a další aktivity pro správu a monitorování prováděly Microsoftem. Azure Stack je pole, které lze aktualizovat, prostřednictvím portálu pro zabezpečené správy nebo pomocí příkazů rozhraní příkazového řádku, které používají aktualizace zabezpečení a aktualizace funkcí IaaS a PaaS k udržení komerční parity s Azure, a to v případě potřeby z místních nebo vzdálených sítí.

## <a name="benefits-of-using-mdc"></a>Výhody použití MDC

MDC podporuje konzistentní prostředí s Azure v nevýhodných komunikačních prostředích:

 - Statická, modulární a rychle nasazená datacentra s Azure Cloud Services umožňují používat rozsáhlé analytické aplikace v modulárních provozních centrech.
 - Modulární hraniční nabídky Azure zachovávají konzistenci mezi cloudem a hraničním přístupem k podpoře IaaS primitivních elementů, jako jsou virtuální počítače, úložiště a virtuální sítě.
 - Podpora Azure Active Directory a RBAC.
 - Společná rozhraní pro správu.
 - Rozhraní API symetrie a podpora nástrojů pro DevOps od Microsoftu, třetích stran a open source.
 - Správa a monitorování prostřednictvím Azure Log Analytics a Azure Security Center.
 - Umožněte použití hybridního cloudu a využijte tak flexibilitu cloud computingu pro vaše místní prostředí a Edge.<br>Můžete:
     - Používejte kód a spouštějte nativní cloudové aplikace konzistentně napříč Azure a místními prostředími.
     - Spouštějte tradiční virtualizované úlohy s volitelnými připojeními ke službám Azure.
     - Přeneste data do cloudu nebo je udržujte v datacentru svrchovan, abyste zachovali dodržování předpisů.
     - Spouštějte hardwarově urychlené strojové učení, kontejnerové nebo virtualizované úlohy na inteligentních hraničních zařízeních.

## <a name="next-steps"></a>Další kroky

[Plánování kapacity centra Azure Stack](../operator/azure-stack-capacity-planning-overview.md)
