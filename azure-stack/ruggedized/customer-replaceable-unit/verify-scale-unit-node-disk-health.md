---
title: Ověřit stav disku uzlu škálování jednotky
description: Zjistěte, jak ověřit stav disku uzlu škálování jednotky.
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: b0befa0b2dd2d2acd3ad9f0470722a7135e6e974
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97867549"
---
# <a name="verifying-scale-unit-node-disk-health"></a>Ověřuje se stav disku uzlu jednotky škálování

1.  Připojte se k privilegovanému koncovému bodu (PEP).

    1.  Pokyny pro připojení k PEP najdete v tématu přístup k pracovní stanici s privilegovaným přístupem a privilegovaným koncovým bodem.

    1.  Po připojení zadejte relaci PEP, `Enter-PSSession -Session $pep` .

2.  Získejte stav virtuálního disku.

    1.  Spusťte `Get-VirtualDisk -cimsession "S-Cluster"` pro ověření stavu virtuálního disku.

        Pokud systém nevrátí **provozním** **OK** a **funkčnosti** **v pořádku**, počkejte pár minut a pak znovu spusťte příkaz.
        
        ![](media/image-57.png)
        
    1.  Spusťte `Get-VirtualDisk -cimsession "S-Cluster" | Get-StorageJob` , chcete-li ověřit, zda jsou všechny spuštěné úlohy úložiště dokončeny.
    
    1.  Ověřte, že nejsou vraceny žádné výsledky. Pokud jsou stále spuštěné úlohy, jak ukazuje **JobState**, nebo jsou všechny úlohy označeny jako úplné, počkejte dalších 10 minut a znovu spusťte stejný příkaz. Konečný stav by neměl být uveden v seznamu žádné úlohy.
    
    1.  V případě potřeby najdete další kroky pro ověření stavu úložiště v části [Kontrola stavu opravy virtuálního disku pomocí centra Azure Stack PowerShell](https://docs.microsoft.com/azure-stack/operator/azure-stack-replace-disk?view=azs-2002&check-the-status-of-virtual-disk-repair-using-azure-stack-hub-powershell).
        
