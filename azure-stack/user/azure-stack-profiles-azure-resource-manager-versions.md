---
title: Verze rozhraní API poskytovatele prostředků podporované profily v Azure Stack | Microsoft Docs
description: Přečtěte si o verzích rozhraní API Azure Resource Manager podporovaných profily v Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/12/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/08/2019
ms.openlocfilehash: 7f86ff3347d5d6f24bcd1fe450156c2557fb41e1
ms.sourcegitcommit: 58c28c0c4086b4d769e9d8c5a8249a76c0f09e57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2019
ms.locfileid: "68959387"
---
# <a name="resource-provider-api-versions-supported-by-profiles-in-azure-stack"></a>Verze rozhraní API poskytovatele prostředků podporované profily v Azure Stack

Pro každý profil rozhraní API, který používá Azure Stack v tomto článku, můžete najít poskytovatele prostředků a čísla verzí. V tabulkách v tomto článku jsou uvedeny verze, které jsou podporovány pro jednotlivé poskytovatele prostředků a verze rozhraní API v profilech. Každý poskytovatel prostředků obsahuje sadu typů prostředků a konkrétní čísla verzí.

Profil rozhraní API používá tři konvence pojmenování:

- **nejnovější**
- **yyyy-mm-dd-hybrid**
- **yyyy-mm-dd-profile**

Vysvětlení profilů rozhraní API a verzí verze tempo pro Azure Stack najdete v tématu [Správa profilů verzí rozhraní API v Azure Stack](azure-stack-version-profiles.md).

> [!NOTE]
> **Nejnovější** profil rozhraní API obsahuje nejnovější verzi rozhraní API poskytovatele prostředků a není uvedený v tomto článku.

## <a name="overview-of-the-2019-03-01-hybrid-profile"></a>Přehled 2019-03-01 – hybridní profil

| Poskytovatel prostředků | Verze API-Version |
|-----------------------------------------------|-----------------------------------------------------|
| Microsoft.Compute | 2017-12-01 |
| Microsoft.Network | 2017-10-01<br>VPN Gateway bude 2017-10-01 |
| Microsoft. Storage (rovina dat) | 2017-10-01 |
| Microsoft. Storage (řídicí plocha) | 2017-10-01 |
| Microsoft.Web | 2018-02-01 |
| Microsoft.KeyVault | 2016-10-01 (nemění se) |
| Microsoft. Resources (Azure Resource Manager sám sebe) | 2016-06-01 |
| Microsoft. Authorization (operace zásad) | 2016-09-01 |
| Microsoft.Insights | 2018-01-01 |

Seznam verzí pro každý typ prostředku pro zprostředkovatele v profilu rozhraní API najdete v [podrobnostech pro 2019-03-01 – hybridní profil](#details-for-the-2019-03-01-hybrid-profile).

## <a name="details-for-the-2019-03-01-hybrid-profile"></a>Podrobnosti pro 2019-03-01 – hybridní profil

### <a name="microsoftauthorization"></a>Microsoft.Authorization

Řízení přístupu na základě rolí umožňuje spravovat akce, které můžou uživatelé ve vaší organizaci provádět na svých prostředcích. Můžete definovat role, přiřazovat role uživatelům nebo skupinám a získávat informace o oprávněních. Další informace najdete v tématu [autorizace](/rest/api/authorization/).

| Typy prostředků | Verze rozhraní API |
|---------------------|--------------------|
| Zámky | 2016-09-01 |
| Operace | 2015-07-01 |
| Oprávnění | 2015-07-01 |
| Přiřazení zásad | 2016-12-01 |
| Definice zásad | 2016-12-01 |
| Operace poskytovatele | 2015-07-01 |
| Přiřazení rolí | 2015-07-01 |
| Definice rolí | 2015-07-01 |

### <a name="microsoftcommerce"></a>Microsoft.Commerce

| Typ prostředku | Verze rozhraní API |
|----------------------------------|----------------------|
| Delegovaná předplatná zprostředkovatele | 2015-06-01 – Preview |
| Agregace delegovaného použití | 2015-06-01 – Preview |
| Odhad útraty prostředků | 2015-06-01 – Preview |
| Operace | 2015-06-01 – Preview |
| Agregace využití předplatitele | 2015-06-01 – Preview |
| Agregace využití | 2015-06-01 – Preview |

### <a name="microsoftcompute"></a>Microsoft.Compute

Rozhraní API služby Azure COMPUTE poskytují programový přístup k virtuálním počítačům a jejich podpůrným prostředkům. Další informace najdete v tématu [výpočetní prostředí Azure](/rest/api/compute/).

| Typ prostředku | Verze rozhraní API |
|---------------------------------------------------------------|-------------|
| Sady dostupnosti | 2017-12-01 |
| Umístění | 2017-12-01 |
| Umístění/operace | 2017-12-01 |
| Umístění/vydavatelé | 2017-12-01 |
| Umístění/použití | 2017-12-01 |
| Umístění/povolených velikostí | 2017-12-01 |
| Operace | 2017-12-01 |
| Virtuální počítače | 2017-12-01 |
| Virtual Machines/rozšíření | 2017-12-01 |
| Virtual Machine Scale Sets | 2017-12-01 |
| Virtual Machine Scale Sets/rozšíření | 2017-12-01 |
| Virtual Machine Scale Sets/síťová rozhraní | 2017-12-01 |
| Virtual Machine Scale Sets/Virtual Machines | 2017-12-01|
| Virtual Machines Scale Sets/virtualMachines/networkInterfaces | 2017-12-01 |

### <a name="microsoftgallery"></a>Microsoft.Gallery

| Typ prostředku | Verze rozhraní API |
|------------------|-------------|
| Kurátorování | 2015-04-01 |
| Obsah kurátorování | 2015-04-01 |
| Extrakt kurátorování | 2015-04-01 |
| Položky Galerie | 2015-04-01 |
| Operace | 2015-04-01 |
| Portál | 2015-04-01 |
| Hledat | 2015-04-01 |
| Navrhnout | 2015-04-01 |

### <a name="microsoftinsights"></a>Microsoft.Insights

| Typy prostředků | Verze rozhraní API |
|--------------------|--------------------|
| Operace | 2015-04-01 |
| Typy událostí | 2015-04-01 |
| Kategorie událostí | 2015-04-01 |
| Definice metrik | 2018-01-01 |
| Metriky | 2018-01-01 |
| Nastavení diagnostiky | 2017-05-01-preview |
| Kategorie nastavení diagnostiky | 2017-05-01-preview |

### <a name="microsoftkeyvault"></a>Microsoft.KeyVault

Spravujte své Key Vault a také klíče, tajné klíče a certifikáty v rámci vašeho Key Vault. Další informace najdete v referenčních informacích k [Azure Key Vault REST API](/rest/api/keyvault/).

| Typy prostředků | Verze rozhraní API |
|-------------------------|--------------|
| Operace | 2016-10-01 |
| Trezory | 2016-10-01 |
| Trezory/zásady přístupu | 2016-10-01 |
| Trezory/tajné klíče | 2016-10-01 |

### <a name="microsoftnetwork"></a>Microsoft.Network

Výsledkem volání operací je reprezentace seznamu dostupných síťových cloudových operací. Další informace najdete v tématu [operace REST API](/rest/api/operation/).

| Typy prostředků | Verze rozhraní API |
|---------------------------|--------------|
| Připojení | 2017-10-01 |
| Zóny DNS | 2016-04-01 |
| Nástroje pro vyrovnávání zatížení | 2017-10-01 |
| Brána místní sítě | 2017-10-01 |
| Umístění | 2017-10-01|
| Umístění/operationResults | 2017-10-01 |
| Umístění/operace | 2017-10-01 |
| Umístění/použití |2017-10-01 |
| Síťová rozhraní | 2017-10-01 |
| Network Security Groups (Skupiny zabezpečení sítě) | 2017-10-01 |
| Operace | 2017-10-01 |
| Veřejná IP adresa | 2017-10-01 |
| Směrovací tabulky | 2017-10-01 |
| Brána virtuální sítě | 2017-10-01 |
| Virtuální sítě | 2017-10-01 |

### <a name="microsoftresources"></a>Microsoft. Resources

Azure Resource Manager vám umožní nasadit a spravovat infrastrukturu pro vaše řešení Azure. V rámci skupin prostředků organizujete související prostředky a nasadíte prostředky pomocí šablon JSON. Úvodní informace o nasazení a správě prostředků pomocí Správce prostředků najdete v [přehledu Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview).

| Typy prostředků | Verze rozhraní API |
|-----------------------------------------|-------------------|
| Nasazení | 2018-05-01 |
| Nasazení/operace | 2018-05-01 |
| Odkazy | 2018-05-01 |
| Umístění | 2018-05-01 |
| Operace | 2018-05-01 |
| Zprostředkovatelé | 2018-05-01 |
| Skupiny prostředků| 2018-05-01 |
| Zdroje a prostředky | 2018-05-01/ |
| Předplatná | 2018-05-01 |
| Předplatná/umístění | 2016-06-01 |
| Předplatná/operationresults | 2018-05-01 |
| Předplatná/poskytovatelé | 2018-05-01 |
| Předplatná/ResourceGroups | 2018-05-01 |
| Předplatná/resourceGroups/prostředky | 2018-05-01 |
| Předplatná/prostředky | 2018-05-01 |
| Předplatná/tagNames | 2018-05-01 |
| Předplatná/tagNames/tagValues | 2018-05-01 |
| Tenantů | 2016-06-01 |

### <a name="microsoftstorage"></a>Microsoft.Storage

Zprostředkovatel prostředků úložiště (SRP) umožňuje programově spravovat účet a klíče úložiště. Další informace najdete v referenčních informacích o [Azure Storage poskytovatel prostředků REST API](/rest/api/storagerp/).

| Typy prostředků | Verze rozhraní API |
|-------------------------|--------------|
| CheckNameAvailability | 2017-10-01 |
| Umístění | 2017-10-01 |
| Umístění/kvóty | 2017-10-01 |
| Operace | 2017-10-01 |
| StorageAccounts | 2017-10-01 |
| Využití | 2017-10-01 |

## <a name="next-steps"></a>Další kroky

- [Instalace PowerShellu pro Azure Stack](../operator/azure-stack-powershell-install.md)
- [Konfigurace prostředí PowerShellu Azure Stackho uživatele](azure-stack-powershell-configure-user.md)  
