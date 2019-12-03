---
title: Nahrazení fyzického disku
titleSuffix: Azure Stack
description: Přečtěte si, jak nahradit fyzický disk v Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 449ae53e-b951-401a-b2c9-17fee2f491f1
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/02/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 12/02/2019
ms.openlocfilehash: 0700ca4caefbec2245f2303720a675aece6c21e6
ms.sourcegitcommit: fd7d43738f275f36dacfa0786697e7c44d405abb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2019
ms.locfileid: "74694819"
---
# <a name="replace-a-physical-disk-in-azure-stack"></a>Nahrazení fyzického disku v Azure Stack

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Tento článek popisuje obecný proces nahrazení fyzického disku v Azure Stack. Pokud selže fyzický disk, měli byste ho co nejdříve nahradit.

> [!Note]  
> Nahrazení fyzické datové **jednotky nevyžaduje,** aby uzel jednotky škálování byl přepnut do režimu údržby (vyprázdnění) předem. I po nahrazení fyzické jednotky uzel jednotky škálování není potřeba opravit pomocí portálu Azure Stack hub pro správu. Následující článek obsahuje další informace, pokud je nutná oprava, [nahrazuje hardwarovou součást v uzlu jednotky škálování Azure Stack](azure-stack-replace-component.md).

Tento postup můžete použít pro integrované systémy a pro nasazení Azure Stack Development Kit (ASDK), která mají disky připojitelných za chodu.

Konkrétní postup nahrazení disku se bude lišit v závislosti na vašem dodavateli hardwaru od výrobce OEM. Konkrétní podrobný postup pro váš systém najdete v dokumentaci k jednotce nahraditelné v terénu od vašeho dodavatele.

## <a name="review-disk-alert-information"></a>Kontrola informací o výstrahách disku
Když selže disk, obdržíte upozornění s informací, že došlo ke ztrátě připojení k fyzickému disku.

![Upozornění ukazující ztracené připojení na fyzický disk při správě Azure Stack](media/azure-stack-replace-disk/DiskAlert.png)

Pokud otevřete výstrahu, popis výstrahy obsahuje uzel jednotka škálování a přesné umístění fyzického slotu pro disk, který je nutné nahradit. Azure Stack dále pomůže identifikovat disk, který selhal, pomocí možností indikátoru LED.

## <a name="replace-the-physical-disk"></a>Nahrazení fyzického disku

Při vlastním nahrazování disku postupujte podle pokynů k jednotce nahraditelné v terénu od vašeho dodavatele hardwaru OEM.

> [!note]
> V jednom okamžiku nahraďte disky pro jeden uzel jednotky škálování. Než přejdete na uzel další jednotky škálování, počkejte, než se dokončí úlohy opravy virtuálního disku.

Aby nedocházelo k použití nepodporovaného disku v integrovaném systému, systém blokuje disky, které nejsou podporované vaším dodavatelem. Pokud se pokusíte použít nepodporovaný disk, zobrazí nová výstraha, že disk byl umístěn do karantény z důvodu nepodporovaného modelu nebo firmwaru.

Po nahrazení disku Azure Stack automaticky zjistí nový disk a spustí proces opravy virtuálního disku.

## <a name="check-the-status-of-virtual-disk-repair-using-azure-stack-powershell"></a>Ověření stavu opravy virtuálního disku pomocí Azure Stack PowerShellu

Po nahrazení disku můžete monitorovat stav virtuálního disku a průběh úlohy opravy pomocí Azure Stack PowerShellu.

1. Ověřte, že máte nainstalovaný Azure Stack PowerShell. Další informace najdete v tématu [instalace PowerShellu pro Azure Stack](azure-stack-powershell-install.md).
2. Připojte se k Azure Stack pomocí PowerShellu jako operátoru. Další informace najdete v tématu [připojení k Azure Stack pomocí PowerShellu jako operátoru](azure-stack-powershell-configure-admin.md).
3. Spusťte následující rutiny k ověření stavu virtuálního disku a stavu opravy:

    ```powershell  
    $scaleunit=Get-AzsScaleUnit
    $StorageSubSystem=Get-AzsStorageSubSystem -ScaleUnit $scaleunit.Name
    Get-AzsVolume -StorageSubSystem $StorageSubSystem.Name -ScaleUnit $scaleunit.name | Select-Object VolumeLabel, OperationalStatus, RepairStatus
    ```

    ![Stav Azure Stackch svazků v PowerShellu](media/azure-stack-replace-disk/get-azure-stack-volumes-health.png)

4. Ověří Azure Stack stav systému. Pokyny najdete v tématu [ověření stavu systému Azure Stack](azure-stack-diagnostic-test.md).
5. Volitelně můžete spuštěním následujícího příkazu ověřit stav nahrazeného fyzického disku.

```powershell  
$scaleunit=Get-AzsScaleUnit
$StorageSubSystem=Get-AzsStorageSubSystem -ScaleUnit $scaleunit.Name

Get-AzsDrive -StorageSubSystem $StorageSubSystem.Name -ScaleUnit $scaleunit.name | Sort-Object StorageNode,MediaType,PhysicalLocation | Format-Table Storagenode, Healthstatus, PhysicalLocation, Model, MediaType,  CapacityGB, CanPool, CannotPoolReason
```

![Fyzické disky v Azure Stack v prostředí PowerShell nahrazeny](media/azure-stack-replace-disk/check-replaced-physical-disks-azure-stack.png)

## <a name="check-the-status-of-virtual-disk-repair-using-the-privileged-endpoint"></a>Ověření stavu opravy virtuálního disku pomocí privilegovaného koncového bodu

Po nahrazení disku můžete monitorovat stav virtuálního disku a průběh úlohy opravy pomocí privilegovaného koncového bodu. Postupujte podle těchto kroků z libovolného počítače, který má síťové připojení k privilegovanému koncovému bodu.

1. Otevřete relaci prostředí Windows PowerShell a připojte se k privilegovanému koncovému bodu.
    ```powershell
        $cred = Get-Credential
        Enter-PSSession -ComputerName <IP_address_of_ERCS>`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
    ```
  
2. Spuštěním následujícího příkazu zobrazte stav virtuálního disku:
    ```powershell
        Get-VirtualDisk -CimSession s-cluster
    ```

   ![Výstup PowerShellu příkazu Get-VirtualDisk](media/azure-stack-replace-disk/GetVirtualDiskOutput.png)

3. Spusťte následující příkaz, který zobrazí aktuální stav úlohy úložiště:
    ```powershell
        Get-VirtualDisk -CimSession s-cluster | Get-StorageJob
    ```
      ![Výstup PowerShellu příkazu Get-StorageJob](media/azure-stack-replace-disk/GetStorageJobOutput.png)

4. Ověří stav Azure Stack systému. Pokyny najdete v tématu [ověření stavu systému Azure Stack](azure-stack-diagnostic-test.md).

## <a name="troubleshoot-virtual-disk-repair-using-the-privileged-endpoint"></a>Řešení potíží s opravou virtuálního disku pomocí privilegovaného koncového bodu

Pokud se zobrazí úloha opravy virtuálního disku zablokovaná, spusťte následující příkaz k restartování úlohy:
  ```powershell
        Get-VirtualDisk -CimSession s-cluster | Repair-VirtualDisk
  ```
