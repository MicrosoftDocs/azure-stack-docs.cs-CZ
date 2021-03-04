---
title: Použít aktualizace pro poskytovatele prostředků centra Azure Stack
description: Naučte se, jak použít aktualizaci služby pro poskytovatele prostředků v centru Azure Stack.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 03/01/2021
ms.reviewer: jfggdl
ms.lastreviewed: 03/01/2021
zone_pivot_groups: state-connected-disconnected
ms.openlocfilehash: 60f273603d03c6625addcfeba345a198f82fbe31
ms.sourcegitcommit: 2c6418ee465e67edd417961b1f5211b2e09dbd5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102117001"
---
# <a name="how-to-update-an-azure-stack-hub-resource-provider"></a>Postup aktualizace poskytovatele prostředků centra Azure Stack

> [!IMPORTANT]
> Než budete pokračovat, přečtěte si nejnovější poznámky k verzi poskytovatele prostředků, abyste se seznámili s novými funkcemi, opravami a všemi známými problémy, které by mohly mít vliv na nasazení. Zpráva k vydání verze může také určit minimální verzi centra Azure Stack požadovaná pro poskytovatele prostředků. Pokud jste ještě nikdy nenainstalovali poskytovatele prostředků, přečtěte si místo toho pokyny poskytovatele prostředků a pokyny k prvotní instalaci.

Poskytovatelé prostředků, kteří jsou nainstalováni z webu Marketplace, budou vyžadovat pravidelnou obsluhu. Obsluha se provádí pomocí aktualizací služby, které poskytuje společnost Microsoft pravidelně. Aktualizace mohou zahrnovat nové funkce i opravy.  

## <a name="check-for-updates"></a>Vyhledat aktualizace

Poskytovatelé prostředků se aktualizují pomocí stejné funkce aktualizace, která se používá k instalaci aktualizací centra Azure Stack.

1. Přihlaste se k portálu pro správu služby Azure Stack Hub.
2. Na levé straně vyberte odkaz **všechny služby** a v části **Správa** vyberte **aktualizace**.
   [![Stránka všechny služby](media/resource-provider-apply-updates/1-all-services.png)](media/resource-provider-apply-updates/1-all-services.png#lightbox)

3. Na stránce **aktualizace** najdete aktualizace pro poskytovatele prostředků v části **poskytovatel prostředků** , kde se zobrazuje **stav** "dostupný".

   [![Snímek obrazovky, který ukazuje oddíl poskytovatele prostředků.](media/resource-provider-apply-updates/3-update-available.png)](media/resource-provider-apply-updates/3-update-available.png#lightbox)

## <a name="download-package"></a>Stáhnout balíček

Pokud je k dispozici aktualizace pro daného poskytovatele prostředků:

::: zone pivot="state-connected"
1. V části **poskytovatel prostředků** na stránce **aktualizace** vyberte řádek poskytovatele prostředků, kterého chcete aktualizovat. Všimněte si, že odkaz pro **stažení** v horní části stránky bude povolený.
   [![Stránka aktualizace k dispozici](media/resource-provider-apply-updates/4-download.png)](media/resource-provider-apply-updates/3-update-available.png#lightbox)

2. Kliknutím na odkaz **ke stažení** zahajte stažení instalačního balíčku poskytovatele prostředků. Všimněte si, že sloupec **stav** pro změnu řádku poskytovatele prostředků z možnosti "dostupný" na "stahování".
3. Když se **stav** změní na připraveno k instalaci, stahování se dokončí. 
::: zone-end

::: zone pivot="state-disconnected" 
[!INCLUDE [prereqs](../includes/resource-provider-va-package-download-disconnected.md)]
::: zone-end

## <a name="apply-an-update"></a>Použít aktualizaci

Po stažení balíčku poskytovatele prostředků se vraťte do části **poskytovatel prostředků** na stránce **aktualizace** :

1. Vyberte řádek poskytovatele prostředků, kterého chcete aktualizovat. **Stav** se teď bude zobrazovat jako připraveno k instalaci a v horní části stránky se aktivuje odkaz **Instalovat nyní** .
2. Vyberte odkaz **instalovat hned** a přejdete na stránku **instalace** pro poskytovatele prostředků. 
3. Kliknutím na tlačítko **nainstalovat** zahajte instalaci.
4. V pravém horním rohu se zobrazí oznámení "probíhá instalace" a vrátíte se na stránku **aktualizace** . Sloupec **stav** řádku poskytovatele prostředků se také změní na "probíhá instalace".
5. Po dokončení instalace bude jiné oznámení označovat úspěch nebo neúspěch. Úspěšná instalace aktualizuje taky **verzi** na stránce **poskytovatelé správy Marketplace – prostředky** .

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [funkci aktualizace řídicího panelu Správce](azure-stack-apply-updates.md).
