---
title: Rozdíly mezi globálním Azure, Azure Stack hub Azure Stack HCI
titleSuffix: Azure Stack Hub
description: Přečtěte si o rozdílech mezi globálním úložištěm Azure, Azure Stack hub a Azure Stack HCI.
author: justinha
ms.topic: overview
ms.date: 07/10/2020
ms.author: justinha
ms.reviewer: unknown
ms.lastreviewed: 03/29/2019
ms.openlocfilehash: 96730a5afb143d8a5198c07bdc5b5df14c6483bd
ms.sourcegitcommit: 0e52f460295255b799bac92b40122a22bf994e27
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/21/2020
ms.locfileid: "86866152"
---
# <a name="differences-between-global-azure-azure-stack-hub-and-azure-stack-hci"></a>Rozdíly mezi globálním Azure, Azure Stack hub a Azure Stack HCI

Microsoft poskytuje Azure a Azure Stackovou rodinu centra pro služby v jednom ekosystému Azure. Používejte stejný aplikační model, samoobslužné portály a rozhraní API s Azure Resource Manager k poskytování cloudových funkcí, ať už vaše firma používá globální Azure nebo místní prostředky.

Tento článek popisuje rozdíly mezi globálními funkcemi Azure, Azure Stack hub a Azure Stack HCI. Poskytuje běžná doporučení pro scénáře, která vám pomůžou vydávat nejlepší možnosti pro doručování cloudových služeb Microsoftu pro vaši organizaci.

![Přehled ekosystému Azure](./media/compare-azure-azure-stack/azure-family.svg)

## <a name="global-azure"></a>Globální Azure

Microsoft Azure je neustále se rozšiřující sada cloudových služeb, která vaší organizaci pomůžou splnit vaše obchodní výzvy. Je to volnost při sestavování, správě a nasazování aplikací na obrovské globální síť pomocí vašich oblíbených nástrojů a architektur.

Globální Azure nabízí více než 100 služeb dostupných v 54 oblastech po celém světě. Aktuální seznam globálních služeb Azure najdete v tématu [*Dostupné produkty v jednotlivých oblastech*](https://azure.microsoft.com/regions/services). Služby dostupné v Azure jsou uvedené podle kategorie a také podle toho, jestli jsou všeobecně dostupné nebo dostupné ve verzi Preview.

Další informace o globálních službách Azure najdete v tématu [Začínáme s Azure](/azure/?panel=get-started1&pivot=get-started).

## <a name="azure-stack-hub"></a>Azure Stack Hub

Centrum Azure Stack je rozšířením Azure, které přináší flexibilitu a inovace cloud computingu do místního prostředí. Služba Azure Stack hub nasazená místně se dá použít k poskytování služeb konzistentních v Azure, které se připojují k Internetu (a Azure) nebo v odpojených prostředích bez připojení k Internetu. Centrum Azure Stack používá stejné základní technologie jako globální Azure, což zahrnuje základní součásti infrastruktury jako služby (IaaS), softwaru typu software jako služba (SaaS) a volitelné funkce platformy jako služby (PaaS). Mezi tyto možnosti patří:

- Virtuální počítače Azure pro Windows a Linux
- Funkce a Web Apps Azure
- Azure Key Vault
- Azure Resource Manager
- Azure Marketplace
- Containers
- Nástroje pro správu (plány, nabídky, RBAC atd.)

Možnosti PaaSy centra Azure Stack jsou volitelné, protože Azure Stack hub neprovozuje Microsoft, je provozuje naši zákazníci. To znamená, že pokud jste připraveni k abstrakci základní infrastruktury a procesů od koncového uživatele, můžete nabízet libovolné služby PaaS, které chcete koncovým uživatelům nabídnout. Azure Stack centrum ale obsahuje několik volitelných poskytovatelů služeb PaaS, včetně databází App Service, SQL Database a MySQL. Ty se doručují jako poskytovatelé prostředků, takže jsou připravené pro více tenantů, které se aktualizují v čase se standardními aktualizacemi centra Azure Stack, viditelné na portálu Azure Stack hub a dobře integrované do centra Azure Stack.

Kromě poskytovatelů prostředků popsaných výše jsou k dispozici další služby PaaS a testovány jako [Azure Resource Manager řešení založených na šablonách](https://github.com/Azure/AzureStack-QuickStart-Templates) , která běží v IaaS. Jako operátor centra Azure Stack je můžete nabízet uživatelům jako služby PaaS, včetně těchto:

- Service Fabric
- Služba kontejneru Kubernetes
- Ethereum Blockchain
- Cloud Foundry

### <a name="example-use-cases-for-azure-stack-hub"></a>Příklady případů použití pro centrum Azure Stack

- Finanční modelování
- Data o klinických a deklaracích
- Analýza zařízení IoT
- Optimalizace maloobchodního sortimentu
- Optimalizace dodavatelských řetězců
- Průmyslové IoT
- Prediktivní údržba
- Inteligentní město
- Zapojení občanů

Přečtěte si další informace o centru Azure Stack v [centru Azure Stack](azure-stack-overview.md).

## <a name="azure-stack-hci"></a>Azure Stack HCI

[Azure Stack HCI](../hci/overview.md) je vysoce sblížený cluster, který používá ověřený hardware ke spouštění virtualizovaných úloh Windows a Linux, a snadno se připojit k Azure za účelem cloudového zálohování, obnovení a monitorování. V počátečním stavu založeném na Windows serveru 2019 je teď služba Azure Stack HCL dodaná jako služba Azure se správou licencí na základě předplatného a integrovanými hybridními funkcemi. I když je Azure Stack HCI založen na stejných součástech operačního systému jako Windows Server, je to zcela nová Produktová produkt zaměřená na nejlepšího hostitele virtualizace.

Azure Stack HCI používá k zajištění optimálního výkonu a spolehlivosti hardware ověřený společností Microsoft od partnera OEM. Řešení zahrnují podporu pro technologie, jako jsou NVMe jednotky, trvalá paměť a síť vzdáleného přístupu do paměti (RDMA).

### <a name="example-use-cases-for-azure-stack-hci"></a>Příklady případů použití pro Azure Stack HCI

- Systémy vzdálené sady nebo pobočky
- Konsolidace Datacenter
- Infrastruktura virtuálního klienta
- Kritická podniková infrastruktura
- Nižší náklady na úložiště
- Vysoká dostupnost a zotavení po havárii v cloudu
- Virtualizace podnikových aplikací, jako je SQL Server

Navštivte [web Azure Stack HCL](https://azure.microsoft.com/overview/azure-stack/hci/) pro zobrazení 70 + Azure Stack HCL aktuálně dostupných od partnerů Microsoftu.

## <a name="next-steps"></a>Další kroky

[Základy správy centra Azure Stack](azure-stack-manage-basics.md)

[Rychlý Start: použití portálu pro správu centra Azure Stack](azure-stack-manage-portals.md)
