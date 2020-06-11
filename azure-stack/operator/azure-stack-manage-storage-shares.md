---
title: Správa kapacity úložiště v centru Azure Stack
description: Naučte se monitorovat a spravovat kapacitu a dostupnost úložiště v Azure Stack hub.
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 1/22/2020
ms.author: inhenkel
ms.reviewer: xiaofmao
ms.lastreviewed: 03/19/2019
ms.openlocfilehash: ecac1c8c69a8f332a85bf0a934f688f14dbcaddd
ms.sourcegitcommit: 6306e0c2506106ad01ff50010f36466f3325d0a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/09/2020
ms.locfileid: "84631000"
---
# <a name="manage-storage-capacity-for-azure-stack-hub"></a>Správa kapacity úložiště pro centrum Azure Stack

Tento článek pomáhá Azure Stack operátory cloudového cloudu monitorovat a spravovat kapacitu úložiště nasazení Azure Stack hub. Infrastruktura úložiště Azure Stack hub přiděluje podmnožinu celkové kapacity úložiště nasazení Azure Stack centra jako služby úložiště. Služba úložiště ukládá data tenanta do sdílených složek na svazcích, které odpovídají uzlům nasazení.

Jako operátor cloudu máte k dispozici omezené množství úložiště, se kterým pracujete. Velikost úložiště je definována řešením, které implementujete. Řešení zajišťuje dodavatel OEM při použití řešení s více uzly, nebo je poskytováno hardwarem, na kterém instalujete Azure Stack Development Kit (ASDK).

Vzhledem k tomu, že centrum Azure Stack nepodporuje rozšíření kapacity úložiště, je důležité [monitorovat](#monitor-shares) dostupné úložiště, aby se zajistilo zachování efektivních operací.

Když se zbývající volná kapacita svazku omezí, naplánujte [správu dostupného místa](#manage-available-space) , aby sdílené složky nedocházelo k nedostatku kapacity.

Mezi možnosti správy kapacity patří:
- Kapacita se uvolňuje.
- Migrace objektů úložiště.

Pokud je svazek úložiště objektů využíván 100%, služba Storage již pro tento svazek nefunguje. Pokud potřebujete pomoc s obnovením provozu pro daný svazek, obraťte se na podporu Microsoftu.

## <a name="understand-volumes-and-shares-containers-and-disks"></a>Porozumění svazkům a sdíleným složkám, kontejnerům a diskům
### <a name="volumes-and-shares"></a>Svazky a sdílené složky
*Služba úložiště* rozdělí dostupné úložiště na samostatné, stejné svazky, které jsou přidělené pro uchovávání dat klientů. Další informace o svazcích v centru Azure Stack najdete v tématu [Správa infrastruktury úložiště pro centrum Azure Stack](azure-stack-manage-storage-infrastructure.md).

Svazky úložiště objektů uchovávají data tenanta. Data tenanta zahrnují objekty blob stránky, objekty blob bloku, doplňovací objekty blob, tabulky, fronty, databáze a související úložiště metadat. Počet svazků úložiště objektů se rovná počtu uzlů v nasazení centra Azure Stack:

- Při nasazení se čtyřmi uzly jsou k dispozici čtyři svazky úložiště objektů. Při nasazení s více uzly se počet svazků nesníží, pokud dojde k odebrání nebo selhání uzlu.
- Pokud používáte ASDK, existuje jeden svazek s jednou sdílenou složkou.

Svazky úložiště objektů jsou pro výhradní použití služeb úložiště. Nesmíte přímo upravovat, přidávat ani odebírat žádné soubory na svazcích. Na souborech uložených na těchto svazcích by měly fungovat jenom služby úložiště.

Vzhledem k tomu, že objekty úložiště (objekty blob atd.) jsou jednotlivě obsaženy v jednom svazku, maximální velikost každého objektu nemůže být větší než velikost svazku. Maximální velikost nových objektů závisí na kapacitě, která zůstává ve svazku jako nevyužité místo při vytvoření nového objektu.

Když je na svazku úložiště objektů nedostatek volného místa a [akce pro uvolnění](#reclaim-capacity) místa není úspěšná nebo nedostupná, Azure Stack operátoři cloudového centra můžou migrovat objekty úložiště z jednoho svazku do druhého.

Informace o tom, jak uživatelé klienta pracují s úložištěm objektů BLOB v Azure Stackovém centru, najdete v tématu [služby úložiště Azure Stack hub](/azure-stack/user/azure-stack-storage-overview).

### <a name="containers"></a>Containers
Uživatelé tenanta vytvářejí kontejnery, které se pak používají k ukládání dat objektů BLOB. I když se uživatelé rozhodují, ve kterém kontejneru umístit objekty blob, služba úložiště pomocí algoritmu určuje, který svazek se má vložit do kontejneru. Algoritmus obvykle zvolí svazek s největším množstvím dostupného místa.  

Po umístění objektu blob do kontejneru může objekt BLOB zvětšit použití více místa. Při zvětšení nových objektů BLOB a zmenšení stávajících objektů BLOB se zmenší dostupné místo ve svazku, který obsahuje kontejner. 

Kontejnery nejsou omezeny na jeden svazek. Když se kombinovaná data objektů BLOB v kontejneru zvětšují o použití 80% nebo více dostupného místa, kontejner přejde do režimu *přetečení* . V režimu přetečení jsou všechny nové objekty blob vytvořené v tomto kontejneru přiděleny na jiný svazek, který má dostatek místa. V průběhu času může kontejner v režimu přetečení obsahovat objekty blob distribuované napříč více svazky.

Když 80% (a pak 90%) v případě, že se používá dostupné místo ve svazku, vyvolá systém [výstrahy](#storage-space-alerts) na portálu pro správu centra Azure Stack. Operátoři cloudu by měli zkontrolovat dostupnou kapacitu úložiště a naplánovat, aby se obsah znovu vyrovnal. Služba úložiště přestane fungovat, když se používá disk 100% a nejsou vyvolány žádné další výstrahy.

### <a name="disks"></a>Disky
Centrum Azure Stack podporuje na virtuálních počítačích použití spravovaných disků a nespravovaných disků, jako je operační systém (OS) i datový disk.

**Spravované disky** zjednodušují správu disků pro virtuální počítače Azure IaaS tím, že spravují účty úložiště přidružené k DISKŮM virtuálních počítačů. Stačí zadat velikost disku, kterou potřebujete, a Azure Stack rozbočovač vytvoří a spravuje disk za vás. Další informace najdete v tématu [přehled Managed disks](/azure/virtual-machines/windows/managed-disks-overview).

Pro snazší správu a rovnováhu kapacity se doporučuje použít Managed Disks pro virtuální počítač. Před použitím Managed Disks nemusíte připravovat účet úložiště a kontejnery. Při vytváření více spravovaných disků jsou disky distribuovány do více svazků, což pomáhá vyrovnávat kapacitu svazků.  

**Nespravované disky** jsou soubory VHD, které se ukládají jako objekty blob stránky v účtech úložiště Azure. Objekty blob stránky vytvořené klienty se označují jako disky virtuálních počítačů a ukládají se do kontejnerů v účtech úložiště. Nespravované disky doporučujeme používat jenom pro virtuální počítače, které potřebují kompatibilní s nástroji třetích stran. podporují jenom nespravované disky Azure.

Pro zvýšení výkonu virtuálního počítače je potřeba umístit jednotlivé disky do samostatného kontejneru a zajistit tak jejich pomoc.

- Každý kontejner, který obsahuje disk nebo objekt blob stránky, se z virtuálního počítače považuje za připojený kontejner k virtuálnímu počítači, který je vlastníkem disku.
- Kontejner, který nedrží žádné disky z virtuálního počítače, se považuje za bezplatný kontejner.

Možnosti uvolnění místa na připojeném kontejneru jsou omezené. Další informace najdete v tématu [distribuce nespravovaných disků](#distribute-unmanaged-disks).

>[!TIP]  
> Operátoři cloudu nemůžou přímo provozovat nespravované disky, které jsou připojené k virtuálním počítačům, které můžou klienti přidat do kontejneru. Pokud ale plánujete spravovat místo na sdílených složkách úložiště, může být užitečné pochopit, jak se nespravované disky vztahují k kontejnerům a sdíleným složkám.

::: moniker range="<azs-2002"
## <a name="monitor-shares"></a>Monitorovat sdílené složky
Pomocí Azure PowerShell nebo portálu pro správu monitorujte sdílené složky, abyste mohli pochopit, kdy je volné místo omezené. Když použijete portál, dostanete výstrahy o sdílených složkách, které mají nedostatek místa.

### <a name="use-powershell"></a>Použití prostředí PowerShell
Jako operátor cloudu můžete sledovat kapacitu úložiště sdílené složky pomocí `Get-AzsStorageShare` rutiny prostředí PowerShell. Rutina vrátí celkové, přidělené a volné místo v bajtech na každé sdílené složky.

![Příklad: vrácení volného místa pro sdílené složky](media/azure-stack-manage-storage-shares/free-space.png)

- **Celková kapacita**: celkové místo (v bajtech), které je k dispozici na sdílené složce. Tento prostor se používá pro data a metadata, která jsou spravovaná službami úložiště.
- **Využitá kapacita**: objem dat (v bajtech), který se používá pro všechny rozsahy ze souborů, které ukládají data tenanta a přidružená metadata.

### <a name="use-the-administrator-portal"></a>Použití portálu pro správu
Jako operátor cloudu můžete pomocí portálu pro správu zobrazit kapacitu úložiště pro všechny sdílené složky.

1. Přihlaste se k portálu pro správu `https://adminportal.local.azurestack.external` .
2. Výběrem položky **všechny služby** > **úložiště** > **sdílené složky** otevřete seznam sdílení souborů, kde můžete zobrazit informace o použití.

    ![Příklad: úložiště sdílené složky na portálu pro správu centra Azure Stack](media/azure-stack-manage-storage-shares/storage-file-shares.png)

   - **Total (celkem**): celkový prostor (v bajtech), který je k dispozici na sdílené složce. Tento prostor se používá pro data a metadata, která jsou spravovaná službami úložiště.
   - **Používá**se: množství dat (v bajtech), které se používají ve všech rozsahu ze souborů, které ukládají data tenanta a přidružená metadata.

::: moniker-end
::: moniker range=">=azs-2002"

## <a name="monitor-volumes"></a>Monitorovat svazky
Pomocí PowerShellu nebo portálu pro správu můžete monitorovat svazky, abyste mohli pochopit, kdy je volné místo omezené. Při použití portálu obdržíte výstrahy týkající se svazků, které mají nedostatek místa.

### <a name="use-powershell"></a>Použití prostředí PowerShell
Jako operátor cloudu můžete sledovat kapacitu úložiště svazku pomocí `Get-AzsVolume` rutiny prostředí PowerShell. Rutina vrátí celkové a volné místo v gigabajtech (GB) na každém svazku.

![Příklad: vrácení volného místa pro svazky](media/azure-stack-manage-storage-shares/listvolumespowershell.png)

- **Celková kapacita**: celkové místo v GB, které je ve sdílené složce k dispozici. Tento prostor se používá pro data a metadata, která jsou spravovaná službami úložiště.
- **Zbývající kapacita**: množství místa v GB, které je zdarma pro uložení dat tenanta a přidružených metadat.

### <a name="use-the-administrator-portal"></a>Použití portálu pro správu
Jako operátor cloudu můžete pomocí portálu pro správu zobrazit kapacitu úložiště pro všechny svazky.

1. Přihlaste se k portálu správce centra Azure Stack ( `https://adminportal.local.azurestack.external` ).
2. Výběrem **všech služeb** > **úložiště** > **svazků** otevřete seznam svazků, kde můžete zobrazit informace o použití.

    ![Příklad: svazky úložiště na portálu pro správu centra Azure Stack](media/azure-stack-manage-storage-shares/listvolumes.png)

   - **Total (celkem**): celkové místo k dispozici na svazku. Tento prostor se používá pro data a metadata, která jsou spravovaná službami úložiště.
   - **Používá**se: množství dat, která jsou využívána všemi rozsahy ze souborů, které ukládají data tenanta a přidružená metadata.

::: moniker-end

### <a name="storage-space-alerts"></a>Výstrahy prostoru úložiště
Když použijete portál pro správu, zobrazí se upozornění na svazky, které mají nedostatek místa.

> [!IMPORTANT]
> Jako operátor cloudu byste měli zabránit tomu, aby sdílené složky dosáhly plného využití. Pokud je sdílená složka 100%, služba úložiště pro tuto sdílenou složku přestane fungovat. Chcete-li obnovit volné místo a operace obnovení ve sdílené složce, která je 100%, je třeba se obrátit na podporu společnosti Microsoft.

* **Upozornění**: Pokud je sdílená složka větší než 80%, obdržíte *Upozornění* na portálu pro správu:

  ![Příklad: výstražné upozornění na portálu pro správu centra Azure Stack](media/azure-stack-manage-storage-shares/alert-warning.png)

* **Kritické**: Pokud je sdílená složka větší než 90%, obdržíte *kritickou* výstrahu na portálu pro správu:

  ![Příklad: kritická výstraha na portálu pro správu centra Azure Stack](media/azure-stack-manage-storage-shares/alert-critical.png)

* **Zobrazit podrobnosti**: na portálu pro správu můžete otevřít podrobnosti výstrahy pro zobrazení možností zmírnění rizika:

  ![Příklad: zobrazení podrobností o výstrahách na portálu pro správu centra Azure Stack](media/azure-stack-manage-storage-shares/alert-details.png)

## <a name="manage-available-space"></a>Správa dostupného místa
Pokud je nutné uvolnit místo na svazku, použijte nejprve nejméně invazivní metody. Například zkuste uvolnit místo před tím, než se rozhodnete migrovat spravovaný disk.  

### <a name="reclaim-capacity"></a>Uvolnit kapacitu

Můžete uvolnit kapacitu, kterou používají účty klientů, které byly odstraněny. Tato kapacita se automaticky uvolní při dosažení [doby uchovávání](azure-stack-manage-storage-accounts.md#set-the-retention-period) dat, nebo se můžete chovat, aby se okamžitě znovu vyzpůsobila.

Další informace najdete v části "získání kapacity" v tématu [Správa účtů úložiště Azure Stack hub](azure-stack-manage-storage-accounts.md#reclaim).

::: moniker range="<azs-1910"

### <a name="migrate-a-container-between-volumes"></a>Migrace kontejneru mezi svazky
*Tato možnost se vztahuje jenom na systémy integrované s Azure Stack hub.*

Vzhledem k tomu, že jsou vzory využití tenanta, můžou některé sdílené složky tenanta používat víc místa než jiné. V důsledku toho může dojít k tomu, že některé sdílené složky mají málo místa než jiné nevyužité sdílené složky.

Místo na nevyužité sdílené složce můžete uvolnit tak, že ručně migrujete některé kontejnery objektů blob do jiné sdílené složky. Můžete migrovat několik menších kontejnerů na jednu sdílenou složku, která má kapacitu uchovat vše. K přesunu *volných* kontejnerů použijte migraci. Volné kontejnery jsou kontejnery, které neobsahují disk pro virtuální počítač.

Migrace slučuje všechny objekty blob kontejneru v nové sdílené složce.

- Pokud kontejner přešel do režimu přetečení a umístil objekty blob na další svazky, musí mít Nová sdílená složka dostatečnou kapacitu pro uložení všech objektů BLOB pro kontejner, který migrujete. To zahrnuje objekty blob, které se nacházejí v dalších sdílených složkách.

- Rutina PowerShellu `Get-AzsStorageContainer` identifikuje jenom používané místo na počátečním svazku pro kontejner. Rutina neidentifikuje prostor, který používají objekty blob, které jsou umístěny na dalších svazcích. Proto nemusí být úplná velikost kontejneru zřejmá. Je možné, že konsolidace kontejneru v nové sdílené složce může poslat tuto novou sdílenou složku do podmínky přetečení, kde data umístí do dalších sdílených složek. V důsledku toho může být nutné znovu vyvážit sdílené složky.

- Pokud nemáte oprávnění k určitým skupinám prostředků a nemůžete použít PowerShell k dotazování dalších svazků na data přetečení, pracujte s vlastníkem těchto skupin prostředků a kontejnerů, abyste porozuměli celkovému množství dat, která se mají migrovat, než je migrujete.  

> [!IMPORTANT]
> Migrace objektů BLOB pro kontejner je offline operace, která vyžaduje použití PowerShellu. Dokud nebude migrace dokončená, všechny objekty blob pro kontejner, které migrujete, zůstávají offline a nejde je použít. Měli byste se také vyhnout upgradu centra Azure Stack, dokud nebude dokončena veškerá nepřetržitá migrace.

#### <a name="migrate-containers-by-using-powershell"></a>Migrace kontejnerů pomocí PowerShellu
1. Potvrďte, že máte [nainstalovanou a nakonfigurovanou Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/). Další informace najdete v tématu [Správa prostředků Azure pomocí Azure PowerShell](https://go.microsoft.com/fwlink/?LinkId=394767).
2. Projděte si kontejner, abyste pochopili, jaká data jsou ve sdílené složce, kterou plánujete migrovat. K určení nejlepšího kontejneru kandidátů pro migraci ve svazku použijte `Get-AzsStorageContainer` rutinu:

   ```powershell  
   $farm_name = (Get-AzsStorageFarm)[0].name
   $shares = Get-AzsStorageShare -FarmName $farm_name
   $containers = Get-AzsStorageContainer -ShareName $shares[0].ShareName -FarmName $farm_name
   ```
   Pak prověřte $containers:

   ```powershell
   $containers
   ```

   ![Příklad: $containers](media/azure-stack-manage-storage-shares/containers.png)

3. Identifikujte nejlepší cílové sdílené složky pro uložení kontejneru, který migrujete:

   ```powershell
   $destinationshare = ($shares | Sort-Object FreeCapacity -Descending)[0]
   ```

   Pak prověřte $destinationshares:

   ```powershell 
   $destinationshares
   ```

   ![Příklad: $destination sdílené složky](media/azure-stack-manage-storage-shares/examine-destinationshares.png)

4. Spustí migraci kontejneru. Migrace je asynchronní. Pokud zahájíte migraci dalších kontejnerů před dokončením první migrace, použijte ID úlohy ke sledování stavu každé z nich.

   ```powershell
   $job_id = Start-AzsStorageContainerMigration -StorageAccountName $containers[0].Accountname -ContainerName $containers[0].Containername -ShareName $containers[0].Sharename -DestinationShareUncPath $destinationshares[0].UncPath -FarmName $farm_name
   ```

   Pak prověřte $jobId. V následujícím příkladu nahraďte *D62F8F7A-8B46-4F59-A8AA-5DB96DB4EBB0* ID úlohy, kterou chcete prošetřit:

   ```powershell
   $jobId
   d62f8f7a-8b46-4f59-a8aa-5db96db4ebb0
   ```

5. Ke kontrole stavu úlohy migrace použijte ID úlohy. Po dokončení migrace kontejneru je **MigrationStatus** nastaveno na *dokončeno*.

   ```powershell 
   Get-AzsStorageContainerMigrationStatus -JobId $job_id -FarmName $farm_name
   ```

   ![Příklad: stav migrace](media/azure-stack-manage-storage-shares/migration-status1.png)

6. Probíhající úlohu migrace můžete zrušit. Zrušené úlohy migrace se zpracovávají asynchronně. Zrušení můžete sledovat pomocí $jobid:

   ```powershell
   Stop-AzsStorageContainerMigration -JobId $job_id -FarmName $farm_name
   ```

   ![Příklad: vrácení stavu zpět](media/azure-stack-manage-storage-shares/rollback.png)

7. Můžete znovu spustit příkaz z kroku 6, dokud nebude stav migrace *zrušený*:  

    ![Příklad: zrušený stav](media/azure-stack-manage-storage-shares/cancelled.png)

### <a name="move-vm-disks"></a>Přesunutí disků virtuálního počítače
*Tato možnost se vztahuje jenom na systémy integrované s Azure Stack hub.*

Největší způsob, jak spravovat prostor, zahrnuje přesun disků virtuálních počítačů. Vzhledem k tomu, že přesun připojeného kontejneru (který obsahuje disk virtuálního počítače) je složitý, požádejte o tuto akci podporu Microsoftu.

::: moniker-end
::: moniker range=">=azs-1910"

### <a name="migrate-a-managed-disk-between-volumes"></a>Migrace spravovaného disku mezi svazky
*Tato možnost se vztahuje jenom na systémy integrované s Azure Stack hub.*

Vzhledem k tomu, že se vzory využití tenanta používají, některé svazky klientů využívají více místa než jiné. Výsledkem může být svazek, na kterém je málo volného místa, než je jiný svazek, který se bude relativně nepoužívá.

Můžete uvolnit místo na využívaném svazku tak, že ručně migrujete některé spravované disky na jiný svazek. Můžete migrovat několik spravovaných disků na jeden svazek, který má kapacitu pro udržení všech. Pomocí migrace přesuňte *offline* spravované disky. Offline spravované disky jsou disky, které nejsou připojené k virtuálnímu počítači.

> [!IMPORTANT]
> Migrace spravovaných disků je offline operace, která vyžaduje použití PowerShellu. Před spuštěním úlohy migrace musíte odpojit kandidátské disky pro migraci od svého virtuálního počítače vlastníka (po dokončení úlohy migrace je můžete znovu připojit). Dokud se migrace nedokončí, musí všechny spravované disky, které migrujete, zůstat offline a nebude je možné použít, jinak se úloha migrace zruší a všechny nemigrované disky budou pořád na svých původních svazcích. Měli byste se také vyhnout upgradu centra Azure Stack, dokud se nedokončí veškerá probíhající migrace.

#### <a name="to-migrate-managed-disks-using-powershell"></a>Migrace spravovaných disků pomocí prostředí PowerShell
1. Potvrďte, že máte nainstalovanou a nakonfigurovanou Azure PowerShell. Pokyny týkající se konfigurace prostředí PowerShellu najdete v tématu [instalace PowerShellu pro Azure Stack hub](azure-stack-powershell-install.md). Pokud se chcete přihlásit do centra Azure Stack, přečtěte si téma [Konfigurace prostředí operátorů a přihlášení do centra Azure Stack](azure-stack-powershell-configure-admin.md).
2. Projděte si Managed disks, abyste zjistili, jaké disky jsou na svazku, který plánujete migrovat. Chcete-li identifikovat nejlepší kandidáty na disky pro migraci ve svazku, použijte `Get-AzsDisk` rutinu:

   ```powershell  
   $ScaleUnit = (Get-AzsScaleUnit)[0]
   $StorageSubSystem = (Get-AzsStorageSubSystem -ScaleUnit $ScaleUnit.Name)[0]
   $Volumes = (Get-AzsVolume -ScaleUnit $ScaleUnit.Name -StorageSubSystem $StorageSubSystem.Name | Where-Object {$_.VolumeLabel -Like "ObjStore_*"})
   $SourceVolume  = ($Volumes | Sort-Object RemainingCapacityGB)[0]
   $VolumeName = $SourceVolume.Name.Split("/")[2]
   $VolumeName = $VolumeName.Substring($VolumeName.IndexOf(".")+1)
   $MigrationSource = "\\SU1FileServer."+$VolumeName+"\SU1_"+$SourceVolume.VolumeLabel
   $Disks = Get-AzsDisk -Status All -SharePath $MigrationSource | Select-Object -First 10
   ```
   Pak prověřte $disks:

   ```powershell
   $Disks
   ```

   ![Příklad: $Disks](media/azure-stack-manage-storage-shares/disks.png)

3. Identifikujte nejlepší cílový svazek, který bude obsahovat disky, které migrujete:

   ```powershell
   $DestinationVolume  = ($Volumes | Sort-Object RemainingCapacityGB -Descending)[0]
   $VolumeName = $DestinationVolume.Name.Split("/")[2]
   $VolumeName = $VolumeName.Substring($VolumeName.IndexOf(".")+1)
   $MigrationTarget = "\\SU1FileServer."+$VolumeName+"\SU1_"+$DestinationVolume.VolumeLabel
   ```

4. Spusťte migraci pro Managed disks. Migrace je asynchronní. Pokud zahájíte migraci dalších disků před dokončením první migrace, použijte název úlohy ke sledování stavu každé z nich.

   ```powershell
   $jobName = "MigratingDisk"
   Start-AzsDiskMigrationJob -Disks $Disks -TargetShare $MigrationTarget -Name $jobName
   ```

5. Ke kontrole stavu úlohy migrace použijte název úlohy. Po dokončení migrace disku se **MigrationStatus** nastaví na **dokončeno**.

   ```powershell 
   $job = Get-AzsDiskMigrationJob -Name $jobName
   ```

   ![Příklad: stav migrace](media/azure-stack-manage-storage-shares/diskmigrationjob.png)
   
   Pokud migrujete více spravovaných disků v rámci jedné úlohy migrace, můžete také kontrolovat dílčí úkoly úlohy.

   ```powershell 
   $job.Subtasks
   ```

   ![Příklad: stav dílčí úlohy migrace](media/azure-stack-manage-storage-shares/diskmigrationsubtask.png)

6. Probíhající úlohu migrace můžete zrušit. Zrušené úlohy migrace se zpracovávají asynchronně. Zrušení můžete sledovat pomocí názvu úlohy, dokud stav nepotvrdí, že se úloha migrace **zruší**:

   ```powershell
   Stop-AzsDiskMigrationJob -Name $jobName
   ```

   ![Příklad: zrušený stav](media/azure-stack-manage-storage-shares/diskmigrationstop.png)

### <a name="distribute-unmanaged-disks"></a>Distribuovat nespravované disky
*Tato možnost se vztahuje jenom na systémy integrované s Azure Stack hub.*

Největší způsob správy prostoru zahrnuje přesun nespravovaných disků. Pokud tenant přidá do jednoho kontejneru řadu nespravovaných disků, celková využitá kapacita kontejneru by mohla přesáhnout kapacitu svazku, která ji obsahuje, před vstupem do režimu *přetečení* kontejneru. Aby bylo možné zabránit jednomu kontejneru vyčerpání prostoru svazku, může klient distribuovat stávající nespravované disky jednoho kontejneru do různých kontejnerů. Vzhledem k tomu, že distribuce připojeného kontejneru (ten, který obsahuje disk virtuálního počítače) je komplexní, kontaktujte podpora Microsoftu k provedení této akce.

::: moniker-end

## <a name="next-steps"></a>Další kroky
Další informace o nabídce virtuálních počítačů pro uživatele najdete v tématu [Správa kapacity úložiště pro centrum Azure Stack](azure-stack-tutorial-tenant-vm.md).
