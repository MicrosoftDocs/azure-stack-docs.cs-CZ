---
title: Azure Stack spravované disky; rozdíly a požadavky | Microsoft Docs
description: Přečtěte si o rozdílech a ohledech při práci se spravovanými disky a spravovanými imagemi v Azure Stack.
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
ms.date: 10/04/2019
ms.author: sethm
ms.reviewer: jiahan
ms.lastreviewed: 03/23/2019
ms.openlocfilehash: 97684f2a0ef9960854b192ca15f972bc15ff5b62
ms.sourcegitcommit: f91979c1613ea1aa0e223c818fc208d902b81299
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2019
ms.locfileid: "71974051"
---
# <a name="azure-stack-managed-disks-differences-and-considerations"></a>Azure Stack spravované disky: rozdíly a požadavky

Tento článek shrnuje rozdíly mezi [spravovanými disky v Azure Stack](azure-stack-manage-vm-disks.md) a [spravovanými disky v Azure](/azure/virtual-machines/windows/managed-disks-overview). Další informace o rozdílech na vysoké úrovni mezi Azure Stack a Azure najdete v článku [klíčové důležité informace](azure-stack-considerations.md) .

Spravované disky zjednodušují správu disků pro virtuální počítače s IaaS pomocí správy [účtů úložiště](../operator/azure-stack-manage-storage-accounts.md) přidružených k DISKŮM virtuálních počítačů.

> [!NOTE]  
> Spravované disky v Azure Stack jsou k dispozici od aktualizace 1808. Počínaje aktualizací 1811 je funkce při vytváření virtuálních počítačů pomocí portálu Azure Stack standardně povolená.
  
## <a name="cheat-sheet-managed-disk-differences"></a>List tahák: rozdíly spravovaného disku

| Funkce | Azure (Global) | Azure Stack |
| --- | --- | --- |
|Šifrování pro neaktivní uložená data |Šifrování služby Azure Storage (SSE), Azure Disk Encryption (ADE).     |BitLocker 128-bitové šifrování AES      |
|Image          | Spravovaná vlastní image |Podporováno|
|Možnosti zálohování | Služba Azure Backup |Zatím nepodporováno |
|Možnosti zotavení po havárii | Azure Site Recovery |Zatím nepodporováno|
|Typy disků     |SSD úrovně Premium, SSD úrovně Standard a HDD úrovně Standard. |SSD úrovně Premium HDD úrovně Standard |
|Prémiové disky  |Plně podporováno. |Dá se zřídit, ale neomezuje ani nezaručuje výkon.  |
|Prémiové disky IOPs  |Závisí na velikosti disku.  |2300 IOPs na disk |
|Propustnost prémiových disků |Závisí na velikosti disku. |145 MB za sekundu na disk |
|Velikost disku  |Disk Azure Premium: P4 (32 GiB) do P80 (32 TiB)<br>Disk Azure SSD úrovně Standard: E10 (128 GiB) do E80 (32 TiB)<br>Disk Azure HDD úrovně Standard: S4 (32 GiB) až S80 (32 TiB) |M4: 32 GiB<br>M6: 64 GiB<br>M10: 128 GiB<br>M15: 256 GiB<br>M20: 512 GiB<br>M30: 1023 GiB |
|Kopie snímku disků|Snímky Azure – spravované disky připojené k běžícímu virtuálnímu počítači.|Zatím nepodporováno |
|Analytické nástroje pro výkon disků |Jsou podporovány agregované metriky a metriky na disku. |Zatím nepodporováno |
|Migrace      |Poskytněte Nástroj pro migraci z existujících nespravovaných virtuálních počítačů Azure Resource Manager bez nutnosti znovu vytvořit virtuální počítač.  |Zatím nepodporováno |

> [!NOTE]  
> Služba Managed disks IOPs a propustnost v Azure Stack je místo zřízeného čísla, které může být ovlivněno hardwarem a úlohami spuštěnými v Azure Stack.

## <a name="metrics"></a>Metriky

Existují také rozdíly v metrikách úložiště:

- V případě Azure Stack data transakcí v metrikách úložiště nerozlišují interní nebo externí šířku pásma sítě.
- Data transakcí Azure Stack v metrikách úložiště nezahrnují přístup virtuálních počítačů k připojeným diskům.

## <a name="api-versions"></a>Verze rozhraní API

Azure Stack Managed disks podporuje následující verze rozhraní API:

- 2017-03-30
- 2017-12-01

## <a name="convert-to-managed-disks"></a>Převést na spravované disky

> [!NOTE]  
> Rutinu Azure PowerShell **ConvertTo-AzureRmVMManagedDisk** nelze použít k převedení nespravovaného disku na spravovaný disk v Azure Stack. Azure Stack aktuálně nepodporuje tuto rutinu.

Pomocí následujícího skriptu můžete převést aktuálně zřízený virtuální počítač z nespravovaného na spravované disky. Zástupné symboly nahraďte vlastními hodnotami:

```powershell
$SubscriptionId = "SubId"

# The name of your resource group where your VM to be converted exists.
$ResourceGroupName ="MyResourceGroup"

# The name of the managed disk to be created.
$DiskName = "mngddisk"

# The size of the disks in GB. It should be greater than the VHD file size.
$DiskSize = "50"

# The URI of the VHD file that will be used to create the managed disk.
# The VHD file can be deleted as soon as the managed disk is created.
$VhdUri = "https://rgmgddisks347.blob.local.azurestack.external/vhds/unmngdvm20181109013817.vhd"

# The storage type for the managed disk: PremiumLRS or StandardLRS.
$AccountType = "StandardLRS"

# The Azure Stack location where the managed disk will be located.
# The location should be the same as the location of the storage account in which VHD file is stored.
# Configure the new managed VM point to the old unmanaged VM configuration (network config, VM name, location).
$Location = "local"
$VirtualMachineName = "unmngdvm"
$VirtualMachineSize = "Standard_D1"
$PIpName = "unmngdvm-ip"
$VirtualNetworkName = "unmngdrg-vnet"
$NicName = "unmngdvm"

# Set the context to the subscription ID in which the managed disk will be created.
Select-AzureRmSubscription -SubscriptionId $SubscriptionId

# Delete old VM, but keep the OS disk.
Remove-AzureRmVm -Name $VirtualMachineName -ResourceGroupName $ResourceGroupName

# Create the managed disk configuration.
$DiskConfig = New-AzureRmDiskConfig -AccountType $AccountType -Location $Location -DiskSizeGB $DiskSize -SourceUri $VhdUri -CreateOption Import

# Create managed disk.
New-AzureRmDisk -DiskName $DiskName -Disk $DiskConfig -ResourceGroupName $resourceGroupName
$Disk = Get-AzureRmDisk -DiskName $DiskName -ResourceGroupName $ResourceGroupName
$VirtualMachine = New-AzureRmVMConfig -VMName $VirtualMachineName -VMSize $VirtualMachineSize

# Use the managed disk resource ID to attach it to the virtual machine.
# Change the OS type to "-Windows" if the OS disk has the Windows OS.
$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -ManagedDiskId $Disk.Id -CreateOption Attach -Linux

# Create a public IP for the VM.
$PublicIp = Get-AzureRmPublicIpAddress -Name $PIpName -ResourceGroupName $ResourceGroupName

# Get the virtual network where the virtual machine will be hosted.
$VNet = Get-AzureRmVirtualNetwork -Name $VirtualNetworkName -ResourceGroupName $ResourceGroupName

# Create NIC in the first subnet of the virtual network.
$Nic = Get-AzureRmNetworkInterface -Name $NicName -ResourceGroupName $ResourceGroupName

$VirtualMachine = Add-AzureRmVMNetworkInterface -VM $VirtualMachine -Id $Nic.Id

# Create the virtual machine with managed disk.
New-AzureRmVM -VM $VirtualMachine -ResourceGroupName $ResourceGroupName -Location $Location
```

## <a name="managed-images"></a>Spravované image

Azure Stack podporuje *spravované image*, které vám umožní vytvořit objekt spravované image na ZOBECNĚNém virtuálním počítači (nespravované a spravované), které můžou vytvářet jenom virtuální počítače se spravovanými disky. Spravované image umožňují následující dva scénáře:

- Máte zobecněné nespravované virtuální počítače a chcete používat spravované disky dál.
- Máte zobecněný spravovaný virtuální počítač a chcete vytvořit několik podobných spravovaných virtuálních počítačů.

### <a name="step-1-generalize-the-vm"></a>Krok 1: Generalizace virtuálního počítače

V případě systému Windows postupujte podle části [generalizace virtuálního počítače s Windows pomocí nástroje Sysprep](/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep) . V případě systému Linux postupujte podle [pokynů v kroku 1.](/azure/virtual-machines/linux/capture-image#step-1-deprovision-the-vm)

> [!NOTE]
> Nezapomeňte zobecnit virtuální počítač. Vytvoření virtuálního počítače z image, která se správně zobecněna, povede k chybě **VMProvisioningTimeout** .

### <a name="step-2-create-the-managed-image"></a>Krok 2: Vytvoření spravované image

Pomocí portálu, PowerShellu nebo rozhraní příkazového řádku můžete vytvořit spravovanou bitovou kopii. Postupujte podle kroků v části [Vytvoření spravované image](/azure/virtual-machines/windows/capture-image-resource).

### <a name="step-3-choose-the-use-case"></a>Krok 3: Zvolit případ použití

#### <a name="case-1-migrate-unmanaged-vms-to-managed-disks"></a>1\. případ: Migrace nespravovaných virtuálních počítačů na Managed disks

Než provedete tento krok, nezapomeňte virtuální počítač zobecnit správně. Po generalizaci už nemůžete tento virtuální počítač používat. Vytvoření virtuálního počítače z image, která se správně zobecněna, povede k chybě **VMProvisioningTimeout** .

Postupujte podle pokynů v tématu [Vytvoření image z virtuálního počítače, který používá účet úložiště](/azure/virtual-machines/windows/capture-image-resource#create-an-image-from-a-vm-that-uses-a-storage-account) k vytvoření spravované image z zobecněného virtuálního pevného disku v účtu úložiště. Tento obrázek můžete v budoucnu použít k vytvoření spravovaných virtuálních počítačů.

#### <a name="case-2-create-managed-vm-from-managed-image-using-powershell"></a>2\. případ: Vytvoření spravovaného virtuálního počítače ze spravované Image pomocí PowerShellu

Po vytvoření image z existujícího virtuálního počítače spravovaného disku pomocí skriptu v části [Vytvoření image ze spravovaného disku pomocí prostředí PowerShell](/azure/virtual-machines/windows/capture-image-resource#create-an-image-from-a-managed-disk-using-powershell)použijte následující vzorový skript k vytvoření podobného virtuálního počítače se systémem Linux z existujícího objektu image.

Azure Stack modulu PowerShellu 1.7.0 nebo novější: Postupujte podle pokynů v tématu [Vytvoření virtuálního počítače ze spravované image](/azure/virtual-machines/windows/create-vm-generalized-managed).

Azure Stack modulu PowerShellu 1.6.0 nebo novější:

```powershell
# Variables for common values
$ResourceGroupName = "MyResourceGroup"
$Location = "local"
$VirtualMachineName = "MyVM"
$ImageRG = "managedlinuxrg"
$ImageName = "simplelinuxvmm-image-2019122"

# Create credential object
$Cred = Get-Credential -Message "Enter a username and password for the virtual machine."

# Create a resource group
New-AzureRmResourceGroup -Name $ResourceGroupName -Location $Location

# Create a subnet configuration
$SubnetConfig = New-AzureRmVirtualNetworkSubnetConfig -Name "MySubnet" -AddressPrefix "192.168.1.0/24"

# Create a virtual network
$VNet = New-AzureRmVirtualNetwork -ResourceGroupName $ResourceGroupName -Location $Location `
  -Name "MyVNet" -AddressPrefix "192.168.0.0/16" -Subnet $SubnetConfig

# Create a public IP address and specify a DNS name
$PIp = New-AzureRmPublicIpAddress -ResourceGroupName $ResourceGroupName -Location $Location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create an inbound network security group rule for port 3389
$NsgRuleRDP = New-AzureRmNetworkSecurityRuleConfig -Name "MyNetworkSecurityGroupRuleRDP"  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 3389 -Access Allow

# Create a network security group
$Nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $ResourceGroupName -Location $Location `
  -Name "MyNetworkSecurityGroup" -SecurityRules $NsgRuleRDP

# Create a virtual network card and associate with public IP address and NSG
$Nic = New-AzureRmNetworkInterface -Name "MyNic" -ResourceGroupName $ResourceGroupName -Location $Location `
  -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PIp.Id -NetworkSecurityGroupId $Nsg.Id

$Image = Get-AzureRmImage -ResourceGroupName $ImageRG -ImageName $ImageName

# Create a virtual machine configuration
$VmConfig = New-AzureRmVMConfig -VMName $VirtualMachineName -VMSize "Standard_D1" | `
Set-AzureRmVMOperatingSystem -Linux -ComputerName $VirtualMachineName -Credential $Cred | `
Set-AzureRmVMSourceImage -Id $Image.Id | `
Add-AzureRmVMNetworkInterface -Id $Nic.Id

# Create a virtual machine
New-AzureRmVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VmConfig
```

Portál můžete použít také k vytvoření virtuálního počítače ze spravované image. Další informace najdete v článcích o imagí spravovaných v Azure [Vytvoření spravované image zobecněného virtuálního počítače v Azure](/azure/virtual-machines/windows/capture-image-resource) a [Vytvoření virtuálního počítače ze spravované image](/azure/virtual-machines/windows/create-vm-generalized-managed).

## <a name="configuration"></a>Konfiguraci

Po použití aktualizace 1808 nebo novější je nutné před použitím spravovaných disků provést následující změnu konfigurace:

- Pokud bylo předplatné vytvořeno před aktualizací 1808, postupujte podle následujících kroků a aktualizujte předplatné. V opačném případě může nasazení virtuálních počítačů v tomto předplatném selhat s chybovou zprávou "vnitřní chyba ve Správci disků".
   1. Na portálu Azure Stack User Portal klikněte na **předplatná** a vyhledejte předplatné. Klikněte na tlačítko **poskytovatelů prostředků**, klikněte na **Microsoft.Compute**a potom klikněte na tlačítko **přeregistrovat**.
   2. V rámci stejného předplatného, přejít na **Access Control (IAM)** a ověřte, že je v seznamu uvedeno **Azure Stack spravovaný disk** .
- Pokud používáte víceklientské prostředí, požádejte svého operátora cloudu (který může být ve vaší organizaci nebo od poskytovatele služeb) a překonfigurujte každý adresář hosta podle kroků v [tomto článku](../operator/azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory). V opačném případě může dojít k selhání nasazení virtuálních počítačů v rámci předplatného přidružených k tomuto adresáři hosta s chybovou zprávou "vnitřní chyba ve Správci disků".

## <a name="next-steps"></a>Další kroky

- [Další informace o Azure Stack virtuálních počítačích](azure-stack-compute-overview.md)
