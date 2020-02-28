---
title: Vzor pro vytváření aplikací, které škálují různé cloudy pomocí Prem dat, v Azure a centra Azure Stack.
description: Naučte se používat Azure a centrum Azure Stack k vytvoření škálovatelné aplikace pro více cloudů, které používají data Prem.
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: f492eba4cd2fae28162eb47b990737a8f5b21c53
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77689524"
---
# <a name="cross-cloud-scaling-on-premises-data-pattern"></a>Škálování mezi cloudy (místní data) – vzor

Naučte se, jak vytvořit hybridní aplikaci, která zahrnuje Azure a centrum Azure Stack. Tento model také ukazuje, jak používat jeden místní zdroj dat pro dodržování předpisů.

## <a name="context-and-problem"></a>Kontext a problém

Řada organizací shromažďuje a ukládá obrovské objemy dat citlivých od zákazníků. Často se jim zabraňují ukládat citlivá data ve veřejném cloudu vzhledem k podnikovým předpisům nebo státním zásadám. Tyto organizace také chtějí využít výhod škálovatelnosti veřejného cloudu. Veřejný cloud dokáže zvládnout sezónní špičky v provozu a umožňuje zákazníkům platit přesně pro potřebný hardware, když ho potřebují.

## <a name="solution"></a>Řešení

Řešení využívá výhody dodržování předpisů privátního cloudu a spojuje je s škálovatelností veřejného cloudu. Hybridní cloud Azure a centra Azure Stack zajišťuje konzistentní prostředí pro vývojáře. Tato konzistence umožňuje použít své dovednosti jak na veřejném, tak i v místním prostředí.

Průvodce nasazením řešení umožňuje nasadit identickou webovou aplikaci do veřejného i privátního cloudu. Můžete také přistupovat k cílové síti, která není v Internetu, která je hostována v privátním cloudu. Webové aplikace jsou monitorovány pro načtení. Při významném nárůstu provozu program zpracovává záznamy DNS pro přesměrování provozu do veřejného cloudu. V případě, že provoz již není důležitý, záznamy DNS se aktualizují, aby se přesměrovaly zpět do privátního cloudu.

[![škálování mezi cloudy pomocí Prem datových vzorů](media/pattern-cross-cloud-scale-onprem-data/solution-architecture.png)](media/pattern-cross-cloud-scale-onprem-data/solution-architecture.png)

## <a name="components"></a>Komponenty

Toto řešení používá následující komponenty:

| Vrstva | Komponenta | Popis |
|----------|-----------|-------------|
| Azure | Azure App Service | [Azure App Service](/azure/app-service/) umožňuje sestavovat a hostovat webové aplikace, aplikace API RESTful a Azure Functions. Vše v programovacím jazyce podle vašeho výběru bez správy infrastruktury. |
| | Azure Virtual Network| [Azure Virtual Network (VNET)](/azure/virtual-network/virtual-networks-overview) je základní stavební blok pro privátní sítě v Azure. Virtuální síť umožňuje více typů prostředků Azure, například Virtual Machines (VM), aby bylo možné zabezpečeně komunikovat mezi sebou, internetem a místními sítěmi. Řešení také ukazuje použití dalších síťových součástí:<br>– podsítě aplikací a brány<br>– místní brána místní sítě<br>– Brána virtuální sítě, která funguje jako připojení brány VPN typu Site-to-site.<br>– Veřejná IP adresa<br>– připojení VPN typu Point-to-site<br>– Azure DNS pro hostování domén DNS a poskytování překladu názvů |
| | Azure Traffic Manager | [Azure Traffic Manager](/azure/traffic-manager/traffic-manager-overview) je nástroj pro vyrovnávání zatížení pro provoz založený na DNS. Umožňuje řídit distribuci provozu uživatelů pro koncové body služby v různých datových centrech. |
| | Azure Application Insights | [Application Insights](/azure/azure-monitor/app/app-insights-overview) je rozšiřitelná služba pro správu výkonu aplikací, která vývojářům umožňuje vytvářet a spravovat aplikace na různých platformách.|
| | Azure Functions | [Azure Functions](/azure/azure-functions/) vám umožní spustit kód v prostředí bez serveru, aniž byste nejdřív museli vytvořit virtuální počítač nebo publikovat webovou aplikaci. |
| | Automatické škálování Azure | [Automatické škálování](/azure/azure-monitor/platform/autoscale-overview) je integrovaná funkce Cloud Services, Virtual Machines a webových aplikací. Tato funkce umožňuje aplikacím provádět své nejlepší změny při změnách na vyžádání. Aplikace se upraví pro špičky provozu a upozorní vás, až se metriky změní, a podle potřeby Škálujte. |
| Centrum Azure Stack | IaaS COMPUTE | Centrum Azure Stack umožňuje používat stejný aplikační model, Samoobslužný portál a rozhraní API, které podporuje Azure. Azure Stack centra IaaS umožňuje širokou škálu Open Source technologií pro konzistentní nasazení hybridních cloudů. Příklad řešení používá virtuální počítač s Windows serverem, který SQL Server například.|
| | Azure App Service | Stejně jako u webové aplikace Azure používá řešení [Azure App Service v centru Azure Stack](/azure-stack/operator/azure-stack-app-service-overview) k hostování webové aplikace. |
| | Sítě | Virtual Network centra Azure Stack funguje stejně jako Azure Virtual Network. Používá mnoho ze stejných síťových součástí, včetně vlastních názvů hostitelů. 
| Azure DevOps Services | Registrace | Rychle nastavte průběžnou integraci pro sestavení, testování a nasazení. Další informace najdete v tématu [registrace, přihlášení do Azure DevOps](/azure/devops/user-guide/sign-up-invite-teammates?view=azure-devops). |
| | Azure Pipelines | Použijte [Azure Pipelines](/azure/devops/pipelines/agents/agents?view=azure-devops) pro průběžnou integraci/průběžné doručování. Azure Pipelines umožňuje spravovat hostované agenty sestavení a vydání a definice. |
| | Úložiště kódu | Využijte více úložišť kódu pro zjednodušení vašeho vývojového kanálu. Používejte existující úložiště kódu na GitHubu, BitBucket, Dropboxu, OneDrivu a Azure Repos. |

## <a name="issues-and-considerations"></a>Problémy a důležité informace

Při rozhodování, jak implementovat toto řešení, vezměte v úvahu následující body:

### <a name="scalability"></a>Škálovatelnost 

Centrum Azure a Azure Stack se jedinečně hodí pro podporu potřeb dnešní globálně distribuované firmy.

**Hybridní cloud bez starostí**

Microsoft nabízí bezkonkurenční integraci místních assetů pomocí centra Azure Stack a Azure v jednom sjednoceném řešení. Tato integrace eliminuje starosti se správou řešení více bodů a kombinací poskytovatelů cloudu. Díky škálování mezi cloudy je výkon Azure jenom pár kliknutí. Připojte centrum Azure Stack k Azure pomocí shlukování cloudu a vaše data a aplikace budou v případě potřeby k dispozici v Azure.

- Eliminujte nutnost sestavovat a udržovat sekundární webový server DR.
- Šetřete čas a peníze tím, že eliminují zálohování na pásku a zastavují až 99 let zálohovaných dat v Azure.
- K využití hospodárnosti a pružnosti cloudu můžete snadno migrovat úlohy technologie Hyper-V, fyzických (ve verzi Preview) a VMware (ve verzi Preview) do Azure.
- Spouštějte sestavy náročné na výpočetní výkon nebo analýzy na replikovanou kopii místního assetu v Azure, aniž by to mělo dopad na produkční úlohy.
- Rozhlaste se do cloudu a spouštějte místní úlohy v Azure s většími výpočetními šablonami, pokud je to potřeba. Hybrid poskytuje potřebný výkon, když ho potřebujete.
- Vytváření vícevrstvých vývojových prostředí v Azure s několika kliknutími – dokonce můžete replikovat živá produkční data do vývojového a testovacího prostředí, aby se zachovala téměř v reálném čase.

**Ekonomická škála škálování mezi cloudy pomocí centra Azure Stack**

Klíčovou výhodou pro shlukování cloudu je úspora z ekonomické úspory. Platíte za další prostředky jenom v případě, že se pro tyto prostředky vyžaduje poptávka. Nemusíte nic dalšího vypovídat z zbytečné nadbytečné kapacity ani se pokoušet odhadnout špičky a kolísání poptávky.

**Snížení zátěže s vysokým požadavkem do cloudu**

Škálování mezi více cloudy se dá použít k zátěži při zpracování. Zatížení se distribuuje přesunutím základních aplikací do veřejného cloudu a uvolníte tak místní prostředky pro důležité obchodní aplikace. Aplikace se dá použít pro privátní cloud a pak se rozpíná do veřejného cloudu jenom v případě, že je to nutné pro splnění požadavků.


### <a name="availability"></a>Dostupnost

Globální nasazení má své vlastní výzvy, jako je třeba připojení k proměnným a různá pravidla pro státní správu podle oblasti. Vývojáři můžou vyvíjet jenom jednu aplikaci a pak ji nasadit v různých případech s různými požadavky.  Nasaďte aplikaci do veřejného cloudu Azure a pak místně nasaďte další instance nebo komponenty. Provoz mezi všemi instancemi můžete spravovat pomocí Azure.

### <a name="manageability"></a>Možnosti správy

**Jediný konzistentní vývojářský přístup**

Centrum Azure a Azure Stack vám umožní používat v celé organizaci konzistentní sadu vývojářských nástrojů. Tato konzistence usnadňuje implementaci postupu kontinuální integrace a průběžného vývoje (CI/CD). Mnohé aplikace a služby nasazené v Azure nebo centra Azure Stack jsou zaměnitelné a můžou běžet v libovolném umístění bez problémů.

Hybridní kanál CI/CD vám může pomáhat:
- Iniciuje nové sestavení na základě potvrzení kódu do úložiště kódu.
- K automatickému nasazení nově vytvořeného kódu do Azure pro testování přijetí uživateli.
- Po úspěšném testování kódu se automaticky nasadí do centra Azure Stack.

**Jediné, konzistentní řešení pro správu identit**

Azure Stack centrum funguje s Azure Active Directory a Active Directory Federation Services (AD FS) (ADFS). Centrum Azure Stack pracuje s Azure Active Directory v propojených scénářích. Pro prostředí, která nemají připojení, můžete použít AD FS jako odpojené řešení. Instanční objekty slouží k udělení přístupu k aplikacím, které jim umožní nasadit nebo nakonfigurovat prostředky prostřednictvím Azure Resource Manager. 

### <a name="security"></a>Zabezpečení

**Zajištění dodržování předpisů a suverenity dat**

Služba Azure Stack hub umožňuje spouštět stejnou službu ve více zemích, stejně jako při použití veřejného cloudu. Nasazení stejné aplikace v datových centrech v každé zemi umožňuje splnění požadavků na svrchovanost dat. Tato schopnost zajišťuje uchovávání osobních údajů v rámci hranic každé země.

**Centrum Azure Stack – zabezpečení stav**

Neexistuje žádný stav zabezpečení bez pevného, nepřetržitého procesu obsluhy. Z tohoto důvodu společnost Microsoft investovala do modulu orchestrace, který v rámci celé infrastruktury provede bezproblémové opravy a aktualizace.

Díky partnerství s partnery od výrobců OEM Azure Stack Microsoft rozšiřuje stejné zabezpečení stav na komponenty specifické pro výrobce OEM. Například hostitel životního cyklu životnosti hardwaru a software, který je v něm spuštěný. Toto partnerství zajišťuje, aby centrum Azure Stack v celé infrastruktuře stav jednotné a Solid Security. Zákazníci pak můžou sestavovat a zabezpečovat své aplikační úlohy.

**Použití instančních objektů prostřednictvím PowerShellu, rozhraní příkazového řádku a Azure Portal**

Pokud chcete dát přístup k prostředkům skriptu nebo aplikaci, nastavte identitu vaší aplikace a ověřte aplikaci s vlastními přihlašovacími údaji. Tato identita se označuje jako instanční objekt a umožňuje:

- Přiřaďte oprávnění identitě aplikace, která se liší od vašich vlastních oprávnění a jsou omezená na to, aby aplikace vyhovovaly.
- Při provádění bezobslužného skriptu použít k ověření certifikát. 

Další informace o vytváření instančního objektu a použití certifikátu pro přihlašovací údaje najdete v tématu [použití identity aplikace pro přístup k prostředkům](/azure-stack/operator/azure-stack-create-service-principals).

## <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít 

- Moje organizace používá přístup DevOps nebo má jedno plánované pro budoucnost v blízké budoucnosti.
- Chci implementovat postupy CI/CD v rámci naší implementace centra Azure Stack a veřejného cloudu.
- Chci konsolidovat kanál CI/CD napříč cloudem a místními prostředími.
- Chci mít možnost vyvíjet aplikace hladce pomocí cloudu nebo místních služeb.
- Chci využít konzistentní dovednosti pro vývojáře napříč cloudem a místními aplikacemi.
- Používám Azure, ale mám vývojáře, kteří pracují v místním cloudu Azure Stack hub.
- U mých místních aplikací se v průběhu sezónních, cyklických nebo nepředvídatelných výkyvů objeví špička v poptávce.
- Mám místní součásti a chci Cloud používat k bezproblémovému navýšení kapacity.
- Chci cloudovou škálovatelnost, ale chci, aby byla aplikace spuštěná v místním prostředí, co nejvíce.

## <a name="next-steps"></a>Další kroky

Další informace o tématech zavedených v tomto článku:
- Sledujte, jak se tento model používá, a Prohlédněte si [dynamicky škálované aplikace mezi datovými centry a veřejným cloudem](https://www.youtube.com/watch?v=2lw8zOpJTn0) .
- Další informace o osvědčených postupech najdete v tématu [aspekty návrhu hybridní aplikace](overview-app-design-considerations.md) a odpovědi na další otázky.
- Tento model používá Azure Stack řady produktů, včetně centra Azure Stack. Podívejte se na [Azure Stack rodinu produktů a řešení](/azure-stack), abyste se dozvěděli víc o celém portfoliu produktů a řešení.

Až budete připraveni otestovat příklad řešení, pokračujte v [Průvodci nasazením řešení pro škálování mezi cloudy (místní data)](solution-deployment-guide-cross-cloud-scaling-onprem-data.md). Průvodce nasazením poskytuje podrobné pokyny pro nasazení a testování jeho komponent.