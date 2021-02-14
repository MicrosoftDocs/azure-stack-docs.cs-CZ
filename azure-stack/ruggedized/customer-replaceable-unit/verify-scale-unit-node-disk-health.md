---
title: Ověřit stav disku uzlu škálování jednotky
description: Zjistěte, jak ověřit stav disku uzlu škálování jednotky.
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 8d872a11ce0312e0e1624a310606190da90c48a2
ms.sourcegitcommit: 5ea0e915f24c8bcddbcaf8268e3c963aa8877c9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100487965"
---
# <a name="verifying-scale-unit-node-disk-health"></a>Ověřuje se stav disku uzlu jednotky škálování

1.  Připojte se k privilegovanému koncovému bodu (PEP).

    1.  Pokyny pro připojení k PEP najdete v tématu přístup k pracovní stanici s privilegovaným přístupem a privilegovaným koncovým bodem.

    1.  Po připojení zadejte relaci PEP, `Enter-PSSession -Session $pep` .

2.  Získejte stav virtuálního disku.

    1.  Spusťte `Get-VirtualDisk -cimsession "S-Cluster"` pro ověření stavu virtuálního disku.

        Pokud systém nevrátí **provozním** **OK** a **funkčnosti** **v pořádku**, počkejte pár minut a pak znovu spusťte příkaz.
        
        ![Snímek obrazovky zobrazující, že prostředí Windows PowerShell se zvýrazněnými sloupci ' OperationsStatus ' a ' funkčnosti '.](media/image-57.png)
        
    1.  Spusťte `Get-VirtualDisk -cimsession "S-Cluster" | Get-StorageJob` , chcete-li ověřit, zda jsou všechny spuštěné úlohy úložiště dokončeny.
    
    1.  Ověřte, že nejsou vraceny žádné výsledky. Pokud jsou stále spuštěné úlohy, jak ukazuje **JobState**, nebo jsou všechny úlohy označeny jako úplné, počkejte dalších 10 minut a znovu spusťte stejný příkaz. Konečný stav by neměl být uveden v seznamu žádné úlohy.
    
    1.  V případě potřeby najdete další kroky pro ověření stavu úložiště v části [Kontrola stavu opravy virtuálního disku pomocí centra Azure Stack PowerShell](../../operator/azure-stack-replace-disk.md#check-the-status-of-virtual-disk-repair-using-azure-stack-hub-powershell).
        
