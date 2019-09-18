---
title: Co je Azure Stack? | Dokumenty Microsoft
description: Přečtěte si, jak Azure Stack umožňuje spouštět služby Azure ve vašem datovém centru.
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
ms.openlocfilehash: 7c84a9cb841f06887d09a650d277566fb9ff18ed
ms.sourcegitcommit: 95f30e32e5441599790d39542ff02ba90e70f9d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71070165"
---
# <a name="azure-stack-overview"></a>Přehled služby Azure Stack

Azure Stack je rozšíření Azure, které poskytuje způsob, jak spouštět aplikace v místním prostředí a poskytovat služby Azure ve vašem datovém centru. S konzistentní cloudovou platformou můžou organizace bez obav dělat rozhodování o technologiích na základě podnikových požadavků, a ne podle obchodních rozhodnutí založených na omezeních technologie.

## <a name="why-use-azure-stack"></a>Proč použít Azure Stack?

Azure poskytuje vývojářům bohatou platformu pro vytváření moderních aplikací. Některé cloudové aplikace ale čelí překážkám, jako je latence, přerušované připojení a předpisy. Azure a Azure Stack odemknout nové případy použití hybridního cloudu pro zákazníka i interní obchodní aplikace:

- **Hraniční a odpojená řešení**. Požadavky na latenci a připojení, které zpracovávají data místně v Azure Stack a pak je agreguje v Azure pro další analýzy s využitím společné aplikační logiky napříč oběma. Můžete dokonce nasadit Azure Stack odpojený od Internetu bez připojení k Azure. V příkladech si můžete představit podlahovou podlahu, lodě přepravní a odminování.

- **Cloudové aplikace, které splňují různé předpisy**. Vývoj a nasazování aplikací v Azure s plnou flexibilitou pro nasazení místně pomocí Azure Stack pro splnění zákonných požadavků nebo požadavků na zásady, aniž by se musely dělat žádné změny kódu. Mezi příklady aplikací patří globální audit, finanční vykazování, obchodování s cizím systémem Exchange, hraní online her a generování sestav výdajů.

- **Model cloudové aplikace v místním**prostředí. Pomocí služeb Azure, kontejnerů, bez serveru a architektury mikroslužeb můžete aktualizovat a roztáhnout existující aplikace nebo vytvořit nové. Používejte konzistentní DevOps procesy napříč Azure v cloudu a Azure Stack v místním prostředí, abyste urychlili modernizaci aplikací pro základní klíčové aplikace.

Azure Stack tyto scénáře použití poskytují:

- Integrované prostředí pro doručování, které umožňuje rychle začít pracovat s účelově postavenými Azure Stack integrovanými systémy od důvěryhodných hardwarových partnerů. Po doručení se Azure Stack snadno integruje do datového centra s monitorováním prostřednictvím sady System Center Operations Manager Management Pack nebo rozšíření Nagios.

- Flexibilní správa identit pomocí Azure Active Directory (Azure AD) pro Azure a Azure Stack hybridní prostředí a využití Active Directory Federation Services (AD FS) (AD FS) pro odpojená nasazení. 

- Prostředí pro vývoj aplikací konzistentní s Azure pro maximalizaci produktivity vývojářů a umožňují běžné DevOps přístupy napříč hybridními prostředími.

- Doručování služeb Azure z místního prostředí s využitím hybridního cloudového výpočetního výkonu. Využijte běžné provozní postupy napříč Azure a Azure Stack k nasazení a provozování služeb Azure IaaS a PaaS pomocí stejných prostředí pro správu a nástrojů jako Azure. Microsoft zajišťuje nepřetržitou inovaci Azure na Azure Stack, včetně nových služeb Azure, aktualizací stávajících služeb a dalších Azure Marketplace aplikací a imagí.

## <a name="azure-stack-architecture"></a>Architektura Azure Stack
Azure Stack integrovaných systémů se skládají do racků 4-16 serverů vytvořených důvěryhodnými hardwarovými partnery a jsou přímo dodávané do vašeho datacentra. Po doručení bude poskytovatel řešení spolupracovat s vámi, abyste mohli nasadit integrovaný systém a zajistili, že Azure Stack řešení splňuje vaše obchodní požadavky. Vaše datacentrum budete muset připravit tím, že zajistíte, aby se zajistilo veškeré požadované napájení a chlazení, připojení k okraji a další požadavky na integraci Datacenter. 

> Další informace o prostředí Azure Stack Integration Datacenter najdete v tématu věnovaném [integraci Azure Stack Datacenter](azure-stack-customer-journey.md).

Azure Stack je postavená na standardním hardwaru a spravuje se pomocí stejných nástrojů, které už používáte pro správu předplatných Azure. V důsledku toho můžete použít konzistentní procesy DevOps bez ohledu na to, jestli jste připojení k Azure. 

Architektura Azure Stack umožňuje poskytovat služby Azure na hraničních zařízeních pro vzdálená umístění nebo přerušované připojení odpojené od Internetu. Můžete vytvářet hybridní řešení, která zpracovávají data místně v Azure Stack a pak je agregovat do Azure pro další zpracování a analýzu. A konečně vzhledem k tomu, že Azure Stack je nainstalovaná místně, můžete splnit konkrétní zákonné požadavky nebo požadavky zásad, a to díky flexibilitě nasazení cloudové aplikace do místního prostředí beze změny kódu. 

## <a name="deployment-options"></a>Možnosti nasazení

### <a name="production-or-evaluation-environments"></a>Produkční nebo vyhodnocovací prostředí
Azure Stack se nabízí ve dvou možnostech nasazení, které odpovídají vašim potřebám, Azure Stack integrovaných systémů pro použití v produkčním prostředí a Azure Stack Development Kit (ASDK) pro vyhodnocování Azure Stack:

- **Azure Stack integrovaných systémů**. Azure Stack integrované systémy jsou nabízeny prostřednictvím partnerství s Microsoftem a hardwarovými partnery a vytvářejí řešení, které nabízí cloudové inovace a jednoduchost správy computingu. Vzhledem k tomu, že Azure Stack je nabízená jako integrovaný hardwarový a softwarový systém, máte flexibilitu a kontrolu, které potřebujete, a také schopnost inovovat z cloudu. Azure Stack rozsahy integrovaných systémů je velikost z uzlů 4-16 a jsou společně podporovány hardwarovým partnerem a společností Microsoft. Pomocí Azure Stack integrovaných systémů můžete vytvářet nové scénáře a nasazovat nová řešení pro produkční úlohy.

- **Azure Stack Development Kit**. [Azure Stack Development Kit (ASDK)](../asdk/asdk-what-is.md) je bezplatné nasazení Azure Stack s jedním uzlem, které můžete použít k vyhodnocení a získání informací o Azure Stack. ASDK můžete použít také jako vývojářské prostředí k vytváření aplikací pomocí rozhraní API a nástrojů, které jsou konzistentní s Azure. ASDK ale nemá sloužit jako produkční prostředí a má v porovnání s úplnými provozními nasazeními integrovaných systémů následující omezení:

    - ASDK se dá přidružit jenom k jednomu zprostředkovateli identity Azure Active Directory (Azure AD) nebo Active Directory Federation Services (AD FS) (AD FS).
    - Vzhledem k tomu, že Azure Stack komponenty jsou nasazeny na jednom hostitelském počítači, jsou k dispozici omezené fyzické prostředky pro prostředky tenanta. Tato konfigurace není určena pro škálování nebo pro vyhodnocení výkonu.
    - Síťové scénáře jsou omezené z důvodu požadavků na nasazení s jedním hostitelem a síťovým ROZHRANÍm.

### <a name="connection-models"></a>Modely připojení
Můžete zvolit nasazení Azure Stack buď připojeného k Internetu (a k Azure), nebo z něj **Odpojit** . Tato volba definuje, jaké možnosti jsou k dispozici pro vaše úložiště identit (Azure AD nebo AD FS) a model fakturace (platíte při použití fakturace nebo fakturace na základě kapacity).

> Další informace najdete v tématu požadavky na [připojené](azure-stack-connected-deployment.md) a [odpojené](azure-stack-disconnected-deployment.md) modely nasazení. 

### <a name="identity-provider"></a>Poskytovatel identit 
Azure Stack používá k poskytnutí identit buď Azure Active Directory (Azure AD) nebo Active Directory Federation Services (AD FS) (AD FS). Azure AD je cloudový zprostředkovatel identity pro více tenantů od Microsoftu. Většina hybridních scénářů s nasazeními připojenými k Internetu používá jako úložiště identit službu Azure AD. 

Pro odpojená nasazení Azure Stack musíte použít Active Directory Federation Services (AD FS) (AD FS). Poskytovatelé prostředků Azure Stack a další aplikace fungují podobně jako AD FS nebo Azure AD. Azure Stack obsahuje svou vlastní instanci služby Active Directory a Graph API služby Active Directory.

> [!IMPORTANT]
> Po nasazení nemůžete změnit poskytovatele identity. Pokud chcete použít jiného zprostředkovatele identity, musíte Azure Stack znovu nasadit.

> Další informace o Azure Stack požadavcích na identitu najdete v článku [Přehled identity pro Azure Stack](azure-stack-identity-overview.md).

## <a name="how-is-azure-stack-managed"></a>Jak se Azure Stack spravuje?
Azure Stack můžete spravovat pomocí portálu pro správu, portálu User Portal nebo [PowerShellu](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.7.1). Portály Azure Stack jsou každou zálohované samostatné instance Azure Resource Manager. **Operátor Azure Stack** používá portál pro správu ke správě Azure Stack a k provádění akcí, jako je vytváření nabídek tenantů, a udržování stavu a monitorování stavu integrovaného systému. Portál User Portal (také označovaný jako portál tenanta) poskytuje samoobslužné prostředí pro využití cloudových prostředků, jako jsou virtuální počítače, účty úložiště a webové aplikace. 

> Další informace o správě Azure Stack pomocí portálu pro správu najdete v tématu [rychlý Start k portálu pro správu Azure Stack](azure-stack-manage-portals.md).

Jako operátor Azure Stack můžete doručovat širokou škálu služeb a aplikací, jako jsou [virtuální počítače](azure-stack-tutorial-tenant-vm.md), [webové aplikace](azure-stack-app-service-overview.md), vysoce dostupné [SQL Server](azure-stack-tutorial-sql.md)a databáze [MySQL serveru](azure-stack-tutorial-mysql.md) . K nasazení SharePointu, Exchange a dalších šablon můžete použít taky [Azure Stack rychlý start Azure Resource Manager šablony](https://github.com/Azure/AzureStack-QuickStart-Templates) . 

Pomocí portálu pro správu můžete [nakonfigurovat Azure Stack pro poskytování služeb](azure-stack-plan-offer-quota-overview.md) klientům pomocí plánů, kvót, nabídek a předplatných. Uživatelé tenanta se můžou přihlásit k odběru několika nabídek. Nabídky mohou mít jeden nebo více plánů a plány můžou mít jednu nebo více služeb. Operátoři také spravují kapacitu a reagují na výstrahy. 

Když je nakonfigurované Azure Stack, **Azure Stack uživatel** (označovaný také jako tenant) spotřebovává služby, které nabízí operátor. Uživatelé můžou zřizovat, monitorovat a spravovat služby, ke kterým se přihlásili, jako jsou webové aplikace, úložiště a virtuální počítače.

> Další informace o správě Azure Stack včetně toho, jaké účty se mají použít, kde jsou typické zodpovědnosti operátora, co říct vašim uživatelům a jak získat pomoc, najdete v [Azure Stack základy správy](azure-stack-manage-basics.md).

## <a name="resource-providers"></a>Poskytovatelé prostředků 
Poskytovatelé prostředků jsou webové služby, které tvoří základ pro všechny Azure Stack služby IaaS a PaaS. Azure Resource Manager spoléhá na různé poskytovatele prostředků, aby poskytoval přístup ke službám. Každý poskytovatel prostředků vám pomůže nakonfigurovat a řídit příslušné prostředky. Správci služeb můžou také přidávat nové vlastní poskytovatele prostředků. 

### <a name="foundational-resource-providers"></a>Zprostředkovatelé základních prostředků 
Existují tři poskytovatelé prostředků pro základní IaaS: 

- **Výpočetní**prostředí. Zprostředkovatel prostředků COMPUTE umožňuje Azure Stack klientům vytvářet vlastní virtuální počítače. Poskytovatel výpočetních prostředků zahrnuje možnost vytvářet virtuální počítače i rozšíření virtuálních počítačů. Služba rozšíření virtuálních počítačů pomáhá poskytovat funkce IaaS pro virtuální počítače s Windows a Linux. Jako příklad můžete pomocí zprostředkovatele prostředků služby COMPUTE zřídit virtuální počítač se systémem Linux a spustit skripty bash během nasazování a nakonfigurovat virtuální počítač.
- **Poskytovatel síťových prostředků**. Poskytovatel síťových prostředků nabízí řadu funkcí Software Defined Networking (SDN) a virtualizace síťových funkcí (NFV) pro privátní cloud. Poskytovatele síťových prostředků můžete použít k vytvoření prostředků, jako jsou nástroje pro vyrovnávání zatížení softwaru, veřejné IP adresy, skupiny zabezpečení sítě a virtuální sítě.
- **Poskytovatel prostředků úložiště**. Poskytovatel prostředků úložiště nabízí čtyři služby úložiště konzistentní s Azure: objekty [BLOB](https://docs.microsoft.com/azure/storage/common/storage-introduction#blob-storage), [Queue](https://docs.microsoft.com/azure/storage/common/storage-introduction#queue-storage), [Table](https://docs.microsoft.com/azure/storage/common/storage-introduction#table-storage)a [trezoru](https://docs.microsoft.com/azure/key-vault/) klíčů poskytující správu a auditování tajných kódů, jako jsou hesla a certifikáty. Poskytovatel prostředků úložiště nabízí cloudovou službu pro správu úložiště, která usnadňuje správu služeb úložiště konzistentních od Azure. Azure Storage poskytuje flexibilitu pro ukládání a načítání velkých objemů nestrukturovaných dat, jako jsou dokumenty a mediální soubory s objekty blob Azure a strukturovaná data založená na NoSQL, s tabulkami Azure. 

### <a name="optional-resource-providers"></a>Volitelní poskytovatelé prostředků
Existují tři volitelné poskytovatele prostředků PaaS, které můžete nasadit a používat s Azure Stack: 

- **App Service**. [Azure App Service v Azure Stack](azure-stack-app-service-overview.md) je nabídka typu platforma jako služba (PaaS) Microsoft Azure dostupná pro Azure Stack. Služba umožňuje vašim interním nebo externím zákazníkům vytvářet webové aplikace, rozhraní API a Azure Functions aplikací pro libovolnou platformu nebo zařízení. 
- **SQL Server**. Použijte [poskytovatele prostředků SQL Server](azure-stack-sql-resource-provider.md) k nabídnutí databází SQL jako služby Azure Stack. Až nainstalujete poskytovatele prostředků a připojíte ho k jedné nebo více instancím SQL Server, můžete vy a vaši uživatelé vytvářet databáze pro cloudové nativní aplikace, weby, které používají SQL, a další úlohy, které používají SQL.
- **MySQL Server**. K zveřejnění databází MySQL jako Azure Stack služby použijte [poskytovatele prostředků serveru MySQL](azure-stack-mysql-resource-provider-deploy.md) . Poskytovatel prostředků MySQL se spouští jako služba na virtuálním počítači se systémem Windows Server 2016 Server Core (VM).

## <a name="providing-high-availability"></a>Zajištění vysoké dostupnosti
Aby se dosáhlo vysoké dostupnosti produkčního systému více virtuálních počítačů v Azure, virtuální počítače se umístí do [skupiny dostupnosti](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#configure-multiple-virtual-machines-in-an-availability-set-for-redundancy) , která je rozšíří napříč více doménami selhání a aktualizačními doménami. V menším měřítku Azure Stack je doména selhání ve skupině dostupnosti definovaná jako jeden uzel v jednotce škálování.  

I když je infrastruktura Azure Stack už odolná vůči selháním, pak základní technologie (Clustering s podporou převzetí služeb při selhání) stále způsobí nějaké výpadky virtuálních počítačů na ovlivněném fyzickém serveru, pokud dojde k selhání hardwaru. Azure Stack podporuje pro zajištění konzistence s Azure skupinu dostupnosti s maximálně třemi doménami selhání.

- **Domény selhání**. Virtuální počítače, které jsou umístěné ve skupině dostupnosti, se fyzicky izolují od sebe navzájem, jak je rozšíříte do více domén selhání (Azure Stackch uzlů). Pokud dojde k selhání hardwaru, virtuální počítače z neúspěšné domény selhání budou restartovány v jiných doménách selhání, ale budou uloženy v samostatných doménách selhání z ostatních virtuálních počítačů ve stejné skupině dostupnosti, pokud je to možné. Když se hardware vrátí zpátky do online režimu, virtuální počítače se znovu vyrovnávají, aby se zachovala vysoká dostupnost. 
 
- **Aktualizujte domény**. Aktualizace domén představují jiný koncept Azure, který poskytuje vysokou dostupnost ve skupinách dostupnosti. Aktualizační doména je logická skupina základního hardwaru, která může prostoupit v rámci údržby. Virtuální počítače umístěné ve stejné aktualizační doméně se při plánované údržbě restartují společně. Když klienti vytvářejí virtuální počítače v rámci skupiny dostupnosti, platforma Azure automaticky distribuuje virtuální počítače napříč těmito aktualizačními doménami. V Azure Stack jsou virtuální počítače za provozu přenášeny v jiných online hostitelích v clusteru před tím, než se aktualizuje jejich podkladový hostitel. Vzhledem k tomu, že během aktualizace hostitele nedochází k výpadku tenanta, funkce aktualizační doména v Azure Stack existuje pouze pro kompatibilitu šablon s Azure. Virtuální počítače ve skupině dostupnosti budou na **portálu zobrazeny jako** číslo aktualizační domény. 

## <a name="role-based-access-control"></a>Řízení přístupu na základě role
Řízení přístupu na základě role (RBAC) můžete použít k udělení přístupu k systému autorizovaným uživatelům, skupinám a službám tím, že jim přiřadíte role v předplatném, skupině prostředků nebo jednotlivé úrovni prostředků. Každá role definuje úroveň přístupu, kterou uživatel, skupina nebo služba překročí Microsoft Azure Stack prostředky.

Azure Stack RBAC má tři základní role, které se vztahují na všechny typy prostředků: Vlastník, přispěvatel a čtenář. Vlastník má úplný přístup ke všem prostředkům, včetně práva k delegování přístupu jiným uživatelům. Přispěvatel může vytvářet a spravovat všechny typy prostředků Azure, ale nemůže udělovat přístup ostatním. Čtenář může zobrazit jenom existující prostředky. Zbývající role RBAC umožňují správu konkrétních prostředků Azure. Role Přispěvatel virtuálních počítačů například umožňuje vytváření a správu virtuálních počítačů, ale neumožňuje správu virtuální sítě ani podsítě, ke které se virtuální počítač připojuje.

> Další informace najdete v tématu [Správa řízení přístupu na základě rolí](azure-stack-manage-permissions.md) . 

## <a name="reporting-usage-data"></a>Vytváření sestav – data o využití
Azure Stack shromažďuje a agreguje data o využití napříč všemi poskytovateli prostředků a odesílá je do Azure ke zpracování prostřednictvím Azure Commerce. Data o využití shromážděná na Azure Stack lze zobrazit prostřednictvím REST API. Existuje rozhraní API tenanta konzistentní s Azure a také poskytovatelé a delegovaná rozhraní API pro získání dat o využití napříč všemi předplatnými tenanta. Tato data můžete použít k integraci s externím nástrojem nebo službou pro účely fakturace nebo vrácení peněz. Když Azure Commerce zpracuje využití, dá se zobrazit na fakturačním portálu Azure.

> Přečtěte si další informace o [vytváření sestav Azure Stack dat o využití do Azure](azure-stack-usage-reporting.md).

## <a name="next-steps"></a>Další kroky

[Porovnání Azure Stack a globální Azure](compare-azure-azure-stack.md)

[Základy správy](azure-stack-manage-basics.md)

[Rychlý Start: použití portálu pro správu Azure Stack](azure-stack-manage-portals.md)