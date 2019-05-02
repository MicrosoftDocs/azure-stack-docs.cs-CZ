---
title: Rozdíly a aspekty pro spravované disky a spravovat obrázky ve službě Azure Stack | Dokumentace Microsoftu
description: Další informace o rozdíly a aspekty při práci se spravovanými disky a spravované Image ve službě Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/23/2019
ms.author: sethm
ms.reviewer: jiahan
ms.lastreviewed: 03/23/2019
ms.openlocfilehash: d32d7d8d484d6e9819440fc9261bae52d6395ae2
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985898"
---
# <a name="azure-stack-managed-disks-differences-and-considerations"></a>Azure Stack spravované disky: rozdíly a aspekty

Tento článek shrnuje známé rozdíly mezi [spravované disky Azure Stack](azure-stack-manage-vm-disks.md) a [managed disks pro Azure](/azure/virtual-machines/windows/managed-disks-overview). Další informace o základní rozdíly mezi Azure Stack a Azure, najdete v článku [klíče aspekty](azure-stack-considerations.md) článku.

Spravované disky zjednodušují správu disků pro virtuální počítače IaaS pomocí správy [účty úložiště](../operator/azure-stack-manage-storage-accounts.md) přidružené k diskům virtuálních počítačů.

> [!Note]  
> Spravované disky ve službě Azure Stack je k dispozici aktualizace. 1808. Je povolené ve výchozím nastavení při vytváření virtuálních počítačů pomocí portálu Azure Stack, počínaje 1811 aktualizace.
  
## <a name="cheat-sheet-managed-disk-differences"></a>Tahák: spravovaného disku rozdíly

| Funkce | Azure (globální) | Azure Stack |
| --- | --- | --- |
|Šifrování pro neaktivní uložená Data |Šifrování služby Azure Storage (SSE), Azure Disk Encryption (ADE)     |Šifrování AES 128-bit nástroje BitLocker      |
|Image          | Podpora spravovanou vlastní image |Podporováno|
|Možnosti zálohování |Podpora služby Azure Backup |Není dosud podporován. |
|Možnosti zotavení po havárii |Podpora Azure Site Recovery |Není dosud podporován.|
|Typy disků     |Premium SSD, SSD na úrovni Standard a standardní HDD |Premium SSD, Standard HDD |
|Disky Premium  |Plně podporované. |Je možné zřídit, ale bez omezení výkonu nebo záruk  |
|Prémiové disky vstupně-výstupních operací  |Závisí na velikosti disku  |2300 IOPs na disk |
|Propustnost disků úrovně Premium |Závisí na velikosti disku |145 MB za sekundu na disk |
|Velikost disku  |Disk Azure typu Premium: P4 (32 GB) na P80 (32 TB)<br>Disk Azure SSD na úrovni Standard: E10 (128 GB) na E80 (32 TB)<br>Pevný disk Azure standardní Disk: S4 (32 GB) na S80 (32 TB) |M4: 32 GiB<br>M6: 64 GiB<br>M10: 128 GiB<br>M15: 256 GiB<br>M20: 512 GiB<br>M30: 1024 GiB |
|Kopie snímků disků|Pořízení snímku Azure připojené do spuštěného virtuálního počítače nepodporuje spravované disky|Není dosud podporován. |
|Výkon disků analýzy |Agregace metrik a metriky pro disk podporované |Není dosud podporován. |
|Migrace      |Poskytuje nástroj pro migraci z existující nespravovaná správce prostředků virtuálních počítačů Azure bez nutnosti znovu vytvořte virtuální počítač  |Není dosud podporován. |

> [!NOTE]  
> Spravované disky vstupně-výstupních operací a propustnosti ve službě Azure Stack je číslo zakončení místo zřízené číslo, které může být ovlivněno hardwaru a pracovní postupy spouštěné ve službě Azure Stack.

## <a name="metrics"></a>Metriky

Existují také rozdíly pomocí metrik storage:

- Transakce data v metrikách storage pomocí služby Azure Stack nerozlišuje šířky pásma sítě interní nebo externí.
- Azure Stack data transakcí v metrikách storage neobsahuje virtuální počítač přístup k připojené disky.

## <a name="api-versions"></a>Verze rozhraní API

Azure Stack spravované disky podporuje následující rozhraní API verze:

- 2017-03-30
- 2017-12-01

## <a name="convert-to-managed-disks"></a>Převod na managed disks

Následující skript můžete použít k převodu aktuálně zřízené virtuální počítač z nespravované na spravované disky. Zástupné názvy nahraďte vlastními hodnotami:

```powershell
$subscriptionId = 'subid'

# The name of your resource group where your VM to be converted exists
$resourceGroupName ='rgmgd'

# The name of the managed disk
$diskName = 'unmgdvm'

# The size of the disks in GB. It should be greater than the VHD file size.
$diskSize = '50'

# The URI of the VHD file that will be used to create the managed disk.
# The VHD file can be deleted as soon as the managed disk is created.
$vhdUri = 'https://rgmgddisks347.blob.local.azurestack.external/vhds/unmgdvm20181109013817.vhd' 

# The storage type for the managed disk; PremiumLRS or StandardLRS.
$accountType = 'StandardLRS'

# The Azure Stack location where the managed disk will be located.
# The location should be the same as the location of the storage account in which VHD file is stored.
# Configure the new managed VM point to the old unmanaged VM's configuration (network config, vm name, location).
$location = 'local'
$virtualMachineName = 'mgdvm'
$virtualMachineSize = 'Standard_D1'
$pipname = 'unmgdvm-ip'
$virtualNetworkName = 'rgmgd-vnet'
$nicname = 'unmgdvm295'

# Set the context to the subscription ID in which the managed disk will be created
Select-AzureRmSubscription -SubscriptionId $SubscriptionId

#Delete old VM, but keep the OS disk
Remove-AzureRmVm -Name $virtualMachineName -ResourceGroupName $resourceGroupName

$diskConfig = New-AzureRmDiskConfig -AccountType $accountType  -Location $location -DiskSizeGB $diskSize -SourceUri $vhdUri -CreateOption Import

# Create managed disk
New-AzureRmDisk -DiskName $diskName -Disk $diskConfig -ResourceGroupName $resourceGroupName
$disk = get-azurermdisk -DiskName $diskName -ResourceGroupName $resourceGroupName
$VirtualMachine = New-AzureRmVMConfig -VMName $virtualMachineName -VMSize $virtualMachineSize

# Use the managed disk resource ID to attach it to the virtual machine.
# Change the OS type to Linux if the OS disk has Linux OS.
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -ManagedDiskId $disk.Id -CreateOption Attach -Linux

# Create a public IP for the VM
$publicIp = Get-AzureRmPublicIpAddress -Name $pipname -ResourceGroupName $resourceGroupName 

# Get the virtual network where the virtual machine will be hosted
$vnet = Get-AzureRmVirtualNetwork -Name $virtualNetworkName -ResourceGroupName $resourceGroupName

# Create NIC in the first subnet of the virtual network
$nic = Get-AzureRmNetworkInterface -Name $nicname -ResourceGroupName $resourceGroupName

$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $nic.Id

# Create the virtual machine with managed disk
New-AzureRmVM -VM $VirtualMachine -ResourceGroupName $resourceGroupName -Location $location
```

## <a name="managed-images"></a>Spravovaná Image

Azure Stack podporuje *spravované image*, které umožňují můžete vytvořit objekt spravované image zobecněného virtuálního počítače (nespravované i spravovat), která může vytvářet pouze spravovaný disk virtuálních počítačů do budoucna. Spravované bitové kopie podporují následující dva scénáře:

- Máte zobecněný nespravované virtuální počítače a chcete použít spravované disky do budoucna.
- Máte generalizovaného spravované počítače a chcete vytvořit více, podobně jako spravovaných virtuálních počítačů.

### <a name="step-1-generalize-the-vm"></a>Krok 1: Generalizace virtuálního počítače

Pro Windows, postupujte [generalizace virtuálního počítače Windows pomocí nástroje Sysprep](/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep) oddílu. Pro Linux, postupujte podle kroku 1 [tady](/azure/virtual-machines/linux/capture-image#step-1-deprovision-the-vm).

> [!NOTE]
> Ujistěte se, že generalizace virtuálního počítače. Vytvoření virtuálního počítače z image, který se nezobecnil správně povede k **VMProvisioningTimeout** chyby.

### <a name="step-2-create-the-managed-image"></a>Krok 2: Vytvoření spravované image

Můžete na portálu, Powershellu nebo rozhraní příkazového řádku k vytvoření spravované image. Postupujte podle kroků v článku o Azure [tady](/azure/virtual-machines/windows/capture-image-resource).

### <a name="step-3-choose-the-use-case"></a>Krok 3: Zvolte případ použití

#### <a name="case-1-migrate-unmanaged-vms-to-managed-disks"></a>Případ 1: Migrace nespravované virtuální počítače na managed disks

Ujistěte se, že jste správně zobecnit virtuální počítač před provedením tohoto kroku. Po generalizace kterou již nebudete používat tento virtuální počítač. Vytvoření virtuálního počítače z image, který se nezobecnil správně povede k **VMProvisioningTimeout** chyby.

Postupujte podle pokynů [tady](/azure/virtual-machines/windows/capture-image-resource#create-an-image-from-a-vhd-in-a-storage-account) pro vytvoření spravované image z generalizovaného VHD v účtu úložiště. Můžete použít tuto bitovou kopii do budoucna pro vytvoření spravované virtuální počítače.

#### <a name="case-2-create-managed-vm-from-managed-image-using-powershell"></a>Případ 2: Vytvoření spravovaného virtuálního počítače ze spravované image pomocí Powershellu

Po vytvoření image z existujícího spravovaného disku virtuálního počítače pomocí skriptu [tady](/azure/virtual-machines/windows/capture-image-resource#create-an-image-from-a-managed-disk-using-powershell), následující ukázkový skript vytvoří podobně jako virtuální počítač s Linuxem z existujícího objektu image:

Modul Azure Stack Powershellu 1.7.0 nebo novější: postupujte podle pokynů [tady](/azure/virtual-machines/windows/create-vm-generalized-managed).

Modul Azure Stack Powershellu 1.6.0 nebo dříve:

```powershell
# Variables for common values
$resourceGroup = "myResourceGroup"
$location = "redmond"
$vmName = "myVM"
$imagerg = "managedlinuxrg"
$imagename = "simplelinuxvmm-image-2019122"

# Create user object
$cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzureRmResourceGroup -Name $resourceGroup -Location $location

# Create a subnet configuration
$subnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name mySubnet -AddressPrefix 192.168.1.0/24

# Create a virtual network
$vnet = New-AzureRmVirtualNetwork -ResourceGroupName $resourceGroup -Location $location `
  -Name MYvNET -AddressPrefix 192.168.0.0/16 -Subnet $subnetConfig

# Create a public IP address and specify a DNS name
$pip = New-AzureRmPublicIpAddress -ResourceGroupName $resourceGroup -Location $location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create an inbound network security group rule for port 3389
$nsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name myNetworkSecurityGroupRuleRDP  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow

# Create a network security group
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $resourceGroup -Location $location `
  -Name myNetworkSecurityGroup -SecurityRules $nsgRuleRDP

# Create a virtual network card and associate with public IP address and NSG
$nic = New-AzureRmNetworkInterface -Name myNic -ResourceGroupName $resourceGroup -Location $location `
  -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -NetworkSecurityGroupId $nsg.Id

$image = get-azurermimage -ResourceGroupName $imagerg -ImageName $imagename

# Create a virtual machine configuration
$vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize Standard_D1 | `
Set-AzureRmVMOperatingSystem -Linux -ComputerName $vmName -Credential $cred | `
Set-AzureRmVMSourceImage -Id $image.Id | `
Add-AzureRmVMNetworkInterface -Id $nic.Id

# Create a virtual machine
New-AzureRmVM -ResourceGroupName $resourceGroup -Location $location -VM $vmConfig
```

Na portálu můžete také použít k vytvoření virtuálního počítače ze spravované image. Další informace najdete v tématu Azure spravované image články [vytvoření spravované image zobecněného virtuálního počítače v Azure](/azure/virtual-machines/windows/capture-image-resource) a [vytvoření virtuálního počítače ze spravované image](/azure/virtual-machines/windows/create-vm-generalized-managed).

## <a name="configuration"></a>Konfigurace

Po použití. 1808 update nebo novější, je třeba provést následující konfiguraci před použitím spravovaných disků:

- Pokud odběr byl vytvořen ještě před aktualizací. 1808, postupujte podle následujících kroků provedete aktualizaci odběru. V opačném případě nasazování virtuálních počítačů v tomto předplatném může selhat s chybovou zprávou "Vnitřní chyba ve Správci disků."
   1. Portál pro klienty, přejděte na **předplatná** a vyhledejte předplatné. Klikněte na tlačítko **poskytovatelů prostředků**, klikněte na **Microsoft.Compute**a potom klikněte na tlačítko **přeregistrovat**.
   2. V rámci stejného předplatného, přejděte na **řízení přístupu (IAM)** a ověřte, že **Azure Stack – spravovaný Disk** je uvedena.
- Pokud používáte prostředí s více tenanty, požádejte vašeho cloudu – operátor (který může být ve vaší vlastní organizaci, nebo od poskytovatele služeb) k překonfigurovat všech vašich adresářů hosta, proveďte kroky v [v tomto článku](../operator/azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory). V opačném případě nasazování virtuálních počítačů v rámci služby předplacené přidružené k tomuto adresáři hosta může selhat s chybovou zprávou "Vnitřní chyba ve Správci disků."

## <a name="next-steps"></a>Další postup

- [Další informace o virtuálních počítačích Azure Stack](azure-stack-compute-overview.md)
