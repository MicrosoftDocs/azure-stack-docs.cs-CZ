---
title: Rozdíly mezi Azure Stack a Azure při používání služeb a sestavování aplikací | Microsoft Docs
description: Pochopte rozdíly mezi Azure a Azure Stack při používání služeb a sestavování aplikací.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: c81f551d-c13e-47d9-a5c2-eb1ea4806228
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 07/17/2019
ms.author: sethm
ms.lastreviewed: 12/27/2018
ms.openlocfilehash: fc04032f7741c61a9b2b86e23a9173ca268a1e11
ms.sourcegitcommit: b8260ef3e43f3703dd0df16fb752610ec8a86942
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2019
ms.locfileid: "70008346"
---
# <a name="differences-between-azure-stack-and-azure-when-using-services-and-building-apps"></a>Rozdíly mezi Azure Stack a Azure při používání služeb a sestavování aplikací

Předtím, než použijete služby nebo sestavíte aplikace pro Azure Stack, je důležité porozumět rozdílům mezi Azure Stack a Azure. Tento článek popisuje různé funkce a klíčové důležité pokyny při použití Azure Stack jako prostředí pro vývoj hybridního cloudu.

## <a name="overview"></a>Přehled

Azure Stack je hybridní cloudová platforma, která umožňuje používat služby Azure z datového centra společnosti nebo poskytovatele služeb. Můžete vytvořit aplikaci na Azure Stack a pak ji nasadit do služby Azure Stack, do Azure nebo do hybridního cloudu Azure.

Váš operátor Azure Stack vám umožní zjistit, které služby jsou k dispozici pro použití, a jak získat podporu. Nabízí tyto služby prostřednictvím vlastních plánů a nabídek.

Obsah technické dokumentace Azure předpokládá, že se aplikace vyvíjí pro službu Azure, a ne pro Azure Stack. Při sestavování a nasazování aplikací pro Azure Stack musíte pochopit některé klíčové rozdíly, jako například:

* Azure Stack poskytuje podmnožinu služeb a funkcí, které jsou k dispozici v Azure.
* Společnost nebo poskytovatel služeb si můžou vybrat, které služby chce nabízet. Dostupné možnosti můžou zahrnovat přizpůsobené služby nebo aplikace. Můžou nabízet vlastní přizpůsobenou dokumentaci.
* Je nutné použít správné koncové body specifické pro Azure Stack (například adresy URL adresy portálu a koncový bod Azure Resource Manager).
* Je nutné použít PowerShell a verze rozhraní API, které jsou podporovány nástrojem Azure Stack. Použití podporovaných verzí zajistí, že vaše aplikace fungují jak v Azure Stack, tak i v Azure.

## <a name="cheat-sheet-high-level-differences"></a>Tahák list: Rozdíly vysoké úrovně

Následující tabulka popisuje rozdíly vysoké úrovně mezi Azure Stack a Azure. Mějte na paměti, že při vývoji Azure Stack nebo používání služby Azure Stack Services mějte na paměti tyto rozdíly:

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

| Oblast | Azure (Global) | Azure Stack |
| -------- | ------------- | ----------|
| Kdo to funguje? | Microsoft | Vaše organizace nebo poskytovatel služeb.|
| Koho se obrátíte na podporu? | Microsoft | V případě integrovaného systému kontaktujte operátor Azure Stack (ve vaší organizaci nebo poskytovateli služeb) pro podporu.<br><br>Podporu Azure Stack Development Kit (ASDK) najdete na [fórech Microsoftu](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStack). Vzhledem k tomu, že vývojová sada je zkušební prostředí, není oficiální podpora nabídnuta prostřednictvím služeb Microsoft Customer Support Services (CSS).
| Dostupné služby | Podívejte se na seznam [produktů Azure](https://azure.microsoft.com/services/?b=17.04b). Dostupné služby se liší podle oblasti Azure. | Azure Stack podporuje podmnožinu služeb Azure. Skutečné služby se budou lišit v závislosti na tom, co vaše organizace nebo poskytovatel služeb zvolí jako nabídky.
| Azure Resource Manager koncový bod * | https://management.azure.com | Pro Azure Stack integrovaný systém použijte koncový bod, který poskytuje operátor Azure Stack.<br><br>Pro vývojovou sadu použijte: https://management.local.azurestack.external.
| Adresa URL portálu * | [https://portal.azure.com](https://portal.azure.com) | Pro Azure Stack integrovaný systém použijte adresu URL, kterou poskytuje operátor Azure Stack.<br><br>Pro vývojovou sadu použijte: https://portal.local.azurestack.external.
| Oblast | Můžete vybrat, do které oblasti chcete nasazení nasadit. | Pro Azure Stack integrovaný systém použijte oblast, která je k dispozici ve vašem systému.<br><br>V případě vývojové sady bude oblast vždycky **místní**.
| Skupiny prostředků | Skupina prostředků může zahrnovat oblasti. | V případě integrovaných systémů i vývojové sady existuje pouze jedna oblast.
|Podporované obory názvů, typy prostředků a verze API | Nejnovější (nebo starší verze, které ještě nejsou zastaralé). | Azure Stack podporuje konkrétní verze. Viz část [požadavky na verzi](#version-requirements) v tomto článku.
| | |

\* Pokud jste operátor Azure Stack, přečtěte si téma [používání portálu](../operator/azure-stack-manage-portals.md) pro [správu a základní](../operator/azure-stack-manage-basics.md) informace o správě.

## <a name="helpful-tools-and-best-practices"></a>Užitečné nástroje a osvědčené postupy

Microsoft poskytuje nástroje a pokyny, které vám pomůžou s vývojem pro Azure Stack.

| Doporučení | Odkazy |
| -------- | ------------- |
| Nainstalujte do pracovní stanice pro vývojáře správné nástroje. | - [Instalace PowerShellu](../operator/azure-stack-powershell-install.md)<br>- [Nástroje ke stažení](../operator/azure-stack-powershell-download.md)<br>- [Konfigurace PowerShellu](azure-stack-powershell-configure-user.md)<br>- [Nainstalovat Visual Studio](azure-stack-install-visual-studio.md) 
| Zkontrolujte informace o následujících položkách:<br>– Pokyny pro šablonu Azure Resource Manager<br>– Jak najít šablony pro rychlý Start<br>– Použijte modul zásad, který vám umožní využít Azure k vývoji pro Azure Stack | [Vývoj pro Azure Stack](azure-stack-developer.md) | 
| Projděte si osvědčené postupy pro šablony a použijte je. | [Šablony pro rychlý Start Správce prostředků](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md)
| | |

## <a name="version-requirements"></a>Požadavky na verzi

Azure Stack podporuje konkrétní verze Azure PowerShell a rozhraní API služeb Azure. Použijte podporované verze, abyste zajistili, že se vaše aplikace může nasadit do Azure Stack i do Azure.

Abyste měli jistotu, že používáte správnou verzi Azure PowerShell, použijte [profily verze rozhraní API](azure-stack-version-profiles.md). Chcete-li určit nejnovější profil verze rozhraní API, který můžete použít, přečtěte si sestavení Azure Stack používáte. Tyto informace můžete získat od správce Azure Stack.

> [!NOTE]
> Pokud používáte Azure Stack Development Kit a máte přístup správce, přečtěte si část [určení aktuální verze](../operator/azure-stack-updates.md) pro určení Azure Stack sestavení.

U jiných rozhraní API spusťte následující příkaz PowerShellu pro výstup oborů názvů, typů prostředků a verzí rozhraní API, které jsou podporované v rámci předplatného Azure Stack (na úrovni vlastností můžou existovat rozdíly). Aby tento příkaz fungoval, je potřeba, abyste už [nainstalovali](../operator/azure-stack-powershell-install.md) a [nakonfigurovali](azure-stack-powershell-configure-user.md) PowerShell pro prostředí Azure Stack. Musíte mít také předplatné pro Azure Stack nabídku.

```powershell
Get-AzureRmResourceProvider | Select ProviderNamespace -Expand ResourceTypes | Select * -Expand ApiVersions | `
Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} 
```

Ukázkový výstup (zkrácený): ![Příklad výstupu příkazu Get-AzureRmResourceProvider](media/azure-stack-considerations/image1.png)

## <a name="next-steps"></a>Další postup

Podrobnější informace o rozdílech na úrovni služby najdete v těchto tématech:

* [Požadavky na virtuální počítače v Azure Stack](azure-stack-vm-considerations.md)
* [Pokyny pro úložiště v Azure Stack](azure-stack-acs-differences.md)
* [Aspekty sítí Azure Stack](azure-stack-network-differences.md)
