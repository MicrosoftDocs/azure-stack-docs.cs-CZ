---
title: Co je Azure Stack Development Kit (ASDK)? | Dokumentace Microsoftu
description: Přečtěte si o Azure Stack Development Kit a o tom, jak se používá k vyhodnocení centra Azure Stack.
author: justinha
ms.topic: overview
ms.date: 11/27/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 11/27/2019
ms.openlocfilehash: 38aaf52936d09c999765118e36bfcde909409cdd
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86489908"
---
# <a name="what-is-the-azure-stack-development-kit-asdk"></a>Co je Azure Stack Development Kit (ASDK)?
Rozsah [integrovaných systémů centra Microsoft Azure Stack](../operator/azure-stack-overview.md) je velikost z uzlů 4-16 a jsou společně podporovány hardwarovým partnerem a společností Microsoft. Pomocí integrovaných systémů Azure Stack Hub můžete pro vaše produkční úlohy povolit nové scénáře. Pokud jste operátor centra Azure Stack, který spravuje infrastrukturu integrovaných systémů a nabízí služby, přečtěte si naši [dokumentaci k operátorovi](../operator/index.yml).

ASDK je nasazení centra Azure Stack s jedním uzlem, které můžete **zdarma**stáhnout a používat. Všechny součásti ASDK jsou nainstalovány na virtuálních počítačích běžících na jednom hostitelském počítači, který musí splňovat nebo překročit [minimální požadavky na hardware](asdk-deploy-considerations.md#hardware). ASDK má poskytovat prostředí, ve kterém můžete vyhodnotit Azure Stack hub a vyvíjet moderní aplikace pomocí rozhraní API a nástrojů konzistentních s Azure v *neprodukčním* prostředí. 

> [!IMPORTANT]
> ASDK není určena pro použití nebo podporu v produkčním prostředí.

Vzhledem k tomu, že všechny součásti ASDK jsou nasazeny na jednom hostitelském počítači, jsou k dispozici omezené fyzické prostředky. S ASDK nasazeními jsou virtuální počítače infrastruktury centra Azure Stack i klientské počítače na stejném serveru. Tato konfigurace není určená pro hodnocení škálování nebo výkonu.

ASDK je navržený tak, aby poskytoval hybridní cloudové prostředí konzistentní s Azure pro:
- **Správci** (Azure Stack operátory centra): ASDK je skvělým prostředkem k vyhodnocení a získání informací o dostupných službách Azure Stack centra.
- **Vývojáři**: ASDK se dá použít k vývoji hybridních nebo moderních aplikací v místním prostředí (vývoj a testování). Tato flexibilita nabízí možnost opětovného vývojového prostředí před nebo společně Azure Stack nasazení produkčního centra.

Podívejte se na toto krátké video, kde se dozvíte víc o ASDK:

> [!VIDEO https://www.youtube.com/embed/dbVWDrl00MM]


## <a name="asdk-and-multi-node-azure-stack-hub-differences"></a>ASDK a více uzlů Azure Stackch rozdílů centra
Nasazení ASDK s jedním uzlem se liší od nasazení na více uzlech Azure Stack v několika důležitých způsobech:

|Popis|ASDK|Rozbočovač Azure Stack s více uzly|
|-----|-----|-----|
|**Škálování**|Všechny komponenty jsou nainstalovány v počítači serveru s jedním uzlem.|Velikost může být v rozsahu od 4-16 uzlů.|
|**Odolnost**|Konfigurace s jedním uzlem neposkytuje vysokou dostupnost.|Podporují se možnosti vysoké dostupnosti.|
|**Sítě**|Hostitel ASDK směruje všechny síťové přenosy ASDK. Neexistují žádné další požadavky na přepínač.|Složitější [infrastruktura síťového směrování](../operator/azure-stack-network.md#network-infrastructure) v nasazeních s více uzly je nezbytná, včetně přepínačů "rozvaděče", řadiče pro správu základní desky (BMC) a hraniční sítě (síť datacenter).|
|**Proces opravy a aktualizace**|Chcete-li přejít na novou verzi nástroje ASDK, je nutné znovu nasadit ASDK na hostitelském počítači ASDK.|Proces [opravy a aktualizace](../operator/azure-stack-updates.md) , který se používá k aktualizaci nainstalované verze centra Azure Stack.|
|**Podpora**|Fórum MSDN Azure Stack. Podpora Microsoftu není *k dispozici pro* prostředí, která nejsou v produkčním prostředí.|[MSDN Azure Stack Fórum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStack) a plná podpora.|
| | |

## <a name="learn-about-available-services"></a>Další informace o dostupných službách
Jako operátor centra Azure Stack potřebujete znát, které služby můžete uživatelům zpřístupnit. Centrum Azure Stack podporuje podmnožinu služeb Azure a seznam podporovaných služeb se bude dál vyvíjet v průběhu času.

### <a name="foundational-services"></a>Základní služby
Ve výchozím nastavení zahrnuje Azure Stack hub při nasazení rozhraní ASDK následující "základní služby":
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
Centrum Azure Stack bude dál přidávat podporu dalších služeb Azure. Další informace o tom, co se chystá Azure Stack hub, najdete v tématu [Azure Stack](https://azure.microsoft.com/updates/?query=azure%20stack%20hub).


## <a name="next-steps"></a>Další kroky
Pokud chcete začít s vyhodnocováním centra Azure Stack, musíte nejdřív [Stáhnout nejnovější ASDK](asdk-download.md) a připravit hostitelský počítač ASDK. Pak můžete nainstalovat ASDK a přihlásit se k portálům pro správu a uživatele, abyste mohli začít používat centrum Azure Stack.
