---
title: Vytvoření virtuálního počítače s Windows serverem pomocí prostředí PowerShell v centru pro Azure Stack | Microsoft Docs
description: Vytvořte virtuální počítač s Windows serverem pomocí PowerShellu v centru Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 11/11/2019
ms.author: mabrigg
ms.custom: mvc
ms.reviewer: kivenkat
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: a6b051245821f3def20c20af41301d584b10eb0d
ms.sourcegitcommit: d62400454b583249ba5074a5fc375ace0999c412
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "76023175"
---
# <a name="quickstart-create-a-windows-server-vm-by-using-powershell-in-azure-stack-hub"></a>Rychlý Start: Vytvoření virtuálního počítače s Windows serverem pomocí PowerShellu v Azure Stackovém centru

Virtuální počítač s Windows serverem 2016 můžete vytvořit pomocí centra Azure Stack PowerShell. Pokud chcete vytvořit a použít virtuální počítač, postupujte podle kroků v tomto článku. Tento článek také popisuje kroky pro:

* Připojte se k virtuálnímu počítači pomocí vzdáleného klienta.
* Nainstalujte webový server služby IIS a zobrazte výchozí domovskou stránku.
* Vyčistěte prostředky.

> [!NOTE]
>  Postup popsaný v tomto článku můžete spustit z Azure Stack Development Kit nebo z externího klienta založeného na systému Windows, pokud jste připojení prostřednictvím sítě VPN.

## <a name="prerequisites-for-windows-server-vm"></a>Předpoklady pro virtuální počítač s Windows serverem

* Ujistěte se, že váš operátor centra Azure Stack přidal do tržiště centra Azure Stack image **Windows serveru 2016** .

* Azure Stack centrum vyžaduje pro vytváření a správu prostředků specifickou verzi Azure PowerShell. Pokud nemáte PowerShell nakonfigurovaný pro centrum Azure Stack, postupujte podle pokynů k [instalaci](../operator/azure-stack-powershell-install.md) PowerShellu.

* S nastavením prostředí PowerShell centra Azure Stack se budete muset připojit k prostředí centra Azure Stack. Pokyny najdete v tématu [připojení k Azure Stack hub pomocí PowerShellu jako uživatel](azure-stack-powershell-configure-user.md).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků je logický kontejner, ve kterém se nasazují a spravují prostředky centra Azure Stack. Z vývojové sady nebo integrovaného systému centra Azure Stack spusťte následující blok kódu, který vytvoří skupinu prostředků. 

> [!NOTE]
> Hodnoty jsou přiřazeny pro všechny proměnné v příkladech kódu. V případě potřeby však můžete přiřadit nové hodnoty.

```powershell
# Create variables to store the location and resource group names.
$location = "local"
$ResourceGroupName = "myResourceGroup"

New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $location
```

## <a name="create-storage-resources"></a>Vytvoření prostředků úložiště

Vytvořte účet úložiště, do kterého se uloží výstup diagnostiky spouštění.

```powershell
# Create variables to store the storage account name and the storage account SKU information
$StorageAccountName = "mystorageaccount"
$SkuName = "Standard_LRS"

# Create a new storage account
$StorageAccount = New-AzureRMStorageAccount `
  -Location $location `
  -ResourceGroupName $ResourceGroupName `
  -Type $SkuName `
  -Name $StorageAccountName

Set-AzureRmCurrentStorageAccount `
  -StorageAccountName $storageAccountName `
  -ResourceGroupName $resourceGroupName

```

## <a name="create-networking-resources"></a>Vytvoření síťových prostředků

Vytvořte virtuální síť, podsíť a veřejnou IP adresu. Tyto prostředky slouží k poskytování síťového připojení k virtuálnímu počítači.

```powershell
# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig `
  -Name mySubnet `
  -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -Name MyVnet `
  -AddressPrefix 192.168.0.0/16 `
  -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -AllocationMethod Static `
  -IdleTimeoutInMinutes 4 `
  -Name "mypublicdns$(Get-Random)"
```

### <a name="create-a-network-security-group-and-a-network-security-group-rule"></a>Vytvoření skupiny zabezpečení sítě a pravidla skupiny zabezpečení sítě

Skupina zabezpečení sítě zabezpečuje virtuální počítač pomocí příchozích a odchozích pravidel. Pojďme vytvořit příchozí pravidlo pro port 3389, které umožní příchozí připojení ke vzdálené ploše a příchozí pravidlo pro port 80 pro povolení příchozího webového provozu.

```powershell
# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNetworkSecurityGroupRuleRDP `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1000 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 3389 `
  -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig `
  -Name myNetworkSecurityGroupRuleWWW `
  -Protocol Tcp `
  -Direction Inbound `
  -Priority 1001 `
  -SourceAddressPrefix * `
  -SourcePortRange * `
  -DestinationAddressPrefix * `
  -DestinationPortRange 80 `
  -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -Name myNetworkSecurityGroup `
  -SecurityRules $nsgRuleRDP,$nsgRuleWeb
```

### <a name="create-a-network-card-for-the-vm"></a>Vytvořit síťovou kartu pro virtuální počítač

Síťová karta připojuje virtuální počítač k podsíti, skupině zabezpečení sítě a veřejné IP adrese.

```powershell
# Create a virtual network card and associate it with public IP address and NSG
$nic = New-AzureRmNetworkInterface `
  -Name myNic `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id
```

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

Vytvořte konfiguraci virtuálního počítače. Tato konfigurace zahrnuje nastavení použitá při nasazení virtuálního počítače. Například: přihlašovací údaje, velikost a bitová kopie virtuálního počítače.

```powershell
# Define a credential object to store the username and password for the VM
$UserName='demouser'
$Password='Password@123'| ConvertTo-SecureString -Force -AsPlainText
$Credential=New-Object PSCredential($UserName,$Password)

# Create the VM configuration object
$VmName = "VirtualMachinelatest"
$VmSize = "Standard_A1"
$VirtualMachine = New-AzureRmVMConfig `
  -VMName $VmName `
  -VMSize $VmSize

$VirtualMachine = Set-AzureRmVMOperatingSystem `
  -VM $VirtualMachine `
  -Windows `
  -ComputerName "MainComputer" `
  -Credential $Credential -ProvisionVMAgent

$VirtualMachine = Set-AzureRmVMSourceImage `
  -VM $VirtualMachine `
  -PublisherName "MicrosoftWindowsServer" `
  -Offer "WindowsServer" `
  -Skus "2016-Datacenter" `
  -Version "latest"

# Sets the operating system disk properties on a VM.
$VirtualMachine = Set-AzureRmVMOSDisk `
  -VM $VirtualMachine `
  -CreateOption FromImage | `
  Set-AzureRmVMBootDiagnostics -ResourceGroupName $ResourceGroupName `
  -StorageAccountName $StorageAccountName -Enable |`
  Add-AzureRmVMNetworkInterface -Id $nic.Id


# Create the VM.
New-AzureRmVM `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -VM $VirtualMachine
```

## <a name="connect-to-the-vm"></a>Připojení k virtuálnímu počítači

Abyste se vzdáleně přihlásili k virtuálnímu počítači, který jste vytvořili v předchozím kroku, budete potřebovat jeho veřejnou IP adresu. Spuštěním následujícího příkazu Získejte veřejnou IP adresu virtuálního počítače:

```powershell
Get-AzureRmPublicIpAddress `
  -ResourceGroupName $ResourceGroupName | Select IpAddress
```

Pomocí následujícího příkazu vytvořte relaci vzdálené plochy s VIRTUÁLNÍm počítačem. Nahraďte IP adresu veřejnou IP adresou (*publicIPAddress*) vašeho virtuálního počítače. Po zobrazení výzvy zadejte uživatelské jméno a heslo, které jste použili při vytváření virtuálního počítače.

```powershell
mstsc /v <publicIpAddress>
```

## <a name="install-iis-via-powershell"></a>Instalace služby IIS pomocí PowerShellu

Teď, když jste se přihlásili k virtuálnímu počítači Azure, můžete k instalaci IIS použít jeden řádek PowerShellu a povolit místní pravidlo brány firewall, které povoluje webový provoz. Otevřete příkazový řádek PowerShellu a spusťte následující příkaz:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Zobrazení úvodní stránky služby IIS

Když je služba IIS nainstalovaná a na vašem VIRTUÁLNÍm počítači je otevřený port 80, můžete k zobrazení výchozí uvítací stránky služby IIS použít libovolný prohlížeč. K návštěvě výchozí stránky použijte *publicIpAddress* , který jste si popsali v předchozí části.

![Výchozí web služby IIS](./media/azure-stack-quick-create-vm-windows-powershell/default-iis-website.png)

## <a name="delete-the-vm"></a>Odstranění virtuálního počítače

Pokud už je nepotřebujete, odeberte skupinu prostředků, která obsahuje virtuální počítač a související prostředky, pomocí následujícího příkazu:

```powershell
Remove-AzureRmResourceGroup `
  -Name $ResourceGroupName
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili jednoduchý virtuální počítač s Windows. Další informace o Azure Stackch virtuálních počítačích centra najdete v [Azure Stack funkcí virtuálních počítačů centra](azure-stack-vm-considerations.md).
