---
title: Přeprava zákazníků z nákupu do centra Azure Stack po nasazení | Microsoft Docs
description: Přečtěte si, co očekávat úspěšné nasazení modulárního datového centra (MDC) v lokalitě, od plánování po nasazení.
services: azure-stack
documentationcenter: ''
author: asganesh
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2020
ms.author: justinha
ms.reviewer: asganesh
ms.lastreviewed: 10/27/2020
ms.openlocfilehash: f170ab6025effe394c891aa4fb3ad7111bac7133
ms.sourcegitcommit: 716ca50bd198fd51a4eec5b40d5247f6f8c16530
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/28/2020
ms.locfileid: "92898615"
---
# <a name="mdc-integration-overview"></a>Přehled integrace MDC

Tento článek popisuje kompletní proces integrace MDC z nákupu až po nasazení. Integrací je projekt pro spolupráci mezi zákazníkem a Microsoftem. Následující oddíly obsahují různé fáze pro časovou osu projektu a konkrétní kroky pro členy projektu.

## <a name="introduction"></a>Úvod

Následující tabulka znázorňuje, co je možné očekávat v různých fázích nasazení.

|   |Proces pořadí  |Před nasazením |Integrace, ověřování, přenos |Nasazení na pracovišti  |Po nasazení |
|---|---------------|---------------|-----------------------------------|--------------------|----------------|
|Partnerský vztah Microsoftu  |-Signál k doručování do umístění USA    |Poskytování požadovaných nástrojů a dokumentace pro shromažďování požadavků Datacenter  |– Ověření artefaktů konfigurace a kontrola výsledků ověření<br>– Zajistěte doručení hardwaru  |– Rack a zásobník<br>– Integrace sítě<br>– Azure Stack nasazení centra<br>-Předat zákazníkovi    |Registrace a syndikace webu Marketplace|
|Zákazník   |Označuje nákup   |– Vyplní podrobnosti sítě v listu nasazení.<br>– Shromáždí certifikáty<br>– Získá účty Azure AD.<br>– Spustí jakýkoli poskytnutý Nástroj pro ověření.    |Ujistěte se, že je lokalita připravená na síť, výkon, požadavky na chlazení.    |– Připravte se na artefakty konfigurace nasazení.<br>– K dispozici je inženýr sítě zákazníka   |     |


## <a name="order-process"></a>Proces pořadí

Vaše organizace bude spolupracovat s Microsoftem a umístit objednávku na přidělený počet systémů. Po umístění objednávky Microsoft doručí MDC k vašemu umístění v USA. Microsoft zajistí splnění všech požadavků na zabezpečený dodavatelský řetězec. 


## <a name="pre-deployment"></a>Před nasazením

Rozhodnete, jak integrovat Azure Stack hub do vašeho datového centra. Společnost Microsoft publikovala [list pro nasazení](../operator/azure-stack-deployment-worksheet.md) , který vás provede shromažďováním všech potřebných informací potřebných k úspěšné integraci do vašeho datacentra. V době nasazování je navíc potřeba určit určitou sadu certifikátů. Abychom vám pomohli získat tyto certifikáty, Microsoft vám poskytne nástroj, který se nazývá [Kontrola připravenosti centra Azure Stack](../operator/azure-stack-validation-report.md). Tento nástroj vám pomůže vytvořit žádosti o podepsání certifikátu (zástupci) k poskytování vaší interní certifikační autoritě. 

>[!Important]
>Všechny požadavky jsou ověřeny, aby se předešlo zpožděním při nasazení. Ověřování požadavků může trvat čas a vyžadovat koordinaci a shromažďování dat od různých oddělení v rámci vaší organizace.

Vyberte následující položky:

- **Model připojení centra a zprostředkovatel identity Azure Stack.** Můžete zvolit nasazení centra Azure Stack buď [připojeného k Internetu (a do Azure)](../operator/azure-stack-connected-deployment.md) , nebo [odpojena](../operator/azure-stack-disconnected-deployment.md). Pokud chcete využít výhod centra Azure Stack, včetně hybridních scénářů, měli byste nasadit připojení k Azure. Výběr Active Directory Federation Services (AD FS) (AD FS) nebo Azure Active Directory (Azure AD) je jednorázové rozhodnutí, které je nutné provést v době nasazení. **Zprostředkovatele identity nemůžete později změnit, aniž byste museli znovu nasazovat celý systém.**
- **Integrace sítě.** [Integrace sítě](../operator/azure-stack-network.md) je zásadní pro nasazení, provoz a správu systémů Azure Stack hub. Je potřeba vzít v úvahu několik důležitých informací, které jsou potřeba k tomu, aby řešení centra Azure Stack bylo odolné a má vysokou dostupnou fyzickou infrastrukturu pro podporu jeho operací.
- **Integrace brány firewall.** Doporučuje se [použít bránu firewall](../operator/azure-stack-firewall.md) , která vám usnadní zabezpečení centra Azure Stack. Brány firewall můžou zabránit útokům DDOS, detekci vniknutí a kontrole obsahu. Je však potřeba poznamenat, že se může stát kritickým bodem pro služby Azure Storage.
- **Požadavky na certifikát.** Je důležité, aby byly k dispozici všechny [požadované certifikáty](../operator/azure-stack-pki-certs.md) předtím, než dorazí pracovník služby do vašeho datacentra pro nasazení.

Jakmile se všechny informace o požadovaných součástech shromažďují prostřednictvím listu nasazení, Microsoft zajistí, že se spustí všechny nástroje pro ověřování a že budou pomáhat s případnými dalšími otázkami, které máte k dispozici. 

## <a name="site-preparation"></a>Příprava lokality

Další informace o požadavcích pro přípravu lokality najdete v Úvodní příručka.

## <a name="hardware-delivery"></a>Hardwarové doručování

Microsoft bude s vámi spolupracovat, abyste zajistili, že veškerý požadovaný hardware dorazí do umístění USA v rámci přiděleného času.  

Je **důležité** , aby všechna data požadovaných součástí byla uzamčena a k dispozici před tím, *než se dorazí pracovník pro nasazení společnosti Microsoft k nasazení řešení.*

- V sešitu nasazení jsou vyplněna všechna data. 
- Všechny certifikáty musí být ověřené a připravené.
- Musí být rozhodnuto o názvu oblasti.
- Všechny parametry integrace sítě se dokončují.

>[!Tip]
>Pokud se některá z těchto informací změnila, ujistěte se, že pracujete s vaší interní organizací, abyste se ujistili, že jsou informace aktualizované před příchodem do nástroje pro analýzu nasazení v lokalitě. Tím zabráníte případným zpožděním v procesu nasazení.

## <a name="onsite-deployment"></a>Nasazení na pracovišti

K nasazení centra Azure Stack bude k dispozici inženýr pro nasazení od Microsoftu, který zahájí nasazení. Vyžadujeme, aby byl během období nasazení v rámci lokality dostupný i síťový inženýr z vaší organizace.

Následující kontroly byste měli očekávat od inženýra při nasazení v rámci tohoto prostředí:

- Rozbalení a inventář hardwaru
- Připojení k napájení a zapnutí řešení
- Ověřuje se stav fyzického hardwaru.
- Zkontrolujte všechna připojení kabelů a ohraničení, abyste zajistili správné sestavování řešení a splňovali vaše požadavky.
- Konfigurace HLH řešení (hostitel životního cyklu hardwaru)
- Integrace sítě Datacenter
- Zaškrtněte, pokud chcete zkontrolovat, jestli jsou všechna nastavení fyzického hardwaru správná.
- Ujistěte se, že řešení firmware pro všechny komponenty má nejnovější schválenou verzi.
- Spustit nasazení

## <a name="post-deployment"></a>Po nasazení

Před předáním řešení zákazníkovi je nutné provést několik kroků v rámci nástroje Microsoft Deployment inženýr. V této fázi je ověřování důležité, aby byl systém nasazený a správně fungující.

Akce, které by měly být provedeny nástrojem Microsoft Deployment inženýr:

- Povolit poskytovatelům hodnot přidat prostředky (RPs)
- Spuštění rutiny [test-azurestack](../operator/azure-stack-diagnostic-test.md)
- [Registrace](../operator/azure-stack-registration-role.md) v Azure
- [Syndikace Marketplace](../operator/azure-stack-marketplace.md)
- Konfigurace záložního přepínače a konfigurační soubory HLH
- Příprava Shrnutí zákazníka pro nasazení
- [Zkontrolujte aktualizace](../operator/azure-stack-updates.md) a ujistěte se, že je software řešení aktualizovaný na nejnovější verzi.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [postupu instalace a konfigurace modulárního datového centra](deployment-overview.md).

