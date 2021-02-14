---
title: Vypínání uzlů jednotek škálování
description: Informace o vypnutí uzlů jednotek škálování
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: f35c21320a90a68c7933e9fd0bf898496fda9398
ms.sourcegitcommit: 5ea0e915f24c8bcddbcaf8268e3c963aa8877c9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100488050"
---
# <a name="powering-off-scale-unit-nodes"></a>Vypínání uzlů jednotek škálování

Předtím, než je možné vypnout uzel jednotky škálování, je nutné ověřit stav jednotky škálování \' s a identifikovat uzel, který je potřeba opravit.

Pokud se \' **stav napájení** uzlu **nezastavil**, použijte následující postup k bezpečnému vypnutí uzlu.

**Kroky**

1.  Vyprázdněte uzel jednotky škálování.

    1.  Na portálu pro správu vyberte uzel, který je potřeba opravit, a pak vyberte **vyprázdnit**.

        ![Snímek obrazovky zobrazující stránku pro správu s vybranou akcí vyprázdnit a zvýrazněný uzel](media/image-23.png)
        
    1.  Po zobrazení výzvy zadejte název uzlu, který se má vyprázdnit, a vyberte **Ano**.

        ![Snímek obrazovky zobrazující stránku Správa – uzly](media/image-24.png)
    
    1.  Zobrazí se oznámení, že probíhá vyprazdňování.
    
        ![Snímek obrazovky, na které se zobrazí stránka Správa – uzly se zobrazeným oznámením o vyprázdnění](media/image-25.png)
        
    1.  Přihlaste se k rozhraní iDRAC a ověřte \' v **systémových informacích** značku služby Node.
    

2.  Vypněte uzel jednotka škálování.

    1.  Po dokončení vyprázdnění znovu vyberte uzel, ověřte, zda je **provozní stav** **údržby** , a vyberte možnost **vypnout**.

        ![Snímek obrazovky, který zobrazuje stránku pro správu uzlů s uzlem a vybranou akcí vypnout](media/image-26.png)
        
    1.  Po zobrazení výzvy vyberte **Ano** a potvrďte tak vypnutí.
    
        ![Snímek obrazovky zobrazující stránku Správa – uzly s zobrazeným dialogovým oknem pro vypnutí uzlu](media/image-27.png)
        
    1.  Zobrazí se oznámení oznamující, že probíhá vypnutí.

        ![Snímek obrazovky zobrazující stránku Správa – uzly s zobrazeným dialogovým oknem pro vypnutí](media/image-28.png)
    
    1.  Po dokončení vypnutí se **stav napájení** **zastaví**.
    
        ![Snímek obrazovky, na které se zobrazuje stránka pro správu uzlů s vybraným uzlem a pro stav napájení je zvýrazněná položka zastaveno](media/image-29.png)
        