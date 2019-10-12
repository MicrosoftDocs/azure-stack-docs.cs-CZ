---
title: Nahrazení fyzického disku v Azure Stack | Microsoft Docs
description: Popisuje proces, jak nahradit fyzický disk v Azure Stack.
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
ms.date: 10/10/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 06/04/2019
ms.openlocfilehash: 5da479853487dfd93467bd1413159d6e602b93c6
ms.sourcegitcommit: a6d47164c13f651c54ea0986d825e637e1f77018
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72277669"
---
# <a name="replace-a-physical-disk-in-azure-stack"></a>Nahrazení fyzického disku v Azure Stack

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Tento článek popisuje obecný proces nahrazení fyzického disku v Azure Stack. Pokud fyzický disk přestane fungovat, měli byste ho co nejdříve nahradit.

Tento postup můžete použít pro integrované systémy a pro nasazení vývojových sad, která mají disky Hot-swaped.

Skutečné kroky pro nahrazení disku se budou lišit v závislosti na dodavateli hardwaru OEM (Original Equipment Manufacturer). Podrobné pokyny, které jsou specifické pro váš systém, najdete v dokumentaci k umístění jednotky v poli vašeho dodavatele (FRU).

## <a name="review-disk-alert-information"></a>Kontrola informací o výstrahách disku
Když dojde k chybě disku, obdržíte výstrahu, která oznamuje, že došlo ke ztrátě připojení na fyzický disk.

![Upozornění ukazující ztracené připojení na fyzický disk](media/azure-stack-replace-disk/DiskAlert.png)

Pokud otevřete výstrahu, popis výstrahy obsahuje uzel jednotka škálování a přesné umístění fyzického slotu pro disk, který je nutné nahradit. Azure Stack dále pomůže identifikovat disk, který selhal, pomocí možností indikátoru LED.

## <a name="replace-the-disk"></a>Výměna disku

Použijte pokyny pro vlastní nahrazení disku od dodavatele hardwaru OEM.

> [!note]
> V jednom okamžiku nahraďte disky pro jeden uzel jednotky škálování. Před přechodem na uzel jednotky škálování na další jednotku počkejte, než se dokončí úlohy opravy virtuálního disku.

Aby nedocházelo k použití nepodporovaného disku v integrovaném systému, systém blokuje disky, které váš dodavatel nepodporuje. Pokud se pokusíte použít nepodporovaný disk, zobrazí nová výstraha, že disk byl umístěn do karantény z důvodu nepodporovaného modelu nebo firmwaru.

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

    ![Stav Azure Stackch svazků](media/azure-stack-replace-disk/get-azure-stack-volumes-health.png)

4. Ověří Azure Stack stav systému. Pokyny najdete v tématu [ověření stavu systému Azure Stack](azure-stack-diagnostic-test.md).
5. Volitelně můžete spuštěním následujícího příkazu ověřit stav nahrazeného fyzického disku.

```powershell  
$scaleunit=Get-AzsScaleUnit
$StorageSubSystem=Get-AzsStorageSubSystem -ScaleUnit $scaleunit.Name

Get-AzsDrive -StorageSubSystem $StorageSubSystem.Name -ScaleUnit $scaleunit.name | Sort-Object StorageNode,MediaType,PhysicalLocation | Format-Table Storagenode, Healthstatus, PhysicalLocation, Model, MediaType,  CapacityGB, CanPool, CannotPoolReason
```

![Fyzické disky se nahradily v Azure Stack.](media/azure-stack-replace-disk/check-replaced-physical-disks-azure-stack.png)

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
