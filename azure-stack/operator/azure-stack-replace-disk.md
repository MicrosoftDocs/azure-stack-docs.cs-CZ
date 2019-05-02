---
title: Nahraďte fyzický disk ve službě Azure Stack | Dokumentace Microsoftu
description: Popisuje proces pro nahrazení fyzický disk ve službě Azure Stack.
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
ms.date: 01/22/2019
ms.author: mabrigg
ms.lastreviewed: 01/22/2019
ms.openlocfilehash: 893f3ef3f6b7d98d781d05982d243d44ada1411e
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2019
ms.locfileid: "64984738"
---
# <a name="replace-a-physical-disk-in-azure-stack"></a>Nahraďte fyzický disk ve službě Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Tento článek popisuje obecný postup k nahrazení fyzický disk ve službě Azure Stack. Pokud fyzický disk selže, by ho nahradit co nejdříve.

Tento postup můžete použít pro integrované systémy a pro nasazení development kit, které mají disky vyměnitelné za provozu.

Výměna skutečné disku, které kroky se budou lišit podle dodavatele hardwaru, výrobce OEM (OEM). Dokumentaci od dodavatele pole vyměnitelná jednotka (FRU) podrobné pokyny, které jsou specifické pro váš systém.

## <a name="review-disk-alert-information"></a>Projděte si informace o výstrahách disku
Pokud některý disk selže, obdržíte výstrahu, která říká, že připojení bylo ztraceno fyzický disk.

 ![Fyzický disk ke ztrátě připojení k zobrazení výstrah](media/azure-stack-replace-disk/DiskAlert.png)

Pokud otevřete výstrahy, popis výstrahy obsahuje uzel jednotek škálování a slotu přesné fyzické umístění disku, který je třeba nahradit. Další Azure Stack pomáhá identifikovat pomocí možnosti indikátor LED selhání disku.

## <a name="replace-the-disk"></a>Výměna disku

Postupujte podle pokynů výrobce OEM dodavatele hardwaru FRU určena k nahrazení skutečné disku.

> [!note]
> Nahraďte disky pro jeden uzel jednotek škálování v čase. Počkejte na dokončení před přechodem na další uzel jednotek škálování úloh oprava virtuálního disku

Zabránit používání nepodporované disku v integrovaný systém, bude systém blokovat disky, které nejsou podporovány od dodavatele. Pokud se pokusíte použít nepodporovaný disk, nová výstraha se říká, že disk má byl umístěn do karantény z důvodu nepodporované modelu, nebo firmware.

Po vyměňujete disk, Azure Stack automaticky zjistí nový disk a spustí proces opravy virtuálního disku.
 
## <a name="check-the-status-of-virtual-disk-repair"></a>Kontrola stavu oprava virtuálního disku
 
 Po vyměňujete disk, můžete sledovat stav virtuálního disku a opravit průběh úlohy pomocí privilegovaných koncový bod. Z libovolného počítače, který má síťové připojení ke koncovému bodu privileged postupujte podle těchto kroků.

1. Otevřete relaci Windows Powershellu a připojte se k privilegovaným koncový bod.
    ```powershell
        $cred = Get-Credential
        Enter-PSSession -ComputerName <IP_address_of_ERCS>`
          -ConfigurationName PrivilegedEndpoint -Credential $cred
    ``` 
  
2. Spuštěním následujícího příkazu zobrazíte stav virtuálního disku:
    ```powershell
        Get-VirtualDisk -CimSession s-cluster
    ```
   ![Výstup prostředí PowerShell Get-VirtualDisk příkazu](media/azure-stack-replace-disk/GetVirtualDiskOutput.png)

3. Spuštěním následujícího příkazu zobrazíte aktuální stav úlohy úložiště:
    ```powershell
        Get-VirtualDisk -CimSession s-cluster | Get-StorageJob
    ```
      ![Výstup prostředí PowerShell Get-StorageJob příkazu](media/azure-stack-replace-disk/GetStorageJobOutput.png)

## <a name="troubleshoot-virtual-disk-repair"></a>Řešení potíží s oprava virtuálního disku

Pokud oprava virtuálního disku úlohy se zobrazí zablokované, spuštěním následujícího příkazu restartujete úlohu:
  ```powershell
        Get-VirtualDisk -CimSession s-cluster | Repair-VirtualDisk
  ``` 
