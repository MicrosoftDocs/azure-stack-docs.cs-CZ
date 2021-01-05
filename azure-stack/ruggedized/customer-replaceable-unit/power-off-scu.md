---
title: Vypínání uzlů jednotek škálování
description: Informace o vypnutí uzlů jednotek škálování
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 972c9e8f0b66470a6e1b7cbc81d40062ff34a950
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97874706"
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
        