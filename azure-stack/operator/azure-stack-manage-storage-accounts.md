---
title: Správa účtů úložiště Azure Stack hub | Microsoft Docs
description: Přečtěte si, jak najít, spravovat, obnovit a získat Azure Stack účty úložiště centra.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: conceptual
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 03/19/2019
ms.openlocfilehash: 69b02e95e7819ed6caea19721cbd8b09da1a802d
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76535293"
---
# <a name="manage-azure-stack-hub-storage-accounts"></a>Správa účtů úložiště Azure Stack hub

Naučte se spravovat účty úložiště Azure Stack hub. Vyhledávejte, obnovujte a uvolňujte kapacitu úložiště na základě obchodních potřeb.

## <a name="find-a-storage-account"></a>Najít účet úložiště

Seznam účtů úložiště v oblasti lze zobrazit v Azure Stack centru pomocí následujících kroků:

1. Přihlaste se k [portálu pro správu](https://adminportal.local.azurestack.external).

2. Vyberte **všechny služby** > **úložiště** > **účty úložiště**.

   ![Účty úložiště Azure Stack hub](media/azure-stack-manage-storage-accounts/image4.png)

Ve výchozím nastavení se zobrazí prvních 10 účtů. Další možnosti získáte tak, že kliknete na odkaz **načíst další** v dolní části seznamu.

NEBO

Pokud vás zajímá konkrétní účet úložiště, můžete **filtrovat a načíst jenom příslušné účty** .

**Filtrování účtů:**

1. V horní části podokna vyberte **Filtr** .
2. V podokně filtru můžete zadat **název účtu**, **ID předplatného**nebo **stav** , abyste mohli vyladit seznam účtů úložiště, které se mají zobrazit. Použijte je podle potřeby.
3. Při psaní se v seznamu automaticky použije filtr.

    ![Filtrování účtů úložiště Azure Stack hub](media/azure-stack-manage-storage-accounts/image5.png)

4. Postup obnovení filtru: vyberte **Filtr**, zrušte výběr a aktualizujte.

Textové pole Hledat (v horní části podokna Seznam účtů úložiště) umožňuje v seznamu účtů zvýraznit vybraný text. Tuto možnost můžete použít, pokud není úplný název nebo ID snadno dostupné.

Zde můžete použít bezplatný text, který vám umožní najít účet, který vás zajímá.

![Hledání účtů úložiště Azure Stack hub](media/azure-stack-manage-storage-accounts/image6.png)

## <a name="look-at-account-details"></a>Podívejte se na podrobnosti účtu.
Po vyhledání účtů, které vás zajímají, můžete vybrat konkrétní účet a zobrazit určité podrobnosti. Otevře se nové podokno s podrobnostmi o účtu. Mezi tyto podrobnosti patří druh účtu, čas vytvoření, umístění atd.

![Podrobnosti účtu úložiště](media/azure-stack-manage-storage-accounts/image7.png)

## <a name="recover-a-deleted-account"></a>Obnovení odstraněného účtu
Může se jednat o situaci, kdy potřebujete obnovit odstraněný účet.

V centru Azure Stack existuje jednoduchý způsob, jak to provést:

1. Přejděte do seznamu účty úložiště. Další informace najdete v tématu [vyhledání účtu úložiště](azure-stack-manage-storage-accounts.md) v horní části tohoto článku.
2. V seznamu vyhledejte příslušný účet. Možná budete muset filtrovat.
3. Ověřte *stav* účtu. Měl by se jednat o **odstranění**.
4. Vyberte účet, který otevře podokno Podrobnosti o účtu.
5. V horní části tohoto podokna najděte tlačítko **obnovit** a vyberte ho.
6. Výběrem **Ano** potvrďte.

   ![Potvrzení obnovení účtu úložiště](media/azure-stack-manage-storage-accounts/image8.png)

7. Obnovení se nyní zpracovává. Počkejte na indikaci, že byla úspěšná. Můžete také vybrat ikonu zvonku v horní části portálu a zobrazit indikace průběhu.

   ![Obnovení účtu úložiště bylo úspěšné.](media/azure-stack-manage-storage-accounts/image9.png)

   Po úspěšné synchronizaci obnoveného účtu ho můžete znovu použít.

### <a name="some-gotchas"></a>Některé možná úskalí
* Váš odstraněný účet zobrazuje stav jako **Neuchování**.
  
  Neuchování znamená, že odstraněný účet překročil dobu uchování a nemusí být obnovitelný.

* Odstraněný účet se nezobrazí v seznamu účty.
  
  Účet se v seznamu účtů nemusí zobrazovat, pokud byl odstraněný účet už uvolněný z paměti. V takovém případě ho nelze obnovit. Další informace najdete v tématu [nárok na kapacitu](#reclaim) v tomto článku.

## <a name="set-the-retention-period"></a>Nastavit dobu uchování
Nastavení doby uchovávání umožňuje operátorovi cloudu nastavit časové období ve dnech (od 0 do 9 999 dnů), během kterého je potenciálně možné obnovit jakýkoli odstraněný účet. Výchozí doba uchovávání je nastavená na 0 dnů. Nastavení na hodnotu 0 znamená, že se všechny odstraněné účty okamžitě vyjmou z uchovávání a označí se pro pravidelné uvolňování paměti.

**Postup změny doby uchování:**

1. Přihlaste se k [portálu pro správu](https://adminportal.local.azurestack.external).
2. V části **Správa**vyberte **všechny služby** > **oblasti Správa** .
3. Vyberte možnost **poskytovatelé prostředků** > **Nastavení** > **úložiště** . Vaše cesta je domovská > *oblast* – poskytovatelé prostředků > úložiště.
4. Vyberte **Konfigurace** a pak upravte hodnotu Doba uchování.

   Nastavte počet dnů a pak ho uložte.

   Tato hodnota je okamžitě účinná a je nastavená pro celou oblast.

   ![Upravit dobu uchování na portálu pro správu](media/azure-stack-manage-storage-accounts/image10.png)

## <a name="reclaim"></a>Uvolnit kapacitu
Jedním z vedlejších účinků, které mají dobu uchování, je to, že odstraněný účet bude nadále spotřebovávat kapacitu, dokud nepřijde do doby uchování. Jako operátor cloudu budete možná potřebovat způsob, jak uvolnit odstraněné místo na účtu, i když doba uchování ještě nevypršela.

Kapacitu můžete uvolnit buď pomocí portálu, nebo pomocí PowerShellu.

**Postup uvolnění kapacity pomocí portálu:**
1. Přejděte do podokna účty úložiště. Viz najít účet úložiště.
2. V horní části podokna vyberte **uvolnit místo** .
3. Přečtěte si zprávu a pak vyberte **OK**.

    ![Uvolnit místo v účtech úložiště](media/azure-stack-manage-storage-accounts/image11.png)

4. Počkejte na oznámení o úspěšném dokončení. Podívejte se na ikonu zvonku na portálu.

    ![Opětovné uvolnění místa bylo úspěšné.](media/azure-stack-manage-storage-accounts/image12.png)

5. Aktualizujte stránku účty úložiště. Odstraněné účty již nejsou v seznamu zobrazeny, protože byly vymazány.

Pomocí prostředí PowerShell můžete také explicitně přepsat dobu uchování a okamžitě uvolnit kapacitu.

**Postup uvolnění kapacity pomocí prostředí PowerShell:**

1. Potvrďte, že máte nainstalovanou a nakonfigurovanou Azure PowerShell. V takovém případě postupujte podle následujících pokynů: 
   * Pokud chcete nainstalovat nejnovější verzi Azure PowerShell a přidružit ji k předplatnému Azure, přečtěte si téma [Jak nainstalovat a nakonfigurovat Azure PowerShell](https://azure.microsoft.com/documentation/articles/powershell-install-configure/).
   Další informace o rutinách Azure Resource Manager najdete v tématu [použití Azure PowerShell s Azure Resource Manager](https://go.microsoft.com/fwlink/?LinkId=394767).
2. Spusťte následující rutiny:

> [!NOTE]  
> Pokud tyto rutiny spustíte, trvale odstraníte účet a jeho obsah. Nedá se zotavit. Používejte s ním péči.

```powershell  
    $farm_name = (Get-AzsStorageFarm)[0].name
    Start-AzsReclaimStorageCapacity -FarmName $farm_name
```

Další informace najdete v [dokumentaci k prostředí PowerShell centra Azure Stack](https://docs.microsoft.com/powershell/azure/azure-stack/overview).
 

## <a name="next-steps"></a>Další kroky

 - Informace o správě oprávnění najdete v tématu [Nastavení oprávnění k přístupu pomocí řízení přístupu na základě role](azure-stack-manage-permissions.md).
 - Informace o správě kapacity úložiště pro centrum Azure Stack najdete v tématu [Správa kapacity úložiště pro centrum Azure Stack](azure-stack-manage-storage-shares.md).
