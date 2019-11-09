---
title: Návod pro integraci Azure Stack Datacenter | Microsoft Docs
description: Přečtěte si, co očekávat úspěšné nasazení Azure Stack ve vašem datovém centru v rámci lokality, od plánování po nasazení.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2019
ms.author: mabrigg
ms.reviewer: asganesh
ms.lastreviewed: 11/07/2019
ms.openlocfilehash: 6bcdbcb03cdd4151978e9eeee645a0d4ab488fe3
ms.sourcegitcommit: ed44d477b9fd11573d1e0d1ed3a3c0ef4512df53
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73845775"
---
# <a name="azure-stack-datacenter-integration-walkthrough"></a>Návod pro integraci Azure Stack Datacenter

Tento článek popisuje kompletní proces integrace Azure Stack Datacenter z nákupu až po nasazení podpory. Integrací je projekt pro spolupráci mezi zákazníkem, poskytovatelem řešení a Microsoftem. Kliknutím na následující karty zobrazíte konkrétní kroky pro každého člena projektu a v dalších částech najdete souhrn různých fází pro časovou osu projektu. 

# <a name="customertabcustomer"></a>[Zákazníka](#tab/customer)

1. Popište případy použití a požadavky
1. Určení modelu fakturace
1. Kontrola a schválení smluv
1. Dokončení [listu nasazení](azure-stack-deployment-worksheet.md)
1. Ujistěte se, že předpoklady nasazení jsou splněné
1. Příprava datacentra 
1. Zadání informací o předplatném během nasazení
1. Vyřešit všechny dotazy týkající se poskytnutých dat

# <a name="partnertabpartner"></a>[Instituc](#tab/partner)

1. Doporučené možnosti řešení na základě požadavků zákazníků
1. Navrhnout testování konceptu (pro ověření koncepce) 
1. Rozhodnutí o úrovni podpory
1. Příprava smluv u zákazníka
1. Vytvořit nákupní objednávku zákazníka
1. Rozhodnout o plánu doručení
1. Propojení zákazníka s Microsoftem 
1. Školení zákazníků při nasazení 
1. Pomáhat zákazníkovi ověřit shromážděná data
1. Instalace a ověření základního sestavení a sady Microsoft Deployment Toolkit
1. Dodávání hardwaru zákaznickému webu
1. Poskytnout inženýra na pracovišti
1. Stojan a zásobník
1. Nasazení hostitele životního cyklu hardwaru (HLH) 
1. Nasazení Azure Stacku
1. Předání zákazníkovi

# <a name="microsofttabmicro"></a>[Microsoft](#tab/micro)

1. Zapojení partnera pro předprodejní podporu
2. Příprava licencování softwaru a smluv
3. Poskytněte nástroje pro shromažďování požadavků na integraci Datacenter.
4. Poskytnutí měsíčních sestavení a aktualizací řetězců nástrojů
5. Technici podpory Microsoftu pomáhají s případnými problémy při nasazení

---

## <a name="planning"></a>Plánování
Partner řešení Microsoftu nebo Azure Stack pomůže vyhodnotit vaše cíle. Tyto otázky vám pomůžou při rozhodování:

-   Je Azure Stack správné řešení pro vaši organizaci?
-   Jaký typ fakturačního a licenčního modelu bude pro vaši organizaci fungovat?
-   Jaké řešení velikosti budete potřebovat?
-   Jaké jsou požadavky na napájení a chlazení?

Pomocí [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) můžete prozkoumat a analyzovat nejlepší hardwarovou kapacitu a konfiguraci podle svých potřeb. 

## <a name="ordering"></a>Třídění
Vaše organizace se zavazuje k nákupu Azure Stack, označení smluv a nákupních objednávek a poskytuje údaje o požadavcích na integraci do poskytovatele řešení.

## <a name="pre-deployment"></a>Před nasazením
Rozhodnete, jak integrovat Azure Stack do vašeho datového centra. Společnost Microsoft spolupracuje s poskytovateli řešení k publikování [sešitu nasazení](azure-stack-deployment-worksheet.md) , který vám může usnadnit shromažďování potřebných informací.
Článek [Obecné informace o integraci Datacenter](azure-stack-datacenter-integration.md) poskytuje informace, které vám pomůžou dokončit šablonu, která se označuje jako sešit nasazení.

> [!IMPORTANT]
> Před řazením řešení, které brání zpoždění nasazení, se prošetří všechny požadavky. Ověřování požadavků může trvat čas a vyžadovat koordinaci a shromažďování dat od různých oddělení v rámci vaší organizace. 

Vyberte následující položky:

- **Model připojení Azure Stack a zprostředkovatel identity**. Můžete zvolit nasazení Azure Stack buď [připojeného k Internetu (a k Azure), nebo odpojeni](azure-stack-connection-models.md). Pokud chcete využít výhod Azure Stack, včetně hybridních scénářů, měli byste je nasadit do Azure. Výběr Active Directory Federation Services (AD FS) (AD FS) nebo Azure Active Directory (Azure AD) je jednorázové rozhodnutí, které je nutné provést v době nasazení. **Zprostředkovatele identity nemůžete později změnit, aniž byste museli znovu nasazovat celý systém**.

- **Licenční model**. Možnosti licenčního modelu, ze kterých si můžete vybrat, závisí na typu nasazení, které budete mít. Vaše volba poskytovatele identity nemá žádný vliv na virtuální počítače klientů ani na používané systémy identit a účty.
    - Zákazníci, kteří jsou v [odpojeném nasazení](azure-stack-disconnected-deployment.md) , mají jenom jednu možnost: fakturace na základě kapacity.

    - Zákazníci, kteří se nacházejí v [připojeném nasazení](azure-stack-connected-deployment.md) , si mohou vybrat mezi fakturací a průběžnými platbami na základě kapacity. Účtování na základě kapacity vyžaduje k registraci předplatné Azure smlouva Enterprise (EA). To je nezbytné pro registraci, která umožňuje dostupnost položek v Azure Marketplace prostřednictvím předplatného Azure.

- **Integrace sítě**. [Integrace sítě](azure-stack-network.md) je zásadní pro nasazení, provoz a správu systémů Azure Stack. Je potřeba vzít v úvahu několik důležitých informací, které je potřeba zajistit, aby bylo řešení Azure Stack odolné a vysoce dostupné fyzické infrastruktury pro podporu jeho operací.

- **Integrace brány firewall**. Pro lepší zabezpečení Azure Stack doporučujeme [použít bránu firewall](azure-stack-firewall.md) . Brány firewall můžou zabránit útokům DDOS, detekci vniknutí a kontrole obsahu. Je však potřeba poznamenat, že se může stát kritickým bodem pro služby Azure Storage.

- **Požadavky na certifikát**. Je důležité, aby byly k dispozici všechny [požadované certifikáty](azure-stack-pki-certs.md) *předtím, než* dorazí pracovník služby do vašeho datacentra pro nasazení.

Po shromáždění všech požadovaných informací prostřednictvím sešitu nasazení poskytovatel řešení zahájí proces výroby na základě shromažďovaných dat, aby zajistil úspěšnou integraci Azure Stack do vašeho datacentra.

## <a name="hardware-delivery"></a>Hardwarové doručování 
Poskytovatel řešení bude spolupracovat s vámi na plánování, kdy řešení přijde do vašeho zařízení. Po přijetí a uvedení na místo toho budete muset naplánovat čas u poskytovatele řešení, aby měl inženýr k dispozici k provedení nasazení Azure Stack.

Je **velmi důležité** , aby všechna data požadovaných součástí byla uzamčena a k dispozici *předtím, než dorazí pracovník do lokality k nasazení řešení*.

-   Všechny certifikáty musí být zakoupené a připravené.

-   Musí být rozhodnuto o názvu oblasti.

-   Všechny parametry integrace sítě se dokončují a shodují s tím, co jste sdíleli s vaším poskytovatelem řešení.

> [!TIP]
> Pokud se některé z těchto informací změnily, nezapomeňte před naplánováním vlastního nasazení sdělit změnu u poskytovatele řešení.

## <a name="onsite-deployment"></a>Nasazení na pracovišti 
Aby bylo možné nasadit Azure Stack, musí být k dispozici inženýr v lokalitě od poskytovatele hardwarových řešení, aby bylo možné nasazení aktivovat. Aby se zajistilo úspěšné nasazení, ujistěte se, že se nezměnily všechny informace, které jsou součástí listu nasazení.

Následující kontroly byste měli očekávat od inženýra při nasazení v rámci tohoto prostředí:

- Zkontrolujte všechna připojení kabelů a ohraničení, abyste zajistili správné sestavování řešení a splňovali vaše požadavky.
- Nakonfigurujte HLH řešení (hostitel životního cyklu životnosti hardwaru), pokud je k dispozici.
- Zkontrolujte, jestli jsou správně nastavená všechna řadiče pro správu základní desky, BIOS a síť.
- Ujistěte se, že řešení firmware pro všechny komponenty má nejnovější schválenou verzi.
- Spusťte nasazení.

> [!NOTE]
> Postup nasazení prováděný inženýrem na pracovišti může trvat přibližně jeden pracovní týden.

## <a name="post-deployment"></a>Po nasazení 
Než se řešení dopustí zákazníkovi ve fázi po integraci, musí ho provést několik kroků. V této fázi je ověřování důležité, aby byl systém nasazený a správně fungující. 

Akce, které by měl být prováděna partnerem OEM:

- [Spusťte rutinu test-azurestack](azure-stack-diagnostic-test.md).

- [Registrace v Azure](azure-stack-registration.md).

- [Syndikace Marketplace](azure-stack-download-azure-marketplace-item.md#use-the-marketplace-syndication-tool-to-download-marketplace-items).

- Konfigurace záložního přepínače a konfigurační soubory HLH

- Odeberte DVM.

- Připravte si Shrnutí zákazníka pro nasazení.

- [Zkontrolujte aktualizace a ujistěte se, že je software řešení aktualizovaný na nejnovější verzi](./azure-stack-updates.md).

V závislosti na typu instalace se vyžaduje několik kroků, které jsou povinné nebo volitelné.

- Pokud bylo nasazení dokončeno pomocí [AD FS](azure-stack-integrate-identity.md), bude nutné Azure Stack razítko integrovat s vlastním AD FS zákazníka.

  > [!NOTE]
  > Tento krok je zodpovědný za zákazníka, i když partner může volitelně zvolit poskytování služeb.

- Integrace s existujícím monitorovacím systémem od příslušného partnera.

  -   [Integrace System Center Operations Manager](azure-stack-integrate-monitor.md) podporuje taky možnosti správy loďstva.

  -   [Integrace Nagios](azure-stack-integrate-monitor.md#integrate-with-nagios).

## <a name="schedule"></a>Plán

![Celková časová osa pro Azure Stack nasazení na pracovišti](./media/azure-stack-datacenter-integration-walkthrough/image1.png)

## <a name="support"></a>Podpora
Azure Stack umožňuje integrované prostředí podpory v rámci Azure, které pokrývá kompletní životní cyklus systému. Aby zákazníci mohli plně podporovat Azure Stack integrovaných systémů, potřebují dvě smlouvy o podpoře: jeden s Microsoftem (nebo jeho poskytovatel Cloud Solution Provider) pro podporu služeb Azure a jeden s poskytovatelem hardwaru pro podporu systému. Integrované prostředí podpory zajišťuje koordinovanou eskalaci a řešení, aby zákazníci získali konzistentní možnosti podpory bez ohledu na to, kdo je vyvolal. Pro zákazníky, kteří už mají podporu Premier, Azure – Standard nebo ProDirect nebo partner support s Microsoftem, je k dispozici Azure Stack softwarová podpora.

Integrované prostředí podpory využívá mechanismus výměny malých a velkých písmen pro obousměrné přenosy případů podpory a aktualizace případů mezi společností Microsoft a hardwarovým partnerem. Microsoft Azure Stack se budou řídit [moderními zásadami životního cyklu](https://support.microsoft.com/help/30881).

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [obecných požadavcích integrace Datacenter](azure-stack-datacenter-integration.md).
