---
title: Změnit konkrétní nastavení v konfiguraci přepínače centra Azure Stack
description: Přečtěte si, co můžete přizpůsobit v konfiguraci přepínače centra Azure Stack. Až výrobce OEM (Original Equipment Manufacturer) vytvoří konfiguraci, neměňte ji bez souhlasu od týmu výrobce OEM ani Microsoft Azure Stack technickým týmem centra.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 11/11/2019
ms.author: inhenkel
ms.reviewer: wamota
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 5071af80c5063db8d4fc127f2f64311b21785f12
ms.sourcegitcommit: 97806b43314d306e0ddb15847c86be2c92ae001e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2020
ms.locfileid: "77510074"
---
#  <a name="modify-specific-settings-on-your-azure-stack-hub-switch-configuration"></a>Změnit konkrétní nastavení v konfiguraci přepínače centra Azure Stack

Pro konfiguraci přepínače centra Azure Stack můžete upravit několik nastavení prostředí. Můžete určit, která nastavení můžete změnit v šabloně vytvořené výrobcem OEM (Original Equipment Manufacturer). Tento článek vysvětluje každé z těchto přizpůsobitelných nastavení a způsob, jakým mohou změny ovlivnit vaše centrum Azure Stack. Mezi tato nastavení patří aktualizace hesla, server syslog, monitorování protokolu SNMP, ověřování a seznam řízení přístupu. 

Během nasazování řešení Azure Stack hub vytvoří výrobce OEM (Original Equipment Manufacturer) a použije konfiguraci přepínače pro tory i BMC. Výrobce OEM používá nástroj pro automatizaci Azure Stackového centra k ověření, zda jsou požadované konfigurace na těchto zařízeních správně nastaveny. Konfigurace je založená na informacích v [listu nasazení](azure-stack-deployment-worksheet.md)centra Azure Stack. Poté, co výrobce OEM vytvoří konfiguraci **, neměňte konfiguraci** bez souhlasu buď od výrobce OEM, nebo z technického týmu centra Microsoft Azure Stack. Změna konfigurace síťového zařízení může významně ovlivnit provoz nebo řešení potíží se sítí ve vaší instanci centra Azure Stack.

Existují však některé hodnoty, které lze přidat, odebrat nebo změnit v konfiguraci síťových přepínačů.

>[!Warning]  
> **Neměňte konfiguraci** bez souhlasu od týmu výrobce OEM nebo centra Microsoft Azure Stackho technického oddělení. Změna konfigurace síťového zařízení může významně ovlivnit provoz nebo řešení potíží se sítí ve vaší instanci centra Azure Stack.
>
> Pokud chcete získat další informace o těchto funkcích v síťovém zařízení, jak tyto změny provést, obraťte se na dodavatele hardwaru OEM nebo na podporu Microsoftu. Výrobce OEM má konfigurační soubor vytvořený pomocí nástroje pro automatizaci na základě vašeho listu Azure Stackho centra nasazení. 

## <a name="password-update"></a>Aktualizace hesla

Operátor může kdykoli aktualizovat heslo pro každého uživatele v síťových přepínačích. Není nutné měnit žádné informace v systému Azure Stack hub ani postupovat podle pokynů pro [otočení tajných kódů v centru Azure Stack](azure-stack-rotate-secrets.md).

## <a name="syslog-server"></a>Server syslogu

Operátory mohou přesměrovat protokoly přepínačů na server syslog na svém datacentru. Pomocí této konfigurace můžete zajistit, aby se protokoly z určitého bodu v čase mohly použít k řešení potíží. Ve výchozím nastavení jsou protokoly uloženy v přepínačích. kapacita pro ukládání protokolů je omezená. Informace o tom, jak nakonfigurovat oprávnění pro přístup ke správě přepínačů, najdete v části [aktualizace seznamu řízení přístupu](#access-control-list-updates) .

## <a name="snmp-monitoring"></a>Monitorování SNMP

Operátor může nakonfigurovat protokol SNMP (Simple Network Management Protocol) v2 nebo v3 pro monitorování síťových zařízení a odesílání depeší do aplikace monitorování sítě v datacentru. Z bezpečnostních důvodů použijte protokol SNMPv3, protože je bezpečnější než v2. Pokud potřebujete MIB a konfiguraci, obraťte se na svého poskytovatele hardwaru OEM. Informace o tom, jak nakonfigurovat oprávnění pro přístup ke správě přepínačů, najdete v části [aktualizace seznamu řízení přístupu](#access-control-list-updates) .

## <a name="authentication"></a>Ověřování

Operátor může nakonfigurovat buď protokol RADIUS, nebo TACACS pro správu ověřování na síťových zařízeních. Podporované metody a konfigurace si můžete prostudovat u svého poskytovatele hardwaru OEM.  Informace o tom, jak nakonfigurovat oprávnění pro přístup ke správě přepínačů, najdete v části [aktualizace seznamu řízení přístupu](#access-control-list-updates) .

## <a name="access-control-list-updates"></a>Aktualizace seznamu řízení přístupu

> [!NOTE]
> Počínaje 1910 bude mít sešit nasazení nové pole pro **povolené sítě** , které nahradí ruční kroky nutné k povolení přístupu k rozhraním pro správu síťových zařízení a k hostiteli životního cyklu hardwaru (hLH) z rozsahu důvěryhodné sítě Datacenter. Další informace o této nové funkci najdete v [Plánování integrace sítě pro centrum Azure Stack](azure-stack-network.md#permitted-networks).

Operátor může změnit některý seznam řízení přístupu (ACL) s tím, aby povoloval přístup k rozhraním pro správu síťových zařízení a k hostiteli životního cyklu hardwaru (HLH) z rozsahu důvěryhodné sítě Datacenter. Pomocí seznamu řízení přístupu může operátor dovolit svým virtuálním počítačům s JumpBox pro správu v rámci určitého rozsahu sítě získat přístup k rozhraní pro správu přepínače, HLH operačním systému a HLH BMC.

## <a name="next-steps"></a>Další kroky

[Integrace centrálního centra Azure Stack – DNS](azure-stack-integrate-dns.md)
