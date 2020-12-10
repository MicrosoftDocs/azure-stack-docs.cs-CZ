---
title: Migrace na Azure Stack HCI na novém hardwaru
description: Přečtěte si, jak migrovat na Azure Stack HCI na novém hardwaru.
author: v-dasis
ms.topic: how-to
ms.date: 12/10/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 16fb7544fb223a1038b3f27d0416f0eda04012b6
ms.sourcegitcommit: 97ecba06aeabf2f30de240ac283b9bb2d49d62f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97011620"
---
# <a name="migrate-to-azure-stack-hci-on-new-hardware"></a>Migrace na Azure Stack HCI na novém hardwaru

> Platí pro Azure Stack HCI, verze 20H2; Windows Server 2019, Windows Server 2016, Windows Server 2012 R2, Windows Server 2008 R2

Toto téma popisuje, jak migrovat soubory virtuálního počítače (VM) v systému Windows Server 2012 R2, Windows Server 2016 nebo Windows Server 2019 na nový Azure Stack serverový hardware HCI pomocí prostředí Windows PowerShell a nástroje Robocopy. Robocopy je robustní Metoda kopírování souborů z jednoho serveru na jiný. Obnoví činnost v případě odpojení a pokračuje v práci z posledního známého stavu. Robocopy taky podporuje kopírování souborů s více vlákny přes protokol SMB (Server Message Block). Další informace najdete v tématu [Robocopy](https://docs.microsoft.com/windows-server/administration/windows-commands/robocopy).

> [!NOTE]
> Technologie Hyper-V Migrace za provozu a replika technologie Hyper-V ze systému Windows Server do Azure Stack HCI není podporována.

Pokud máte virtuální počítače ve Windows 2012 R2 nebo starším, které chcete migrovat, přečtěte si téma [migrace starších virtuálních počítačů](#migrating-older-vms).

Postup migrace na Azure Stack HCI pomocí stejného hardwaru najdete v tématu [migrace na Azure Stack HCI na stejném hardwaru](migrate-cluster-same-hardware.md).

Následující diagram znázorňuje zdrojový cluster Windows serveru a cílový cluster Azure Stack HCI jako příklad. Virtuální počítače taky můžete migrovat i na samostatné servery.

:::image type="content" source="media/migrate/migrate-cluster.png" alt-text="Migrovat cluster do Azure Stack HCL" lightbox="media/migrate/migrate-cluster.png":::

V souvislosti s předpokládaným prostojem se pomocí jediného síťového rozhraní s duální 40 GB RDMA East-West sítě mezi clustery a Robocopy nakonfigurované pro 32 více vláken můžete zrychlit přenosů s 1,9 TB za hodinu.

> [!NOTE]
> V tomto článku se nezabývá migrace virtuálních počítačů pro roztažené clustery.

## <a name="before-you-begin"></a>Než začnete

Před zahájením migrace je potřeba vzít v úvahu několik požadavků a věcí:

- Všechny příkazy prostředí Windows PowerShell musí být spuštěny jako správce.

- Musíte mít přihlašovací údaje domény s oprávněním správce pro zdrojový i cílový cluster s úplnými právy ke zdrojové a cílové organizační jednotce (OU), která obsahuje oba clustery.

- Oba clustery musí být ve stejné doménové struktuře a doméně služby Active Directory, aby se usnadnilo ověřování pomocí protokolu Kerberos mezi clustery pro migraci virtuálních počítačů.

- Oba clustery se musí nacházet v organizační jednotce služby Active Directory s nastavením dědičnosti blokování Zásady skupiny objektů (GPO) v této organizační jednotce. Tím se zajistí, že migrace nebude mít vliv na objekty zásad skupiny a zásady zabezpečení na úrovni domény.

- Oba clustery musí být připojeny ke stejnému zdroji dat, aby podporovaly konzistentní ověřování protokolem Kerberos mezi clustery.

- Poznamenejte si název virtuálního přepínače Hyper-V používaný virtuálními počítači na zdrojovém clusteru. Před importem virtuálních počítačů je nutné použít stejný název virtuálního přepínače pro cílový cluster Azure Stack HCL "síť virtuálního počítače".

- Odeberte všechny soubory imagí ISO pro zdrojové virtuální počítače. To se provádí pomocí Správce technologie Hyper-V ve **vlastnostech virtuálního počítače** v **části hardware**. Vyberte možnost **Odebrat** pro všechny virtuální jednotky CD/DVD.

- Vypněte všechny virtuální počítače ve zdrojovém clusteru. Tato operace je nutná k tomu, aby bylo zajištěno udržování řízení a stavu verzí během procesu migrace.

- Ověřte, jestli Azure Stack HCI podporuje vaši verzi virtuálních počítačů pro import a aktualizaci vašich virtuálních počítačů podle potřeby. Postup najdete v části [Podpora a aktualizace verzí virtuálních počítačů](#vm-version-support-and-update) .

- Zálohujte všechny virtuální počítače ve zdrojovém clusteru. Dokončete zálohování všech aplikací a dat konzistentní s chybou a zálohou všech databází konzistentních vzhledem k aplikacím. Informace o zálohování do Azure najdete v tématu [použití Azure Backup](https://docs.microsoft.com/azure-stack/hci/manage/use-azure-backup).

- Vytvořte kontrolní bod virtuálních počítačů a řadičů domény zdrojového clusteru pro případ, že se budete muset vrátit k předchozímu stavu. To se nevztahuje na fyzické servery.

- Zajistěte, aby maximální velikosti rámce typu Jumbo byly stejné mezi zdrojovými a cílovými sítěmi úložiště clusteru, konkrétně síťové adaptéry RDMA a jejich příslušné síťové porty přepínače, které poskytují nejúčinnější přenosnou velikost paketů na konci.

- Poznamenejte si název virtuálního přepínače Hyper-V ve zdrojovém clusteru. Znovu ji použijete v cílovém clusteru.

- Hardware Azure Stack HCL by měl mít alespoň stejnou kapacitu a konfiguraci jako zdrojový hardware.

- Minimalizujte počet segmentů směrování sítě nebo fyzickou vzdálenost mezi zdrojovým a cílovým clusterem, aby se usnadnilo nejrychlejší přenos souborů.

## <a name="vm-version-support-and-update"></a>Podpora a aktualizace verzí virtuálních počítačů

V této tabulce jsou uvedené verze operačních systémů Windows Server a jejich verze virtuálních počítačů.

Bez ohledu na verzi operačního systému, na kterém může být virtuální počítač spuštěný, je minimální verze virtuálního počítače podporovaná pro přímou migraci na Azure Stack HCL verze 5,0. To představuje výchozí verzi pro virtuální počítače v systému Windows Server 2012 R2. Proto musí být všechny virtuální počítače, na kterých běží verze 2,0, 3,0 nebo 4,0, aktualizované na verzi 5,0 před migrací.

|Verze operačního systému|Verze virtuálního počítače|
|---|---|
|Windows Server 2008 SP1|2.0|
|Windows Server 2008 R2|3.0|
|Windows Server 2012|4,0|
|Windows Server 2012 R2|5,0|
|Windows Server 2016|8.0|
|Windows Server 2019|9.0|
|Azure Stack HCI|9.0|

Pro virtuální počítače ve Windows Serveru 2012 R2, Windows Server 2016 a Windows Server 2019 aktualizujte všechny virtuální počítače na nejnovější verzi virtuálního počítače, která je na zdrojovém hardwaru nejdřív podporovaná, a teprve potom spusťte skript pro migraci Robocopy. Tím se zajistí, že všechny virtuální počítače jsou minimálně ve verzi 5,0 pro úspěšný import virtuálního počítače.

Pro virtuální počítače v systému Windows Server 2008 SP1, Windows Server 2008 R2-SP1 a Windows 2012 bude verze virtuálního počítače menší než verze 5,0. Tyto virtuální počítače také používají soubor. XML pro konfiguraci místo souboru. vcmx. Přímý import virtuálního počítače do Azure Stack HCI není podporován. V těchto případech máte dvě možnosti, jak je podrobně popsáno v [migraci starších virtuálních počítačů](#migrating-older-vms).

### <a name="updating-the-vm-version"></a>Aktualizuje se verze virtuálního počítače.

Následující příkazy se vztahují na Windows Server 2012 R2 a novější. Pomocí následujícího příkazu zobrazte všechny verze virtuálních počítačů na jednom serveru:

```powershell
Get-VM * | Format-Table Name,Version
```

Chcete-li zobrazit všechny verze virtuálních počítačů napříč všemi servery v clusteru:

```powershell
Get-VM –ComputerName (Get-ClusterNode)
```

Aktualizace všech virtuálních počítačů na nejnovější podporovanou verzi na všech serverech:

```powershell
Get-VM –ComputerName (Get-ClusterNode) | Update-VMVersion -Force
```

## <a name="rdma-recommendations"></a>Doporučení RDMA

Pokud používáte přímý přístup do paměti vzdáleného počítače (RDMA), nástroj Robocopy ho může využít ke kopírování virtuálních počítačů mezi clustery. Tady je několik doporučení pro použití RDMA:

- Pokud chcete použít nejrychlejší síťovou cestu mezi zdrojovým a cílovým clusterem, připojte oba clustery ke stejnému přepínači pro začátek racku. V případě cesty k síti úložiště to obvykle podporuje rychlost 10GbE/25GbE nebo vyšší a využívá RDMA.

- Pokud se adaptér RDMA nebo Standard liší mezi zdrojovým a cílovým clusterem (ROCE vs iWARP), nástroj Robocopy místo toho využívá protokol SMB přes protokol TCP/IP prostřednictvím nejrychlejší dostupné sítě. Obvykle se jedná o duální 10Gbe/25Gbe nebo vyšší rychlost pro East-West síť. tím se zajistí optimální způsob kopírování souborů VHDX virtuálního počítače mezi clustery.

- Chcete-li zajistit, aby nástroj Robocopy mohl využívat RDMA mezi clustery (Východově západní sítě), nakonfigurujte sítě pro úložiště RDMA, aby byly mezi zdrojovými a cílovými clustery směrovány.

## <a name="create-the-new-cluster"></a>Vytvoření nového clusteru

Předtím, než budete moci vytvořit cluster Azure Stack HCI, je nutné nainstalovat Azure Stack systému HCI na každém novém serveru, který bude v clusteru. Informace o tom, jak to provést, najdete v tématu [nasazení operačního systému Azure Stack HCI](operating-system.md).

K vytvoření nového clusteru použijte centrum pro správu Windows nebo Windows PowerShell. Podrobné informace o tom, jak to provést, najdete v tématu [Vytvoření clusteru Azure Stack HCI pomocí centra pro správu systému Windows](create-cluster.md) a [Vytvoření clusteru Azure Stack HCI pomocí prostředí Windows PowerShell](create-cluster-powershell.md).

> [!IMPORTANT]
> Názvy virtuálních přepínačů Hyper-V `VMSwitch` mezi clustery musí být stejné. Ujistěte se, že názvy virtuálních přepínačů vytvořené v cílovém clusteru odpovídají hodnotám používaným na zdrojovém clusteru na všech serverech. Před importem virtuálních počítačů ověřte názvy přepínačů stejné.

> [!NOTE]
> Než budete moct vytvořit nové virtuální počítače, musíte nejdřív zaregistrovat cluster Azure Stack HCI s Azure. Další informace najdete v tématu [registrace v Azure](register-with-azure.md).

## <a name="run-the-migration-script"></a>Spuštění skriptu migrace

Následující skript PowerShellu `Robocopy_Remote_Server_.ps1` pomocí nástroje Robocopy kopíruje soubory virtuálních počítačů a jejich závislé adresáře a metadata ze zdrojového do cílového clusteru. Tento skript se změnil z původního skriptu na TechNetu v: [Robocopy soubory na vzdálený server pomocí PowerShellu a nástroje Robocopy](https://gallery.technet.microsoft.com/scriptcenter/Robocoy-Files-to-Remote-bdfc5154).

Skript zkopíruje všechny soubory VHD, VHDX a VMCX virtuálních počítačů do cílového clusteru pro daný sdílený svazek clusteru (CSV). Jeden sdílený svazek clusteru se migruje současně.

Skript pro migraci se spouští místně na každém zdrojovém serveru a využívá výhod RDMA a rychlého přenosu v síti. Použijte následující postup:

1. Zajistěte, aby byl každý cílový uzel clusteru nastavený na vlastníka sdíleného svazku clusteru (CSV) pro cílový sdílený svazek clusteru.

1. Chcete-li určit umístění všech souborů VHD a VHDX pro virtuální počítače, které mají být zkopírovány, použijte následující rutinu. Projděte si `C:\vmpaths.txt` soubor a určete nejvyšší cestu ke zdrojovému souboru, ze které se má v nástroji Robocopy začít v kroku 4:

    ```powershell  
    Get-ChildItem -Path "C:\Clusterstorage\Volume01\*.vhd*" -Recurse > c:\vmpaths.txt
    ```

    > [!NOTE]
    > Pokud se soubory VHD a VHDX nacházejí v různých cestách na stejném svazku, budete muset skript migrace spustit pro každou jinou cestu, abyste je zkopírovali.

1. Změňte následující tři proměnné tak, aby odpovídaly cestě virtuálního počítače zdrojového clusteru s cílovou cestou k virtuálnímu počítači clusteru:

    - `$Dest_Server = "Node01"`
    - `$source  = "C:\Clusterstorage\Volume01"`
    - `$dest = "\\$Dest_Server\C$\Clusterstorage\Volume01"`

1. Na každém zdrojovém serveru se systémem Windows Server spusťte následující skript:

```powershell
<#
#===========================================================================  
# Script: Robocopy_Remote_Server_.ps1
#===========================================================================  
.DESCRIPTION:
Change the following variables to match your source cluster VM path with the destination cluster VM path. Then run this script on each source Cluster Node CSV owner and make sure the destination cluster node is set to the CSV owner for the destination CSV.

        Change $Dest_Server = "Node01"
        Change $source  = "C:\Clusterstorage\Volume01"
        Change $dest = "\\$Dest_Server\C$\Clusterstorage\Volume01"
#>

$Space       = Write-host ""
$Dest_Server = "Node01"
$source      = "C:\Clusterstorage\Volume01"
$dest        = "\\$Dest_Server\C$\Clusterstorage\Volume01"
$Logfile     = "c:\temp\Robocopy1-$date.txt"
$date        = Get-Date -UFormat "%Y%m%d"
$cmdArgs     = @("$source","$dest",$what,$options)  
$what        = @("/COPYALL")
$options     = @("/E","/MT:32","/R:0","/W:1","/NFL","/NDL","/LOG:$logfile","/xf")
 
## Get Start Time
$startDTM = (Get-Date)
 
$Dest_Server     = "Node01"
$TARGETDIR   = \\$Dest_Server\C$\Clusterstorage\Volume01
$Space
Clear
## Provide Information
Write-host ".....Copying Virtual Machines FROM $Source to $TARGETDIR ....................." -fore Green -back black
Write-Host "........................................." -Fore Green

## Kick off the copy with options defined  
robocopy @cmdArgs
 
## Get End Time
$endDTM = (Get-Date)
 
## Echo Time elapsed
$Time = "Elapsed Time: = $(($endDTM-$startDTM).totalminutes) minutes"  
## Provide time it took
Write-host ""
Write-host " Copy Virtual Machines to $Dest_Server has been completed......" -fore Green -back black
Write-host " Copy Virtual Machines to $Dest_Server took $Time        ......" -fore Cyan
```

## <a name="import-the-vms"></a>Import virtuálních počítačů

Osvědčeným postupem je vytvořit alespoň jeden sdílený svazek clusteru (CSV) na jeden uzel clusteru a umožnit tak rovnoměrné vyvážení virtuálních počítačů pro každého vlastníka CSV, aby se zvýšila odolnost, výkon a škálování úloh virtuálních počítačů. Ve výchozím nastavení se tento zůstatek automaticky objevuje každých pět minut a je potřeba ho zvážit při použití nástroje Robocopy mezi zdrojovým uzlem clusteru a cílovým uzlem, aby se zajistilo, že se zdrojové a cílové vlastníci sdílených svazků clusteru shodují, aby poskytovaly optimální cestu a rychlost přenosu.

Proveďte následující kroky v clusteru Azure Stack HCI pro import virtuálních počítačů, zpřístupněte je vysoce dostupné a spusťte je:

1. Spuštěním následující rutiny zobrazíte všechny uzly vlastníka sdíleného svazku clusteru:

    ```powershell
    Get-ClusterSharedVolume
    ```

1. Pro každý uzel serveru použijte `C:\Clusterstorage\Volume` příkaz a nastavte cestu pro všechny virtuální počítače – například `C:\Clusterstorage\volume01` .

1. Spusťte následující rutinu na každém uzlu vlastníka sdíleného svazku clusteru, abyste zobrazili cestu k souborům VMCX virtuálních počítačů na jeden svazek před importem virtuálního počítače. Upravte cestu tak, aby odpovídala vašemu prostředí:

    ```powershell
    Get-ChildItem -Path "C:\Clusterstorage\Volume01\*.vmcx" -Recurse
    ```

    > [!NOTE]
    > Windows Server 2012 R2 a starší virtuální počítače místo souboru VCMX používají soubor XML. Další informace najdete v části **migrace starších virtuálních počítačů**.

1. Spusťte následující rutinu pro každý uzel serveru pro import, registraci a zajištění vysoké dostupnosti virtuálních počítačů na každém uzlu vlastníka sdíleného svazku clusteru. Tím je zajištěna sudá distribuce virtuálních počítačů pro optimální přidělení procesoru a paměti:

    ```powershell
    Get-ChildItem -Path "C:\Clusterstorage\Volume01\*.vmcx" -Recurse | Import-VM -Register | Get-VM | Add-ClusterVirtualMachineRole
    ```

1. Spusťte každý cílový virtuální počítač na každém uzlu:

    ```powershell
    Start-VM -Name
    ```

1. Přihlaste se a ověřte, zda jsou všechny virtuální počítače spuštěné a zda jsou zde všechny vaše aplikace a data:

    ```powershell
    Get-VM -ComputerName Server01 | Where-Object {$_.State -eq 'Running'}
    ```

1. Aktualizujte své virtuální počítače na nejnovější verzi nástroje Azure Stack HCL, abyste mohli využít výhod všech výhod:

    ```powershell
    Get-VM | Update-VMVersion -Force
    ```

1. Po dokončení skriptu zkontrolujte v souboru protokolu Robocopy případné chyby, které jsou uvedené v seznamu, a ověřte, že se všechny virtuální počítače úspěšně zkopírovaly.

## <a name="migrating-older-vms"></a>Migrace starších virtuálních počítačů

Pokud máte Windows Server 2008 SP1, Windows Server 2008 R2-SP1, Windows Server 2012 nebo Windows Server 2012 R2, Tato část se vás týká. Pro zpracování těchto virtuálních počítačů máte dvě možnosti:

- Nejprve migrujte tyto virtuální počítače na Windows Server 2012 R2, Windows Server 2016 nebo Windows Server 2019, aktualizujte verzi virtuálního počítače a pak zahajte proces migrace.

- Pomocí nástroje Robocopy zkopírujte všechny virtuální pevné disky virtuálních počítačů do Azure Stack HCI. Pak vytvořte nové virtuální počítače a připojte zkopírované virtuální pevné disky k virtuálním počítačům v Azure Stack HCI. Tím se vynechá omezení verze virtuálních počítačů u těchto starších virtuálních počítačů.

Windows Server 2012 R2 a starší hostitelé Hyper-V používají pro svou konfiguraci virtuálních počítačů formát souboru XML, který se liší od formátu souboru VCMX používaného pro hostitele s Windows serverem 2016 a novějším Hyper-V. K kopírování těchto virtuálních počítačů do Azure Stack HCI se vyžaduje jiný příkaz Robocopy.

### <a name="option-1-staged-migration"></a>Možnost 1: migrace do fáze

Toto je dvoufázové migrace používané pro virtuální počítače hostované na Windows serveru 2008 SP1, Windows Server 2008 R2-SP a Windows Server 2012. Tady je proces, který používáte:

1. Vyhledejte umístění všech souborů VHD a VHDX virtuálních počítačů, které se mají zkopírovat, a pak zkontrolujte `vmpaths.txt` soubor, abyste zjistili nejvyšší cestu ke zdrojovému souboru, ze které se bude Robocopy spouštět. Použijte následující rutinu:

    ```powershell
    Get-ChildItem -Path "C:\Clusterstorage\Volume01\*.vhd*" -Recurse > c:\vmpaths.txt
    ```

1. K zkopírování virtuálních počítačů do Windows Serveru 2012 R2 použijte následující příklad příkazu Robocopy, který je určený v kroku 1:

    `Robocopy \\2012R2-Clus01\c$\clusterstorage\volume01\Hyper-V\ \\20H2-Clus01\c$\clusterstorage\volume01\Hyper-V\ /E /MT:32 /R:0 /w:1 /NFL /NDL /copyall /log:c:\log.txt /xf`

1. Ověřte, že název virtuálního přepínače ( `VMSwitch` ) používaný v clusteru Windows Server 2012 R2 je stejný jako název přepínače, který se používá ve zdroji windows 2008 R2 nebo Windows Server 2008 R2-SP1. Chcete-li zobrazit názvy přepínačů používaných na všech serverech v clusteru, použijte tento příkaz:

     ```powershell
    Get-VMSwitch -CimSession $Servers | Select-Object Name
    ```

    Podle potřeby přejmenujte název přepínače v systému Windows Server 20212 R2. Pokud chcete přejmenovat název přepínače na všech serverech v clusteru, použijte tento příkaz:

    ```powershell
    Invoke-Command -ComputerName $Servers -ScriptBlock {rename-VMSwitch -Name $using:vSwitcholdName -NewName $using:vSwitchnewname}
    ```

1. Zkopírování a import virtuálních počítačů do systému Windows Server 2012 R2:

     ```powershell
    Get-ChildItem -Path "c:\clusterstorage\volume01\Hyper-V\*.xml"-Recurse
    ```

    ```powershell
    Get-ChildItem -Path "c:\clusterstorage\volume01\image\*.xml" -Recurse    | Import-VM -Register | Get-VM | Add-ClusterVirtualMachineRole  
    ```

1. V systému Windows Server 2012 R2 aktualizujte verzi virtuálního počítače na 5,0 pro všechny virtuální počítače:

    ```powershell
    Get-VM | Update-VMVersion -Force
    ```

1. [Spuštěním skriptu migrace](#run-the-migration-script) zkopírujte virtuální počítače do Azure Stack HCI.

1. Postupujte podle kroků v části [Import virtuálních počítačů](#import-the-vms), nahraďte krok 3 a krok 4 následujícími kroky pro zpracování souborů XML a import virtuálních počítačů do Azure Stack HCI:

    ```powershell
    Get-ChildItem -Path "c:\clusterstorage\volume01\Hyper-V\*.xml"-Recurse
    ```

    ```powershell
    Get-ChildItem -Path "c:\clusterstorage\volume01\image\*.xml" -Recurse    | Import-VM -Register | Get-VM | Add-ClusterVirtualMachineRole  
    ```

1. Dokončete zbývající kroky v části [Import virtuálních počítačů](#import-the-vms).

### <a name="option-2-direct-vhd-copy"></a>Možnost 2: Přímá kopie VHD

Tato metoda pomocí nástroje Robocopy kopíruje virtuální pevné disky virtuálních počítačů hostované v systému Windows 2008 SP1, Windows 2008 R2-SP1 a Windows 2012 pro Azure Stack HCL. Tím se obchází minimální podporovaná omezení verzí virtuálních počítačů pro tyto starší virtuální počítače. Tuto možnost doporučujeme u virtuálních počítačů hostovaných v systémech Windows Server 2008 SP1 a Windows Server 2008 R2-SP1.

Virtuální počítače hostované v systémech Windows 2008 SP1 a Windows 2008 R2-SP1 podporují pouze virtuální počítače generace 1 s virtuálními pevnými disky 1. generace. V takovém případě je potřeba vytvořit odpovídající virtuální počítače 1. generace na Azure Stack HCI, aby se zkopírované virtuální pevné disky mohly připojit k novým virtuálním počítačům. Všimněte si, že tyto virtuální pevné disky nelze upgradovat na virtuální pevné disky 2. generace.

> [!NOTE]
> Windows Server 2012 podporuje virtuální počítače generace 1 i 2. generace.

Tady je proces, který používáte:

1. K zkopírování virtuálních pevných disků přímo do Azure Stack HCI použijte příklad Robocopy:

    `Robocopy \\2012R2-Clus01\c$\clusterstorage\volume01\Hyper-V\ \\20H2-Clus01\c$\clusterstorage\volume01\Hyper-V\ /E /MT:32 /R:0 /w:1 /NFL /NDL /copyall /log:c:\log.txt /xf`

1. Vytvořte nové virtuální počítače 1. generace. Podrobné informace o tom, jak to udělat, najdete v tématu [Správa virtuálních počítačů](https://docs.microsoft.com/azure-stack/hci/manage/vm).

1. Připojte zkopírované soubory VHD k novým virtuálním počítačům. Podrobné informace najdete v tématu [Správa virtuálních pevných disků (VHD)](https://docs.microsoft.com/windows-server/storage/disk-management/manage-virtual-hard-disks) .

V takovém případě podporují následující hostované operační systémy Windows Server virtuální počítače generace 2:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012 R2
- Windows Server 2012
- Windows 10
- 64 bitové verze Windows 8.1 (64 bitů)
- 64 bitové verze systému Windows 8 (64 bitů)
- Linux (viz [podporované virtuální počítače se systémem Linux a FreeBSD](https://docs.microsoft.com/windows-server/virtualization/hyper-v/Supported-Linux-and-FreeBSD-virtual-machines-for-Hyper-V-on-Windows))

## <a name="next-steps"></a>Další kroky

- Po migraci ověřte cluster. Přečtěte si téma [ověření Azure Stack clusteru HCI](validate.md).

- Pokud chcete migrovat Azure Stack místně pomocí stejného hardwaru, přečtěte si téma [migrace na Azure Stack HCI na stejném hardwaru](migrate-cluster-same-hardware.md).