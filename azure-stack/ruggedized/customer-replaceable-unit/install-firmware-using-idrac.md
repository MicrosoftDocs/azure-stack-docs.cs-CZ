---
title: Nainstalovat firmware pomocí iDRAC
description: Naučte se instalovat firmware pomocí iDRAC.
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 3b6a257d5f5f42c951313c2220559306577de15d
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2021
ms.locfileid: "97910240"
---
# <a name="installing-firmware-using-the-idrac-interface"></a>Instalace firmwaru pomocí rozhraní iDRAC

Integrovaná karta pro vzdálený přístup Dell (iDRAC) umožňuje vzdáleně aktualizovat firmware (jeden na jeden) pomocí funkce **aktualizace a vrácení zpět** . Funguje i v případě, že server běží.

Přejít na stránku [dokumentace k integrovanému systému pro Microsoft Azure Stack centra 14G](https://www.dell.com/support/home/product-support/product/cloud-for-microsoft-azure-stack14g/docs) ke stažení nejnovější matrice podpory, která obsahuje seznam podporovaných verzí firmwaru.

Pomocí následujícího postupu můžete aktualizovat firmware jednoho zařízení pomocí webového rozhraní iDRAC.

**Kroky**

1.  Přejít k **údržbě** \* * aktualizace systému * *. Zobrazí se stránka **aktualizace firmwaru** .

    ![Snímek obrazovky zobrazující stránku Údržba pro integrovaný řadič vzdáleného přístupu Dell 9 | Enterprise.](media/image-85.png)

2.  Na kartě **aktualizace** vyberte jako umístění souboru možnost **místní** .

3.  Vyberte **Procházet**, vyberte bitovou kopii firmwaru pro požadovanou součást a pak vyberte **Odeslat**.

4.  Po dokončení nahrávání se v části **Podrobnosti aktualizace** zobrazí všechny soubory firmwaru, které se nahrají do iDRAC a její stav. Pokud je soubor bitové kopie firmwaru platný a byl úspěšně odeslán, zobrazí se ve sloupci **obsah** vedle názvu souboru bitové kopie firmwaru ikona (+). Rozbalte název, abyste zobrazili informace o **názvu zařízení**, **aktuální** a **dostupné verzi firmwaru** .

5.  Vyberte požadovaný soubor firmwaru a proveďte jednu z následujících akcí:

    -   Pro Image firmwaru, které nevyžadují restart hostitelského systému, vyberte **nainstalovat**.

    -   Pro Image firmwaru, které vyžadují restart hostitelského systému, vyberte **nainstalovat a restartujte** nebo **nainstalujte další restartování**.

    -   Pokud chcete aktualizaci firmwaru zrušit, vyberte **Zrušit**.

6.  Chcete-li zobrazit stránku **fronta úloh** , vyberte možnost **fronta úloh**. Pomocí této stránky můžete zobrazit a spravovat aktualizace připraveného firmwaru nebo vybrat

    **Kliknutím na tlačítko OK** aktualizujete aktuální stránku a zobrazíte stav aktualizace firmwaru.
    
