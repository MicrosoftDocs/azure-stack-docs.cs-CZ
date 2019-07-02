---
title: Vytvoření disku úložiště virtuálního počítače ve službě Azure Stack | Dokumentace Microsoftu
description: Vytvoření disků pro virtuální počítače ve službě Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: sethm
ms.reviewer: jiahan
ms.lastreviewed: 01/18/2019
ms.openlocfilehash: 8b4d48b55cfe21cf7de09119b9069ae4056d3efd
ms.sourcegitcommit: eccbd0098ef652919f357ef6dba62b68abde1090
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67492428"
---
# <a name="create-vm-disk-storage-in-azure-stack"></a>Vytvoření disku úložiště virtuálního počítače ve službě Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Tento článek popisuje postup vytvoření disku úložiště virtuálního počítače (VM) s použitím portálu Azure Stack nebo pomocí prostředí PowerShell.

## <a name="overview"></a>Přehled

Počínaje verzí. 1808, Azure Stack podporuje použití spravované disky a nespravované disky ve virtuálních počítačích, jako operační systém (OS) a datového disku. Dříve než ve verzi. 1808 jsou podporovány pouze nespravované disky.

[Spravované disky](/azure/virtual-machines/windows/managed-disks-overview) zjednodušit správu disků virtuálních počítačů Azure IaaS tím, že spravují účty úložiště přidružené k diskům virtuálních počítačů. Stačí jenom zadat požadovanou velikost disku a Azure Stack ho vytvoří a bude spravovat za vás.

Nespravované disky se vyžaduje, abyste vytvořili účet úložiště pro ukládání disků. Disky, které vytvoříte, se označují jako disky virtuálních počítačů a ukládají v kontejnery v účtu úložiště.

### <a name="best-practice-guidelines"></a>Pravidla osvědčených postupů

Chcete-li zvýšit výkon a snížit celkové náklady na, doporučujeme, abyste že jednotlivých disků virtuálního počítače umístíte do samostatného kontejneru. Kontejner uchovávat disk s operačním systémem nebo datový disk, ale ne obojí současně. Oba typy disku ale můžete také vložit do stejného kontejneru.

Pokud chcete přidat jednu nebo několik datových disků k virtuálnímu počítači, použijte další kontejnery jako umístění pro ukládání těchto disků. Disk s operačním systémem pro další virtuální počítače musí být také ve své vlastní kontejnery.

Když vytváříte virtuální počítače, můžete znovu použít stejný účet úložiště pro každý nový virtuální počítač. Kontejnery, které vytvoříte, musí být jedinečné.

### <a name="adding-new-disks"></a>Přidávání nových disků

Následující tabulka shrnuje, jak přidat disky pomocí portálu a pomocí prostředí PowerShell:

| Metoda | Možnosti
|-|-|
|Portál User portal|-Přidáte nové datové disky existujícího virtuálního počítače. Nové disky, které jsou vytvořeny ve službě Azure Stack. </br> </br>-Přidáte existující soubor disku (VHD) do dříve vytvořeného virtuálního počítače. Uděláte to tak, musíte připravit VHD a pak nahrajte soubor do služby Azure Stack. |
|[PowerShell](#use-powershell-to-add-multiple-unmanaged-disks-to-a-vm) | -Vytvořit nový virtuální počítač s diskem operačního systému a ve stejnou dobu jeden nebo více datových disků k tomuto virtuálnímu počítači. |

## <a name="use-the-portal-to-add-disks-to-a-vm"></a>Přidejte disky do virtuálního počítače pomocí portálu

Ve výchozím nastavení se při použití portálu k vytvoření virtuálního počítače většiny položek na webu marketplace, vytvoří jenom disk s operačním systémem.

Po vytvoření virtuálního počítače, můžete na portálu:

* Vytvořit nový datový disk a připojit k virtuálnímu počítači.
* Nahrát stávající datový disk a připojit ho k virtuálnímu počítači.

Každý nespravovaný disk, který přidáte měly být umístěny v samostatných kontejneru.

>[!NOTE]  
>Disky, vytvářet a spravovat Azure, se nazývají [spravované disky](/azure/virtual-machines/windows/managed-disks-overview).

### <a name="use-the-portal-to-create-and-attach-a-new-data-disk"></a>Použití portálu k vytvoření a připojení nového datového disku

1. Na portálu vyberte **všechny služby**, pak **virtuálních počítačů**.
   ![Příklad: Řídicí panel virtuálního počítače](media/azure-stack-manage-vm-disks/vm-dashboard.png)

2. Vyberte virtuální počítač, který předtím nebyl vytvořen.
   ![Příklad: Vyberte virtuální počítač na řídicím panelu](media/azure-stack-manage-vm-disks/select-a-vm.png)

3. U virtuálního počítače, vyberte **disky**, pak **přidat datový disk**.
   ![Příklad: Připojit nový disk k virtuálnímu počítači](media/azure-stack-manage-vm-disks/Attach-disks.png)

4. Pro datový disk:
   * Zadejte **logickou jednotku**. Logická jednotka musí být platné číslo.
   * Vyberte **vytvořit disk**.
   ![Příklad: Připojit nový disk k virtuálnímu počítači](media/azure-stack-manage-vm-disks/add-a-data-disk-create-disk.png)

5. V **vytvoření spravovaného disku** okno:
   * Zadejte **název** disku.
   * Vyberte existující **skupiny prostředků** nebo vytvořte novou.
   * Vyberte **umístění**. Ve výchozím umístění se nastaví do kontejneru, který obsahuje disk s operačním systémem.
   * Vyberte **typ účtu**.
      ![Příklad: Připojit nový disk k virtuálnímu počítači](media/azure-stack-manage-vm-disks/create-manage-disk.png)

      **Premium SSD**  
      Disky úrovně Premium (SSD) se zálohují na jednotky SSD a nabízejí konzistentní, nízkou latenci výkon. Nabízí nejlepší poměr mezi cenou a výkonem a jsou ideální pro aplikace I vstupně-výstupními operacemi a úloh v produkčním prostředí.

      **Standard HDD**  
      Standardní disky (HDD) se zálohují na magnetické jednotky a jsou vhodnější pro aplikace, kde se data přistupovat moc často. Zónově redundantní disky jsou zajišťované zónově redundantní úložiště (ZRS), který se replikuje vaše data napříč několika zónami, zajistit, že vaše data budou dostupná i v případě, že jedna zóna není mimo provoz.

   * Vyberte **typ zdroje**.

     Vytvoření disku ze snímku jiného disku, objektů blob v účtu úložiště, nebo vytvořte prázdný disk.

      **Snímek**: Vyberte snímek, který, pokud je k dispozici. Snímek musí být v dostupných v předplatném a umístění Virtuálního počítače.

      **Objekt blob úložiště**:
     * Přidejte identifikátor URI objektu blob úložiště, který obsahuje image disku.  
     * Vyberte **Procházet** a otevřete tak okno účty úložiště. Pokyny najdete v tématu [přidat datový disk z účtu úložiště](#add-a-data-disk-from-a-storage-account).
     * Vyberte typ bitové kopie operačního systému: **Windows**, **Linux**, nebo **žádný (datový disk)** .

   * Vyberte **velikost (GiB)** .

     Náklady na standardní disk se zvyšují podle velikosti disku. Premium disku náklady a výkon se zvyšují podle velikosti disku. Další informace najdete v tématu [cenami služby Managed Disks](https://go.microsoft.com/fwlink/?linkid=843142).

   * Vyberte **Vytvořit**. Azure Stack vytvoří a ověří spravovaného disku.

6. Poté, co Azure Stack disk vytvoří a připojí ho k virtuálnímu počítači, nový disk je uvedený v nastavení disku virtuálního počítače v rámci **datové disky**.

   ![Příklad: Zobrazení disku](media/azure-stack-manage-vm-disks/view-data-disk.png)

### <a name="add-a-data-disk-from-a-storage-account"></a>Přidání datového disku z účtu úložiště

Další informace o práci s účty úložiště ve službě Azure Stack najdete v tématu [seznámení se službou Azure Stack storage](azure-stack-storage-overview.md).

1. Vyberte **účtu úložiště** používat.
2. Vyberte **kontejneru** kam chcete umístit datový disk. Z **kontejnery** okně můžete vytvořit nový kontejner. Pokud chcete. Pak můžete změnit umístění nového disku do vlastního kontejneru. Pokud použijete samostatný kontejner pro každý disk, je distribuovat umístění datového disku, což zvyšuje výkon.
3. Zvolte **vyberte** a uložte výběr.

    ![Příklad: Vyberte kontejner](media/azure-stack-manage-vm-disks/select-container.png)

## <a name="attach-an-existing-data-disk-to-a-vm"></a>Připojit stávající datový disk k virtuálnímu počítači

1. [Připravte soubor VHD](https://docs.microsoft.com/azure/virtual-machines/windows/classic/createupload-vhd) pro použití jako datového disku virtuálního počítače. Nahrání souboru VHD do účtu úložiště, který používáte s virtuálním Počítačem, na kterou chcete připojit soubor .vhd.

    Plán, který chcete použít jiný kontejner pro uložení souboru .vhd než kontejner, který obsahuje disk s operačním systémem.
    ![Příklad: Nahrání souboru VHD](media/azure-stack-manage-vm-disks/upload-vhd.png)

2. Po nahrání souboru VHD, jste připraveni připojit virtuální pevný disk k virtuálnímu počítači. V nabídce na levé straně vyberte **virtuálních počítačů**.  
 ![Příklad: Vyberte virtuální počítač na řídicím panelu](media/azure-stack-manage-vm-disks/vm-dashboard.png)

3. Vyberte virtuální počítač ze seznamu.

    ![Příklad: Vyberte virtuální počítač na řídicím panelu](media/azure-stack-manage-vm-disks/select-a-vm.png)

4. Na stránce pro virtuální počítač vyberte **disky**a pak vyberte **připojit existující**.

    ![Příklad: Připojení stávajícího disku](media/azure-stack-manage-vm-disks/attach-disks2.png)

5. V **připojit stávající disk** stránce **souboru virtuálního pevného disku**. **Účty úložiště** otevře se stránka.

    ![Příklad: Vyberte soubor virtuálního pevného disku](media/azure-stack-manage-vm-disks/select-vhd.png)

6. V části **účty úložiště**, vyberte účet, který chcete použít a pak zvolte kontejner, který obsahuje soubor .vhd, který byl dříve odeslán. Vyberte soubor VHD a klikněte na tlačítko **vyberte** a uložte výběr.

    ![Příklad: Vyberte kontejner](media/azure-stack-manage-vm-disks/select-container2.png)

7. V části **připojit stávající disk**, vybraný soubor je uvedený v části **souboru virtuálního pevného disku**. Aktualizace **hostovat ukládání do mezipaměti** nastavení disku a pak vyberte **OK** uložte novou konfiguraci disku pro virtuální počítač.

    ![Příklad: Připojit soubor virtuálního pevného disku](media/azure-stack-manage-vm-disks/attach-vhd.png)

8. Poté, co Azure Stack disk vytvoří a připojí ho k virtuálnímu počítači, nový disk je uvedený v nastavení disku Virtuálního počítače v rámci **datové disky**.

    ![Příklad: Kompletní připojit disk](media/azure-stack-manage-vm-disks/complete-disk-attach.png)

## <a name="use-powershell-to-add-multiple-unmanaged-disks-to-a-vm"></a>Chcete-li přidat více nespravované disky virtuálního počítače pomocí Powershellu

Zřízení virtuálního počítače a přidat nový datový disk pomocí Powershellu nebo připojit existující **VHD** souboru jako datového disku.

**Add-AzureRmVMDataDisk** rutina přidá datový disk k virtuálnímu počítači. Datový disk můžete přidat při vytváření virtuálního počítače nebo datový disk můžete přidat do existujícího virtuálního počítače. Zadejte **VhdUri** parametr k distribuci discích, které mají různé kontejnery.

### <a name="add-data-disks-to-a-new-vm"></a>Použití datových disků k novému virtuálnímu počítači

Následující příklady používají příkazy prostředí PowerShell k vytvoření virtuálního počítače pomocí tří datových disků, každý umístěny do jiného kontejneru.

První příkaz vytvoří objekt virtuálního počítače a uloží jej v `$VirtualMachine` proměnné. Příkaz přiřadí název a velikost virtuálního počítače:

```powershell
$VirtualMachine = New-AzureRmVMConfig -VMName "VirtualMachine" `
                                      -VMSize "Standard_A2"
```

Následující tři příkazy přiřadit cesty tří datových disků, aby `$DataDiskVhdUri01`, `$DataDiskVhdUri02`, a `$DataDiskVhdUri03` proměnné. Definujte název jiné cestě v adrese URL pro distribuci disků do různých kontejnerů:

```powershell
$DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
```

```powershell
$DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
```

```powershell
$DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
```

Poslední tři příkazy přidat datové disky virtuálního počítače uloženého v `$VirtualMachine`. Každý příkaz určuje název, umístění a další vlastnosti disku. Identifikátor URI každý disk je uložený v `$DataDiskVhdUri01`, `$DataDiskVhdUri02`, a `$DataDiskVhdUri03`:

```powershell
$VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk1' `
                -Caching 'ReadOnly' -DiskSizeInGB 10 -Lun 0 `
                -VhdUri $DataDiskVhdUri01 -CreateOption Empty
```

```powershell
$VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk2' `
                -Caching 'ReadOnly' -DiskSizeInGB 11 -Lun 1 `
                -VhdUri $DataDiskVhdUri02 -CreateOption Empty
```

```powershell
$VirtualMachine = Add-AzureRmVMDataDisk -VM $VirtualMachine -Name 'DataDisk3' `
                -Caching 'ReadOnly' -DiskSizeInGB 12 -Lun 2 `
                -VhdUri $DataDiskVhdUri03 -CreateOption Empty
```

Použijte následující příkazy Powershellu k přidání konfigurace operačního systému disku a sítě k virtuálnímu počítači a potom spusťte nový virtuální počítač:

```powershell
#set variables
$rgName = "myResourceGroup"
$location = "local"
#Set OS Disk
$osDiskUri = "https://contoso.blob.local.azurestack.external/vhds/osDisk.vhd"
$osDiskName = "osDisk"

$VirtualMachine = Set-AzureRmVMOSDisk -VM $VirtualMachine -Name $osDiskName -VhdUri $osDiskUri `
    -CreateOption FromImage -Windows

# Create a subnet configuration
$subnetName = "mySubNet"
$singleSubnet = New-AzureRmVirtualNetworkSubnetConfig -Name $subnetName -AddressPrefix 10.0.0.0/24

# Create a vnet configuration
$vnetName = "myVnetName"
$vnet = New-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName -Location $location `
    -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet

# Create a public IP
$ipName = "myIP"
$pip = New-AzureRmPublicIpAddress -Name $ipName -ResourceGroupName $rgName -Location $location `
    -AllocationMethod Dynamic

# Create a network security group configuration
$nsgName = "myNsg"
$rdpRule = New-AzureRmNetworkSecurityRuleConfig -Name myRdpRule -Description "Allow RDP" `
    -Access Allow -Protocol Tcp -Direction Inbound -Priority 110 `
    -SourceAddressPrefix Internet -SourcePortRange * `
    -DestinationAddressPrefix * -DestinationPortRange 3389
$nsg = New-AzureRmNetworkSecurityGroup -ResourceGroupName $rgName -Location $location `
    -Name $nsgName -SecurityRules $rdpRule

# Create a NIC configuration
$nicName = "myNicName"
$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName `
-Location $location -SubnetId $vnet.Subnets[0].Id -NetworkSecurityGroupId $nsg.Id -PublicIpAddressId $pip.Id

#Create the new VM
$VirtualMachine = Set-AzureRmVMOperatingSystem -VM $VirtualMachine -Windows -ComputerName VirtualMachine | `
    Set-AzureRmVMSourceImage -PublisherName MicrosoftWindowsServer -Offer WindowsServer `
    -Skus 2016-Datacenter -Version latest | Add-AzureRmVMNetworkInterface -Id $nic.Id
New-AzureRmVM -ResourceGroupName $rgName -Location $location -VM $VirtualMachine
```

### <a name="add-data-disks-to-an-existing-vm"></a>Přidat datové disky existujícího virtuálního počítače

Následující příklady používají příkazy Powershellu přidáte tři datové disky existujícího virtuálního počítače. První příkaz načte virtuální počítač s názvem **VirtualMachine** pomocí **Get-AzureRmVM** rutiny. Tento příkaz uloží virtuální počítač v `$VirtualMachine` proměnné:

```powershell
$VirtualMachine = Get-AzureRmVM -ResourceGroupName "myResourceGroup" `
                                -Name "VirtualMachine"
```

Následující tři příkazy přiřadit cesty tří datových disků, aby `$DataDiskVhdUri01`, `$DataDiskVhdUri02`, a `$DataDiskVhdUri03` proměnné. Názvy jinou cestu v identifikátorech URI virtuálního pevného disku ukazují různé kontejnery pro umístění disku:

```powershell
$DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
```

```powershell
$DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
```

```powershell
$DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
```

Následující tři příkazy přidat datové disky virtuálního počítače uloženou v `$VirtualMachine` proměnné. Každý příkaz určuje název, umístění a další vlastnosti disku. Identifikátor URI každý disk je uložený v `$DataDiskVhdUri01`, `$DataDiskVhdUri02`, a `$DataDiskVhdUri03`:

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk1" `
                      -VhdUri $DataDiskVhdUri01 -LUN 0 `
                      -Caching ReadOnly -DiskSizeinGB 10 -CreateOption Empty
```

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk2" `
                      -VhdUri $DataDiskVhdUri02 -LUN 1 `
                      -Caching ReadOnly -DiskSizeinGB 11 -CreateOption Empty
```

```powershell
Add-AzureRmVMDataDisk -VM $VirtualMachine -Name "disk3" `
                      -VhdUri $DataDiskVhdUri03 -LUN 2 `
                      -Caching ReadOnly -DiskSizeinGB 12 -CreateOption Empty
```

Poslední příkaz aktualizuje stav virtuálního počítače uloženou v `$VirtualMachine` v `-ResourceGroupName`:

```powershell
Update-AzureRmVM -ResourceGroupName "myResourceGroup" -VM $VirtualMachine
```

## <a name="next-steps"></a>Další postup

Další informace o virtuálních počítačích Azure Stack, najdete v článku [důležité informace týkající se virtuálních počítačů ve službě Azure Stack](azure-stack-vm-considerations.md).
