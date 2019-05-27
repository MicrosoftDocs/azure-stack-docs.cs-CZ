---
title: Rozdíly mezi Azure Stack a Azure při používání služby a vytváření aplikací | Dokumentace Microsoftu
description: Znát rozdíly mezi Azure a Azure Stack při použití služby a vytváření aplikací.
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
ms.date: 04/08/2019
ms.author: sethm
ms.lastreviewed: 12/27/2018
ms.openlocfilehash: 9fcf27c8ebbde86e775b54eda593b25fcd03979c
ms.sourcegitcommit: be5382f715a9c1c18c660b630d8fcd823f13aae3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/24/2019
ms.locfileid: "66197291"
---
# <a name="differences-between-azure-stack-and-azure-when-using-services-and-building-apps"></a>Rozdíly mezi Azure Stack a Azure při používání služby a vytváření aplikací

Před použitím služby nebo vytvářejte aplikace pro Azure Stack, je důležité znát rozdíly mezi Azure Stack a Azure. Tento článek identifikuje různé funkce a klíčové aspekty při používání služby Azure Stack jako vývojové prostředí hybridního cloudu.

## <a name="overview"></a>Přehled

Azure Stack je hybridní Cloudová platforma, která umožňuje využívat služby Azure z datového centra vaší společností nebo službou zprostředkovatele. Můžete vytvořit aplikaci ve službě Azure Stack a potom ji nasadit do služby Azure Stack, Azure nebo Azure hybridního cloudu.

Operátor Azure Stack vám dá vědět, které služby jsou k dispozici pro použití a jak získat podporu. Nabízejí tyto služby prostřednictvím jejich vlastní plány a nabídky.

Azure technického obsahu se předpokládá, že se aplikace vyvíjejí pro služby Azure a ne Azure Stack. Při sestavení a nasazení aplikací do služby Azure Stack, musíte znát několik klíčových rozdílů, například:

* Azure Stack nabízí podmnožinu funkcí, které jsou dostupné v Azure a služby.
* Vaše společnost nebo poskytovali služeb, můžete zvolit služby, které chtějí nabídnout. Dostupné možnosti patří vlastní služby nebo aplikace. Nabízejí může vlastní přizpůsobené dokumentace.
* Je nutné použít správné Azure Stack specifické koncové body (například adresy URL adresy portálu a koncový bod Azure Resource Manager).
* Je nutné použít prostředí PowerShell a rozhraní API verze, které podporuje Azure Stack. Pomocí podporované verze zajistí, že vaše aplikace fungovat v Azure Stack a Azure.

## <a name="cheat-sheet-high-level-differences"></a>Tahák: Nejvýraznějších rozdílů

Následující tabulka popisuje základní rozdíly mezi Azure Stack a Azure. Tyto rozdíly v úvahu byste měli vzít při vývoji pro Azure Stack nebo pomocí služby Azure Stack.

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

| Oblast | Azure (globální) | Azure Stack |
| -------- | ------------- | ----------|
| Kdo funguje? | Microsoft | Vaše organizace nebo poskytovali služeb.|
| Kdo je kontaktovat kvůli podpoře? | Microsoft | Pro integrovaný systém obraťte se na vaše služby Azure Stack – operátor (v organizaci nebo service provider) pro podporu.<br><br>Azure Stack Development Kit podporu najdete [fórech Microsoftu](https://social.msdn.microsoft.com/Forums/home?forum=azurestack). Vývojová sada je prostředí pro testování, neexistuje žádné oficiální podporu, kterou nabízí prostřednictvím Microsoft podporu služby zákazníkům (CSS).
| Dostupné služby | Zobrazit seznam [produkty Azure](https://azure.microsoft.com/services/?b=17.04b). Dostupné služby se liší podle oblasti Azure. | Azure Stack podporuje podmnožinu služeb Azure. Skutečné služby budou lišit v závislosti na vaší organizace nebo poskytovali služeb, zvolí nabízí.
| Azure Resource Manageru. koncový bod * | https://management.azure.com | Systém Azure Stack integrované pomocí koncového bodu, který obsahuje operátor Azure stacku.<br><br>Sada, použijte: https://management.local.azurestack.external.
| Portál URL * | [https://portal.azure.com](https://portal.azure.com) | Pro systém Azure Stack integrované použijte adresu URL, která poskytuje vaše operátory Azure stacku.<br><br>Sada, použijte: https://portal.local.azurestack.external.
| Oblast | Které oblasti, kterou chcete nasadit, můžete vybrat. | Pro systém Azure Stack integrované použijte oblast, která je k dispozici ve vašem systému.<br><br>Pro development kit, oblast bude vždy **místní**.
| Skupiny prostředků | Skupina prostředků může zahrnovat oblasti. | Pro integrované systémy a vývojová sada existuje pouze jedna oblast.
|Podporovaných oborech názvů, typy prostředků a verze rozhraní API | Nejnovější (nebo starší verze, které ještě nejsou zastaralé). | Azure Stack podporuje konkrétní verze. Zobrazit [požadavky na verzi](#version-requirements) části tohoto článku.
| | |

* Pokud jste operátory Azure stacku, přečtěte si téma [pomocí portálu správce](../operator/azure-stack-manage-portals.md) a [základy správy](../operator/azure-stack-manage-basics.md) Další informace.

## <a name="helpful-tools-and-best-practices"></a>Užitečné nástroje a osvědčené postupy

Společnost Microsoft poskytuje nástroje a pokyny, které vám pomohou vyvíjet pro Azure Stack.

| Doporučení | Odkazy |
| -------- | ------------- |
| Nainstalujte nástroje správné na vaši vývojářskou pracovní stanici. | - [Instalace Powershellu](../operator/azure-stack-powershell-install.md)<br>- [Stáhněte si nástroje](../operator/azure-stack-powershell-download.md)<br>- [Konfigurace Powershellu](azure-stack-powershell-configure-user.md)<br>- [Install Visual Studio](azure-stack-install-visual-studio.md) 
| Přečtěte si informace o následujících položkách:<br>– Aspekty šablon azure Resource Manageru<br>– Tom, jak najít šablony pro rychlý start<br>-Použití modulu zásad vám pomůžou používat Azure k vývoji pro Azure Stack | [Vývoj pro Azure Stack](azure-stack-developer.md) | 
| Přečtěte si a použijte osvědčené postupy pro šablony. | [Šablony Resource Manageru pro rychlý start](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md#best-practices)
| | |

## <a name="version-requirements"></a>Požadavky na verzi

Azure Stack podporuje konkrétní verze prostředí Azure PowerShell a rozhraní API služby Azure. Ujistěte se, že vaše aplikace můžete nasadit i Azure Stack a Azure pomocí podporované verze.

Chcete-li mít jistotu, že používáte správnou verzi Azure Powershellu, použijte [profilů verzí API](azure-stack-version-profiles.md). K určení profilu nejnovější verze rozhraní API, které můžete použít, přečtěte si sestavení služby Azure Stack, který používáte. Tyto informace můžete získat od správce služby Azure Stack.

> [!NOTE]
> Pokud používáte Azure Stack Development Kit a máte přístup správce, podívejte se [zjistit aktuální verzi](../operator/azure-stack-updates.md#determine-the-current-version) části k určení sestavení služby Azure Stack.

Pro jiná rozhraní API spusťte následující příkaz Powershellu na výstup obory názvů, typy prostředků a verze rozhraní API, které jsou podporovány v rámci vašeho předplatného Azure Stack (nemusí být rozdíly na úrovni vlastnost). Pro tento příkaz fungovat, musíte už mít [nainstalované](../operator/azure-stack-powershell-install.md) a [nakonfigurované](azure-stack-powershell-configure-user.md) prostředí PowerShell pro prostředí Azure Stack. Také musíte mít předplatné služby Azure Stack nabídky.

```powershell
Get-AzureRmResourceProvider | Select ProviderNamespace -Expand ResourceTypes | Select * -Expand ApiVersions | `
Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} 
```

Příklad výstupu (zkrácená): ![Příklad výstupu z příkazu Get-AzureRmResourceProvider](media/azure-stack-considerations/image1.png)

## <a name="next-steps"></a>Další postup

Podrobnější informace o rozdílech na úrovni služby naleznete v tématu:

* [Důležité informace týkající se virtuálních počítačů v Azure stacku](azure-stack-vm-considerations.md)
* [Důležité informace týkající se úložiště v Azure stacku](azure-stack-acs-differences.md)
* [Aspekty sítí Azure Stack](azure-stack-network-differences.md)
