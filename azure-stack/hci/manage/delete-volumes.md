---
title: Odstranění svazků v Azure Stack HCI a Windows Server
description: Postup odstranění svazků v Azure Stack HCI a Windows serveru pomocí centra pro správu Windows a prostředí PowerShell.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 03/09/2021
ms.openlocfilehash: fdc7fd4ae102871d534d846dd653af077a75d49a
ms.sourcegitcommit: 02a4c34fb829e053016912a4fffcc51e32685425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102532476"
---
# <a name="deleting-volumes-in-azure-stack-hci-and-windows-server"></a>Odstraňování svazků v Azure Stack HCI a Windows Server

> Platí pro: Azure Stack HCI, verze 20H2; Windows Server 2019

V tomto tématu najdete pokyny k odstranění svazků pomocí centra pro správu Windows nebo PowerShellu.

## <a name="delete-volumes-with-windows-admin-center"></a>Odstranění svazků v centru pro správu Windows

1. V centru pro správu Windows se připojte ke clusteru a pak v levém podokně **nástroje** vyberte **svazky** .
2. Na stránce **svazky** vyberte kartu **inventář** a potom vyberte svazek, který chcete odstranit.
3. V horní části stránky s podrobnostmi o svazcích vyberte možnost **Odstranit**.
4. V dialogovém okně potvrzení zaškrtněte políčko pro potvrzení, že chcete svazek odstranit, a vyberte **Odstranit**.

   :::image type="content" source="media/delete-volumes/delete-volume.png" alt-text="Vyberte svazek, který chcete odstranit, vyberte Odstranit a pak potvrďte, že chcete vymazat všechna data ve svazku." lightbox="media/delete-volumes/delete-volume.png":::

## <a name="delete-volumes-with-powershell"></a>Odstranění svazků pomocí PowerShellu

Pomocí rutiny **Remove-VirtualDisk** odstraňte objekt **VirtualDisk** a vraťte prostor, který se používá pro fond úložiště, který zpřístupňuje objekt **VirtualDisk** .

Nejdřív na počítači pro správu spusťte PowerShell a spuštěním rutiny **Get-VirtualDisk** s parametrem **CimSession** , což je název clusteru nebo uzlu serveru, například *clustername.contoso.com*:

```PowerShell
Get-VirtualDisk -CimSession clustername.contoso.com
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

- [Plánování svazků](../concepts/plan-volumes.md)
- [Vytváření svazků](create-volumes.md)
- [Rozšiřování svazků](extend-volumes.md)