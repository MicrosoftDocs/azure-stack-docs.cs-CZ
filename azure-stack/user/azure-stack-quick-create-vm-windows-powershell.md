---
title: Vytvoření virtuálního počítače s Windows serverem pomocí prostředí PowerShell ve službě Azure Stack | Dokumentace Microsoftu
description: Vytvoření virtuálního počítače s Windows serverem pomocí prostředí PowerShell ve službě Azure Stack.
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
ms.date: 04/09/2019
ms.author: mabrigg
ms.custom: mvc
ms.reviewer: kivenkat
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: d6293aec1d9a4a7ce58442b21302c09162cc3a61
ms.sourcegitcommit: 87d93cdcdb6efb06e894f56c2f09cad594e1a8b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65712445"
---
# <a name="quickstart-create-a-windows-server-virtual-machine-by-using-powershell-in-azure-stack"></a>Rychlý start: Vytvoření virtuálního počítače s Windows serverem pomocí prostředí PowerShell ve službě Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Virtuální počítač (VM) Windows serveru 2016 můžete vytvořit pomocí Azure Stack Powershellu. Postupujte podle kroků v tomto článku vytváření a používání virtuálního počítače. Tento článek také obsahuje postup:

* Připojení k virtuálnímu počítači pomocí vzdáleného klienta.
* Instalace webového serveru služby IIS a zobrazit výchozí domovskou stránku.
* Vyčištění prostředků.

> [!NOTE]
>  Můžete použít postup popsaný v tomto článku z Azure Stack Development Kit, nebo z externího klienta se systémem Windows, pokud jste připojeni přes síť VPN.

## <a name="prerequisites-for-windows-server-vm"></a>Požadavky virtuálního počítače s Windows serverem

* Ujistěte se, že operátor Azure stacku přidal **Windows serveru 2016** image na marketplace služby Azure Stack.

* Azure Stack vyžaduje určitou verzi prostředí Azure PowerShell k vytváření a správě prostředků. Pokud nemáte nakonfigurované pro službu Azure Stack Powershellu, postupujte podle kroků pro [nainstalovat](../operator/azure-stack-powershell-install.md) prostředí PowerShell.

* S Azure Stack Powershellu nastavení bude nutné se připojit k prostředí Azure Stack. Pokyny naleznete v tématu [připojit ke službě Azure Stack pomocí prostředí PowerShell jako uživatel](azure-stack-powershell-configure-user.md).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků je logický kontejner, do které služby Azure Stack se nasazují a spravují prostředky. Vývojová sada nebo systém integrovat Azure Stack spusťte následující blok kódu a vytvořte skupinu prostředků. 

> [!NOTE]
> Hodnoty jsou přiřazeny pro všechny proměnné v příkladech kódu. Pokud chcete, ale můžete přiřadit nové hodnoty.

```powershell
# Create variables to store the location and resource group names.
$location = "local"
$ResourceGroupName = "myResourceGroup"

New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $location
```

## <a name="create-storage-resources"></a>Vytvoření prostředků úložiště

Vytvoření účtu úložiště a kontejner úložiště pro uložení image Windows serveru 2016.

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

Vytvořte virtuální síť, podsíť a veřejnou IP adresu. Tyto prostředky se používají k zajištění síťové připojení k virtuálnímu počítači.

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

Skupina zabezpečení sítě zabezpečuje virtuální počítač pomocí příchozích a odchozích pravidel. Pojďme vytvořit příchozí pravidlo pro port 3389 povolovat příchozí připojení ke vzdálené ploše a příchozí pravidlo pro port 80 povolit příchozí webový provoz.

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

### <a name="create-a-network-card-for-the-virtual-machine"></a>Vytvoření síťové karty pro virtuální počítač

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

## <a name="create-a-virtual-machine"></a>Vytvořit virtuální počítač

Vytvořte konfiguraci virtuálního počítače. Tato konfigurace zahrnuje nastavení používané při nasazení virtuálního počítače. Příklad: přihlašovací údaje, velikost a image virtuálního počítače.

```powershell
# Define a credential object to store the username and password for the virtual machine
$UserName='demouser'
$Password='Password@123'| ConvertTo-SecureString -Force -AsPlainText
$Credential=New-Object PSCredential($UserName,$Password)

# Create the virtual machine configuration object
$VmName = "VirtualMachinelatest"
$VmSize = "Standard_A1"
$VirtualMachine = New-AzureRmVMConfig `
  -VMName $VmName `
  -VMSize $VmSize

$VirtualMachine = Set-AzureRmVMOperatingSystem `
  -VM $VirtualMachine `
  -Windows `
  -ComputerName "MainComputer" `
  -Credential $Credential

$VirtualMachine = Set-AzureRmVMSourceImage `
  -VM $VirtualMachine `
  -PublisherName "MicrosoftWindowsServer" `
  -Offer "WindowsServer" `
  -Skus "2016-Datacenter" `
  -Version "latest"

# Sets the operating system disk properties on a virtual machine.
$VirtualMachine = Set-AzureRmVMOSDisk `
  -VM $VirtualMachine `
  -CreateOption FromImage | `
  Set-AzureRmVMBootDiagnostics -ResourceGroupName $ResourceGroupName `
  -StorageAccountName $StorageAccountName -Enable |`
  Add-AzureRmVMNetworkInterface -Id $nic.Id


# Create the virtual machine.
New-AzureRmVM `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -VM $VirtualMachine
```

## <a name="connect-to-the-virtual-machine"></a>Připojení k virtuálnímu počítači

Na vzdáleném připojení k virtuální počítač, který jste vytvořili v předchozím kroku musíte svou veřejnou IP adresu. Spuštěním následujícího příkazu získejte veřejnou IP adresu virtuálního počítače:

```powershell
Get-AzureRmPublicIpAddress `
  -ResourceGroupName $ResourceGroupName | Select IpAddress
```

Pomocí následujícího příkazu vytvořte s virtuálním počítačem relaci vzdálené plochy. IP adresu nahraďte veřejnou IP adresou (*publicIPAddress*) vašeho virtuálního počítače. Po zobrazení výzvy zadejte uživatelské jméno a heslo použité při vytváření virtuálního počítače.

```powershell
mstsc /v <publicIpAddress>
```

## <a name="install-iis-via-powershell"></a>Instalace služby IIS pomocí PowerShellu

Teď, když jste přihlášení k virtuálnímu počítači Azure, můžete použít jeden řádek Powershellu k instalaci IIS a aktivace pravidla místní brány firewall pro povolení webového provozu. Otevřete příkazový řádek PowerShellu a spusťte následující příkaz:

```powershell
Install-WindowsFeature -name Web-Server -IncludeManagementTools
```

## <a name="view-the-iis-welcome-page"></a>Zobrazení úvodní stránky služby IIS

S nainstalovanou službou IIS a s portem 80 otevřete na svém virtuálním počítači můžete použít libovolný prohlížeč Chcete-li zobrazit výchozí úvodní stránka služby IIS. Použití *publicIpAddress* popsanou v předchozí části pro návštěvu výchozí stránky.

![Výchozí web služby IIS](./media/azure-stack-quick-create-vm-windows-powershell/default-iis-website.png)

## <a name="delete-the-virtual-machine"></a>Odstraňte virtuální počítač

Pokud už nepotřebujete, použijte následující příkaz k odebrání skupiny prostředků, která obsahuje virtuální počítač a související prostředky:

```powershell
Remove-AzureRmResourceGroup `
  -Name $ResourceGroupName
```

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste nasadili jednoduchý virtuální počítač Windows. Další informace o virtuálních počítačích Azure Stack, [důležité informace týkající se virtuálních počítačů ve službě Azure Stack](azure-stack-vm-considerations.md).
