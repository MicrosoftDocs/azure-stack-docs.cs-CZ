---
title: Názorný postup pro integraci centra Azure Stack hub
description: Přečtěte si, co očekávat úspěšné nasazení centra Azure Stack v rámci lokality ve vašem datovém centru, od plánování po nasazení.
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: inhenkel
ms.reviewer: asganesh
ms.lastreviewed: 11/07/2019
ms.openlocfilehash: d55176266fc169b344e7bb43389a46657174b88e
ms.sourcegitcommit: 1fa0140481a483e5c27f602386fe1fae77ad29f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78368822"
---
# <a name="azure-stack-hub-datacenter-integration-walkthrough"></a>Názorný postup pro integraci centra Azure Stack hub

Tento článek popisuje kompletní proces integrace centra Azure Stack pro integraci z nákupu až po nasazení. Integrací je projekt pro spolupráci mezi zákazníkem, poskytovatelem řešení a Microsoftem. Kliknutím na následující karty zobrazíte konkrétní kroky pro každého člena projektu a v dalších částech najdete souhrn různých fází pro časovou osu projektu. 

# <a name="customer"></a>[Zákazník](#tab/customer)

1. Popište případy použití a požadavky
1. Určení modelu fakturace
1. Kontrola a schválení smluv
1. Dokončení [listu nasazení](azure-stack-deployment-worksheet.md)
1. Ujistěte se, že předpoklady nasazení jsou splněné
1. Příprava datacentra 
1. Zadání informací o předplatném během nasazení
1. Vyřešit všechny dotazy týkající se poskytnutých dat

# <a name="partner"></a>[Instituc](#tab/partner)

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
1. Nasazení centra Azure Stack
1. Předání zákazníkovi

# <a name="microsoft"></a>[Microsoft](#tab/micro)

1. Zapojení partnera pro předprodejní podporu
2. Příprava licencování softwaru a smluv
3. Poskytněte nástroje pro shromažďování požadavků na integraci Datacenter.
4. Poskytnutí měsíčních sestavení a aktualizací řetězců nástrojů
5. Technici podpory Microsoftu pomáhají s případnými problémy při nasazení

---

## <a name="planning"></a>Plánování
Microsoft nebo partner řešení Azure Stack hub vám pomůže vyhodnotit vaše cíle. Tyto otázky vám pomůžou při rozhodování:

-   Je Azure Stack centrum správné řešení pro vaši organizaci?
-   Jaký typ fakturačního a licenčního modelu bude pro vaši organizaci fungovat?
-   Jaké řešení velikosti budete potřebovat?
-   Jaké jsou požadavky na napájení a chlazení?

[Capacity Planner centra Azure Stack](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) slouží k prozkoumání a analýze nejlepší hardwarové kapacity a konfigurace podle vašich potřeb. 

## <a name="ordering"></a>Třídění
Vaše organizace se zavazuje k zakoupení centra Azure Stack, zaznamenání smluv a nákupních objednávek a poskytuje požadavky na integraci do poskytovatele řešení.

## <a name="pre-deployment"></a>Před nasazením
Rozhodnete, jak integrovat Azure Stack hub do vašeho datového centra. Společnost Microsoft spolupracuje s poskytovateli řešení k publikování [sešitu nasazení](azure-stack-deployment-worksheet.md) , který vám může usnadnit shromažďování potřebných informací.
Článek [Obecné informace o integraci Datacenter](azure-stack-datacenter-integration.md) poskytuje informace, které vám pomůžou dokončit šablonu, která se označuje jako sešit nasazení.

> [!IMPORTANT]
> Před řazením řešení, které brání zpoždění nasazení, se prošetří všechny požadavky. Ověřování požadavků může trvat čas a vyžadovat koordinaci a shromažďování dat od různých oddělení v rámci vaší organizace. 

Vyberte následující položky:

- **Model připojení centra a zprostředkovatel identity Azure Stack**. Můžete zvolit nasazení centra Azure Stack buď [připojeného k Internetu (a do Azure), nebo odpojena](azure-stack-connection-models.md). Pokud chcete využít výhod centra Azure Stack, včetně hybridních scénářů, měli byste nasadit připojení k Azure. Výběr Active Directory Federation Services (AD FS) (AD FS) nebo Azure Active Directory (Azure AD) je jednorázové rozhodnutí, které je nutné provést v době nasazení. **Zprostředkovatele identity nemůžete později změnit, aniž byste museli znovu nasazovat celý systém**.

- **Licenční model**. Možnosti licenčního modelu, ze kterých si můžete vybrat, závisí na typu nasazení, které budete mít. Vaše volba poskytovatele identity nemá žádný vliv na virtuální počítače klientů ani na používané systémy identit a účty.
    - Zákazníci, kteří jsou v [odpojeném nasazení](azure-stack-disconnected-deployment.md) , mají jenom jednu možnost: fakturace na základě kapacity.

    - Zákazníci, kteří se nacházejí v [připojeném nasazení](azure-stack-connected-deployment.md) , si mohou vybrat mezi fakturací a průběžnými platbami na základě kapacity. Účtování na základě kapacity vyžaduje k registraci předplatné Azure smlouva Enterprise (EA). To je nezbytné pro registraci, která umožňuje dostupnost položek v Azure Marketplace prostřednictvím předplatného Azure.

- **Integrace sítě**. [Integrace sítě](azure-stack-network.md) je zásadní pro nasazení, provoz a správu systémů Azure Stack hub. Je potřeba vzít v úvahu několik důležitých informací, které jsou potřeba k tomu, aby řešení centra Azure Stack bylo odolné a má vysokou dostupnou fyzickou infrastrukturu pro podporu jeho operací.

- **Integrace brány firewall**. Doporučuje se [použít bránu firewall](azure-stack-firewall.md) , která vám usnadní zabezpečení centra Azure Stack. Brány firewall můžou zabránit útokům DDOS, detekci vniknutí a kontrole obsahu. Je však potřeba poznamenat, že se může stát kritickým bodem pro služby Azure Storage.

- **Požadavky na certifikát**. Je důležité, aby byly k dispozici všechny [požadované certifikáty](azure-stack-pki-certs.md) *předtím, než* dorazí pracovník služby do vašeho datacentra pro nasazení.

Jakmile se všechny informace o požadovaných součástech shromažďují prostřednictvím listu nasazení, poskytovatel řešení aktivuje proces výroby na základě shromažďovaných dat, aby bylo zajištěno úspěšné začlenění Azure Stack do vašeho datového centra.

## <a name="hardware-delivery"></a>Hardwarové doručování 
Poskytovatel řešení bude spolupracovat s vámi na plánování, kdy řešení přijde do vašeho zařízení. Po přijetí a uvedení na místo toho budete muset naplánovat čas u poskytovatele řešení, aby měl inženýr k dispozici k provedení nasazení centra Azure Stack.

Je **velmi důležité** , aby všechna data požadovaných součástí byla uzamčena a k dispozici *předtím, než dorazí pracovník do lokality k nasazení řešení*.

-   Všechny certifikáty musí být zakoupené a připravené.

-   Musí být rozhodnuto o názvu oblasti.

-   Všechny parametry integrace sítě se dokončují a shodují s tím, co jste sdíleli s vaším poskytovatelem řešení.

> [!TIP]
> Pokud se některé z těchto informací změnily, nezapomeňte před naplánováním vlastního nasazení sdělit změnu u poskytovatele řešení.

## <a name="onsite-deployment"></a>Nasazení na pracovišti 
Aby bylo možné nasadit centrum Azure Stack, musí být k dispozici inženýr od poskytovatele hardwarových řešení, aby bylo možné nasazení aktivovat. Aby se zajistilo úspěšné nasazení, ujistěte se, že se nezměnily všechny informace, které jsou součástí listu nasazení.

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

- Pokud se nasazení dokončilo pomocí [AD FS](azure-stack-integrate-identity.md), musí být razítko centra Azure Stack integrované s vlastním AD FS zákazníka.

  > [!NOTE]
  > Tento krok je zodpovědný za zákazníka, i když partner může volitelně zvolit poskytování služeb.

- Integrace s existujícím monitorovacím systémem od příslušného partnera.

  -   [Integrace System Center Operations Manager](azure-stack-integrate-monitor.md) podporuje taky možnosti správy loďstva.

  -   [Integrace Nagios](azure-stack-integrate-monitor.md#integrate-with-nagios).

## <a name="schedule"></a>Plán

![Celková časová osa pro nasazení centra Azure Stack v lokalitě](./media/azure-stack-datacenter-integration-walkthrough/image1.png)

## <a name="support"></a>Podpora
Centrum Azure Stack umožňuje integrovanou podporu v rámci Azure, která pokrývá kompletní životní cyklus systému. Aby zákazníci mohli plně podporovat Azure Stack integrovaných systémů centra, potřebují dvě smlouvy o podpoře: jeden s Microsoftem (nebo jeho poskytovatel Cloud Solution Provider) pro podporu služeb Azure a jeden u poskytovatele hardwaru pro podporu systému. Integrované prostředí podpory zajišťuje koordinovanou eskalaci a řešení, aby zákazníci získali konzistentní možnosti podpory bez ohledu na to, kdo je vyvolal. Pro zákazníky, kteří už mají podporu na úrovni Premier, Azure – Standard nebo ProDirect nebo partner s Microsoftem, je k dispozici podpora softwaru Azure Stack hub.

Integrované prostředí podpory využívá mechanismus výměny malých a velkých písmen pro obousměrné přenosy případů podpory a aktualizace případů mezi společností Microsoft a hardwarovým partnerem. Centrum Microsoft Azure Stack se bude řídit [moderními zásadami životního cyklu](https://support.microsoft.com/help/30881).

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [obecných požadavcích integrace Datacenter](azure-stack-datacenter-integration.md).
