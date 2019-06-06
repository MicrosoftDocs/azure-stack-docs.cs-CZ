---
title: Nasazení šablony pomocí prostředí PowerShell ve službě Azure Stack | Dokumentace Microsoftu
description: Nasazení šablony pomocí prostředí PowerShell ve službě Azure Stack.
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
ms.date: 04/08/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: be325c41023c859ee70b8ee932673db75f41ec4b
ms.sourcegitcommit: 7f39bdc83717c27de54fe67eb23eb55dbab258a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2019
ms.locfileid: "66691442"
---
# <a name="deploy-a-template-using-powershell-in-azure-stack"></a>Nasazení šablony pomocí prostředí Powershell ve službě Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Prostředí PowerShell můžete použít k nasazení šablony Azure Resource Manageru ke službě Azure Stack. Tento článek popisuje použití Powershellu k nasazení šablony.

## <a name="run-azurerm-powershell-cmdlets"></a>Spusťte rutiny AzureRM Powershellu

Tento příklad používá **AzureRM** rutiny Powershellu a šablony uložené na Githubu. Šablona vytvoří virtuální počítač Windows Server 2012 R2 Datacenter.

>[!NOTE]
> Než se pokusíte v tomto příkladu, ujistěte se, že jste [nakonfigurovali PowerShell](azure-stack-powershell-configure-user.md) pro uživatele Azure stacku.

1. Přejděte [AzureStackGitHub úložiště](https://aka.ms/AzureStackGitHub) a najít **101-simple-windows-vm** šablony. Uložte šablonu do tohoto umístění: `C:\templates\azuredeploy-101-simple-windows-vm.json`.
2. Otevřete příkazový řádek se zvýšenými oprávněními prostředí PowerShell.
3. Nahraďte `username` a `password` v následujícím skriptu se uživatelské jméno a heslo a pak spusťte skript:

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
    > Pokaždé když spustíte tento skript, zvýšit hodnotu `$myNum` parametr, abyste zabránili přepsání vašeho nasazení.

4. Otevřete portál, vyberte Azure Stack **Procházet**a pak vyberte **virtuálních počítačů** najít nového virtuálního počítače (**myDeployment001**).

## <a name="next-steps"></a>Další postup

- [Nasazení šablony pomocí sady Visual Studio](azure-stack-deploy-template-visual-studio.md)
