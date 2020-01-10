---
title: Porovnání centra Azure Stack a globální služby Azure | Microsoft Docs
description: Přečtěte si, jak Microsoft poskytuje Azure a řadu služeb Azure Stack hub v jednom ekosystému Azure.
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
ms.openlocfilehash: a3930a0259ba71d4fabce99a3cc1168d44d139bd
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75812599"
---
# <a name="differences-between-global-azure-azure-stack-hub-and-azure-stack-hub-hci"></a>Rozdíly mezi globálním úložištěm Azure, centra Azure Stack a Azure Stackm v rámci centra HCI

Microsoft poskytuje Azure a Azure Stackovou rodinu centra pro služby v jednom ekosystému Azure. Používejte stejný aplikační model, samoobslužné portály a rozhraní API s Azure Resource Manager k poskytování cloudových funkcí, ať už vaše firma používá globální Azure nebo místní prostředky.

Tento článek popisuje globální možnosti služby Azure, centra Azure Stack hub a Azure Stack hub pro prostředí HCI a poskytuje běžná doporučení pro scénáře, která vám pomůžou vydávat nejlepší možnosti pro doručování cloudových služeb Microsoftu pro vaši organizaci.

![Přehled ekosystému Azure](./media/compare-azure-azure-stack/azure-family.png)

## <a name="global-azure"></a>Globální Azure

Microsoft Azure je neustále se rozšiřující sada cloudových služeb, které pomáhají vaší organizaci překonávat překážky v podnikání. Je to volnost při sestavování, správě a nasazování aplikací v obrovské globální síti pomocí vašich oblíbených nástrojů a architektur.

Globální Azure nabízí více než 100 služeb dostupných v 54 oblastech po celém světě. Aktuální seznam globálních služeb Azure najdete v tématu [*Dostupné produkty v jednotlivých oblastech*](https://azure.microsoft.com/regions/services). Služby, které jsou dostupné v Azure, jsou uvedené podle kategorie a taky jestli jsou všeobecně dostupné nebo dostupné ve verzi Preview.

Další informace o globálních službách Azure najdete v tématu [Začínáme s Azure](https://docs.microsoft.com/azure/#pivot=get-started&panel=get-started1).

## <a name="azure-stack-hub"></a>Azure Stack Hub

Centrum Azure Stack je rozšířením Azure, které přináší flexibilitu a inovace cloud computingu do místního prostředí. Služba Azure Stack hub nasazená místně se dá použít k poskytování služeb konzistentních v Azure, které se připojují k Internetu (a Azure) nebo v odpojených prostředích bez připojení k Internetu. Centrum Azure Stack používá stejné základní technologie jako globální Azure, což zahrnuje základní součásti infrastruktury jako služby (IaaS), softwaru typu software jako služba (SaaS) a volitelné funkce PaaS (Platform as a Service), včetně:

- Virtuální počítače Azure pro Windows a Linux
- Funkce a Web Apps Azure
- Azure Key Vault
- Azure Resource Manager
- Azure Marketplace
- Kontejnery
- Nástroje pro správu (plány, nabídky, RBAC atd.)

Možnosti PaaS centra Azure Stack jsou volitelné, protože Azure Stack centrum neprovozuje Microsoft – je provozuje naši zákazníci. To znamená, že pokud jste připraveni k abstrakci základní infrastruktury a procesů od koncového uživatele, můžete nabízet libovolné služby PaaS, které chcete koncovým uživatelům nabídnout. Azure Stack centrum ale obsahuje několik volitelných poskytovatelů služeb PaaS, včetně databází App Service, SQL Database a MySQL. Ty se dodávají jako poskytovatelé prostředků, takže jsou připravené pro více tenantů, které se aktualizují v čase se standardními aktualizacemi centra Azure Stack, viditelné na portálu Azure Stack hub a dobře integrované do centra Azure Stack.

Kromě poskytovatelů prostředků popsaných výše jsou k dispozici další služby PaaS a testovány jako [Azure Resource Manager řešení založených na šablonách](https://github.com/Azure/AzureStack-QuickStart-Templates) , která běží v IaaS, ale jste jako operátor centra Azure Stack ho mohou nabízet uživatelům jako PaaS služby, včetně těchto:

- Service Fabric
- Služba kontejneru Kubernetes
- Ethereum Blockchain
- Cloud Foundry

### <a name="example-use-cases-for-azure-stack-hub"></a>Příklady případů použití pro centrum Azure Stack:

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

## <a name="azure-stack-hub-hci"></a>Azure Stack centra HCI

Řešení [HCI centra Azure Stack](azure-stack-hci-overview.md) umožňují spouštět virtuální počítače místně a snadno se připojovat k Azure pomocí řešení s více sblíženými infrastrukturami (HCI). Sestavujte a spouštějte cloudové aplikace s využitím konzistentních služeb Azure v místním prostředí a zajistěte splnění legislativních a technických požadavků. Kromě spouštění virtualizovaných aplikací v místním prostředí Azure Stack rozbočovač HCI umožňuje nahradit a sjednotit infrastrukturu serveru pro stárnutí a připojit se k Azure pro Cloud Services pomocí centra pro správu systému Windows.

Služba Azure Stack hub HCI poskytuje ověřená řešení HCI založená na technologii Hyper-V a Prostory úložiště s přímým přístupem s Windows serverem 2019 Software-Defined Datacenter (SDDC). Centrum pro správu Windows se používá ke správě a integrovanému přístupu ke službám Azure, jako jsou:

- Azure Backup
- Azure Site Recovery
- Azure Monitor a aktualizace

Aktualizovaný seznam služeb Azure, ke kterým se můžete připojit Azure Stack hub pro Azure Hybrid Services, najdete v tématu [propojení Windows serveru](https://docs.microsoft.com/windows-server/azure-hybrid-services/index).

### <a name="example-use-cases-for-azure-stack-hub-hci"></a>Příklady případů použití pro Azure Stack centra HCI
- Systémy vzdálené sady nebo pobočky
- Konsolidace Datacenter
- Infrastruktura virtuálních klientských počítačů
- Kritická podniková infrastruktura
- Nižší náklady na úložiště
- Vysoká dostupnost a zotavení po havárii v cloudu
- Podnikové aplikace, jako je SQL Server

Navštivte [web Azure Stack hub HCI](https://azure.microsoft.com/overview/azure-stack/hci/) , kde můžete zobrazit 70 + Azure Stack centra pro řešení HCI, která jsou aktuálně dostupná od partnerů Microsoftu.

## <a name="next-steps"></a>Další kroky

[Základy správy centra Azure Stack](azure-stack-manage-basics.md)

[Rychlý Start: použití portálu pro správu centra Azure Stack](azure-stack-manage-portals.md)
