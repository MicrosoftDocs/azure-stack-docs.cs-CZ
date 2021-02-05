---
title: Vytváření svazků v Azure Stack HCI
description: Postup vytváření svazků v Azure Stack HCI pomocí centra pro správu Windows a PowerShellu
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 02/04/2021
ms.openlocfilehash: 9bb0ff34863f8262d5919e5eae6f735709097bf5
ms.sourcegitcommit: 283b1308142e668749345bf24b63d40172559509
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2021
ms.locfileid: "99570731"
---
# <a name="create-volumes-in-azure-stack-hci"></a>Vytváření svazků v Azure Stack HCI

> Platí pro: Azure Stack HCI, verze 20H2

Toto téma popisuje, jak vytvořit svazky v Azure Stack clusteru HCI pomocí centra pro správu Windows a prostředí Windows PowerShell, jak pracovat se soubory na svazcích a jak povolit odstranění duplicitních dat a kompresi na svazcích. Informace o tom, jak vytvářet svazky a nastavit replikaci pro roztažené clustery, najdete v tématu [Vytvoření roztaženého svazku](create-stretched-volumes.md).

## <a name="create-a-three-way-mirror-volume"></a>Vytvoření trojrozměrného zrcadlového svazku

Postup vytvoření trojrozměrného zrcadlového svazku pomocí centra pro správu systému Windows:

1. V centru pro správu Windows se připojte ke clusteru a potom v podokně **nástroje** vyberte **svazky** .
2. Na stránce **svazky** vyberte kartu **inventář** a pak vyberte **vytvořit svazek**.
3. V podokně **vytvořit svazek** zadejte název svazku a možnost **odolnosti** nechte jako **Třícestný zrcadlový** svazek.
4. V části **Velikost na HDD** zadejte velikost svazku. Například 5 TB (terabajty).
5. Vyberte **Vytvořit**.

V závislosti na velikosti může vytvoření svazku trvat několik minut. Oznámení v pravém horním rohu vám umožní zjistit, kdy se svazek vytvoří. Nový svazek se zobrazí v seznamu inventáře.

Podívejte se na rychlé video o vytváření trojrozměrného zrcadlového svazku.

> [!VIDEO https://www.youtube-nocookie.com/embed/o66etKq70N8]

## <a name="create-a-mirror-accelerated-parity-volume"></a>Vytvoření svazku parity s akcelerovaným zrcadlením

Zrcadlově akcelerovaná parita (mapa) snižuje nároky na pevný disk. Například trojrozměrné zrcadlení svazek by znamenalo, že pro každých 10 terabajtů velikosti budete potřebovat 30 terabajtů jako nároků. Pokud chcete snížit nároky na nároky, vytvořte svazek s paritou podporující zrcadlení. Tím se snižuje nároky z 30 terabajtů na pouhých 22 terabajtů, a to i na 4 servery, a to díky zrcadlení nejaktivnějších 20 procent dat a používání parity, což je více místa na disku pro uložení zbytku. Tento poměr paritního a zrcadleného prostředí můžete upravit, aby se zajistil výkon a kompromisy s kapacitou, které jsou pro vaše zatížení nejvhodnější. Například 90 procentuální parita a 10% zrcadlení vychází z nižšího výkonu, ale zjednodušuje nároky ještě více.

  >[!NOTE]
  >Zrcadlené svazky s paritou vyžadují odolný systém souborů (ReFS).

Vytvoření svazku s paritou podporující zrcadlení v centru pro správu systému Windows:

1. V centru pro správu Windows se připojte ke clusteru a potom v podokně **nástroje** vyberte **svazky** .
2. Na stránce svazky vyberte kartu **inventář** a pak vyberte **vytvořit svazek**.
3. V podokně **vytvořit svazek** zadejte název svazku.
4. V případě **odolnosti** vyberte možnost **zrcadlově urychlené parity**.
5. V části **procento parity** vyberte procento parity.
6. Vyberte **Vytvořit**.

Podívejte se na rychlé video o tom, jak vytvořit zrcadlený svazek parity.

> [!VIDEO https://www.youtube-nocookie.com/embed/R72QHudqWpE]

## <a name="open-volume-and-add-files"></a>Otevřít svazek a přidat soubory

Chcete-li otevřít svazek a přidat soubory do svazku v centru pro správu systému Windows:

1. V centru pro správu Windows se připojte ke clusteru a potom v podokně **nástroje** vyberte **svazky** .
2. Na stránce **svazky** vyberte kartu **inventář** .
2. V seznamu svazků vyberte název svazku, který chcete otevřít.

    Na stránce Podrobnosti o svazku vidíte cestu ke svazku.

4. V horní části stránky vyberte **otevřít**. Tím se spustí nástroj **soubory** v centru pro správu systému Windows.
5. Přejděte na cestu ke svazku. Tady můžete procházet soubory ve svazku.
6. Vyberte **nahrát** a pak vyberte soubor, který se má nahrát.
7. Pomocí tlačítka **zpět** v prohlížeči se vraťte do podokna **nástroje** v centru pro správu Windows.

Podívejte se na rychlé video o tom, jak otevřít svazek a přidat soubory.

> [!VIDEO https://www.youtube-nocookie.com/embed/j59z7ulohs4]

## <a name="turn-on-deduplication-and-compression"></a>Zapnutí odstraňování duplicit a komprimace

Odstraňování duplicitních dat a komprese je spravovaná na jeden svazek. Odstranění duplicitních dat a komprimace používá model následného zpracování, což znamená, že nebudete moct sledovat úspory, dokud je nespustíte. Pokud k tomu dojde, bude fungovat ve všech souborech, dokonce i těch, které existovaly dříve.

Další informace najdete v tématu [povolení šifrování svazků, odstraňování duplicit a komprimace](volume-encryption-deduplication.md) .

## <a name="create-volumes-using-windows-powershell"></a>Vytváření svazků pomocí Windows PowerShellu

Nejdřív v nabídce Start systému Windows spusťte Windows PowerShell. K vytvoření svazků pro Azure Stack HCI doporučujeme použít rutinu **New-Volume** . Nabízí nejrychlejší a nejpřímější prostředí. Tato jediná rutina automaticky vytvoří virtuální disk, oddíly a naformátuje ho, vytvoří svazek se shodným názvem a přidá ho ke sdíleným svazkům clusteru – to vše v jednom jednoduchém kroku.

Rutina **New-Volume** má čtyři parametry, které budete muset zadat vždycky:

- **FriendlyName:** Libovolný požadovaný řetězec, například *"Volume1"*
- **Systém souborů:** Buď **CSVFS_ReFS** (doporučeno pro všechny svazky; vyžadované pro svazky parity s zrcadlením) nebo **CSVFS_NTFS**
- **StoragePoolFriendlyName:** Název fondu úložiště, například *s2d na clusteru*
- **Velikost:** Velikost svazku, například *"10 TB"*

   > [!NOTE]
   > Windows, včetně PowerShellu, se počítá pomocí binárních čísel (číslic 2), zatímco jednotky jsou často označené pomocí desítkových čísel (základu 10). Vysvětluje, proč se v systému Windows zobrazuje jednotka "One terabajt" definovaná jako 1 000 000 000 000 bajtů, a to přibližně "909 GB". To se očekává. Při vytváření svazků pomocí **New-Volume** byste měli zadat parametr **velikosti** v binárních (číslicích) (v desítkové dvojkě). Například zadáním "909GB" nebo "0.909495 TB" se vytvoří svazek o velikosti přibližně 1 000 000 000 000 bajtů.

### <a name="example-with-2-or-3-servers"></a>Příklad: se dvěma nebo 3 servery

Pokud má vaše nasazení jen dva servery, Prostory úložiště s přímým přístupem bude pro odolnost automaticky používat obousměrné zrcadlení. Pokud má vaše nasazení pouze tři servery, bude automaticky používat trojrozměrné zrcadlení.

```PowerShell
New-Volume -FriendlyName "Volume1" -FileSystem CSVFS_ReFS -StoragePoolFriendlyName S2D* -Size 1TB
```

### <a name="example-with-4-servers"></a>Příklad: se 4 servery

Pokud máte čtyři nebo víc serverů, můžete k výběru typu odolnosti použít volitelný parametr **ResiliencySettingName** .

-   **ResiliencySettingName:** **Zrcadlení** nebo **parita**.

V následujícím příkladu používá *"Volume2"* trojrozměrné zrcadlení a *"Volume3"* používá duální paritu (často označované jako "mazání kódu").

```PowerShell
New-Volume -FriendlyName "Volume2" -FileSystem CSVFS_ReFS -StoragePoolFriendlyName S2D* -Size 1TB -ResiliencySettingName Mirror
New-Volume -FriendlyName "Volume3" -FileSystem CSVFS_ReFS -StoragePoolFriendlyName S2D* -Size 1TB -ResiliencySettingName Parity
```

## <a name="using-storage-tiers"></a>Používání vrstev úložiště

V nasazeních se třemi typy jednotek může jeden svazek zahrnovat vrstvy SSD a HDD, které se na každé z nich nacházejí částečně. Stejně tak v nasazení se čtyřmi nebo více servery může jeden svazek kombinovat zrcadlení a duální paritu na každé straně.

Aby vám usnadnila vytváření takových svazků, Azure Stack HCL poskytuje výchozí šablony vrstev s názvem **MirrorOn *** Free * a **NestedMirrorOn *** Free * (pro výkon) a **ParityOn *** Free * a **NestedParityOn *** Free * (pro kapacitu), *kde je* na discích typu HDD nebo SSD. Šablony reprezentují vrstvy úložiště na základě typů médií a zapouzdřují definice pro trojrozměrné zrcadlení na rychlejších diskových jednotkách (Pokud je k dispozici) a duální parita na pomalejších jednotkách kapacity (Pokud je k dispozici).

Můžete je zobrazit spuštěním rutiny **Get-StorageTier** na jakémkoli serveru v clusteru.

```PowerShell
Get-StorageTier | Select FriendlyName, ResiliencySettingName, PhysicalDiskRedundancy
```

Pokud máte například cluster se dvěma uzly s pouze pevným diskem, váš výstup může vypadat přibližně takto:

```PowerShell
FriendlyName      ResiliencySettingName PhysicalDiskRedundancy
------------      --------------------- ----------------------
NestedParityOnHDD Parity                                     1
Capacity          Mirror                                     1
NestedMirrorOnHDD Mirror                                     3
MirrorOnHDD       Mirror                                     1
```

Pokud chcete vytvořit vrstvené svazky, odkazujte na tyto šablony vrstev pomocí parametrů **StorageTierFriendlyNames** a **StorageTierSizes** rutiny **New-Volume** . Následující rutina například vytvoří jeden svazek, který kombinuje trojrozměrné zrcadlení a duální paritu v 30:70 proportech.

```PowerShell
New-Volume -FriendlyName "Volume1" -FileSystem CSVFS_ReFS -StoragePoolFriendlyName S2D* -StorageTierFriendlyNames MirrorOnHDD, Capacity -StorageTierSizes 300GB, 700GB
```

Pokud chcete vytvořit více než jeden svazek, opakujte postup podle potřeby.

### <a name="nested-resiliency-volumes"></a>Vnořené svazky odolné proti chybám

Vnořená odolnost se vztahuje pouze na clustery se dvěma servery. vnořenou odolnost nelze použít, pokud má cluster tři nebo více serverů. Vnořená odolnost umožňuje clusteru se dvěma servery vydržet několik selhání hardwaru současně bez ztráty dostupnosti úložiště, což umožňuje uživatelům, aplikacím a virtuálním počítačům i nadále běžet bez přerušení. Další informace najdete v tématu [plánování svazků: výběr typu odolnosti](../concepts/plan-volumes.md#choosing-the-resiliency-type).

#### <a name="create-nested-storage-tiers"></a>Vytváření vnořených vrstev úložiště

Postup vytvoření NestedMirror úrovně:

```PowerShell
New-StorageTier -StoragePoolFriendlyName S2D* -FriendlyName NestedMirror -ResiliencySettingName Mirror -NumberOfDataCopies 4 -MediaType HDD -CimSession 2nodecluster
```

Postup vytvoření NestedParity úrovně:

```PowerShell
New-StorageTier -StoragePoolFriendlyName S2D* -FriendlyName NestedParity -ResiliencySettingName Parity -NumberOfDataCopies 2 -PhysicalDiskRedundancy 1 -NumberOfGroups 1 -FaultDomainAwareness StorageScaleUnit -ColumnIsolation PhysicalDisk -MediaType HDD -CimSession 2nodecluster
```

#### <a name="create-nested-volumes"></a>Vytvořit vnořené svazky

Vytvoření svazku NestedMirror:

```PowerShell
New-Volume -StoragePoolFriendlyName S2D* -FriendlyName MyMirrorNestedVolume -StorageTierFriendlyNames NestedMirror -StorageTierSizes 500GB -CimSession 2nodecluster
```

Vytvoření svazku NestedParity:

```PowerShell
New-Volume -StoragePoolFriendlyName S2D* -FriendlyName MyParityNestedVolume -StorageTierFriendlyNames NestedMirror,NestedParity -StorageTierSizes 200GB, 1TB -CimSession 2nodecluster
```

### <a name="storage-tier-summary-table"></a>Tabulka souhrnu úrovně úložiště

Následující tabulky shrnují vrstvy úložiště, které jsou/je možné vytvořit v Azure Stack HCI.

**NumberOfNodes: 2**

| FriendlyName      | Média | ResiliencySettingName | NumberOfDataCopies | PhysicalDiskRedundancy | NumberOfGroups | FaultDomainAwareness | ColumnIsolation | Poznámka         |
| ----------------- | :-------: | :-------------------: | :----------------: | :--------------------: |:--------------:| :------------------: | :-------------: | :----------: |
| MirrorOnHDD       | HDD       | Zrcadlový                | 2                  | 1                      | 1              | StorageScaleUnit     | PhysicalDisk    | automaticky vytvořeno |
| MirrorOnSSD       | SSD       | Zrcadlový                | 2                  | 1                      | 1              | StorageScaleUnit     | PhysicalDisk    | automaticky vytvořeno |
| MirrorOnSCM       | SUBVENC       | Zrcadlový                | 2                  | 1                      | 1              | StorageScaleUnit     | PhysicalDisk    | automaticky vytvořeno |
| NestedMirrorOnHDD | HDD       | Zrcadlový                | 4                  | 3                      | 1              | StorageScaleUnit     | PhysicalDisk    | zásah       |
| NestedMirrorOnSSD | SSD       | Zrcadlový                | 4                  | 3                      | 1              | StorageScaleUnit     | PhysicalDisk    | zásah       |
| NestedMirrorOnSCM | SUBVENC       | Zrcadlový                | 4                  | 3                      | 1              | StorageScaleUnit     | PhysicalDisk    | zásah       |
| NestedParityOnHDD | HDD       | Parity                | 2                  | 1                      | 1              | StorageScaleUnit     | PhysicalDisk    | zásah       |
| NestedParityOnSSD | SSD       | Parity                | 2                  | 1                      | 1              | StorageScaleUnit     | PhysicalDisk    | zásah       |
| NestedParityOnSCM | SUBVENC       | Parity                | 2                  | 1                      | 1              | StorageScaleUnit     | PhysicalDisk    | zásah       |

**NumberOfNodes: 3**

| FriendlyName      | Média | ResiliencySettingName | NumberOfDataCopies | PhysicalDiskRedundancy | NumberOfGroups | FaultDomainAwareness | ColumnIsolation | Poznámka         |
| ----------------- | :-------: | :-------------------: | :----------------: | :--------------------: |:--------------:| :------------------: | :-------------: | :----------: |
| MirrorOnHDD       | HDD       | Zrcadlový                | 3                  | 2                      | 1              | StorageScaleUnit     | PhysicalDisk    | automaticky vytvořeno |
| MirrorOnSSD       | SSD       | Zrcadlový                | 3                  | 2                      | 1              | StorageScaleUnit     | PhysicalDisk    | automaticky vytvořeno |
| MirrorOnSCM       | SUBVENC       | Zrcadlový                | 3                  | 2                      | 1              | StorageScaleUnit     | PhysicalDisk    | automaticky vytvořeno |

**NumberOfNodes: 4 +**

| FriendlyName      | Média | ResiliencySettingName | NumberOfDataCopies | PhysicalDiskRedundancy | NumberOfGroups | FaultDomainAwareness | ColumnIsolation | Poznámka         |
| ----------------- | :-------: | :-------------------: | :----------------: | :--------------------: |:--------------:| :------------------: | :-------------: | :----------: |
| MirrorOnHDD       | HDD       | Zrcadlový                | 3                  | 2                      | 1              | StorageScaleUnit     | PhysicalDisk    | automaticky vytvořeno |
| MirrorOnSSD       | SSD       | Zrcadlový                | 3                  | 2                      | 1              | StorageScaleUnit     | PhysicalDisk    | automaticky vytvořeno |
| MirrorOnSCM       | SUBVENC       | Zrcadlový                | 3                  | 2                      | 1              | StorageScaleUnit     | PhysicalDisk    | automaticky vytvořeno |
| ParityOnHDD       | HDD       | Parity                | 1                  | 2                      | Auto           | StorageScaleUnit     | StorageScaleUnit| automaticky vytvořeno |
| ParityOnSSD       | SSD       | Parity                | 1                  | 2                      | Auto           | StorageScaleUnit     | StorageScaleUnit| automaticky vytvořeno |
| ParityOnSCM       | SUBVENC       | Parity                | 1                  | 2                      | Auto           | StorageScaleUnit     | StorageScaleUnit| automaticky vytvořeno |

## <a name="next-steps"></a>Další kroky

Související témata a další úlohy správy úložišť najdete zde:

- [Přehled Prostorů úložiště s přímým přístupem](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [Plánování svazků](../concepts/plan-volumes.md)
- [Rozšiřování svazků](extend-volumes.md)
- [Odstranit svazky](delete-volumes.md)