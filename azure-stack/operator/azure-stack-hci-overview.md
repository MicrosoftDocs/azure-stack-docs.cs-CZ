---
title: Přehled Azure Stack centra HCI
description: Služba Azure Stack hub HCI je cluster Windows Server 2019 s podporou technologie Hyper-v, který používá ověřený hardware ke spouštění virtualizovaných úloh místně. Volitelně se můžete připojit ke službám Azure za účelem cloudového zálohování, obnovení lokality a dalších možností. Řešení pro Azure Stack centra HCI používají hardware ověřený společností Microsoft k zajištění optimálního výkonu a spolehlivosti a zahrnují podporu pro technologie, jako jsou NVMe Drives, trvalá paměť a sítě RDMA (Remote Direct přímý přístup do paměti).
ms.topic: article
author: jasongerend
ms.author: jgerend
ms.localizationpriority: medium
ms.date: 11/04/2019
ms.openlocfilehash: d35c38ab40543556d31af443f45c0c249ae76e2c
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75882483"
---
# <a name="azure-stack-hub-hci-overview"></a>Přehled Azure Stack centra HCI

HCI Azure Stack hub je vysoce sblížený cluster s Windows serverem 2019, který používá ověřený hardware ke spouštění virtualizovaných úloh místně. Volitelně se můžete připojit ke službám Azure za účelem cloudového zálohování, obnovení lokality a dalších možností. Řešení pro Azure Stack centra HCI používají hardware ověřený společností Microsoft k zajištění optimálního výkonu a spolehlivosti a zahrnují podporu pro technologie, jako jsou NVMe Drives, trvalá paměť a sítě RDMA (Remote Direct přímý přístup do paměti).

HCI Azure Stack hub je řešení, které kombinuje několik produktů:

- Hardware od partnera OEM

- Edice Windows Server 2019 Datacenter

- Windows Admin Center

- Služby Azure (volitelné)

![HCI Azure Stack hub je řešení s jednou konvergou od Microsoftu, které je dostupné z široké škály hardwarových partnerů.](media/azure-stack-hci/azure-stack-hci-solution.png)

HCI Azure Stack hub je řešení s jednou konvergou od Microsoftu, které je dostupné z široké škály hardwarových partnerů. Vezměte v úvahu následující scénáře, které vám pomůžou určit, jestli je řešení pro Azure Stack hub v centru, které nejlépe vyhovuje vašim potřebám:

- **Aktualizovat hardware pro sledování splatnosti.** Nahraďte starší servery a infrastrukturu úložiště a místní a na hraničních počítačích s Windows a Linux, a to s využitím stávajících dovedností a nástrojů IT.

- **Konsolidujte virtualizované úlohy.** Konsolidujte starší verze aplikací s využitím účinné, s více sblíženými infrastrukturami. Využijte stejný typ cloudového efektivity, který se používá ke spouštění datových center v rámci technologie Hyper-Scale, jako je Microsoft Azure.

- **Připojte se k Azure pro hybridní cloudové služby.** Zjednodušte přístup ke službám Cloud Management a zabezpečení v Azure, včetně zálohování mimo pracoviště, Site Recovery, cloudového monitorování a dalších.

## <a name="the-azure-stack-hub-family"></a>Rodina centra Azure Stack

Rozhraní HCI služby Azure Stack hub je součástí řady rozbočovačů Azure a Azure Stack a používá stejný software definovaný pro výpočetní prostředky, úložiště a síťový software jako centrum Azure Stack. Tady je stručný přehled různých řešení (Další informace najdete v tématu [porovnání ekosystému centra Azure Stack](compare-azure-azure-stack.md)):

- [Azure](https://azure.microsoft.com) – využijte veřejné cloudové služby pro samoobslužné výpočetní prostředky na vyžádání k migraci a modernizovat stávajících aplikací a vytváření nových aplikací nativních pro Cloud.
- [Azure Stack Edge centra](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview) – Zrychlete úlohy strojového učení a Spouštějte aplikace s podporou kontejnerů nebo virtualizovaných úloh na zařízení, které spravuje Cloud.
- [Azure Stack centra HCI](https://azure.microsoft.com/overview/azure-stack/hci) – spouštění virtualizovaných aplikací v místním prostředí, výměna a konsolidace infrastruktury serveru pro stárnutí a připojení k Azure pro cloudové služby.
- [Azure Stack centrum](azure-stack-overview.md) – spouštění cloudových aplikací v místním prostředí, pokud je odpojená, nebo aby splňovaly zákonné požadavky, a to s využitím konzistentních služeb Azure.

![Lokalita centra Azure Stack je cloudové zařízení spravované pro spuštění strojového učení a kontejnerových aplikací na hraničních zařízeních, Azure Stack je rozhraní HCI služby The-premise nakonvergované řešení pro spouštění virtuálních počítačů a úložiště v místním prostředí, zatímco služba Azure Stack hub poskytuje nativní Cloud. Služby konzistentní s Azure v místním prostředí.](media/compare-azure-azure-stack/azure-stack-family.png)

Další informace najdete v tématech:

- Další informace najdete na webu řešení pro řešení [HCI centra Azure Stack](https://azure.microsoft.com/overview/azure-stack/hci) .
- Sledujte odborníky Microsoftu Jan Woolsey a Vijay Tewari, [která se zabývá novými řešeními HCI centra Azure Stack](https://aka.ms/AzureStackOverviewVideo).

## <a name="hyperconverged-efficiencies"></a>Nakonvergované efektivity

Řešení Azure Stack centra HCI dohromady přinášejí vysoce virtualizované výpočetní prostředky, úložiště a sítě na standardních serverech a komponentách x86. Kombinování prostředků ve stejném clusteru usnadňuje nasazení, správu a škálování. Spravujte pomocí možnosti automatizace příkazového řádku nebo centra pro správu systému Windows.

Zajištění špičkového výkonu virtuálních počítačů pro vaše serverové aplikace pomocí technologie Hyper-V, základní technologie hypervisoru Microsoft cloudu a Prostory úložiště s přímým přístupem technologie s integrovanou podporou pro NVMe, trvalou paměť a síť vzdáleného přímého přístupu do paměti (RDMA).

Zabezpečte aplikace a data zabezpečená pomocí chráněných virtuálních počítačů, mikrosegmentace sítě a nativního šifrování.

## <a name="hybrid-capabilities"></a>Hybridní možnosti

Ve veřejném cloudu můžete využít výhod cloudu a místního prostředí spolu s nasazenou platformou infrastruktury. Váš tým může začít sestavovat cloudové dovednosti díky integrované integraci do služeb Azure Infrastructure Management:

- Azure Site Recovery pro zajištění vysoké dostupnosti a zotavení po havárii jako služba (DRaaS).

- Azure Monitor centralizované centrum, které sleduje, co se děje napříč vašimi aplikacemi, sítí a infrastrukturou – díky pokročilým analýzám, které využívají AI.

- Disk s kopií cloudu, aby se Azure používal jako zjednodušený modul pro dělení na clustery pro kvorum clusteru.

- Azure Backup k ochraně dat mimo lokalitu a k ochraně před ransomwarem.

- Azure Update Management pro posouzení aktualizací a nasazení aktualizací pro virtuální počítače s Windows běžící v Azure a v místním prostředí.

- Síťový adaptér Azure pro připojení prostředků v místním prostředí k virtuálním počítačům v Azure prostřednictvím sítě VPN typu Point-to-site.

- Synchronizujte souborový server s cloudem pomocí Azure File Sync.

Podrobnosti najdete v tématu [propojení Windows serveru s Azure Hybrid Services](https://docs.microsoft.com/windows-server/manage/windows-admin-center/azure/index).

## <a name="management-tools-and-system-center"></a>Nástroje pro správu a System Center

Rozhraní HCI služby Azure Stack hub používá stejnou virtualizační a softwarově definované úložiště a síťový software jako centrum Azure Stack. V případě Azure Stack v programu hub máte v clusteru plná práva správce a můžete spravovat libovolné technologie přímo:

- [Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/hyper-v-on-windows-server)
- [Prostory úložiště s přímým přístupem](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [Softwarově definované sítě](https://docs.microsoft.com/windows-server/networking/sdn/)
- [Clusteringu s podporou převzetí služeb při selhání](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)

Chcete-li tyto technologie spravovat, můžete použít následující nástroje pro správu:

- [Centrum pro správu systému Windows](https://docs.microsoft.com/windows-server/manage/windows-admin-center/overview)
- [System Center](https://www.microsoft.com/cloud-platform/system-center)
- [PowerShell](https://docs.microsoft.com/powershell/?view=powershell-6)
- Další nástroje pro správu, například [Správce serveru](https://docs.microsoft.com/windows-server/administration/server-manager/server-manager)a moduly snap-in konzoly MMC
- Nástroje od jiných výrobců, jako je společnosti 5NINE Manager

Pokud se rozhodnete k nasazení a správě infrastruktury používat System Center, budete používat nástroj System Center Virtual Machine Management (VMM) a System Center Operations Manager. Pomocí nástroje VMM zřizujete a spravujete prostředky potřebné k vytváření a nasazování virtuálních počítačů a služeb do privátních cloudů. Pomocí Operations Manager můžete monitorovat služby, zařízení a operace v celém podniku a identifikovat problémy pro okamžité akce.

## <a name="hardware-partners"></a>Hardwarové partneři

Můžete zakoupit ověřená řešení Azure Stack centra HCI, která používají Windows Server 2019, od 20 partnerů. Upřednostňovaný partner Microsoftu vám pomůže pracovat bez zdlouhavého návrhu a času sestavování. Nabízí také jeden kontaktní bod pro implementaci a služby podpory.

Navštivte [web Azure Stack hub HCI](https://azure.microsoft.com/overview/azure-stack/hci) , kde můžete zobrazit naše 70 a Azure Stacká řešení HCI centra, která jsou aktuálně dostupná od těchto partnerů Microsoftu: ASUS, Axellio, bluechip, DataON, Dell EMC, Fujitsu, HPE, Hitachi, Huawei, Lenovo, NEC, primeLine Solutions, QCT, SecureGUARD a Micro.

## <a name="faq"></a>Časté otázky

### <a name="what-do-azure-stack-hub-and-azure-stack-hub-hci-solutions-have-in-common"></a>Co jsou běžné řešení Azure Stack centra a Azure Stack centra pro HCI?

Řešení rozhraní HCI služby Azure Stack hub obsahují stejné technologie založené na výpočetním, úložném a síťovém softwaru založeném na technologii Hyper-V jako centrum Azure Stack. Obě nabídky splňují přísná kritéria testování a ověřování, která zajišťují spolehlivost a kompatibilitu s podkladovou hardwarovou platformou.

### <a name="how-are-they-different"></a>Jaký je mezi nimi rozdíl?

Pomocí centra Azure Stack spustíte cloudové služby v místním prostředí. Můžete spouštět služby Azure IaaS a PaaS v místním prostředí a konzistentně vytvářet a spouštět cloudové aplikace odkudkoli a spravovat je s Azure Portal v místním prostředí.

Pomocí Azure Stackového centra HCI spouštíte virtualizované úlohy místně, spravované pomocí centra pro správu Windows a známých nástrojů Windows serveru. Volitelně se můžete připojit k Azure v případě hybridních scénářů, jako jsou cloudové služby Site Recovery, monitorování a další.

### <a name="why-is-microsoft-bringing-its-hci-offering-to-the-azure-stack-hub-family"></a>Proč společnost Microsoft přinese svou nabídku HCI do rodiny Azure Stack hub?

Technologie s jednou konvergou Microsoftu je už základem centra Azure Stack.

Mnoho zákazníků Microsoftu má složitá IT prostředí a naším cílem je poskytovat řešení, která je splňují, kde jsou s správnou technologií pro správné obchodní potřeby. Rozhraní HCI služby Azure Stack hub je vývoj řešení (WSSD) pro Windows Server 2016, která byla dříve k dispozici od našich hardwarových partnerů. Připravili jsme ji do rodiny centra Azure Stack, protože jsme začali nabízet nové možnosti pro bezproblémové připojení s Azure pro služby správy infrastruktury.

### <a name="does-azure-stack-hub-hci-need-to-be-connected-to-azure"></a>Musí být ke službě Azure připojená Azure Stack centra HCI?

Ne, je volitelné. Můžete využít výhod integrace s Azure pro hybridní scénáře, jako je zálohování mimo pracoviště a zotavení po havárii, a cloudové monitorování a správa aktualizací, ale jsou volitelné. Nejedná se o problém spuštění odpojení z Internetu.

### <a name="how-does-azure-stack-hub-hci-relate-to-windows-server"></a>Jak se vztahuje Azure Stack hub v systému Windows Server?

Windows Server 2019 je základem skoro každého produktu Azure. Všechny funkce, které zadáváte, dál dodávají a podporují ve Windows serveru. Rozhraní HCI služby Azure Stack hub je doporučeným způsobem, jak nasadit lokálně v místním prostředí pomocí hardwaru ověřovaného společností Microsoft od našich partnerů.

### <a name="can-i-upgrade-from-azure-stack-hub-hci-to-azure-stack-hub"></a>Můžu upgradovat z Azure Stack z centra HCI na Azure Stack hub? 

Ne, ale zákazníci můžou migrovat své úlohy z centra Azure Stack v HCI na Azure Stack hub nebo Azure.

### <a name="what-azure-services-can-i-connect-to-azure-stack-hub-hci"></a>Jaké služby Azure se můžu připojit k Azure Stack hub?

Aktualizovaný seznam služeb Azure, ke kterým se můžete připojit Azure Stack hub pro Azure Hybrid Services, najdete v tématu [propojení Windows serveru](https://docs.microsoft.com/windows-server/manage/windows-admin-center/azure/index).

### <a name="how-does-the-cost-of-azure-stack-hub-hci-compare-to-azure-stack-hub"></a>Jakým způsobem jsou náklady na Azure Stack centra HCI porovnat s Azure Stackm rozbočovačem? 

Centrum Azure Stack se prodává jako plně integrovaný systém, který zahrnuje služby a podporu. Centrum Azure Stack můžete koupit jako systém, který spravujete, nebo jako plně spravovanou službu od našich partnerů. Kromě základního systému se služby Azure, které běží na službě Azure Stack hub nebo Azure, prodávají na základě průběžných plateb.

Řešení HCI centra Azure Stack sledují tradiční model nákupu. Ověřený hardware si můžete koupit z centra Azure Stack partneři a software (Windows Server 2019 Datacenter Edition) z různých existujících kanálů, a to pomocí softwarově definovaných funkcí Datacenter a centra pro správu Windows). Pro služby Azure, které můžete používat s centrem pro správu Windows, platíte pomocí předplatného Azure.

### <a name="how-do-i-buy-azure-stack-hub-hci-solutions"></a>Návody koupit řešení HCI Azure Stack hub?

Postupujte následovně:

1. Zakupte hardwarový systém ověřený společností Microsoft od preferovaného hardwarového partnera.
1. Instalace Windows serveru 2019 Datacenter Edition a centra pro správu Windows pro správu a možnost připojení k Azure pro cloudové služby
1. Volitelně můžete pomocí účtu Azure připojit cloudové služby pro správu a zabezpečení k vašim úlohám.

![Pokud chcete koupit řešení HCI centra Azure Stack, vyberte hardwarový partner a konfiguraci, které nejlépe vyhovuje vašim potřebám.](media/azure-stack-hci/buying-azure-stack-hci.png)

## <a name="compare-azure-stack-hub-and-azure-stack-hub-hci"></a>Porovnání centra Azure Stack a Azure Stackho centra HCI

V případě, že vaše organizace bude digitálně transformovat, může se stát, že můžete rychleji přesunout pomocí veřejných cloudových služeb a vytvořit si moderní architektury a aktualizovat starší aplikace. Z důvodů, které zahrnují technologické a regulativní překážky, ale mnoho úloh musí zůstat v místním prostředí. Následující tabulka vám pomůže určit, kterou strategii hybridního cloudu od Microsoftu nabízí, co potřebujete, a dodávat cloudové inovace pro úlohy bez ohledu na to, kde jsou.

| Azure Stack Hub | Azure Stack centra HCI |
| --------------- | --------------- |
| Nové dovednosti, inovativní procesy | Stejné dovednosti, známé procesy |
| Služby Azure ve vašem datovém centru | Připojení datacentra ke službám Azure |

### <a name="when-to-use-azure-stack-hub"></a>Kdy použít centrum Azure Stack

| Azure Stack Hub | Azure Stack centra HCI |
| --------------- | --------------- |
| Služba Azure Stack hub slouží k samoobslužné infrastruktuře jako služby (IaaS) se silným izolací a přesným sledováním využití a vrácení peněz pro více společně umístěných klientů. Ideální pro poskytovatele služeb a privátní cloudy v podniku. Šablony z Azure Marketplace. | V případě služby Azure Stack hub není nativně vynutila nebo neposkytuje pro víceklientské architektury. |
| Pomocí centra Azure Stack můžete vyvíjet a spouštět aplikace, které spoléhají na služby typu platforma jako služba (PaaS), jako jsou Web Apps, funkce nebo Event Hubs v místním prostředí. Tyto služby běží na Azure Stackovém rozbočovači přesně stejně jako v Azure a poskytují konzistentní prostředí pro hybridní vývoj a běhové prostředí. | Služba Azure Stack hub HCI nespouští PaaS služby v místním prostředí. |
| Pomocí centra Azure Stack můžete modernizovat nasazení a provoz aplikací s postupy DevOps, jako je infrastruktura jako kód, průběžná integrace a průběžné nasazování (CI/CD) a pohodlné funkce, jako jsou třeba rozšíření virtuálních počítačů konzistentní s Azure. Ideální pro vývojové a DevOps týmy. | DevOps hub nezahrnuje nativně žádné nástroje pro. Azure Stack |

### <a name="when-to-use-azure-stack-hub-hci"></a>Kdy použít Azure Stack centra HCI

| Azure Stack Hub | Azure Stack centra HCI |
| --------------- | --------------- |
| Azure Stack hub vyžaduje minimálně 4 uzly a vlastní síťové přepínače. | Pro minimální nároky na vzdálené pobočky a větve použijte Azure Stack hub. Začněte s využitím pouhých 2 uzlů serveru a bez přepínat zpět na zpětnou síť pro zjednodušení a dostupnost. V nabídce hardware se spouští 4 jednotky, 64 GB paměti, a to i v poli $10 000/uzel. |
| Rozbočovač Azure Stack omezuje možnost využití technologie Hyper-V a sadu funkcí pro zajištění konzistence s Azure. | Pro klasické podnikové aplikace, jako je Exchange, SharePoint a SQL Server a virtualizace rolí Windows serveru, jako jsou souborové servery, DNS, DHCP, IIS a AD, použijte službu Azure Stack hub v Frills. Neomezený přístup ke všem funkcím technologie Hyper-V, jako jsou stíněné virtuální počítače.|
| Azure Stack hub nezveřejňuje tyto technologie infrastruktury. | Pomocí Azure Stackového centra HCI se používá softwarově definovaná infrastruktura místo polí úložišť nebo síťových zařízení, aniž by došlo k zásadní opětovné architektuře. Integrované technologie Hyper-V, Prostory úložiště s přímým přístupem a softwarově definované sítě (SDN) jsou přímo přístupné a spravovatelné. |
