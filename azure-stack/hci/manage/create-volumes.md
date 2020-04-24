---
title: Vytváření svazků v Azure Stack HCI
description: Postup vytváření svazků v Azure Stack HCI pomocí centra pro správu Windows a PowerShellu
author: khdownie
ms.author: v-kedow
ms.topic: article
ms.date: 02/28/2020
ms.openlocfilehash: b195a55314935282f43f99bf02bda1d2bc298b54
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "78370406"
---
# <a name="creating-volumes-in-azure-stack-hci"></a>Vytváření svazků v Azure Stack HCI

> Platí pro: Windows Server 2019

V tomto tématu se dozvíte, jak vytvořit svazky v Prostory úložiště s přímým přístupemm clusteru pomocí centra pro správu Windows a prostředí Windows PowerShell, jak pracovat se soubory na svazcích a jak na svazcích Povolit odstranění duplicitních dat a kompresi.

## <a name="create-a-three-way-mirror-volume"></a>Vytvoření trojrozměrného zrcadlového svazku

Postup vytvoření trojrozměrného zrcadlového svazku pomocí centra pro správu systému Windows:

1. V centru pro správu Windows se připojte ke clusteru Prostory úložiště s přímým přístupem a potom v podokně **nástroje** vyberte **svazky** .
2. Na stránce **svazky** vyberte kartu **inventář** a pak vyberte **vytvořit svazek**.
3. V podokně **vytvořit svazek** zadejte název svazku a možnost **odolnosti** nechte jako **Třícestný zrcadlový**svazek.
4. V části **Velikost na HDD**zadejte velikost svazku. Například 5 TB (terabajty).
5. Vyberte **Vytvořit**.

V závislosti na velikosti může vytvoření svazku trvat několik minut. Oznámení v pravém horním rohu vám umožní zjistit, kdy se svazek vytvoří. Nový svazek se zobrazí v seznamu inventáře.

Podívejte se na rychlé video o vytváření trojrozměrného zrcadlového svazku.

> [!VIDEO https://www.youtube-nocookie.com/embed/o66etKq70N8]

## <a name="create-a-mirror-accelerated-parity-volume"></a>Vytvoření svazku parity s akcelerovaným zrcadlením

Zrcadlově urychlené parity zmenší nároky na pevný disk. Například trojrozměrné zrcadlení svazek by znamenalo, že pro každých 10 terabajtů velikosti budete potřebovat 30 terabajtů jako nároků. Pokud chcete snížit nároky na nároky, vytvořte svazek s paritou podporující zrcadlení. Tím se snižuje nároky z 30 terabajtů na pouhých 22 terabajtů, a to i na 4 servery, a to díky zrcadlení nejaktivnějších 20 procent dat a používání parity, což je více místa na disku pro uložení zbytku. Tento poměr paritního a zrcadleného prostředí můžete upravit, aby se zajistil výkon a kompromisy s kapacitou, které jsou pro vaše zatížení nejvhodnější. Například 90 procentuální parita a 10% zrcadlení vychází z nižšího výkonu, ale zjednodušuje nároky ještě více.

Vytvoření svazku s paritou podporující zrcadlení v centru pro správu systému Windows:

1. V centru pro správu Windows se připojte ke clusteru Prostory úložiště s přímým přístupem a potom v podokně **nástroje** vyberte **svazky** .
2. Na stránce svazky vyberte kartu **inventář** a pak vyberte **vytvořit svazek**.
3. V podokně **vytvořit svazek** zadejte název svazku.
4. V případě **odolnosti**vyberte možnost **zrcadlově urychlené parity**.
5. V části **procento parity**vyberte procento parity.
6. Vyberte **Vytvořit**.

Podívejte se na rychlé video o tom, jak vytvořit zrcadlený svazek parity.

> [!VIDEO https://www.youtube-nocookie.com/embed/R72QHudqWpE]

## <a name="open-volume-and-add-files"></a>Otevřít svazek a přidat soubory

Chcete-li otevřít svazek a přidat soubory do svazku v centru pro správu systému Windows:

1. V centru pro správu Windows se připojte ke clusteru Prostory úložiště s přímým přístupem a potom v podokně **nástroje** vyberte **svazky** .
2. Na stránce **svazky** vyberte kartu **inventář** .
2. V seznamu svazků vyberte název svazku, který chcete otevřít.

    Na stránce Podrobnosti o svazku vidíte cestu ke svazku.

4. V horní části stránky vyberte **otevřít**. Tím se spustí nástroj **soubory** v centru pro správu systému Windows.
5. Přejděte na cestu ke svazku. Tady můžete procházet soubory ve svazku.
6. Vyberte **nahrát**a pak vyberte soubor, který se má nahrát.
7. Pomocí tlačítka **zpět** v prohlížeči se vraťte do podokna **nástroje** v centru pro správu Windows.

Podívejte se na rychlé video o tom, jak otevřít svazek a přidat soubory.

> [!VIDEO https://www.youtube-nocookie.com/embed/j59z7ulohs4]

## <a name="turn-on-deduplication-and-compression"></a>Zapnutí odstraňování duplicit a komprimace

Odstraňování duplicitních dat a komprese je spravovaná na jeden svazek. Odstranění duplicitních dat a komprimace používá model následného zpracování, což znamená, že nebudete moct sledovat úspory, dokud je nespustíte. Pokud k tomu dojde, bude fungovat ve všech souborech, dokonce i těch, které existovaly dříve.

1. V centru pro správu Windows se připojte ke clusteru Prostory úložiště s přímým přístupem a potom v podokně **nástroje** vyberte **svazky** .
2. Na stránce **svazky** vyberte kartu **inventář** .
3. V seznamu svazků vyberte název svazku, který chcete spravovat.
4. Na stránce Podrobnosti o svazku klikněte na přepínač s názvem **odstranění duplicit a komprese**.
5. V podokně **Povolit odstraňování duplicitních dat** vyberte režim odstranění duplicitních dat.

    Místo složitých nastavení vám centrum pro správu systému Windows umožní vybrat si předem připravené profily pro různé úlohy. Pokud si nejste jistí, použijte výchozí nastavení.

6. Vyberte **Povolit**.

Podívejte se na rychlé video o zapnutí odstraňování duplicit a komprimace.

> [!VIDEO https://www.youtube-nocookie.com/embed/PRibTacyKko]

## <a name="create-volumes-using-windows-powershell"></a>Vytváření svazků pomocí Windows PowerShellu

Nejdřív v nabídce Start systému Windows spusťte Windows PowerShell. K vytvoření svazků pro Azure Stack HCI doporučujeme použít rutinu **New-Volume** . Nabízí nejrychlejší a nejpřímější prostředí. Tato jediná rutina automaticky vytvoří virtuální disk, oddíly a naformátuje ho, vytvoří svazek se shodným názvem a přidá ho ke sdíleným svazkům clusteru – to vše v jednom jednoduchém kroku.

Rutina **New-Volume** má čtyři parametry, které budete muset zadat vždycky:

- **FriendlyName:** Libovolný požadovaný řetězec, například *"Volume1"*
- **Systém souborů:** Buď **CSVFS_ReFS** (doporučeno) nebo **CSVFS_NTFS**
- **StoragePoolFriendlyName:** Název fondu úložiště, například *s2d na clusteru*
- **Velikost:** Velikost svazku, například *"10 TB"*

   > [!NOTE]
   > Windows, včetně PowerShellu, se počítá pomocí binárních čísel (číslic 2), zatímco jednotky jsou často označené pomocí desítkových čísel (základu 10). Vysvětluje, proč se v systému Windows zobrazuje jednotka "One terabajt" definovaná jako 1 000 000 000 000 bajtů, a to přibližně "909 GB". To se očekává. Při vytváření svazků pomocí **New-Volume**byste měli zadat parametr **velikosti** v binárních (číslicích) (v desítkové dvojkě). Například zadáním "909GB" nebo "0.909495 TB" se vytvoří svazek o velikosti přibližně 1 000 000 000 000 bajtů.

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

### <a name="example-using-storage-tiers"></a>Příklad: použití vrstev úložiště

V nasazeních se třemi typy jednotek může jeden svazek zahrnovat vrstvy SSD a HDD, které se na každé z nich nacházejí částečně. Stejně tak v nasazení se čtyřmi nebo více servery může jeden svazek kombinovat zrcadlení a duální paritu na každé straně.

Aby vám usnadnila vytváření takových svazků, Prostory úložiště s přímým přístupem poskytuje šablony výchozích vrstev s názvem *výkon* a *kapacitu*. Zapouzdřují definice pro trojrozměrné zrcadlení na rychlejších diskových jednotkách (pokud jsou k dispozici) a duální parity na pomalejších jednotkách kapacity (pokud jsou k dispozici).

Můžete je zobrazit spuštěním rutiny **Get-StorageTier** .

```PowerShell
Get-StorageTier | Select FriendlyName, ResiliencySettingName, PhysicalDiskRedundancy
```

![Snímek PowerShellu vrstev úložiště](media/creating-volumes/storage-tiers-screenshot.png)

Pokud chcete vytvořit vrstvené svazky, odkazujte na tyto šablony vrstev pomocí parametrů **StorageTierFriendlyNames** a **StorageTierSizes** rutiny **New-Volume** . Následující rutina například vytvoří jeden svazek, který kombinuje trojrozměrné zrcadlení a duální paritu v 30:70 proportech.

```PowerShell
New-Volume -FriendlyName "Volume4" -FileSystem CSVFS_ReFS -StoragePoolFriendlyName S2D* -StorageTierFriendlyNames Performance, Capacity -StorageTierSizes 300GB, 700GB
```

A je to hotové! Pokud chcete vytvořit více než jeden svazek, opakujte postup podle potřeby.

## <a name="next-steps"></a>Další kroky

Pokud chcete provádět další úlohy správy úložiště v Prostory úložiště s přímým přístupem, přečtěte si také:

- [Přehled Prostory úložiště s přímým přístupem](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [Plánování svazků v Prostory úložiště s přímým přístupem](/windows-server/storage/storage-spaces/plan-volumes)
- [Rozšíření svazků v Prostory úložiště s přímým přístupem](/windows-server/storage/storage-spaces/resize-volumes)
- [Odstraňování svazků v Prostory úložiště s přímým přístupem](/windows-server/storage/storage-spaces/delete-volumes)