---
title: SQL Server osvědčené postupy pro optimalizaci výkonu v centru Azure Stack. | Dokumentace Microsoftu
description: Tento článek popisuje osvědčené postupy pro SQL Server, které vám pomůžou zvýšit výkon a optimalizovat SQL Server v Azure Stackch virtuálních počítačích centra.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/02/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 63fbc121aea2fe83b2ffda860f505fe069a4a5f1
ms.sourcegitcommit: d62400454b583249ba5074a5fc375ace0999c412
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "76023078"
---
# <a name="sql-server-best-practices-to-optimize-performance-in-azure-stack-hub"></a>Osvědčené postupy pro SQL Server k optimalizaci výkonu v centru Azure Stack

Tento článek popisuje osvědčené postupy pro SQL Server, které optimalizují SQL Server a zlepšují výkon ve virtuálních počítačích s Microsoft Azure Stack hub. Při spuštění SQL Server v Azure Stackch rozbočovačích centra používejte stejné možnosti pro ladění výkonu databáze, které platí pro SQL Server v místním serverovém prostředí. Výkon relační databáze v cloudu Azure Stack hub závisí na mnoha faktorech, včetně rodinné velikosti virtuálního počítače a konfigurace datových disků.

Při vytváření SQL Server imagí [zvažte zřízení virtuálních počítačů na portálu centra Azure Stack](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-server-provision). Stáhněte si rozšíření SQL IaaS ze správy Marketplace na portálu správce centra Azure Stack a Stáhněte si své možnosti SQL Server imagí virtuálních počítačů. Mezi ně patří SQL Server 2016 SP1, SQL Server 2016 SP2 a SQL Server 2017.

> [!NOTE]  
> V tomto článku se dozvíte, jak zřídit SQL Server virtuální počítač s využitím globálního Azure Portal, ale pokyny platí i pro Azure Stack centra s následujícími rozdíly: jednotka SSD není pro disk s operačním systémem k dispozici a v úložišti jsou drobné rozdíly. rozšířeného.

V imagí virtuálních počítačů můžete pro SQL Server použít jenom vlastní licenci (BYOL). Pro Windows Server je výchozím licenčním modelem průběžné platby (PAYG). Podrobné informace o modelu licencí Windows serveru ve virtuálním počítači najdete v článku [Nejčastější dotazy k Windows serveru na webu Azure Stack hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-windows-server-faq#what-about-other-vms-that-use-windows-server-such-as-sql-or-machine-learning-server).  

V tomto článku se zaměřujete na *nejlepší* výkon pro SQL Server v Azure Stackch virtuálních počítačích centra. Pokud je vaše úloha méně náročná, možná nebudete potřebovat při každé doporučené optimalizaci. Při hodnocení těchto doporučení Vezměte v úvahu potřeby výkonu a vzory úloh.

> [!NOTE]  
> Pokyny k výkonu pro SQL Server ve virtuálních počítačích Azure najdete v [tomto článku](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-performance).

## <a name="checklist-for-sql-server-best-practices"></a>Kontrolní seznam pro osvědčené postupy pro SQL Server

Následující kontrolní seznam slouží k zajištění optimálního výkonu SQL Server na virtuálních počítačích Azure Stack hub:


|Oblast|Optimalizace|
|-----|-----|
|Velikost virtuálního počítače |[DS3](azure-stack-vm-sizes.md) nebo vyšší pro edici SQL Server Enterprise.<br><br>[DS2](azure-stack-vm-sizes.md) nebo vyšší pro edice SQL Server Standard a Web Edition.|
|Storage |Použijte rodinu virtuálních počítačů, která podporuje [Premium Storage](azure-stack-acs-differences.md).|
|Disky |Použijte minimálně dva datové disky (jeden pro soubory protokolů a jeden pro datový soubor a databázi TempDB) a podle potřeb kapacity vyberte velikost disku. Nastavte výchozí umístění datových souborů na tyto disky během instalace SQL Server.<br><br>Nepoužívejte operační systém ani dočasné disky pro ukládání a protokolování databáze.<br>Proložením několika datových disků Azure získáte vyšší propustnost vstupně-výstupních operací pomocí prostorů úložiště.<br><br>Formát s dokumentovanými velikostmi přidělení|
|V/V|Povolí okamžitou inicializaci souborů pro datové soubory.<br><br>Omezte autogrow v databázích s přiměřenými malými pevnými přírůstky (64 MB – 256 MB).<br><br>Zakáže v databázi automaticky zmenšení.<br><br>Nastavte výchozí zálohu a umístění souborů databáze na datových discích, nikoli na disku operačního systému.<br><br>Povolit uzamčené stránky.<br><br>Použijte SQL Server Service Pack a kumulativní aktualizace.|
|Specifické pro jednotlivé funkce|Zálohujte se přímo do úložiště objektů BLOB (pokud to podporuje SQL Server používané verze).|
|||

Další informace o *tom, jak* a *Proč* provádět tyto optimalizace, najdete v podrobnostech a pokynech uvedených v následujících částech.

## <a name="vm-size-guidance"></a>Pokyny pro velikost virtuálního počítače

Pro aplikace citlivé na výkon jsou doporučené následující [velikosti virtuálních počítačů](azure-stack-vm-sizes.md) :

- **Edice SQL Server Enterprise:** DS3 nebo vyšší

- **Edice SQL Server Standard a web:** DS2 nebo vyšší

U Azure Stackového centra nedochází k žádnému rozdílu mezi výkonem řady DS a DS_v2 řady virtuálních počítačů.

## <a name="storage-guidance"></a>Pokyny k ukládání

Virtuální počítače řady DS-Series (spolu s DSv2-Series) v centru Azure Stack poskytují maximální disk s operačním systémem a propustnost datových disků (IOPS). Virtuální počítač z řady DS nebo DSv2 poskytuje až 1 000 IOPS pro disk s operačním systémem a až 2 300 IOPS na datový disk bez ohledu na typ nebo velikost vybraného disku.

Propustnost datového disku je určena jedinečně na základě řady rodiny virtuálních počítačů. V [tomto článku najdete informace o tom](azure-stack-vm-sizes.md) , jak identifikovat propustnost datového disku podle řady virtuálních počítačů.

> [!NOTE]  
> V případě produkčních úloh vyberte virtuální počítač řady DS-Series nebo DSv2-Series a zadejte maximální možné IOPS na disku a datových discích operačního systému.

Při vytváření účtu úložiště v Azure Stackovém centru nemá možnost geografické replikace žádný vliv, protože tato funkce není dostupná v centru Azure Stack.

## <a name="disks-guidance"></a>Doprovodné materiály pro disky

Na virtuálním počítači Azure Stack hub existují tři hlavní typy disků:

- **Disk s operačním systémem:** Když vytvoříte virtuální počítač centra Azure Stack, platforma připojí aspoň jeden disk (označený jako jednotka **C** ) k virtuálnímu počítači pro disk s operačním systémem. Tento disk je virtuální pevný disk uložený jako objekt blob stránky v úložišti.

- **Dočasný disk:** Virtuální počítače centra Azure Stack obsahují další disk nazvaný dočasný disk (označený jako jednotka **D** ). Toto je disk na uzlu, který lze použít pro pomocné místo.

- **Datové disky:** K VIRTUÁLNÍmu počítači můžete připojit další disky jako datové disky a tyto disky se ukládají do úložiště jako objekty blob stránky.

Následující části popisují doporučení pro používání těchto různých disků.

### <a name="operating-system-disk"></a>Disk operačním systému

Disk s operačním systémem je virtuální pevný disk, který můžete spustit a připojit jako běžící verzi operačního systému a který je označený jako jednotka **C** .

### <a name="temporary-disk"></a>Dočasný disk

Dočasná Úložná jednotka označená jako jednotka **D** není trvalá. Neukládejte žádná data, která nebudete moct ztratit na jednotce **D** . To zahrnuje soubory uživatelské databáze a soubory protokolu transakcí uživatele.

Databázi TempDB doporučujeme uložit na datový disk, protože každý datový disk poskytuje maximálně 2 300 vstupně-výstupních operací na datový disk.

### <a name="data-disks"></a>Datové disky

- **Použití datových disků pro data a soubory protokolů.** Pokud nepoužíváte diskový disk, použijte dva datové disky z virtuálního počítače, který podporuje Premium Storage, kde jeden disk obsahuje soubory protokolu a druhý obsahuje data a soubory TempDB. Každý datový disk poskytuje řadu IOPS v závislosti na rodině virtuálních počítačů, jak je popsáno v tématu [velikosti virtuálních počítačů podporované v Azure Stack hub](azure-stack-vm-sizes.md). Pokud používáte technika disku, například prostory úložiště, umístěte všechna data a soubory protokolů na stejnou jednotku (včetně databáze TempDB). Tato konfigurace poskytuje maximální počet IOPS dostupných pro SQL Server, které se mají využít, bez ohledu na to, který soubor potřebuje v konkrétní dobu.

> [!NOTE]  
> Když zřizujete SQL Server virtuální počítač na portálu, máte možnost upravit si konfiguraci úložiště. V závislosti na konfiguraci Azure Stack hub nakonfiguruje jeden nebo více disků. Více disků je sloučeno do jednoho fondu úložiště. Data i soubory protokolů se v této konfiguraci nacházejí společně.

- **Diskové svazky:** Pro zvýšení propustnosti můžete přidat další datové disky a používat diskové svazky. Pokud chcete zjistit počet potřebných datových disků, analyzujte počet IOPS potřebných pro soubory protokolů a pro vaše data a soubory TempDB. Všimněte si, že omezení IOPS jsou na datový disk založená na rodině řad virtuálních počítačů, a ne na základě velikosti virtuálního počítače. Omezení šířky pásma sítě jsou však založena na velikosti virtuálního počítače. Další podrobnosti najdete v tabulkách o [velikostech virtuálních počítačů v centru Azure Stack](azure-stack-vm-sizes.md) . Postupujte takto:

  - Pro Windows Server 2012 nebo novější použijte [prostory úložiště](https://technet.microsoft.com/library/hh831739.aspx) s následujícími pokyny:

    1. Nastavte prokládání (velikost Stripe) na 64 KB (65 536 bajtů) pro úlohy online zpracování transakcí (OLTP) a 256 KB (262 144 bajty) pro úlohy datového skladu, aby se předešlo problémům s výkonem v důsledku chybného zarovnání oddílu. Tato nastavení se musí nastavit pomocí PowerShellu.

    2. Nastavte počet sloupců = počet fyzických disků. Použijte PowerShell při konfiguraci více než osmi disků (ne Správce serveru uživatelského rozhraní).

       Například následující PowerShell vytvoří nový fond úložiště s velikostí proložení nastavenou na 64 KB a počet sloupců na 2:

       ```powershell  
       $PoolCount = Get-PhysicalDisk -CanPool $True
       $PhysicalDisks = Get-PhysicalDisk | Where-Object {$_.FriendlyName -like "*2" -or $_.FriendlyName -like "*3"}

       New-StoragePool -FriendlyName "DataFiles" -StorageSubsystemFriendlyName "Storage Spaces*" -PhysicalDisks $PhysicalDisks | New-VirtualDisk -FriendlyName "DataFiles" -Interleave 65536 -NumberOfColumns 2 -ResiliencySettingName simple -UseMaximumSize |Initialize-Disk -PartitionStyle GPT -PassThru |New-Partition -AssignDriveLetter -UseMaximumSize |Format-Volume -FileSystem NTFS -NewFileSystemLabel "DataDisks" -AllocationUnitSize 65536 -Confirm:$false
       ```

- Určete počet disků přidružených k vašemu fondu úložiště na základě očekávání zatížení. Mějte na paměti, že různé velikosti virtuálních počítačů umožňují různé počty připojených datových disků. Další informace najdete v tématu [velikosti virtuálních počítačů podporované v centru Azure Stack](azure-stack-vm-sizes.md).
- Chcete-li získat maximální možný počet IOPS pro datové disky, doporučujeme přidat maximální počet datových disků podporovaných [velikostí virtuálního počítače](azure-stack-vm-sizes.md) a používat diskové svazky.
- **Velikost alokační jednotky NTFS:** Při formátování datového disku doporučujeme použít velikost alokační jednotky 64-KB pro data a soubory protokolů i pro databázi TempDB.
- **Postupy správy disků:** Při odebrání datového disku zastavte službu SQL Server během změny. Neměňte také nastavení mezipaměti na discích, protože neposkytují žádná vylepšení výkonu.

> [!WARNING]  
> Selhání zastavení služby SQL během těchto operací může způsobit poškození databáze.

## <a name="io-guidance"></a>Doprovodné materiály k v/v

- Zvažte možnost Povolit okamžitou inicializaci souborů, aby se zkrátila doba potřebná k počátečnímu přidělení souborů. Pokud chcete využít výhod okamžité inicializace souborů, udělíte účtu služby SQL Server (MSSQLSERVER) **SE_MANAGE_VOLUME_NAME** a přidáte ho do zásad zabezpečení **provádět úlohy údržby multilicence** . Pokud používáte image platformy SQL Server pro Azure, výchozí účet služby (**NT Service\MSSQLSERVER**) se nepřidá do zásad zabezpečení **provádět úlohy údržby multilicence** . Jinými slovy, okamžitá inicializace souborů není povolená v SQL Server Image platformy Azure. Po přidání účtu služby SQL Server do zásad zabezpečení **úlohy údržby multilicence proveďte** restart služby SQL Server. Při použití této funkce se mohly vycházet z bezpečnostních důvodů. Další informace najdete v tématu [inicializace souboru databáze](https://msdn.microsoft.com/library/ms175935.aspx).
- **Autogrow** je pohotovostní pro neočekávaný nárůst. Nespravujte svoje data a protokolujte na každodenní bázi pomocí autogrow. Pokud se používá autogrow, soubor předem Rozšiřte pomocí přepínače **Size** .
- Ujistěte se, že je možnost automaticky **zmenšit** zakázaná, aby nedocházelo k zbytečné režii, která může negativně ovlivnit výkon
- Nastavte výchozí zálohu a umístění souborů databáze. Použijte doporučení v tomto článku a proveďte změny v okně Vlastnosti serveru. Pokyny najdete v tématu [zobrazení nebo změna výchozích umístění pro data a soubory protokolů (SQL Server Management Studio)](https://msdn.microsoft.com/library/dd206993.aspx). Následující snímek obrazovky ukazuje, kde provést tyto změny:

    > ![Zobrazení nebo změna výchozích umístění](./media/sql-server-vm-considerations/image1.png)

- Povolte uzamčené stránky, aby se snížila vstupně-výstupní operace a jakékoli aktivity stránkování. Další informace najdete v tématu [Povolení možnosti Uzamknout stránky v paměti (Windows)](https://msdn.microsoft.com/library/ms190730.aspx).

- Při přenosu do centra Azure Stack, včetně záloh, zvažte komprimaci všech datových souborů.

## <a name="feature-specific-guidance"></a>Doprovodné materiály k jednotlivým funkcím

Některá nasazení mohou dosáhnout dalších výhod výkonu s využitím pokročilejších technik konfigurace. V následujícím seznamu se vysvětlují některé SQL Server funkce, které vám pomůžou dosáhnout vyššího výkonu:

- **Zálohování do Azure** **Storage.** Když vytváříte zálohy pro SQL Server spuštěné v Azure Stackch virtuálních počítačích centra, můžete použít SQL Server zálohování na adresu URL. Tato funkce je k dispozici od verze SQL Server 2012 SP1 CU2 a doporučuje se pro zálohování na připojené datové disky.

    Při zálohování nebo obnovení pomocí Azure Storage postupujte podle doporučení uvedených v článku [SQL Server zálohování na Doporučené postupy a řešení potíží](https://msdn.microsoft.com/library/jj919149.aspx) a [obnovení ze záloh uložených v Microsoft Azure](https://docs.microsoft.com/sql/relational-databases/backup-restore/restoring-from-backups-stored-in-microsoft-azure?view=sql-server-2017). Tyto zálohy můžete také automatizovat pomocí [automatizovaného zálohování pro SQL Server ve virtuálních počítačích Azure](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-automated-backup).

-   **Zálohujte do Azure Stack úložiště centra.** Můžete zálohovat do Azure Stack centra úložiště podobným způsobem jako při zálohování do Azure Storage. Když vytvoříte zálohu v SQL Server Management Studio (SSMS), musíte ručně zadat informace o konfiguraci. SSMS se nedá použít k vytvoření kontejneru úložiště nebo sdíleného přístupového podpisu. SSMS se připojuje jenom k předplatným Azure, ne k předplatným centra Azure Stack. Místo toho je třeba vytvořit účet úložiště, kontejner a sdílený přístupový podpis na portálu centra Azure Stack nebo prostředí PowerShell.


    ![Zálohování SQL Server](./media/sql-server-vm-considerations/image3.png)

    > [!NOTE]  
    > Sdílený přístupový podpis je token SAS z portálu Azure Stack hub bez úvodní "?" v řetězci. Pokud používáte funkci kopírování z portálu, je nutné odstranit úvodní znak?. pro fungování tokenu v rámci SQL Server.

    Jakmile nastavíte a nakonfigurujete cíl zálohování v SQL Server, můžete zálohovat do úložiště objektů BLOB centra Azure Stack.

## <a name="next-steps"></a>Další kroky

[Použití služeb nebo sestavování aplikací pro centrum Azure Stack](azure-stack-considerations.md)
