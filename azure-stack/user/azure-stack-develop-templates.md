---
title: Vývoj šablon pro Azure Stack | Dokumentace Microsoftu
description: Přečtěte si osvědčené postupy pro šablony služby Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 8a5bc713-6f51-49c8-aeed-6ced0145e07b
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: 9967da0434be577e3db8586f28e3078658623e9b
ms.sourcegitcommit: 6fcd5df8b77e782ef72f0e1419f1f75ec8c16c04
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991340"
---
# <a name="azure-resource-manager-template-considerations"></a>Aspekty šablon Azure Resource Manageru

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Při vývoji vaší aplikace, je důležité pro zajištění přenositelnosti šablony mezi Azure a Azure Stack. Tento článek obsahuje důležité informace týkající se vývoje [šablon Azure Resource Manageru](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf), což vám umožní prototypu nasazení vaší aplikace a testování v Azure bez přístupu k prostředí Azure Stack.

## <a name="resource-provider-availability"></a>Dostupnost poskytovatele prostředků

Šablonu, kterou plánujete nasadit smí používat jen služby Microsoft Azure, které jsou už k dispozici nebo je ve verzi preview ve službě Azure Stack.

## <a name="public-namespaces"></a>Veřejné obory názvů

Protože Azure Stack je hostované ve vašem datovém centru, má obory názvů koncový bod jinou službu než veřejného cloudu Azure. V důsledku toho pevně zakódované veřejné koncové body v šablonách Azure Resource Manageru selhání při pokusu o jejich nasazení do služby Azure Stack. Můžete vytvářet dynamicky pomocí koncových bodů služby `reference` a `concatenate` funkce k načtení hodnoty od zprostředkovatele prostředků během nasazování. Například místo pevného kódování `blob.core.windows.net` v šabloně, načíst [primaryEndpoints.blob](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/101-vm-windows-create/azuredeploy.json#L175) nastavovat dynamicky *osDisk.URI* koncový bod:

```json
"osDisk": {"name": "osdisk","vhd": {"uri":
"[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, variables('vmStorageAccountContainerName'),
 '/',variables('OSDiskName'),'.vhd')]"}}
```

## <a name="api-versioning"></a>Správa verzí API

Verze služby Azure může lišit mezi Azure a Azure Stack. Jednotlivé prostředky vyžadují **apiVersion** atribut, který definuje funkce, které nabízí. K zajištění kompatibility verzí rozhraní API ve službě Azure Stack, jsou platné pro každý poskytovatel prostředků v následujících verzích rozhraní API:

| Poskytovatel prostředků | apiVersion |
| --- | --- |
| Compute |**2015-06-15** |
| Síť |**2015-06-15**, **2015-05-01-preview** |
| Úložiště |**2016-01-01**, **2015-06-15**, **2015-05-01-preview** |
| KeyVault | **2015-06-01** |
| App Service |**2015-08-01** |

## <a name="template-functions"></a>Funkce šablon

Azure Resource Manageru [funkce](/azure/azure-resource-manager/resource-group-template-functions) poskytuje funkce potřebné k sestavování dynamických šablony. Jako příklad slouží pro úlohy, jako:

* Zřetězení nebo oříznutí řetězce.
* Odkazování na hodnoty z jiných prostředků.
* Iterace na prostředky k nasazení více instancí.

Tyto funkce nejsou k dispozici ve službě Azure Stack:

* Přeskočit
* Take

## <a name="resource-location"></a>Umístění prostředku

Pomocí šablony Azure Resource Manageru `location` atribut umístit prostředkům během nasazení. V Azure najdete v umístění do oblasti, jako je například USA – západ nebo Jižní Ameriky. Ve službě Azure Stack umístění se liší, protože Azure Stack je ve vašem datovém centru. K zajištění, že šablony jsou přenosné mezi Azure a Azure Stack, by měly odkazovat umístění skupiny prostředků jako nasazení jednotlivých prostředků. Můžete provést pomocí `[resourceGroup().Location]` aby všechny prostředky dědit umístění skupiny prostředků. Následující kód je příkladem použití této funkce při nasazení účtu úložiště:

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

## <a name="next-steps"></a>Další postup

* [Nasazení šablon pomocí PowerShellu](azure-stack-deploy-template-powershell.md)
* [Nasazení šablon pomocí Azure CLI](azure-stack-deploy-template-command-line.md)
* [Nasazení šablon pomocí sady Visual Studio](azure-stack-deploy-template-visual-studio.md)
