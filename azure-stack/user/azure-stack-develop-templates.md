---
title: Vývoj šablon pro centrum Azure Stack
description: Naučte se vyvíjet šablony Azure Resource Manager pro přenositelnost aplikací mezi Azure a centrum Azure Stack.
author: mattbriggs
ms.topic: article
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviewer: unknown
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: dd43abbf1194aa4aaa3ca1cc75a3e2ff6262bbbc
ms.sourcegitcommit: db3c9179916a36be78b43a8a47e1fd414aed3c2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/28/2020
ms.locfileid: "84146746"
---
# <a name="develop-templates-for-azure-stack-hub-with-azure-resource-manager"></a>Vývoj šablon pro centra Azure Stack s využitím Azure Resource Manager

Při vývoji aplikace je důležité mít přenositelnost šablon mezi Azure a Azure Stack hub. Tento článek poskytuje pokyny pro vývoj [Azure Resource Manager šablon](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf). Pomocí těchto šablon můžete vytvářet prototypy aplikace a testovat nasazení v Azure bez přístupu k prostředí centra Azure Stack.

## <a name="resource-provider-availability"></a>Dostupnost poskytovatele prostředků

Šablona, kterou hodláte nasadit, musí používat pouze Microsoft Azure služby, které jsou již k dispozici, nebo ve verzi Preview v centru Azure Stack.

## <a name="public-namespaces"></a>Veřejné obory názvů

Vzhledem k tomu, že je centrum Azure Stack hostované ve vašem datovém centru, má jiné obory názvů koncového bodu služby než veřejný cloud Azure. V důsledku toho pevně zakódované veřejné koncové body v šablonách Azure Resource Manager selžou při pokusu o jejich nasazení do centra Azure Stack. Koncovým bodům služby můžete dynamicky sestavovat pomocí `reference` `concatenate` funkcí a k načtení hodnot od poskytovatele prostředků během nasazování. Například místo hardwarového kódování `blob.core.windows.net` ve vaší šabloně načtěte [objekt primaryEndpoints. blob](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/101-vm-windows-create/azuredeploy.json#L175) k dynamickému nastavení koncového bodu *osDisk. URI* :

```json
"osDisk": {"name": "osdisk","vhd": {"uri":
"[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, variables('vmStorageAccountContainerName'),
 '/',variables('OSDiskName'),'.vhd')]"}}
```

## <a name="api-versioning"></a>Správa verzí rozhraní API

Verze služeb Azure se můžou v centru Azure a Azure Stack lišit. Každý prostředek vyžaduje atribut **apiVersion** , který definuje nabízené funkce. Aby se zajistila Kompatibilita verzí rozhraní API v Azure Stackovém centru, jsou pro každého poskytovatele prostředků platné následující verze API:

| Poskytovatel prostředků | apiVersion |
| --- | --- |
| Compute |**2015-06-15** |
| Síť |**2015-06-15**, **2015-05-01 – Preview** |
| Storage |**2016-01-01**, **2015-06-15**, **2015-05-01 – Preview** |
| KeyVault | **2015-06-01** |
| App Service |**2015-08-01** |

## <a name="template-functions"></a>Funkce šablon

[Funkce](/azure/azure-resource-manager/resource-group-template-functions) Azure Resource Manager poskytují možnosti požadované k vytváření dynamických šablon. Například můžete použít funkce pro úlohy, jako například:

* Zřetězení nebo ořezávání řetězců.
* Odkazování na hodnoty z jiných prostředků.
* Probíhá iterace prostředků pro nasazení více instancí.

Tyto funkce nejsou k dispozici v centru Azure Stack:

* Přeskočit
* Take

## <a name="resource-location"></a>Umístění prostředku

Azure Resource Manager šablony používají `location` atribut k umístění prostředků během nasazování. Umístění v Azure odkazuje na oblast, jako je Západní USA nebo Jižní Amerika. V Azure Stackovém centru se umístění liší, protože centrum Azure Stack je ve vašem datovém centru. Pokud chcete zajistit, aby se šablony přenesly mezi Azure a Azure Stack hub, měli byste při nasazení jednotlivých prostředků odkazovat na umístění skupiny prostředků. To můžete provést pomocí `[resourceGroup().Location]` , aby všechny prostředky zdědily umístění skupiny prostředků. Následující kód je příkladem použití této funkce při nasazení účtu úložiště:

```json
"resources": [
{
  "name": "[variables('storageAccountName')]",
  "type": "Microsoft.Storage/storageAccounts",
  "apiVersion": "[variables('apiVersionStorage')]",
  "location": "[resourceGroup().location]",
  "comments": "This storage account is used to store the VM disks",
  "properties": {
  "accountType": "Standard_GRS"
  }
}
]
```

## <a name="next-steps"></a>Další kroky

* [Nasazení šablon pomocí PowerShellu](azure-stack-deploy-template-powershell.md)
* [Nasazení šablon pomocí Azure CLI](azure-stack-deploy-template-command-line.md)
* [Nasazení šablon pomocí sady Visual Studio](azure-stack-deploy-template-visual-studio.md)
