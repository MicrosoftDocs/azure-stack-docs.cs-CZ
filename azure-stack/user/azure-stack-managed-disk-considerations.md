---
title: Rozdíly a požadavky na spravované disky centra Azure Stack
description: Přečtěte si o rozdílech a ohledech při práci se spravovanými disky a spravovanými imagemi v centru Azure Stack.
author: sethmanheim
ms.topic: article
ms.date: 08/27/2020
ms.author: sethm
ms.reviewer: jiahan
ms.lastreviewed: 03/23/2019
ms.openlocfilehash: 3f5a53ce1bfb219db05e92d361f8d4018f755dad
ms.sourcegitcommit: 990e9cbfc3ce2edd2bd3dccc10db465bf8ac518f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94567237"
---
# <a name="azure-stack-hub-managed-disks-differences-and-considerations"></a>Azure Stack spravované disky centra: rozdíly a požadavky

Tento článek shrnuje rozdíly mezi [ *spravovanými disky* v centru Azure Stack](azure-stack-manage-vm-disks.md) a [spravovaných discích v Azure](/azure/virtual-machines/windows/managed-disks-overview). Další informace o hlavních rozdílech mezi Azure Stack hub a Azure najdete v článku [klíčové důležité informace](azure-stack-considerations.md) .

Spravované disky zjednodušují správu disků pro virtuální počítače s IaaS pomocí správy [účtů úložiště](../operator/azure-stack-manage-storage-accounts.md) přidružených k DISKŮM virtuálních počítačů.

Spravované disky jsou ve výchozím nastavení povolené při vytváření virtuálních počítačů pomocí portálu Azure Stack hub.
  
## <a name="cheat-sheet-managed-disk-differences"></a>List tahák: rozdíly spravovaného disku

| Příznak | Azure (Global) | Azure Stack Hub |
| --- | --- | --- |
|Šifrování pro neaktivní uložená data |Šifrování služby Azure Storage (SSE), Azure Disk Encryption (ADE).     |BitLocker 128-bitové šifrování AES      |
|Image          | Spravovaná vlastní image |Podporováno|
|Možnosti zálohování | Služba Azure Backup |Zatím nepodporováno |
|Možnosti zotavení po havárii | Azure Site Recovery |Zatím nepodporováno|
|Typy disků     |SSD úrovně Premium, SSD úrovně Standard a HDD úrovně Standard. |SSD úrovně Premium HDD úrovně Standard |
|Prémiové disky  |Plně podporováno. |Dá se zřídit, ale neomezuje ani nezaručuje výkon.  |
|Prémiové disky IOPs  |Závisí na velikosti disku.  |2300 IOPs na disk |
|Propustnost prémiových disků |Závisí na velikosti disku. |145 MB za sekundu na disk |
|Velikost disku  |Disk Azure Premium: P4 (32 GiB) do P80 (32 TiB)<br>Disk Azure SSD úrovně Standard: E10 (128 GiB) do E80 (32 TiB)<br>Disk Azure HDD úrovně Standard: S4 (32 GiB) do S80 (32 TiB) |M4:32 GiB<br>M6:64 GiB<br>M10:128 GiB<br>M15:256 GiB<br>M20:512 GiB<br>M30:1023 GiB |
|Kopie snímku disků|Snímky Azure Managed disks připojené k běžícímu virtuálnímu počítači se podporují.|Zatím nepodporováno |
|Analytické nástroje pro výkon disků |Jsou podporovány agregované metriky a metriky na disku. |Zatím nepodporováno |
|Migrace      |Poskytněte Nástroj pro migraci z existujících nespravovaných virtuálních počítačů Azure Resource Manager bez nutnosti znovu vytvořit virtuální počítač.  |Zatím nepodporováno |

> [!NOTE]  
> Služba Managed disks IOPs a propustnost v Azure Stack hub je místo zřízeného čísla, které může být ovlivněno hardwarem a úlohami běžícími v centru Azure Stack.

## <a name="metrics"></a>Metriky

Existují také rozdíly v metrikách úložiště:

- U Azure Stackového centra nerozlišuje data transakcí v metrikách úložiště rozdíl mezi vnitřní nebo externí šířkou pásma sítě.
- Data transakce centra Azure Stack v metrikě úložiště neobsahují přístup k virtuálnímu počítači pro připojené disky.

## <a name="api-versions"></a>Verze rozhraní API

Spravované disky centra Azure Stack podporují následující verze rozhraní API:

::: moniker range=">=azs-2008"
- 2019-03-01
- 2018-09-30
- 2018-06-01
- 2018-04-01
- 2017-03-30
::: moniker-end

::: moniker range="<=azs-2005"

- 2017-03-30
- 2017-12-01 (pouze spravované image, žádné disky, žádné snímky)
::: moniker-end

## <a name="convert-to-managed-disks"></a>Převést na spravované disky

> [!NOTE]  
> Rutinu Azure PowerShell **ConvertTo-AzVMManagedDisk** nelze použít k převedení nespravovaného disku na spravovaný disk v centru Azure Stack. Centrum Azure Stack v tuto chvíli tuto rutinu nepodporuje.

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

# The Azure Stack Hub location where the managed disk will be located.
# The location should be the same as the location of the storage account in which VHD file is stored.
# Configure the new managed VM point to the old unmanaged VM configuration (network config, VM name, location).
$Location = "local"
$VirtualMachineName = "unmngdvm"
$VirtualMachineSize = "Standard_D1"
$PIpName = "unmngdvm-ip"
$VirtualNetworkName = "unmngdrg-vnet"
$NicName = "unmngdvm"

# Set the context to the subscription ID in which the managed disk will be created.
Select-AzSubscription -SubscriptionId $SubscriptionId

# Delete old VM, but keep the OS disk.
Remove-AzVm -Name $VirtualMachineName -ResourceGroupName $ResourceGroupName

# Create the managed disk configuration.
$DiskConfig = New-AzDiskConfig -AccountType $AccountType -Location $Location -DiskSizeGB $DiskSize -SourceUri $VhdUri -CreateOption Import

# Create managed disk.
New-AzDisk -DiskName $DiskName -Disk $DiskConfig -ResourceGroupName $resourceGroupName
$Disk = Get-AzDisk -DiskName $DiskName -ResourceGroupName $ResourceGroupName
$VirtualMachine = New-AzVMConfig -VMName $VirtualMachineName -VMSize $VirtualMachineSize

# Use the managed disk resource ID to attach it to the virtual machine.
# Change the OS type to "-Windows" if the OS disk has the Windows OS.
$VirtualMachine = Set-AzVMOSDisk -VM $VirtualMachine -ManagedDiskId $Disk.Id -CreateOption Attach -Linux

# Create a public IP for the VM.
$PublicIp = Get-AzPublicIpAddress -Name $PIpName -ResourceGroupName $ResourceGroupName

# Get the virtual network where the virtual machine will be hosted.
$VNet = Get-AzVirtualNetwork -Name $VirtualNetworkName -ResourceGroupName $ResourceGroupName

# Create NIC in the first subnet of the virtual network.
$Nic = Get-AzNetworkInterface -Name $NicName -ResourceGroupName $ResourceGroupName

$VirtualMachine = Add-AzVMNetworkInterface -VM $VirtualMachine -Id $Nic.Id

# Create the virtual machine with managed disk.
New-AzVM -VM $VirtualMachine -ResourceGroupName $ResourceGroupName -Location $Location
```

## <a name="managed-images"></a>Spravované image

Centrum Azure Stack podporuje *spravované image* , které vám umožní vytvořit objekt spravované image na ZOBECNĚNém virtuálním počítači (nespravované a spravované), který umožňuje vytvářet virtuální počítače se spravovanými disky jenom dál. Spravované image umožňují následující dva scénáře:

- Máte zobecněné nespravované virtuální počítače a chcete používat spravované disky dál.
- Máte zobecněný spravovaný virtuální počítač a chcete vytvořit několik podobných spravovaných virtuálních počítačů.

### <a name="step-1-generalize-the-vm"></a>Krok 1: generalizace virtuálního počítače

V případě systému Windows postupujte podle části [generalizace virtuálního počítače s Windows pomocí nástroje Sysprep](/azure/virtual-machines/windows/capture-image-resource#generalize-the-windows-vm-using-sysprep) . V případě systému Linux postupujte podle [pokynů v kroku 1.](/azure/virtual-machines/linux/capture-image#step-1-deprovision-the-vm)

> [!NOTE]
> Nezapomeňte zobecnit virtuální počítač. Vytvoření virtuálního počítače z image, která nebyla správně zobecněna, může způsobit chybu **VMProvisioningTimeout** .

### <a name="step-2-create-the-managed-image"></a>Krok 2: vytvoření spravované image

Pomocí portálu, PowerShellu nebo rozhraní příkazového řádku můžete vytvořit spravovanou bitovou kopii. Postupujte podle kroků v části [Vytvoření spravované image](/azure/virtual-machines/windows/capture-image-resource).

### <a name="step-3-choose-the-use-case"></a>Krok 3: volba případu použití

#### <a name="case-1-migrate-unmanaged-vms-to-managed-disks"></a>Případ 1: migrace nespravovaných virtuálních počítačů na Managed disks

Než provedete tento krok, nezapomeňte virtuální počítač zobecnit správně. Po generalizaci už nemůžete tento virtuální počítač používat. Vytvoření virtuálního počítače z image, která se správně zobecněna, povede k chybě **VMProvisioningTimeout** .

Postupujte podle pokynů v tématu [Vytvoření image z virtuálního počítače, který používá účet úložiště](/azure/virtual-machines/windows/capture-image-resource#create-an-image-from-a-vm-that-uses-a-storage-account) k vytvoření spravované image z zobecněného virtuálního pevného disku v účtu úložiště. Tento obrázek můžete v budoucnu použít k vytvoření spravovaných virtuálních počítačů.

#### <a name="case-2-create-managed-vm-from-managed-image-using-powershell"></a>Případ 2: vytvoření spravovaného virtuálního počítače ze spravované Image pomocí PowerShellu

Po vytvoření image z existujícího virtuálního počítače spravovaného disku pomocí skriptu v části [Vytvoření image ze spravovaného disku pomocí prostředí PowerShell](/azure/virtual-machines/windows/capture-image-resource#create-an-image-from-a-managed-disk-using-powershell)použijte následující vzorový skript k vytvoření podobného virtuálního počítače se systémem Linux z existujícího objektu image.

Azure Stack 1.7.0 centra PowerShellu nebo novější: postupujte podle pokynů v tématu [Vytvoření virtuálního počítače ze spravované image](/azure/virtual-machines/windows/create-vm-generalized-managed).

1.6.0 nebo dřívější modul PowerShellu centra Azure Stack:

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
New-AzResourceGroup -Name $ResourceGroupName -Location $Location

# Create a subnet configuration
$SubnetConfig = New-AzVirtualNetworkSubnetConfig -Name "MySubnet" -AddressPrefix "192.168.1.0/24"

# Create a virtual network
$VNet = New-AzVirtualNetwork -ResourceGroupName $ResourceGroupName -Location $Location `
  -Name "MyVNet" -AddressPrefix "192.168.0.0/16" -Subnet $SubnetConfig

# Create a public IP address and specify a DNS name
$PIp = New-AzPublicIpAddress -ResourceGroupName $ResourceGroupName -Location $Location `
  -Name "mypublicdns$(Get-Random)" -AllocationMethod Static -IdleTimeoutInMinutes 4

# Create an inbound network security group rule for port 3389
$NsgRuleSSH = New-AzNetworkSecurityRuleConfig -Name "MyNetworkSecurityGroupRuleSSH"  -Protocol Tcp `
  -Direction Inbound -Priority 1000 -SourceAddressPrefix * -SourcePortRange * -DestinationAddressPrefix * `
  -DestinationPortRange 22 -Access Allow

# Create a network security group
$Nsg = New-AzNetworkSecurityGroup -ResourceGroupName $ResourceGroupName -Location $Location `
  -Name "MyNetworkSecurityGroup" -SecurityRules $NsgRuleSSH

# Create a virtual network card and associate with public IP address and NSG
$Nic = New-AzNetworkInterface -Name "MyNic" -ResourceGroupName $ResourceGroupName -Location $Location `
  -SubnetId $VNet.Subnets[0].Id -PublicIpAddressId $PIp.Id -NetworkSecurityGroupId $Nsg.Id

$Image = Get-AzImage -ResourceGroupName $ImageRG -ImageName $ImageName

# Create a virtual machine configuration
$VmConfig = New-AzureRmVMConfig -VMName $VirtualMachineName -VMSize "Standard_D1" | `
Set-AzureRmVMOperatingSystem -Linux -ComputerName $VirtualMachineName -Credential $Cred | `
Set-AzureRmVMSourceImage -Id $Image.Id | `
Set-AzureRmVMOSDisk -VM $VmConfig -CreateOption FromImage -Linux | `
Add-AzureRmVMNetworkInterface -Id $Nic.Id

# Create a virtual machine
New-AzVM -ResourceGroupName $ResourceGroupName -Location $Location -VM $VmConfig
```

Portál můžete použít také k vytvoření virtuálního počítače ze spravované image. Další informace najdete v článcích o spravovaných bitových kopiích Azure [Vytvoření spravované image zobecněného virtuálního počítače v Azure](/azure/virtual-machines/windows/capture-image-resource) a [Vytvoření virtuálního počítače ze spravované image](/azure/virtual-machines/windows/create-vm-generalized-managed).

## <a name="configuration"></a>Konfigurace

Po použití aktualizace 1808 nebo novější je nutné před použitím spravovaných disků provést následující změnu konfigurace:

- Pokud bylo předplatné vytvořeno před aktualizací 1808, postupujte podle následujících kroků a aktualizujte předplatné. V opačném případě může nasazení virtuálních počítačů v tomto předplatném selhat s chybovou zprávou "vnitřní chyba ve Správci disků".
   1. V uživatelském portálu centra Azure Stack klikněte na **předplatná** a vyhledejte předplatné. Klikněte na **poskytovatelé prostředků** , potom klikněte na **Microsoft. COMPUTE** a pak klikněte na **znovu registrovat**.
   2. V rámci stejného předplatného, přejít na **Access Control (IAM)** a ověřte, že je v seznamu uveden **Azure Stack disk spravovaný** centrem.
- Pokud používáte víceklientské prostředí, požádejte svého operátora cloudu (který může být ve vaší organizaci nebo od poskytovatele služeb) a překonfigurujte každý adresář hosta podle kroků v [tomto článku](../operator/azure-stack-enable-multitenancy.md#register-azure-stack-hub-with-the-guest-directory). V opačném případě může dojít k selhání nasazení virtuálních počítačů v rámci předplatného přidružených k tomuto adresáři hosta s chybovou zprávou "vnitřní chyba ve Správci disků".

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [virtuálních počítačích s Azure Stack hub](azure-stack-compute-overview.md).
