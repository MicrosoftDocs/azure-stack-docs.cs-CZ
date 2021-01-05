---
title: Přeprava zákazníků z nákupu do centra Azure Stack po nasazení | Microsoft Docs
description: Přečtěte si, co očekávat úspěšné nasazení modulárního datacentra Azure (MDC) v lokalitě, od plánování po nasazení.
services: azure-stack
documentationcenter: ''
author: ashika789
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/04/2020
ms.author: patricka
ms.reviewer: asganesh
ms.lastreviewed: 11/04/2020
ms.openlocfilehash: e69881e5f898b0c1f397a4a6d407da5995dd3049
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97872343"
---
# <a name="modular-datacenter-integration-overview"></a>Přehled integrace modulárního datacentra

Tento článek popisuje kompletní proces pro integraci modulárních Datacenter (MDC) v Azure, od nákupu po nasazení. Integrací je projekt pro spolupráci mezi zákazníkem a Microsoftem. Následující části jsou pokryty různými fázemi časové osy projektu a konkrétními kroky pro členy projektu.

## <a name="introduction"></a>Úvod

Následující tabulka uvádí, co je možné očekávat během různých fází nasazení.

| Strany |Proces pořadí |Před nasazením |Integrace, ověřování, přenos |Nasazení na pracovišti |Po nasazení |
|---|---------------|---------------|-----------------------------------|--------------------|----------------|
|Partnerský vztah Microsoftu  | Signál pro doručení do umístění v USA.    |Poskytněte potřebné nástroje a dokumentaci pro shromažďování požadavků Datacenter. |– Ověřte artefakty konfigurace a zkontrolujte výsledky ověření.<br>– Zajistěte doručení hardwaru.    |– Rack a Stack.<br>– Integrace sítě.<br>-Azure Stack nasazení centra.<br>-Ruku od zákazníka.    |Registrace a syndikace webu centra Azure Stack.|
|Customer (Zákazník)   |Nákup signálu   |– Vyplní podrobnosti sítě v listu nasazení.<br>– Shromáždění certifikátů.<br>– Získejte účty Azure Active Directory (Azure AD).<br>-Spustit jakýkoli poskytnutý Nástroj pro ověření.   |Ujistěte se, že je lokalita připravena se všemi požadavky na síť, napájení a chlazení.   |– Připravte se na artefakty konfigurace nasazení.<br>– Ujistěte se, že je k dispozici pracovník sítě zákazníka.   |     |


## <a name="order-process"></a>Proces pořadí

Vaše organizace bude spolupracovat s Microsoftem a umístit objednávku na přidělený počet systémů. Po umístění objednávky Microsoft doručí MDC do svého umístění v USA. Microsoft zajistí splnění všech požadavků na zabezpečený dodavatelský řetězec.

## <a name="hardware-delivery"></a>Hardwarové doručování

Microsoft bude s vámi spolupracovat, abyste zajistili, že veškerý požadovaný hardware dorazí do umístění USA v rámci přiděleného času.

Je *důležité* , aby všechna data požadovaných součástí byla uzamčena a k dispozici před tím, *než se dorazí pracovník pro nasazení společnosti Microsoft k nasazení řešení.*

- V sešitu nasazení jsou vyplněna všechna data.
- Všechny certifikáty musí být ověřené a připravené.
- Musí být rozhodnuto o názvu oblasti.
- Všechny parametry integrace sítě se dokončují.

>[!Tip]
>Pokud se některá z těchto informací změnila, ujistěte se, že pracujete s vaší interní organizací, abyste se ujistili, že jsou informace aktualizované před příchodem do nástroje pro analýzu nasazení v lokalitě. Aktualizace vašich informací zabrání jakémukoli zpoždění v procesu nasazení.

## <a name="predeployment"></a>Před nasazením

Rozhodnete, jak integrovat Azure Stack hub do vašeho datového centra. Společnost Microsoft publikovala [list pro nasazení](../operator/azure-stack-deployment-worksheet.md) , který vás provede shromažďováním všech potřebných informací potřebných k úspěšné integraci do vašeho datacentra. V době nasazování je navíc potřeba určit určitou sadu certifikátů. Abychom vám pomohli získat tyto certifikáty, Microsoft vám nabídne [kontrolu připravenosti centra Azure Stack](../operator/azure-stack-validation-report.md). Tento nástroj vám pomůže vytvořit žádosti o podepsání certifikátu (zástupci) k poskytování vaší interní certifikační autoritě.

>[!Important]
>Všechny požadavky jsou ověřeny, aby se předešlo zpožděním při nasazení. Ověřování požadavků může trvat čas a vyžadovat koordinaci a shromažďování dat od různých oddělení v rámci vaší organizace.

Vyberte následující položky:

- **Model připojení centra a zprostředkovatel identity Azure Stack.** Můžete zvolit nasazení centra Azure Stack buď [připojeného k Internetu (a do Azure)](../operator/azure-stack-connected-deployment.md) , nebo [odpojena](../operator/azure-stack-disconnected-deployment.md). Pokud chcete využít výhod centra Azure Stack, včetně hybridních scénářů, chcete nasadit připojení k Azure. Výběr Active Directory Federation Services (AD FS) (AD FS) nebo služby Azure AD je jednorázové rozhodnutí, které je nutné provést v době nasazení. *Zprostředkovatele identity nemůžete později změnit, aniž byste museli znovu nasazovat celý systém.*
- **Integrace sítě.** [Integrace sítě](../operator/azure-stack-network.md) je zásadní pro nasazení, provoz a správu systémů Azure Stack hub. Je potřeba vzít v úvahu několik důležitých informací, které jsou potřeba k tomu, aby řešení centra Azure Stack bylo odolné a má vysokou dostupnou fyzickou infrastrukturu pro podporu jeho operací.
- **Integrace brány firewall.** Pro lepší zabezpečení centra Azure Stack doporučujeme [použít bránu firewall](../operator/azure-stack-firewall.md) . Brány firewall můžou zabránit útokům DDoS, detekci vniknutí a kontrole obsahu. Upozorňujeme, že brány firewall se můžou stát kritickým bodem pro služby Azure Storage.
- **Požadavky na certifikát.** Je důležité, aby byly k dispozici všechny [požadované certifikáty](../operator/azure-stack-pki-certs.md) předtím, než dorazí pracovník služby do vašeho datacentra pro nasazení.

Po shromáždění všech požadovaných informací prostřednictvím listu nasazení Microsoft zajistí, že ověříte, že všechny nástroje pro ověřování byly spuštěny, a bude pomáhat s případnými dalšími otázkami, které máte.

## <a name="onsite-deployment"></a>Nasazení na pracovišti

K nasazení centra Azure Stack bude k dispozici inženýr pro nasazení od Microsoftu, který zahájí nasazení. K dispozici je také síťový inženýr od vaší organizace, který bude dostupný během nasazení v rámci lokality.

Následující kontroly byste měli očekávat od inženýra při nasazení v rámci tohoto prostředí:

- Unbox a inventář hardwaru.
- Připojte napájení k řešení a zapněte ho.
- Ověří stav fyzického hardwaru.
- Zkontrolujte všechna připojení kabelů a ohraničení, abyste zajistili správné sestavování řešení a splňovali vaše požadavky.
- Nakonfigurujte hostitele řešení pro životní cyklus hardwaru (HLH).
- Integrujte síť datacenter.
- Zkontrolujte, jestli jsou všechna nastavení fyzického hardwaru správná.
- Ujistěte se, že řešení firmware pro všechny komponenty má nejnovější schválenou verzi.
- Spusťte nasazení.

## <a name="post-deployment"></a>Po nasazení

Před předáním řešení zákazníkovi je nutné provést několik kroků v rámci nástroje Microsoft Deployment inženýr. V této fázi je ověřování důležité, aby byl systém nasazený a správně fungující.

Akce, které by měly být provedeny nástrojem Microsoft Deployment inženýr:

- Povolit přidávání zprostředkovatelů prostředků s hodnotou
- Spusťte rutinu [test-azurestack](../operator/azure-stack-diagnostic-test.md).
- [Zaregistrujte](../operator/azure-stack-registration-role.md) se v Azure.
- Zajistěte [syndikaci Azure Stack centra Marketplace](../operator/azure-stack-marketplace.md).
- Zálohujte konfiguraci přepínače a konfigurační soubory HLH.
- Připravte si Shrnutí zákazníka pro nasazení.
- [Zkontrolujte aktualizace](../operator/azure-stack-updates.md) a ujistěte se, že je software řešení aktualizovaný na nejnovější verzi.

## <a name="next-steps"></a>Další kroky

[Přehled nasazení modulárního datového centra](deployment-overview.md)

