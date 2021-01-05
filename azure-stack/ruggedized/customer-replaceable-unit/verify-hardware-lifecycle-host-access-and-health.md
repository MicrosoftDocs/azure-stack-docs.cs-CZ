---
title: Ověření přístupu a stavu hostitele životního cyklu životnosti hardwaru
description: Informace o ověření přístupu a stavu hostitele životního cyklu hardwaru
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: c203923352202dd8d58b27fdf955942342685bdf
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97874485"
---
# <a name="verifying-hardware-lifecycle-host-access-and-health"></a>Ověření přístupu a stavu hostitele životního cyklu hardwaru

Přihlaste se k iDRAC a v operačním systému HLH (hardware životního cyklu) a ověřte stav systému.

1.  Připojte se k iDRAC.

    1.  Pomocí webového prohlížeče přejděte do webového rozhraní iDRAC a přihlaste se pomocí přihlašovacích údajů poskytnutých zákazníkem.

        ![](media/image-3.png) 
    
    1.  V horní navigační nabídce vyberte možnost **systém**.

        ![](media/image-4.png)
        
    1.  Na kartě **Přehled** ověřte, zda je systém zcela v pořádku, nebo zobrazuje očekávaný problém, který by měl být během nahrazování hardwaru opraven.
    
        ![](media/image-5.png)
    
2.  Připojte se k HLH operačním systému pomocí virtuální konzole iDRAC.

    > [!NOTE]
    > Tento krok můžete přeskočit, pokud se přihlásíte pomocí chybového košíku s připojením VGA a USB.
    
    1.  Ve webovém rozhraní iDRAC vyberte možnost **řídicí panel**.

        ![](media/image-6.png)
    
    1.  V podokně **virtuální konzola** vyberte **Nastavení**.
    
        ![](media/image-7.png)
        
    1.  Ověřte, že je **Typ modulu plug-in** nastavený na **HTML 5**. Pokud tomu tak není, změňte tuto možnost, vyberte **použít** a po zobrazení výzvy klikněte na **OK** .
    
        ![](media/image-8.png)
        
    1.  Vyberte **Spustit virtuální konzolu**.

        ![](media/image-9.png)
    
    1.  Pokud se zobrazí automaticky otevírané upozornění, změňte nastavení prohlížeče tak, aby bylo vždy povoleno. V Internet Exploreru můžete například vybrat **Možnosti pro tento web** a vybrat **vždy povoleno**. V případě potřeby po změně nastavení prohlížeče opakujte předchozí krok a spusťte tak virtuální konzolu.
    
        ![](media/image-10.png)
        
    1.  Virtuální konzola by teď měla být k dispozici. Pokud se chcete přihlásit k operačnímu systému, v nabídce nahoře vyberte **ovládací prvky konzoly**.
    
        ![](media/image-11.png)
        
    1.  Vyberte **makro klávesnice**, **CTRL + ALT + DEL** a pak klikněte na **použít** a pak **Zavřít**.
    
        ![](media/image-12.png)
        
    1.  Vyberte **uživatele** na základě přihlašovacích údajů, které poskytl zákazník, zadejte heslo a vyberte **šipku** pro přihlášení.
    
        ![](media/image-13.png)
        
        Nyní jste přihlášeni ke službě HLH.
        
3.  Ověřte stav.

    1.  Spusťte **Správce serveru**.

        ![](media/image-14.png)
        
    1.  U výzvy **řízení uživatelských účtů** vyberte **Ano** .
    
        ![](media/image-15.png)
        
    1.  V nabídce **nástroje** vyberte **Správce technologie Hyper-V**.
    
        ![](media/image-16.png)
        
    1.  Ve **Správci technologie Hyper-V** vyberte v levé nabídce uzel nejvyšší úrovně a pak ověřte, že virtuální počítače, jako je například **pracovní stanice privilegovaného přístupu**(Pokud je k dispozici), jsou ve stavu **spuštěno** .