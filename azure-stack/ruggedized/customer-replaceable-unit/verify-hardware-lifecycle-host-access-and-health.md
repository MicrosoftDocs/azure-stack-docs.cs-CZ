---
title: Ověření přístupu a stavu hostitele životního cyklu životnosti hardwaru
description: Informace o ověření přístupu a stavu hostitele životního cyklu hardwaru
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: d4dd073e3ec9ec47110df916517ef0f5e3273a3a
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2021
ms.locfileid: "97910665"
---
# <a name="verifying-hardware-lifecycle-host-access-and-health"></a>Ověření přístupu a stavu hostitele životního cyklu hardwaru

Přihlaste se k iDRAC a v operačním systému HLH (hardware životního cyklu) a ověřte stav systému.

1.  Připojte se k iDRAC.

    1.  Pomocí webového prohlížeče přejděte do webového rozhraní iDRAC a přihlaste se pomocí přihlašovacích údajů poskytnutých zákazníkem.

        ![Snímek obrazovky zobrazující přihlašovací stránku iDRAC](media/image-3.png) 
    
    1.  V horní navigační nabídce vyberte možnost **systém**.

        ![Snímek obrazovky, který zobrazuje řídicí panel se zvýrazněnou nabídkou System](media/image-4.png)
        
    1.  Na kartě **Přehled** ověřte, zda je systém zcela v pořádku, nebo zobrazuje očekávaný problém, který by měl být během nahrazování hardwaru opraven.
    
        ![Snímek obrazovky zobrazující stránku System s zvýrazněnými dlaždicemi přehledu](media/image-5.png)
    
2.  Připojte se k HLH operačním systému pomocí virtuální konzole iDRAC.

    > [!NOTE]
    > Tento krok můžete přeskočit, pokud se přihlásíte pomocí chybového košíku s připojením VGA a USB.
    
    1.  Ve webovém rozhraní iDRAC vyberte možnost **řídicí panel**.

        ![Snímek obrazovky, který zobrazuje vybrané tlačítko řídicího panelu](media/image-6.png)
    
    1.  V podokně **virtuální konzola** vyberte **Nastavení**.
    
        ![Snímek obrazovky, který zobrazuje vybrané tlačítko nastavení.](media/image-7.png)
        
    1.  Ověřte, že je **Typ modulu plug-in** nastavený na **HTML 5**. Pokud tomu tak není, změňte tuto možnost, vyberte **použít** a po zobrazení výzvy klikněte na **OK** .
    
        ![Snímek obrazovky zobrazující stránku konfigurace s typem modulu plug-in-HTML5 a vybraným tlačítkem použít.](media/image-8.png)
        
    1.  Vyberte **Spustit virtuální konzolu**.

        ![Snímek obrazovky zobrazující stránku konfigurace se zvýrazněnou možností spustit virtuální konzolu](media/image-9.png)
    
    1.  Pokud se zobrazí automaticky otevírané upozornění, změňte nastavení prohlížeče tak, aby bylo vždy povoleno. V Internet Exploreru můžete například vybrat **Možnosti pro tento web** a vybrat **vždy povoleno**. V případě potřeby po změně nastavení prohlížeče opakujte předchozí krok a spusťte tak virtuální konzolu.
    
        ![Snímek obrazovky, na kterém se zobrazuje automaticky otevírané okno upozornění](media/image-10.png)
        
    1.  Virtuální konzola by teď měla být k dispozici. Pokud se chcete přihlásit k operačnímu systému, v nabídce nahoře vyberte **ovládací prvky konzoly**.
    
        ![Snímek obrazovky, který zobrazuje virtuální konzolu s vybraným tlačítkem ovládací prvky konzoly](media/image-11.png)
        
    1.  Vyberte **makro klávesnice**, **CTRL + ALT + DEL** a pak klikněte na **použít** a pak **Zavřít**.
    
        ![Snímek obrazovky znázorňující obrazovku Controls konzoly s klávesovými zkratkami a vybraným tlačítkem Zavřít](media/image-12.png)
        
    1.  Vyberte **uživatele** na základě přihlašovacích údajů, které poskytl zákazník, zadejte heslo a vyberte **šipku** pro přihlášení.
    
        ![Snímek obrazovky zobrazující zadání přihlašovacích údajů uživatele](media/image-13.png)
        
        Nyní jste přihlášeni ke službě HLH.
        
3.  Ověřte stav.

    1.  Spusťte **Správce serveru**.

        ![Snímek obrazovky zobrazující vybrané Správce serveru](media/image-14.png)
        
    1.  U výzvy **řízení uživatelských účtů** vyberte **Ano** .
    
        ![Snímek obrazovky s vybraným tlačítkem Ano pro zobrazení výzvy řízení uživatelských účtů.](media/image-15.png)
        
    1.  V nabídce **nástroje** vyberte **Správce technologie Hyper-V**.
    
        ![Snímek obrazovky, na kterém je otevřená nabídka nástroje a je vybraný Správce technologie Hyper-V](media/image-16.png)
        
    1.  Ve **Správci technologie Hyper-V** vyberte v levé nabídce uzel nejvyšší úrovně a pak ověřte, že virtuální počítače, jako je například **pracovní stanice privilegovaného přístupu**(Pokud je k dispozici), jsou ve stavu **spuštěno** .