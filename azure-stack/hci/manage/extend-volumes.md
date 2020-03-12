---
title: Rozšiřování svazků v Azure Stack HCI
description: Postup změny velikosti svazků v Azure Stack HCI pomocí centra pro správu Windows a PowerShellu
author: khdownie
ms.author: v-kedow
ms.topic: article
ms.date: 03/10/2020
ms.openlocfilehash: 703931b0dccb533b2b924847eb3302f0efa46d1a
ms.sourcegitcommit: 900332596d0bb473d82b1d1a28c3fe3aa6522add
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2020
ms.locfileid: "79089289"
---
# <a name="extending-volumes-in-storage-spaces-direct"></a>Rozšíření svazků v Prostory úložiště s přímým přístupem
> Platí pro: Windows Server 2019

Toto téma poskytuje pokyny pro změnu velikosti svazků v [prostory úložiště s přímým přístupemm](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) clusteru pomocí centra pro správu systému Windows.

> [!WARNING]
> **Nepodporováno: Změna velikosti podkladového úložiště používaného Prostory úložiště s přímým přístupem.** Pokud používáte Prostory úložiště s přímým přístupem v prostředí virtualizovaného úložiště, včetně v Azure, změna velikosti nebo změny vlastností úložných zařízení používaných virtuálními počítači není podporované a způsobí, že data nebudou přístupná. Místo toho postupujte podle pokynů v části [Přidání serverů nebo jednotek](/windows-server/storage/storage-spaces/add-nodes) a přidejte další kapacitu před rozšířením svazků.

Podívejte se na rychlé video o tom, jak změnit velikost svazku.

> [!VIDEO https://www.youtube-nocookie.com/embed/hqyBzipBoTI]

## <a name="extending-volumes-using-windows-admin-center"></a>Rozšíření svazků pomocí centra pro správu Windows

1. V centru pro správu Windows se připojte ke clusteru Prostory úložiště s přímým přístupem a potom v podokně **nástroje** vyberte **svazky** .
2. Na stránce **svazky** vyberte kartu **inventář** a potom vyberte svazek, u kterého chcete změnit velikost.

    Na stránce s podrobnostmi o svazku je uvedena kapacita úložiště pro daný svazek. Můžete také otevřít stránku podrobností o svazcích přímo z řídicího panelu. Na řídicím panelu v podokně výstrahy vyberte výstrahu, která vás upozorní na to, jestli na svazku není dostatek úložné kapacity, a pak vyberte **Přejít na svazek**.

4. V horní části stránky s podrobnostmi o svazcích vyberte **změnit velikost**.
5. Zadejte novou větší velikost a pak vyberte **změnit velikost**.

    Na stránce s podrobnostmi o svazcích je uvedena větší kapacita úložiště pro daný svazek a upozornění na řídicím panelu je vymazáno.

## <a name="extending-volumes-using-powershell"></a>Rozšíření svazků pomocí prostředí PowerShell

### <a name="capacity-in-the-storage-pool"></a>Kapacita ve fondu úložiště

Než začnete měnit velikost svazku, ujistěte se, že máte ve fondu úložiště dostatečnou kapacitu, aby vyhovovala novému, většímu množství. Například při změně velikosti trojrozměrného zrcadlového svazku z 1 TB na 2 TB budou jeho nároky růst z 3 TB na 6 TB. Aby se změna změnila na úspěch, budete potřebovat minimálně (6-3) = 3 TB dostupné kapacity ve fondu úložiště.

### <a name="familiarity-with-volumes-in-storage-spaces"></a>Znalost svazků v prostorech úložiště

V Prostory úložiště s přímým přístupem se každý svazek skládá z několika skládaných objektů: sdílený svazek clusteru (CSV), což je svazek. oddíl; disk, což je virtuální disk; a jednu nebo více úrovní úložiště (Pokud je k dispozici). Chcete-li změnit velikost svazku, budete muset změnit velikost několika těchto objektů.

![svazky v rozhraní SMAPI](media/extend-volumes/volumes-in-smapi.png)

Pokud se s nimi chcete seznámit, zkuste spustit **příkaz Get –** s odpovídajícím podstatným účelem v prostředí PowerShell.

Příklad:

```PowerShell
Get-VirtualDisk
```

Pokud chcete sledovat přidružení mezi objekty v zásobníku, přesměrujte jednu rutinu **Get-** rutinu na další.

Tady je příklad, jak získat z virtuálního disku až po jeho svazek:

```PowerShell
Get-VirtualDisk <FriendlyName> | Get-Disk | Get-Partition | Get-Volume 
```

### <a name="step-1--resize-the-virtual-disk"></a>Krok 1 – Změna velikosti virtuálního disku

Virtuální disk může používat vrstvy úložiště, nebo ne, v závislosti na tom, jak byl vytvořen.

Pro kontrolu spusťte následující rutinu:

```PowerShell
Get-VirtualDisk <FriendlyName> | Get-StorageTier 
```

Pokud rutina nevrátí žádnou hodnotu, virtuální disk nepoužívá vrstvy úložiště.

#### <a name="no-storage-tiers"></a>Žádné vrstvy úložiště

Pokud virtuální disk nemá žádné vrstvy úložiště, můžete změnit jeho velikost přímo pomocí rutiny **Resize-VirtualDisk** .

Zadejte novou velikost v parametru **-Size** .

```PowerShell
Get-VirtualDisk <FriendlyName> | Resize-VirtualDisk -Size <Size>
```

Při změně velikosti **VirtualDisk**se **disk** automaticky a zároveň změní jeho velikost.

![Změna velikosti – VirtualDisk](media/extend-volumes/Resize-VirtualDisk.gif)

#### <a name="with-storage-tiers"></a>S vrstvami úložiště

Pokud virtuální disk používá vrstvy úložiště, můžete změnit velikost jednotlivých vrstev samostatně pomocí rutiny **Resize-StorageTier** .

Pomocí přidružení z virtuálního disku Získejte názvy vrstev úložiště.

```PowerShell
Get-VirtualDisk <FriendlyName> | Get-StorageTier | Select FriendlyName
```

Pak pro každou vrstvu zadejte novou velikost v parametru **-Size** .

```PowerShell
Get-StorageTier <FriendlyName> | Resize-StorageTier -Size <Size>
```

> [!TIP]
> Pokud jsou vaše úrovně různými typy fyzických médií (například na discích **= SSD** a na discích **= HDD**), musíte zajistit, abyste měli jistotu, že máte dostatečnou kapacitu každého typu média ve fondu úložiště, abyste mohli přizpůsobit nové, větší nároky na jednotlivé úrovně.

Když změníte velikost **StorageTier**(y), **VirtualDisk** a **disk** se dodrží automaticky a změní se také velikost.

![Změna velikosti – StorageTier](media/extend-volumes/Resize-StorageTier.gif)

### <a name="step-2--resize-the-partition"></a>Krok 2 – Změna velikosti oddílu

Dále změňte velikost oddílu pomocí rutiny **změnit velikost oddílu** . Očekává se, že virtuální disk bude mít dva oddíly: první je vyhrazená a neměla by být upravena. ta, kterou potřebujete změnit, má **PartitionNumber = 2** a **Type = Basic**.

Zadejte novou velikost v parametru **-Size** . Doporučujeme použít maximální podporovanou velikost, jak je znázorněno níže.

```PowerShell
# Choose virtual disk
$VirtualDisk = Get-VirtualDisk <FriendlyName>

# Get its partition
$Partition = $VirtualDisk | Get-Disk | Get-Partition | Where PartitionNumber -Eq 2

# Resize to its maximum supported size 
$Partition | Resize-Partition -Size ($Partition | Get-PartitionSupportedSize).SizeMax
```

Když změníte velikost **oddílu**, **svazek** a **ClusterSharedVolume** se automaticky dodrží a změní se také velikost.

![Změna velikosti – oddíl](media/extend-volumes/Resize-Partition.gif)

A to je vše!

> [!TIP]
> Spuštěním **Get-Volume**můžete ověřit, jestli má svazek novou velikost.

## <a name="next-steps"></a>Další kroky

Podrobné pokyny pro další důležité úlohy správy úložiště najdete tady:

- [Plánování svazků v Prostory úložiště s přímým přístupem](/windows-server/storage/storage-spaces/plan-volumes)
- [Vytváření svazků v Prostory úložiště s přímým přístupem](/windows-server/storage/storage-spaces/create-volumes)
- [Odstraňování svazků v Prostory úložiště s přímým přístupem](/windows-server/storage/storage-spaces/delete-volumes)