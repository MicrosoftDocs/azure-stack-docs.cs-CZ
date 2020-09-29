---
title: Vytvoření roztažené svazky clusteru a nastavení replikace
description: Postup vytvoření svazků a nastavení replikace pro roztažené clustery v Azure Stack HCI pomocí centra pro správu Windows a PowerShellu.
author: v-dasis
ms.author: v-dasis
ms.topic: how-to
ms.date: 07/24/2020
ms.openlocfilehash: 8c885f7a344ff9d34228bad76c79e65f1f345f25
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90573679"
---
# <a name="create-stretched-cluster-volumes-and-set-up-replication"></a>Vytvoření roztažené svazky clusteru a nastavení replikace

> Platí pro: Azure Stack HCI, verze 20H2

Tento článek popisuje, jak vytvořit svazky a nastavit replikaci pro roztažené clustery v Azure Stack HCI pomocí centra pro správu Windows a PowerShellu.

Vytvoříme svazky na čtyřech serverech ve dvou lokalitách, a to jako příklad dva servery na web. Mějte na paměti, že pokud chcete vytvořit trojrozměrné svazky zrcadlení, budete potřebovat alespoň šest serverů, tři servery na web.

## <a name="stretched-volumes-and-replication-using-windows-admin-center"></a>Roztažené svazky a replikace pomocí centra pro správu Windows

Vytvoření svazku a nastavení replikace:

1. V centru pro správu systému Windows v části **nástroje**vyberte **svazky**.
1. V pravém podokně vyberte kartu **inventář** a pak vyberte **vytvořit**.
1. Na panelu **vytvořit svazek** vyberte možnost **replikovat svazek mezi lokalitami**.
1. V rozevíracím seznamu vyberte směr replikace mezi lokalitami.
1. V části **režim replikace**vyberte **asynchronní** nebo **synchronní**.
1. Zadejte název zdrojové replikační skupiny a název cílové replikační skupiny.
1. Zadejte požadovanou velikost pro svazek protokolu.
1. V části **Upřesnit**můžete volitelně provést následující akce:
     - Zadejte nebo změňte **název zdrojové replikační skupiny**.
     - Zadejte nebo změňte **název cílové skupiny replikace**.
     - Chcete-li **použít bloky již osazené v cíli**..., zaškrtněte příslušné políčko.
     - Pokud chcete **Šifrovat provoz replikace**, zaškrtněte příslušné políčko.
     - Chcete-li **Povolit skupiny konzistence**, zaškrtněte příslušné políčko.
1. Jakmile budete hotovi, klikněte na **Vytvořit**.
1. V pravém podokně ověřte, zda je datový disk a disk protokolu vytvořen v primárním (aktivním) webu a zda jsou v sekundárním (pasivním) lokalitě vytvořeny odpovídající disky repliky dat a protokolů. U obousměrné replikace byste měli vidět dvě sady dat a svazků disků.
1. V části **nástroje**vyberte **replika úložiště**.
1. V pravém podokně v části **partnerství**ověřte, že partnerství replikace bylo úspěšně vytvořeno.

Následně byste měli před nasazením virtuálních počítačů a dalších úloh ověřit úspěšnou replikaci dat mezi lokalitami. Další informace najdete v části ověření replikace v tématu [ověření clusteru](../deploy/validate.md) .

## <a name="create-stretched-volumes-using-powershell"></a>Vytvoření roztaženého svazku pomocí prostředí PowerShell

Vytvoření svazku se liší v případě standardních clusterů s jednou lokalitou versus roztažené clustery (se dvěma servery). U obou scénářů ale pomocí `New-Volume` rutiny vytvoříte virtuální disk, rozdělíte ho a naformátujete ho, vytvoříte svazek se shodným názvem a přidáte ho do sdílených svazků clusteru (CSV).

Vytváření svazků a virtuálních disků pro roztažené clustery je o něco větší než u clusterů s jedním webem. Roztažené clustery vyžadují minimálně čtyři svazky – dva datové svazky a dva svazky protokolu s dvojicí svazků dat/protokolů umístěnou v každé lokalitě. Pak vytvoříte replikační skupinu pro každou lokalitu a nastavíte mezi nimi replikaci. Musíme přesunout skupiny prostředků ze serveru na server. `Move-ClusterGroup`Tato rutina se používá.

1. Nejdříve přesuneme `Available Storage` skupinu prostředků fondu úložiště do umístění `Server1` `Site1` pomocí `Move-ClusterGroup` rutiny:

    ```powershell
    Move-ClusterGroup -Cluster ClusterS1 -Name ‘Available Storage’ -Node Server1
    ```

1. Dále vytvořte první virtuální disk ( `Disk1` ) pro `Server1` v `Site1` :

    ```powershell
    New-Volume -CimSession Server1 -FriendlyName Disk1 -FileSystem REFS -DriveLetter F -ResiliencySettingName Mirror -Size 10GB -StoragePoolFriendlyName "Storage Pool for Site 1"
    ```

1. Vytvořte druhý virtuální disk ( `Disk2` ) pro `Server1` v nástroji `Site1` :

    ```powershell
    New-Volume -CimSession Server1 -FriendlyName Disk2 -FileSystem REFS -DriveLetter G -ResiliencySettingName Mirror -Size 10GB -StoragePoolFriendlyName "Storage Pool for Site 1"
    ```

1. Teď skupinu převeďte `Available Storage` do režimu offline:

    ```powershell
    Stop-ClusterGroup -Cluster ClusterS1 -Name 'Available Storage'
    ```

1. A přesuňte `Available Storage` skupinu do `Server3` `Site2` :

    ```powershell
    Move-ClusterGroup -Name 'Available Storage' -Node Server3
    ```

1. Vytvořte první virtuální disk ( `Disk3` ) na `Server3` v `Site2` :

    ```powershell
    New-Volume -CimSession Server3 -FriendlyName Disk3 -FileSystem REFS -DriveLetter H -ResiliencySettingName Mirror -Size 10GB -StoragePoolFriendlyName "Storage Pool for Site 2"
    ```

1. A vytvořte druhý virtuální disk ( `Disk4` ) na `Server3` v `Site2` :

    ```powershell
    New-Volume -CimSession Server3 -FriendlyName Disk4 -FileSystem REFS -DriveLetter I -ResiliencySettingName Mirror -Size 10GB -StoragePoolFriendlyName "Storage Pool for Site 2"
    ```

1. Teď skupinu převeďte do `Available Storage` offline režimu a pak ji přesuňte zpátky na jeden ze serverů v `Site1` :

    ```powershell
    Stop-ClusterGroup -Cluster ClusterS1 -Name 'Available Storage'
    ```

    ```powershell
    Move-ClusterGroup -Cluster ClusterS1 -Name 'Available Storage' -Node Server1
    ```

1. Pomocí `Get-ClusterResource` rutiny se ujistěte, že se vytvořily čtyři svazky virtuálních disků, a to dvě v každém fondu úložiště:

    ```powershell
    Get-ClusterResource -Cluster ClusterS1
    ```

1. Nyní přidat `Disk1` ke sdíleným svazkům clusteru:

    ```powershell
    Add-ClusterSharedVolume -Name 'Cluster Virtual Disk (Disk1)'
    ```

Dokončili jste vytváření svazků a připraveni k nastavení repliky úložiště pro replikaci.

## <a name="set-up-replication-using-powershell"></a>Nastavení replikace pomocí PowerShellu

Když pomocí prostředí PowerShell nastavíte repliku úložiště pro roztaženého clusteru, bude nutné přidat disk, který se bude používat pro zdrojová data, jako sdílený svazek clusteru (CSV). Všechny ostatní disky musí zůstat ve skupině úložiště k dispozici jako jednotky, které nejsou CSV. Tyto disky se pak přidají jako sdílené svazky clusteru během procesu vytváření repliky úložiště.

V předchozím kroku byly virtuální disky přidány pomocí písmen jednotek, aby bylo možné jejich identifikaci usnadnit. Replika úložiště je replikace typu 1:1, což znamená, že jeden disk může být replikován na jiný jeden disk.

### <a name="step-1-validate-the-topology-for-replication"></a>Krok 1: ověření topologie replikace

Než začnete, měli byste spustit `Test-SRTopology` rutinu delší dobu (například několik hodin). `Test-SRTopology`Rutina ověří potenciální replikační partnerství a ověří místního hostitele na cílovém serveru nebo vzdáleně mezi zdrojovým a cílovým serverem.

Tato rutina ověří, zda:

- K SMB je možné připínat přes síť, což znamená, že port TCP 445 a port 5445 jsou otevřené v obousměrném směru.
- K protokolu WS-MAN se dá v síti přistup přes protokol HTTP, což znamená, že jsou otevřené porty TCP 5985 a 5986.
- K poskytovateli SR WMIv2 se dá dostat a přijímat požadavky.
- Zdrojové a cílové datové svazky existují a jsou zapisovatelné.
- Zdrojové a cílové svazky protokolů existují s formátováním systému souborů NTFS nebo s formátováním ReFS a s dostatkem volného místa.
- Úložiště se inicializuje ve formátu GPT, ne v MBR s vyhovujícími velikostmi sektorů.
- Pro spuštění replikace je k dispozici dostatek fyzické paměti.

Kromě toho `Test-SRTopology` bude rutina také měřit:

- Latence odezvy protokolu ICMP a vykazovat průměr.
- Čítače výkonu pro zápis vstupů a výstupů a vykazují průměrnou dobu zobrazenou na tomto svazku.
- Odhadovaná doba prvotní synchronizace.

Po dokončení test-SRTopology vytvoří soubor. html (si zprávu testsrtopologyreport s datem a časem) ve složce tempa ve Windows. Všechna upozornění nebo selhání by se měla zkontrolovat, protože by mohlo dojít k tomu, že replika úložiště není správně vytvořená.

Příkladem příkazu, který se spustí po dobu 5 hodin, by byl:

```powershell
Test-SRTopology -SourceComputerName Server1 -SourceVolumeName W: -SourceLogVolumeName X: -DestinationComputerName Server3 -DestinationVolumeName Y: -DestinationLogVolumeName Z: -DurationInMinutes 300 -ResultPath c:\temp
```

### <a name="step-2-create-the-replication-partnership"></a>Krok 2: Vytvoření partnerství replikace

Teď, když jste dokončili `Test-SRTopology` testy, jste připraveni ke konfiguraci repliky úložiště a vytvoření partnerství replikace. V kostce nakonfigurujeme repliku úložiště vytvořením replikačních skupin (RG) pro každou lokalitu a zadáním datových svazků a svazků protokolů pro uzly zdrojového serveru v Site1 (Server1, Server2) a cílovém (replikovaném) uzlu serveru v site2 (Server3, Server4).

Pojďme začít:

1. Přidejte datový disk Site1 jako sdílený svazek clusteru (CSV):

   ```powershell
   Add-ClusterSharedVolume -Name "Cluster Virtual Disk (Site1)"
   ```

1. Dostupná skupina úložišť by měla být vlastněná uzlem, na kterém je aktuálně umístěná. Skupinu můžete přesunout na Server1 pomocí:

   ```powershell
   Move-ClusterGroup -Name “Available Storage” -Node Server1
   ```

1. K vytvoření partnerství replikace použijte `New-SRPartnership` rutinu. Tato rutina je také tam, kde zadáváte názvy zdrojových dat a svazků protokolů:

   ```powershell
   New-SRPartnership -SourceComputerName "Server1" -SourceRGName "Replication1" -SourceVolumeName "C:\ClusterStorage\Disk1\" -SourceLogVolumeName "G:" -DestinationComputerName "Server3" -DestinationRGName "Replication2" -DestinationVolumeName "H:" -DestinationLogVolumeName "I:"
   ```

`New-SRPartnership`Rutina vytvoří partnerství replikace mezi dvěma replikačními skupinami pro tyto dvě lokality. V tomto příkladu `Replication1` je replikační skupina pro primární uzel Server1 v Site1 a `Replication2` je replikační skupina pro cílový uzel Server3 v site2.

Replika úložiště teď nastaví vše nahoru. Pokud existují nějaká data, která by se mohla replikovat, provede to tady. V závislosti na množství dat, která je potřeba replikovat, může to chvíli trvat. Nedoporučuje se přesouvat žádné skupiny, dokud tento proces nedokončíte.

## <a name="next-steps"></a>Další kroky

Související témata a další úlohy správy úložišť najdete zde:

- [Přehled roztaženého clusteru](../concepts/stretched-clusters.md)
- [Plánování svazků](../concepts/plan-volumes.md)
- [Rozšiřování svazků](extend-volumes.md)
- [Odstranit svazky](delete-volumes.md)
