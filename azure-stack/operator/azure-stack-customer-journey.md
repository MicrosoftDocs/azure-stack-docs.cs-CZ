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
ms.date: 06/13/2019
ms.author: mabrigg
ms.reviewer: asganesh
ms.lastreviewed: 12/10/2018
ms.openlocfilehash: 57e92f877ab9516b7b4978b5a919b18dad9b60ea
ms.sourcegitcommit: c2ea4ffb42563c26faaf2993ba7b484bcb6d5cb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71342839"
---
# <a name="azure-stack-datacenter-integration-walkthrough"></a>Návod pro integraci Azure Stack Datacenter

Tento článek popisuje kompletní Azure Stack pro zákazníky z nákupu integrovaného systému až po úspěšné nasazení na pracovišti poskytovatelem řešení. Tyto informace můžete využít k usnadnění cesty a k tomu, abyste si mohli nastavovat očekávání jako Azure Stack zákazník.

Jako Azure Stack zákazníkem byste měli odhadnout následující fáze:

|     |Plánovací fáze|Proces pořadí|Před nasazením|Proces výroby|Hardwarové doručování|Nasazení na pracovišti|
|-----|-----|-----|-----|-----|-----|-----|
|**Microsoft**|Zapojte se s partnerem a poskytněte předprodejní podporu.|Připravte si licencování softwaru a smlouvy podle potřeby.|Poskytněte požadované nástroje pro shromažďování požadavků na integraci Datacenter a dokumentaci pro zákazníky.|Poskytněte nejnovější základní buildy a aktualizace řetězců nástrojů na měsíční tempo.|neuvedeno|Technici podpory Microsoftu pomáhají s případnými problémy při nasazení.|
|**Partner**|Doporučte možnosti řešení na základě požadavků zákazníků.<br><br>V případě potřeby Navrhujte testování konceptu (pro ověření koncepce).<br><br>Vytvořte obchodní vztah.<br><br>Určete úroveň podpory.|Příprava nezbytných smluv na zákazníka<br><br>Vytvořte objednávku nákupu zákazníka.<br><br>Určete časovou osu doručení.<br><br>V případě potřeby propojte zákazníka s Microsoftem.|Poskytněte zákazníkům potřebné školení, aby se zajistilo porozumění všem požadavkům nasazení a možnostem integrace Datacenter.<br><br>Pomůže zákazníkovi ověřit shromážděná data, aby se zajistila úplnost a přesnost.|Použijte poslední ověřené sestavení směrného plánu.<br><br>Použijte požadovanou sadu nástrojů Microsoft Deployment Toolkit.|Dodejte hardware na zákaznickou lokalitu.|Nasazení zpracovávané inženýrem na pracovišti.<br><br>Stojan a zásobník.<br><br>Nasazení hostitele životního cyklu hardwaru (HLH).<br><br>Nasazení Azure Stack.<br><br>Předání zákazníkovi.|
|**Zákazníka**|Popište zamýšlené případy použití a určete požadavky.|Určete, jak má model fakturace používat, kontrolovat a schvalovat smlouvy.|Dokončete [sešit nasazení](azure-stack-deployment-worksheet.md)a ujistěte se, že všechny požadavky nasazení jsou splněné a připravené k nasazení.|neuvedeno|Připravte si datové centrum tím, že zajistíte splnění všech požadovaných požadavků na napájení a chlazení, hraniční připojení a další požadované požadavky na integraci Datacenter.|K dispozici během nasazení za účelem poskytnutí přihlašovacích údajů a podpory předplatného, pokud se na poskytnutých datech nacházejí nějaké otázky.|
| | | | | | | |


## <a name="planning-phase"></a>Plánovací fáze
Plánovací fáze je v případě, že společnost Microsoft nebo partner řešení Azure Stack s vámi pracuje na vyhodnocení a pochopení vašich potřeb, abyste zjistili, jestli je pro vás Azure Stack správné řešení:

Pomůžou vám rozhodnout na následujících otázkách:

-   Je Azure Stack správné řešení pro vaši organizaci?

-   Jaké řešení velikosti budete potřebovat?

-   Jaký typ fakturačního a licenčního modelu bude pro vaši organizaci fungovat?

-   Jaké jsou požadavky na napájení a chlazení?

Aby se zajistilo, že hardwarové řešení bude nejlépe vyhovovat vašim potřebám, [Azure Stack Capacity Planner](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) se bude pomáhat při plánování předběžného nákupu, aby bylo možné určit vhodnou kapacitu a konfiguraci pro vaše Azure Stack hardwarové řešení.

Tabulka není určena k použití jako náhrada za vaše vlastní šetření a analýzu hardwarových řešení, která nejlépe vyhovuje vašim potřebám. Při plánování nasazení Azure Stack byste si měli projít také [Obecné požadavky na integraci Datacenter](azure-stack-datacenter-integration.md) pro Azure Stack integrované systémy.

## <a name="order-process-phase"></a>Fáze procesu pořadí
V této fázi bylo zodpovězeno mnoho vašich otázek s ohledem na proveditelnost. Teď, když jste připraveni potvrdit Azure Stack nákupu a po podepsání všech požadovaných smluv a nákupních objednávek, budete požádáni o poskytnutí údajů o integraci do poskytovatele řešení.

## <a name="pre-deployment-phase"></a>Fáze předběžného nasazení
V průběhu této fáze se musíte rozhodnout, jak chcete integrovat Azure Stack do svého datacentra. Abychom tento proces usnadnili, Microsoft vloží šablonu požadavků, která vám usnadní shromáždění potřebných informací pro plánování integrovaného nasazení systému v rámci vašeho prostředí. Tato šablona požadavků byla vytvořena ve spolupráci s poskytovateli řešení.

Článek [Obecné informace o integraci Datacenter](azure-stack-datacenter-integration.md) poskytuje informace, které vám pomůžou dokončit šablonu, která se označuje jako sešit nasazení.

> [!IMPORTANT]
> Během této fáze je důležité, aby před objednáním řešení byly prověřeny všechny informace o požadovaných součástech a rozhodnuty na nich. Mějte na paměti, že tento krok je časově náročný a vyžaduje koordinaci a shromažďování dat z více oborů v rámci vaší organizace. Nesprávné nebo neúplné informace mohou mít za následek delší nasazení. 

Ve fázi předběžného nasazení se musíte rozhodnout na následujících položkách:

- **Model připojení Azure Stack a zprostředkovatel identity**. Můžete zvolit nasazení Azure Stack buď připojeného [k Internetu (a k Azure), nebo odpojeni](azure-stack-connection-models.md). Pokud chcete využít výhod Azure Stack, včetně hybridních scénářů, měli byste je nasadit do Azure. Výběr Active Directory Federation Services (AD FS) (AD FS) nebo Azure Active Directory (Azure AD) je jednorázové rozhodnutí, které je nutné provést v době nasazení. **Zprostředkovatele identity nemůžete později změnit, aniž byste museli znovu nasazovat celý systém**.

- **Licenční model**. Možnosti licenčního modelu, ze kterých si můžete vybrat, závisí na typu nasazení, které budete mít. Vaše volba poskytovatele identity nemá žádný vliv na virtuální počítače klientů ani na používané systémy identit a účty.
    - Zákazníci, kteří jsou v [odpojeném nasazení](azure-stack-disconnected-deployment.md) , mají jenom jednu možnost: fakturace na základě kapacity.

    - Zákazníci, kteří se nacházejí v [připojeném nasazení](azure-stack-connected-deployment.md) , si mohou vybrat mezi fakturací a průběžnými platbami na základě kapacity. Účtování na základě kapacity vyžaduje k registraci předplatné Azure smlouva Enterprise (EA). To je nezbytné pro registraci, která umožňuje dostupnost položek v Azure Marketplace prostřednictvím předplatného Azure.

- **Integrace sítě**. [Integrace sítě](azure-stack-network.md) je zásadní pro nasazení, provoz a správu systémů Azure Stack. Je potřeba vzít v úvahu několik důležitých informací, které je potřeba zajistit, aby bylo řešení Azure Stack odolné a vysoce dostupné fyzické infrastruktury pro podporu jeho operací.

- **Integrace brány firewall**. Pro lepší zabezpečení Azure Stack doporučujeme [použít bránu firewall](azure-stack-firewall.md) . Brány firewall můžou zabránit útokům DDOS, detekci vniknutí a kontrole obsahu. Je však potřeba poznamenat, že se může stát kritickým bodem pro služby Azure Storage.


- **Požadavky na certifikát**. Je důležité, aby byly k dispozici všechny [požadované certifikáty](azure-stack-pki-certs.md) *předtím, než* dorazí pracovník služby do vašeho datacentra pro nasazení.

Po shromáždění všech požadovaných informací prostřednictvím sešitu nasazení poskytovatel řešení zahájí proces výroby na základě shromažďovaných dat, aby zajistil úspěšnou integraci Azure Stack do vašeho datacentra.

## <a name="hardware-delivery-phase"></a>Fáze doručování hardwaru
Poskytovatel řešení bude spolupracovat s vámi na plánování, kdy řešení přijde do vašeho zařízení. Po přijetí a uvedení na místo toho budete muset naplánovat čas u poskytovatele řešení, aby měl inženýr k dispozici k provedení nasazení Azure Stack.

Je **velmi důležité** , aby všechna data požadovaných součástí byla uzamčena a k dispozici *předtím, než dorazí pracovník do lokality k nasazení řešení*.

-   Všechny certifikáty musí být zakoupené a připravené.

-   Musí být rozhodnuto o názvu oblasti.

-   Všechny parametry integrace sítě se dokončují a shodují s tím, co jste sdíleli s vaším poskytovatelem řešení.

> [!TIP]
> Pokud se některé z těchto informací změnily, nezapomeňte před naplánováním vlastního nasazení sdělit změnu u poskytovatele řešení.

## <a name="onsite-deployment-phase"></a>Fáze nasazení na pracovišti
Aby bylo možné nasadit Azure Stack, musí být k dispozici inženýr v lokalitě od poskytovatele hardwarových řešení, aby bylo možné nasazení aktivovat. Aby se zajistilo úspěšné nasazení, ujistěte se, že se nezměnily všechny informace, které jsou součástí listu nasazení.

Následující kontroly byste měli očekávat od inženýra při nasazení v rámci tohoto prostředí:

- Zkontrolujte všechna připojení kabelů a ohraničení, abyste zajistili správné sestavování řešení a splňovali vaše požadavky.
- Nakonfigurujte HLH řešení (hostitel životního cyklu životnosti hardwaru), pokud je k dispozici.
- Zkontrolujte, jestli jsou správně nastavená všechna řadiče pro správu základní desky, BIOS a síť.
- Ujistěte se, že řešení firmware pro všechny komponenty má nejnovější schválenou verzi.
- Spusťte nasazení.

> [!NOTE]
> Postup nasazení prováděný inženýrem na pracovišti může trvat přibližně jeden pracovní týden.

## <a name="post-deployment-phase"></a>Fáze po nasazení
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

## <a name="overall-timeline"></a>Celková časová osa

![Celková časová osa pro Azure Stack nasazení na pracovišti](./media/azure-stack-datacenter-integration-walkthrough/image1.png)

## <a name="support"></a>Podpora
Azure Stack umožňuje integrované prostředí podpory v rámci Azure, které pokrývá kompletní životní cyklus systému. Aby zákazníci mohli plně podporovat Azure Stack integrovaných systémů, potřebují dvě smlouvy o podpoře: jeden s Microsoftem (nebo jeho poskytovatel Cloud Solution Provider) pro podporu služeb Azure a jeden s poskytovatelem hardwaru pro podporu systému. Integrované prostředí podpory zajišťuje koordinovanou eskalaci a řešení, aby zákazníci získali konzistentní možnosti podpory bez ohledu na to, kdo je vyvolal. Pro zákazníky, kteří už mají podporu Premier, Azure – Standard nebo ProDirect nebo partner support s Microsoftem, je k dispozici Azure Stack softwarová podpora.

Integrované prostředí podpory využívá mechanismus výměny malých a velkých písmen pro obousměrné přenosy případů podpory a aktualizace případů mezi společností Microsoft a hardwarovým partnerem. Microsoft Azure Stack se budou řídit [moderními zásadami životního cyklu](https://support.microsoft.com/help/30881).

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [obecných požadavcích integrace Datacenter](azure-stack-datacenter-integration.md).
