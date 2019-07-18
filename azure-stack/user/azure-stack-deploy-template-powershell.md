---
title: Nasazení šablony pomocí PowerShellu v Azure Stack | Microsoft Docs
description: Nasazení šablony pomocí PowerShellu v Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 12fe32d7-0a1a-4c02-835d-7b97f151ed0f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/17/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 6824e6bfd0b6c824783c82041fb1a51ba8f5213f
ms.sourcegitcommit: 2063332b4d7f98ee944dd1f443847eea70eb5614
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2019
ms.locfileid: "68303118"
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
3. Pomocí `username` uživatelského `password` jména a hesla nahraďte a v následujícím skriptu spusťte skript:

    ```powershell
    # Set deployment variables
    $myNum = "001" # Modify this per deployment
    $RGName = "myRG$myNum"
    $myLocation = "local"

    # Create resource group for template deployment
    New-AzureRmResourceGroup -Name $RGName -Location $myLocation

    # Deploy simple IaaS template
    New-AzureRmResourceGroupDeployment `
        -Name myDeployment$myNum `
        -ResourceGroupName $RGName `
        -TemplateFile c:\templates\azuredeploy-101-simple-windows-vm.json `
        -NewStorageAccountName mystorage$myNum `
        -DnsNameForPublicIP mydns$myNum `
        -AdminUsername <username> `
        -AdminPassword ("<password>" | ConvertTo-SecureString -AsPlainText -Force) `
        -VmName myVM$myNum `
        -WindowsOSVersion 2012-R2-Datacenter
    ```

    >[!IMPORTANT]
    > Pokaždé, když tento skript spustíte, zvyšte hodnotu `$myNum` parametru, aby nedošlo k přepsání nasazení.

4. Otevřete portál Azure Stack, vyberte **Procházet**a pak vyberte **virtuální počítače** , abyste našli nový virtuální počítač (**myDeployment001**).

## <a name="next-steps"></a>Další postup

- [Nasazení šablony pomocí sady Visual Studio](azure-stack-deploy-template-visual-studio.md)
