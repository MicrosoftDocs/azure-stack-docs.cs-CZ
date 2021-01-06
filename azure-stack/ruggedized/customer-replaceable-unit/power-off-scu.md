---
title: Vypínání uzlů jednotek škálování
description: Informace o vypnutí uzlů jednotek škálování
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 2eb2c85bcdb350173923b3d8b5a3b463ce6a16ff
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2021
ms.locfileid: "97910716"
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
        