---
title: Přehled řešení Azure Stack HCI
description: Azure Stack HCI je řešení clusteru s jednou konvergou (HCI), které hostuje virtualizované úlohy Windows a Linux a jejich úložiště v hybridním místním prostředí. Služba Azure Hybrid Services vylepšuje cluster s využitím funkcí, jako jsou cloudové monitorování, Site Recovery a zálohování virtuálních počítačů, a také centrální zobrazení všech Azure Stackch nasazení HCL v Azure Portal.
ms.topic: overview
author: khdownie
ms.author: v-kedow
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 12/15/2020
ms.openlocfilehash: 6eef8388367bb1bba3f419fba5b61c54fd729743
ms.sourcegitcommit: 32d77de1a554315f53473407279e464a72aa9aa1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/15/2020
ms.locfileid: "97515009"
---
# <a name="azure-stack-hci-solution-overview"></a>Přehled řešení Azure Stack HCI

> Platí pro: Azure Stack HCI, verze 20H2

Azure Stack HCI je řešení clusteru s více konvergy (HCI), které hostuje virtualizované úlohy Windows a Linux a jejich úložiště v hybridním místním prostředí. Služba Azure Hybrid Services vylepšuje cluster s využitím funkcí, jako jsou cloudové monitorování, Site Recovery a zálohování virtuálních počítačů, a také centrální zobrazení všech Azure Stackch nasazení HCL v Azure Portal. Cluster můžete spravovat pomocí stávajících nástrojů, včetně centra pro správu Windows a prostředí PowerShell.

Azure Stack HCI, verze 20H2 je teď [k dispozici ke stažení](https://azure.microsoft.com/products/azure-stack/hci/hci-download/). Je určený pro místní clustery, na kterých běží virtualizované úlohy s integrovanými připojeními hybridního cloudu. V takovém případě se Azure Stack HCI doručuje jako služba Azure a účtuje se na základě předplatného Azure. Azure Stack rozhraní HCI teď také nabízí možnost hostovat službu Azure Kubernetes. Podrobnosti najdete v tématu [Služba Azure Kubernetes na Azure Stack HCL](../aks-hci/overview.md).

Podívejte se na video o funkcích na nejvyšší úrovni Azure Stack HCL v několika minutách:

> [!VIDEO https://www.youtube.com/embed/fw8RVqo9dcs]

V jádru se Azure Stack HCI jedná o řešení, které kombinuje následující:

- Ověřený hardware od partnera OEM
- Operační systém Azure Stack HCI
- Centrum pro správu systému Windows
- Služby Azure

:::image type="content" source="media/overview/azure-stack-hci-solution.png" alt-text="Operační systém Azure Stack HCI běží nad ověřeným hardwarem, spravuje centrum pro správu systému Windows a připojuje se k Azure." border="false":::

Azure Stack HCI, verze 20H2 poskytuje nové funkce, které nejsou k dispozici v systému Windows Server, jako je například schopnost použít Centrum pro správu systému Windows k vytvoření vysoce sblíženého clusteru, který používá Prostory úložiště s přímým přístupem k vynikajícímu výkonu ceny úložiště. To zahrnuje možnost roztáhnout cluster napříč lokalitami na automatické převzetí služeb při selhání napříč lokalitami. Podrobnosti najdete [v tématu Co je nového v Azure Stack HCL](#whats-new-in-azure-stack-hci) .

## <a name="use-cases-for-azure-stack-hci"></a>Případy použití pro Azure Stack HCI

K dispozici je mnoho případů použití Azure Stack HCL, i když není určené pro nevirtualizované úlohy. Zákazníci často vyberou Azure Stack HCI v následujících scénářích:

### <a name="data-center-consolidation-and-modernization"></a>Konsolidace a modernizace datových center

Aktualizace a konsolidace hostitelů virtualizace pro Azure Stack HCI můžou zlepšit škálovatelnost a usnadnit správu a zabezpečení vašeho prostředí. Je také možné vyřadit starší úložiště SAN a snížit tak nároky na vlastnictví a celkové náklady na vlastnictví. Správa operací a systémů je zjednodušená pomocí jednotných nástrojů a rozhraní a jediného bodu podpory.

### <a name="remote-and-branch-offices"></a>Vzdálené pobočky a pobočky

Díky řešení clusterů se dvěma servery od verze nižší než $20 000 pro každé umístění Azure Stack HCI je skvělým způsobem, jak modernizovat vzdálené a firemní pobočky, prodejny a weby polí. Inovativní vnořená odolnost umožňuje svazkům zůstat online a přístupná i v případě, že dojde k několika selháním hardwaru současně. Technologie pro hostování v cloudu umožňuje používat Azure jako zjednodušený počítač pro kvorum clusteru, což znemožňuje podmínky dělení na mozek bez nákladů na třetího hostitele. Zákazníci mohou také centrálně zobrazovat vzdálená Azure Stack nasazení rozhraní HCI v Azure Portal.

### <a name="virtual-desktops"></a>Virtuální plochy

Mnoho organizací chce hostovat virtuální klienty v místním prostředí za účelem nízké latence a suverenity dat. Azure Stack HCI může poskytovat jako místní výkon.

### <a name="high-performance-virtualized-workloads"></a>Vysoce výkonné virtualizované úlohy

Azure Stack HCI může poskytovat nejlepší výkon pro databáze SQL Server a další virtualizované náročné úlohy, které vyžadují miliony IOPS úložiště nebo databázových transakcí za sekundu, a nabízí tak konzistentně nízkou latenci s NVMe SSD, robustním zásobníkem RDMA a trvalou pamětí.

## <a name="azure-integration-benefits"></a>Výhody Integrace Azure

Azure Stack HCI je jednoznačně umístěný pro hybridní infrastrukturu, což vám umožní využít výhod cloudových a místních prostředků společně a nativně monitorovat, zabezpečit a zálohovat do cloudu.

Po registraci Azure Stack clusteru HCI v Azure můžete použít počáteční Azure Portal pro:

- **Monitorování:** Zobrazte všechny Azure Stack clustery HCI v jednom globálním zobrazení, kde je můžete seskupit podle skupiny prostředků a označit je.
- **Fakturace:** Platíte za Azure Stack HCI prostřednictvím předplatného Azure.

Pracujeme na vytváření dalších možností, takže je ještě více vyladěno.

Můžete se také přihlásit k odběru dalších hybridních služeb Azure:

- **Azure Site Recovery** pro zajištění vysoké dostupnosti a zotavení po havárii jako služba (DRaaS).
- **Azure monitor** centralizované centrum, které sleduje, co se děje napříč vašimi aplikacemi, sítí a infrastrukturou – díky pokročilým analýzám, které využívají AI.
- **Disk s kopií cloudu**, aby bylo možné používat Azure jako zjednodušené rozšíření pro kvorum clusteru.
- **Azure Backup** k ochraně dat mimo lokalitu a k ochraně před ransomwarem.
- **Azure Update Management** pro posouzení aktualizací a nasazení aktualizací pro virtuální počítače s Windows běžící v Azure a v místním prostředí.
- **Síťový adaptér Azure** pro připojení prostředků v místním prostředí k virtuálním počítačům v Azure prostřednictvím sítě VPN typu Point-to-site.
- **Azure File Sync** k synchronizaci souborového serveru s cloudem.

Další informace najdete v tématu [propojení Windows serveru s Azure Hybrid Services](/windows-server/manage/windows-admin-center/azure/index).

## <a name="why-azure-stack-hci"></a>Proč Azure Stack HCI?

Azure Stack HCI je špičková integrovaná sada virtualizace postavená na prověřených technologiích, které již byly nasazeny ve velkém měřítku, včetně technologie Hyper-V, Prostory úložiště s přímým přístupem a Azure-nechte inspirovat Software-Defined Networking (SDN). Existuje mnoho důvodů, proč si zákazníci vyberou Azure Stack HCL, včetně:

- Je známý pro technologie Hyper-V a správce serveru, což jim umožňuje využívat stávající koncepty a dovednosti úložiště.
- Funguje se stávajícími procesy a nástroji datového centra, jako je Microsoft System Center, Active Directory, Zásady skupiny a PowerShell Scripting.
- Funguje s oblíbenými nástroji pro zálohování, zabezpečení a monitorování třetích stran.
- Flexibilní možnosti hardwaru umožňují zákazníkům zvolit si dodavatele s nejlepší službou a podporou v jejich zeměpisných oblastech.
- Společná podpora mezi společností Microsoft a dodavatelem hardwaru zlepšuje prostředí pro zákazníky.
- Bezproblémové, úplné aktualizace zásobníku usnadňují aktuálnost.
- Flexibilní a široká ekosystém nabízí odborníkům v oblasti IT flexibilitu, která potřebuje k sestavení řešení, které nejlépe vyhovuje jejich potřebám.

## <a name="what-you-need-for-azure-stack-hci"></a>Co potřebujete pro Azure Stack HCL

Začněte tím, že budete potřebovat:

- Cluster dvou nebo více serverů z [katalogu Azure Stack HCI](https://hcicatalog.azurewebsites.net), zakoupený od preferovaného poskytovatele hardwaru Microsoftu
- [Předplatné Azure](https://azure.microsoft.com/)
- Připojení k Internetu pro každý server v clusteru, který se může připojit prostřednictvím odchozího provozu HTTPS k dobře známým koncovým bodům Azure nejméně každých 30 dnů
- Pro roztažené clustery mezi lokalitami potřebujete minimálně 1 1 GB připojení mezi lokalitami (upřednostňuje se 25 GB připojení RDMA), s průměrnou latencí 5 MS na konci přenosu, pokud chcete provést synchronní replikaci, kde se v obou lokalitách vyskytují zápisy současně.
- Pokud plánujete používat softwarově definované sítě (SDN), budete potřebovat virtuální pevný disk (VHD) pro Azure Stack operační systém HCI pro vytváření virtuálních počítačů síťového adaptéru (viz [Plánování nasazení síťového adaptéru](concepts/network-controller.md)).

Další informace najdete v tématu [požadavky na systém](concepts/system-requirements.md). Informace o požadavcích na Azure Stack HCL pro službu Azure Kubernetes najdete v tématu [AKS požadavky na Azure Stack HCL](../aks-hci/overview.md#what-you-need-to-get-started).

## <a name="hardware-partners"></a>Hardwarové partneři

Ověřená řešení Azure Stack HCI si můžete koupit od preferovaného partnera Microsoftu, abyste mohli začít pracovat bez zdlouhavého návrhu a času sestavování. Partneři Microsoftu nabízejí také jeden kontaktní bod pro implementaci a služby podpory. Můžete si buď koupit ověřené uzly, nebo integrovaný systém, který zahrnuje předinstalované operační systémy Azure Stack HCI a také rozšíření partnerů pro aktualizace ovladačů a firmwaru.

Navštivte stránku [Azure Stack řešení HCI](https://azure.microsoft.com/overview/azure-stack/hci) nebo si projděte [Azure Stack v katalogu HCI](https://hcicatalog.azurewebsites.net) , kde můžete zobrazit 70 + Azure Stack řešení HCI, která jsou aktuálně k dispozici od partnerů Microsoftu, jako je ASUS, Axellio, Blue čip, DataON, Dell EMC, Fujitsu, HPE, Hitachi, Huawei, Lenovo, NEC, primeLine Solutions, QCT, SecureGUARD a Micro.

## <a name="software-partners"></a>Partneři softwaru

Na softwaru pracuje celá řada partnerů Microsoftu, která rozšiřuje možnosti Azure Stack HCI a zároveň umožňuje správcům IT používat známé nástroje. Další informace najdete v tématu [pomocné aplikace pro Azure Stack HCI](concepts/utility-applications.md).

## <a name="licensing-billing-and-pricing"></a>Licencování, fakturace a ceny

Vyúčtování Azure Stack HCL vychází z měsíčního předplatného na fyzický procesor, nikoli na trvalou licenci. Když se zákazníci připojí k Azure, počet použitých jader se automaticky nahraje a vyhodnotí pro účely fakturace. Náklady se neliší při spotřebě nad rámec fyzických procesorů, což znamená, že další virtuální počítače se neúčtují více a zákazníci, kteří můžou spouštět hustá virtuální prostředí, se neúčtují.

Zákazníci si můžou koupit ověřené servery od hardwarového partnera s předinstalovaným operačním systémem Azure Stack HCI nebo můžou koupit ověřené holé servery od výrobce OEM a pak se přihlásit k odběru služby Azure Stack HCI a [stáhnout Azure Stack operační systém HCI](https://azure.microsoft.com/products/azure-stack/hci/).

## <a name="the-azure-stack-family"></a>Řada Azure Stack

Azure Stack rozhraní HCI je součástí řady Azure a Azure Stack, která využívá stejný software definovaný pro výpočetní prostředky, úložiště a síťový software jako centrum Azure Stack. V následující části najdete stručný přehled různých řešení. Další informace najdete v tématu [porovnání Azure Stack ekosystému](../operator/compare-azure-azure-stack.md).

- [Azure](https://azure.microsoft.com) – využijte veřejné cloudové služby pro samoobslužné výpočetní prostředky na vyžádání k migraci a modernizovat stávajících aplikací a k vytváření nových aplikací nativních pro Cloud.
- [Azure Stack Edge](/azure/databox-online/data-box-edge-overview) – Zrychlete úlohy strojového učení a Spouštějte aplikace s podporou kontejnerů nebo virtualizovaných úloh na zařízení spravovaném pomocí cloudu.
- [Azure Stack](https://azure.microsoft.com/overview/azure-stack/hci) v prostředí HCI – spouštění virtualizovaných aplikací v místním prostředí, výměna a konsolidace infrastruktury serveru pro stárnutí a připojení k Azure pro cloudové služby.
- [Azure Stack centrum](../operator/azure-stack-overview.md) – spouštění cloudových aplikací v místním prostředí, pokud je odpojená, nebo aby splňovaly zákonné požadavky, a to s využitím konzistentních služeb Azure.

:::image type="content" source="media/overview/azure-family-updated.png" alt-text="Diagram řešení Azure Stack Family" border="false":::

## <a name="whats-new-in-azure-stack-hci"></a>Co je nového v Azure Stack HCL

Centrum pro správu systému Windows verze 2009 přidává řadu funkcí Azure Stack HCL, včetně následujících:

- **Možnosti hostování služeb Azure Kubernetes**: teď můžete nainstalovat verzi Preview [služby Azure Kubernetes Service na Azure Stack HCL](https://azure.microsoft.com/products/azure-stack/hci/hci-download/).
- **Zahrnutí softwarově definovaných sítí v Průvodci vytvořením clusteru**: Průvodce vytvořením clusteru teď obsahuje možnost nasazení funkce síťového adaptéru s [softwarově definovanými sítěmi (SDN)](concepts/software-defined-networking.md) během [vytváření clusteru](deploy/create-cluster.md#step-5-sdn-optional).
- **Vylepšení přímého přístupu do paměti vzdáleného počítače (RDMA) v Průvodci vytvořením clusteru**: Průvodce vytvořením clusteru teď může nakonfigurovat RDMA pro síťové adaptéry IWARP a roce, včetně přemostění DATACENTER (DCB).

Podrobnosti o nových funkcích najdete v tématu [oznamujeme obecnou dostupnost rozšíření pro vytváření clusteru v centru pro správu systému Windows](https://techcommunity.microsoft.com/t5/windows-admin-center-blog/announcing-general-availability-of-the-cluster-creation/ba-p/1978332).

Clustery se systémem Azure Stack HCI verze 20H2 ve srovnání s řešeními založenými na Windows serveru 2019 obsahují následující nové funkce:

- **Nové funkce v centru pro správu Windows**: Díky možnosti vytvářet a aktualizovat sblížené clustery prostřednictvím INTUITIVNÍHO uživatelského rozhraní Azure Stack HCL jednodušší než dřív.
- **Roztažené clustery pro automatické převzetí služeb při selhání**: clusterování s více lokalitami s replikací úložiště a automatickým převzetím služeb při selhání zajišťuje nativní zotavení po havárii a kontinuitu provozu.
- **Spřažení a pravidla** vzájemného spřažení: můžete je použít podobně jako způsob, jakým Azure používá zóny dostupnosti k udržení všech virtuálních počítačů a úložišť v clusterech s více doménami selhání, jako jsou například roztažené clustery.
- **Azure Portal Integration**: Azure Portal prostředí pro Azure Stack HCI je navrženo tak, aby zobrazilo všechny vaše Azure Stack clustery HCI po celém světě a nové funkce ve vývoji.
- **Akcelerace GPU pro úlohy s vysokým výkonem**: aplikace AI/ml můžou těžit z zvyšování výkonu pomocí GPU.
- **Šifrování BitLockeru**: teď můžete použít BitLocker k šifrování obsahu datových svazků v Azure Stack HCL a pomáhat státním institucím a dalším zákazníkům, aby dodržovali standardy jako FIPS 140-2 a HIPAA.
- **Vylepšená rychlost opravy prostory úložiště s přímým přístupem svazků**: Opravte svazky rychle a hladce.

Pokud chcete získat další informace o novinkách v Azure Stack HCI 20H2, podívejte se na [Toto video](https://www.youtube.com/watch?v=DPG7wGhh3sAa) z Microsoft inspirovat.

## <a name="roles-you-can-run-without-virtualizing"></a>Role, které můžete spustit bez virtualizace

Vzhledem k tomu, že Azure Stack HCI je určený jako hostitel virtualizace, ve kterém spouštíte všechny vaše úlohy ve virtuálních počítačích, Azure Stack podmínek HCI umožňuje spustit pouze to, co je potřeba pro hostování virtuálních počítačů.

To znamená, že můžete spouštět následující role serveru:

- Hyper-V
- Síťový adaptér a další součásti požadované pro softwarově definované sítě (SDN)

Ale všechny ostatní role a aplikace musí běžet v rámci virtuálních počítačů. Všimněte si, že můžete spouštět nástroje, aplikace a služby, které jsou nezbytné pro správu a stav hostovaných virtuálních počítačů.

## <a name="video-based-learning"></a>Učení na základě videa

Video o rozšířené síti Azure najdete tady:

- [Bezproblémové připojení k Azure s Windows serverem a hybridními sítěmi](https://www.youtube.com/watch?v=do2_4Y2p9dk)

Videa o původní verzi Azure Stack HCL založené na Windows serveru 2019:

- [Azure Stack a Azure Stack zobrazení přehledu HCI](https://aka.ms/AzureStackOverviewVideo)
- [Microsoft Ignite Live 2019-Začínáme s Azure Stack HCL](https://www.youtube.com/watch?v=vueHIBqNIEU)
- [Zjistit Azure Stack HCI](https://www.youtube.com/watch?v=4aGZK0Ndmh8&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=13&t=0s)
- [Modernizovat své prodejny nebo firemní pobočky pomocí Azure Stack HCI](https://www.youtube.com/watch?v=-JzLhjfkhmM&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=9&t=0s)
- [Novinky Azure Stack HCL: 45 věcí během 45 minut](https://www.youtube.com/watch?v=C5J4IEnlS_E&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=12&t=0s)
- [Rychlé zprovoznění nasazení prostředí HCI pro Azure Stack](https://www.youtube.com/watch?v=gxaPJLrWy5w&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=11&t=0s)
- [Případ zmenšení dat: odstranění duplicitních dat v Azure Stack HCI](https://www.youtube.com/watch?v=fmm4iDbDiY4&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=23&t=0s)
- [Poznámky k Dave Kawula z pole v Azure Stack HCI](https://www.youtube.com/watch?v=OXv7fLlz0ew&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=2&t=0s)

Tady je video z hybridní cloudové virtuální události:

- [Azure Stack HCI | Virtuální událost hybridního cloudu](https://www.youtube.com/watch?v=nxpoEva-R2Y)

## <a name="next-steps"></a>Další kroky

- [Stáhnout Azure Stack HCL](https://azure.microsoft.com/products/azure-stack/hci/hci-download/)
- [Vytvoření clusteru Azure Stack HCI a jeho registrace v Azure](deploy/deployment-quickstart.md)
- [Použití Azure Stack HCL s centrem pro správu Windows](get-started.md)
