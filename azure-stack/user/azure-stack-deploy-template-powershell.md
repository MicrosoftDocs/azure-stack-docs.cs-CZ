---
title: Nasazení šablony pomocí PowerShellu v Azure Stack | Microsoft Docs
description: Nasazení šablony pomocí PowerShellu v Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 09/23/2019
ms.openlocfilehash: af4ac82e03b96b4fc3f6d728cbebf5a6fa9d6388
ms.sourcegitcommit: e8aa26b078a9bab09c8fafd888a96785cc7abb4d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2019
ms.locfileid: "71708965"
---
# <a name="deploy-a-template-using-powershell-in-azure-stack"></a>Nasazení šablony pomocí PowerShellu v Azure Stack

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

K nasazení Azure Resource Manager šablon Azure Stack můžete použít PowerShell. Tento článek popisuje, jak pomocí PowerShellu nasadit šablonu.

## <a name="run-azurerm-powershell-cmdlets"></a>Spuštění rutin prostředí PowerShell pro AzureRM

Tento příklad používá rutiny prostředí PowerShell **AzureRM** a šablonu uloženou na GitHubu. Šablona vytvoří virtuální počítač s Windows Serverem 2012 R2 Datacenter.

>[!NOTE]
> Než si vyzkoušíte tento příklad, ujistěte se, že jste pro uživatele Azure Stack [nakonfigurovali PowerShell](azure-stack-powershell-configure-user.md) .

1. Procházejte [úložiště AzureStackGitHub](https://aka.ms/AzureStackGitHub) a vyhledejte šablonu **101-Simple-Windows-VM** . Uložte šablonu do tohoto umístění: `C:\templates\azuredeploy-101-simple-windows-vm.json`.
2. Otevřete příkazový řádek PowerShellu se zvýšenými oprávněními.
3. Pomocí uživatelského jména a hesla nahraďte `username` a `password` v následujícím skriptu a spusťte skript:

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
    > Pokaždé, když tento skript spustíte, zvyšte hodnotu `$myNum` parametru, aby nedošlo k přepsání nasazení.

4. Otevřete portál Azure Stack, vyberte **Procházet**a pak vyberte **virtuální počítače** , abyste našli nový virtuální počítač (**myDeployment001**).

## <a name="cancel-a-running-template-deployment"></a>Zrušení běžícího nasazení šablony

Pokud chcete zrušit nasazení spuštěné šablony, použijte rutinu `Stop-AzureRmResourceGroupDeployment` prostředí PowerShell.

## <a name="next-steps"></a>Další kroky

- [Nasazení šablony pomocí sady Visual Studio](azure-stack-deploy-template-visual-studio.md)
