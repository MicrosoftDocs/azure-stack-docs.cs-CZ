---
title: Odstranění svazků v Azure Stack HCI
description: Postup odstranění svazků v Azure Stack HCI pomocí centra pro správu Windows a PowerShellu
author: khdownie
ms.author: v-kedow
ms.topic: article
ms.date: 03/17/2020
ms.openlocfilehash: cf556a9b6c130907e8607d8e5b9436b71756a3d4
ms.sourcegitcommit: 53efd12bf453378b6a4224949b60d6e90003063b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2020
ms.locfileid: "79511891"
---
# <a name="deleting-volumes-in-azure-stack-hci"></a>Odstraňování svazků v Azure Stack HCI

> Platí pro: Windows Server 2019

Toto téma poskytuje pokyny pro odstranění svazků v [prostory úložiště s přímým přístupemm](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) clusteru pomocí centra pro správu systému Windows.

Podívejte se na rychlé video o tom, jak odstranit svazek pomocí centra pro správu Windows.

> [!VIDEO https://www.youtube-nocookie.com/embed/DbjF8r2F6Jo]

## <a name="use-windows-admin-center-to-delete-a-volume"></a>Použití centra pro správu systému Windows k odstranění svazku

1. V centru pro správu Windows se připojte ke clusteru Prostory úložiště s přímým přístupem a potom v podokně **nástroje** vyberte **svazky** .
2. Na stránce **svazky** vyberte kartu **inventář** a potom vyberte svazek, který chcete odstranit.
3. V horní části stránky s podrobnostmi o svazcích vyberte možnost **Odstranit**.
4. V dialogovém okně potvrzení zaškrtněte políčko pro potvrzení, že chcete svazek odstranit, a vyberte **Odstranit**.

## <a name="delete-volumes-using-powershell"></a>Odstranění svazků pomocí PowerShellu

Pomocí rutiny **Remove-VirtualDisk** odstraňte svazky v prostory úložiště s přímým přístupem. Tato rutina slouží k odstranění objektu **VirtualDisk** a vrácení místa, které se použilo ve fondu úložiště, který zpřístupňuje objekt **VirtualDisk** .

Nejdřív na počítači pro správu spusťte PowerShell a spuštěním rutiny **Get-VirtualDisk** s parametrem **CimSession** , který je názvem prostory úložiště s přímým přístupem clusteru nebo uzlu serveru, například *clustername.Microsoft.com*: 

```PowerShell
Get-VirtualDisk -CimSession clustername.microsoft.com
```

Tím se vrátí seznam možných hodnot pro parametr **-FriendlyName** , který odpovídá názvům svazků v clusteru.

### <a name="example"></a>Příklad

Pokud chcete odstranit zrcadlený svazek s názvem *Volume1,* spusťte v PowerShellu následující příkaz:

```PowerShell
Remove-VirtualDisk -FriendlyName "Volume1"
```

Zobrazí se výzva k potvrzení, že chcete provést akci a vymazat všechna data, která svazek obsahuje. Vyberte Y nebo N.

   > [!WARNING]
   > Nejedná se o obnovitelné akce. Tento příklad trvale odstraní objekt **VirtualDisk** Volume.

## <a name="next-steps"></a>Další kroky

Podrobné pokyny pro další důležité úlohy správy úložiště najdete tady:

- [Plánování svazků v Prostory úložiště s přímým přístupem](../concepts/plan-volumes.md)
- [Vytváření svazků v Prostory úložiště s přímým přístupem](create-volumes.md)
- [Rozšíření svazků v Prostory úložiště s přímým přístupem](extend-volumes.md)