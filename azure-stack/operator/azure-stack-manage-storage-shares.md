---
title: Správa kapacity úložiště v centru Azure Stack
description: Naučte se monitorovat a spravovat kapacitu a dostupnost úložiště v Azure Stack hub.
author: mattbriggs
ms.topic: conceptual
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 03/19/2019
ms.openlocfilehash: b97d8e6dcb3069ec7d693f9dfc8677947de019fb
ms.sourcegitcommit: 0a3c8b0bf9c116a5caaeca453a2bbc6e7f7cbfb9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2020
ms.locfileid: "77147873"
---
# <a name="manage-storage-capacity-for-azure-stack-hub"></a>Správa kapacity úložiště pro centrum Azure Stack

Tento článek pomáhá Azure Stack operátory cloudového cloudu monitorovat a spravovat kapacitu úložiště nasazení Azure Stack hub. Infrastruktura úložiště Azure Stack hub přiděluje podmnožinu celkové kapacity úložiště nasazení Azure Stack centra jako *služby úložiště*. Služba úložiště ukládá data tenanta do sdílených složek na svazcích, které odpovídají uzlům nasazení.

Jako operátor cloudu máte k dispozici omezené množství úložiště, se kterým pracujete. Velikost úložiště je definována řešením, které implementujete. Řešení zajišťuje dodavatel OEM při použití řešení s více uzly, nebo je poskytováno hardwarem, na kterém instalujete Azure Stack Development Kit (ASDK).

Vzhledem k tomu, že centrum Azure Stack nepodporuje rozšíření kapacity úložiště, je důležité [monitorovat](#monitor-shares) dostupné úložiště, aby se zajistilo zachování efektivních operací.

Když se zbývající volná kapacita sdíleného prostředku omezí, naplánujte [správu dostupného místa](#manage-available-space) , aby sdílené složky nedocházelo k nedostatku kapacity.

Mezi možnosti správy kapacity patří:
- Kapacita se uvolňuje.
- Migrace kontejneru.

Pokud je sdílená složka využívána 100 procent, služba úložiště pro tuto sdílenou složku přestane fungovat. Pokud chcete získat pomoc při obnovování operací pro sdílenou složku, obraťte se na podporu Microsoftu.

## <a name="understand-volumes-and-shares-containers-and-disks"></a>Porozumění svazkům a sdíleným složkám, kontejnerům a diskům
### <a name="volumes-and-shares"></a>Svazky a sdílené složky
*Služba úložiště* rozdělí dostupné úložiště na samostatné, stejné svazky, které jsou přidělené pro uchovávání dat klientů. Počet svazků se rovná počtu uzlů v nasazení centra Azure Stack:

- V nasazení se čtyřmi uzly jsou čtyři svazky. Každý svazek má jednu sdílenou složku. V nasazení s více uzly se počet sdílených složek nesníží, pokud je uzel odebraný nebo pokud nefunguje.
- Pokud používáte ASDK, existuje jeden svazek s jednou sdílenou složkou.

Vzhledem k tomu, že sdílené složky úložiště jsou pro exkluzivní použití služeb úložiště, nesmíte přímo upravovat, přidávat ani odebírat žádné soubory ve sdílených složkách. Na souborech uložených na těchto svazcích by měly fungovat jenom služby úložiště.

Sdílené složky na svazcích uchovávají data tenanta. Data tenanta zahrnují objekty blob stránky, objekty blob bloku, doplňovací objekty blob, tabulky, fronty, databáze a související úložiště metadat. Vzhledem k tomu, že objekty úložiště (objekty blob atd.) jsou jednotlivě obsaženy v rámci jedné sdílené složky, maximální velikost každého objektu nemůže být větší než velikost sdílené složky. Maximální velikost nového objektu závisí na kapacitě, která zůstává ve sdílené složce jako nevyužité místo při vytvoření nového objektu.

Pokud je u sdílené složky nedostatek volného místa a [akce pro uvolnění](#reclaim-capacity) místa není úspěšná nebo nejsou k dispozici, Azure Stack mohou operátoři cloudového cloudu migrovat kontejnery objektů BLOB z jedné sdílené složky do jiné.

Informace o tom, jak uživatelé klienta pracují s úložištěm objektů BLOB v Azure Stackovém centru, najdete v tématu [služby úložiště Azure Stack hub](/azure-stack/user/azure-stack-storage-overview).

### <a name="containers"></a>Containers
Uživatelé tenanta vytvářejí kontejnery, které se pak používají k ukládání dat objektů BLOB. I když se uživatelé rozhodují, ve kterém kontejneru umístit objekty blob, služba úložiště pomocí algoritmu určuje, který svazek se má vložit do kontejneru. Algoritmus obvykle zvolí svazek s největším množstvím dostupného místa.  

Po umístění objektu blob do kontejneru může objekt BLOB zvětšit použití více místa. Při zvětšení nových objektů BLOB a zmenšení stávajících objektů BLOB se zmenší dostupné místo ve svazku, který obsahuje kontejner.  

Kontejnery nejsou omezeny pouze na jednu sdílenou složku. Když se kombinovaná data objektů BLOB v kontejneru zvětšují o použití 80% nebo více dostupného místa, kontejner přejde do režimu *přetečení* . Pokud je kontejner v režimu přetečení, všechny nové objekty blob, které jsou vytvořeny v kontejneru, budou přiděleny na jiný svazek, který má dostatek místa. V průběhu času může kontejner v režimu přetečení obsahovat objekty blob distribuované napříč více svazky.

Když se použije 80% (a pak 90 procent) dostupného místa ve svazku, systém vyvolá výstrahy na portálu pro správu centra Azure Stack. Operátoři cloudu by měli zkontrolovat dostupnou kapacitu úložiště a naplánovat, aby se obsah znovu vyrovnal. Služba úložiště přestane fungovat, když je disk 100 procent a nejsou vyvolány žádné další výstrahy.

### <a name="disks"></a>Disky
Disky virtuálních počítačů se přidávají do kontejnerů v klientech a zahrnují disk s operačním systémem. Virtuální počítače mohou mít také jeden nebo více datových disků. Oba typy disků se ukládají jako objekty blob stránky. Pokyny pro klienty je umístit jednotlivé disky do samostatného kontejneru, aby se zlepšil výkon virtuálního počítače.

- Každý kontejner, který obsahuje disk nebo objekt blob stránky, se z virtuálního počítače považuje za připojený kontejner k virtuálnímu počítači, který je vlastníkem disku.
- Kontejner, který nedrží žádné disky z virtuálního počítače, se považuje za bezplatný kontejner.

Možnosti uvolnění místa na připojeném kontejneru jsou omezené. Další informace najdete v tématu [Přesun disků virtuálních počítačů](#move-vm-disks).

>[!TIP]  
> Operátoři cloudu nespravují přímo disky, které jsou připojené k virtuálním počítačům, které můžou klienti přidávat do kontejneru. Pokud ale plánujete spravovat místo na sdílených složkách úložiště, může být užitečné pochopit, jak disky souvisejí s kontejnery a sdílenými složkami.

## <a name="monitor-shares"></a>Monitorovat sdílené složky
Pomocí Azure PowerShell nebo portálu pro správu monitorujte sdílené složky, abyste mohli pochopit, kdy je volné místo omezené. Když použijete portál, dostanete výstrahy o sdílených složkách, které mají nedostatek místa.

### <a name="use-powershell"></a>Použití prostředí PowerShell
Jako operátor cloudu můžete sledovat kapacitu úložiště sdílené složky pomocí rutiny PowerShellu `Get-AzsStorageShare`. Rutina vrátí celkové, přidělené a volné místo v bajtech na každé sdílené složky.

![Příklad: vrácení volného místa pro sdílené složky](media/azure-stack-manage-storage-shares/free-space.png)

- **Celková kapacita**: celkové místo (v bajtech), které je k dispozici na sdílené složce. Tento prostor se používá pro data a metadata, která jsou spravovaná službami úložiště.
- **Využitá kapacita**: objem dat (v bajtech), který se používá pro všechny rozsahy ze souborů, které ukládají data tenanta a přidružená metadata.

### <a name="use-the-administrator-portal"></a>Použití portálu pro správu
Jako operátor cloudu můžete pomocí portálu pro správu zobrazit kapacitu úložiště pro všechny sdílené složky.

1. Přihlaste se k [portálu pro správu](https://adminportal.local.azurestack.external).
2. Výběrem položky **všechny služby** > **úložiště** > **sdílené složky** otevřete seznam sdílení souborů, kde můžete zobrazit informace o použití.

    ![Příklad: úložiště sdílené složky na portálu pro správu centra Azure Stack](media/azure-stack-manage-storage-shares/storage-file-shares.png)

   - **Total (celkem**): celkový prostor (v bajtech), který je k dispozici na sdílené složce. Tento prostor se používá pro data a metadata, která jsou spravovaná službami úložiště.
   - **Používá**se: množství dat (v bajtech), které se používají ve všech rozsahu ze souborů, které ukládají data tenanta a přidružená metadata.

### <a name="storage-space-alerts"></a>Výstrahy prostoru úložiště
Když použijete portál pro správu, zobrazí se upozornění na sdílené složky, které mají nedostatek místa.

> [!IMPORTANT]
> Jako operátor cloudu byste měli zabránit tomu, aby sdílené složky dosáhly plného využití. Pokud je sdílená složka využívána 100 procent, služba úložiště pro tuto sdílenou složku přestane fungovat. Chcete-li obnovit volné místo a operace obnovení ve sdílené složce, která je 100 procent, je nutné se obrátit na podporu společnosti Microsoft.

* **Upozornění**: Pokud je sdílená složka větší než 80%, zobrazí se na portálu pro správu výstražné *Upozornění:*

  ![Příklad: výstražné upozornění na portálu pro správu centra Azure Stack](media/azure-stack-manage-storage-shares/alert-warning.png)

* **Kritické**: Pokud je sdílená složka větší než 90 procent, obdržíte *kritickou* výstrahu na portálu pro správu:

  ![Příklad: kritická výstraha na portálu pro správu centra Azure Stack](media/azure-stack-manage-storage-shares/alert-critical.png)

* **Zobrazit podrobnosti**: na portálu pro správu můžete otevřít podrobnosti výstrahy pro zobrazení možností zmírnění rizika:

  ![Příklad: zobrazení podrobností o výstrahách na portálu pro správu centra Azure Stack](media/azure-stack-manage-storage-shares/alert-details.png)

## <a name="manage-available-space"></a>Spravovat dostupné místo
Pokud je nutné uvolnit místo ve sdílené složce, použijte nejprve nejméně invazivní metody. Například zkuste uvolnit místo před tím, než se rozhodnete migrovat kontejner.  

### <a name="reclaim-capacity"></a>Uvolnit kapacitu
*Tato možnost se vztahuje na nasazení ve více uzlech i na Azure Stack Development Kit.*

Můžete uvolnit kapacitu, kterou používají účty klientů, které byly odstraněny. Tato kapacita se automaticky uvolní při dosažení [doby uchovávání](azure-stack-manage-storage-accounts.md#set-the-retention-period) dat, nebo se můžete chovat, aby se okamžitě znovu vyzpůsobila.

Další informace najdete v části "získání kapacity" v tématu [Správa účtů úložiště Azure Stack hub](azure-stack-manage-storage-accounts.md#reclaim).

### <a name="migrate-a-container-between-volumes"></a>Migrace kontejneru mezi svazky
*Tato možnost se vztahuje jenom na systémy integrované s Azure Stack hub.*

Vzhledem k tomu, že jsou vzory využití tenanta, můžou některé sdílené složky tenanta používat víc místa než jiné. V důsledku toho může dojít k tomu, že některé sdílené složky mají málo místa než jiné nevyužité sdílené složky.

Místo na nevyužité sdílené složce můžete uvolnit tak, že ručně migrujete některé kontejnery objektů blob do jiné sdílené složky. Můžete migrovat několik menších kontejnerů na jednu sdílenou složku, která má kapacitu uchovat vše. K přesunu *volných* kontejnerů použijte migraci. Volné kontejnery jsou kontejnery, které neobsahují disk pro virtuální počítač.

Migrace slučuje všechny objekty blob kontejneru v nové sdílené složce.

- Pokud kontejner přešel do režimu přetečení a umístil objekty blob na další svazky, musí mít Nová sdílená složka dostatečnou kapacitu pro uložení všech objektů BLOB pro kontejner, který migrujete. To zahrnuje objekty blob, které se nacházejí v dalších sdílených složkách.

- Rutina prostředí PowerShell `Get-AzsStorageContainer` identifikuje pouze používané místo na počátečním svazku pro kontejner. Rutina neidentifikuje prostor, který používají objekty blob, které jsou umístěny na dalších svazcích. Proto nemusí být úplná velikost kontejneru zřejmá. Je možné, že konsolidace kontejneru v nové sdílené složce může poslat tuto novou sdílenou složku do podmínky přetečení, kde data umístí do dalších sdílených složek. V důsledku toho může být nutné znovu vyvážit sdílené složky.

- Pokud nemáte oprávnění k určitým skupinám prostředků a nemůžete použít PowerShell k dotazování dalších svazků na data přetečení, pracujte s vlastníkem těchto skupin prostředků a kontejnerů, abyste porozuměli celkovému množství dat, která se mají migrovat, než je migrujete.  

> [!IMPORTANT]
> Migrace objektů BLOB pro kontejner je offline operace, která vyžaduje použití PowerShellu. Dokud nebude migrace dokončená, všechny objekty blob pro kontejner, které migrujete, zůstávají offline a nejde je použít. Měli byste se také vyhnout upgradu centra Azure Stack, dokud nebude dokončena veškerá nepřetržitá migrace.

#### <a name="migrate-containers-by-using-powershell"></a>Migrace kontejnerů pomocí PowerShellu
1. Potvrďte, že máte [nainstalovanou a nakonfigurovanou Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/). Další informace najdete v tématu [Správa prostředků Azure pomocí Azure PowerShell](https://go.microsoft.com/fwlink/?LinkId=394767).
2. Projděte si kontejner, abyste pochopili, jaká data jsou ve sdílené složce, kterou plánujete migrovat. Pokud chcete identifikovat nejlepší kandidáty pro migraci ve svazku, použijte rutinu `Get-AzsStorageContainer`:

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

## <a name="next-steps"></a>Další kroky
Další informace o nabídce virtuálních počítačů pro uživatele najdete v tématu [Správa kapacity úložiště pro centrum Azure Stack](azure-stack-tutorial-tenant-vm.md).
