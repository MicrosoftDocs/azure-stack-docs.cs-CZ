---
title: Vzor škálování mezi cloudy (místní data) v centru Azure Stack
description: Naučte se, jak vytvořit škálovatelnou mezicloudovou aplikaci, která používá Prem data v Azure a centra Azure Stack.
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: edbb608fbf8e5288f29572bfe4cca98ffb3cb8fc
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "80812386"
---
# <a name="cross-cloud-scaling-on-premises-data-pattern"></a>Škálování mezi cloudy (místní data) – vzor

Naučte se, jak vytvořit hybridní aplikaci, která zahrnuje Azure a centrum Azure Stack. Tento model také ukazuje, jak používat jeden místní zdroj dat pro dodržování předpisů.

## <a name="context-and-problem"></a>Kontext a problém

Mnoho organizací shromažďuje a ukládá obrovské množství citlivých zákaznických dat. Často se jim zabraňují ukládat citlivá data ve veřejném cloudu kvůli právním předpisům nebo zásadám pro státní správu. Tyto organizace také chtějí využít výhod škálovatelnosti veřejného cloudu. Veřejný cloud dokáže zvládnout sezónní špičky v provozu a umožňuje zákazníkům platit přesně pro potřebný hardware, když ho potřebují.

## <a name="solution"></a>Řešení

Řešení využívá výhody dodržování předpisů privátního cloudu a spojuje je s škálovatelností veřejného cloudu. Hybridní cloud Azure a centra Azure Stack zajišťuje konzistentní prostředí pro vývojáře. Tato konzistence umožňuje použít své dovednosti jak pro veřejné, tak pro místní prostředí.

Průvodce nasazením řešení umožňuje nasadit identickou webovou aplikaci do veřejného i privátního cloudu. Můžete také přistupovat k síti, která není v Internetu hostovaná v privátním cloudu. Webové aplikace se monitorují pro zatížení. Při významném nárůstu provozu program zpracovává záznamy DNS pro přesměrování provozu do veřejného cloudu. V případě, že provoz již není důležitý, záznamy DNS se aktualizují, aby se přesměrovaly zpět do privátního cloudu.

[![Škálování v různých cloudech s premmi datovými vzorci](media/pattern-cross-cloud-scale-onprem-data/solution-architecture.png)](media/pattern-cross-cloud-scale-onprem-data/solution-architecture.png)

## <a name="components"></a>Komponenty

Toto řešení používá následující komponenty:

| Vrstva | Součást | Popis |
|----------|-----------|-------------|
| Azure | Azure App Service | [Azure App Service](/azure/app-service/) umožňuje sestavovat a hostovat webové aplikace, aplikace API RESTful a Azure Functions. Vše v programovacím jazyce podle vašeho výběru bez správy infrastruktury. |
| | Azure Virtual Network| [Azure Virtual Network (VNET)](/azure/virtual-network/virtual-networks-overview) je základní stavební blok pro privátní sítě v Azure. Virtuální síť umožňuje více typů prostředků Azure, jako jsou virtuální počítače, pro zabezpečenou komunikaci mezi sebou, internetem a místními sítěmi. Řešení také ukazuje použití dalších síťových součástí:<br>– podsítě aplikací a brány.<br>– místní brána místní sítě.<br>– Brána virtuální sítě, která funguje jako připojení brány VPN typu Site-to-site.<br>– Veřejná IP adresa.<br>– připojení VPN typu Point-to-site.<br>– Azure DNS pro hostování domén DNS a poskytování překladu názvů. |
| | Azure Traffic Manager | [Azure Traffic Manager](/azure/traffic-manager/traffic-manager-overview) je nástroj pro vyrovnávání zatížení pro provoz založený na DNS. Umožňuje řídit distribuci provozu uživatelů pro koncové body služby v různých datových centrech. |
| | Azure Application Insights | [Application Insights](/azure/azure-monitor/app/app-insights-overview) je rozšiřitelná služba pro správu výkonu aplikací, která umožňuje webovým vývojářům vytvářet a spravovat aplikace na různých platformách.|
| | Azure Functions | [Azure Functions](/azure/azure-functions/) umožňuje spuštění kódu v prostředí bez serveru, aniž byste nejdřív museli vytvořit virtuální počítač nebo publikovat webovou aplikaci. |
| | Automatické škálování Azure | [Automatické škálování](/azure/azure-monitor/platform/autoscale-overview) je integrovaná funkce Cloud Services, virtuálních počítačů a webových aplikací. Tato funkce umožňuje aplikacím provádět své nejlepší změny při změnách na vyžádání. Aplikace se upraví pro špičky provozu a upozorní vás, když se podle potřeby mění a mění velikost metrik. |
| Centrum Azure Stack | IaaS COMPUTE | Služba Azure Stack hub umožňuje používat stejný aplikační model, Samoobslužný portál a rozhraní API, které podporuje Azure. Azure Stack centra IaaS umožňuje širokou škálu Open Source technologií pro konzistentní nasazení hybridních cloudů. Příklad řešení používá virtuální počítač s Windows serverem, který SQL Server například.|
| | Azure App Service | Stejně jako u webové aplikace Azure používá řešení [Azure App Service v centru Azure Stack](/azure-stack/operator/azure-stack-app-service-overview) k hostování webové aplikace. |
| | Sítě | Virtual Network centra Azure Stack funguje stejně jako Azure Virtual Network. Používá mnoho ze stejných síťových součástí, včetně vlastních názvů hostitelů.
| Azure DevOps Services | Registrace | Rychle nastavte průběžnou integraci pro sestavování, testování a nasazování. Další informace najdete v tématu [registrace, přihlášení do Azure DevOps](/azure/devops/user-guide/sign-up-invite-teammates?view=azure-devops). |
| | Azure Pipelines | Použijte [Azure Pipelines](/azure/devops/pipelines/agents/agents?view=azure-devops) pro průběžnou integraci/průběžné doručování. Azure Pipelines umožňuje spravovat hostované agenty sestavení a vydání a definice. |
| | Úložiště kódu | Využijte více úložišť kódu pro zjednodušení vašeho vývojového kanálu. Používejte existující úložiště kódu na GitHubu, BitBucket, Dropboxu, OneDrivu a Azure Repos. |

## <a name="issues-and-considerations"></a>Problémy a důležité informace

Při rozhodování, jak implementovat toto řešení, vezměte v úvahu následující body:

### <a name="scalability"></a>Škálovatelnost

Centrum Azure a Azure Stack se jedinečně hodí pro podporu potřeb dnešní globálně distribuované firmy.

#### <a name="hybrid-cloud-without-the-hassle"></a>Hybridní cloud bez starostí

Microsoft nabízí bezkonkurenční integraci místních assetů pomocí centra Azure Stack a Azure v jednom sjednoceném řešení. Tato integrace eliminuje starosti se správou řešení více bodů a kombinací poskytovatelů cloudu. Díky škálování mezi cloudy je výkon Azure jenom pár kliknutí. Stačí připojit centrum Azure Stack k Azure pomocí shlukování cloudu a vaše data a aplikace budou v případě potřeby k dispozici v Azure.

- Eliminujte nutnost sestavovat a udržovat sekundární webový server DR.
- Šetřete čas a peníze tím, že eliminují zálohování na pásku a zastavují až 99 let zálohovaných dat v Azure.
- K využití hospodárnosti a pružnosti cloudu můžete snadno migrovat úlohy technologie Hyper-V, fyzických (ve verzi Preview) a VMware (ve verzi Preview) do Azure.
- Spouštějte sestavy náročné na výpočetní výkon nebo analýzy na replikovanou kopii místního assetu v Azure, aniž by to mělo dopad na produkční úlohy.
- Rozhlaste se do cloudu a spouštějte místní úlohy v Azure s většími výpočetními šablonami, pokud je to potřeba. Hybrid poskytuje potřebný výkon, když ho potřebujete.
- Vytváření vícevrstvých vývojových prostředí v Azure s několika kliknutími – dokonce můžete replikovat živá produkční data do vývojového a testovacího prostředí, aby se zachovala téměř v reálném čase.

#### <a name="economy-of-cross-cloud-scaling-with-azure-stack-hub"></a>Ekonomická škála škálování mezi cloudy pomocí centra Azure Stack

Klíčovou výhodou pro shlukování cloudu je úspora z ekonomické úspory. Platíte za další prostředky jenom v případě, že se pro tyto prostředky vyžaduje poptávka. Nemusíte nic dalšího vypovídat z zbytečné nadbytečné kapacity ani se pokoušet odhadnout špičky a kolísání poptávky.

#### <a name="reduce-high-demand-loads-into-the-cloud"></a>Snížení zátěže s vysokým požadavkem do cloudu

Škálování mezi více cloudy se dá použít k zátěži při zpracování. Zatížení se distribuuje přesunutím základních aplikací do veřejného cloudu a uvolníte tak místní prostředky pro důležité obchodní aplikace. Aplikaci můžete použít pro privátní cloud a pak se rozlišit k veřejnému cloudu jenom v případě, že je to nutné pro splnění požadavků.

### <a name="availability"></a>Dostupnost

Globální nasazení má své vlastní výzvy, jako je připojení k proměnným a liší se státními předpisy podle oblasti. Vývojáři můžou vyvíjet jenom jednu aplikaci a pak ji nasadit v různých případech s různými požadavky. Nasaďte aplikaci do veřejného cloudu Azure a pak místně nasaďte další instance nebo komponenty. Provoz mezi všemi instancemi můžete spravovat pomocí Azure.

### <a name="manageability"></a>Možnosti správy

#### <a name="a-single-consistent-development-approach"></a>Jediný konzistentní vývojářský přístup

Centrum Azure a Azure Stack vám umožní používat v celé organizaci konzistentní sadu vývojářských nástrojů. Tato konzistence usnadňuje implementaci postupu kontinuální integrace a průběžného vývoje (CI/CD). Mnohé aplikace a služby nasazené v Azure nebo centra Azure Stack jsou zaměnitelné a můžou běžet v libovolném umístění bez problémů.

Hybridní kanál CI/CD vám může pomáhat:

- Iniciuje nové sestavení na základě potvrzení kódu do úložiště kódu.
- K automatickému nasazení nově vytvořeného kódu do Azure pro testování přijetí uživateli.
- Po úspěšném testování kódu se automaticky nasadí do centra Azure Stack.

### <a name="a-single-consistent-identity-management-solution"></a>Jediné, konzistentní řešení pro správu identit

Centrum Azure Stack spolupracuje s Azure Active Directory (Azure AD) i Active Directory Federation Services (AD FS) (ADFS). Centrum Azure Stack spolupracuje se službou Azure AD v propojených scénářích. Pro prostředí, která nemají připojení, můžete použít AD FS jako odpojené řešení. Instanční objekty slouží k udělení přístupu k aplikacím, které jim umožní nasadit nebo nakonfigurovat prostředky prostřednictvím Azure Resource Manager.

### <a name="security"></a>Zabezpečení

#### <a name="ensure-compliance-and-data-sovereignty"></a>Zajištění dodržování předpisů a suverenity dat

Služba Azure Stack hub umožňuje spouštět stejnou službu napříč několika zeměmi, jako byste používali veřejný cloud. Nasazení stejné aplikace v datových centrech v každé zemi umožňuje splnění požadavků na svrchovanost dat. Tato schopnost zajišťuje uchovávání osobních údajů v rámci hranic každé země.

#### <a name="azure-stack-hub---security-posture"></a>Centrum Azure Stack – zabezpečení stav

Neexistuje žádný stav zabezpečení bez pevného, nepřetržitého procesu obsluhy. Z tohoto důvodu společnost Microsoft investovala do modulu orchestrace, který v rámci celé infrastruktury provede bezproblémové opravy a aktualizace.

Díky partnerství s partnery od výrobců OEM Azure Stack Microsoft rozšiřuje stejné zabezpečení stav na komponenty specifické pro výrobce OEM, jako je hostitel životního cyklu hardwaru a software, který je na něm spuštěný. Toto partnerství zajišťuje, aby centrum Azure Stack v celé infrastruktuře stav jednotné a Solid Security. Zákazníci pak můžou sestavovat a zabezpečovat své aplikační úlohy.

#### <a name="use-of-service-principals-via-powershell-cli-and-azure-portal"></a>Použití instančních objektů prostřednictvím PowerShellu, rozhraní příkazového řádku a Azure Portal

Pokud chcete dát přístup k prostředkům skriptu nebo aplikaci, nastavte identitu vaší aplikace a ověřte aplikaci s vlastními přihlašovacími údaji. Tato identita je označována jako instanční objekt a umožňuje:

- Přiřaďte oprávnění identitě aplikace, která se liší od vašich vlastních oprávnění a jsou omezená na to, aby aplikace vyhovovaly.
- Při provádění bezobslužného skriptu použít k ověření certifikát.

Další informace o vytváření instančního objektu a použití certifikátu pro přihlašovací údaje najdete v tématu [použití identity aplikace pro přístup k prostředkům](/azure-stack/operator/azure-stack-create-service-principals).

## <a name="when-to-use-this-pattern"></a>Kdy se má tento model použít

- Moje organizace používá přístup DevOps nebo má jedno plánované pro budoucnost v blízké budoucnosti.
- Chci implementovat postupy CI/CD v rámci naší implementace centra Azure Stack a veřejného cloudu.
- Chci konsolidovat kanál CI/CD napříč cloudem a místními prostředími.
- Chci mít možnost vyvíjet aplikace hladce pomocí cloudu nebo místních služeb.
- Chci využít konzistentní vývojářské dovednosti napříč cloudem a místními aplikacemi.
- Používám Azure, ale mám vývojáře, kteří pracují v místním cloudu Azure Stack hub.
- U mých místních aplikací se během sezónních, cyklických nebo nepředvídatelných výkyvů objeví špička v poptávce.
- Mám místní součásti a chci Cloud používat k bezproblémovému navýšení kapacity.
- Chci cloudovou škálovatelnost, ale chci, aby moje aplikace běžela v místním prostředí, co nejvíce.

## <a name="next-steps"></a>Další kroky

Další informace o tématech zavedených v tomto článku:

- Sledujte, jak se tento model používá, a Prohlédněte si [dynamicky škálované aplikace mezi datovými centry a veřejným cloudem](https://www.youtube.com/watch?v=2lw8zOpJTn0) .
- Další informace o osvědčených postupech a zodpovězení dalších dotazů, které můžete mít, najdete v tématu [aspekty návrhu hybridní aplikace](overview-app-design-considerations.md) .
- Tento model používá Azure Stack řady produktů, včetně centra Azure Stack. Další informace o celém portfoliu produktů a řešení najdete v [Azure Stack rodině produktů a řešení](/azure-stack) .

Až budete připraveni otestovat příklad řešení, pokračujte v [Průvodci nasazením řešení pro škálování mezi cloudy (místní data)](solution-deployment-guide-cross-cloud-scaling-onprem-data.md). Průvodce nasazením poskytuje podrobné pokyny pro nasazení a testování jeho komponent.
