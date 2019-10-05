---
title: Vytvoření diskového úložiště virtuálního počítače v Azure Stack | Microsoft Docs
description: Vytvořte disky pro virtuální počítače v Azure Stack.
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
ms.date: 10/02/2019
ms.author: sethm
ms.reviewer: jiahan
ms.lastreviewed: 01/18/2019
ms.openlocfilehash: af110f6b4140a69e01dadcd38a32843866744abf
ms.sourcegitcommit: b2d19e12a50195bb8925879ee75c186c9604f313
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71961600"
---
# <a name="create-vm-disk-storage-in-azure-stack"></a>Vytvoření diskového úložiště virtuálního počítače v Azure Stack

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit @ no__t-0

Tento článek popisuje, jak vytvořit úložiště disku virtuálního počítače pomocí Azure Stackového portálu nebo pomocí PowerShellu.

## <a name="overview"></a>Přehled

Počínaje verzí 1808 Azure Stack podporuje na virtuálních počítačích použití spravovaných disků a nespravovaných disků, jako je operační systém (OS) i datový disk. Před verzí 1808 jsou podporovány pouze nespravované disky.

[Spravované disky](/azure/virtual-machines/windows/managed-disks-overview) zjednodušují správu disků pro virtuální počítače Azure IaaS tím, že spravují účty úložiště přidružené k DISKŮM virtuálních počítačů. Stačí jenom zadat požadovanou velikost disku a Azure Stack ho vytvoří a bude spravovat za vás.

Nespravované disky vyžadují, abyste vytvořili účet úložiště pro ukládání disků. Disky, které vytvoříte, jsou označovány jako disky virtuálních počítačů a jsou uloženy v kontejnerech v účtu úložiště.

### <a name="best-practice-guidelines"></a>Pokyny k osvědčeným postupům

Pro zvýšení výkonu a snížení celkových nákladů doporučujeme umístit všechny disky virtuálního počítače do samostatného kontejneru. Kontejner by měl obsahovat buď disk s operačním systémem, nebo datový disk, ale ne obojí současně. Do stejného kontejneru ale také můžete umístit oba typy disků.

Pokud přidáte jeden nebo více datových disků k virtuálnímu počítači, použijte k uložení těchto disků další kontejnery jako umístění. Disk s operačním systémem pro další virtuální počítače by měl být také ve vlastních kontejnerech.

Při vytváření virtuálních počítačů můžete znovu použít stejný účet úložiště pro každý nový virtuální počítač. Pouze kontejnery, které vytvoříte, by měly být jedinečné.

### <a name="adding-new-disks"></a>Přidávání nových disků

Následující tabulka shrnuje, jak přidat disky pomocí portálu a pomocí prostředí PowerShell:

| Metoda | Možnosti
|-|-|
|User Portal|– Přidejte nové datové disky do existujícího virtuálního počítače. Nové disky jsou vytvořeny pomocí Azure Stack. </br> </br> – Přidejte existující soubor disku (. VHD) do dříve vytvořeného virtuálního počítače. K tomu je potřeba připravit soubor. VHD a pak ho nahrát do Azure Stack. |
|[PowerShell](#use-powershell-to-add-multiple-unmanaged-disks-to-a-vm) | – Vytvořte nový virtuální počítač s diskem s operačním systémem a na tento virtuální počítač přidejte aspoň jeden datový disk. |

## <a name="use-the-portal-to-add-disks-to-a-vm"></a>Přidání disků do virtuálního počítače pomocí portálu

Ve výchozím nastavení platí, že když použijete portál k vytvoření virtuálního počítače pro většinu položek Marketplace, vytvoří se jenom disk s operačním systémem.

Po vytvoření virtuálního počítače můžete portál použít k těmto akcím:

* Vytvořte nový datový disk a připojte ho k virtuálnímu počítači.
* Nahrajte existující datový disk a připojte ho k virtuálnímu počítači.

Každý nespravovaný disk, který přidáte, by měl být umístěn v samostatném kontejneru.

>[!NOTE]  
>Disky vytvořené a spravované pomocí Azure se nazývají [spravované disky](/azure/virtual-machines/windows/managed-disks-overview).

### <a name="use-the-portal-to-create-and-attach-a-new-data-disk"></a>Vytvoření a připojení nového datového disku pomocí portálu

1. Na portálu vyberte **všechny služby**a pak **virtuální počítače**.
   ![Příklad: Řídicí panel virtuálních počítačů @ no__t-0

2. Vyberte virtuální počítač, který byl dříve vytvořen.
   ![Příklad: Vyberte virtuální počítač na řídicím panelu @ no__t-0.

3. Pro virtuální počítač vyberte **disky**a pak **přidat datový disk**.
   ![Příklad: Připojte nový disk k virtuálnímu počítači @ no__t-0

4. Pro datový disk:
   * Zadejte **logickou jednotku (LUN)** . Logická jednotka (LUN) musí být platné číslo.
   * Vyberte **vytvořit disk**.
   ![Příklad: Připojte nový disk k virtuálnímu počítači @ no__t-0

5. V okně **vytvořit spravovaný disk** :
   * Zadejte **název** disku.
   * Vyberte existující **skupinu prostředků** nebo vytvořte novou.
   * Vyberte **umístění**. Ve výchozím nastavení je umístění nastaveno na stejný kontejner, který obsahuje disk s operačním systémem.
   * Vyberte **typ účtu**.
      ![Příklad: Připojte nový disk k virtuálnímu počítači @ no__t-0

      **Premium SSD**  
      Disky úrovně Premium (SSD) jsou založené na jednotkách Solid-State a nabízejí konzistentní výkon s nízkou latencí. Poskytují nejlepší rovnováhu mezi cenou a výkonem a jsou ideální pro aplikace náročné na vstupně-výstupní operace a produkční úlohy.

      **HDD úrovně Standard**  
      Standardní disky (HDD) se zálohují na magnetické jednotky a jsou vhodnější pro aplikace, na kterých se data otevírají zřídka. Redundantní disky v zóně jsou založené na zóně redundantního úložiště (ZRS), které replikují vaše data napříč několika zónami, a zajišťuje tak dostupnost vašich dat i v případě, že je jedna zóna mimo provoz.

   * Vyberte **typ zdroje**.

     Vytvořte disk ze snímku jiného disku, objektu BLOB v účtu úložiště nebo vytvořte prázdný disk.

      **Snímek**: Vyberte snímek, pokud je k dispozici. Snímek musí být dostupný v předplatném a umístění virtuálního počítače.

      **Objekt BLOB úložiště**:
     * Přidejte identifikátor URI objektu BLOB úložiště, který obsahuje bitovou kopii disku.  
     * Vyberte **Procházet** a otevřete okno účty úložiště. Pokyny najdete v tématu [Přidání datového disku z účtu úložiště](#add-a-data-disk-from-a-storage-account).
     * Vyberte typ operačního systému pro bitovou kopii: **Windows**, **Linux**nebo **žádný (datový disk)** .

   * Vyberte **Velikost (GIB)** .

     Náklady na disk úrovně Standard se zvyšují podle velikosti disku. Náklady na disk Premium a výkon se zvyšují na základě velikosti disku. Další informace najdete v tématu [Managed disks ceny](https://go.microsoft.com/fwlink/?linkid=843142).

   * Vyberte **Vytvořit**. Azure Stack vytvoří a ověří spravovaný disk.

6. Po Azure Stack vytvoří disk a připojí ho k virtuálnímu počítači, nový disk se zobrazí v nastavení disku virtuálního počítače v části **datové disky**.

   ![Příklad: Zobrazit disk](media/azure-stack-manage-vm-disks/view-data-disk.png)

### <a name="add-a-data-disk-from-a-storage-account"></a>Přidání datového disku z účtu úložiště

Další informace o práci s účty úložiště v Azure Stack najdete v tématu [Úvod do Azure Stack Storage](azure-stack-storage-overview.md).

1. Vyberte **účet úložiště** , který se má použít.
2. Vyberte **kontejner** , do kterého chcete uložit datový disk. V okně **kontejnery** můžete vytvořit nový kontejner, pokud chcete. Pak můžete změnit umístění nového disku na vlastní kontejner. Pokud pro každý disk použijete samostatný kontejner, rozšíříte umístění datového disku, který zvyšuje výkon.
3. Zvolením **možnosti vybrat** uložte výběr.

    ![Příklad: Vyberte kontejner.](media/azure-stack-manage-vm-disks/select-container.png)

## <a name="attach-an-existing-data-disk-to-a-vm"></a>Připojit existující datový disk k virtuálnímu počítači

1. [Připravte soubor. VHD](/azure/virtual-machines/windows/classic/createupload-vhd) pro použití jako datový disk pro virtuální počítač. Nahrajte tento soubor. VHD do účtu úložiště, který používáte s virtuálním počítačem, ke kterému chcete připojit soubor. VHD.

    Naplánujte použití jiného kontejneru pro uložení souboru. VHD, než je kontejner, který obsahuje disk s operačním systémem.
    ![Příklad: Nahrání souboru VHD @ no__t-0

2. Po nahrání souboru. VHD jste připraveni připojit virtuální pevný disk k virtuálnímu počítači. V nabídce na levé straně vyberte **virtuální počítače**.  
 ![Příklad: Vyberte virtuální počítač na řídicím panelu @ no__t-0.

3. V seznamu vyberte virtuální počítač.

    ![Příklad: Výběr virtuálního počítače na řídicím panelu](media/azure-stack-manage-vm-disks/select-a-vm.png)

4. Na stránce pro virtuální počítač vyberte **disky**a pak vyberte **připojit existující**.

    ![Příklad: Připojení stávajícího disku](media/azure-stack-manage-vm-disks/attach-disks2.png)

5. Na stránce **připojit existující disk** vyberte **soubor VHD**. Otevře se stránka **účty úložiště** .

    ![Příklad: Vybrat soubor VHD](media/azure-stack-manage-vm-disks/select-vhd.png)

6. V části **účty úložiště**vyberte účet, který se má použít, a pak zvolte kontejner, který obsahuje soubor. VHD, který jste nahráli dříve. Vyberte soubor. VHD a pak zvolte **Vybrat** a uložte výběr.

    ![Příklad: Vyberte kontejner.](media/azure-stack-manage-vm-disks/select-container2.png)

7. V části **Připojit stávající disk**se vybraný soubor zobrazí v části **soubor VHD**. Aktualizujte nastavení **ukládání do mezipaměti hostitele** disku a pak výběrem **OK** uložte novou konfiguraci disku pro virtuální počítač.

    ![Příklad: Připojit soubor VHD](media/azure-stack-manage-vm-disks/attach-vhd.png)

8. Po Azure Stack vytvoří disk a připojí ho k virtuálnímu počítači, nový disk se zobrazí v nastavení disku virtuálního počítače v části **datové disky**.

    ![Příklad: Dokončete připojení k disku.](media/azure-stack-manage-vm-disks/complete-disk-attach.png)

## <a name="use-powershell-to-add-multiple-unmanaged-disks-to-a-vm"></a>Použití PowerShellu k přidání několika nespravovaných disků do virtuálního počítače

Pomocí PowerShellu můžete zřídit virtuální počítač a přidat nový datový disk nebo připojit existující soubor. VHD jako datový disk.

Rutina **Add-AzureRmVMDataDisk** přidá datový disk k virtuálnímu počítači. Datový disk můžete přidat při vytváření virtuálního počítače nebo můžete přidat datový disk k existujícímu virtuálnímu počítači. Zadejte parametr **VhdUri** pro distribuci disků do různých kontejnerů.

### <a name="add-data-disks-to-a-new-vm"></a>Přidání datových disků do nového virtuálního počítače

Následující příklady používají příkazy prostředí PowerShell k vytvoření virtuálního počítače se třemi datovými disky, které jsou umístěny v jiném kontejneru.

První příkaz vytvoří objekt virtuálního počítače a uloží jej do proměnné `$VirtualMachine`. Příkaz přiřadí virtuálnímu počítači název a velikost:

```powershell
$VirtualMachine = New-AzureRmVMConfig -VMName "VirtualMachine" `
                                      -VMSize "Standard_A2"
```

Následující tři příkazy přiřadí cesty tří datových disků k proměnným `$DataDiskVhdUri01`, `$DataDiskVhdUri02` a `$DataDiskVhdUri03`. V adrese URL definujte jiný název cesty pro distribuci disků do různých kontejnerů:

```powershell
$DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
```

```powershell
$DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
```

```powershell
$DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
```

Poslední tři příkazy přidávají datové disky do virtuálního počítače uloženého v `$VirtualMachine`. Každý příkaz určuje název, umístění a další vlastnosti disku. Identifikátor URI každého disku je uložený v `$DataDiskVhdUri01`, `$DataDiskVhdUri02` a `$DataDiskVhdUri03`:

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

Pomocí následujících příkazů PowerShell přidejte do virtuálního počítače disk s operačním systémem a konfiguraci sítě a potom spusťte nový virtuální počítač:

```powershell
# Set variables
$rgName = "myResourceGroup"
$location = "local"
# Set OS Disk
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

### <a name="add-data-disks-to-an-existing-vm"></a>Přidání datových disků do existujícího virtuálního počítače

Následující příklady používají příkazy prostředí PowerShell k přidání tří datových disků do existujícího virtuálního počítače. První příkaz načte virtuální počítač s názvem **VirtualMachine** pomocí rutiny **Get-AzureRmVM** . Příkaz uloží virtuální počítač do proměnné `$VirtualMachine`:

```powershell
$VirtualMachine = Get-AzureRmVM -ResourceGroupName "myResourceGroup" `
                                -Name "VirtualMachine"
```

Následující tři příkazy přiřadí cesty tří datových disků k proměnným `$DataDiskVhdUri01`, `$DataDiskVhdUri02` a `$DataDiskVhdUri03`. Názvy různých cest v identifikátorech URI VHD označují různé kontejnery pro umístění disku:

```powershell
$DataDiskVhdUri01 = "https://contoso.blob.local.azurestack.external/test1/data1.vhd"
```

```powershell
$DataDiskVhdUri02 = "https://contoso.blob.local.azurestack.external/test2/data2.vhd"
```

```powershell
$DataDiskVhdUri03 = "https://contoso.blob.local.azurestack.external/test3/data3.vhd"
```

Následující tři příkazy přidají datové disky do virtuálního počítače uloženého v proměnné `$VirtualMachine`. Každý příkaz určuje název, umístění a další vlastnosti disku. Identifikátor URI každého disku je uložený v `$DataDiskVhdUri01`, `$DataDiskVhdUri02` a `$DataDiskVhdUri03`:

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

Poslední příkaz aktualizuje stav virtuálního počítače, který je uložený v `$VirtualMachine` v `-ResourceGroupName`:

```powershell
Update-AzureRmVM -ResourceGroupName "myResourceGroup" -VM $VirtualMachine
```

## <a name="next-steps"></a>Další kroky

Další informace o Azure Stack virtuálních počítačů najdete [v tématu týkajícím se Virtual Machines v Azure Stack](azure-stack-vm-considerations.md).
