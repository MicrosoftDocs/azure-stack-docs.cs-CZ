---
author: mattbriggs
ms.author: mabrigg
ms.service: azure-stack
ms.topic: include
ms.date: 08/04/2020
ms.reviewer: thoroet
ms.lastreviewed: 08/04/2020
ms.openlocfilehash: a9a926b8d926e919ece60e35dc24db6b642328df
ms.sourcegitcommit: 9a340b383dcf42c85bc6ec0d01ff3c9ae29dfe4c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2020
ms.locfileid: "89609763"
---
1. Přihlaste se k portálu pro uživatele centra Azure Stack.

    Pokud jste operátor cloudu, který vytváří disk platformy, postupujte podle pokynů v části [Přidání image platformy](/azure-stack/operator/azure-stack-add-vm-image.md#add-a-platform-image) a přidejte VHD pomocí portálu pro správu nebo pomocí koncových bodů správce.

2. Na portálu User Portal vyberte **všechny**  >  **disky**služby  >  **Přidat**.

3. V nástroji **vytvořit spravovaný disk**:

    1. Zadejte **název** obrázku.
    2. Vyberte své **předplatné**.
    3. Vytvořte nebo přidejte obrázek do **skupiny prostředků**.
    4. Vyberte **umístění**, které se také označuje jako oblast ASDK.
    5. Vyberte **typ účtu**.
        - **Disky úrovně Premium (SSD)** jsou založené na jednotkách Solid-State a nabízejí konzistentní výkon s nízkou latencí. Poskytují nejlepší rovnováhu mezi cenou a výkonem a jsou ideální pro aplikace náročné na vstupně-výstupní operace a produkční úlohy.  
        - **Disky úrovně Standard** se zálohují na magnetické jednotky a jsou vhodnější pro aplikace, ke kterým se data otevírají zřídka. Redundantní disky v zóně jsou založené na zóně redundantního úložiště (ZRS), které replikují vaše data napříč několika zónami a jsou dostupné i v případě, že je jedna zóna mimo provoz.

    6. Jako **objekt BLOB úložiště** vyberte **typ zdroje**. Vytvořili jste disk z objektu BLOB v účtu úložiště.
    7. Jako zdroj VHD vyberte:
        1. Zdrojové předplatné, ve kterém se nachází účet úložiště.
        1. Vyberte **Procházet** a potom přejděte na svůj účet úložiště, kontejner a virtuální pevný disk. Zvolte **Vybrat**.
        1. Vyberte **typ operačního systému** , který se shoduje s virtuálním pevným diskem.
    8. Vyberte velikost disku **(GIB)** , která je větší nebo rovna velikosti virtuálního pevného disku.
    9. Vyberte **Vytvořit**.

4. Po vytvoření disku můžete použít disk k vytvoření nového virtuálního počítače.