---
title: Upravit konkrétní nastavení v konfiguraci přepínače Azure Stack | Microsoft Docs
description: Přečtěte si, co můžete přizpůsobit v konfiguraci přepínače Azure Stack. Až výrobce OEM (Original Equipment Manufacturer) vytvoří konfiguraci, neměňte ji bez souhlasu od výrobce OEM ani Microsoft Azure Stack technický tým.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: Femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: wamota
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 1bd138be243c2803b5a280de2a3a8625e84db998
ms.sourcegitcommit: 102ef41963b5d2d91336c84f2d6af3fdf2ce11c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73955331"
---
#  <a name="modify-specific-settings-on-your-azure-stack-switch-configuration"></a>Úprava konkrétního nastavení v konfiguraci přepínače Azure Stack

Pro konfiguraci přepínače Azure Stack můžete upravit několik nastavení prostředí. Můžete určit, která nastavení můžete změnit v šabloně vytvořené výrobcem OEM (Original Equipment Manufacturer). Tento článek vysvětluje každé z těchto přizpůsobitelných nastavení a způsob, jakým můžou změny ovlivnit vaše Azure Stack. Mezi tato nastavení patří aktualizace hesla, server syslog, monitorování protokolu SNMP, ověřování a seznam řízení přístupu. 

Při nasazení Azure Stack řešení vytvoří výrobce OEM (Original Equipment Manufacturer) a použije konfiguraci přepínače pro tory i BMC. Výrobce OEM používá nástroj Azure Stack Automation k ověření, zda jsou požadované konfigurace na těchto zařízeních správně nastaveny. Konfigurace vychází z informací v [listu nasazení](azure-stack-deployment-worksheet.md)Azure Stack. Poté, co výrobce OEM vytvoří konfiguraci, neměňte konfiguraci bez souhlasu buď od výrobce **OEM, nebo** z technického týmu Microsoft Azure Stack. Změna konfigurace síťového zařízení může významně ovlivnit provoz nebo řešení potíží se sítí ve vaší instanci Azure Stack.

Existují však některé hodnoty, které lze přidat, odebrat nebo změnit v konfiguraci síťových přepínačů.

>[!Warning]  
> **Neměňte konfiguraci** bez souhlasu výrobce OEM ani Microsoft Azure Stack technický tým. Změna konfigurace síťového zařízení může významně ovlivnit provoz nebo řešení potíží se sítí ve vaší instanci Azure Stack.
>
> Pokud chcete získat další informace o těchto funkcích v síťovém zařízení, jak tyto změny provést, obraťte se na dodavatele hardwaru OEM nebo na podporu Microsoftu. Výrobce OEM má konfigurační soubor vytvořený pomocí nástroje pro automatizaci na základě vašeho listu nasazení Azure Stack. 

## <a name="password-update"></a>Aktualizace hesla

Operátor může kdykoli aktualizovat heslo pro každého uživatele v síťových přepínačích. Není nutné měnit žádné informace v Azure Stackm systému ani postupovat podle pokynů pro [otočení tajných kódů v Azure Stack](azure-stack-rotate-secrets.md).

## <a name="syslog-server"></a>Server syslogu

Operátory mohou přesměrovat protokoly přepínačů na server syslog na svém datacentru. Pomocí této konfigurace můžete zajistit, aby se protokoly z určitého bodu v čase mohly použít k řešení potíží. Ve výchozím nastavení jsou protokoly uloženy v přepínačích. kapacita pro ukládání protokolů je omezená. Informace o tom, jak nakonfigurovat oprávnění pro přístup ke správě přepínačů, najdete v části [aktualizace seznamu řízení přístupu](#access-control-list-updates) .

## <a name="snmp-monitoring"></a>Monitorování SNMP

Operátor může nakonfigurovat protokol SNMP (Simple Network Management Protocol) v2 nebo v3 pro monitorování síťových zařízení a odesílání depeší do aplikace monitorování sítě v datacentru. Z bezpečnostních důvodů použijte protokol SNMPv3, protože je bezpečnější než v2. Pokud potřebujete MIB a konfiguraci, obraťte se na svého poskytovatele hardwaru OEM. Informace o tom, jak nakonfigurovat oprávnění pro přístup ke správě přepínačů, najdete v části [aktualizace seznamu řízení přístupu](#access-control-list-updates) .

## <a name="authentication"></a>Ověření

Operátor může nakonfigurovat buď protokol RADIUS, nebo TACACS pro správu ověřování na síťových zařízeních. Podporované metody a konfigurace si můžete prostudovat u svého poskytovatele hardwaru OEM.  Informace o tom, jak nakonfigurovat oprávnění pro přístup ke správě přepínačů, najdete v části [aktualizace seznamu řízení přístupu](#access-control-list-updates) .

## <a name="access-control-list-updates"></a>Aktualizace seznamu řízení přístupu

Operátor může změnit některý seznam řízení přístupu (ACL) s tím, aby povoloval přístup k rozhraním pro správu síťových zařízení a k hostiteli životního cyklu hardwaru (HLH) z rozsahu důvěryhodné sítě Datacenter. Operátor může vybrat, která komponenta bude dosažitelná a kde. V seznamu řízení přístupu může operátor dovolit, aby JumpBox virtuální počítače pro správu v rámci určitého rozsahu sítě pro přístup k rozhraní pro správu přepínače a k rozhraní HLH a HLH BMC.

## <a name="next-steps"></a>Další kroky

[Integrace Azure Stack Datacenter – DNS](azure-stack-integrate-dns.md)