---
title: Správa kapacity úložiště v centru Azure Stack | Microsoft Docs
description: Naučte se monitorovat a spravovat kapacitu a dostupnost úložiště v Azure Stack hub.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: conceptual
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 03/19/2019
ms.openlocfilehash: 56c6ee17ed569689267bffc79aadece0e451897c
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76535242"
---
# <a name="manage-storage-capacity-for-azure-stack-hub"></a>Správa kapacity úložiště pro centrum Azure Stack

Informace v tomto článku vám pomohou monitorovat operátor cloudu Azure Stack hub a spravovat kapacitu úložiště nasazení Azure Stack centra. Infrastruktura úložiště centra Azure Stack přiděluje podmnožinu celkové úložné kapacity nasazení centra Azure Stack, která se má použít pro **služby úložiště**. Služby úložiště ukládají data tenantů do sdílených složek na svazcích, které odpovídají uzlům nasazení.

Jako operátor cloudu máte k dispozici omezené množství úložiště, se kterým pracujete. Velikost úložiště je definována řešením, které implementujete. Vaše řešení zajišťuje dodavatel OEM, když používáte řešení s více uzly, nebo hardware, na kterém instalujete Azure Stack Development Kit (ASDK).

Vzhledem k tomu, že centrum Azure Stack nepodporuje rozšíření kapacity úložiště, je důležité [monitorovat](#monitor-shares) dostupné úložiště, aby se zajistilo zachování efektivních operací.

Když se zbývající volná kapacita sdíleného prostředku omezí, naplánujte [správu prostoru](#manage-available-space) , aby nedocházelo k nedostatku kapacity sdílených složek.

Mezi možnosti správy kapacity patří:
- Uvolnit kapacitu
- Migrace kontejneru

Pokud je sdílená složka 100%, služba úložiště pro tuto sdílenou složku přestane fungovat. Pokud chcete získat pomoc při obnovování operací pro sdílenou složku, obraťte se na podporu Microsoftu.

## <a name="understand-volumes-and-shares-containers-and-disks"></a>Porozumění svazkům a sdíleným složkám, kontejnerům a diskům
### <a name="volumes-and-shares"></a>Svazky a sdílené složky
*Služba úložiště* rozdělí dostupné úložiště na samostatné a stejné svazky, které jsou přiděleny pro uchovávání dat klientů. Počet svazků se rovná počtu uzlů v nasazení centra Azure Stack:

- V nasazení se čtyřmi uzly jsou čtyři svazky. Každý svazek má jednu sdílenou složku. Při nasazení s více uzly se počet sdílených složek nesníží, pokud dojde k odebrání nebo selhání uzlu.
- Pokud používáte ASDK, existuje jeden svazek s jednou sdílenou složkou.

Vzhledem k tomu, že sdílené složky úložiště jsou pro exkluzivní použití služeb úložiště, nesmíte přímo upravovat, přidávat ani odebírat žádné soubory ve sdílených složkách. Na souborech uložených na těchto svazcích by měly fungovat jenom služby úložiště.

Sdílené složky na svazcích uchovávají data tenanta. Data tenanta zahrnují objekty blob stránky, objekty blob bloku, doplňovací objekty blob, tabulky, fronty, databáze a související úložiště metadat. Vzhledem k tomu, že objekty úložiště (objekty blob atd.) jsou jednotlivě obsaženy v rámci jedné sdílené složky, maximální velikost každého objektu nemůže být větší než velikost sdílené složky. Maximální velikost nových objektů závisí na kapacitě, která zůstává ve sdílené složce jako nevyužité místo při vytvoření nového objektu.

Když je u sdílené složky nedostatek volného místa a [akce pro uvolnění](#reclaim-capacity) místa není úspěšná nebo nedostupná, může operátor cloudu Azure Stackového centra migrovat kontejnery objektů BLOB z jedné sdílené složky do jiné.

- Informace o tom, jak uživatelé klienta pracují s úložištěm objektů BLOB v Azure Stackovém centru, najdete v tématu [služby úložiště Azure Stack hub](/azure-stack/user/azure-stack-storage-overview#azure-stack-storage-services).


### <a name="containers"></a>Kontejnery
Uživatelé tenanta vytvářejí kontejnery, které se pak používají k ukládání dat objektů BLOB. I když se uživatel rozhodne, do kterého kontejneru umístit objekty blob, služba úložiště pomocí algoritmu určí, který svazek se má vložit do kontejneru. Algoritmus obvykle zvolí svazek s největším množstvím dostupného místa.  

Po umístění objektu blob do kontejneru může tento objekt BLOB zvětšit použití více místa. Při zvětšení nových objektů BLOB a zmenšení stávajících objektů BLOB se zmenší dostupné místo ve svazku, který tento kontejner obsahuje.  

Kontejnery nejsou omezeny pouze na jednu sdílenou složku. Když se kombinovaná data objektů BLOB v kontejneru zvětšují o použití 80% nebo více dostupného místa, kontejner přejde do režimu *přetečení* . V režimu přetečení jsou všechny nové objekty blob vytvořené v tomto kontejneru přiděleny na jiný svazek, který má dostatek místa. V průběhu času může kontejner v režimu přetečení obsahovat objekty blob distribuované napříč více svazky.

Když 80% (a pak 90%) v případě, že se používá dostupné místo ve svazku, vyvolá systém výstrahy na portálu pro správu centra Azure Stack. Operátoři cloudu by měli zkontrolovat dostupnou kapacitu úložiště a naplánovat, aby se obsah znovu vyrovnal. Služba úložiště přestane fungovat, když se používá disk 100% a nejsou vyvolány žádné další výstrahy.

### <a name="disks"></a>Disky
Disky virtuálních počítačů se přidávají do kontejnerů v klientech a zahrnují disk s operačním systémem. Virtuální počítače mohou mít také jeden nebo více datových disků. Oba typy disků se ukládají jako objekty blob stránky. Pro zvýšení výkonu virtuálního počítače je potřeba umístit jednotlivé disky do samostatného kontejneru a zajistit tak jejich pomoc.

- Každý kontejner, který obsahuje disk (objekt blob stránky) z virtuálního počítače, se považuje za připojený kontejner k virtuálnímu počítači, který je vlastníkem disku.
- Kontejner, který nedrží žádný disk z virtuálního počítače, se považuje za bezplatný kontejner.

Možnosti uvolnění místa na připojeném kontejneru [jsou omezené](#move-vm-disks).

>[!TIP]  
> Operátoři cloudu nespravují přímo disky, které jsou připojené k virtuálním počítačům, které můžou klienti přidávat do kontejneru. Když ale naplánujete správu místa na sdílených složkách úložiště, může být k pochopení, jak disky souvisejí s kontejnery a sdílenými složkami, použít.

## <a name="monitor-shares"></a>Monitorovat sdílené složky
Pomocí PowerShellu nebo portálu pro správu monitorujte sdílené složky, abyste mohli pochopit, kdy je volné místo omezené. Když použijete portál, dostanete výstrahy o sdílených složkách, které mají nedostatek místa.

### <a name="use-powershell"></a>Použití PowerShellu
Jako operátor cloudu můžete sledovat kapacitu úložiště sdílené složky pomocí rutiny PowerShellu **Get-AzsStorageShare** . Rutina Get-AzsStorageShare vrátí celkovou, přidělenou a volnou velikost v bajtech na každé sdílené složky.

![Příklad: vrácení volného místa pro sdílené složky](media/azure-stack-manage-storage-shares/free-space.png)

- **Celková kapacita** je celkové místo v bajtech, které jsou k dispozici ve sdílené složce. Tento prostor se používá pro data a metadata, která jsou spravovaná službami úložiště.
- **Využitá kapacita** je množství dat v bajtech, které se používá u všech rozsahů ze souborů, které ukládají data tenanta a přidružená metadata.

### <a name="use-the-administrator-portal"></a>Použití portálu pro správu
Jako operátor cloudu můžete pomocí portálu pro správu zobrazit kapacitu úložiště pro všechny sdílené složky.

1. Přihlaste se k [portálu pro správu](https://adminportal.local.azurestack.external).
2. Výběrem položky **všechny služby** > **úložiště** > **sdílené složky** otevřete seznam sdílení souborů, kde můžete zobrazit informace o použití.

    ![Příklad: úložiště sdílené složky na portálu pro správu centra Azure Stack](media/azure-stack-manage-storage-shares/storage-file-shares.png)

   - **Total** je celkové místo v bajtech, které jsou k dispozici ve sdílené složce. Tento prostor se používá pro data a metadata, která jsou spravovaná službami úložiště.
   - **Používá** se množství dat v bajtech, které se používá v rozsahu ze souborů, které ukládají data tenanta a přidružená metadata.

### <a name="storage-space-alerts"></a>Výstrahy prostoru úložiště
Když použijete portál pro správu, zobrazí se upozornění na sdílené složky, které mají nedostatek místa.

> [!IMPORTANT]
> Jako operátor cloudu Udržujte sdílené složky, abyste dosáhli plného využití. Pokud je sdílená složka 100%, služba úložiště pro tuto sdílenou složku přestane fungovat. Chcete-li obnovit volné místo a operace obnovení ve sdílené složce, která je 100%, je nutné se obrátit na podporu společnosti Microsoft.

**Upozornění**: Pokud je sdílená složka větší než 80%, obdržíte *Upozornění* na portálu pro správu:

![Příklad: výstražné upozornění na portálu pro správu centra Azure Stack](media/azure-stack-manage-storage-shares/alert-warning.png)

**Kritické**: Pokud je sdílená složka větší než 90%, obdržíte *kritickou* výstrahu na portálu pro správu:

![Příklad: kritická výstraha v portálu pro správu centra Azure Stack](media/azure-stack-manage-storage-shares/alert-critical.png)

**Zobrazit podrobnosti**: na portálu pro správu můžete otevřít podrobnosti výstrahy pro zobrazení možností zmírnění rizika: ![příklad: zobrazení podrobností o výstrahách na portálu pro správu Azure Stack centra](media/azure-stack-manage-storage-shares/alert-details.png)

## <a name="manage-available-space"></a>Spravovat dostupné místo
Pokud je nutné uvolnit místo na sdílené složce, použijte nejprve nejméně invazivní metody. Například zkuste uvolnit místo před tím, než se rozhodnete migrovat kontejner.  

### <a name="reclaim-capacity"></a>Uvolnit kapacitu
*Tato možnost se vztahuje na nasazení ve více uzlech i na Azure Stack Development Kit.*

Můžete uvolnit kapacitu, kterou používají účty klientů, které byly odstraněny. Tato kapacita se automaticky uvolní při dosažení [doby uchovávání](azure-stack-manage-storage-accounts.md#set-the-retention-period) dat, nebo se můžete chovat, aby se okamžitě znovu vyzpůsobila.

Další informace najdete v tématu věnovaném [uvolnění kapacity](azure-stack-manage-storage-accounts.md#reclaim) v tématu Správa prostředků úložiště.

### <a name="migrate-a-container-between-volumes"></a>Migrace kontejneru mezi svazky
*Tato možnost se vztahuje jenom na systémy integrované s Azure Stack hub.*

Vzhledem k tomu, že jsou vzory využití tenanta, můžou některé sdílené složky tenanta používat víc místa než jiné. Výsledkem může být sdílená složka, ve které je málo volného místa, než se vybírají nevyužité sdílené složky.

Místo na nevyužité sdílené složce můžete uvolnit tak, že ručně migrujete některé kontejnery objektů blob do jiné sdílené složky. Můžete migrovat několik menších kontejnerů na jednu sdílenou složku, která má kapacitu uchovat vše. K přesunu *volných* kontejnerů použijte migraci. Volné kontejnery jsou kontejnery, které neobsahují disk pro virtuální počítač.

Migrace slučuje všechny objekty blob kontejneru v nové sdílené složce.

- Pokud kontejner přešel do režimu přetečení a umístil objekty blob na další svazky, musí mít Nová sdílená složka dostatečnou kapacitu pro uložení všech objektů BLOB pro kontejner, který migrujete. To zahrnuje objekty blob, které se nacházejí v dalších sdílených složkách.

- Rutina PowerShellu *Get-AzsStorageContainer* identifikuje jenom používané místo na počátečním svazku pro kontejner. Rutina neidentifikuje prostor, který používají objekty blob, které jsou umístěny na dalších svazcích. Proto nemusí být úplná velikost kontejneru zřejmá. Je možné, že konsolidace kontejneru v nové sdílené složce může poslat tuto novou sdílenou složku do stavu přetečení, kde data umísťují do dalších sdílených složek. V důsledku toho může být nutné znovu vyvážit sdílené složky.

- Pokud nemáte oprávnění ke skupině prostředků a nemůžete použít PowerShell k dotazování dalších svazků na data přetečení, pracujte s vlastníkem těchto skupin prostředků a kontejnerů, abyste pochopili celkovou velikost dat, která se mají migrovat, před migrací těchto dat.  

> [!IMPORTANT]
> Migrace objektů BLOB pro kontejner je offline operace, která vyžaduje použití PowerShellu. Dokud nebude migrace dokončená, všechny objekty blob pro kontejner, které migrujete, zůstávají offline a nejde je použít. Měli byste se také vyhnout upgradu centra Azure Stack, dokud se nedokončí veškerá probíhající migrace.

#### <a name="to-migrate-containers-using-powershell"></a>Migrace kontejnerů pomocí PowerShellu
1. Potvrďte, že máte [nainstalovanou a nakonfigurovanou Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/). Další informace najdete v tématu [Použití Azure PowerShellu s Azure Resource Managerem](https://go.microsoft.com/fwlink/?LinkId=394767).
2. Projděte si kontejner, abyste pochopili, jaká data jsou ve sdílené složce, kterou plánujete migrovat. Pokud chcete identifikovat nejlepší kandidáty pro migraci ve svazku, použijte rutinu **Get-AzsStorageContainer** :

   ```powershell  
   $farm_name = (Get-AzsStorageFarm)[0].name
   $shares = Get-AzsStorageShare -FarmName $farm_name
   $containers = Get-AzsStorageContainer -ShareName $shares[0].ShareName -FarmName $farm_name
   ```
   Pak prověřte $containers:

   ```powershell
   $containers
   ```

   ![Příklad: $Containers](media/azure-stack-manage-storage-shares/containers.png)

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

5. Ke kontrole stavu úlohy migrace použijte ID úlohy. Po dokončení migrace kontejneru je **MigrationStatus** nastaveno na **dokončeno**.

   ```powershell 
   Get-AzsStorageContainerMigrationStatus -JobId $job_id -FarmName $farm_name
   ```

   ![Příklad: stav migrace](media/azure-stack-manage-storage-shares/migration-status1.png)

6. Probíhající úlohu migrace můžete zrušit. Zrušené úlohy migrace se zpracovávají asynchronně. Zrušení můžete sledovat pomocí $jobid:

   ```powershell
   Stop-AzsStorageContainerMigration -JobId $job_id -FarmName $farm_name
   ```

   ![Příklad: vrácení stavu zpět](media/azure-stack-manage-storage-shares/rollback.png)

7. Příkaz můžete spustit z kroku 6 znovu, dokud stav nepotvrdí, že se úloha migrace **zruší**:  

    ![Příklad: zrušený stav](media/azure-stack-manage-storage-shares/cancelled.png)

### <a name="move-vm-disks"></a>Přesunutí disků virtuálního počítače
*Tato možnost se vztahuje jenom na systémy integrované s Azure Stack hub.*

Největší způsob, jak spravovat prostor, zahrnuje přesun disků virtuálních počítačů. Vzhledem k tomu, že přesun připojeného kontejneru (ten, který obsahuje disk virtuálního počítače) je složitý, kontaktujte podpora Microsoftu k provedení této akce.

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [nabídce virtuálních počítačů pro uživatele](azure-stack-tutorial-tenant-vm.md).
