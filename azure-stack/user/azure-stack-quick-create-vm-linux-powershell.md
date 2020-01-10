---
title: Vytvoření virtuálního počítače se systémem Linux pomocí prostředí PowerShell v centru Azure Stack | Microsoft Docs
description: Vytvořte virtuální počítač se systémem Linux pomocí prostředí PowerShell v centru Azure Stack.
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
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 4d846734c609485532da4534231a7b94c0514f21
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75816016"
---
# <a name="quickstart-create-a-linux-server-vm-by-using-powershell-in-azure-stack-hub"></a>Rychlý Start: Vytvoření virtuálního počítače s Linux serverem pomocí prostředí PowerShell v centru Azure Stack

*Platí pro: Azure Stack integrovaných systémů centra a Azure Stack Development Kit*

Virtuální počítač s Ubuntu serverem 16,04 LTS můžete vytvořit pomocí centra prostředí PowerShell pro Azure Stack. V tomto článku vytvoříte a použijete virtuální počítač. Tento článek také ukazuje, jak:

* Připojte se k virtuálnímu počítači pomocí vzdáleného klienta.
* Nainstalujte webový server NGINX a zobrazte výchozí domovskou stránku.
* Vyčistit nepoužívané prostředky.

## <a name="prerequisites"></a>Požadavky

* Image Linux na tržišti Azure Stack hub. Tržiště centra Azure Stack ve výchozím nastavení nemá image Linux. Použijte operátor centra Azure Stack, který obsahuje bitovou kopii Ubuntu serveru 16,04 LTS, kterou potřebujete. Operátor může použít pokyny v tématu [stažení položek Marketplace z Azure do centra Azure Stack](../operator/azure-stack-download-azure-marketplace-item.md).

* Aby bylo možné vytvářet a spravovat prostředky, Azure Stack centrum vyžaduje specifickou verzi rozhraní příkazového řádku Azure. 
  * Pokud nemáte PowerShell nakonfigurovaný pro centrum Azure Stack, přečtěte si téma [instalace PowerShellu pro Azure Stack hub](../operator/azure-stack-powershell-install.md). 
  * Po nastavení centra Azure Stackho prostředí PowerShell se připojíte k vašemu prostředí Azure Stack hub. Pokyny najdete v tématu [připojení k Azure Stack hub pomocí prostředí PowerShell jako uživatel](azure-stack-powershell-configure-user.md).

* Klíč veřejného Secure Shell (SSH) s názvem *id_rsa. pub* uložený v adresáři *. ssh* uživatelského profilu Windows. Podrobné informace o vytváření klíčů SSH najdete v tématu [použití veřejného klíče SSH](azure-stack-dev-start-howto-ssh-public-key.md).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků je logický kontejner, ve kterém můžete nasazovat a spravovat prostředky centra Azure Stack. Pokud chcete vytvořit skupinu prostředků, z Azure Stack Development Kit (ASDK) nebo z integrovaného systému centra Azure Stack spusťte následující blok kódu: 

> [!NOTE]
> V následujících příkladech kódu jsme přiřadili hodnoty pro všechny proměnné. Můžete ale přiřadit vlastní hodnoty.

```powershell  
# Create variables to store the location and resource group names.
$location = "local"
$ResourceGroupName = "myResourceGroup"

New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $location
```

## <a name="create-storage-resources"></a>Vytvoření prostředků úložiště

Vytvořte účet úložiště, který se použije k uložení výstupu diagnostiky spouštění.

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

Skupina zabezpečení sítě zabezpečuje virtuální počítač pomocí příchozích a odchozích pravidel. Vytvořte příchozí pravidlo pro port 3389, které povolí příchozí připojení ke vzdálené ploše a příchozí pravidlo pro port 80 pro povolení příchozího webového provozu.

```powershell
# Create variables to store the network security group and rules names.
$nsgName = "myNetworkSecurityGroup"
$nsgRuleSSHName = "myNetworkSecurityGroupRuleSSH"
$nsgRuleWebName = "myNetworkSecurityGroupRuleWeb"


# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name $nsgRuleSSHName -Protocol Tcp `
-Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 22 -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name $nsgRuleWebName -Protocol Tcp `
-Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroupName -Location $location `
-Name $nsgName -SecurityRules $nsgRuleSSH,$nsgRuleWeb
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

Vytvořte konfiguraci virtuálního počítače. Tato konfigurace zahrnuje nastavení, která se použijí při nasazení virtuálního počítače (například přihlašovací údaje uživatele, velikost a bitová kopie virtuálního počítače).

```powershell
# Define a credential object
$UserName='demouser'
$securePassword = ConvertTo-SecureString ' ' -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ($UserName, $securePassword)

# Create the VM configuration object
$VmName = "VirtualMachinelatest"
$VmSize = "Standard_D1"
$VirtualMachine = New-AzureRmVMConfig `
  -VMName $VmName `
  -VMSize $VmSize

$VirtualMachine = Set-AzureRmVMOperatingSystem `
  -VM $VirtualMachine `
  -Linux `
  -ComputerName "MainComputer" `
  -Credential $cred

$VirtualMachine = Set-AzureRmVMSourceImage `
  -VM $VirtualMachine `
  -PublisherName "Canonical" `
  -Offer "UbuntuServer" `
  -Skus "16.04-LTS" `
  -Version "latest"

# Set the operating system disk properties on a VM
$VirtualMachine = Set-AzureRmVMOSDisk `
  -VM $VirtualMachine `
  -CreateOption FromImage | `
  Set-AzureRmVMBootDiagnostics -ResourceGroupName $ResourceGroupName `
  -StorageAccountName $StorageAccountName -Enable |`
  Add-AzureRmVMNetworkInterface -Id $nic.Id

# Configure SSH keys
$sshPublicKey = Get-Content "$env:USERPROFILE\.ssh\id_rsa.pub"

# Add the SSH key to the VM
Add-AzureRmVMSshPublicKey -VM $VirtualMachine `
 -KeyData $sshPublicKey `
 -Path "/home/azureuser/.ssh/authorized_keys"

# Create the VM
New-AzureRmVM `
  -ResourceGroupName $ResourceGroupName `
 -Location $location `
  -VM $VirtualMachine
```

## <a name="vm-quick-create-full-script"></a>Rychlé vytvoření virtuálního počítače: úplný skript

> [!NOTE]
> Tento krok je v podstatě předchozí sloučený kód, ale s heslem namísto klíče SSH pro ověřování.

```powershell
## Create a resource group

<#
A resource group is a logical container where you can deploy and manage Azure Stack Hub resources. From your development kit or the Azure Stack Hub integrated system, run the following code block to create a resource group. Though we've assigned values for all the variables in this article, you can use these values or assign new ones.
#>

# Edit your variables, if required

# Create variables to store the location and resource group names
$location = "local"
$ResourceGroupName = "myResourceGroup"

# Create variables to store the storage account name and the storage account SKU information
$StorageAccountName = "mystorageaccount"
$SkuName = "Standard_LRS"

# Create variables to store the network security group and rules names
$nsgName = "myNetworkSecurityGroup"
$nsgRuleSSHName = "myNetworkSecurityGroupRuleSSH"
$nsgRuleWebName = "myNetworkSecurityGroupRuleWeb"

# Create variable for VM password
$VMPassword = 'Password123!'

# End of variables - no need to edit anything past that point to deploy a single VM

# Create a resource group
New-AzureRmResourceGroup `
  -Name $ResourceGroupName `
  -Location $location

## Create storage resources

# Create a storage account, and then create a storage container for the Ubuntu Server 16.04 LTS image

# Create a new storage account
$StorageAccount = New-AzureRMStorageAccount `
  -Location $location `
  -ResourceGroupName $ResourceGroupName `
  -Type $SkuName `
  -Name $StorageAccountName

Set-AzureRmCurrentStorageAccount `
  -StorageAccountName $storageAccountName `
  -ResourceGroupName $resourceGroupName

# Create a storage container to store the VM image
$containerName = 'osdisks'
$container = New-AzureStorageContainer `
  -Name $containerName `
  -Permission Blob


## Create networking resources

# Create a virtual network, a subnet, and a public IP address, resources that are used provide network connectivity to the VM

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


### Create a network security group and a network security group rule

<#
The network security group secures the VM by using inbound and outbound rules. Create an inbound rule for port 3389 to allow incoming Remote Desktop connections and an inbound rule for port 80 to allow incoming web traffic.
#>

# Create an inbound network security group rule for port 22
$nsgRuleSSH = New-AzureRmNetworkSecurityRuleConfig -Name $nsgRuleSSHName -Protocol Tcp `
-Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 22 -Access Allow

# Create an inbound network security group rule for port 80
$nsgRuleWeb = New-AzureRmNetworkSecurityRuleConfig -Name $nsgRuleWebName -Protocol Tcp `
-Direction Inbound -Priority 1001 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
-DestinationPortRange 80 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroupName -Location $location `
-Name $nsgName -SecurityRules $nsgRuleSSH,$nsgRuleWeb

### Create a network card for the VM

# The network card connects the VM to a subnet, network security group, and public IP address.

# Create a virtual network card and associate it with public IP address and NSG
$nic = New-AzureRmNetworkInterface `
  -Name myNic `
  -ResourceGroupName $ResourceGroupName `
  -Location $location `
  -SubnetId $vnet.Subnets[0].Id `
  -PublicIpAddressId $pip.Id `
  -NetworkSecurityGroupId $nsg.Id

## Create a VM
<#
Create a VM configuration. This configuration includes the settings used when deploying the VM. For example: user credentials, size, and the VM image.
#>

# Define a credential object
$UserName='demouser'
$securePassword = ConvertTo-SecureString $VMPassword -AsPlainText -Force
$cred = New-Object System.Management.Automation.PSCredential ($UserName, $securePassword)

# Create the VM configuration object
$VmName = "VirtualMachinelatest"
$VmSize = "Standard_D1"
$VirtualMachine = New-AzureRmVMConfig `
  -VMName $VmName `
  -VMSize $VmSize

$VirtualMachine = Set-AzureRmVMOperatingSystem `
  -VM $VirtualMachine `
  -Linux `
  -ComputerName "MainComputer" `
  -Credential $cred

$VirtualMachine = Set-AzureRmVMSourceImage `
  -VM $VirtualMachine `
  -PublisherName "Canonical" `
  -Offer "UbuntuServer" `
  -Skus "16.04-LTS" `
  -Version "latest"

$osDiskName = "OsDisk"
$osDiskUri = '{0}vhds/{1}-{2}.vhd' -f `
  $StorageAccount.PrimaryEndpoints.Blob.ToString(),`
  $vmName.ToLower(), `
  $osDiskName

# Set the operating system disk properties on a VM
$VirtualMachine = Set-AzureRmVMOSDisk `
  -VM $VirtualMachine `
  -Name $osDiskName `
  -VhdUri $OsDiskUri `
  -CreateOption FromImage | `
  Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create the VM
New-AzureRmVM `
  -ResourceGroupName $ResourceGroupName `
 -Location $location `
  -VM $VirtualMachine
```

## <a name="connect-to-the-vm"></a>Připojení k virtuálnímu počítači

Po nasazení virtuálního počítače Nakonfigurujte pro něj připojení SSH. K získání veřejné IP adresy virtuálního počítače použijte příkaz [Get-AzureRmPublicIpAddress](/powershell/module/azurerm.network/get-azurermpublicipaddress) :

```powershell
Get-AzureRmPublicIpAddress -ResourceGroupName myResourceGroup | Select IpAddress
```

Z klientského systému s nainstalovaným SSH použijte k připojení k virtuálnímu počítači následující příkaz. Pokud pracujete v systému Windows, můžete vytvořit připojení pomocí [výstupu](https://www.putty.org/) .

```
ssh <Public IP Address>
```

Až budete vyzváni, přihlaste se jako **azureuser**. Pokud jste použili přístupové heslo při vytváření klíčů SSH, budete muset zadat heslo.

## <a name="install-the-nginx-web-server"></a>Instalace webového serveru NGINX

Pokud chcete aktualizovat prostředky balíčku a nainstalovat nejnovější balíček NGINX, spusťte následující skript:

```bash
#!/bin/bash

# update package source
apt-get -y update

# install NGINX
apt-get -y install nginx
```

## <a name="view-the-nginx-welcome-page"></a>Zobrazení úvodní stránky serveru NGINX

S nainstalovaným webovým serverem NGINX a na vašem VIRTUÁLNÍm počítači se otevře port 80. k webovému serveru se můžete dostat pomocí veřejné IP adresy virtuálního počítače. Otevřete webový prohlížeč a přejdete na ```http://<public IP address>```.

![Úvodní stránka webového serveru NGINX](./media/azure-stack-quick-create-vm-linux-cli/nginx.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Prostředky, které už nepotřebujete, můžete vyčistit pomocí příkazu [Remove-AzureRmResourceGroup](/powershell/module/azurerm.resources/remove-azurermresourcegroup) . Pokud chcete odstranit skupinu prostředků a všechny její prostředky, spusťte následující příkaz:

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup
```

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili základní virtuální počítač s Linux serverem. Další informace o Azure Stackch virtuálních počítačů centra najdete [v centru Azure Stack na webu s důležitými informacemi o virtuálních počítačích](azure-stack-vm-considerations.md).
