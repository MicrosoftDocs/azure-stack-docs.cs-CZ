---
title: Přehled služby Azure Stack HCL
description: Azure Stack HCL je ověřen konvergované na hyper v Windows serveru 2019 cluster, který používá ke spuštění virtualizované úlohy v místním hardwaru. Také v případě potřeby můžete připojit ke službám Azure pro cloudové zálohování, obnovení webu a další. Řešení Azure Stack HCL Microsoft ověření hardwaru můžete zajistit optimální výkon a spolehlivost a zahrnují podporu pro technologie, jako jsou jednotky NVMe, trvalé paměti a sítě paměti vzdálený přímý přístup do (počítače RDMA).
ms.technology: storage
ms.topic: article
author: jasongerend
ms.author: jgerend
ms.localizationpriority: medium
ms.prod: windows-server-threshold
ms.date: 05/31/2019
ms.openlocfilehash: c3f25f38429b58d8d72037ff4ed9758d9993a2c5
ms.sourcegitcommit: 07cc716d97bf484c7260eb165ae205ae25e09589
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/31/2019
ms.locfileid: "66453535"
---
# <a name="azure-stack-hci-overview"></a>Přehled služby Azure Stack HCL

>To platí pro: Windows Server 2019

Azure Stack HCL je ověřen konvergované na hyper v Windows serveru 2019 cluster, který používá ke spuštění virtualizované úlohy v místním hardwaru. Také v případě potřeby můžete připojit ke službám Azure pro cloudové zálohování, obnovení webu a další. Řešení Azure Stack HCL Microsoft ověření hardwaru můžete zajistit optimální výkon a spolehlivost a zahrnují podporu pro technologie, jako jsou jednotky NVMe, trvalé paměti a sítě paměti vzdálený přímý přístup do (počítače RDMA).

Azure Stack HCL je řešení, která kombinuje několik produktů:

- Hardware od partnera pro výrobce OEM

- Edice Windows Server 2019 Datacenter

- Windows Admin Center

- Služby Azure (volitelné)

![Azure Stack HCL je hyperkonvergovaného řešení od Microsoftu k dispozici ze široké škály hardwarových partnerů.](media/azure-stack-hci/azure-stack-hci-solution.png)

Azure Stack HCL je hyperkonvergovaného řešení od Microsoftu k dispozici ze široké škály hardwarových partnerů. Zvažte následující scénáře hyperkonvergovaného řešení, které vám pomohou určit, jestli HCL Azure Stack je řešení, které nejlépe vyhovuje vašim potřebám:

- **Aktualizujte stárnutí hardwaru.** Nahraďte starší servery a infrastruktury úložiště a spouštění Windows a Linuxem virtuální počítače v místním prostředí a na hranici s existující IT dovednosti a nástroje.

- **Konsolidace virtualizované úlohy.** Konsolidovat starší verze aplikací na účinné, hyperkonvergovaného infrastruktury. Pronikněte do stejné typy efektivitu cloudu používá ke spouštění velkokapacitní datová centra, jako je například Microsoft Azure.

- **Připojení k Azure kvůli hybridním cloudovým službám.** Zjednodušte přístup ke cloudovým management a služby zabezpečení v Azure, včetně zálohování dat mimo pracoviště, site recovery, monitorování založené na cloudu a další.

## <a name="the-azure-stack-family"></a>Řada Azure Stack

HCL Azure Stack je součástí sady Azure a řady Azure Stack pomocí stejného softwarově definované výpočetní prostředky, úložiště a sítě softwaru jako služby Azure Stack. Tady je stručný přehled různých řešení:

- [Azure](https://azure.microsoft.com) -využívat služby veřejného cloudu
- [Azure Stack](azure-stack-overview.md) -provozovat místní služby cloud services
- [Azure Stack HCL](https://azure.microsoft.com/overview/azure-stack/hci) -spustit virtualizované aplikace v místním, volitelné připojení k Azure

![Azure a Azure Stackem spouštějí cloudové služby, když probíhá HCL Azure Stack virtualizovaných aplikací v místním prostředí](media/azure-stack-hci/azure-family.png)

|Azure: Využití veřejných cloudových služeb|Azure Stack: Provoz cloudových služeb v místním prostředí|Azure Stack HCL: Virtualizované aplikace v místním spuštění|
|-----------------|-----------------|-----------------|
|Na vyžádání, samoobslužné výpočetní prostředky k migraci a modernizovat existující aplikace a vytvářet nové aplikace nativní pro cloud.|Sestavení a spouštět cloudové aplikace na hraničních zařízeních, při odpojení nebo se zákonnými požadavky pomocí konzistentní vzhledem k aplikacím Azure services v místním.| Spustit virtualizované aplikace místní, nahraďte a konsolidovat stárnoucích serverové infrastruktury a připojení k Azure pro cloudové služby.|
|Více než 100 služeb dostupných v 54 oblastech po celém světě|Azure virtuálních počítačů pro Windows a Linux, Azure Web Apps a Functions, Azure Key Vault, Azure Resource Manageru, Azure Marketplace, kontejnery, Azure IoT a služby Event Hubs, nástrojů pro správu (plány, nabídky, RBAC)|Ověřit HCL řešení používá technologii Hyper-V a prostory úložiště – přímé s 2019 serveru systému Windows a Windows Admin Center pro správu a integrovaný přístup ke službám Azure.|

Další informace:

- Další informace najdete na naší [Azure Stack HCL](https://azure.microsoft.com/overview/azure-stack/hci) řešení pro web.
- Podívejte se na experty Microsoftu Jan Woolsey a Vijay Tewari [nová řešení Azure Stack HCL](https://aka.ms/AzureStackOverviewVideo).

## <a name="hyperconverged-efficiencies"></a>Hyperkonvergovaného efektivity

Řešení Azure Stack HCL dejte dohromady vysoce virtualizované výpočetní prostředky, úložiště a sítě na standardní x86 servery a součásti. Kombinování prostředky ve stejném clusteru usnadňuje můžete nasadit, spravovat a škálovat. Můžete spravujte pomocí podle vaší volby příkazového řádku služby automation nebo Windows Admin Center.

Dosažení špičkový výkon virtuálních počítačů pro vaše aplikace serveru s Hyper-V, základní hypervisoru technologie v cloudu společnosti Microsoft a technologii Storage Spaces Direct s integrovanou podporou pro NVMe, trvalé paměti a sítě paměti vzdálený přímý přístup do (počítače RDMA).

Pomáhají aplikace a data stíněných virtuálních počítačů, microsegmentation sítě a nativní šifrování.

## <a name="hybrid-capabilities"></a>Hybridní možnosti

Můžete využít výhod cloudu a místních spolupracuje s ověřovacími platformu infrastruktury hyperkonvergovaného ve veřejném cloudu. Váš tým můžete začít vytvářet dovednosti v oblasti cloudu díky integrované integraci do správy služby infrastruktury Azure:

- Azure Site Recovery pro vysokou dostupnost a zotavení po havárii jako služba (DRaaS).

- Azure Monitor, centrála sledovat, co se děje v různých aplikacích, síť a infrastrukturu, pomocí pokročilé analýzy zařízení využívající AI.

- Disk s kopií cloudu, do Azure můžete používat jako nenáročné zkrácené pro kvorum clusteru.

- Azure Backup pro ochranu dat mimo pracoviště a chrání před ransomwarem.

- Azure Update Management pro posouzení aktualizací a nasazení pro virtuální počítače Windows spuštěné v Azure a místní aktualizací.

- Azure síťový adaptér pro připojení místní prostředky pomocí vašich virtuálních počítačů v Azure prostřednictvím sítě VPN point-to-site.

- Synchronizujte souborového serveru s využitím cloudu pomocí Azure File Sync.

Podrobnosti najdete v tématu [připojení Windows serveru do služby Azure hybrid](https://docs.microsoft.com/windows-server/manage/windows-admin-center/azure/index).

## <a name="management-tools-and-system-center"></a>Nástroje pro správu a System Center

Azure Stack HCL používá stejné virtualizace a softwarově definovaného úložiště a sítě softwaru jako služby Azure Stack. S Azure Stack HCL však mít práva správce na cluster a můžete spravovat všechny její technologie přímo:

- [Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/hyper-v-on-windows-server)
- [Prostory úložiště s přímým přístupem](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [Softwarově definované sítě](https://docs.microsoft.com/windows-server/networking/sdn/)
- [Clustering převzetí služeb při selhání](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)

Ke správě těchto technologií, můžete použít následující nástroje pro správu:

- [Windows Admin Center](https://docs.microsoft.com/windows-server/manage/windows-admin-center/overview)
- [System Center](https://www.microsoft.com/cloud-platform/system-center)
- [PowerShell](https://docs.microsoft.com/powershell/?view=powershell-6)
- Například nástroje pro další správu [správce serveru](https://docs.microsoft.com/windows-server/administration/server-manager/server-manager)a modul snap in konzoly MMC
- Nástrojů od jiných výrobců, jako jsou společnosti 5Nine správce

Pokud se rozhodnete použít k nasazení a správě infrastruktury System Center, budete používat System Center Virtual Machine Management (VMM) a System Center Operations Manager. V nástroji VMM zřizovat a spravovat prostředky potřebné k vytvoření a nasazení virtuálních počítačů a služeb do privátních cloudů. S nástrojem Operations Manager monitorovat služby, zařízení a operace napříč vaší organizace k identifikaci problémů okamžitě zasáhnout.

## <a name="hardware-partners"></a>Hardwarových partnerů

Můžete zakoupit ověřené řešení HCL Azure Stack, na kterých běží Windows Server 2019 15 partnerů. Upřednostňovaným partnerem Microsoftu vám pomůže zprovoznit bez dlouhého času návrhu a sestavení. Také nabízejí jediný bod kontaktu pro implementaci a služby podpory.

Přejděte [HCL Azure Stack web](https://azure.microsoft.com/overview/azure-stack/hci) zobrazíte naše 70 Azure Stack HCL řešení aktuálně k dispozici od těchto partnerů společnosti Microsoft: ASUS Axellio, bluechip, DataON, Dell EMC, Fujitsu, HPE, Hitachi, Huawei, Lenovo, NEC, primeLine řešení, QCT, SecureGUARD a Supermicro.

## <a name="faq"></a>Nejčastější dotazy

### <a name="what-do-azure-stack-and-azure-stack-hci-solutions-have-in-common"></a>Co řešení pro Azure Stack a Azure Stack HCL mají společné?

Řešení Azure Stack HCL funkce stejné technologie Hyper-V na základě softwarově definované compute, storage a síťové technologie jako Azure Stack. Obě nabídky kritériím důkladné testování a ověřování zajistit spolehlivost a kompatibilitu s platformou základní hardware.

### <a name="how-are-they-different"></a>Jaký je mezi nimi rozdíl?

Pomocí služby Azure Stack spustíte cloudových služeb v místním prostředí. Spustíte Azure IaaS a PaaS služby místních konzistentně vytvářet a spouštět cloudové aplikace kdekoli a spravovaných Azure portal na pracovištích.

S Azure Stack HCL, spustíte virtualizované úlohy v místním spravované pomocí Windows Admin Center a Windows Server známé nástroje. Můžete volitelně připojit k Azure pro hybridní scénáře, například cloudové site recovery, monitorování a další.

### <a name="why-is-microsoft-bringing-its-hci-offering-to-the-azure-stack-family"></a>Proč je Microsoft přináší jeho HCL nabídky řady Azure Stack?

Technologie společnosti Microsoft hyperkonvergovaného už základ pro Azure Stack.

Mnozí zákazníci Microsoftu mají komplexní IT prostředí a Naším cílem je poskytnout řešení, které splňují je tam, kde jsou se správnou technologií pro správná obchodní potřebují. Azure Stack HCL jsou výsledkem vývoje řešení založených na Windows serveru 2016 Windows Server Software-Defined (WSSD), dříve k dispozici od našich hardwarových partnerů. Jsme načtení do služby Azure Stack řady vzhledem k tomu, že jsme začali nabízí nové možnosti, které testy bezproblémového připojení s Azure, služby pro správu infrastruktury.

### <a name="does-azure-stack-hci-need-to-be-connected-to-azure"></a>Azure Stack HCL musí být připojené k Azure?

Ne, je volitelný. Můžete využít integraci s Azure pro hybridní scénáře, například odlehlého zálohování a zotavení po havárii a monitorování a aktualizace správy založené na cloudu, ale jsou volitelné. Je ke spuštění odpojený od Internetu žádný problém.

### <a name="how-does-azure-stack-hci-relate-to-windows-server"></a>Jak Azure Stack HCL souvisí s Windows Server?

2019 systému Windows Server je základem pro téměř každý produkt Azure. Všechny funkce hodnotu můžete i nadále dodávání a podporované ve Windows serveru. Azure Stack HCL je doporučený způsob, jak nasadit HCL on-premises Microsoft ověření hardwaru od našich partnerů.

### <a name="can-i-upgrade-from-azure-stack-hci-to-azure-stack"></a>Můžu upgradovat z Azure Stack HCL do služby Azure Stack? 

Ne, ale zákazníci můžete migrovat své úlohy z Azure Stack HCL do služby Azure Stack nebo v Azure.

### <a name="what-azure-services-can-i-connect-to-azure-stack-hci"></a>Jaké služby Azure můžete připojit k Azure Stack HCL?

Aktualizovaný seznam Azure služeb, které se můžete připojit HCL Azure Stack, přejděte k části [připojení Windows serveru do služby Azure hybrid](https://docs.microsoft.com/windows-server/manage/windows-admin-center/azure/index).

### <a name="how-does-the-cost-of-azure-stack-hci-compare-to-azure-stack"></a>Jak náklady na Azure Stack HCL porovnání služby Azure Stack? 

Azure Stack se prodává jako plně integrovaný systém, který zahrnuje služby a podporu. Azure Stack si můžete zakoupit jako systém, která spravujete, nebo jako plně spravovaná služba od našich partnerů. Kromě základního systému služby Azure, které běží na Azure Stack nebo v Azure se prodávají na základě platit jako využití.

Řešení Azure Stack HCL podle tradiční nákupní model. Prověřený hardware si můžete zakoupit od partnerů Azure Stack HCL a z různých kanálů existující software (Windows Server. 2019 Datacenter edition s možnostmi softwarově definovaného datového centra a Windows Admin Center). Pro služby Azure, které můžete použít s Windows Admin Center platíte s předplatným Azure.

### <a name="how-do-i-buy-azure-stack-hci-solutions"></a>Jak můžu si koupit Azure Stack HCL řešení?

Postupujte následovně:

1. Koupit Microsoft ověřit hardwarovém systému od svého partnera upřednostňované hardwaru.
1. Nainstalujte Windows Server. 2019 Datacenter edition a Windows Admin Center pro správu a umožňuje připojení k Azure pro cloudové služby
1. Volitelně pomocí účtu Azure pro připojení služby správy a zabezpečení založené na cloudu pro vaše úlohy.

![Koupit Azure Stack HCL řešení, zvolte partnera hardware a konfiguraci, která nejlépe vyhovuje vašim potřebám.](media/azure-stack-hci/buying-azure-stack-hci.png)

## <a name="compare-azure-stack-and-azure-stack-hci"></a>Porovnání služby Azure Stack a Azure Stack HCL

Jak vaše organizace digitálně transformuje, můžete zjistit, můžete přesunout rychleji s využitím veřejných cloudových služeb vyvíjet moderní architektury a aktualizovat starší verze aplikací. Z důvodů, které zahrnují technologické a zákonné překážky, ale mnoho úloh musí zůstat místní. Následující tabulka vám pomůže určit strategii, kterou Microsoft hybridní cloud poskytuje, co je třeba kde je třeba ho, zajištění cloudových inovací pro úlohy, ať jsou kdekoli.

|Azure Stack|HCI služby Azure Stack|
|--------|-------|
|Nové dovednosti, inovativní procesy|Stejné dovednosti, zkušenosti procesy|
|Služby Azure ve vašem datovém centru|Připojte svoje datacentrum do služby Azure|

### <a name="when-to-use-azure-stack"></a>Kdy použít službu Azure Stack

|Azure Stack|HCI služby Azure Stack|
|--------|-------|
|Azure Stack využívejte pro samoobslužné služby infrastruktury jako služba (IaaS), se silnou izolaci a sledování využití přesné a vrácení peněz pro více tenantů společně umísťovat. Ideální pro poskytovatele služeb a podnikových privátních cloudů. Šablony na webu Azure Marketplace.|Azure Stack HCL nepodporuje nativně vynutit nebo zadejte pro více tenantů.|
|Použití Azure Stack pro vývoj a spouštění aplikací, které závisí na službách Platform-as-a-Service (PaaS), jako jsou Web Apps, Functions, nebo Event Hubs na místě. Tyto služby jsou spuštěny ve službě Azure Stack přesně jako v Azure, poskytuje prostředí pro vývoj a modulu runtime konzistentním hybridním.|Azure Stack HCL nespustí služby PaaS v místním prostředí.
|Modernizace nasazení aplikací a operace s postupy DevOps, jako je infrastruktura jako kód, průběžná integrace a průběžného nasazování (CI/CD) a pohodlné funkce, jako jsou konzistentní vzhledem k aplikacím Azure rozšíření virtuálních počítačů pomocí služby Azure Stack. Ideální pro týmy, které pro účely vývoje a DevOps.|Azure Stack HCL nativně neobsahuje žádné nástroje DevOps.

### <a name="when-to-use-azure-stack-hci"></a>Kdy použít Azure Stack HCL

|Azure Stack|HCI služby Azure Stack|
|---------------|---------------|
|Azure Stack vyžaduje minimální 4 uzly a vlastní síťové přepínače.|Použití Azure Stack HCL pro minimální nároky na místo pro vzdálená pracoviště a větve. Začněte s pouze 2 uzly serveru a switchless back-to-back sítě pro jednoduchost ve špičce a cenovou dostupnost. Hardware nabídky start v jednotkách se 4, 64 GB paměti, a v části $10 tis. / uzel.
|Azure Stack omezí možnosti konfigurace: Hyper-V a funkci konzistenci v rámci Azure.|Použití Azure Stack HCL no-frills virtualizace Hyper-V pro klasické podnikové aplikace, jako je Exchange, SharePoint a SQL Server a Virtualizovat role Windows serveru jako souborový Server DNS, DHCP, služby IIS a AD. Neomezený přístup ke všem funkcím Hyper-V, jako jsou stíněné virtuální počítače.|
|Azure Stack nezveřejňuje tyto infrastruktury technologie.|Pomocí softwarově definované infrastruktury namísto stárnoucích polí úložiště nebo síťová zařízení, bez hlavní rearchitecture použijte HCL Azure Stack. Integrované technologie Hyper-V, prostory úložiště – přímé a softwarově definované sítě (SDN) jsou přímo přístupné a spravovatelné.|