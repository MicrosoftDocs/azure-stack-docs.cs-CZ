---
title: Migrace na Azure Stack HCI na stejném hardwaru
description: Naučte se migrovat cluster pro Azure Stack HCL na stejném hardwaru.
author: v-dasis
ms.topic: how-to
ms.date: 12/10/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 08ed2b7272fd8a4f9f28f2721b8aff6552131afc
ms.sourcegitcommit: afdae61022037b5dba8345cb264049897e0aca8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97051560"
---
# <a name="migrate-to-azure-stack-hci-on-same-hardware"></a>Migrace na Azure Stack HCI na stejném hardwaru

> Platí pro Azure Stack HCI, verze 20H2; Windows Server 2019, Windows Server 2016, Windows Server 2012 R2, Windows Server 2008 R2

Toto téma popisuje, jak migrovat cluster Windows serveru 2016 nebo Windows Server 2019 na Azure Stack HCL pomocí stávajícího serverového hardwaru. Tento proces nainstaluje nový operační systém Azure Stack HCI a zachová stávající nastavení clusteru a úložiště a importuje vaše virtuální počítače.

Pokud chcete migrovat virtuální počítače na nový Azure Stack hardware HCI, přečtěte si téma [migrace na Azure Stack HCI na novém hardwaru](migrate-cluster-new-hardware.md).

> [!NOTE]
> V tomto článku se nezabývá migrace roztaženého clusteru.

## <a name="before-you-begin"></a>Než začnete

Před zahájením migrace je potřeba vzít v úvahu několik požadavků a věcí:

- Všechny příkazy prostředí Windows PowerShell musí být spuštěny jako správce.

- Musíte mít přihlašovací údaje domény s oprávněním správce pro Azure Stack HCI.

- Zálohujte všechny virtuální počítače ve zdrojovém clusteru. Dokončete zálohování všech aplikací a dat konzistentní s chybou a zálohou všech databází konzistentních vzhledem k aplikacím.  Informace o zálohování do Azure najdete v tématu [použití Azure Backup](https://docs.microsoft.com/azure-stack/hci/manage/use-azure-backup).

- Shromážděte inventář a konfiguraci všech uzlů clusteru a názvů clusterů, konfigurace sítě, sdílený svazek clusteru (CSV) odolnost a určující kapacitu kvora.

- Vypněte virtuální počítače clusteru, offline CSV, offline fondy úložiště a Clusterovou službu.
- Zakažte objekt názvu clusteru (objekt CNO) (bude znovu použit později) a:
    - Ověřte, že objekt CNO má práva pro vytváření objektů na vlastní organizační jednotku (OU).
    - Ověřte, jestli je v organizační jednotce nastavená zásada zděděný blok.
    - Nastavte požadované zásady pro Azure Stack HCI v této organizační jednotce.

## <a name="vm-version-support-and-update"></a>Podpora a aktualizace verzí virtuálních počítačů

Následující tabulka uvádí podporované verze operačních systémů Windows Server a jejich verze virtuálních počítačů pro místní migraci na stejný hardware.

Bez ohledu na verzi operačního systému, na kterém může být virtuální počítač spuštěný, je minimální verze virtuálního počítače podporovaná pro migraci na Azure Stack HCI verze 5,0. Proto musí být všechny virtuální počítače, na kterých běží verze 2,0, 3,0 nebo 4,0 v clusteru se systémem Windows Server 2016 nebo Windows Server 2019, aktualizovány na verzi 5,0 před migrací.

|Verze operačního systému|Verze virtuálního počítače|
|---|---|
|Windows Server 2008 SP1|2.0|
|Windows Server 2008 R2|3.0|
|Windows Server 2012|4,0|
|Windows Server 2012 R2|5,0|
|Windows Server 2016|8.0|
|Windows Server 2019|9.0|
|Azure Stack HCI|9.0|

Pro virtuální počítače na Windows serveru 2008 SP1, Windows Server 2008 R2-SP1 a Windows 2012 se nepodporuje přímá migrace na Azure Stack HCI. V těchto případech máte dvě možnosti:

- Nejprve migrujte tyto virtuální počítače na Windows Server 2012 R2, Windows Server 2016 nebo Windows Server 2019, aktualizujte verzi virtuálního počítače a pak zahajte proces migrace.

- Pomocí nástroje Robocopy zkopírujte všechny virtuální pevné disky virtuálních počítačů do Azure Stack HCI. Pak vytvořte nové virtuální počítače a připojte zkopírované virtuální pevné disky ke svým virtuálním počítačům v Azure Stack HCI. Tím se vynechá omezení verze virtuálních počítačů u těchto starších virtuálních počítačů.

## <a name="updating-the-vm-version"></a>Aktualizuje se verze virtuálního počítače.

Pomocí následujícího příkazu zobrazte všechny verze virtuálních počítačů na jednom serveru:

```powershell
Get-VM * | Format-Table Name,Version
```

Chcete-li zobrazit všechny verze virtuálních počítačů ve všech uzlech v clusteru s Windows serverem:

```powershell
Get-VM –ComputerName (Get-ClusterNode)
```

Aktualizace všech virtuálních počítačů na nejnovější verzi na všech uzlech Windows serveru:

```powershell
Get-VM –ComputerName (Get-ClusterNode) | Update-VMVersion -Force
```

## <a name="updating-the-servers-and-cluster"></a>Aktualizace serverů a clusteru

Migrace se skládá ze spuštěných Azure Stack nastavení HCI v nasazení Windows serveru pro čistou instalaci operačního systému s vašimi virtuálními počítači a úložištěm beze změny. Tím se nahradí aktuální operační systém Azure Stack HCL. Podrobné informace najdete v tématu [nasazení operačního systému Azure Stack HCI](operating-system.md). Následně vytvoříte nový cluster Azure Stack HCI, znovu připojíte úložiště a naimportujete virtuální počítače.

1. Vypněte stávající virtuální počítače s clustery, offline CSV, fondy úložiště offline a Clusterovou službu.

1. Přejít do umístění, kam jste stáhli Azure Stack HCI, a potom na každém uzlu Windows serveru spusťte instalaci Azure Stack HCI.

1. Během instalace vyberte **vlastní: instalovat novější verzi Azure Stack pouze HCI (rozšířené)**. Opakujte pro každý server.

1. Vytvořte nový cluster Azure Stack HCI. K tomu můžete použít Centrum pro správu systému Windows nebo prostředí Windows PowerShell, jak je popsáno níže.  

> [!IMPORTANT]
> Virtuální přepínač technologie Hyper-V ( `VMSwitch` ) musí mít stejný název, který je zachycený v inventáři konfigurace clusteru. Před importem virtuálních počítačů se ujistěte, že název virtuálního přepínače, který se používá v clusteru Azure Stack HCI, odpovídá původnímu názvu zdrojového virtuálního přepínače.

> [!NOTE]
> Než budete moct vytvořit nové virtuální počítače, musíte nejdřív zaregistrovat cluster Azure Stack HCI s Azure. Další informace najdete v tématu [registrace v Azure](register-with-azure.md).

### <a name="using-windows-admin-center"></a>Použití centra pro správu Windows

Pokud při vytváření clusteru Azure Stack HCI pomocí centra pro správu systému Windows, Průvodce vytvořením clusteru automaticky nainstaluje všechny požadované role a funkce na každý uzel serveru.

Podrobné informace o tom, jak vytvořit cluster, najdete v tématu [Vytvoření clusteru Azure Stack HCI pomocí centra pro správu systému Windows](create-cluster.md).

> [!IMPORTANT]
> V Průvodci vytvořením clusteru přeskočte krok **4,1 vyčistit jednotky** . V opačném případě odstraníte stávající virtuální počítače a úložiště.

1. Spusťte Průvodce vytvořením clusteru. Až se dostanete ke **kroku 4: úložiště**:

1. Přeskočte krok **4,1 vyčistit jednotky**. Tento postup neprovádějte.

1. Krok mimo průvodce.

1. Otevřete PowerShell a spuštěním následující rutiny vytvořte nové `Storagesubsystem Object` ID, znovu vyhledejte všechny skříně úložiště a přiřaďte čísla jednotek ses:

    ```powershell
    Enable-ClusterS2D -Verbose
    ```

    Pokud migrujete ze systému Windows Server 2016, vytvoří se tím také nový `ClusterperformanceHistory` svazek ReFS a přiřadí se ke skupině prostředků clusteru SDDC.

    Při migraci ze systému Windows Server 2019 Tato funkce také přidá existující `ClusterperformanceHistory` svazek ReFS a přiřadí ho ke skupině prostředků clusteru SDDC.

1. Vraťte se do průvodce. V kroku **4,2 ověření jednotek** ověřte, zda jsou všechny jednotky uvedeny bez varování nebo chyb.

1. Dokončete průvodce.

### <a name="using-windows-powershell"></a>Použití Windows PowerShellu

Pokud pomocí prostředí PowerShell vytvoříte cluster Azure Stack HCI, musí být na každém Azure Stack uzlu clusteru HCI nainstalovány následující role a funkce pomocí následující rutiny:

```powershell
Install-WindowsFeature -Name Hyper-V, Failover-Clustering, FS-Data-Deduplication, Bitlocker, Data-Center-Bridging, RSAT-AD-PowerShell -IncludeAllSubFeature -IncludeManagementTools -Verbose
```

Další informace o tom, jak vytvořit cluster pomocí prostředí PowerShell, najdete v tématu [Vytvoření clusteru Azure Stack HCI pomocí prostředí Windows PowerShell](create-cluster-powershell.md).

> [!NOTE]
> Znovu použijte stejný název pro dříve zakázaný objekt názvu clusteru.

1. Spuštěním následující rutiny vytvořte cluster:

    ```powershell
    New-cluster –name "clustername" –node Server01,Server02 –staticaddress xx.xx.xx.xx –nostorage
    ```

1. Spusťte následující rutinu, která vytvoří nové `Storagesubsystem Object` ID, znovu zjistí všechny skříně úložiště a přiřadí čísla jednotek ses:

    ```powershell
    Enable-ClusterS2D -Verbose
    ```

1. Pokud migrujete ze systému Windows Server 2016, vytvoří se tím také nový `ClusterperformanceHistory` svazek ReFS a přiřadí se ke skupině prostředků clusteru SDDC.

    > [!NOTE]
    > Pokud fond úložiště zobrazuje chyby minoritního disku (zobrazitelné ve Správci clusteru), spusťte `Enable-ClusterS2D -verbose` rutinu znovu.

1. Pomocí Správce clusterů povolte všechny sdílené svazky clusteru s výjimkou `ClusterperformanceHistory` svazku, který je svazkem ReFS (Ujistěte se, že se nejedná o soubor CSV s příponou ReFS).

1. Pokud migrujete ze systému Windows Server 2019, spusťte `Enable-ClusterS2D -verbose` rutinu znovu. Tím se přiřadí `ClusterperformanceHistory` svazek ReFS ke skupině prostředků clusteru SDDC.

1. Aktuální název a verzi fondu úložiště zjistíte spuštěním následujících kroků:

    ```powershell
    Get-StoragePool | ? IsPrimordial -eq $false | ft FriendlyName,Version
    ```

1. Teď určete název a verzi nového fondu úložiště:

    ```powershell
    Get-StoragePool | ? IsPrimordial -eq $false | ft FriendlyName,Version
    ```

1. Vytvořte určující disk kvora. Informace o tom, jak najdete v tématu [Nastavení určujícího clusteru](https://docs.microsoft.com/azure-stack/hci/deploy/witness).

1. Ověřte, zda jsou úlohy opravy úložiště dokončeny pomocí následujících kroků:

    ```powershell
    Get-StorageJob
    ```

    > [!NOTE]
    >To může trvat značnou dobu v závislosti na počtu virtuálních počítačů spuštěných během upgradu.

1. Ověřte, zda jsou všechny disky v pořádku:

    ```powershell
    Get-VirtualDisk
    ```

1. Určete verzi uzlu clusteru, která zobrazí `ClusterFunctionalLevel` a `ClusterUpgradeVersion` . K získání této akce spusťte následující příkaz:

    ```powershell
    Get-ClusterNodeSupportedVersion
    ```

    > [!NOTE]
    > `ClusterFunctionalLevel` je automaticky nastaven na `10` a nevyžaduje aktualizaci z důvodu nového operačního systému a vytvoření clusteru.

1. Aktualizujte fond úložiště následujícím způsobem:

    ```powershell
    Get-StoragePool | Update-StoragePool
    ```

## <a name="refs-volumes"></a>Svazky ReFS

Při migraci ze systému Windows Server 2016 jsou podporovány odolné svazky systému souborů (ReFS), ale tyto svazky nevyužívají následující vylepšení výkonu v Azure Stack HCI:

- Zrcadlení – urychlené parity
- Nepoužívat protokol mapy

Tato vylepšení vyžadují vytvoření nového svazku ReFS pomocí `New-Volume` rutiny.

> [!NOTE]
> Pro zrcadlené svazky s paritou systému Windows Server 2016 nebyly k dispozici komprimace ReFS, takže se tyto svazky znovu připojí, ale budou méně provedeny v porovnání s vytvořením nového svazku mapy v Azure Stack clusteru HCI.

## <a name="import-the-vms"></a>Import virtuálních počítačů

Osvědčeným postupem je vytvořit alespoň jeden sdílený svazek clusteru (CSV) na jeden uzel clusteru a umožnit tak rovnoměrné vyvážení virtuálních počítačů pro každého vlastníka CSV, aby se zvýšila odolnost, výkon a škálování úloh virtuálních počítačů. Ve výchozím nastavení se tento zůstatek automaticky objevuje každých pět minut a je potřeba ho zvážit při použití nástroje Robocopy mezi zdrojovým uzlem clusteru a cílovým uzlem, aby se zajistilo, že se zdrojové a cílové vlastníci sdílených svazků clusteru shodují, aby poskytovaly optimální cestu a rychlost přenosu.

Proveďte následující kroky v clusteru Azure Stack HCI pro import virtuálních počítačů, zpřístupněte je vysoce dostupné a spusťte je:

1. Spuštěním následující rutiny zobrazíte všechny uzly vlastníka sdíleného svazku clusteru:

    ```powershell
    Get-ClusterSharedVolume
    ```

1. Pro každý uzel serveru použijte `C:\Clusterstorage\Volume` příkaz a nastavte cestu pro všechny virtuální počítače – například `C:\Clusterstorage\volume01` .

1. Spusťte následující rutinu na každém uzlu vlastníka sdíleného svazku clusteru, abyste zobrazili cestu k souborům VMCX virtuálních počítačů na jeden svazek před importem virtuálního počítače. Upravte cestu tak, aby odpovídala vašemu prostředí:

    ```powershell
    Get-ChildItem -Path "C:\Clusterstorage\Volume01\*.vmcx" -Recurse
    ```

1. Spusťte následující rutinu pro každý uzel serveru pro import a registraci všech virtuálních počítačů a zpřístupněte je vysoce k dispozici na každém uzlu vlastníka sdíleného svazku clusteru. Tím je zajištěna sudá distribuce virtuálních počítačů pro optimální přidělení procesoru a paměti:

    ```powershell
    Get-ChildItem -Path "C:\Clusterstorage\Volume01\*.vmcx" -Recurse | Import-VM -Register | Get-VM | Add-ClusterVirtualMachineRole
    ```

1. Spusťte každý cílový virtuální počítač na každém uzlu:

    ```powershell
    Start-VM -Name
    ```

1. Přihlaste se a ověřte, že všechny virtuální počítače běží a že existují všechny vaše aplikace a data:

    ```powershell
    Get-VM -ComputerName Server01 | Where-Object {$_.State -eq 'Running'}
    ```

1. Nakonec aktualizujte své virtuální počítače na nejnovější verzi Azure Stack HCI, abyste mohli využít výhod všech výhod:

    ```powershell
    Get-VM | Update-VMVersion -Force
    ```

## <a name="next-steps"></a>Další kroky

- Po migraci ověřte cluster. Přečtěte si téma [ověření Azure Stack clusteru HCI](validate.md).
- Pokud chcete migrovat virtuální počítače s Windows serverem do nového Azure Stack hardwaru HCL, přečtěte si téma [migrace do Azure Stack HCI na novém hardwaru](migrate-cluster-new-hardware.md).