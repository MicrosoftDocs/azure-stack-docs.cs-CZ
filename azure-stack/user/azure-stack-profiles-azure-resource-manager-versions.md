---
title: Verze rozhraní API poskytovatele prostředků podporované profily ve službě Azure Stack | Dokumentace Microsoftu
description: Další informace o rozhraní API Azure Resource Manageru verze podporuje profily ve službě Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/08/2019
ms.openlocfilehash: 21e1e8df3d5f43f91e391b8c39f3f5aca3aefea8
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/28/2019
ms.locfileid: "66269570"
---
# <a name="resource-provider-api-versions-supported-by-profiles-in-azure-stack"></a>Verze rozhraní API poskytovatele prostředků podporované profily ve službě Azure Stack

Vyhledejte poskytovatele prostředků a čísla verze pro každý profil rozhraní API používané ve službě Azure Stack v tomto článku. V tabulkách v tomto článku jsou uvedeny verze podporované pro každý poskytovatel prostředků a rozhraní API verze profilů. Každý poskytovatel prostředků obsahuje sadu typů prostředků a čísla konkrétní verzi.

Profil rozhraní API používá tři zásady vytváření názvů:

- **nejnovější**
- **yyyy-mm-dd-hybrid**
- **yyyy-mm-dd-profile**

Vysvětlení profilů rozhraní API a verze vydávání verzí pro službu Azure Stack najdete v tématu [profilů verzí API spravovat ve službě Azure Stack](azure-stack-version-profiles.md).

> [!Note]
> **Nejnovější** profil rozhraní API obsahuje nejnovější verzi rozhraní API poskytovatele prostředků a není uvedená v tomto článku.

## <a name="overview-of-the-2019-03-01-hybrid-profile"></a>Přehled profilu 2019-03-01hybridní

| Poskytovatel prostředků | verze API-version |
|-----------------------------------------------|-----------------------------------------------------|
| Microsoft.Compute | 2017-12-01 |
| Microsoft.Network | 2017-10-01<br>VPN Gateway bude 2017-10-01 |
| Microsoft.Storage (rovina dat) | 2017-10-01 |
| Microsoft.Storage (rovina řízení) | 2017-10-01 |
| Microsoft.Web | 2018-02-01 |
| Microsoft.KeyVault | 2016-10-01 (ne změnit) |
| Microsoft.Resources (Azure Resource Manageru samotné) | 2016-06-01 |
| Microsoft.Authorization (operace zásad) | 2016-09-01 |
| Microsoft.Insights | 2018-01-01 |

Seznam verzí pro každý typ prostředku pro zprostředkovatele v profilu rozhraní API najdete v tématu [Podrobnosti profilu 2019-03-01hybridní](#details-for-the-2019-03-01-hybrid-profile).

## <a name="details-for-the-2019-03-01-hybrid-profile"></a>Podrobnosti o profilu. 2019-03-01hybridní

### <a name="microsoftauthorization"></a>Microsoft.Authorization

Řízení přístupu na základě role umožňuje spravovat akce, které můžou uživatelé ve vaší organizaci provádět s prostředky. Můžete definovat role, přiřazovat role uživatelům nebo skupinám a získávat informace o oprávněních. Další informace najdete v tématu [autorizace](/rest/api/authorization/).

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
| Předplatná delegované poskytovatele | verze 2015-06-01 - preview |
| Agregace delegované využití | verze 2015-06-01 - preview |
| Odhad prostředků výdajů | verze 2015-06-01 - preview |
| Operace | verze 2015-06-01 - preview |
| Agregace využití odběratele | verze 2015-06-01 - preview |
| Agregace využití | verze 2015-06-01 - preview |

### <a name="microsoftcompute"></a>Microsoft.Compute

Rozhraní API Azure Compute poskytují programový přístup k virtuálním počítačům a jejich pomocným prostředkům. Další informace najdete v tématu [Azure Compute](/rest/api/compute/).

| Typ prostředku | Verze rozhraní API |
|---------------------------------------------------------------|-------------|
| Sady dostupnosti | 2017-12-01 |
| Umístění | 2017-12-01 |
| Umístění/operace | 2017-12-01 |
| Umístění a vydavatele | 2017-12-01 |
| Umístění/využití | 2017-12-01 |
| Umístění/vmSizes | 2017-12-01 |
| Operace | 2017-12-01 |
| Virtuální počítače | 2017-12-01 |
| Virtual Machines/extensions | 2017-12-01 |
| Virtual Machine Scale Sets | 2017-12-01 |
| Virtual Machine Scale Sets/rozšíření | 2017-12-01 |
| Virtual Machine Scale Sets/síťová rozhraní | 2017-12-01 |
| Virtuální počítač Škálovací sady a virtuálních počítačů | 2017-12-01|
| Virtuální počítače Škálovací sady a virtuálních počítačů/síťová | 2017-12-01 |

### <a name="microsoftgallery"></a>Microsoft.Gallery

| Typ prostředku | Verze rozhraní API |
|------------------|-------------|
| Kurátorování | 2015-04-01 |
| Obsah kurátorování | 2015-04-01 |
| Extrakt kurátorování | 2015-04-01 |
| Položky galerie | 2015-04-01 |
| Operace | 2015-04-01 |
| Portál | 2015-04-01 |
| Vyhledávání | 2015-04-01 |
| Navrhnout | 2015-04-01 |

### <a name="microsoftinsights"></a>Microsoft.Insights

| Typy prostředků | Verze rozhraní API |
|--------------------|--------------------|
| Operace | 2015-04-01 |
| Typy událostí | 2015-04-01 |
| Kategorie události | 2015-04-01 |
| Definice metrik | 2018-01-01 |
| Metriky | 2018-01-01 |
| Nastavení diagnostiky | 2017-05-01-preview |
| Kategorie nastavení diagnostiky | 2017-05-01-preview |

### <a name="microsoftkeyvault"></a>Microsoft.KeyVault

Správa služby Key Vault a klíče, tajné kódy a certifikáty v rámci služby Key Vault. Další informace najdete v tématu [REST API služby Azure Key Vault odkaz](/rest/api/keyvault/).

| Typy prostředků | Verze rozhraní API |
|-------------------------|--------------|
| Operace | 2016-10-01 |
| Trezory | 2016-10-01 |
| Trezory Recovery Services nebo zásady přístupu | 2016-10-01 |
| Trezory/tajných klíčů | 2016-10-01 |

### <a name="microsoftnetwork"></a>Microsoft.Network

Výsledek volání operací je reprezentace seznamu dostupných síťových cloudových operací. Další informace najdete v tématu [operace REST API](/rest/api/operation/).

| Typy prostředků | Verze rozhraní API |
|---------------------------|--------------|
| Připojení | 2017-10-01 |
| Zóny DNS | 2016-04-01 |
| Nástroje pro vyrovnávání zatížení | 2017-10-01 |
| Brána místní sítě | 2017-10-01 |
| Umístění | 2017-10-01|
| Umístění/operationResults | 2017-10-01 |
| Umístění/operace | 2017-10-01 |
| Umístění/využití |2017-10-01 |
| Síťová rozhraní | 2017-10-01 |
| Network Security Groups (Skupiny zabezpečení sítě) | 2017-10-01 |
| Operace | 2017-10-01 |
| Veřejná IP adresa | 2017-10-01 |
| Směrovací tabulky | 2017-10-01 |
| Brána virtuální sítě | 2017-10-01 |
| Virtuální sítě | 2017-10-01 |

### <a name="microsoftresources"></a>Microsoft.Resources

Azure Resource Manager vám umožňuje nasadit a spravovat infrastrukturu pro vaše řešení Azure. Uspořádání souvisejících prostředků ve skupinách prostředků a nasazení prostředků pomocí šablon JSON. Úvod k nasazování a správě prostředků pomocí Resource Manageru, najdete v článku [přehled Azure Resource Manageru](/azure/azure-resource-manager/resource-group-overview).

| Typy prostředků | Verze rozhraní API |
|-----------------------------------------|-------------------|
| Nasazení | 2018-05-01 |
| Nasazení/operace | 2018-05-01 |
| Odkazy | 2018-05-01 |
| Umístění | 2018-05-01 |
| Operace | 2018-05-01 |
| Zprostředkovatelé | 2018-05-01 |
| Skupiny prostředků| 2018-05-01 |
| Prostředky | 2018-05-01/ |
| Subscriptions | 2018-05-01 |
| Předplatné a umístění | 2016-06-01 |
| Předplatné/operationresults | 2018-05-01 |
| Předplatná a poskytovatelé | 2018-05-01 |
| Předplatná nebo skupiny prostředků | 2018-05-01 |
| Subscriptions/resourceGroups/resources | 2018-05-01 |
| Předplatná a prostředky | 2018-05-01 |
| Subscriptions/tagNames | 2018-05-01 |
| Subscriptions/tagNames/tagValues | 2018-05-01 |
| Tenanti | 2016-06-01 |

### <a name="microsoftstorage"></a>Microsoft.Storage

Poskytovatel prostředků úložiště (SRP) umožňuje spravovat váš účet úložiště a klíče prostřednictvím kódu programu. Další informace najdete v tématu [odkaz na Azure Storage Resource Provider REST API](/rest/api/storagerp/).

| Typy prostředků | Verze rozhraní API |
|-------------------------|--------------|
| CheckNameAvailability | 2017-10-01 |
| Umístění | 2017-10-01 |
| Umístění a kvóty | 2017-10-01 |
| Operace | 2017-10-01 |
| StorageAccounts | 2017-10-01 |
| Využití | 2017-10-01 |

## <a name="next-steps"></a>Další postup

* [Instalace PowerShellu pro Azure Stack](../operator/azure-stack-powershell-install.md)
* [Konfigurace prostředí PowerShell uživatele Azure stacku](azure-stack-powershell-configure-user.md)  
