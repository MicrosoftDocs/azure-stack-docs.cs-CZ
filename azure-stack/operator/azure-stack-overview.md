---
title: Přehled centra Azure Stack | Microsoft Docs
description: Přehled toho, co je centrum Azure Stack a jak vám umožňuje spouštět služby Azure ve vašem datovém centru.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 09/16/2019
ms.author: justinha
ms.reviewer: unknown
ms.custom: ''
ms.lastreviewed: 05/14/2019
ms.openlocfilehash: 9461b7c306eac0c2a1467378a49d4e91e234a629
ms.sourcegitcommit: 5ef433aa6b75cdfb557fab0ef9308ff2118e66e5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2019
ms.locfileid: "73595426"
---
# <a name="azure-stack-hub-overview"></a>Přehled centra Azure Stack

Centrum Azure Stack je rozšíření Azure, které poskytuje způsob, jak spouštět aplikace v místním prostředí a poskytovat služby Azure ve vašem datovém centru. S konzistentní cloudovou platformou můžou organizace bez obav dělat rozhodování o technologiích na základě podnikových požadavků, a ne podle obchodních rozhodnutí založených na omezeních technologie.

## <a name="why-use-azure-stack-hub-"></a>Proč používat centrum Azure Stack?

Azure poskytuje vývojářům bohatou platformu pro vytváření moderních aplikací. Některé cloudové aplikace ale čelí překážkám, jako je latence, přerušované připojení a předpisy. Centrum Azure a Azure Stack odemknout nové případy použití hybridního cloudu pro zákazníky i interní obchodní aplikace:

- **Hraniční a odpojená řešení**. Požadavky na latenci a připojení k místnímu zpracování dat v Azure Stackovém centru a jejich agregaci v Azure pro další analýzy s využitím společné aplikační logiky napříč oběma. Můžete dokonce nasazovat Azure Stack hub odpojený od Internetu bez připojení k Azure. V příkladech si můžete představit podlahovou podlahu, lodě přepravní a odminování.

- **Cloudové aplikace, které splňují různé předpisy**. Vývoj a nasazování aplikací v Azure s plnou flexibilitou pro nasazení místně pomocí centra Azure Stack pro splnění zákonných požadavků nebo zásad. Nevyžadují se žádné změny kódu. Mezi příklady aplikací patří globální audit, finanční vykazování, obchodování s cizím systémem Exchange, hraní online her a generování sestav výdajů.

- **Model cloudové aplikace v místním**prostředí. Používejte služby, kontejnery, bez serveru a architektury mikroslužeb, abyste mohli aktualizovat a roztáhnout existující aplikace nebo vytvářet nové. Pomocí konzistentních DevOps procesů napříč Azure v cloudu a centra Azure Stack v místním prostředí Urychlete modernizaci aplikací pro základní klíčové aplikace.

Azure Stack hub umožňuje tyto scénáře používání poskytnout:

- Integrované prostředí pro doručování, které vám umožní rychle začít pracovat s účelově vytvořenými integrovanými systémy Azure Stack hub od důvěryhodných hardwarových partnerů. Po doručení se Azure Stack centrum snadno integruje do datového centra pomocí monitorování prostřednictvím sady System Center Operations Manager Management Pack nebo rozšíření Nagios.

- Flexibilní správa identit pomocí služby Azure Active Directory (Azure AD) pro Azure a Azure Stack centra hybridních prostředí a využití Active Directory Federation Services (AD FS) (AD FS) pro odpojená nasazení.

- Prostředí pro vývoj aplikací konzistentní s Azure, které umožňuje maximalizovat produktivitu vývojářů a umožnit běžné DevOps přístupy napříč hybridními prostředími.

- Doručování služeb Azure z místního prostředí s využitím hybridního cloudového výpočetního výkonu. K nasazení a provozování služeb Azure IaaS (infrastruktura jako služby) a PaaS (platforma jako služba) a (platforma jako služba) použijte stejné administrativní prostředí a nástroje jako Azure, a to prostřednictvím služby Azure a centra Azure Stack. Microsoft zajišťuje nepřetržitou inovaci Azure do centra Azure Stack, včetně nových služeb Azure, aktualizací stávajících služeb a dalších Azure Marketplace aplikací a imagí.

## <a name="azure-stack-hub-architecture"></a>Architektura centra Azure Stack

Integrované systémy Azure Stack hub se skládají z racků 4-16 serverů vytvořených důvěryhodnými hardwarovými partnery a přímo do vašeho datacentra. Po doručení bude poskytovatel řešení spolupracovat s vámi, aby nasadil integrovaný systém a zajistil, že řešení Azure Stack hub splňuje vaše obchodní požadavky. Připravte si vaše datové centrum tím, že zajistíte splnění všech požadovaných požadavků na napájení a chlazení, připojení k hraniční síti a další požadované požadavky na integraci Datacenter.

> Další informace o prostředí integrace Datacenter centra Azure Stack najdete v tématu [integrace centrálního](azure-stack-customer-journey.md)centra pro Azure Stack.

Centrum Azure Stack je postavené na standardním hardwaru a spravuje se pomocí stejných nástrojů, které už používáte pro správu předplatných Azure. V důsledku toho můžete použít konzistentní procesy DevOps bez ohledu na to, jestli jste připojení k Azure.

Architektura centra Azure Stack umožňuje poskytovat služby Azure na hraničních zařízeních pro vzdálená umístění nebo přerušované připojení odpojené od Internetu. Můžete vytvářet hybridní řešení, která zpracovávají data místně v Azure Stackovém centru a pak je agregovat do Azure pro další zpracování a analýzu. Vzhledem k tomu, že centrum Azure Stack je nainstalované místně, můžete splnit konkrétní zákonné požadavky nebo požadavky zásad, a to díky flexibilitě nasazování cloudových aplikací v místním prostředí bez změny kódu.

## <a name="deployment-options"></a>Možnosti nasazení

### <a name="production-or-evaluation-environments"></a>Produkční nebo vyhodnocovací prostředí

Centrum Azure Stack se nabízí ve dvou možnostech nasazení, které odpovídají vašim potřebám, Azure Stack integrovaným systémům rozbočovačů pro produkční použití a Azure Stack hub Development Kit (ASDK) pro vyhodnocování Azure Stack centra:

- **Azure Stack integrovaných systémů centra**: integrované systémy Azure Stack hub jsou nabízeny prostřednictvím partnerství s Microsoftem a hardwarovými partnery a vytvářejí řešení, které nabízí cloudové inovace a jednoduchost správy computingu. Vzhledem k tomu, že centrum Azure Stack se nabízí jako integrovaný hardware a softwarový systém, máte flexibilitu a kontrolu, které potřebujete, a také schopnost inovovat z cloudu. Rozsah integrovaných systémů centra Azure Stack je velikost od 4-16 uzlů a jsou společně podporovány hardwarovým partnerem a společností Microsoft. Pomocí integrovaných systémů Azure Stack Hub můžete vytvářet nové scénáře a nasazovat nová řešení pro produkční úlohy.

- **ASDK (Azure Stack hub Development Kit)** : [ASDK](../asdk/asdk-what-is.md) je bezplatné nasazení s jedním uzlem Azure Stackho centra, které můžete použít k vyhodnocení a získání informací o Azure Stack hub. ASDK můžete použít také jako vývojářské prostředí k vytváření aplikací pomocí rozhraní API a nástrojů, které jsou konzistentní s Azure. ASDK ale nemá sloužit jako produkční prostředí a má v porovnání s úplnými provozními nasazeními integrovaných systémů následující omezení:

    - ASDK se dá přidružit jenom k jednomu zprostředkovateli identity Azure AD nebo AD FS.
    - Vzhledem k tomu, že komponenty centra Azure Stack jsou nasazeny na jednom hostitelském počítači, jsou k dispozici omezené fyzické prostředky pro prostředky tenanta. Tato konfigurace není určena pro škálování nebo pro vyhodnocení výkonu.
    - Síťové scénáře jsou omezené z důvodu požadavků na nasazení s jedním hostitelem a síťovým ROZHRANÍm.

### <a name="connection-models"></a>Modely připojení

Můžete zvolit nasazení centra Azure Stack **připojeného** k Internetu (a do Azure) nebo z něj **Odpojit** . Tato volba definuje, jaké možnosti jsou k dispozici pro vaše úložiště identit (Azure AD nebo AD FS) a model fakturace (platíte při použití fakturace nebo fakturace na základě kapacity).

> Další informace najdete v tématu požadavky na [připojené](azure-stack-connected-deployment.md) a [odpojené](azure-stack-disconnected-deployment.md) modely nasazení.

### <a name="identity-provider"></a>Zprostředkovatel identity 

Azure Stack hub používá k poskytnutí identit buď Azure AD, nebo Active AD FS. Azure AD je cloudový zprostředkovatel identity pro více tenantů od Microsoftu. Většina hybridních scénářů s nasazeními připojenými k Internetu používá jako úložiště identit službu Azure AD.

Pro odpojená nasazení centra Azure Stack musíte použít AD FS. Poskytovatelé prostředků Azure Stack hub a další aplikace fungují podobně jako AD FS nebo Azure AD. Centrum Azure Stack zahrnuje svou vlastní instanci služby Active Directory a Graph API služby Active Directory.

> [!IMPORTANT]
> Po nasazení nemůžete změnit poskytovatele identity. Pokud chcete použít jiného zprostředkovatele identity, musíte znovu nasadit centrum Azure Stack. Další informace o požadavcích na identitu centra Azure Stack najdete v článku [Přehled identity pro centrum Azure Stack](azure-stack-identity-overview.md).

## <a name="how-is-azure-stack-hub-managed"></a>Jak se spravuje centrum Azure Stack?

Centrum Azure Stack můžete spravovat pomocí portálu pro správu, portálu User Portal nebo [PowerShellu](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.7.1). Portály centra Azure Stack jsou každou zálohované samostatnými instancemi Azure Resource Manager. **Operátor centra Azure Stack** využívá portál pro správu ke správě centra Azure Stack a k provádění akcí, jako je vytváření nabídek tenantů, a udržování stavu a monitorování stavu integrovaného systému. Portál User Portal poskytuje samoobslužné prostředí pro využívání cloudových prostředků, jako jsou virtuální počítače, účty úložiště a webové aplikace.

> Další informace o správě centra Azure Stack pomocí portálu pro správu najdete v tématu [rychlý Start k portálu pro správu centra Azure Stack](azure-stack-manage-portals.md).

Jako operátor centra Azure Stack můžete doručovat širokou škálu služeb a aplikací, jako jsou [virtuální počítače](azure-stack-tutorial-tenant-vm.md), [webové aplikace](azure-stack-app-service-overview.md), vysoce dostupné [SQL Server](azure-stack-tutorial-sql.md)a databáze [MySQL serveru](azure-stack-tutorial-mysql.md) . K nasazení SharePointu, Exchange a dalších šablon můžete také použít [rychlý Start centra Azure Stack Azure Resource Manager](https://github.com/Azure/AzureStack-QuickStart-Templates) .

Pomocí portálu pro správu můžete [nakonfigurovat centrum Azure Stack pro poskytování služeb](service-plan-offer-subscription-overview.md) klientům pomocí plánů, kvót, nabídek a předplatných. Uživatelé tenanta se můžou přihlásit k odběru několika nabídek. Nabídky mohou mít jeden nebo více plánů a plány můžou mít jednu nebo více služeb. Operátoři také spravují kapacitu a reagují na výstrahy.

Když je nakonfigurované centrum Azure Stack, **uživatel centra Azure Stack** spotřebovává služby, které nabízí operátor. Uživatelé můžou zřizovat, monitorovat a spravovat služby, ke kterým se přihlásili, jako jsou webové aplikace, úložiště a virtuální počítače.

> Další informace o správě centra Azure Stack, včetně toho, jaké účty se mají použít, kde jsou typické zodpovědnosti operátora, co říct vašim uživatelům a jak získat pomoc, najdete v [Azure Stack základy správy centra](azure-stack-manage-basics.md).

## <a name="resource-providers"></a>Poskytovatelé prostředků

Poskytovatelé prostředků jsou webové služby, které tvoří základ pro všechny služby centra Azure Stack IaaS a PaaS. Azure Resource Manager spoléhá na různé poskytovatele prostředků, aby poskytoval přístup ke službám. Každý poskytovatel prostředků vám pomůže nakonfigurovat a řídit příslušné prostředky. Správci služeb můžou také přidávat nové vlastní poskytovatele prostředků.

### <a name="foundational-resource-providers"></a>Zprostředkovatelé základních prostředků

Existují tři poskytovatelé prostředků pro základní IaaS:

- **COMPUTE**: poskytovatel výpočetních prostředků umožňuje Azure Stack klientům centra vytvářet vlastní virtuální počítače. Poskytovatel výpočetních prostředků zahrnuje možnost vytvářet virtuální počítače i rozšíření virtuálních počítačů. Služba rozšíření virtuálních počítačů pomáhá poskytovat funkce IaaS pro virtuální počítače s Windows a Linux. Jako příklad můžete pomocí zprostředkovatele prostředků služby COMPUTE zřídit virtuální počítač pro Linux a spustit skripty bash během nasazování a nakonfigurovat virtuální počítač.
- **Poskytovatel síťových prostředků**: poskytovatel síťových prostředků nabízí řadu funkcí Software Defined Networking (SDN) a virtualizace síťových funkcí (NFV) pro privátní cloud. Poskytovatele síťových prostředků můžete použít k vytvoření prostředků, jako jsou nástroje pro vyrovnávání zatížení softwaru, veřejné IP adresy, skupiny zabezpečení sítě a virtuální sítě.
- **Poskytovatel prostředků úložiště**: poskytovatel prostředků úložiště nabízí čtyři služby úložiště konzistentní s Azure: objekty [BLOB](https://docs.microsoft.com/azure/storage/common/storage-introduction#blob-storage), [front](https://docs.microsoft.com/azure/storage/common/storage-introduction#queue-storage), [tabulek](https://docs.microsoft.com/azure/storage/common/storage-introduction#table-storage)a [Key Vault](https://docs.microsoft.com/azure/key-vault/) poskytující správu a auditování tajných kódů. například hesla a certifikáty. Poskytovatel prostředků úložiště nabízí cloudovou službu pro správu úložiště, která usnadňuje správu služeb úložiště konzistentních od Azure. Azure Storage poskytuje flexibilitu pro ukládání a načítání velkých objemů nestrukturovaných dat, jako jsou dokumenty a mediální soubory s objekty blob Azure a strukturovaná data založená na NoSQL s tabulkami Azure.

### <a name="optional-resource-providers"></a>Volitelní poskytovatelé prostředků

Existují tři Volitelní poskytovatelé prostředků PaaS, které můžete nasadit a používat s Azure Stack hub:

- **App Service**: [Azure App Service na rozbočovači Azure Stack](azure-stack-app-service-overview.md) je nabídka PaaS Microsoft Azure dostupná pro Azure Stack centra. Služba umožňuje vašim interním nebo externím zákazníkům vytvářet webové aplikace, rozhraní API a Azure Functions aplikací pro libovolnou platformu nebo zařízení.
- **SQL Server**: k poskytování databází SQL jako služby centra Azure Stack použijte [poskytovatele prostředků SQL Server](azure-stack-sql-resource-provider.md) . Až nainstalujete poskytovatele prostředků a připojíte ho k jedné nebo více instancím SQL Server, můžete vy a vaši uživatelé vytvářet databáze pro cloudové nativní aplikace, weby, které používají SQL, a další úlohy, které používají SQL.
- **Server MySQL**: k zveřejnění databází MySQL jako služby centra Azure Stack použijte [poskytovatele prostředků serveru MySQL](azure-stack-mysql-resource-provider-deploy.md) . Poskytovatel prostředků MySQL se spouští jako služba na virtuálním počítači jádra serveru Windows Server 2016.

## <a name="providing-high-availability"></a>Zajištění vysoké dostupnosti

Aby se dosáhlo vysoké dostupnosti produkčního systému více virtuálních počítačů v Azure, virtuální počítače se umístí do [skupiny dostupnosti](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) , která je rozšíří napříč více doménami selhání a aktualizačními doménami. V menším měřítku centra Azure Stack se doména selhání ve skupině dostupnosti definuje jako jeden uzel v jednotce škálování.  

I když je infrastruktura centra Azure Stack už odolná vůči selháním, pak základní technologie (Clustering s podporou převzetí služeb při selhání) stále způsobí nějaké výpadky virtuálních počítačů na ovlivněném fyzickém serveru, pokud dojde k selhání hardwaru. Centrum Azure Stack podporuje skupinu dostupnosti s maximálním počtem tří domén selhání pro zajištění konzistence s Azure.

- **Domény selhání**: virtuální počítače umístěné ve skupině dostupnosti se fyzicky izolují od sebe navzájem, jejich rozprostření do více domén selhání (Azure Stack uzly centra). Pokud dojde k selhání hardwaru, virtuální počítače z neúspěšné domény selhání se restartují v jiných doménách selhání. Budou se uchovávat v samostatných doménách selhání z ostatních virtuálních počítačů, ale ve stejné skupině dostupnosti, pokud je to možné. Když se hardware vrátí zpátky do online režimu, virtuální počítače se znovu vyrovnávají, aby se zachovala vysoká dostupnost.

- **Aktualizace domén**: aktualizační domény jsou další pojmy Azure, které v sadách dostupnosti poskytují vysokou dostupnost. Aktualizační doména je logická skupina základního hardwaru, která může prostoupit v rámci údržby. Virtuální počítače umístěné ve stejné aktualizační doméně se při plánované údržbě restartují společně. Když klienti vytvářejí virtuální počítače v rámci skupiny dostupnosti, platforma Azure automaticky distribuuje virtuální počítače napříč těmito aktualizačními doménami. V Azure Stackovém centru jsou virtuální počítače za provozu přenášeny v jiných online hostitelích v clusteru před tím, než se aktualizuje jejich podkladový hostitel. Vzhledem k tomu, že během aktualizace hostitele nedochází k výpadku tenanta, funkce aktualizační doména v centru Azure Stack existuje pouze pro kompatibilitu šablon s Azure. Virtuální počítače ve skupině dostupnosti budou na **portálu zobrazeny jako** číslo aktualizační domény.

## <a name="role-based-access-control"></a>Řízení přístupu na základě role

Řízení přístupu na základě role (RBAC) můžete použít k udělení přístupu k systému autorizovaným uživatelům, skupinám a službám tím, že jim přiřadíte role v předplatném, skupině prostředků nebo jednotlivé úrovni prostředků. Každá role definuje úroveň přístupu, kterou má uživatel, skupina nebo služba nad Microsoft Azure Stack prostředky centra.

RBAC centra Azure Stack má tři základní role, které se vztahují na všechny typy prostředků: vlastník, přispěvatel a čtenář. Vlastník má úplný přístup ke všem prostředkům, včetně práva k delegování přístupu jiným uživatelům. Přispěvatel může vytvářet a spravovat všechny typy prostředků Azure, ale nemůže udělovat přístup ostatním. Čtenář může zobrazit jenom existující prostředky. Zbývající role RBAC umožňují správu konkrétních prostředků Azure. Role Přispěvatel virtuálních počítačů například umožňuje vytváření a správu virtuálních počítačů, ale neumožňuje správu virtuální sítě ani podsítě, ke které se virtuální počítač připojuje.

> Další informace najdete v tématu [Správa řízení přístupu na základě rolí](azure-stack-manage-permissions.md).

## <a name="reporting-usage-data"></a>Vytváření sestav – data o využití

Centrum Azure Stack shromažďuje a agreguje data o využití napříč všemi poskytovateli prostředků a odesílá je do Azure ke zpracování prostřednictvím Azure Commerce. Data o využití shromážděná v centru Azure Stack lze zobrazit prostřednictvím REST API. Existuje rozhraní API tenanta konzistentní s Azure a také poskytovatelé a delegovaná rozhraní API pro získání dat o využití napříč všemi předplatnými tenanta. Tato data můžete použít k integraci s externím nástrojem nebo službou pro účely fakturace nebo vrácení peněz. Když Azure Commerce zpracuje využití, dá se zobrazit na fakturačním portálu Azure.

> Přečtěte si další informace o [vytváření sestav Azure Stack dat o využití centra do Azure](azure-stack-usage-reporting.md).

## <a name="next-steps"></a>Další kroky

[Porovnejte Azure Stack hub a globální Azure](compare-azure-azure-stack.md).

[Základy správy](azure-stack-manage-basics.md).

[Rychlý Start: použijte portál pro správu centra Azure Stack](azure-stack-manage-portals.md).
