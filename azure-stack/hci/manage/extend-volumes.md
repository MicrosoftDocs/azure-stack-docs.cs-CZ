---
title: Rozbalení svazků v Azure Stack HCI
description: Postup rozbalení svazků v Azure Stack HCI pomocí centra pro správu Windows a prostředí PowerShell.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 03/04/2021
ms.openlocfilehash: 275cafad3f813b28e2b926ad2008bca777904e0e
ms.sourcegitcommit: f194f9ca4297864500e62d8658674a0625b29d1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102187329"
---
# <a name="expanding-volumes-in-azure-stack-hci"></a>Rozbalování svazků v Azure Stack HCI

> Platí pro: Azure Stack HCI, verze 20H2; Windows Server 2019

Toto téma poskytuje pokyny pro rozbalení svazků v clusteru pomocí centra pro správu Windows a PowerShellu.

> [!WARNING]
> **Nepodporováno: Změna velikosti podkladového úložiště používaného Prostory úložiště s přímým přístupem.** Pokud používáte Prostory úložiště s přímým přístupem v prostředí virtualizovaného úložiště, včetně v Azure, změna velikosti nebo změny vlastností úložných zařízení používaných virtuálními počítači není podporované a způsobí, že data nebudou přístupná. Místo toho postupujte podle pokynů v části [Přidání serverů nebo jednotek](/windows-server/storage/storage-spaces/add-nodes) a přidejte další kapacitu před rozbalením svazků.

## <a name="expand-volumes-using-windows-admin-center"></a>Rozbalení svazků pomocí centra pro správu Windows

1. V centru pro správu Windows se připojte k Azure Stack clusteru HCI a pak v podokně **nástroje** vyberte **svazky** .
2. Na stránce **svazky** vyberte kartu **inventář** a potom vyberte svazek, který chcete rozšířit.

    Na stránce s podrobnostmi o svazku je uvedena kapacita úložiště pro daný svazek. Můžete také otevřít stránku podrobností o svazcích přímo z řídicího panelu. Na řídicím panelu v podokně výstrahy vyberte výstrahu, která vás upozorní na to, jestli na svazku není dostatek úložné kapacity, a pak vyberte **Přejít na svazek**.

3. V horní části stránky s podrobnostmi o svazcích vyberte **Rozbalit**.
4. Zadejte novou větší velikost a pak vyberte **Rozbalit**.

    Na stránce s podrobnostmi o svazcích je uvedena větší kapacita úložiště pro daný svazek a upozornění na řídicím panelu je vymazáno.

## <a name="expand-volumes-using-powershell"></a>Rozbalení svazků pomocí PowerShellu

### <a name="capacity-in-the-storage-pool"></a>Kapacita ve fondu úložiště

Než rozbalíte svazek, ujistěte se, že máte dostatek kapacity ve fondu úložiště, aby se vešlo na nové, větší nároky. Například při rozšiřování trojrozměrného zrcadlového svazku z 1 TB do 2 TB by jeho nároky vzrostly ze 3 TB na 6 TB. Aby bylo rozšíření úspěšné, budete potřebovat minimálně (6-3) = 3 TB dostupné kapacity ve fondu úložiště.

### <a name="familiarity-with-volumes-in-storage-spaces"></a>Znalost svazků v prostorech úložiště

V Prostory úložiště s přímým přístupem se každý svazek skládá z několika skládaných objektů: sdílený svazek clusteru (CSV), což je svazek. oddíl; disk, což je virtuální disk; a jednu nebo více úrovní úložiště (Pokud je k dispozici). Chcete-li změnit velikost svazku, budete muset změnit velikost několika těchto objektů.

![Diagram znázorňuje vrstvy svazku, včetně svazku horizontálních oddílů clusteru, svazku, oddílu, disku, virtuálního disku a vrstev úložiště.](media/extend-volumes/volumes-in-smapi.png)

Pokud se s nimi chcete seznámit, zkuste spustit **příkaz Get –** s odpovídajícím podstatným účelem v prostředí PowerShell.

Například:

```PowerShell
Get-VirtualDisk
```

Pokud chcete sledovat přidružení mezi objekty v zásobníku, přesměrujte jednu rutinu **Get-** rutinu na další.

Tady je příklad, jak získat z virtuálního disku až po jeho svazek:

```PowerShell
Get-VirtualDisk <FriendlyName> | Get-Disk | Get-Partition | Get-Volume
```

### <a name="step-1--expand-the-virtual-disk"></a>Krok 1 – rozšíření virtuálního disku

Virtuální disk může používat vrstvy úložiště, nebo ne, v závislosti na tom, jak byl vytvořen.

Pro kontrolu spusťte následující rutinu:

```PowerShell
Get-VirtualDisk <FriendlyName> | Get-StorageTier
```

Pokud rutina nevrátí žádnou hodnotu, virtuální disk nepoužívá vrstvy úložiště.

#### <a name="no-storage-tiers"></a>Žádné vrstvy úložiště

Pokud virtuální disk nemá žádné vrstvy úložiště, můžete ho rozšířit přímo pomocí rutiny **Resize-VirtualDisk** .

Zadejte novou velikost v parametru **-Size** .

```PowerShell
Get-VirtualDisk <FriendlyName> | Resize-VirtualDisk -Size <Size>
```

Když rozbalíte **VirtualDisk**, **disk** se automaticky vymění a zároveň se změní jeho velikost.

![Animovaný diagram ukazuje, že virtuální disk svazku se bude zvětšovat, zatímco vrstva disku hned nad ním bude automaticky větší, jako výsledek.](media/extend-volumes/Resize-VirtualDisk.gif)

#### <a name="with-storage-tiers"></a>S vrstvami úložiště

Pokud virtuální disk používá vrstvy úložiště, můžete jednotlivé úrovně rozbalovat samostatně pomocí rutiny **Resize-StorageTier** .

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

Když rozbalíte **StorageTier**, **VirtualDisk** a **disk** se dodrží automaticky a změní se také velikost.

![Animovaný diagram zobrazuje nejprve další úroveň úložiště, která se stane velkou, zatímco vrstva virtuálního disku a vrstva disku výše se zvětšují také.](media/extend-volumes/Resize-StorageTier.gif)

### <a name="step-2--expand-the-partition"></a>Krok 2 – rozšíření oddílu

Dále rozbalte oddíl pomocí rutiny **změnit velikost oddílu** . Očekává se, že virtuální disk bude mít dva oddíly: první je vyhrazená a neměla by být upravena. ta, kterou potřebujete změnit, má **PartitionNumber = 2** a **Type = Basic**.

Zadejte novou velikost v parametru **-Size** . Doporučujeme použít maximální podporovanou velikost, jak je znázorněno níže.

```PowerShell
# Choose virtual disk
$VirtualDisk = Get-VirtualDisk <FriendlyName>

# Get its partition
$Partition = $VirtualDisk | Get-Disk | Get-Partition | Where PartitionNumber -Eq 2

# Resize to its maximum supported size
$Partition | Resize-Partition -Size ($Partition | Get-PartitionSupportedSize).SizeMax
```

Když rozbalíte **oddíl**, **svazek** a **ClusterSharedVolume** se dodrží automaticky a zároveň se změní jeho velikost.

![Animovaný diagram zobrazuje vrstvu virtuálních disků ve spodní části svazku a zvětšuje se tak větší s každou vrstvou, která je větší a roste.](media/extend-volumes/Resize-Partition.gif)

A to je vše!

> [!TIP]
> Spuštěním **Get-Volume** můžete ověřit, jestli má svazek novou velikost.

## <a name="next-steps"></a>Další kroky

Podrobné pokyny pro další důležité úlohy správy úložiště najdete tady:

- [Plánování svazků](../concepts/plan-volumes.md)
- [Vytváření svazků](create-volumes.md)
- [Odstranit svazky](delete-volumes.md)