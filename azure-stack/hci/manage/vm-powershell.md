---
title: Správa virtuálních počítačů ve Azure Stack HCI pomocí prostředí Windows PowerShell
description: Správa virtuálních počítačů v Azure Stack HCI pomocí prostředí Windows PowerShell
author: v-dasis
ms.topic: article
ms.date: 05/20/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 471dc57505832ef58424a74d08f1534a66ea285e
ms.sourcegitcommit: d69eacbf48c06309b00d17c82ebe0ce2bc6552df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/22/2020
ms.locfileid: "83794193"
---
# <a name="manage-vms-on-azure-stack-hci-using-windows-powershell"></a>Správa virtuálních počítačů ve Azure Stack HCI pomocí prostředí Windows PowerShell

> Platí pro Windows Server 2019.

Prostředí Windows PowerShell slouží k vytváření a správě virtuálních počítačů na Azure Stack HCL.

Obvykle můžete spravovat virtuální počítače ze vzdáleného počítače se systémem Windows 10, nikoli na hostitelském serveru v clusteru. Tento vzdálený počítač se nazývá počítač pro správu.

> [!NOTE]
> Pokud spouštíte příkazy prostředí PowerShell z počítače pro správu, zahrňte parametr-ComputerName s názvem hostitelského serveru, který spravujete. Názvy rozhraní NetBIOS, IP adresy a plně kvalifikované názvy domény jsou povoleny.

Kompletní referenční dokumentaci pro správu virtuálních počítačů pomocí prostředí PowerShell najdete v tématu [Reference k Hyper-V](https://docs.microsoft.com/powershell/module/hyper-v/?view=win10-ps).

## <a name="get-a-list-of-vms"></a>Získání seznamu virtuálních počítačů

Následující příklad vrátí seznam všech virtuálních počítačů v Server1.

```powershell
Get-VM -ComputerName Server1
```

Následující příklad vrátí seznam všech spuštěných virtuálních počítačů na serveru přidáním filtru pomocí `Where-Object` příkazu. Další informace najdete v tématu [použití dokumentace Where-Object](<https://docs.microsoft.com/previous-versions/windows/it-pro/windows-powershell-1.0/ee177028(v=technet.10)>) .

```powershell
Get-VM -ComputerName Server1 | Where-Object -Property State -eq "Running"
```

V dalším příkladu se vrátí seznam všech vypnutých virtuálních počítačů na serveru.

```powershell
Get-VM -ComputerName Server1 | Where-Object -Property State -eq "Off"
```

## <a name="start-and-stop-a-vm"></a>Spuštění a zastavení virtuálního počítače

Pomocí `Start-VM` příkazů a `Stop-VM` Spusťte nebo zastavte virtuální počítač. Podrobné informace najdete v dokumentaci [Start-VM](https://docs.microsoft.com/powershell/module/hyper-v/start-vm?view=win10-ps) a [Stop-VM](https://docs.microsoft.com/powershell/module/hyper-v/stop-vm?view=win10-ps) reference.

Následující příklad ukazuje, jak spustit virtuální počítač s názvem VM1:

```powershell
Start-VM -Name VM1 -ComputerName Server1
```

Následující příklad ukazuje, jak vypnout virtuální počítač s názvem TestVM:

```powershell
Stop-VM -Name VM1 -ComputerName Server1
```

## <a name="move-a-vm"></a>Přesun virtuálního počítače

`Move-VM`Rutina přesune virtuální počítač na jiný server. Další informace najdete v referenční dokumentaci k [Move-VM](https://docs.microsoft.com/powershell/module/hyper-v/move-vm?view=win10-ps) .

 Následující příklad ukazuje, jak přesunout virtuální počítač na Server2, když je virtuální počítač uložený ve sdílené složce SMB v Server1:

```powershell
Move-VM -ComputerName Server1 -Name VM1 -DestinationHost Server2
 ```

Následující příklad ukazuje, jak přesunout virtuální počítač na Server2 z Server1 a přesunout všechny soubory přidružené k tomuto VIRTUÁLNÍmu počítači do D:\ VM_name na vzdáleném počítači:

```powershell
Move-VM -ComputerName Server1 -Name VM1 -DestinationHost Server2 -IncludeStorage -DestinationStoragePath D:\VM_name
```

## <a name="import-or-export-a-vm"></a>Import nebo Export virtuálního počítače

`Import-VM` `Export-VM` Rutiny a importují a exportují virtuální počítač. Následující příklad ukazuje několik příkladů. Další informace najdete v dokumentaci [Import-VM](https://docs.microsoft.com/powershell/module/hyper-v/import-vm?view=win10-ps) a [Export-VM](https://docs.microsoft.com/powershell/module/hyper-v/export-vm?view=win10-ps) reference.

Následující příklad ukazuje, jak importovat virtuální počítač ze svého konfiguračního souboru. Virtuální počítač je zaregistrován místně, takže jeho soubory se nekopírují:

```powershell
Import-VM -ComputerName Server1 -Name VM1 -Path 'C:\<vm export path>\2B91FEB3-F1E0-4FFF-B8BE-29CED892A95A.vmcx'
```

Následující příklad exportuje virtuální počítač do kořenového adresáře jednotky D:

```powershell
Export-VM -ComputerName Server1 -Name VM1 -Path D:\
```

## <a name="rename-a-vm"></a>Přejmenování virtuálního počítače

`Rename-VM`Rutina slouží k přejmenování virtuálního počítače. Podrobné informace najdete v dokumentaci k [přejmenování-VM](https://docs.microsoft.com/powershell/module/hyper-v/rename-vm?view=win10-ps) .

Následující příklad přejmenuje VM1 na VM2 a zobrazí přejmenovaný virtuální počítač:

```powershell
Rename-VM -ComputerName Server1 -Name VM1 -NewName VM2
```

## <a name="create-a-vm-checkpoint"></a>Vytvoření kontrolního bodu virtuálního počítače

`Checkpoint-VM`Rutina se používá k vytvoření kontrolního bodu pro virtuální počítač. Podrobné informace najdete v referenční dokumentaci k [kontrolnímu bodu virtuálního počítače](https://docs.microsoft.com/powershell/module/hyper-v/checkpoint-vm?view=win10-ps) .

Následující příklad vytvoří kontrolní bod s názvem BeforeInstallingUpdates pro virtuální počítač s názvem test.

```powershell
Checkpoint-VM -ComputerName Server1 -Name VM1 -SnapshotName BeforeInstallingUpdates
```

## <a name="create-a-vhd-for-a-vm"></a>Vytvoření virtuálního pevného disku pro virtuální počítač

`New-VHD`Rutina se používá k vytvoření nového virtuálního pevného disku pro virtuální počítač. Podrobné informace o tom, jak ho používat, najdete v referenční dokumentaci k [novému virtuálnímu](https://docs.microsoft.com/powershell/module/hyper-v/new-vhd?view=win10-ps) pevnému disku.

Následující příklad vytvoří dynamický virtuální pevný disk ve formátu VHDX, který má velikost 10 GB. Přípona názvu souboru určuje formát a výchozí typ dynamické je použit, protože není zadán žádný typ.

```powershell
New-VHD -ComputerName Server1 -Name VM1 -Path c:\Base.vhdx -SizeBytes 10GB
```

## <a name="add-a-network-adapter-to-a-vm"></a>Přidání síťového adaptéru k virtuálnímu počítači

`Add-VMNetworkAdapter`Rutina se používá k přidání virtuálního síťového adaptéru k virtuálnímu počítači. Následující příklad ukazuje několik příkladů. Podrobné informace o tom, jak ho používat, najdete v referenční dokumentaci k [Add-VMNetworkAdapter](https://docs.microsoft.com/powershell/module/hyper-v/add-vmnetworkadapter?view=win10-ps) .

Následující příklad přidá virtuální síťový adaptér s názvem Redmond NIC1 do virtuálního počítače s názvem VM1:

```powershell
Add-VMNetworkAdapter -ComputerName Server1 -VMName VM1 -Name "Redmond NIC1"
```

Tento příklad přidá virtuální síťový adaptér k virtuálnímu počítači s názvem VM1 a připojí ho k virtuálnímu přepínači s názvem Network:

```powershell
Add-VMNetworkAdapter -ComputerName Server1 -VMName VM1 -SwitchName Network
```

## <a name="create-a-virtual-switch-for-a-vm"></a>Vytvoření virtuálního přepínače pro virtuální počítač

`New-VMSwitch`Rutina se používá pro nový virtuální přepínač na hostiteli virtuálního počítače. Podrobné informace o tom, jak ho používat, najdete v referenční dokumentaci k [New-VMSwitch](https://docs.microsoft.com/powershell/module/hyper-v/new-vmswitch?view=win10-ps) .

Následující příklad vytvoří nový přepínač s názvem "přepínač technologie QoS", který se váže k síťovému adaptéru s názvem kabelové připojení Ethernet 3 a podporuje minimální šířku pásma na základě váhy.

```powershell
New-VMSwitch "QoS Switch" -NetAdapterName "Wired Ethernet Connection 3" -MinimumBandwidthMode Weight
```

## <a name="set-memory-for-a-vm"></a>Nastavení paměti pro virtuální počítač

`Set-VMMemory`Rutina se používá ke konfiguraci paměti virtuálního počítače. Podrobné informace o tom, jak ho používat, najdete v referenční dokumentaci k [set-VMMemory](https://docs.microsoft.com/powershell/module/hyper-v/set-vmmemory?view=win10-ps) .

Následující příklad povoluje dynamickou paměť na virtuálním počítači s názvem VM1, nastavuje jeho minimální, spouštěcí a maximální paměť, její prioritu paměti a její vyrovnávací paměť.

```powershell
Set-VMMemory -ComputerName Server1 -Name VM1 -DynamicMemoryEnabled $true -MinimumBytes 64MB -StartupBytes 256MB -MaximumBytes 2GB -Priority 80 -Buffer 25
```

## <a name="set-virtual-processors-for-a-vm"></a>Nastavení virtuálních procesorů pro virtuální počítač

`Set-VMProcessor`Rutina se používá ke konfiguraci virtuálních procesorů pro virtuální počítač. Podrobné informace o tom, jak ho používat, najdete v referenční dokumentaci k [set-VMProcessor](https://docs.microsoft.com/powershell/module/hyper-v/set-vmprocessor?view=win10-ps
) .

V následujícím příkladu se nakonfiguruje virtuální počítač s názvem VM1 se dvěma virtuálními procesory, rezerva 10%, omezení 75% a relativní váha 200.

```powershell
Set-VMProcessor -ComputerName Server1 -Name VM1 -Count 2 -Reserve 10 -Maximum 75 -RelativeWeight 200
```

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače  

`New-VM`Rutina se používá k vytvoření nového virtuálního počítače. Podrobné informace o použití najdete v referenční dokumentaci k [novému virtuálnímu počítači](https://docs.microsoft.com/powershell/module/hyper-v/new-vm?view=win10-ps) .

Tady jsou nastavení, která můžete zadat při vytváření nového virtuálního počítače s existujícím virtuálním pevným diskem, kde:

- **-Name** je název, který zadáte pro virtuální počítač, který vytváříte.

- **-MemoryStartupBytes** je velikost paměti, která je k dispozici pro virtuální počítač při spuštění.

- **-BootDevice** je zařízení, ke kterému se virtuální počítač spustí, když se spustí.
 Obvykle se jedná o virtuální pevný disk (VHD), soubor ISO pro spuštění na základě DVD nebo síťový adaptér (síťový adaptér) pro spuštění ze sítě.

- **-VHDPath** je cesta k disku virtuálního počítače, který chcete použít.

- **-Path** je cesta k uložení konfiguračních souborů virtuálního počítače.

- **-Generace** je generace virtuálního počítače. Použijte generaci 1 pro VHD a generaci 2 pro VHDX.

- **-Switch** je název virtuálního přepínače, který má virtuální počítač používat pro připojení k jiným virtuálním počítačům nebo síti. Získejte název virtuálního přepínače pomocí [Get-VMSwitch](https://docs.microsoft.com/powershell/module/hyper-v/get-vmswitch?view=win10-ps). Například:  

Úplný příkaz, který je pro vytvoření virtuálního počítače s názvem VM1 následující:

 ```powershell
 New-VM -ComputerName Server1 -Name VM1 -MemoryStartupBytes <Memory> -BootDevice <BootDevice> -VHDPath <VHDPath> -Path <Path> -Generation <Generation> -Switch <SwitchName>
 ```

V dalším příkladu se vytvoří virtuální počítač 2. generace s 4 GB paměti. Spustí se ze složky VMs\Win10.vhdx v aktuálním adresáři a použije virtuální přepínač s názvem ExternalSwitch. Konfigurační soubory virtuálního počítače jsou uložené ve složce VMData.  

``` powershell
New-VM -ComputerName Server1 -Name VM1 -MemoryStartupBytes 4GB -BootDevice VHD -VHDPath .\VMs\Win10.vhdx -Path .\VMData -Generation 2 -Switch ExternalSwitch
```

K určení virtuálních pevných disků se používají následující parametry.

Pokud chcete vytvořit virtuální počítač s novým virtuálním pevným diskem, nahraďte parametr **-VHDPath** z výše uvedeného příkladu pomocí parametru **-NewVHDPath** a přidejte parametr **-NewVHDSizeBytes** , jak je znázorněno zde:  

``` powershell  
New-VM -ComputerName Server1 -Name VM1 -MemoryStartupBytes 4GB -BootDevice VHD -NewVHDPath .\VMs\Win10.vhdx -Path .\VMData -NewVHDSizeBytes 20GB -Generation 2 -Switch ExternalSwitch  
 ```  

Pokud chcete vytvořit virtuální počítač s novým virtuálním diskem, který se spustí s imagí operačního systému, přečtěte si příklad PowerShellu v tématu [Vytvoření virtuálního počítače návod pro Hyper-V ve Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_create_vm).  

## <a name="next-steps"></a>Další kroky  

Virtuální počítače taky můžete vytvářet a spravovat pomocí centra pro správu Windows. Další informace najdete v [centru pro správu systému Windows](https://docs.microsoft.com/windows-server/manage/windows-admin-center/overview).