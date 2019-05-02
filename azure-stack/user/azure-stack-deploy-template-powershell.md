---
title: Nasazení šablon pomocí prostředí PowerShell ve službě Azure Stack | Dokumentace Microsoftu
description: Nasazení šablony do služby Azure Stack powershellu.
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: 12fe32d7-0a1a-4c02-835d-7b97f151ed0f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 04/08/2019
ms.date: 04/29/2019
ms.author: v-jay
ms.reviewer: unknown
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: 9c1df99557293030dc0b1c0693b0bbc517a3f0ff
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "64298599"
---
# <a name="deploy-a-template-to-azure-stack-using-powershell"></a>Nasazení šablony do služby Azure Stack pomocí Powershellu

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

- [Nasazení šablon pomocí sady Visual Studio](azure-stack-deploy-template-visual-studio.md)
