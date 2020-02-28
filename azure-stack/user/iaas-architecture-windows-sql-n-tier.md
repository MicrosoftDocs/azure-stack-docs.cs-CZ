---
title: N-vrstvá aplikace Windows v centru Azure Stack s SQL Server
description: Naučte se spouštět N-vrstvou aplikaci Windows v Azure Stackovém centru s SQL Server.
author: mattbriggs
ms.topic: how-to
ms.date: 11/01/2019
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 11/01/2019
ms.openlocfilehash: a44c4cee7948fe9f2cf4a55e7ed337c3932fd95c
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77704943"
---
# <a name="windows-n-tier-application-on-azure-stack-hub-with-sql-server"></a>N-vrstvá aplikace Windows v centru Azure Stack s SQL Server

Tato referenční architektura ukazuje, jak nasadit virtuální počítače a virtuální síť nakonfigurovanou pro [N-vrstvou](https://docs.microsoft.com/azure/architecture/guide/architecture-styles/n-tier) aplikaci pomocí SQL Server ve Windows pro datovou vrstvu. 

## <a name="architecture"></a>Architektura

Tato architektura se skládá z následujících součástí.

![](./media/iaas-architecture-windows-sql-n-tier/image1.png)

## <a name="general"></a>Obecné

-   **Skupina prostředků**. [Skupiny prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) se používají k seskupení prostředků Azure, aby je bylo možné spravovat podle doby životnosti, vlastníka a dalších kritérií.

-   **Skupina dostupnosti.** Skupina dostupnosti je konfigurace datového centra pro zajištění redundance a dostupnosti virtuálních počítačů. Tato konfigurace v rámci razítka centra Azure Stack zajišťuje, aby během plánované nebo neplánované události údržby byl k dispozici alespoň jeden virtuální počítač. Virtuální počítače jsou umístěné ve skupině dostupnosti, která je rozšíří napříč několika doménami selhání (Azure Stack hostitelé centra).

## <a name="networking-and-load-balancing"></a>Sítě a vyrovnávání zatížení

-   **Virtuální síť a podsítě**. Každý virtuální počítač Azure je nasazený do virtuální sítě, která se dá rozdělit do podsítí. Vytvořte pro každou vrstvu samostatnou podsíť.

-   **Load Balancer vrstvy 7.** Protože Application Gateway ještě není v centru Azure Stack k dispozici, jsou k dispozici alternativy na [trhu centra Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-marketplace-azure-items?view=azs-1908) , jako je: [kemp LoadMaster Load Balancer ADC Content Switch](https://azuremarketplace.microsoft.com/marketplace/apps/kemptech.vlm-azure)/ [F5 Big-IP Virtual Edition](https://azuremarketplace.microsoft.com/marketplace/apps/f5-networks.f5-big-ip-best) nebo [A10 vThunder ADC](https://azuremarketplace.microsoft.com/marketplace/apps/a10networks.vthunder-414-gr1)

-   **Nástroje pro vyrovnávání zatížení.** Použijte [Azure Load Balancer ](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)k distribuci síťového provozu z webové vrstvy do obchodní vrstvy a z obchodní vrstvy na SQL Server.

-   **Skupiny zabezpečení sítě** (skupin zabezpečení sítě). Použijte skupin zabezpečení sítě k omezení síťového provozu v rámci virtuální sítě. Například v třívrstvé architektuře zobrazené na úrovni databáze nepřijímá provoz z webového front-endu, pouze z obchodní vrstvy a podsítě pro správu.

-   **Služba DNS**. Centrum Azure Stack neposkytuje vlastní hostitelskou službu DNS, proto prosím použijte server DNS ve vašem prostředí přidat.

**Virtual Machines**

-   **Skupina dostupnosti AlwaysOn systému SQL Server**. Umožňuje replikaci a převzetí služeb při selhání, čímž poskytuje datové vrstvě vysokou dostupnost. Využívá technologie systému Windows Server Failover Cluster (WSFC) pro převzetí služeb při selhání.

-   **Servery služby Active Directory Domain Services (AD DS)** . Objekty počítače pro cluster převzetí služeb při selhání a přidružené Clusterové role se vytvoří ve službě Active Directory Domain Services (AD DS). Nastavení služba AD DS serverů na virtuálních počítačích ve stejné virtuální síti jsou upřednostňovanou metodou pro připojení jiných virtuálních počítačů k služba AD DS. Virtuální počítače můžete připojit taky k existujícím podnikovým služba AD DS připojením k podnikové síti pomocí připojení VPN. V obou případech je potřeba změnit DNS virtuální sítě na váš služba AD DS server DNS (ve virtuální síti nebo ve stávající podnikové síti) a vyřešit služba AD DS plně kvalifikovaný název domény.

-   **Disk s kopií cloudu** Cluster převzetí služeb při selhání vyžaduje více než polovina jeho uzly ke spuštění, které se označuje jako s kvora. Pokud cluster má jenom dva uzly, síťového oddílu může způsobit, že každému uzlu myslíte, že je hlavní uzel. V takovém případě potřebujete *určující* , aby bylo možné rušit vazby a vytvořit kvorum. Určujícího je prostředek, jako je sdílený disk, který může sloužit jako zkrácené stanovit kvorum. Disk s kopií cloudu je typ určující sdílené složky, která využívá úložiště objektů Blob Azure. Další informace o konceptu kvora najdete v tématu [Principy kvora clusteru a fondu](https://docs.microsoft.com/windows-server/storage/storage-spaces/understand-quorum). Další informace o určujícím cloudu najdete v tématu [nasazení určujícího cloudu pro cluster s podporou převzetí služeb při selhání](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness). V Azure Stack hub se koncový bod cloudu s kopií cloudu liší od globálního Azure. 

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

Když vytváříte virtuální síť, určete, kolik IP adres vaše prostředky v každé podsíti vyžadují. Zadejte masku podsítě a dostatečně velký rozsah síťových adres pro požadované IP adresy pomocí zápisu [CIDR](https://en.wikipedia.org/wiki/Classless_Inter-Domain_Routing) . Použijte adresní prostor, který spadá do standardních [bloků privátních IP adres](https://en.wikipedia.org/wiki/Private_network#Private_IPv4_address_spaces), které jsou 10.0.0.0/8, 172.16.0.0/12 a 192.168.0.0/16.

Vyberte rozsah adres, který se nepřekrývá s vaší místní sítí, pro případ, že budete potřebovat nastavit bránu mezi virtuální sítí a místní sítí později. Po vytvoření virtuální sítě nemůžete změnit rozsah adres.

Při navrhování podsítí myslete na požadované funkce a požadavky na zabezpečení. Všechny virtuální počítače ve stejné vrstvě nebo roli by měly patřit do stejné podsítě, která může být hranicí zabezpečení. Další informace o návrhu virtuálních sítí a podsítí najdete v tématu [plánování a návrh Azure Virtual Networks](https://docs.microsoft.com/azure/virtual-network/virtual-network-vnet-plan-design-arm).

### <a name="load-balancers"></a>Služby vyrovnávání zatížení

Nevystavujte virtuální počítače přímo k Internetu, ale místo toho dejte každému virtuálnímu počítači privátní IP adresu. Klienti se připojují pomocí veřejné IP adresy přidružené k Load Balancer vrstvy 7.

Definujte pravidla nástroje pro vyrovnávání zatížení, aby síťový provoz směroval na virtuální počítače. Například pokud chcete povolit provoz protokolu HTTP, namapujte port 80 z front-endové konfigurace k portu 80 ve fondu back endových adres. Když klient odešle požadavek HTTP na port 80, nástroj pro vyrovnávání zatížení vybere back-end IP adresu pomocí [algoritmu hash](/azure/load-balancer/concepts-limitations#load-balancer-concepts) , který zahrnuje zdrojovou IP adresu. Požadavky klientů distribuovány mezi všechny virtuální počítače ve fondu back endových adres.

### <a name="network-security-groups"></a>Skupiny zabezpečení sítě

Použijte pravidla skupiny zabezpečení sítě, abyste omezili provoz mezi vrstvami. Ve třívrstvé architektuře zobrazené výše webová vrstva nekomunikuje přímo s databázovou vrstvu. Pro vymáhání tohoto pravidla by měla databázová vrstva blokovat příchozí provoz z podsítě webové vrstvy.

1.  Zakazuje veškerý příchozí provoz z virtuální sítě. (Použijte značku VIRTUAL_NETWORK v pravidle.)

2.  Povolí příchozí provoz z podsítě obchodní vrstvy.

3.  Povolí příchozí provoz ze samotné podsítě databázové vrstvy. Toto pravidlo umožňuje komunikaci mezi databází virtuálních počítačů, která je potřebná pro replikaci databáze a převzetí služeb při selhání.

4.  Povolte provoz protokolu RDP (portu 3389) z podsítě jumpbox. Toto pravidlo umožňuje správcům připojit se z jumpboxu k databázové vrstvě.

Vytvořte pravidla 2 – 4 s vyšší prioritou, než má první pravidlo, takže se přepíší.

## <a name="sql-server-always-on-availability-groups"></a>Další informace o skupinách dostupnosti AlwaysOn pro SQL Server

Pro SQL Server vysokou dostupnost doporučujeme [skupiny dostupnosti Always On](https://msdn.microsoft.com/library/hh510230.aspx) . Skupiny dostupnosti AlwaysOn vyžadují před Windows Serverem 2016 řadič domény a všechny uzly ve skupině dostupnosti musí být ve stejné doméně AD.

Pro vysokou dostupnost vrstvy virtuálního počítače musí být všechny virtuální počítače SQL ve skupině dostupnosti.

Jiné úrovně se k databázi připojují prostřednictvím [naslouchacího procesu skupiny dostupnosti](https://msdn.microsoft.com/library/hh213417.aspx). Naslouchací proces umožňuje klientovi SQL připojit se bez znalosti názvu fyzické instance SQL Serveru. Virtuální počítače, které přistupují k databázi, musí být připojené k doméně. Klient (v tomto případě jiná vrstva) používá DNS, aby přeložil název virtuální sítě naslouchacího procesu do IP adres.

Skupiny dostupnosti AlwaysOn pro SQL Server nakonfigurujte následovně:

1.  Vytvořte cluster s podporou převzetí služeb při selhání Windows Serveru (WSFC), skupinu dostupnosti AlwaysOn pro SQL Server a primární repliku. Další informace najdete v tématu [Začínáme se skupinami dostupnosti Always On](https://msdn.microsoft.com/library/gg509118.aspx).

2.  Vytvořte interní nástroj pro vyrovnávání zatížení se statickou privátní IP adresou.

3.  Vytvořte naslouchací proces skupiny dostupnosti a namapujte název DNS naslouchacího procesu na IP adresu interního nástroje pro vyrovnávání zatížení.

4.  Pro port SQL Serveru pro naslouchání (ve výchozím nastavení je to port 1433 protokolu TCP) vytvořte pravidlo nástroje pro vyrovnávání zatížení. Pravidlo nástroje pro vyrovnávání zatížení musí umožňovat *plovoucí IP adresu* (jinak známou jako přímou odpověď ze serveru). Díky tomu bude virtuální počítač odpovídat přímo klientovi, což umožňuje přímé připojení k primární replice.

> [!Note]
> Když je plovoucí adresa povolená, front-endové číslo portu musí být stejné jako back-endové číslo portu v pravidle nástroje pro vyrovnávání zatížení.

Když se klient SQL pokusí připojit, nástroj pro vyrovnávání zatížení bude směrovat žádosti na připojení na primární repliku. Pokud dojde k převzetí služeb při selhání jinou replikou, nástroj pro vyrovnávání zatížení automaticky směruje nové požadavky na novou primární repliku. Další informace najdete v tématu [Konfigurace naslouchacího procesu interního nástroje pro skupiny dostupnosti Always On SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-alwayson-int-listener).

Během převzetí služeb při selhání jsou existující připojení klienta uzavřená. Po dokončení převzetí služeb při selhání se budou nová připojení směrovat na novou primární repliku.

Pokud vaše aplikace poskytuje více operací čtení než zápisů, můžete přesměrovat některé dotazy jen pro čtení na sekundární repliku. Viz téma [použití naslouchacího procesu pro připojení k sekundární replice jen pro čtení (směrování jen pro čtení)](https://technet.microsoft.com/library/hh213417.aspx#ConnectToSecondary).

Otestujte nasazení [vynucením ručního převzetí služeb při selhání](https://msdn.microsoft.com/library/ff877957.aspx) skupiny dostupnosti.

V případě optimalizace výkonu SQL můžete také informace o [osvědčených postupech pro SQL Server, které optimalizují výkon v centru Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-sql-server-vm-considerations).

**JumpBox**

Nepovolujte přístup protokolu RDP z veřejného Internetu k virtuálním počítačům, na kterých běží úlohy aplikace. Místo toho by se měl veškerý přístup k těmto virtuálním počítačům přes protokol RDP procházet přes JumpBox. Správce se přihlásí do jumpboxu a potom se z jumpboxu přihlásí k jinému virtuálnímu počítači. Jumpbox provoz protokolu RDP z internetu umožňuje, ale pouze ze známých a bezpečných IP adres.

Jumpbox má minimální požadavky na výkon, takže vyberte malou velikost virtuálního počítače. Vytvořte jumpboxu [veřejné IP adresy](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm). JumpBox umístěte do stejné virtuální sítě jako ostatní virtuální počítače, ale v samostatné podsíti pro správu.

Pokud chcete jumpbox zabezpečit, přidejte pravidlo NSG, které povoluje připojení protokolu RDP pouze z bezpečné sady veřejných IP adres. Nakonfigurujte skupiny zabezpečení sítě pro ostatní podsítě, aby přijímaly provoz protokolu RDP z podsítě pro správu.

## <a name="scalability-considerations"></a>Aspekty zabezpečení

### <a name="scale-sets"></a>Škálovací sady

Pro webové a obchodní vrstvy zvažte použití [sady škálování virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview) místo nasazení samostatných virtuálních počítačů. Sada škálování usnadňuje nasazení a správu sady identických virtuálních počítačů. Pokud potřebujete rychle škálovat virtuální počítače, zvažte nastavení škálování.

Existují dva základní způsoby, jak virtuální počítače nasazené ve škálovací sadě nakonfigurovat:

-   Pomocí rozšíření můžete nakonfigurovat virtuální počítač po nasazení. U tohoto přístupu je možné, že se nové instance virtuálních počítačů budou načítat déle než u virtuálních počítačů bez rozšíření.

-   Nasaďte [spravovaný disk](https://docs.microsoft.com/azure-stack/user/azure-stack-managed-disk-considerations) s vlastní imagí disku. Nasazení této možnosti může být rychlejší, Ale to vyžaduje, abyste image udržovali aktuální.

Další informace najdete v tématu věnovaném [hlediskům návrhu pro sady škálování](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-design-overview). Toto posouzení návrhu je pro Azure Stack centrum většinou pravdivé, ale existují nějaká upozornění:

-   Sada škálování virtuálních počítačů na rozbočovači Azure Stack nepodporuje převracení ani postupné inovace.

-   Na Azure Stackovém centru nemůžete automatické škálování virtuálních počítačů škálovat.

-   Důrazně doporučujeme používat na Azure Stack hub místo nespravovaných disků pro sadu škálování virtuálního počítače spravované disky.

-   V současné době existuje limit 700 virtuálních počítačů v Azure Stackovém centru, které účty pro všechny virtuální počítače infrastruktury centra Azure Stack, jednotlivé virtuální počítače a instance sady škálování.

## <a name="subscription-limits"></a>Omezení předplatného

Každé předplatné tenanta Azure Stackho centra má nastavené výchozí limity, včetně maximálního počtu virtuálních počítačů na oblast nakonfigurované pomocí operátoru centra Azure Stack. Další informace najdete v tématu [Azure Stack služby centra, plány, nabídky a předplatné přehled](https://docs.microsoft.com/azure-stack/operator/service-plan-offer-subscription-overview). Také se můžete podívat na [typy kvót v centru Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-quota-types).

## <a name="security-considerations"></a>Aspekty zabezpečení

Virtuální sítě jsou hranicí izolace provozu v Azure. Virtuální počítače v jedné virtuální síti ve výchozím nastavení nemůžou komunikovat přímo s virtuálními počítači v jiné virtuální síti.

**Skupiny NSG:** Pomocí [skupin zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) (skupin zabezpečení sítě) můžete omezit provoz na Internet a z něj. Další informace najdete v tématu [zabezpečení cloudových služeb a sítí od Microsoftu](https://docs.microsoft.com/azure/best-practices-network-security).

**DMZ**. Zvažte přidání síťového virtuálního zařízení, abyste mezi internetem a virtuální sítí Azure vytvořili síť DMZ. Síťové virtuální zařízení je obecný termín pro virtuální zařízení, které provádí úlohy související se sítí, jako je brána firewall, kontrola paketu, auditování a vlastní směrování.

**Šifrování**. Zašifrujte citlivá data v klidovém umístění a pomocí [Key Vault v centru Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-key-vault-manage-portal) spravujte šifrovací klíče databáze. Další informace najdete v tématu [Konfigurace Integrace se službou Azure Key Vault pro virtuální počítače Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-ps-sql-keyvault). Také se doporučuje ukládat tajné kódy aplikace, jako jsou databázové připojovací řetězce, ve službě Key Vault.

## <a name="next-steps"></a>Další kroky

- Další informace o vzorech cloudu Azure najdete v tématu [vzory návrhu cloudu](https://docs.microsoft.com/azure/architecture/patterns).
