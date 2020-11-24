---
title: Nasazení šablony pomocí PowerShellu v Azure Stackovém centru
description: Nasaďte šablonu pomocí PowerShellu v Azure Stackovém centru.
author: mattbriggs
ms.topic: article
ms.date: 11/20/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 11/20/2020
ms.openlocfilehash: 5bda04678c672c3951cc7644b3dc89f5d9ad25cf
ms.sourcegitcommit: 8c745b205ea5a7a82b73b7a9daf1a7880fd1bee9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95518326"
---
# <a name="deploy-a-template-using-powershell-in-azure-stack-hub"></a>Nasazení šablony pomocí PowerShellu v Azure Stackovém centru

K nasazení Azure Resource Manager šablon do centra Azure Stack můžete použít PowerShell. Tento článek popisuje, jak pomocí PowerShellu nasadit šablonu.

## <a name="run-powershell-cmdlets"></a>Spustit rutiny PowerShellu

### <a name="az-modules"></a>[AZ modules](#tab/az)

Tento příklad využívá **AZ** PowerShell rutiny a šablonu uloženou na GitHubu. Šablona vytvoří virtuální počítač s Windows Serverem 2012 R2 Datacenter.

>[!NOTE]
> Než tento příklad vyzkoušíte, ujistěte se, že jste [nakonfigurovali PowerShell](azure-stack-powershell-configure-user.md) pro uživatele centra Azure Stack.

1. Projděte si [úložiště AzureStack-Starter Templates](https://aka.ms/AzureStackGitHub) a vyhledejte šablonu **101-Simple-Windows-VM** . Uložte šablonu do tohoto umístění: `C:\templates\azuredeploy-101-simple-windows-vm.json` .
2. Otevřete příkazový řádek PowerShellu se zvýšenými oprávněními.
3. `username` `password` Pomocí uživatelského jména a hesla nahraďte a v následujícím skriptu spusťte skript:

    ```powershell
    # Set deployment variables
    $myNum = "001" # Modify this per deployment
    $RGName = "myRG$myNum"
    $myLocation = "yourregion" # local for the ASDK

    # Create resource group for template deployment
    New-AzResourceGroup -Name $RGName -Location $myLocation

    # Deploy simple IaaS template
    New-AzResourceGroupDeployment `
        -Name myDeployment$myNum `
        -ResourceGroupName $RGName `
        -TemplateUri <path>\AzureStack-QuickStart-Templates\101-vm-windows-create\azuredeploy.json `
        -AdminUsername <username> `
        -AdminPassword ("<password>" | ConvertTo-SecureString -AsPlainText -Force)
    ```

    >[!IMPORTANT]
    > Pokaždé, když tento skript spustíte, zvyšte hodnotu `$myNum` parametru, aby nedošlo k přepsání nasazení.

4. Otevřete portál centra Azure Stack, vyberte **Procházet** a pak vyberte  **virtuální počítače** , abyste našli nový virtuální počítač (**myDeployment001**).

### <a name="azurerm-modules"></a>[Moduly AzureRM](#tab/azurerm)

Tento příklad používá rutiny prostředí PowerShell **AzureRM** a šablonu uloženou na GitHubu. Šablona vytvoří virtuální počítač s Windows Serverem 2012 R2 Datacenter.

>[!NOTE]
> Než tento příklad vyzkoušíte, ujistěte se, že jste [nakonfigurovali PowerShell](azure-stack-powershell-configure-user.md) pro uživatele centra Azure Stack.

1. Projděte si [úložiště AzureStack-Starter Templates](https://aka.ms/AzureStackGitHub) a vyhledejte šablonu **101-Simple-Windows-VM** . Uložte šablonu do tohoto umístění: `C:\templates\azuredeploy-101-simple-windows-vm.json` .
2. Otevřete příkazový řádek PowerShellu se zvýšenými oprávněními.
3. `username` `password` Pomocí uživatelského jména a hesla nahraďte a v následujícím skriptu spusťte skript:

    ```powershell
    # Set deployment variables
    $myNum = "001" # Modify this per deployment
    $RGName = "myRG$myNum"
    $myLocation = "yourregion" # local for the ASDK

    # Create resource group for template deployment
    New-AzureRMResourceGroup -Name $RGName -Location $myLocation

    # Deploy simple IaaS template
    New-AzureRMResourceGroupDeployment `
        -Name myDeployment$myNum `
        -ResourceGroupName $RGName `
        -TemplateUri <path>\AzureStack-QuickStart-Templates\101-vm-windows-create\azuredeploy.json `
        -AdminUsername <username> `
        -AdminPassword ("<password>" | ConvertTo-SecureString -AsPlainText -Force)
    ```

    >[!IMPORTANT]
    > Pokaždé, když tento skript spustíte, zvyšte hodnotu `$myNum` parametru, aby nedošlo k přepsání nasazení.

4. Otevřete portál centra Azure Stack, vyberte **Procházet** a pak vyberte  **virtuální počítače** , abyste našli nový virtuální počítač (**myDeployment001**).

---
## <a name="cancel-a-running-template-deployment"></a>Zrušení běžícího nasazení šablony

Pokud chcete zrušit nasazení spuštěné šablony, použijte `Stop-AzResourceGroupDeployment` rutinu PowerShellu.

## <a name="next-steps"></a>Další kroky

- [Nasazení šablony pomocí sady Visual Studio](azure-stack-deploy-template-visual-studio.md)
