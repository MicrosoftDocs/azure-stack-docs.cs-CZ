---
title: Použití šablon Správce prostředků pro spravované disky v centru Azure Stack
description: Přečtěte si o rozdílech mezi spravovanými a nespravovanými disky při použití šablon Azure Resource Manager.
author: sethmanheim
ms.author: sethm
ms.date: 8/25/2020
ms.topic: conceptual
ms.reviewer: wellsluo
ms.lastreviewed: 8/25/2020
ms.openlocfilehash: 2d2f274fffd1a07857b79255587b659003a3a1ce
ms.sourcegitcommit: 65a115d1499b5fe16b6fe1c31cce43be21d05ef8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/25/2020
ms.locfileid: "88823249"
---
# <a name="use-vm-managed-disks-templates"></a>Použití šablon spravovaných disků virtuálních počítačů

Tento článek popisuje rozdíly mezi spravovanými a nespravovanými disky při použití šablon Azure Resource Manager ke zřízení virtuálních počítačů v centru Azure Stack. Příklady vám pomůžou převést existující šablony, které používají nespravované disky na spravované disky.

## <a name="unmanaged-disks-template-formatting"></a>Formátování šablony nespravovaných disků

Začněte podíváme se na to, jak jsou nasazené nespravované disky. Při vytváření nespravovaných disků potřebujete účet úložiště pro uchovávání souborů VHD. Můžete vytvořit nový účet úložiště, nebo použít jiný, který už existuje. Vytvořte nový prostředek účtu úložiště v bloku Resources šablony následujícím způsobem:

```json
{
    "type": "Microsoft.Storage/storageAccounts",
    "apiVersion": "2017-10-01",
    "name": "[variables('storageAccountName')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "kind": "Storage"
}
```

V rámci objektu virtuálního počítače přidejte závislost na účet úložiště, abyste měli jistotu, že se vytvoří před virtuálním počítačem. V `storageProfile` části zadejte úplný identifikátor URI umístění virtuálního pevného disku, který odkazuje na účet úložiště a který je nutný pro disk s operačním systémem a všechny datové disky. Následující příklad vytvoří jeden disk s operačním systémem z image a jeden prázdný datový disk s 1023 GBou velikostí:

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2017-12-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "name": "osdisk",
                "vhd": {
                    "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/osdisk.vhd')]"
                },
                "caching": "ReadWrite",
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "name": "datadisk1",
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "vhd": {
                        "uri": "[concat(reference(resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))).primaryEndpoints.blob, 'vhds/datadisk1.vhd')]"
                    },
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

## <a name="managed-disks-template-formatting"></a>Formátování šablon spravovaných disků

Se službou Azure Managed disks se disk stane prostředkem nejvyšší úrovně a už nepotřebuje, aby uživatel vytvořil účet úložiště. Spravované disky se nejdřív zavedly do `2017-03-30` verze rozhraní API. V následujících oddílech se seznámíte s výchozími nastaveními a vysvětlete, jak tyto disky dále upravovat.

### <a name="default-managed-disk-settings"></a>Výchozí nastavení spravovaného disku

Pokud chcete vytvořit virtuální počítač se spravovanými disky, nebudete už muset vytvořit prostředek účtu úložiště. V níže uvedeném příkladu šablony jsou některé rozdíly oproti předchozím nespravovaným diskovým příkladům:

- `apiVersion`Je verze pro typ prostředku "virtualMachines", který podporuje spravované disky.
- `osDisk` a `dataDisks` už neodkazuje na konkrétní identifikátor URI pro virtuální pevný disk.
- Při nasazování bez zadání dalších vlastností disk používá typ úložiště na základě velikosti virtuálního počítače. Pokud například používáte velikost virtuálního počítače, která podporuje Premium Storage (velikosti s "s" v názvu, například Standard_DS2_v2), budou standardně nakonfigurované prémiové disky. Tuto změnu můžete změnit pomocí nastavení skladové položky (SKU) disku pro určení typu úložiště.
- Pokud není zadaný žádný název disku, bude mít formát `<VMName>_OsDisk_1_<randomstring>` pro disk s operačním systémem a `<VMName>_disk<#>_<randomstring>` pro každý datový disk.
  - Pokud se virtuální počítač vytváří z vlastní image, pak se výchozí nastavení pro typ účtu úložiště a název disku načtou z vlastností disku definovaných v prostředku vlastní image. Ty mohou být přepsány zadáním hodnot pro tyto hodnoty v šabloně.
- Ve výchozím nastavení je ukládání do mezipaměti disku pro disk s operačním systémem nastaveno na **čtení a zápis** **a pro datové disky není k** dispozici.
- V následujícím příkladu je stále závislá na účtu úložiště, i když se jedná jenom o úložiště diagnostiky a není potřeba pro diskové úložiště:

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2017-12-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "diskSizeGB": 1023,
                    "lun": 0,
                    "createOption": "Empty"
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="use-a-top-level-managed-disk-resource"></a>Použít prostředek spravovaného disku nejvyšší úrovně

Jako alternativu k zadání konfigurace disku v objektu virtuálního počítače můžete vytvořit diskový prostředek na nejvyšší úrovni a připojit ho jako součást vytváření virtuálního počítače. Nezapomeňte použít `2017-03-30` jako `disks` verzi rozhraní API prostředků. Například můžete vytvořit prostředek disku následujícím způsobem, který se použije jako datový disk. V tomto příkladu `vmName` se používá jako součást názvu disku:

```json
{
    "type": "Microsoft.Compute/disks",
    "apiVersion": "2017-03-30",
    "name": "[concat(variables('vmName'),'-datadisk1')]",
    "location": "[resourceGroup().location]",
    "sku": {
        "name": "Standard_LRS"
    },
    "properties": {
        "creationData": {
            "createOption": "Empty"
        },
        "diskSizeGB": 1023
    }
}
```

V rámci objektu virtuálního počítače odkazujte na objekt disku, který se má připojit. Zadáním ID prostředku spravovaného disku vytvořeného ve `managedDisk` vlastnosti umožníte vytvoření přílohy disku jako virtuálního počítače. `apiVersion`Pro prostředek virtuálního počítače je nastavená na `2017-12-01` . Přidala se závislost na prostředku disku, aby se zajistilo jejich úspěšné vytvoření před vytvořením virtuálního počítače:

```json
{
    "type": "Microsoft.Compute/virtualMachines",
    "apiVersion": "2017-12-01",
    "name": "[variables('vmName')]",
    "location": "[resourceGroup().location]",
    "dependsOn": [
        "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
        "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]",
        "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
    ],
    "properties": {
        "hardwareProfile": {...},
        "osProfile": {...},
        "storageProfile": {
            "imageReference": {
                "publisher": "MicrosoftWindowsServer",
                "offer": "WindowsServer",
                "sku": "[parameters('windowsOSVersion')]",
                "version": "latest"
            },
            "osDisk": {
                "createOption": "FromImage"
            },
            "dataDisks": [
                {
                    "lun": 0,
                    "name": "[concat(variables('vmName'),'-datadisk1')]",
                    "createOption": "attach",
                    "managedDisk": {
                        "id": "[resourceId('Microsoft.Compute/disks/', concat(variables('vmName'),'-datadisk1'))]"
                    }
                }
            ]
        },
        "networkProfile": {...},
        "diagnosticsProfile": {...}
    }
}
```

### <a name="create-managed-availability-sets-with-vms-using-managed-disks"></a>Vytvoření spravovaných skupin dostupnosti pomocí virtuálních počítačů pomocí spravovaných disků

Pokud chcete vytvořit spravované skupiny dostupnosti s virtuálními počítači pomocí spravovaných disků, přidejte `sku` objekt do prostředku skupiny dostupnosti a nastavte `name` vlastnost na `Aligned` . Tato vlastnost zajišťuje, aby byly disky pro každý virtuální počítač dostatečně izolované od sebe navzájem, aby se předešlo jednomu bodu selhání. Všimněte si také, že `apiVersion` u prostředku skupiny dostupnosti je nastavená tato `2017-12-01` :

```json
{
    "type": "Microsoft.Compute/availabilitySets",
    "apiVersion": "2017-12-01",
    "location": "[resourceGroup().location]",
    "name": "[variables('avSetName')]",
    "properties": {
        "PlatformUpdateDomainCount": 1,
        "PlatformFaultDomainCount": 2
    },
    "sku": {
        "name": "Aligned"
    }
}
```

## <a name="next-steps"></a>Další kroky

<!--
* For full templates that use managed disks visit the following Azure Quickstart Repo links.
    * [Windows VM with managed disk](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-windows)
    * [Linux VM with managed disk](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-simple-linux)
-->

- Další informace o spravovaných discích najdete v tématu [spravované disky centra Azure Stack](azure-stack-managed-disk-considerations.md) .
- Projděte si referenční dokumentaci k šabloně pro prostředky virtuálních počítačů najdete v referenčních informacích k [šabloně Microsoft. COMPUTE/virtualMachines](/azure/templates/microsoft.compute/2017-12-01/virtualmachines).
- Projděte si referenční dokumentaci k šabloně pro diskové prostředky v [referenčním dokumentu šablony Microsoft. COMPUTE/disks](/azure/templates/microsoft.compute/2017-03-30/disks) .
