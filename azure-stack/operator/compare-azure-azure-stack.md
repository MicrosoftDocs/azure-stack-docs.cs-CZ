---
title: Porovnání Azure Stack a globálních Azure | Microsoft Docs
description: Přečtěte si, jak Microsoft poskytuje Azure a Azure Stack rodinu služeb v jednom ekosystému Azure.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 05/03/2019
ms.author: justinha
ms.reviewer: unknown
ms.custom: ''
ms.lastreviewed: 03/29/2019
ms.openlocfilehash: 1a847e842dff8b8b2b3032ded2466402897a3ae4
ms.sourcegitcommit: d9430072dd96ae305101da6d8a47d6c23a0a64c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74995599"
---
# <a name="differences-between-global-azure-azure-stack-and-azure-stack-hci"></a>Rozdíly mezi globálním Azure, Azure Stack a Azure Stack HCL

Microsoft poskytuje Azure a Azure Stackovou rodinu služeb v jednom ekosystému Azure. Používejte stejný aplikační model, samoobslužné portály a rozhraní API s Azure Resource Manager k poskytování cloudových funkcí, ať už vaše firma používá globální Azure nebo místní prostředky.

Tento článek popisuje globální možnosti Azure, Azure Stack a Azure Stack HCI a poskytuje běžná doporučení pro scénáře, která vám pomůžou s tím, že vám usnadní poskytování cloudových služeb Microsoftu pro vaši organizaci.

![Přehled ekosystému Azure](./media/compare-azure-azure-stack/azure-family.png)

## <a name="global-azure"></a>Globální Azure

Microsoft Azure je neustále se rozšiřující sada cloudových služeb, které pomáhají vaší organizaci překonávat překážky v podnikání. Je to volnost při sestavování, správě a nasazování aplikací v obrovské globální síti pomocí vašich oblíbených nástrojů a architektur.

Globální Azure nabízí více než 100 služeb dostupných v 54 oblastech po celém světě. Aktuální seznam globálních služeb Azure najdete v tématu [*Dostupné produkty v jednotlivých oblastech*](https://azure.microsoft.com/regions/services). Služby, které jsou dostupné v Azure, jsou uvedené podle kategorie a taky jestli jsou všeobecně dostupné nebo dostupné ve verzi Preview.

Další informace o globálních službách Azure najdete v tématu [Začínáme s Azure](https://docs.microsoft.com/azure/#pivot=get-started&panel=get-started1).

## <a name="azure-stack"></a>Azure Stack

Azure Stack je rozšířením Azure, které přináší flexibilitu a inovace cloud computingu do místního prostředí. Nasazená místně, Azure Stack se dají použít k poskytování služeb konzistentních v Azure, které se připojují k Internetu (a Azure) nebo v odpojených prostředích bez připojení k Internetu. Azure Stack používá stejné základní technologie jako globální Azure, což zahrnuje základní součásti infrastruktury jako služby (IaaS), softwaru typu software jako služba (SaaS) a volitelné funkce PaaS (Platform as a Service), včetně:

- Virtuální počítače Azure pro Windows a Linux
- Funkce a Web Apps Azure
- Azure Key Vault
- Azure Resource Manager
- Azure Marketplace
- Containers
- Nástroje pro správu (plány, nabídky, RBAC atd.)

PaaS možnosti služby Azure Stack jsou volitelné, protože Azure Stack není provozována společností Microsoft, je provozována našimi zákazníky. To znamená, že pokud jste připraveni k abstrakci základní infrastruktury a procesů od koncového uživatele, můžete nabízet libovolné služby PaaS, které chcete koncovým uživatelům nabídnout. Azure Stack ale obsahuje několik volitelných poskytovatelů služeb PaaS, včetně databází App Service, SQL Database a MySQL. Ty se doručují jako poskytovatelé prostředků, takže jsou připravené pro více tenantů, které se aktualizují v čase se standardními aktualizacemi Azure Stack, viditelné na portálu Azure Stack a dobře integrované s Azure Stack.

Kromě poskytovatelů prostředků popsaných výše jsou k dispozici další služby PaaS a testovány jako [Azure Resource Manager řešení založených na šablonách](https://github.com/Azure/AzureStack-QuickStart-Templates) , která běží v IaaS, ale jste jako operátor Azure Stack mohou nabízet uživatele jako PaaS služby uživatelům, včetně těchto:

- Service Fabric
- Služba kontejneru Kubernetes
- Ethereum Blockchain
- Cloud Foundry

### <a name="example-use-cases-for-azure-stack"></a>Příklady případů použití pro Azure Stack:

- Finanční modelování
- Data o klinických a deklaracích
- Analýza zařízení IoT
- Optimalizace maloobchodního sortimentu
- Optimalizace dodavatelských řetězců
- Průmyslové IoT
- Prediktivní údržba
- Inteligentní město
- Zapojení občanů

Přečtěte si další informace o Azure Stack v [Azure Stack](azure-stack-overview.md).

## <a name="azure-stack-hci"></a>HCI služby Azure Stack

[Azure Stack řešení HCI](azure-stack-hci-overview.md) vám umožní spustit virtuální počítače místně a snadno se připojit k Azure pomocí řešení konvergované infrastruktury (HCI). Sestavujte a spouštějte cloudové aplikace s využitím konzistentních služeb Azure v místním prostředí a zajistěte splnění legislativních a technických požadavků. Kromě spouštění virtualizovaných aplikací v místním prostředí Azure Stack HCL umožňuje nahradit a konsolidovat infrastrukturu serveru pro stárnutí a připojit se k Azure pro Cloud Services pomocí centra pro správu systému Windows.

Azure Stack HCI poskytují ověřená řešení HCI založená na technologii Hyper-V a Prostory úložiště s přímým přístupem s Windows serverem 2019 Software-Defined Datacenter (SDDC). Centrum pro správu Windows se používá ke správě a integrovanému přístupu ke službám Azure, jako jsou:

- Azure Backup
- Azure Site Recovery
- Azure Monitor a aktualizace

Aktualizovaný seznam služeb Azure, ke kterým se můžete připojit Azure Stack HCL, najdete v tématu [připojení Windows serveru k Azure Hybrid Services](https://docs.microsoft.com/windows-server/azure-hybrid-services/index).

### <a name="example-use-cases-for-azure-stack-hci"></a>Příklady případů použití pro Azure Stack HCI
- Systémy vzdálené sady nebo pobočky
- Konsolidace Datacenter
- Infrastruktura virtuálních klientských počítačů
- Kritická podniková infrastruktura
- Nižší náklady na úložiště
- Vysoká dostupnost a zotavení po havárii v cloudu
- Podnikové aplikace, jako je SQL Server

Navštivte [web Azure Stack HCL](https://azure.microsoft.com/overview/azure-stack/hci/) pro zobrazení 70 + Azure Stack HCL aktuálně dostupných od partnerů Microsoftu.

## <a name="next-steps"></a>Další kroky

[Základy správy Azure Stack](azure-stack-manage-basics.md)

[Rychlý Start: použití portálu pro správu Azure Stack](azure-stack-manage-portals.md)
