---
title: Nasazení infrastruktury virtuálních klientských počítačů (VDI) na Azure Stack HCL
description: V tomto tématu najdete pokyny k plánování, konfiguraci a nasazení infrastruktury virtuálních klientských počítačů (VDI) v Azure Stack operační systém HCI.
author: JohnCobb1
ms.author: v-johcob
ms.topic: how-to
ms.date: 01/15/2021
ms.openlocfilehash: dfe22eb768b5bf661760c31c913d8c93caf590a4
ms.sourcegitcommit: 48a46142ea7bccd6c8a609e188dd7f3f6444f3c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/18/2021
ms.locfileid: "98562896"
---
# <a name="deploy-virtual-desktop-infrastructure-vdi-on-azure-stack-hci"></a>Nasazení infrastruktury virtuálních klientských počítačů (VDI) na Azure Stack HCL

>Platí pro: Azure Stack HCI, verze 20H2

V tomto tématu najdete pokyny k plánování, konfiguraci a nasazení infrastruktury virtuálních klientských počítačů (VDI) v Azure Stack operační systém HCI. Využijte své Azure Stacké investice do HCI k poskytování centralizované, vysoce dostupné, zjednodušené a zabezpečené správy pro uživatele ve vaší organizaci. Tyto pokyny vám pomůžou při povolování scénářů, jako jsou vlastní zařízení (BYOD) pro vaše uživatele, a jejich poskytování konzistentního a spolehlivého prostředí pro důležité podnikové aplikace bez omezení zabezpečení.

## <a name="overview"></a>Přehled
VDI používá serverový hardware ke spouštění stolních operačních systémů a softwarových programů na virtuálním počítači (VM). Infrastruktura virtuálních klientských počítačů tak umožňuje spouštět tradiční desktopové úlohy na centralizovaných serverech. Výhody infrastruktury virtuálních počítačů v podnikovém nastavení zahrnují udržení citlivých firemních aplikací a dat v zabezpečeném datacentru a BYOD zásady, aniž byste se museli starat o vymíchání osobních údajů pomocí podnikových assetů. Infrastruktura virtuálních klientských počítačů (VDI) také slouží jako standard pro podporu vzdálených a firemních pracovních procesů a poskytování přístupu dodavatelům a partnerům.

Azure Stack HCI nabízí optimální platformu pro VDI. Ověřené řešení Azure Stack HCI v kombinaci se [službou Vzdálená plocha (RDS)](/windows-server/remote/remote-desktop-services/welcome-to-rds) společnosti Microsoft vám umožňuje dosáhnout vysoce dostupné a vysoce škálovatelné architektury.

Kromě toho Azure Stack HCI VDI přináší následující jedinečné cloudové funkce pro ochranu zatížení a klientů infrastruktury virtuálních počítačů:
- Centrálně spravované aktualizace s využitím Azure Update Management
- Jednotná Správa zabezpečení a Rozšířená ochrana před internetovými útoky pro klienty VDI

## <a name="deploy-vdi"></a>Nasazení infrastruktury virtuálních klientských počítačů
Tato část popisuje na vysoké úrovni, jak získat hardware pro nasazení infrastruktury virtuálních klientských počítačů do Azure Stack HCL a použití centra pro správu Windows pro správu. Zahrnuje také nasazení služby RDS pro podporu infrastruktury virtuálních počítačů.

### <a name="step-1-acquire-hardware-for-vdi-on-azure-stack-hci"></a>Krok 1: získání hardwaru pro VDI na Azure Stack HCI
Nejdřív budete muset zakoupit hardware. Nejjednodušší způsob, jak to udělat, je vyhledat preferovaného poskytovatele hardwaru Microsoftu v [katalogu Azure Stack HCI](https://hcicatalog.azurewebsites.net) a koupit integrovaný systém s předinstalovaným operačním systémem Azure Stack HCI. V katalogu můžete filtrovat, abyste viděli hardware od dodavatele optimalizovaného pro tento typ úlohy. Ujistěte se, že se obraťte na svého hardwarového partnera a ujistěte se, že hardware může podporovat počet virtuálních klientů, které chcete hostovat na clusteru.

V opačném případě bude nutné nasadit operační systém Azure Stack HCI na svůj vlastní hardware. Podrobnosti o Azure Stack možností nasazení HCI a o instalaci centra pro správu systému Windows najdete v tématu [nasazení Azure Stack v operačním systému rozhraní HCI](./operating-system.md).

Dále pomocí centra pro správu systému Windows [vytvořte cluster Azure Stack HCI](./create-cluster.md).

### <a name="step-2-set-up-azure-update-management-in-windows-admin-center"></a>Krok 2: nastavení Update Management Azure v centru pro správu Windows
V centru pro správu Windows nastavte [Azure Update Management](/windows-server/manage/windows-admin-center/azure/azure-update-management) , abyste rychle vyhodnotili stav dostupných aktualizací, naplánovali požadované aktualizace a zkontrolovali výsledky nasazení za účelem ověření použitých aktualizací.

Abyste mohli začít s Azure Update Management, potřebujete předplatné, které Microsoft Azure. Pokud předplatné nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/free).

Centrum pro správu systému Windows můžete použít také k nastavení dalších hybridních služeb Azure, jako je zálohování, Synchronizace souborů, Site Recovery, připojení VPN typu Point-to-site a Azure Security Center.

### <a name="step-3-deploy-remote-desktop-services-rds-for-vdi-support"></a>Krok 3: nasazení služby Vzdálená plocha (RDS) pro podporu infrastruktury virtuálních počítačů
Po dokončení nasazení Azure Stack HCI a registraci v Azure za účelem použití Update Management jste připraveni k použití pokynů v této části k sestavení a nasazení služby RDS k podpoře infrastruktury virtuálních počítačů.

Postup při sestavování a nasazování služby RDS:
1. [Nasazení prostředí vzdálené plochy](/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure)
1. [Vytvoření kolekce relací VP](/windows-server/remote/remote-desktop-services/rds-create-collection) pro sdílení aplikací a prostředků
1. [Licence na nasazení služby RDS](/windows-server/remote/remote-desktop-services/rds-client-access-license)
1. Nasměrování uživatelů na instalaci [klienta vzdálené plochy](/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients) pro přístup k aplikacím a prostředkům
1. Přidejte zprostředkovatele připojení a hostitele relací, aby bylo možné povolit vysokou dostupnost:
    - [Horizontální navýšení kapacity existující kolekce služby RDS pomocí Hostitel relace VP farmy](/windows-server/remote/remote-desktop-services/rds-scale-rdsh-farm)
    - [Přidání vysoké dostupnosti do infrastruktury Zprostředkovatel připojení k VP](/windows-server/remote/remote-desktop-services/rds-connection-broker-cluster)
    - [Přidání vysoké dostupnosti webu VP a Brána VP webového frontu](/windows-server/remote/remote-desktop-services/rds-rdweb-gateway-ha)
    - [Nasazení Prostory úložiště s přímým přístupemho systému souborů se dvěma uzly pro úložiště UPD](/windows-server/remote/remote-desktop-services/rds-storage-spaces-direct-deployment)

## <a name="next-steps"></a>Další kroky
Další informace týkající se infrastruktury virtuálních klientských počítačů najdete v tématu [podporované konfigurace služby Vzdálená plocha](/windows-server/remote/remote-desktop-services/rds-supported-config) .
