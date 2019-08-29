---
title: Co je ASDK? | Dokumenty Microsoft
description: Přečtěte si o Azure Stack Development Kit (ASDK) a o tom, jak se používá k vyhodnocení Azure Stack.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.date: 02/08/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: 50a08480a098c9ca5a6d50e94dd6dc7dbfc78b09
ms.sourcegitcommit: e8f7fe07b32be33ef621915089344caf1fdca3fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70118626"
---
# <a name="what-is-the-asdk"></a>Co je ASDK?
[Microsoft Azure Stack](../operator/azure-stack-overview.md) rozsahy integrovaných systémů je velikost z uzlů 4-16 a jsou společně podporovány hardwarovým partnerem a společností Microsoft. Pomocí Azure Stack integrovaných systémů můžete povolit nové scénáře pro produkční úlohy. Pokud jste Azure Stack operátor, který spravuje infrastrukturu integrovaných systémů a nabízí služby, přečtěte si naši [dokumentaci k operátorovi](/azure-stack/operator).

Azure Stack Development Kit (ASDK) je nasazení Azure Stack s jedním uzlem, které můžete **zdarma**stáhnout a používat. Všechny součásti ASDK jsou nainstalovány na virtuálních počítačích běžících na jednom hostitelském počítači, který musí splňovat nebo překročit [minimální požadavky na hardware](asdk-deploy-considerations.md#hardware). ASDK je určeno k poskytování prostředí, ve kterém můžete vyhodnotit Azure Stack a vyvíjet moderní aplikace pomocí rozhraní API a nástrojů konzistentních s Azure v neprodukčním prostředí. 

> [!IMPORTANT]
> ASDK není určena pro použití nebo podporu v produkčním prostředí.

Vzhledem k tomu, že všechny součásti ASDK jsou nasazeny na jednom hostitelském počítači, jsou k dispozici omezené fyzické prostředky. S ASDK nasazeními jsou virtuální počítače infrastruktury Azure Stack i klientské počítače na stejném serveru. Tato konfigurace není určená pro hodnocení škálování nebo výkonu.

ASDK je navržený tak, aby poskytoval hybridní cloudové prostředí konzistentní s Azure pro:
- **Správci** (Azure Stack operátory): ASDK je skvělým prostředkem k vyhodnocení a získání informací o dostupných službách Azure Stack.
- **Vývojáři**: ASDK se dá použít k vývoji hybridních nebo moderních aplikací v místním prostředí (vývoj a testování). Tato flexibilita nabízí možnost opakování vývoje před nasazením Azure Stack v produkčním prostředí nebo spolu s nimi.

Podívejte se na toto krátké video, kde se dozvíte víc o ASDK:

> [!VIDEO https://www.youtube.com/embed/dbVWDrl00MM]


## <a name="asdk-and-multi-node-azure-stack-differences"></a>ASDK a více uzlů Azure Stack rozdíly
Nasazení ASDK s jedním uzlem se liší od nasazení Azure Stack s více uzly, a to několika důležitými způsoby:

|Popis|ASDK|Azure Stack s více uzly|
|-----|-----|-----|
|**Škálování**|Všechny komponenty jsou nainstalovány v počítači serveru s jedním uzlem.|Velikost může být v rozsahu od 4-16 uzlů.|
|**Odolnost**|Konfigurace s jedním uzlem neposkytuje vysokou dostupnost.|Podporují se možnosti [vysoké dostupnosti](../operator/azure-stack-overview.md#providing-high-availability) .|
|**Sítě**|Hostitel ASDK směruje všechny síťové přenosy ASDK. Neexistují žádné další požadavky na přepínač.|Složitější [infrastruktura síťového směrování](../operator/azure-stack-network.md#network-infrastructure) v nasazeních s více uzly je nezbytná, včetně přepínačů "rozvaděče", řadiče pro správu základní desky (BMC) a hraniční sítě (síť datacenter).|
|**Proces opravy a aktualizace**|Chcete-li přejít na novou verzi nástroje ASDK, je nutné znovu nasadit ASDK na hostitelském počítači ASDK.|Proces [opravy a aktualizace](../operator/azure-stack-updates.md) , který se používá k aktualizaci nainstalované verze Azure Stack.|
|**Podpora**|Fórum MSDN Azure Stack. Podpora zákaznických služeb a podpory společnosti Microsoft (CSS ) není dostupná pro neprodukční prostředí.|[MSDN Azure Stack Fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStack) a plná podpora šablon stylů CSS.|
| | |

## <a name="learn-about-available-services"></a>Další informace o dostupných službách
Jako operátor Azure Stack potřebujete znát, které služby můžete uživatelům zpřístupnit. Azure Stack podporuje podmnožinu služeb Azure a seznam podporovaných služeb se bude dál vyvíjet v průběhu času.

### <a name="foundational-services"></a>Základní služby
Ve výchozím nastavení Azure Stack zahrnuje následující "základní služby" při nasazení rozhraní ASDK:
- Compute
- Storage
- Sítě
- Key Vault

S těmito základními službami můžete uživatelům s minimální konfigurací nabídnout infrastrukturu jako službu (IaaS).

### <a name="additional-services"></a>Další služby
V současné době jsou podporovány následující další služby platformy jako služby (PaaS):
- App Service
- Azure Functions
- Databáze SQL a MySQL

> [!NOTE]
> Tyto služby vyžadují další konfiguraci, aby je bylo možné zpřístupnit uživatelům, a nejsou k dispozici ve výchozím nastavení při instalaci ASDK.

## <a name="service-roadmap"></a>Plán služby
Azure Stack bude dál přidávat podporu dalších služeb Azure. Další informace o tom, co se chystá Azure Stack, najdete v článku [přehled Azure Stack](https://azure.microsoft.com/roadmap/?tag=azure-stack). 


## <a name="next-steps"></a>Další postup
Chcete-li začít s vyhodnocováním Azure Stack, je třeba nejprve [Stáhnout nejnovější ASDK](asdk-download.md) a připravit hostitelský počítač ASDK. Pak můžete nainstalovat ASDK a přihlásit se k portálům pro správu a uživatele a začít používat Azure Stack.