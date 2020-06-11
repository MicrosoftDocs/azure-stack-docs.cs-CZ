---
title: Rozdíly mezi centrem Azure Stack a Azure při používání služeb a sestavování aplikací
description: Při používání služeb a sestavování aplikací je důležité pochopit rozdíly mezi Azure a Azure Stack hub.
author: sethmanheim
ms.topic: overview
ms.date: 01/06/2020
ms.author: sethm
ms.lastreviewed: 12/27/2019
ms.openlocfilehash: a6e2eda40a41c5ffbd7cd39789d993255a8603f9
ms.sourcegitcommit: 6306e0c2506106ad01ff50010f36466f3325d0a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/09/2020
ms.locfileid: "84631340"
---
# <a name="differences-between-azure-stack-hub-and-azure-when-using-services-and-building-apps"></a>Rozdíly mezi centrem Azure Stack a Azure při používání služeb a sestavování aplikací

Předtím, než použijete služby nebo sestavíte aplikace pro centrum Azure Stack, je důležité pochopit rozdíly mezi Azure Stackm centrem a Azure. Tento článek popisuje různé funkce a klíčové důležité pokyny při používání centra Azure Stack jako prostředí pro vývoj hybridního cloudu.

## <a name="overview"></a>Přehled

Centrum Azure Stack je hybridní cloudová platforma, která umožňuje používat služby Azure z datového centra společnosti nebo poskytovatele služeb. Můžete vytvořit aplikaci v centru Azure Stack a pak ji nasadit do služby Azure Stack hub, do Azure nebo do hybridního cloudu Azure.

Váš operátor centra Azure Stack oznamuje, které služby jsou k dispozici pro použití, a jak získat podporu. Nabízí tyto služby prostřednictvím vlastních plánů a nabídek.

[Obsah technické dokumentace Azure](/azure) předpokládá, že se aplikace vyvíjí pro službu Azure, a ne pro centra Azure Stack. Při sestavování a nasazování aplikací do centra Azure Stack je třeba pochopit některé klíčové rozdíly, jako například:

* Služba Azure Stack hub nabízí podmnožinu služeb a funkcí, které jsou k dispozici v Azure.
* Společnost nebo poskytovatel služeb si můžou vybrat, které služby chce nabízet. Dostupné možnosti můžou zahrnovat přizpůsobené služby nebo aplikace. Můžou nabízet vlastní přizpůsobenou dokumentaci.
* Použijte správné koncové body centra Azure Stack (například adresy URL pro adresu portálu a koncový bod Azure Resource Manager).
* Je nutné použít PowerShell a verze rozhraní API, které jsou podporovány nástrojem Azure Stack hub. Pomocí podporovaných verzí zajistíte, aby vaše aplikace pracovaly v Azure Stackovém centru i v Azure.

## <a name="cheat-sheet-high-level-differences"></a>List tahák: rozdíly vysoké úrovně

Následující tabulka popisuje rozdíly vysoké úrovně mezi Azure Stack hub a Azure. Mějte na paměti, že při vývoji centra pro Azure Stack nebo používání služby Azure Stack hub máte tyto rozdíly:

| Oblast | Azure (Global) | Centrum Azure Stack |
| -------- | ------------- | ----------|
| Kdo to funguje? | Microsoft | Vaše organizace nebo poskytovatel služeb.|
| Koho se obrátíte na podporu? | Microsoft | V případě integrovaného systému kontaktujte operátor centra Azure Stack (ve vaší organizaci nebo poskytovateli služeb) pro podporu.<br><br>Podporu Azure Stack Development Kit (ASDK) najdete na [fórech Microsoftu](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStack). Vzhledem k tomu, že vývojová sada je zkušební prostředí, není oficiální podpora nabídnuta prostřednictvím služeb Microsoft Customer Support Services (CSS).
| Dostupné služby | Podívejte se na seznam [produktů Azure](https://azure.microsoft.com/services/?b=17.04b). Dostupné služby se liší podle oblasti Azure. | Centrum Azure Stack podporuje podmnožinu služeb Azure. Skutečné služby se budou lišit v závislosti na tom, co vaše organizace nebo poskytovatel služeb zvolí jako nabídky.
| Azure Resource Manager koncový bod * | `https://management.azure.com` | Pro integrovaný systém Azure Stack hub použijte koncový bod, který poskytuje váš operátor centra Azure Stack.<br><br>Pro vývojovou sadu použijte: `https://management.local.azurestack.external` .
| Adresa URL portálu * | [https://portal.azure.com](https://portal.azure.com) | Pro integrovaný systém Azure Stack hub použijte adresu URL, kterou poskytuje operátor centra Azure Stack.<br><br>Pro vývojovou sadu použijte: `https://portal.local.azurestack.external` .
| Oblast | Můžete vybrat, do které oblasti chcete nasazení nasadit. | Pro integrovaný systém Azure Stack hub použijte oblast, která je k dispozici ve vašem systému.<br><br>U Azure Stack Development Kit (ASDK) bude oblast vždycky **místní**.
| Skupiny prostředků | Skupina prostředků může zahrnovat oblasti. | V případě integrovaných systémů i vývojové sady existuje pouze jedna oblast.
|Podporované obory názvů, typy prostředků a verze API | Nejnovější (nebo starší verze, které ještě nejsou zastaralé). | Centrum Azure Stack podporuje konkrétní verze. Viz část [požadavky na verzi](#version-requirements) v tomto článku.
| | |

* Pokud jste operátor centra Azure Stack, přečtěte si téma [používání portálu](../operator/azure-stack-manage-portals.md) pro [správu a základní](../operator/azure-stack-manage-basics.md) informace o správě.

## <a name="helpful-tools-and-best-practices"></a>Užitečné nástroje a osvědčené postupy

Microsoft poskytuje nástroje a pokyny, které vám pomůžou při vývoji centra Azure Stack.

| Doporučení | Reference |
| -------- | ------------- |
| Nainstalujte do pracovní stanice pro vývojáře správné nástroje. | - [Instalace PowerShellu](../operator/azure-stack-powershell-install.md)<br>- [Nástroje ke stažení](../operator/azure-stack-powershell-download.md)<br>- [Konfigurace PowerShellu](azure-stack-powershell-configure-user.md)<br>- [Nainstalovat Visual Studio](azure-stack-install-visual-studio.md)
| Zkontrolujte informace o následujících položkách:<br>– Pokyny pro šablonu Azure Resource Manager.<br>– Jak najít šablony pro rychlý Start.<br>– Použijte modul zásad, který vám umožní využít Azure k vývoji pro centra Azure Stack. | [Vývoj pro centrum Azure Stack](azure-stack-developer.md) |
| Projděte si osvědčené postupy pro šablony a použijte je. | [Šablony pro rychlý Start Správce prostředků](https://aka.ms/aa6yz42)
| | |

## <a name="version-requirements"></a>Požadavky na verzi

Centrum Azure Stack podporuje konkrétní verze Azure PowerShell a rozhraní API služeb Azure. Použijte podporované verze, abyste zajistili, že se vaše aplikace může nasadit do centra Azure Stack a do Azure.

Abyste měli jistotu, že používáte správnou verzi Azure PowerShell, použijte [profily verze rozhraní API](azure-stack-version-profiles.md). Chcete-li určit nejnovější profil verze rozhraní API, který můžete použít, přečtěte si sestavení centra Azure Stack, které používáte. Tyto informace můžete získat od svého správce centra Azure Stack.

> [!NOTE]
> Pokud používáte Azure Stack Development Kit a máte přístup správce, přečtěte si část [určení aktuální verze](../operator/azure-stack-updates.md) , která určuje Azure Stack sestavení centra.

U jiných rozhraní API spusťte následující příkaz PowerShellu pro výstup oborů názvů, typů prostředků a verzí rozhraní API, které jsou podporované v rámci vašeho předplatného centra Azure Stack (na úrovni vlastností můžou existovat rozdíly). Aby tento příkaz fungoval, je nutné, abyste už [nainstalovali](../operator/azure-stack-powershell-install.md) a [nakonfigurovali](azure-stack-powershell-configure-user.md) PowerShell pro prostředí Azure Stack hub. Musíte mít také předplatné nabídky centra Azure Stack.

```powershell
Get-AzureRmResourceProvider | Select ProviderNamespace -Expand ResourceTypes | Select * -Expand ApiVersions | `
Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} 
```

Příklad výstupu (zkrácený): ![ příklad výstupu příkazu Get-AzureRmResourceProvider](media/azure-stack-considerations/image1.png)

## <a name="next-steps"></a>Další kroky

Podrobnější informace o rozdílech na úrovni služby najdete v těchto tématech:

* [Požadavky na virtuální počítače v centru Azure Stack](azure-stack-vm-considerations.md)
* [Pokyny pro úložiště v centru Azure Stack](azure-stack-acs-differences.md)
* [Požadavky na síť centra Azure Stack](azure-stack-network-differences.md)
