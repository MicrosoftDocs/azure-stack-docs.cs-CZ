---
title: Jak použít aktualizace pro poskytovatele prostředků centra Azure Stack.
description: Naučte se, jak použít aktualizaci služby pro poskytovatele prostředků v centru Azure Stack.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 11/18/2019
ms.reviewer: jfggdl
ms.lastreviewed: 11/18/2019
ms.openlocfilehash: 5ec6f85fc2f2e526c4b85521337ab65dc2b939cc
ms.sourcegitcommit: 519f4298dc1ed5c33f9c4fef811f61d61731dd84
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/05/2020
ms.locfileid: "82799862"
---
# <a name="how-to-update-an-azure-stack-hub-resource-provider"></a>Postup aktualizace poskytovatele prostředků centra Azure Stack

> [!IMPORTANT]
> Než budete pokračovat, přečtěte si nejnovější poznámky k verzi poskytovatele prostředků, abyste se seznámili s novými funkcemi, opravami a všemi známými problémy, které by mohly mít vliv na nasazení. Zpráva k vydání verze může také určit minimální verzi centra Azure Stack požadovaná pro poskytovatele prostředků.

Poskytovatelé prostředků nainstalovanou z Marketplace budou vyžadovat pravidelnou obsluhu. Obsluha se provádí pomocí aktualizací služby, které poskytuje společnost Microsoft pravidelně. Aktualizace mohou zahrnovat nové funkce i opravy.  

## <a name="check-for-updates"></a>Vyhledat aktualizace

Poskytovatelé prostředků se aktualizují pomocí stejné funkce aktualizace, která se používá k instalaci aktualizací centra Azure Stack.

1. Přihlaste se k portálu pro správu centra Azure Stack.
2. Na levé straně vyberte odkaz **všechny služby** a v části **Správa** vyberte **aktualizace**.
   ![Stránka všechny služby](media/resource-provider-apply-updates/1-all-services.png)

3. Na stránce **aktualizace** najdete v části **poskytovatel prostředků** aktualizace pro poskytovatele prostředků.

   [![Stránka aktualizace k dispozici](media/resource-provider-apply-updates/3-update-available.png)](media/resource-provider-apply-updates/3-update-available.png#lightbox)

## <a name="apply-an-update"></a>Použít aktualizaci

Pokud je k dispozici aktualizace pro daného poskytovatele prostředků:

1. Vyberte řádek poskytovatele prostředků, kterého chcete aktualizovat. Všimněte si, že odkaz pro **stažení** v horní části stránky bude povolený.
   [![Stránka aktualizace k dispozici](media/resource-provider-apply-updates/4-download.png)](media/resource-provider-apply-updates/3-update-available.png#lightbox)

2. Kliknutím na odkaz **ke stažení** zahajte stažení instalačního balíčku poskytovatele prostředků. Všimněte si, že sloupec **stav** pro změnu řádku poskytovatele prostředků z možnosti "dostupný" na "stahování".
3. Když se **stav** změní na připraveno k instalaci, stahování se dokončí. Všimněte si, že se také aktivuje odkaz **Instalovat nyní** v horní části stránky.
4. Vyberte odkaz **instalovat hned** a přejdete na stránku **instalace** pro poskytovatele prostředků. 
5. Kliknutím na tlačítko **nainstalovat** zahajte instalaci.
6. V pravém horním rohu se zobrazí oznámení "probíhá instalace" a vrátíte se na stránku **aktualizace** . Sloupec **stav** řádku poskytovatele prostředků se také změní na "probíhá instalace".
7. Po dokončení instalace bude jiné oznámení označovat úspěch nebo neúspěch. Úspěšná instalace aktualizuje taky **verzi** na stránce **poskytovatelé správy Marketplace – prostředky** .

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [funkci aktualizace řídicího panelu Správce](azure-stack-apply-updates.md).
