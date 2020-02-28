---
title: Nasazení šablony pomocí PowerShellu v Azure Stackovém centru
description: Nasaďte šablonu pomocí PowerShellu v Azure Stackovém centru.
author: mattbriggs
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/23/2019
ms.openlocfilehash: 9081464e05825cbd211f043818e841276a840303
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77704263"
---
# <a name="deploy-a-template-using-powershell-in-azure-stack-hub"></a>Nasazení šablony pomocí PowerShellu v Azure Stackovém centru

K nasazení Azure Resource Manager šablon do centra Azure Stack můžete použít PowerShell. Tento článek popisuje, jak pomocí PowerShellu nasadit šablonu.

## <a name="run-azurerm-powershell-cmdlets"></a>Spuštění rutin prostředí PowerShell pro AzureRM

Tento příklad používá rutiny prostředí PowerShell **AzureRM** a šablonu uloženou na GitHubu. Šablona vytvoří virtuální počítač s Windows Serverem 2012 R2 Datacenter.

>[!NOTE]
> Než tento příklad vyzkoušíte, ujistěte se, že jste [nakonfigurovali PowerShell](azure-stack-powershell-configure-user.md) pro uživatele centra Azure Stack.

1. Projděte si [úložiště AzureStack-Starter Templates](https://aka.ms/AzureStackGitHub) a vyhledejte šablonu **101-Simple-Windows-VM** . Uložte šablonu do tohoto umístění: `C:\templates\azuredeploy-101-simple-windows-vm.json`.
2. Otevřete příkazový řádek PowerShellu se zvýšenými oprávněními.
3. Pomocí uživatelského jména a hesla nahraďte `username` a `password` v následujícím skriptu a spusťte tento skript:

    ```powershell
    # Set deployment variables
    $myNum = "001" # Modify this per deployment
    $RGName = "myRG$myNum"
    $myLocation = "yourregion" # local for the ASDK

    # Create resource group for template deployment
    New-AzureRmResourceGroup -Name $RGName -Location $myLocation

    # Deploy simple IaaS template
    New-AzureRmResourceGroupDeployment `
        -Name myDeployment$myNum `
        -ResourceGroupName $RGName `
        -TemplateUri <path>\AzureStack-QuickStart-Templates\101-vm-windows-create\azuredeploy.json `
        -AdminUsername <username> `
        -AdminPassword ("<password>" | ConvertTo-SecureString -AsPlainText -Force)
    ```

    >[!IMPORTANT]
    > Pokaždé, když tento skript spustíte, zvyšte hodnotu parametru `$myNum`, aby se zabránilo přepsání nasazení.

4. Otevřete portál centra Azure Stack, vyberte **Procházet**a pak vyberte **virtuální počítače** , abyste našli nový virtuální počítač (**myDeployment001**).

## <a name="cancel-a-running-template-deployment"></a>Zrušení běžícího nasazení šablony

Pokud chcete zrušit nasazení spuštěné šablony, použijte rutinu `Stop-AzureRmResourceGroupDeployment` PowerShellu.

## <a name="next-steps"></a>Další kroky

- [Nasazení šablony pomocí sady Visual Studio](azure-stack-deploy-template-visual-studio.md)
