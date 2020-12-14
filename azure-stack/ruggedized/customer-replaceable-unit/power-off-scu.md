---
title: Vypínání uzlů jednotek škálování
description: Informace o vypnutí uzlů jednotek škálování
author: myoungerman
ms.topic: how-to
ms.date: 11/13/2020
ms.author: v-myoung
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: fde7496a54f0c2d3b28d98584295a761c683e17c
ms.sourcegitcommit: 3bd42be22e626564b62e560dc037aed4d462011f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2020
ms.locfileid: "97390767"
---
# <a name="powering-off-scale-unit-nodes"></a>Vypínání uzlů jednotek škálování

Předtím, než je možné vypnout uzel jednotky škálování, je nutné ověřit stav jednotky škálování \' s a identifikovat uzel, který je potřeba opravit.

Pokud se \' **stav napájení** uzlu **nezastavil**, použijte následující postup k bezpečnému vypnutí uzlu.

**Kroky**

1.  Vyprázdněte uzel jednotky škálování.

    1.  Na portálu pro správu vyberte uzel, který je potřeba opravit, a pak vyberte **vyprázdnit**.

        ![](media/image-23.png)
        
    1.  Po zobrazení výzvy zadejte název uzlu, který se má vyprázdnit, a vyberte **Ano**.

        ![](media/image-24.png)
    
    1.  Zobrazí se oznámení, že probíhá vyprazdňování.
    
        ![](media/image-25.png)
        
    1.  Přihlaste se k rozhraní iDRAC a ověřte \' v **systémových informacích** značku služby Node.
    

2.  Vypněte uzel jednotka škálování.

    1.  Po dokončení vyprázdnění znovu vyberte uzel, ověřte, zda je **provozní stav** **údržby** , a vyberte možnost **vypnout**.

        ![](media/image-26.png)
        
    1.  Po zobrazení výzvy vyberte **Ano** a potvrďte tak vypnutí.
    
        ![](media/image-27.png)
        
    1.  Zobrazí se oznámení oznamující, že probíhá vypnutí.

        ![](media/image-28.png)
    
    1.  Po dokončení vypnutí se **stav napájení** **zastaví**.
    
        ![](media/image-29.png)
        