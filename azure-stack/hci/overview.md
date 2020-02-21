---
title: Přehled Azure Stack HCI
description: Azure Stack HCI je cluster Windows Server 2019 s podporou technologie Hyper-v, který používá ověřený hardware ke spouštění virtualizovaných úloh místně. Volitelně se můžete připojit ke službám Azure za účelem cloudového zálohování, obnovení lokality a dalších možností. Azure Stack řešení HCI používají hardware ověřený společností Microsoft k zajištění optimálního výkonu a spolehlivosti a zahrnují podporu pro technologie, jako jsou NVMe Drives, trvalá paměť a Vzdálená síť s přímým přístupem do paměti (RDMA).
ms.topic: article
author: jasongerend
ms.author: jgerend
ms.localizationpriority: medium
ms.date: 11/04/2019
ms.openlocfilehash: 2bf9af3db5a3577b62c59e92879a3680d9a3532a
ms.sourcegitcommit: 97806b43314d306e0ddb15847c86be2c92ae001e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2020
ms.locfileid: "77519446"
---
# <a name="azure-stack-hci-overview"></a>Přehled Azure Stack HCI

Azure Stack HCI je vysoce sblížený cluster s Windows serverem 2019, který používá ověřený hardware ke spouštění virtualizovaných úloh místně. Volitelně se můžete připojit ke službám Azure za účelem cloudového zálohování, obnovení lokality a dalších možností. Azure Stack řešení HCI používají hardware ověřený společností Microsoft k zajištění optimálního výkonu a spolehlivosti a zahrnují podporu pro technologie, jako jsou NVMe Drives, trvalá paměť a Vzdálená síť s přímým přístupem do paměti (RDMA).

Azure Stack HCI je řešení, které kombinuje několik produktů:

- Hardware od partnera OEM

- Windows Server 2019 Datacenter Edition

- Centrum pro správu systému Windows

- Služby Azure (volitelné)

![Azure Stack HCI je řešení s jednou konvergou Microsoftu dostupné z široké škály hardwarových partnerů.](media/overview/azure-stack-hci-solution.png)

Azure Stack HCI je řešení s jednou konvergou Microsoftu dostupné z široké škály hardwarových partnerů. Vezměte v úvahu následující scénáře, které vám pomůžou určit, jestli je Azure Stack HCL řešení, které nejlépe vyhovuje vašim potřebám:

- **Aktualizovat hardware pro sledování splatnosti.** Nahraďte starší servery a infrastrukturu úložiště a místní a na hraničních počítačích s Windows a Linux, a to s využitím stávajících dovedností a nástrojů IT.

- **Konsolidujte virtualizované úlohy.** Konsolidujte starší verze aplikací s využitím účinné, s více sblíženými infrastrukturami. Využijte stejný typ cloudového efektivity, který se používá ke spouštění datových center v rámci technologie Hyper-Scale, jako je Microsoft Azure.

- **Připojte se k Azure pro hybridní cloudové služby.** Zjednodušte přístup ke službám Cloud Management a zabezpečení v Azure, včetně zálohování mimo pracoviště, Site Recovery, cloudového monitorování a dalších.

## <a name="the-azure-stack-family"></a>Řada Azure Stack

Azure Stack rozhraní HCI je součástí řady Azure a Azure Stack, která využívá stejný software definovaný pro výpočetní prostředky, úložiště a síťový software jako centrum Azure Stack. Tady je stručný přehled různých řešení (Další informace najdete v tématu [porovnání Azure Stack ekosystému](../operator/compare-azure-azure-stack.md)):

- [Azure](https://azure.microsoft.com) – využijte veřejné cloudové služby pro samoobslužné výpočetní prostředky na vyžádání k migraci a modernizovat stávajících aplikací a vytváření nových aplikací nativních pro Cloud.
- [Azure Stack Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview) – Zrychlete úlohy strojového učení a Spouštějte aplikace s podporou kontejnerů nebo virtualizovaných úloh na zařízení spravovaném pomocí cloudu.
- [Azure Stack](https://azure.microsoft.com/overview/azure-stack/hci) v prostředí HCI – spouštění virtualizovaných aplikací v místním prostředí, výměna a konsolidace infrastruktury serveru pro stárnutí a připojení k Azure pro cloudové služby.
- [Azure Stack centrum](../operator/azure-stack-overview.md) – spouštění cloudových aplikací v místním prostředí, pokud je odpojená, nebo aby splňovaly zákonné požadavky, a to s využitím konzistentních služeb Azure.

![Azure Stack Edge je cloudové zařízení spravované pro provoz strojového učení a kontejnerů na hraničních zařízeních, Azure Stack HCI je vysoce sblížené řešení pro spouštění virtuálních počítačů a úložiště v místním prostředí, zatímco služba Azure Stack hub poskytuje cloudové nativní prostředí, které je konzistentní vzhledem k Azure místní služby.](media/overview/azure-family.png)

Další informace:

- Další informace najdete na našem webu řešení [Azure Stack HCL](https://azure.microsoft.com/overview/azure-stack/hci) .
- Sledujte odborníky Microsoftu Jan Woolsey a Vijay Tewari a [prodiskutujte nové řešení Azure Stack HCI](https://aka.ms/AzureStackOverviewVideo).

## <a name="hyperconverged-efficiencies"></a>Nakonvergované efektivity

Azure Stack řešení HCI dohromady přinášejí vysoce virtualizované výpočetní prostředky, úložiště a sítě na standardních serverech a komponentách x86. Kombinování prostředků ve stejném clusteru usnadňuje nasazení, správu a škálování. Spravujte pomocí možnosti automatizace příkazového řádku nebo centra pro správu systému Windows.

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

Azure Stack HCI používá stejnou virtualizaci a softwarově definované úložiště a síťový software jako centrum Azure Stack. U Azure Stack HCL máte v clusteru úplná práva správce a můžete spravovat libovolné technologie přímo:

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

Můžete koupit ověřená Azure Stack řešení HCI, která používají Windows Server 2019 od 20 partnerů. Upřednostňovaný partner Microsoftu vám pomůže pracovat bez zdlouhavého návrhu a času sestavování. Nabízí také jeden kontaktní bod pro implementaci a služby podpory.

Navštivte [web Azure Stack HCL](https://azure.microsoft.com/overview/azure-stack/hci) , kde můžete zobrazit naše 70 a Azure Stack HCL, které jsou aktuálně k dispozici od těchto partnerů Microsoftu: ASUS, Axellio, bluechip, DataON, Dell EMC, Fujitsu, HPE, Hitachi, Huawei, Lenovo, NEC, primeLine Solutions, QCT, SecureGUARD a Micro.

## <a name="video-based-learning"></a>Učení na základě videa

Tady jsou některá videa z relací Microsoft Ignite 2019:

- [Microsoft Ignite Live 2019-Začínáme s Azure Stack HCL](https://www.youtube.com/watch?v=vueHIBqNIEU)
- [Zjistit Azure Stack HCI](https://www.youtube.com/watch?v=4aGZK0Ndmh8&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=13&t=0s)
- [Modernizovat své prodejny nebo firemní pobočky pomocí Azure Stack HCI](https://www.youtube.com/watch?v=-JzLhjfkhmM&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=9&t=0s)
- [Novinky Azure Stack HCL: 45 věcí během 45 minut](https://www.youtube.com/watch?v=C5J4IEnlS_E&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=12&t=0s)
- [Rychlé zprovoznění nasazení prostředí HCI pro Azure Stack](https://www.youtube.com/watch?v=gxaPJLrWy5w&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=11&t=0s)
- [Případ zmenšení dat: odstranění duplicitních dat v Azure Stack HCI](https://www.youtube.com/watch?v=fmm4iDbDiY4&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=23&t=0s)
- [Poznámky k Dave Kawula z pole v Azure Stack HCI](https://www.youtube.com/watch?v=OXv7fLlz0ew&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=2&t=0s)

Tady je video z hybridní cloudové virtuální události:

- [Azure Stack HCI | Virtuální událost hybridního cloudu](https://www.youtube.com/watch?v=nxpoEva-R2Y)

## <a name="faq"></a>Nejčastější dotazy

### <a name="what-do-azure-stack-hub-and-azure-stack-hci-solutions-have-in-common"></a>Co jsou běžné řešení Azure Stack hub a Azure Stack HCL?

Azure Stack řešení HCI obsahují stejné technologie založené na výpočetním, úložném a síťovém softwaru založeném na technologii Hyper-V jako centrum Azure Stack. Obě nabídky splňují přísná kritéria testování a ověřování, která zajišťují spolehlivost a kompatibilitu s podkladovou hardwarovou platformou.

### <a name="how-are-they-different"></a>Jaký je mezi nimi rozdíl?

Pomocí centra Azure Stack spustíte cloudové služby v místním prostředí. Můžete spouštět služby Azure IaaS a PaaS v místním prostředí a konzistentně vytvářet a spouštět cloudové aplikace odkudkoli a spravovat je s Azure Portal v místním prostředí.

Díky Azure Stack HCI spouštíte virtualizované úlohy místně, spravované pomocí centra pro správu Windows a známých nástrojů Windows serveru. Volitelně se můžete připojit k Azure v případě hybridních scénářů, jako jsou cloudové služby Site Recovery, monitorování a další.

### <a name="why-is-microsoft-bringing-its-hci-offering-to-the-azure-stack-family"></a>Proč společnost Microsoft do řady Azure Stack přinese svou nabídku HCI?

Technologie s jednou konvergou Microsoftu je už základem centra Azure Stack.

Mnoho zákazníků Microsoftu má složitá IT prostředí a naším cílem je poskytovat řešení, která je splňují, kde jsou s správnou technologií pro správné obchodní potřeby. Azure Stack HCI je vývoj řešení (WSSD) systému Windows Server 2016, která byla dříve k dispozici od našich hardwarových partnerů. Připravili jsme ji do Azure Stack řady, protože jsme začali nabízet nové možnosti pro bezproblémové připojení s Azure pro služby správy infrastruktury.

### <a name="does-azure-stack-hci-need-to-be-connected-to-azure"></a>Musí být Azure Stack HCIy připojené k Azure?

Ne, je volitelné. Můžete využít výhod integrace s Azure pro hybridní scénáře, jako je zálohování mimo pracoviště a zotavení po havárii, a cloudové monitorování a správa aktualizací, ale jsou volitelné. Nejedná se o problém spuštění odpojení z Internetu.

### <a name="how-does-azure-stack-hci-relate-to-windows-server"></a>Jak se vztahuje Azure Stack HCL na Windows Server?

Windows Server 2019 je základem skoro každého produktu Azure. Všechny funkce, které zadáváte, dál dodávají a podporují ve Windows serveru. Azure Stack HCI je doporučený způsob, jak nasadit lokálně v místním prostředí s využitím hardwaru ověřeného společností Microsoft od našich partnerů.

### <a name="can-i-upgrade-from-azure-stack-hci-to-azure-stack-hub"></a>Můžu upgradovat z Azure Stack HCL na Azure Stack hub? 

Ne, ale zákazníci můžou migrovat své úlohy z Azure Stack HCI na Azure Stack hub nebo Azure.

### <a name="what-azure-services-can-i-connect-to-azure-stack-hci"></a>Které služby Azure se mohu připojit k Azure Stack HCI?

Aktualizovaný seznam služeb Azure, ke kterým se můžete připojit Azure Stack HCL, najdete v tématu [připojení Windows serveru k Azure Hybrid Services](https://docs.microsoft.com/windows-server/manage/windows-admin-center/azure/index).

### <a name="how-does-the-cost-of-azure-stack-hci-compare-to-azure-stack-hub"></a>Jak se náklady na Azure Stack HCI porovnávají s Azure Stack hub? 

Centrum Azure Stack se prodává jako plně integrovaný systém, který zahrnuje služby a podporu. Centrum Azure Stack můžete koupit jako systém, který spravujete, nebo jako plně spravovanou službu od našich partnerů. Kromě základního systému se služby Azure, které běží na službě Azure Stack hub nebo Azure, prodávají na základě průběžných plateb.

Řešení Azure Stack HCI sledují tradiční model nákupu. Ověřený hardware si můžete koupit z Azure Stack partneři a software funkce HCI (Windows Server 2019 Datacenter Edition pomocí softwarově definovaných funkcí Datacenter a centra pro správu systému Windows) z různých existujících kanálů. Pro služby Azure, které můžete používat s centrem pro správu Windows, platíte pomocí předplatného Azure.

### <a name="how-do-i-buy-azure-stack-hci-solutions"></a>Návody koupit řešení Azure Stack HCI?

Postupujte následovně:

1. Zakupte hardwarový systém ověřený společností Microsoft od preferovaného hardwarového partnera.
1. Instalace Windows serveru 2019 Datacenter Edition a centra pro správu Windows pro správu a možnost připojení k Azure pro cloudové služby
1. Volitelně můžete pomocí účtu Azure připojit cloudové služby pro správu a zabezpečení k vašim úlohám.

![Pokud chcete koupit Azure Stack řešení HCI, vyberte hardwarového partnera a konfiguraci, který nejlépe vyhovuje vašim potřebám.](media/overview/buying-azure-stack-hci.png)

## <a name="compare-azure-stack-hub-and-azure-stack-hci"></a>Porovnat centra Azure Stack a Azure Stack HCI

V případě, že vaše organizace bude digitálně transformovat, může se stát, že můžete rychleji přesunout pomocí veřejných cloudových služeb a vytvořit si moderní architektury a aktualizovat starší aplikace. Z důvodů, které zahrnují technologické a regulativní překážky, ale mnoho úloh musí zůstat v místním prostředí. Následující tabulka vám pomůže určit, kterou strategii hybridního cloudu od Microsoftu nabízí, co potřebujete, a dodávat cloudové inovace pro úlohy bez ohledu na to, kde jsou.

| Centrum Azure Stack | Azure Stack HCI |
| --------------- | --------------- |
| Nové dovednosti, inovativní procesy | Stejné dovednosti, známé procesy |
| Služby Azure ve vašem datovém centru | Připojení datacentra ke službám Azure |

### <a name="when-to-use-azure-stack-hub"></a>Kdy použít centrum Azure Stack

| Centrum Azure Stack | Azure Stack HCI |
| --------------- | --------------- |
| Služba Azure Stack hub slouží k samoobslužné infrastruktuře jako služby (IaaS) se silným izolací a přesným sledováním využití a vrácení peněz pro více společně umístěných klientů. Ideální pro poskytovatele služeb a privátní cloudy v podniku. Šablony z Azure Marketplace. | Azure Stack HCI není nativně vynutila nebo neposkytuje pro víceklientské architektury. |
| Pomocí centra Azure Stack můžete vyvíjet a spouštět aplikace, které spoléhají na služby typu platforma jako služba (PaaS), jako jsou Web Apps, funkce nebo Event Hubs v místním prostředí. Tyto služby běží na Azure Stackovém rozbočovači přesně stejně jako v Azure a poskytují konzistentní prostředí pro hybridní vývoj a běhové prostředí. | Azure Stack HCI neběží v místním prostředí PaaS Services. |
| Pomocí centra Azure Stack můžete modernizovat nasazení a provoz aplikací s postupy DevOps, jako je infrastruktura jako kód, průběžná integrace a průběžné nasazování (CI/CD) a pohodlné funkce, jako jsou třeba rozšíření virtuálních počítačů konzistentní s Azure. Ideální pro vývojové a DevOps týmy. | Azure Stack HCI nezahrnuje nativně žádné nástroje DevOps. |

### <a name="when-to-use-azure-stack-hci"></a>Kdy použít Azure Stack HCL

| Centrum Azure Stack | Azure Stack HCI |
| --------------- | --------------- |
| Azure Stack hub vyžaduje minimálně 4 uzly a vlastní síťové přepínače. | Pro minimální nároky na vzdálené pobočky a větve použijte Azure Stack HCL. Začněte s využitím pouhých 2 uzlů serveru a bez přepínat zpět na zpětnou síť pro zjednodušení a dostupnost. V nabídce hardware se spouští 4 jednotky, 64 GB paměti, a to i v poli $10 000/uzel. |
| Rozbočovač Azure Stack omezuje možnost využití technologie Hyper-V a sadu funkcí pro zajištění konzistence s Azure. | K virtualizaci Frills technologie Hyper-V pro klasické podnikové aplikace, jako je Exchange, SharePoint a SQL Server a Virtualizujte role Windows serveru jako souborové servery, DNS, DHCP, IIS a AD, použijte Azure Stack HCI. Neomezený přístup ke všem funkcím technologie Hyper-V, jako jsou stíněné virtuální počítače.|
| Azure Stack hub nezveřejňuje tyto technologie infrastruktury. | Použijte Azure Stack HCI k použití softwarově definované infrastruktury místo polí úložišť nebo síťových zařízení, bez hlavní opětovné architektury. Integrované technologie Hyper-V, Prostory úložiště s přímým přístupem a softwarově definované sítě (SDN) jsou přímo přístupné a spravovatelné. |
