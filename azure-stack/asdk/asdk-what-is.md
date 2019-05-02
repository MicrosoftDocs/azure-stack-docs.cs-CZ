---
title: Úvod k Azure Stack Development Kit (ASDK) | Dokumentace Microsoftu
description: Popisuje, co je ASDK a běžné případy použití za vaše rozhodnutí vyzkoušet Microsoft Azure Stack.
services: azure-stack
documentationcenter: ''
author: jeffgilb
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
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: edde001bece869e1398ee13fbcc7b3fa32616cfa
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "64290019"
---
# <a name="what-is-the-azure-stack-development-kit"></a>Co je Azure Stack Development Kit?
[Microsoft Azure Stack integrované systémy](../operator/azure-stack-overview.md) rozsah od 4 až 16 uzlů a společně podporuje hardwarových partnerů a Microsoft. Použijte integrované systémy Azure Stack umožňuje nové scénáře pro vaše produkční úlohy. Pokud je pro vás, operátorovi služby Azure Stack, který spravuje integrovaných systémů infrastruktury a nabízí služby, přečtěte si naše [dokumentace operátora k](/azure-stack/operator).

Azure Stack Development Kit (ASDK) je jedním uzlem nasazení služby Azure Stack, který můžete stáhnout a použít **zdarma**. Ve virtuálních počítačích spuštěn v počítači serveru jednoho hostitele, které musí splňovat nebo překračovat jsou nainstalovány všechny součásti ASDK [minimální požadavky na hardware](asdk-deploy-considerations.md#hardware). Je určená ASDK a poskytuje prostředí, ve kterém můžete vyhodnotit Azure Stack a vývoj moderních aplikací pomocí rozhraní API a nástrojů konzistentní s Azure v *-li se o neprodukční* prostředí. 

> [!IMPORTANT]
> ASDK není určen k použití nebo podporované v produkčním prostředí.

Protože všechny součásti ASDK se nasadí do jediného vývojového kit hostitelském počítači a k dispozici omezené fyzické prostředky. U ASDK nasazení virtuální počítače infrastruktury Azure stacku a tenanta virtuálních počítačů existovat vedle sebe na stejném serveru. Tato konfigurace není určena pro zkušební škálování a výkon.

ASDK je navržené pro poskytování Azure konzistentní hybridní cloudové prostředí pro:
- **Správci** (operátoři Azure stacku). ASDK je skvělým zdrojem k vyhodnocení a další informace o dostupných služeb Azure Stack.
- **Vývojáři**. ASDK slouží k vývoji hybridní nebo moderních aplikací v místním (vývojových/testovacích prostředí). Tato možnost nabízí opakovatelnosti vývojové prostředí před, nebo spolu s nasazení v produkčním prostředí Azure Stack. 

Podívejte se na toto krátké video získat další informace o ASDK:

> [!VIDEO https://www.youtube.com/embed/dbVWDrl00MM]


## <a name="asdk-and-multi-node-azure-stack-differences"></a>Rozdíly v Azure stacku ASDK a více uzly
Jednouzlový ASDK nasazení se liší od nasazení Azure Stack na víc uzlů v několika důležitých směrech –, které byste měli vědět.

|Popis|ASDK|Azure Stack s několika uzly|
|-----|-----|-----|
|**Škálování**|Na počítači jedním uzlem serveru jsou nainstalovány všechny součásti.|Může být v rozsahu od 4 až 16 uzlů.|
|**Odolnost**|Konfigurace s jedním uzlem nebude poskytovat vysokou dostupnost|[Vysoká dostupnost](../operator/azure-stack-overview.md#providing-high-availability) funkce jsou podporovány.|
|**Sítě**|Hostitel ASDK směruje veškerý přenos v síti ASDK. Neexistují žádné požadavky na další přepínače.|Složitější [síťová infrastruktura směrování](../operator/azure-stack-network.md#network-infrastructure) v nasazení na víc uzlů je nutné včetně Top-Of-Rack (TOR), řadiče pro správu základní desky (BMC) a přepínače ohraničení (síti datového centra).|
|**Proces opravy a aktualizace**|Pokud chcete přesunout na novou verzi ASDK, je nutné znovu nasadit ASDK na hostitelském počítači development kit.|[Opravy a aktualizace](../operator/azure-stack-updates.md) proces používá k aktualizaci nainstalované verze služby Azure Stack.|
|**Podpora**|Fóra MSDN Azure Stack. Podpora Microsoft zákaznický servis a podporu šablon stylů CSS je *není* k dispozici pro neprodukční prostředí.|[Fóra MSDN Azure Stack](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStack) a podporu celé šablon stylů CSS.|
| | |

## <a name="learn-about-available-services"></a>Další informace o dostupných služeb
Jako operátor Azure stacku je potřeba vědět, služby, které můžete zpřístupnit uživatelům. Azure Stack podporuje podmnožinu služeb Azure a seznam podporovaných služeb budou i nadále v průběhu času vyvíjejí.

### <a name="foundational-services"></a>Základní služby
Ve výchozím nastavení, služby Azure Stack zahrnuje následující "základní služby" při nasazování ASDK:
- Compute
- Úložiště
- Sítě
- Key Vault

Pomocí těchto základních služeb můžou nabízet Infrastructure-as--Service (IaaS) uživatelům s minimální konfigurací.

### <a name="additional-services"></a>Další služby
V současné době jsou podporovány následující další služby Platform-as-a-Service (PaaS):
- App Service
- Azure Functions
- Databáze SQL a MySQL

> [!NOTE]
> Tyto služby vyžadují další konfiguraci, předtím, než je můžete zpřístupnit uživatelům a nejsou k dispozici ve výchozím nastavení při instalaci ASDK.

## <a name="service-roadmap"></a>Plán služby
Azure Stack bude pokračovat a přidat podporu pro další služby Azure. Další informace o co se chystá pomocí služby Azure Stack, najdete v článku [plán služby Azure Stack](https://azure.microsoft.com/roadmap/?tag=azure-stack). 


## <a name="next-steps"></a>Další postup
Abyste mohli začít, vaše rozhodnutí vyzkoušet Azure Stack, musíte si nejdřív [stáhnout nejnovější ASDK](asdk-download.md) a příprava hostitelském počítači ASDK. Po přípravě hostitele development kit, můžete nainstalovat ASDK a přihlásit na portály správce a uživatele chcete začít používat Azure Stack.