---
title: N-vrstvá aplikace Windows v centru Azure Stack s SQL Server
description: Naučte se spouštět N-vrstvou aplikaci Windows v Azure Stackovém centru s SQL Server.
author: mattbriggs
ms.topic: how-to
ms.date: 2/1/2021
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 11/01/2019
ms.openlocfilehash: 5af32be701617f7ff357d4776557b2edbbdee729
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99247485"
---
# <a name="windows-n-tier-application-on-azure-stack-hub-with-sql-server"></a>N-vrstvá aplikace Windows v centru Azure Stack s SQL Server

Tato referenční architektura ukazuje, jak nasadit virtuální počítače a virtuální síť nakonfigurovanou pro [N-vrstvou](/azure/architecture/guide/architecture-styles/n-tier) aplikaci pomocí SQL Server ve Windows pro datovou vrstvu. 

## <a name="architecture"></a>Architektura

Tato architektura se skládá z následujících součástí.

![Diagram zobrazuje virtuální síť skládající se ze šesti podsítí: Application Gateway, Správa, webová vrstva, obchodní vrstva, Datová vrstva a služba Active Directory. Podsíť datové vrstvy používá disk s kopií cloudu. Existují tři nástroje pro vyrovnávání zatížení.](./media/iaas-architecture-windows-sql-n-tier/image1.png)

## <a name="general"></a>Obecné

-   **Skupina prostředků**. [Skupiny prostředků](/azure/azure-resource-manager/resource-group-overview) se používají k seskupení prostředků Azure, aby je bylo možné spravovat podle doby životnosti, vlastníka a dalších kritérií.

-   **Skupina dostupnosti.** Skupina dostupnosti je konfigurace datového centra pro zajištění redundance a dostupnosti virtuálních počítačů. Tato konfigurace v rámci razítka centra Azure Stack zajišťuje, aby během plánované nebo neplánované události údržby byl k dispozici alespoň jeden virtuální počítač. Virtuální počítače jsou umístěné ve skupině dostupnosti, která je rozšíří napříč několika doménami selhání (Azure Stack hostitelé centra).

## <a name="networking-and-load-balancing"></a>Sítě a vyrovnávání zatížení

-   **Virtuální síť a podsítě**. Každý virtuální počítač Azure je nasazený do virtuální sítě, která se dá rozdělit do podsítí. Vytvořte pro každou vrstvu samostatnou podsíť.

-   **Load Balancer vrstvy 7.** Protože Application Gateway ještě není v centru Azure Stack k dispozici, jsou k dispozici alternativy na [trhu centra Azure Stack](../operator/azure-stack-marketplace-azure-items.md) , jako je: [kemp LoadMaster Load Balancer ADC Content Switch](https://azuremarketplace.microsoft.com/marketplace/apps/kemptech.vlm-azure) /  [F5 Big-IP Virtual Edition](https://azuremarketplace.microsoft.com/marketplace/apps/f5-networks.f5-big-ip-best) nebo [A10 vThunder ADC](https://azuremarketplace.microsoft.com/marketplace/apps/a10networks.vthunder-414-gr1)

-   Nástroje pro **Vyrovnávání zatížení**. Použijte [Azure Load Balancer ](/azure/load-balancer/load-balancer-overview)k distribuci síťového provozu z webové vrstvy do obchodní vrstvy a z obchodní vrstvy na SQL Server.

-   **Skupiny zabezpečení sítě** (skupin zabezpečení sítě). Použijte skupin zabezpečení sítě k omezení síťového provozu v rámci virtuální sítě. Například v níže uvedené architektuře architektury nepřijímá databázová vrstva přenos z webového front-endu, a to pouze z obchodní vrstvy a z podsítě pro správu.

-   **DNS**. Centrum Azure Stack neposkytuje vlastní hostitelskou službu DNS, proto prosím použijte server DNS ve vašem prostředí přidat.

**Virtual Machines**

-   **Skupina dostupnosti Always On SQL Server**. Umožňuje replikaci a převzetí služeb při selhání, čímž poskytuje datové vrstvě vysokou dostupnost. Používá technologii Windows Server failover cluster (WSFC) pro převzetí služeb při selhání.

-   **Servery služby Active Directory Domain Services (AD DS)**. V Active Directory Domain Services (služba AD DS) se vytvoří objekty počítače pro cluster s podporou převzetí služeb při selhání a přidružené clusterové role. Nastavení služba AD DS serverů na virtuálních počítačích ve stejné virtuální síti jsou upřednostňovanou metodou pro připojení jiných virtuálních počítačů k služba AD DS. Virtuální počítače můžete připojit taky k existujícím podnikovým služba AD DS připojením k podnikové síti pomocí připojení VPN. V obou případech je potřeba změnit DNS virtuální sítě na váš služba AD DS server DNS (ve virtuální síti nebo ve stávající podnikové síti) a vyřešit služba AD DS plně kvalifikovaný název domény.

-   **Disk s kopií cloudu** Cluster s podporou převzetí služeb při selhání vyžaduje více než polovinu uzlů, které mají být spuštěny, což se označuje jako kvorum. Pokud má cluster pouze dva uzly, může síťový oddíl způsobit, že se každý uzel bude domnívat, že je hlavním uzlem. V takovém případě potřebujete *určující* , aby bylo možné rušit vazby a vytvořit kvorum. Určující disk je prostředek, jako je například sdílený disk, který může při navazování kvora fungovat jako akce pro svázání. Disk s kopií cloudu je typ určující sdílené složky, který používá Azure Blob Storage. Další informace o konceptu kvora najdete v tématu [Principy kvora clusteru a fondu](/windows-server/storage/storage-spaces/understand-quorum). Další informace o určujícím cloudu najdete v tématu [nasazení určujícího cloudu pro cluster s podporou převzetí služeb při selhání](/windows-server/failover-clustering/deploy-cloud-witness). V Azure Stack hub se koncový bod cloudu s kopií cloudu liší od globálního Azure. 

Může vypadat takto:

- Pro globální Azure:  
  `https://mywitness.blob.core.windows.net/`

- Pro centrum Azure Stack:  
  `https://mywitness.blob.<region>.<FQDN>`

-   **Jumpbox**. Označuje se také jako [bastion host](https://en.wikipedia.org/wiki/Bastion_host) (ochranná bašta). Je to zabezpečený virtuální počítač v síti, který správci používají pro připojení k jiným virtuálním počítačům. Jumpbox má skupinu NSG, která umožňuje vzdálenou komunikaci pouze z jedné veřejné IP adresy na seznamu bezpečných adres. NSG musí povolit provoz vzdálené plochy (RDP).

## <a name="recommendations"></a>Doporučení

Vaše požadavky se mohou od popsané architektury lišit. Použijte tato doporučení jako výchozí bod.

### <a name="virtual-machines"></a>Virtuální počítače

Doporučení týkající se konfigurace virtuálních počítačů najdete v tématu [spuštění virtuálního počítače s Windows v centru Azure Stack](iaas-architecture-vm-windows.md).

### <a name="virtual-network"></a>Virtuální síť

Když vytváříte virtuální síť, určete, kolik IP adres vaše prostředky v každé podsíti vyžadují. Zadejte masku podsítě a dostatečně velký rozsah síťových adres pro požadované IP adresy pomocí zápisu [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) . Použijte adresní prostor, který spadá do standardních [bloků privátních IP adres](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces), kterými jsou 10.0.0.0/8, 172.16.0.0/12 a 192.168.0.0/16.

Vyberte rozsah adres, který se nepřekrývá s vaší místní sítí, pro případ, že budete potřebovat nastavit bránu mezi virtuální sítí a místní sítí později. Po vytvoření virtuální sítě nemůžete změnit rozsah adres.

Při navrhování podsítí myslete na požadované funkce a požadavky na zabezpečení. Všechny virtuální počítače ve stejné vrstvě nebo roli by měly patřit do stejné podsítě, která může být hranicí zabezpečení. Další informace o návrhu virtuálních sítí a podsítí najdete v tématu [plánování a návrh Azure Virtual Networks](/azure/virtual-network/virtual-network-vnet-plan-design-arm).

### <a name="load-balancers"></a>Nástroje pro vyrovnávání zatížení

Nevystavujte virtuální počítače přímo na internetu, ale místo toho každému virtuálnímu počítači poskytněte privátní IP adresu. Klienti se připojují pomocí veřejné IP adresy přidružené k Load Balancer vrstvy 7.

Definujte pravidla nástroje pro vyrovnávání zatížení, aby síťový provoz směroval na virtuální počítače. Pokud třeba chcete povolit přenosy HTTP, namapujte port 80 z front-endové konfigurace na port 80 ve fondu back-end adres. Když klient odešle žádost HTTP na port 80, nástroj pro vyrovnávání zatížení vybere back-endovou IP adresu použitím [algoritmu hash](/azure/load-balancer/concepts#limitations), který obsahuje zdrojovou IP adresu. Požadavky klientů jsou distribuovány napříč všemi virtuálními počítači ve fondu back-end adres.

### <a name="network-security-groups"></a>Skupiny zabezpečení sítě

Použijte pravidla skupiny zabezpečení sítě, abyste omezili provoz mezi vrstvami. Ve výše uvedené architektuře architektury nekomunikuje webová vrstva přímo s databázovou vrstvou. Pro vymáhání tohoto pravidla by měla databázová vrstva blokovat příchozí provoz z podsítě webové vrstvy.

1.  Zakazuje veškerý příchozí provoz z virtuální sítě. (Použijte značku VIRTUAL_NETWORK v pravidle.)

2.  Povolí příchozí provoz z podsítě obchodní vrstvy.

3.  Povolí příchozí provoz z vlastní podsítě databázové vrstvy. Toto pravidlo umožňuje komunikaci mezi virtuálními počítači databáze, které jsou potřeba pro replikaci a převzetí služeb při selhání databáze.

4.  Povolte provoz protokolu RDP (port 3389) z podsítě JumpBox. Toto pravidlo umožňuje správcům připojit se z jumpboxu k databázové vrstvě.

Vytvořte pravidla 2 – 4 s vyšší prioritou, než má první pravidlo, takže se přepíší.

## <a name="sql-server-always-on-availability-groups"></a>Další informace o skupinách dostupnosti AlwaysOn pro SQL Server

Pro vysoce dostupný SQL Server vám doporučujeme použít [skupiny dostupnosti AlwaysOn](/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server?view=sql-server-ver15&preserve-view=true). Skupiny dostupnosti AlwaysOn vyžadují před Windows Serverem 2016 řadič domény a všechny uzly ve skupině dostupnosti musí být ve stejné doméně AD.

Pro vysokou dostupnost vrstvy virtuálního počítače musí být všechny virtuální počítače SQL ve skupině dostupnosti.

Další vrstvy se k databázi připojí prostřednictvím [naslouchacího procesu skupiny dostupnosti](/sql/database-engine/availability-groups/windows/listeners-client-connectivity-application-failover?view=sql-server-ver15&preserve-view=true). Naslouchací proces umožňuje klientovi SQL připojit se bez znalosti názvu fyzické instance SQL Serveru. Virtuální počítače, které přistupují k databázi, musí být připojené k doméně. Klient (v tomto případě jiná vrstva) používá DNS, aby přeložil název virtuální sítě naslouchacího procesu do IP adres.

Skupiny dostupnosti AlwaysOn pro SQL Server nakonfigurujte následovně:

1.  Vytvořte cluster s podporou převzetí služeb při selhání Windows Serveru (WSFC), skupinu dostupnosti AlwaysOn pro SQL Server a primární repliku. Další informace najdete v článku [Začínáme se skupinami dostupnosti Always On](/sql/database-engine/availability-groups/windows/getting-started-with-always-on-availability-groups-sql-server?view=sql-server-ver15&preserve-view=true).

2.  Vytvořte interní nástroj pro vyrovnávání zatížení se statickou privátní IP adresou.

3.  Vytvořte naslouchací proces skupiny dostupnosti a namapujte název DNS naslouchacího procesu na IP adresu interního nástroje pro vyrovnávání zatížení.

4.  Pro port SQL Serveru pro naslouchání (ve výchozím nastavení je to port 1433 protokolu TCP) vytvořte pravidlo nástroje pro vyrovnávání zatížení. Pravidlo nástroje pro vyrovnávání zatížení musí umožňovat *plovoucí IP adresu* (jinak známou jako přímou odpověď ze serveru). Díky tomu bude virtuální počítač odpovídat přímo klientovi, což umožňuje přímé připojení k primární replice.

> [!NOTE]
> Když je plovoucí adresa povolená, front-endové číslo portu musí být stejné jako back-endové číslo portu v pravidle nástroje pro vyrovnávání zatížení.

Když se klient SQL pokusí připojit, nástroj pro vyrovnávání zatížení bude směrovat žádosti na připojení na primární repliku. Pokud dojde k převzetí služeb při selhání jiné repliky, nástroj pro vyrovnávání zatížení automaticky směruje nové požadavky na novou primární repliku. Více informací získáte v tématu [Konfigurace naslouchacího procesu ILB pro skupiny dostupnosti AlwaysOn systému SQL Server](/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener).

Během převzetí služeb při selhání jsou existující připojení klienta uzavřená. Po dokončení převzetí služeb při selhání se budou nová připojení směrovat na novou primární repliku.

Pokud vaše aplikace poskytuje více operací čtení než zápisů, můžete přesměrovat některé dotazy jen pro čtení na sekundární repliku. Podívejte se na článek o [použití naslouchacího procesu pro připojení k sekundární replice, která je jen pro čtení (směrování jen pro čtení)](/sql/database-engine/availability-groups/windows/listeners-client-connectivity-application-failover?view=sql-server-ver15&preserve-view=true#ConnectToSecondary).

Otestujte své nasazení [vynucením ručního převzetí služeb při selhání](/sql/database-engine/availability-groups/windows/perform-a-forced-manual-failover-of-an-availability-group-sql-server?view=sql-server-ver15&preserve-view=true) skupiny dostupnosti.

V případě optimalizace výkonu SQL můžete také informace o [osvědčených postupech pro SQL Server, které optimalizují výkon v centru Azure Stack](./azure-stack-sql-server-vm-considerations.md).

**JumpBox**

Nepovolujte přístup RDP z veřejného Internetu k virtuálním počítačům, které spouštějí úlohu aplikace. Místo toho by se měl veškerý přístup k těmto virtuálním počítačům přes protokol RDP procházet přes JumpBox. Správce se přihlásí do jumpboxu a potom se z jumpboxu přihlásí k jinému virtuálnímu počítači. Jumpbox provoz protokolu RDP z internetu umožňuje, ale pouze ze známých a bezpečných IP adres.

JumpBox má minimální požadavky na výkon, takže vyberte malou velikost virtuálního počítače. Vytvořte jumpboxu [veřejné IP adresy](/azure/virtual-network/virtual-network-ip-addresses-overview-arm). JumpBox umístěte do stejné virtuální sítě jako ostatní virtuální počítače, ale v samostatné podsíti pro správu.

K zabezpečení JumpBox přidejte pravidlo NSG, které umožňuje připojení RDP jenom z bezpečné sady veřejných IP adres. Nakonfigurujte skupiny zabezpečení sítě pro ostatní podsítě, aby přijímaly provoz protokolu RDP z podsítě pro správu.

## <a name="scalability-considerations"></a>Aspekty zabezpečení

### <a name="scale-sets"></a>Škálovací sady

Pro webové a obchodní vrstvy zvažte použití [sady škálování virtuálních počítačů](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview) místo nasazení samostatných virtuálních počítačů. Sada škálování usnadňuje nasazení a správu sady identických virtuálních počítačů. Pokud potřebujete rychle škálovat virtuální počítače, zvažte nastavení škálování.

Existují dva základní způsoby, jak virtuální počítače nasazené ve škálovací sadě nakonfigurovat:

-   Pomocí rozšíření nakonfigurujte virtuální počítač po jeho nasazení. U tohoto přístupu je možné, že se nové instance virtuálních počítačů budou načítat déle než u virtuálních počítačů bez rozšíření.

-   Nasaďte [spravovaný disk](./azure-stack-managed-disk-considerations.md) s vlastní imagí disku. Nasazení této možnosti může být rychlejší, Ale vyžaduje, abyste si image zachovali v aktuálním stavu.

Další informace najdete v tématu věnovaném [hlediskům návrhu pro sady škálování](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview). Toto posouzení návrhu je pro Azure Stack centrum většinou pravdivé, ale existují nějaká upozornění:

-   Sada škálování virtuálních počítačů na rozbočovači Azure Stack nepodporuje převracení ani postupné inovace.

-   Na Azure Stackovém centru nemůžete automatické škálování virtuálních počítačů škálovat.

-   Důrazně doporučujeme používat na Azure Stack hub místo nespravovaných disků pro sadu škálování virtuálního počítače spravované disky.

-   V současné době existuje limit 700 virtuálních počítačů v Azure Stackovém centru, které účty pro všechny virtuální počítače infrastruktury centra Azure Stack, jednotlivé virtuální počítače a instance sady škálování.

## <a name="subscription-limits"></a>Omezení předplatného

Každé předplatné tenanta Azure Stackho centra má nastavené výchozí limity, včetně maximálního počtu virtuálních počítačů na oblast nakonfigurované pomocí operátoru centra Azure Stack. Další informace najdete v tématu [Azure Stack služby centra, plány, nabídky a předplatné přehled](../operator/service-plan-offer-subscription-overview.md). Také se můžete podívat na [typy kvót v centru Azure Stack](../operator/azure-stack-quota-types.md).

## <a name="security-considerations"></a>Důležité informace o zabezpečení

Virtuální sítě jsou hranicí izolace provozu v Azure. Virtuální počítače v jedné virtuální síti ve výchozím nastavení nemůžou komunikovat přímo s virtuálními počítači v jiné virtuální síti.

**Skupin zabezpečení sítě**. Pomocí [skupin zabezpečení sítě](/azure/virtual-network/virtual-networks-nsg) (skupin zabezpečení sítě) můžete omezit provoz na Internet a z něj. Další informace získáte v tématu [Zabezpečení sítí a cloudových služeb Microsoftu](/azure/best-practices-network-security).

**DMZ**. Zvažte přidání síťového virtuálního zařízení, abyste mezi internetem a virtuální sítí Azure vytvořili síť DMZ. Síťové virtuální zařízení je obecný termín pro virtuální zařízení, které provádí úlohy související se sítí, jako je brána firewall, kontrola paketu, auditování a vlastní směrování.

**Šifrování**. Zašifrujte citlivá data v klidovém umístění a pomocí [Key Vault v centru Azure Stack](./azure-stack-key-vault-manage-portal.md) spravujte šifrovací klíče databáze. Další informace najdete v tématu [Konfigurace Integrace se službou Azure Key Vault pro virtuální počítače Azure](/azure/azure-sql/virtual-machines/windows/azure-key-vault-integration-configure). Doporučuje se také ukládat tajné klíče aplikace, jako jsou databázové připojovací řetězce, v Key Vault.

## <a name="next-steps"></a>Další kroky

- Další informace o vzorech cloudu Azure najdete v tématu [vzory návrhu cloudu](/azure/architecture/patterns).
