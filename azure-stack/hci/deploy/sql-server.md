---
title: Nasazení SQL Server na Azure Stack HCI (AKS-HCI)
description: Toto téma poskytuje pokyny pro nasazení SQL Server v Azure Stack HCI.
author: JohnCobb1
ms.author: v-johcob
ms.topic: how-to
ms.date: 01/11/2021
ms.openlocfilehash: 8f93a56840d4e4410a42aafe117f6cb1eebe84b4
ms.sourcegitcommit: 0983c1f90734b7ea5e23ae614eeaed38f9cb3c9a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/19/2021
ms.locfileid: "98571527"
---
# <a name="deploy-sql-server-on-azure-stack-hci"></a>Nasazení SQL Server v Azure Stack HCI

>Platí pro: Azure Stack HCI, verze 20H2; SQL Server (všechny podporované verze)

Toto téma poskytuje pokyny k plánování, konfiguraci a nasazení SQL Server v operačním systému Azure Stack HCI. Operační systém je řešení clusteru s jednou sblíženou infrastrukturou (HCI), které hostuje virtualizované úlohy Windows a Linux a jejich úložiště v hybridním místním prostředí.

## <a name="solution-overview"></a>Přehled řešení
Azure Stack HCI nabízí vysoce dostupnou, cenově výhodnější a flexibilní platformu pro spouštění SQL Server a Prostory úložiště s přímým přístupem. Azure Stack HCI může spouštět úlohy online zpracování transakcí (OLTP), datový sklad, BI a AI a pokročilou analýzu nad velkými objemy dat.

Flexibilita platformy je zvláště důležitá pro důležité databáze. Můžete spouštět SQL Server na virtuálních počítačích, které používají systém Windows Server nebo Linux, což vám umožní konsolidovat více úloh databáze a do Azure Stack prostředí HCI přidat další virtuální počítače podle potřeby. Azure Stack HCI taky umožňuje integrovat SQL Server s Azure Site Recovery, aby poskytovala cloudová migrace, obnovení a řešení ochrany pro spolehlivá a bezpečná data vaší organizace.

## <a name="deploy-sql-server"></a>Nasazení SQL Server
Tato část popisuje na vysoké úrovni, jak získat hardware pro SQL Server v Azure Stack HCI a pomocí centra pro správu systému Windows spravovat operační systém na serverech. K dispozici jsou informace o nastavení SQL Server, ladění a ladění výkonu a používání služby Azure Hybrid Services (HA).

### <a name="step-1-acquire-hardware-from-the-azure-stack-hci-catalog"></a>Krok 1: získání hardwaru z katalogu Azure Stack HCI
Nejdřív budete muset zakoupit hardware. Nejjednodušší způsob, jak to udělat, je vyhledat preferovaného poskytovatele hardwaru Microsoftu v [katalogu Azure Stack HCI](https://hcicatalog.azurewebsites.net) a koupit integrovaný systém s předinstalovaným operačním systémem Azure Stack HCI. V katalogu můžete filtrovat, abyste viděli hardware od dodavatele optimalizovaného pro tento typ úlohy.

V opačném případě bude nutné nasadit operační systém Azure Stack HCI na svůj vlastní hardware. Podrobnosti o Azure Stack možností nasazení HCI a o instalaci centra pro správu systému Windows najdete v tématu [nasazení Azure Stack v operačním systému rozhraní HCI](./operating-system.md).

Dále pomocí centra pro správu systému Windows [vytvořte cluster Azure Stack HCI](./create-cluster.md).

### <a name="step-2-install-sql-server-on-azure-stack-hci"></a>Krok 2: instalace SQL Server na Azure Stack HCL
V závislosti na vašich požadavcích můžete SQL Server nainstalovat na virtuální počítače se systémem Windows Server nebo Linux.

Pokyny k instalaci SQL Server najdete v tématech:
- [Instalační průvodce SQL Server pro Windows](https://docs.microsoft.com/sql/database-engine/install-windows/install-sql-server?view=sql-server-ver15&preserve-view=true).
- [Pokyny k instalaci SQL Server on Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-setup?view=sql-server-ver15&preserve-view=true).

### <a name="step-3-monitor-and-performance-tune-sql-server"></a>Krok 3: SQL Server monitorování a výkonu pro Intune
Microsoft poskytuje komplexní sadu nástrojů pro monitorování událostí v SQL Server a pro optimalizaci návrhu fyzické databáze. Volba nástroje závisí na typu monitorování nebo ladění, které chcete provést.

Pokud chcete zajistit výkon a stav instancí SQL Server v Azure Stack HCI, přečtěte si téma [monitorování výkonu a ladění](https://docs.microsoft.com/sql/relational-databases/performance/performance-monitoring-and-tuning-tools?view=sql-server-ver15&preserve-view=true).

Informace o ladění SQL Server 2017 a SQL Server 2016 najdete v tématu [Doporučené aktualizace a možnosti konfigurace pro SQL Server 2017 a 2016 s vysokým výkonem úloh](https://support.microsoft.com/help/4465518/recommended-updates-and-configurations-for-sql-server).

### <a name="step-4-use-sql-server-high-availability-features"></a>Krok 4: použití funkcí vysoké dostupnosti SQL Server
Azure Stack HCI využívá [Clustering s podporou převzetí služeb při selhání Windows serveru s SQL Server](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) (WSFC) k podpoře SQL Server spouštění na virtuálních počítačích v případě selhání hardwaru. SQL Server taky nabízí [skupiny dostupnosti Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) (AG) k zajištění vysoké dostupnosti na úrovni databáze, která je navržená tak, aby pomáhala chybám aplikací a softwaru. Kromě služby WSFC a AG může Azure Stack HCI používat [vždy na instanci clusteru s podporou převzetí služeb při selhání](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server) (FCI), která je založená na [prostory úložiště s přímým přístupem](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) technologii pro sdílené úložiště.

Všechny tyto možnosti fungují s Microsoft Azure [cloudovou kopií](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness) pro řízení kvora. Pro virtuální počítače, které jsou umístěné na různých fyzických uzlech, doporučujeme používat pravidla pro [antispřažení](https://docs.microsoft.com/windows-server/failover-clustering/cluster-affinity) clusteru v rámci služby WSFC pro zajištění doby provozu SQL Server v případě selhání hostitelů při konfiguraci skupiny dostupnosti Always On.

### <a name="step-5-set-up-azure-hybrid-services"></a>Krok 5: nastavení Azure Hybrid Services
K dispozici je několik hybridních služeb Azure, které vám pomůžou zajistit zabezpečení vašich SQL Serverch dat a aplikací. [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) je zotavení po havárii jako služba (DRaaS). Další informace o použití této služby k ochraně SQL Server back-endu aplikace, aby bylo možné udržet úlohy online, najdete v tématu [Nastavení zotavení po havárii pro SQL Server](https://docs.microsoft.com/azure/site-recovery/site-recovery-sql).

[Azure Backup](https://azure.microsoft.com/services/backup/) vám umožní definovat zásady zálohování pro ochranu podnikových úloh a podporuje zálohování a obnovení konzistence SQL Server. Další informace o tom, jak zálohovat místní data SQL, najdete v tématu [Install Azure Backup Server](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup).

Alternativně můžete použít funkci [spravovaného zálohování SQL Server](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure?view=sql-server-ver15&preserve-view=true) v SQL Server ke správě zálohování služby Azure Blob Storage.

Další informace o použití této možnosti, která je vhodná pro archivaci mimo lokalitu, najdete v těchto tématech: 

- [Kurz: Použití služby Azure Blob Storage s SQL Serverem 2016](https://docs.microsoft.com/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016?view=sql-server-ver15&preserve-view=true)
- [Rychlý Start: zálohování a obnovení SQL do služby Azure Blob Storage](https://docs.microsoft.com/sql/relational-databases/tutorial-sql-server-backup-and-restore-to-azure-blob-storage-service?view=sql-server-ver15&tabs=SSMS&preserve-view=true)

Kromě těchto scénářů zálohování můžete nastavit další databázové služby, které SQL Server nabídky, včetně [Azure Data Factory](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/move-sql-azure-adf) a [Azure Feature Pack pro integrační služby (SSIS)](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-ver15&preserve-view=true).

## <a name="next-steps"></a>Další kroky
Další informace o práci s SQL Server najdete v těchto tématech:
- [Kurz: Začínáme s databázovým strojem](https://docs.microsoft.com/sql/relational-databases/tutorial-getting-started-with-the-database-engine?view=sql-server-ver15&preserve-view=true)
