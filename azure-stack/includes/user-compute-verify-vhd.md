---
author: mattbriggs
ms.author: mabrigg
ms.service: azure-stack
ms.topic: include
ms.date: 2/1/2021
ms.reviewer: thoroet
ms.lastreviewed: 08/04/2020
ms.openlocfilehash: a1659bb40e3917aa548a6df5b031562b6913802a
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99245732"
---
Před nahráním virtuálního pevného disku musíte ověřit, že virtuální pevný disk splňuje požadavky. Virtuální pevné disky, které nesplňují požadavky, se nepodaří načíst do centra Azure Stack.

1. Budete používat moduly prostředí PowerShell, které byly nalezeny s technologií Hyper-V. Aktivace technologie Hyper-V nainstaluje podpůrné moduly prostředí PowerShell. Můžete ověřit, že máte modul, a to tak, že otevřete PowerShell s výzvou se zvýšenými oprávněními a spustíte tuto rutinu:

    ```powershell  
    Get-Command -Module hyper-v
    ```

    Pokud nemáte příkazy technologie Hyper-V, přečtěte si téma [práce s technologií Hyper-v a prostředím Windows PowerShell](/virtualization/hyper-v-on-windows/quick-start/try-hyper-v-powershell). 

2. Získejte na svém počítači cestu k virtuálnímu pevnému disku. Spusťte následující rutinu:

    ```powershell  
    get-vhd <path-to-your-VHD>
    ```

    Rutina vrátí objekt VHD a zobrazí atributy, jako například:
    
    ```powershell  
    ComputerName            : YOURMACHINENAME
    Path                    : <path-to-your-VHD>
    VhdFormat               : VHD
    VhdType                 : Fixed
    FileSize                : 68719477248
    Size                    : 68719476736
    MinimumSize             : 32212254720
    LogicalSectorSize       : 512
    PhysicalSectorSize      : 512
    BlockSize               : 0
    ParentPath              :
    DiskIdentifier          : 3C084D21-652A-4C0E-B2D1-63A8E8E64C0C
    FragmentationPercentage : 0
    Alignment               : 1
    Attached                : False
    DiskNumber              :
    IsPMEMCompatible        : False
    AddressAbstractionType  : None
    Number                  :
    ```

3. V případě objektu VHD ověřte, zda splňuje požadavky centra Azure Stack.
    - [Virtuální pevný disk je pevného typu.](#vhd-is-of-fixed-type)
    - [Virtuální pevný disk má minimální virtuální velikost alespoň 20 MB.](#vhd-has-minimum-virtual-size-of-at-least-20-mb)
    - [Virtuální pevný disk je zarovnaný.](#vhd-is-aligned)
    - [Délka objektu BLOB VHD = virtuální velikost + délka zápatí VHD (512).](#vhd-blob-length) 
    
    Kromě toho rozbočovač Azure Stack podporuje jenom image z [generace jednoho (1) virtuálních počítačů.](#generation-one-vms)

4. Pokud váš virtuální pevný disk není kompatibilní s Azure Stack hub, budete se muset vrátit ke zdrojové imagi a technologii Hyper-V, vytvořit virtuální pevný disk, který splňuje požadavky, a nahrát ho. Chcete-li minimalizovat možné poškození procesu nahrávání, použijte AzCopy.

### <a name="how-to-fix-your-vhd"></a>Jak opravit VHD

Kvůli kompatibilitě virtuálního pevného disku s Azure Stack hub je potřeba splnit následující požadavky.

#### <a name="vhd-is-of-fixed-type"></a>VHD má pevný typ.
**Identifikace**: `get-vhd` k získání objektu VHD použijte rutinu.  
**Oprava**: soubor VHDX můžete převést na VHD, převést dynamicky se zvětšující disk na disk s pevnou velikostí, ale nemůžete změnit generaci virtuálního počítače.
K převedení disku použijte [Správce technologie Hyper-V nebo PowerShell](/azure/virtual-machines/windows/prepare-for-upload-vhd-image#use-hyper-v-manager-to-convert-the-disk) .

### <a name="vhd-has-minimum-virtual-size-of-at-least-20-mb"></a>Virtuální pevný disk má minimální virtuální velikost alespoň 20 MB.
**Identifikace**: `get-vhd` k získání objektu VHD použijte rutinu.  
**Oprava**: ke změně velikosti disku použijte [Správce technologie Hyper-V nebo PowerShell](/azure/virtual-machines/windows/prepare-for-upload-vhd-image#use-hyper-v-manager-to-resize-the-disk) . 

### <a name="vhd-is-aligned"></a>Virtuální pevný disk je zarovnaný
**Identifikace**: `get-vhd` k získání objektu VHD použijte rutinu.  
**Oprava**: virtuální velikost musí být násobkem jedné (1) MB. 

Disky musí mít virtuální velikost zarovnaná na 1 MiB. Pokud je váš virtuální pevný disk zlomek 1 MiB, budete muset změnit velikost disku na násobek 1 MiB. Disky, které jsou zlomky souboru MiB, způsobují chyby při vytváření imagí z nahraného virtuálního pevného disku. Pokud chcete ověřit velikost, můžete použít rutinu Get-VHD PowerShellu k zobrazení "size", která musí být násobkem 1 MiB v Azure a "velikost souboru", která bude odpovídat velikosti a 512 bajtů pro zápatí VHD.

Ke změně velikosti disku použijte [Správce technologie Hyper-V nebo PowerShell](/azure/virtual-machines/windows/prepare-for-upload-vhd-image#use-hyper-v-manager-to-resize-the-disk) . 


### <a name="vhd-blob-length"></a>Délka objektu BLOB VHD
**Identifikace**: použití `get-vhd` rutiny k zobrazení `Size`   
**Oprava**: délka objektu BLOB VHD = virtuální velikost + délka zápatí vhd (512). Malé zápatí na konci objektu BLOB popisuje vlastnosti VHD. `Size` musí se jednat o násobek 1 MiB v Azure a `FileSize` , který bude `Size` pro zápatí VHD roven + 512 bajtů.

Ke změně velikosti disku použijte [Správce technologie Hyper-V nebo PowerShell](/azure/virtual-machines/windows/prepare-for-upload-vhd-image#use-hyper-v-manager-to-resize-the-disk) . 

### <a name="generation-one-vms"></a>Generace jednoho virtuálního počítače
**Identifikace**: Chcete-li ověřit, zda je váš virtuální počítač 1. generace, použijte rutinu `Get-VM | Format-Table Name, Generation` .  
**Oprava**: v hypervisoru (Hyper-V) budete muset virtuální počítač znovu vytvořit.