---
title: Zálohování účtů úložiště v centru Azure Stack
description: Přečtěte si, jak zálohovat účty úložiště v centru Azure Stack.
author: mattbriggs
ms.topic: how-to
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/19/2019
ms.openlocfilehash: b01f4b796f3d436f42da7cba5f2df60e1c666856
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76884575"
---
# <a name="back-up-your-storage-accounts-on-azure-stack-hub"></a>Zálohování účtů úložiště v centru Azure Stack

Tento článek se zabývá ochranou a obnovením účtů úložiště v rámci účtů Azure Storage v centru Azure Stack.

## <a name="elements-of-the-solution"></a>Prvky řešení

V této části se prohlíží celková struktura řešení a hlavních částí.

![Azure Stack zálohování úložiště centra](./media/azure-stack-network-howto-backup-storage/azure-stack-storage-backup.png)

### <a name="application-layer"></a>Aplikační vrstva

Data se dají replikovat mezi účty úložiště v různých jednotkách škálování centra Azure Stack, a to vyvoláním několika operací [Put BLOB](https://docs.microsoft.com/rest/api/storageservices/put-blob) nebo [Put](https://docs.microsoft.com/rest/api/storageservices/put-block) pro zápis objektů do více umístění. Alternativně může aplikace vydat operaci [kopírování](https://docs.microsoft.com/rest/api/storageservices/copy-blob) objektu BLOB pro zkopírování objektu blob do účtu úložiště hostovaného v samostatné jednotce škálování po dokončení operace PUT s primárním účtem.

### <a name="scheduled-copy-task"></a>Úloha plánovaného kopírování

AzCopy je skvělý nástroj, který se dá využít ke kopírování dat z místních systémů souborů, Azure Cloud Storage Azure Stack, úložiště centra a S3. AzCopy v současné době nemůže kopírovat data mezi dvěma účty úložiště centra Azure Stack. Kopírování objektů ze zdrojového účtu úložiště Azure Stack do cílového účtu úložiště Azure Stack hub vyžaduje zprostředkující místní systém souborů.

Další informace najdete v tématu AzCopy v článku [použití nástrojů pro přenos dat v Azure Stack centra](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-transfer?view=azs-1908#azcopy) .

### <a name="azure-stack-hub-source"></a>Centrum Azure Stack (zdroj)

Toto je zdroj dat účtu úložiště, který chcete zálohovat.

Budete potřebovat adresu URL zdrojového účtu úložiště a token SAS. Pokyny k práci s účtem úložiště najdete v tématu [Začínáme s nástroji pro vývoj úložiště Azure Stack hub](azure-stack-storage-dev.md).

### <a name="azure-stack-hub-target"></a>Centrum Azure Stack (cíl)

Jedná se o cíl, ve kterém budou uložena data účtu, který chcete zálohovat. Cílová instance centra Azure Stack musí být v jiném umístění, než je vaše cílové centrum Azure Stack. A zdroj bude muset být schopný připojit se k cíli.

Budete potřebovat adresu URL zdrojového účtu úložiště a token SAS. Pokyny k práci s účtem úložiště najdete v tématu [Začínáme s nástroji pro vývoj úložiště Azure Stack hub](azure-stack-storage-dev.md).

### <a name="intermediary-local-filesystem"></a>Zprostředkující místní systém souborů

Budete potřebovat místo, kde můžete spustit AzCopy a ukládat data při kopírování ze zdroje a pak zapisovat do cílového centra Azure Stack. Toto je zprostředkující server ve zdrojovém Azure Stackovém centru.

Jako zprostředkující server můžete vytvořit Linux nebo Windows Server. Server bude potřebovat dostatek místa pro uložení všech objektů v kontejnerech účtu zdrojového úložiště.
- Pokyny k nastavení serveru pro Linux najdete v tématu [Vytvoření virtuálního počítače s Linux serverem pomocí portálu Azure Stack hub](azure-stack-quick-linux-portal.md).  
- Pokyny k nastavení Windows serveru najdete v tématu [Vytvoření virtuálního počítače s Windows serverem pomocí portálu Azure Stack hub](azure-stack-quick-windows-portal.md).  

Po nastavení Windows serveru budete muset nainstalovat [Azure Stack centrum PowerShell](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-install?toc=https%3A%2F%2Fdocs.microsoft.com%2FFazure-stack%2Fuser%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2FFazure-stack%2Fbreadcrumb%2Ftoc.json) a [nástroje Azure Stack hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-download?toc=https%3A%2F%2Fdocs.microsoft.com%2FFazure-stack%2Fuser%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2FFazure-stack%2Fbreadcrumb%2Ftoc.json).

## <a name="set-up-backup-for-storage-accounts"></a>Nastavení zálohování pro účty úložiště

1. Načtěte koncový bod objektu BLOB pro zdrojové a cílové účty úložiště.

    ![Azure Stack zálohování úložiště centra](./media/azure-stack-network-howto-backup-storage/back-up-step1.png)

2. Vytvořte a zaznamenejte tokeny SAS pro zdrojové a cílové účty úložiště.

    ![Azure Stack zálohování úložiště centra](./media/azure-stack-network-howto-backup-storage/back-up-step2.png)

3. Nainstalujte [AzCopy](https://github.com/Azure/azure-storage-azcopy) na zprostředkující server a nastavte verzi rozhraní API na účet pro účty úložiště služby Azure Stack hub.

    - Pro Windows Server:

    ```PowerShell  
    set AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09 PowerShell use: $env:AZCOPY_DEFAULT_SERVICE_API_VERSION="2017-11-09"
    ```

    - Pro server Linux (Ubuntu):

    ```bash  
    export AZCOPY_DEFAULT_SERVICE_API_VERSION=2017-11-09
    ```

4. Na zprostředkujícím serveru vytvořte skript. Aktualizujte tento příkaz s vaším **účtem úložiště**, **klíčem SAS**a **cestou k místnímu adresáři**. Spouštíte skript pro přírůstkové kopírování dat ze **zdrojového** účtu úložiště.

    ```
    azcopy sync "https:/<storagaccount>/<container>?<SAS Key>" "C:\\myFolder" --recursive=true --delete-destination=true
    ```

5.  Zadejte **účet úložiště**, * * klíč SAS * * a * * cesta k místnímu adresáři.  Použijete to pro přírůstkové kopírování dat do **cílového** účtu úložiště.
    
    ```
    azcopy sync "C:\\myFolder" "https:// <storagaccount>/<container>?<SAS Key>" --recursive=true --delete-destination=true
    ```

6.  Pomocí cron nebo Windows Plánovač úloh naplánovat kopírování ze zdrojového Azure Stack účtu úložiště do místního úložiště na zprostředkujícím serveru. Pak zkopírujte z místního úložiště v zprostředkujícím serveru do cílového účtu úložiště Azure Stack hub.

    RPO, který můžete s tímto řešením dosáhnout, bude určeno hodnotou parametru/MO a šířkou pásma sítě mezi zdrojovým a zprostředkujícím serverem a cílovým účtem.

    - Pro server Linux (Ubuntu):

    ```bash  
    schtasks /CREATE /SC minute /MO 5 /TN "AzCopy Script" /TR C:\\&lt;script name>.bat
    ```

    | Parametr | Poznámka | 
    | ---- | ---- |
    | /SC | Použijte minutový plán. |
    | /MO | Interval v *XX* minutách. |
    | /TN | Název úlohy |
    | /TR | Cesta k souboru `script.bat`. |


    - Pro Windows Server:

    Informace o použití plánu úlohy Windows najdete v tématu [Plánovač úloh pro vývojáře](https://docs.microsoft.com/windows/win32/taskschd/task-scheduler-start-page) .
    

## <a name="use-your-storage-account-in-a-disaster"></a>Použití účtu úložiště při havárii

Každý účet úložiště centra Azure Stack má jedinečný název DNS odvozený od názvu samotné oblasti centra Azure Stack, například `https://krsource.blob.east.asicdc.com/`. Aby se aplikace zapisující do a četly z tohoto názvu DNS, bude muset přizpůsobit změnu názvu DNS účtu úložiště, když cílový účet, například `https://krtarget.blob.west.asicdc.com/`, musí být použit během havárie.

Připojovací řetězce aplikací lze upravit po havárii, která je deklarována pro přemístění objektů, nebo pokud se používá záznam CNAME před front-end zdrojového a cílového účtu úložiště pro vyrovnávání zatížení, lze nakonfigurovat nástroj pro vyrovnávání zatížení. pomocí ručního algoritmu převzetí služeb při selhání, který umožní správci deklarovat cíl

Pokud aplikace místo AAD nebo AD FS používá SAS, výše uvedená metoda nebude fungovat a řetězce připojení aplikace bude nutné aktualizovat pomocí adresy URL cílového účtu úložiště a klíčů SAS generovaných pro cílový účet úložiště.

## <a name="next-steps"></a>Další kroky

[Začínáme s nástroji pro vývoj pro úložiště Azure Stack hub](azure-stack-storage-dev.md)