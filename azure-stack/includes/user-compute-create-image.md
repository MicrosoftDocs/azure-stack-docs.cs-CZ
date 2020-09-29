---
author: mattbriggs
ms.author: mabrigg
ms.service: azure-stack
ms.topic: include
ms.date: 08/04/2020
ms.reviewer: thoroet
ms.lastreviewed: 08/04/2020
ms.openlocfilehash: 7da53434f8e4e11d3686e63b2e80672cdf575e21
ms.sourcegitcommit: 9a340b383dcf42c85bc6ec0d01ff3c9ae29dfe4c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2020
ms.locfileid: "89609772"
---
1. Přihlaste se k portálu pro uživatele centra Azure Stack.

    Pokud jste operátor cloudu, který vytváří image platformy, postupujte podle pokynů v části [Přidání image platformy](/azure-stack/operator/azure-stack-add-vm-image.md#add-a-platform-image) a přidejte VHD přes portál správce nebo s koncovými body správce.

2. Na portálu User Portal vyberte **všechny**  >  **Image**Services  >  **Přidat**.

3. V nástroji **vytvořit obrázek**:

    1. Zadejte **název** obrázku.
    2. Vyberte své **předplatné**.
    3. Vytvořte nebo přidejte obrázek do **skupiny prostředků**.
    4. Vyberte **umístění**, které se také označuje jako oblast ASDK.
    5. Vyberte **typ operačního systému** , který odpovídá vašemu obrázku.
    6. Vyberte **Procházet** a potom přejděte na svůj účet úložiště, kontejner a virtuální pevný disk. Zvolte **Vybrat**.
    5. Vyberte **typ účtu**.
        - **Disky úrovně Premium (SSD)** jsou založené na jednotkách Solid-State a nabízejí konzistentní výkon s nízkou latencí. Poskytují nejlepší rovnováhu mezi cenou a výkonem a jsou ideální pro aplikace náročné na vstupně-výstupní operace a produkční úlohy.  
        - **Disky úrovně Standard** se zálohují na magnetické jednotky a jsou vhodnější pro aplikace, ke kterým se data otevírají zřídka. Redundantní disky v zóně jsou založené na zóně redundantního úložiště (ZRS), které replikují vaše data napříč několika zónami a jsou dostupné i v případě, že je jedna zóna mimo provoz.

    8. Pro zachycení hostitele vyberte možnost **čtení/zápis** .
    9. Vyberte **Vytvořit**.

4. Po vytvoření image použijte image k vytvoření nového virtuálního počítače.