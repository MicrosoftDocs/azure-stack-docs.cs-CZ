---
title: Přehled centra Azure Stack
description: Přehled toho, co je centrum Azure Stack a jak vám umožňuje spouštět služby Azure ve vašem datovém centru.
author: justinha
ms.topic: overview
ms.date: 11/08/2019
ms.author: justinha
ms.reviewer: unknown
ms.lastreviewed: 11/08/2019
ms.openlocfilehash: d6fa05eb64f749c3d1e2371840b9de1afb8cb7d9
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76881652"
---
# <a name="azure-stack-hub-overview"></a>Přehled centra Azure Stack

Centrum Azure Stack je rozšíření Azure, které poskytuje způsob, jak spouštět aplikace v místním prostředí a poskytovat služby Azure ve vašem datovém centru. S konzistentní cloudovou platformou můžou organizace bez obav dělat rozhodování o technologiích na základě podnikových požadavků, a ne podle obchodních rozhodnutí založených na omezeních technologie.

## <a name="why-use-azure-stack-hub"></a>Proč používat centrum Azure Stack?

Azure poskytuje vývojářům bohatou platformu pro vytváření moderních aplikací. Některé cloudové aplikace ale čelí překážkám, jako je latence, přerušované připojení a předpisy. Centrum Azure a Azure Stack odemknout nové případy použití hybridního cloudu pro zákazníky i interní obchodní aplikace:

- **Hraniční a odpojená řešení**. Požadavky na latenci a připojení k místnímu zpracování dat v Azure Stackovém centru a jejich agregaci v Azure pro další analýzy s využitím společné aplikační logiky napříč oběma. Můžete dokonce nasazovat Azure Stack hub odpojený od Internetu bez připojení k Azure. V příkladech si můžete představit podlahovou podlahu, lodě přepravní a odminování.

- **Cloudové aplikace, které splňují různé předpisy**. Vývoj a nasazování aplikací v Azure s plnou flexibilitou pro nasazení místně pomocí centra Azure Stack pro splnění zákonných požadavků nebo zásad. Nevyžadují se žádné změny kódu. Mezi příklady aplikací patří globální audit, finanční vykazování, obchodování s cizím systémem Exchange, hraní online her a generování sestav výdajů.

- **Model cloudové aplikace v místním**prostředí. Používejte služby, kontejnery, bez serveru a architektury mikroslužeb, abyste mohli aktualizovat a roztáhnout existující aplikace nebo vytvářet nové. Pomocí konzistentních DevOps procesů napříč Azure v cloudu a centra Azure Stack v místním prostředí Urychlete modernizaci aplikací pro základní klíčové aplikace.

## <a name="azure-stack-hub-architecture"></a>Architektura centra Azure Stack

Integrované systémy Azure Stack hub se skládají z racků 4-16 serverů vytvořených důvěryhodnými hardwarovými partnery a přímo do vašeho datacentra. Po doručení bude poskytovatel řešení spolupracovat s vámi, aby nasadil integrovaný systém a zajistil, že řešení Azure Stack hub splňuje vaše obchodní požadavky. Vaše datové centrum můžete připravit tak, že zajistíte, aby bylo zajištěné veškeré požadované napájení a chlazení, připojení k okraji a další požadované požadavky na integraci Datacenter.

> Další informace o prostředí integrace Datacenter centra Azure Stack najdete v tématu [integrace centrálního](azure-stack-customer-journey.md)centra pro Azure Stack.

Centrum Azure Stack je postavené na standardním hardwaru a spravuje se pomocí stejných nástrojů, které už používáte pro správu předplatných Azure. V důsledku toho můžete použít konzistentní procesy DevOps bez ohledu na to, jestli jste připojení k Azure.

Architektura centra Azure Stack umožňuje poskytovat služby Azure na hraničních zařízeních pro vzdálená umístění nebo přerušované připojení odpojené od Internetu. Můžete vytvářet hybridní řešení, která zpracovávají data místně v Azure Stackovém centru a pak je agregovat do Azure pro další zpracování a analýzu. Vzhledem k tomu, že centrum Azure Stack je nainstalované místně, můžete splnit konkrétní zákonné požadavky nebo požadavky zásad, a to díky flexibilitě nasazování cloudových aplikací v místním prostředí bez změny kódu.

## <a name="deployment-options"></a>Možnosti nasazení

Integrované systémy Azure Stack hub jsou nabízeny prostřednictvím partnerství s Microsoftem a hardwarovými partnery a vytvářejí řešení, které nabízí cloudové inovace a jednoduchost správy computingu. Vzhledem k tomu, že centrum Azure Stack se nabízí jako integrovaný hardware a softwarový systém, máte flexibilitu a kontrolu, které potřebujete, a také schopnost inovovat z cloudu. 

Integrovaný systém Azure Stackového centra může mít velikost v rozsahu od 4-16 serverů, které se nazývají *jednotka škálování*. Integrované systémy jsou společně podporovány hardwarovým partnerem a společností Microsoft. V následujícím diagramu vidíte příklad jednotky škálování. 

![Diagram znázorňující integrovaný systém Azure Stack hub](./media/azure-stack-overview/azure-stack-integrated-system.png). 

<!---add info and image on regions, etc--->

### <a name="connection-models"></a>Modely připojení

Můžete zvolit nasazení centra Azure Stack **připojeného** k Internetu (a do Azure) nebo z něj **Odpojit** . 

> Další informace najdete v tématu požadavky na [připojené](azure-stack-connected-deployment.md) a [odpojené](azure-stack-disconnected-deployment.md) modely nasazení.

### <a name="identity-provider"></a>Zprostředkovatel identity 

Azure Stack hub používá buď Azure Active Directory (Azure AD) nebo Active Directory Federation Services (AD FS) (AD FS). Azure AD je cloudový zprostředkovatel identity pro více tenantů od Microsoftu. Většina hybridních scénářů s nasazeními připojenými k Internetu používá jako úložiště identit službu Azure AD.

Pro odpojená nasazení centra Azure Stack musíte použít AD FS. Poskytovatelé prostředků Azure Stack hub a další aplikace fungují podobně jako AD FS nebo Azure AD. Centrum Azure Stack zahrnuje svou vlastní instanci služby Active Directory a Graph API služby Active Directory.


## <a name="how-is-azure-stack-hub-managed"></a>Jak se spravuje centrum Azure Stack?

Azure Stack hub používá stejný model operací jako Azure. Operátor centra Azure Stack může doručovat nejrůznější služby a aplikace uživatelům klientů, podobně jako Microsoft poskytuje služby Azure pro uživatele klientů. 

![Diagram znázorňující Azure Stack role úloh centra](./media/azure-stack-overview/azure-stack-job-roles.png)

Centrum Azure Stack můžete spravovat pomocí portálu pro správu, portálu User Portal nebo [PowerShellu](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.7.1). Portály centra Azure Stack jsou každou zálohované samostatnými instancemi Azure Resource Manager. **Operátor centra Azure Stack** využívá portál pro správu ke správě centra Azure Stack a k provádění akcí, jako je vytváření nabídek tenantů, a udržování stavu a monitorování stavu integrovaného systému. Portál User Portal poskytuje samoobslužné prostředí pro využívání cloudových prostředků, jako jsou virtuální počítače, účty úložiště a webové aplikace.

> Další informace o správě centra Azure Stack pomocí portálu pro správu najdete v tématu [rychlý Start k portálu pro správu centra Azure Stack](azure-stack-manage-portals.md).

Jako operátor centra Azure Stack můžete doručovat [virtuální počítače](azure-stack-tutorial-tenant-vm.md), [webové aplikace](azure-stack-app-service-overview.md), vysoce dostupné databáze [SQL Server](azure-stack-tutorial-sql.md)a [MySQL serveru](azure-stack-tutorial-mysql.md) . K nasazení SharePointu, Exchange a dalších šablon můžete také použít [rychlý Start centra Azure Stack Azure Resource Manager](https://github.com/Azure/AzureStack-QuickStart-Templates) .

Operátor může spravovat Azure Stack hub pomocí portálu pro [správu](azure-stack-manage-portals.md) nebo [PowerShellu](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.7.1). Centrum Azure Stack můžete nakonfigurovat tak, aby [poskytovala služby](service-plan-offer-subscription-overview.md) klientům pomocí plánů, kvót, nabídek a předplatných. Uživatelé tenanta se můžou přihlásit k odběru několika nabídek. Nabídky mohou mít jeden nebo více plánů a plány můžou mít jednu nebo více služeb. Operátoři také spravují kapacitu a reagují na výstrahy.

Uživatelé využívají služby, které nabízí operátor. Uživatelé můžou zřizovat, monitorovat a spravovat služby, ke kterým se přihlásili, jako jsou webové aplikace, úložiště a virtuální počítače. Uživatelé můžou spravovat centrum Azure Stack pomocí portálu User Portal nebo PowerShellu.

> Další informace o správě centra Azure Stack, včetně toho, jaké účty se mají použít, kde jsou typické zodpovědnosti operátora, co říct vašim uživatelům a jak získat pomoc, najdete v [Azure Stack základy správy centra](azure-stack-manage-basics.md).

## <a name="resource-providers"></a>Poskytovatelé prostředků

Poskytovatelé prostředků jsou webové služby, které tvoří základ pro všechny služby centra Azure Stack IaaS a PaaS. Azure Resource Manager spoléhá na různé poskytovatele prostředků, aby poskytoval přístup ke službám. Každý poskytovatel prostředků vám pomůže nakonfigurovat a řídit příslušné prostředky. Správci služeb můžou také přidávat nové vlastní poskytovatele prostředků.

### <a name="foundational-resource-providers"></a>Zprostředkovatelé základních prostředků

Existují tři poskytovatelé prostředků pro základní IaaS:

- **COMPUTE**: poskytovatel výpočetních prostředků umožňuje Azure Stack klientům centra vytvářet vlastní virtuální počítače. Poskytovatel výpočetních prostředků zahrnuje možnost vytvářet virtuální počítače i rozšíření virtuálních počítačů. Služba rozšíření virtuálních počítačů pomáhá poskytovat funkce IaaS pro virtuální počítače s Windows a Linux. Jako příklad můžete pomocí zprostředkovatele prostředků služby COMPUTE zřídit virtuální počítač pro Linux a spustit skripty bash během nasazování a nakonfigurovat virtuální počítač.
- **Poskytovatel síťových prostředků**: poskytovatel síťových prostředků nabízí řadu funkcí Software Defined Networking (SDN) a virtualizace síťových funkcí (NFV) pro privátní cloud. Poskytovatele síťových prostředků můžete použít k vytvoření prostředků, jako jsou nástroje pro vyrovnávání zatížení softwaru, veřejné IP adresy, skupiny zabezpečení sítě a virtuální sítě.
- **Poskytovatel prostředků úložiště**: poskytovatel prostředků úložiště nabízí čtyři služby úložiště konzistentní s Azure: objekty [BLOB](https://docs.microsoft.com/azure/storage/common/storage-introduction#blob-storage), [front](https://docs.microsoft.com/azure/storage/common/storage-introduction#queue-storage), [tabulek](https://docs.microsoft.com/azure/storage/common/storage-introduction#table-storage)a [Key Vault](https://docs.microsoft.com/azure/key-vault/) poskytující správu a auditování tajných kódů, jako jsou hesla a certifikáty. Poskytovatel prostředků úložiště nabízí cloudovou službu pro správu úložiště, která usnadňuje správu služeb úložiště konzistentních od Azure. Azure Storage poskytuje flexibilitu pro ukládání a načítání velkých objemů nestrukturovaných dat, jako jsou dokumenty a mediální soubory s objekty blob Azure a strukturovaná data založená na NoSQL s tabulkami Azure.

### <a name="optional-resource-providers"></a>Volitelní poskytovatelé prostředků

Existují tři Volitelní poskytovatelé prostředků PaaS, které můžete nasadit a používat s Azure Stack hub:

- **App Service**: [Azure App Service na rozbočovači Azure Stack](azure-stack-app-service-overview.md) je nabídka PaaS Microsoft Azure dostupná pro Azure Stack centra. Služba umožňuje vašim interním nebo externím zákazníkům vytvářet webové aplikace, rozhraní API a Azure Functions aplikací pro libovolnou platformu nebo zařízení.
- **SQL Server**: k poskytování databází SQL jako služby centra Azure Stack použijte [poskytovatele prostředků SQL Server](azure-stack-sql-resource-provider.md) . Až nainstalujete poskytovatele prostředků a připojíte ho k jedné nebo více instancím SQL Server, můžete vy a vaši uživatelé vytvářet databáze pro cloudové nativní aplikace, weby, které používají SQL, a další úlohy, které používají SQL.
- **Server MySQL**: k zveřejnění databází MySQL jako služby centra Azure Stack použijte [poskytovatele prostředků serveru MySQL](azure-stack-mysql-resource-provider-deploy.md) . Poskytovatel prostředků MySQL se spouští jako služba na virtuálním počítači jádra serveru Windows Server 2019.

## <a name="next-steps"></a>Další kroky

[Porovnat portfolio centra Azure Stack](compare-azure-azure-stack.md)

[Základy správy](azure-stack-manage-basics.md)

[Rychlý Start: použití portálu pro správu centra Azure Stack](azure-stack-manage-portals.md)

[Pochopení využití a fakturace](azure-stack-usage-reporting.md)
