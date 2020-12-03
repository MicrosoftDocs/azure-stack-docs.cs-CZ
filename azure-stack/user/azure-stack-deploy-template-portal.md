---
title: Nasazení šablony pomocí portálu v Azure Stackovém centru
description: Naučte se používat portál centra Azure Stack k nasazení šablony.
author: mattbriggs
ms.topic: how-to
ms.date: 12/2/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: 7b5508f7c4e1bbd9b21f2160aecb6fd27c9a10e3
ms.sourcegitcommit: 9ef2cdc748cf00cd3c8de90705ea0542e29ada97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96525434"
---
# <a name="deploy-a-template-using-the-portal-in-azure-stack-hub"></a>Nasazení šablony pomocí portálu v Azure Stackovém centru

K nasazení Azure Resource Manager šablon do centra Azure Stack můžete použít uživatelský portál centra Azure Stack.

## <a name="to-deploy-a-template"></a>Nasazení šablony

1. Přihlaste se na portál pro uživatele centra Azure Stack. Vyberte **+ vytvořit prostředek**  >  **vlastní**  >  **template Deployment**.

   ![Vytvoření prostředku na portálu Azure Stack hub](media/azure-stack-deploy-template-portal/template-deploy1a.png)

2. Můžete buď vybrat **typ pro spuštění filtru** , a zvolit šablonu pro rychlý Start GitHubu, nebo zvolit **vytvořit vlastní šablonu v editoru**.

   ![Nasazení šablony na portálu centra Azure Stack](media/azure-stack-deploy-template-portal/template-deploy2a.png)

    [**AzureStack – rychlý Start – šablony**](https://github.com/Azure/AzureStack-QuickStart-Templates) vytváří člen komunity centra Azure Stack, nikoli Microsoft. Na každou šablonu vám na základě licenční smlouvy uděluje její vlastník, ne Microsoft. Společnost Microsoft není zodpovědná za tyto šablony a není na obrazovce pro zabezpečení, kompatibilitu nebo výkon. Šablony komunity nejsou podporované v rámci žádné služby nebo programu podpory Microsoftu a jsou dostupné tak, *jak* jsou, bez jakékoli záruky.

3. Pokud jste **v editoru vybrali možnost sestavit vlastní šablonu**, vložte kód šablony JSON do okna Code (kód).

   ![Upravit šablonu na portálu centra Azure Stack](media/azure-stack-deploy-template-portal/template-deploy3a.png)

    - Vyberte **šablonu pro rychlý Start** , která načte šablonu komunity v editoru.

    - Výběrem **načíst soubor** načtete šablonu Azure Resource Manager z místního počítače do editoru.

    - Vyberte **Stáhnout** a uložte šablonu Azure Resource Manager do místního počítače.

    Až dokončíte provádění změn v šabloně, vyberte **Uložit**.

4. Vyberte **předplatné**. Vyberte předplatné, které chcete použít. Vyberte **skupinu prostředků**. Můžete vybrat existující skupinu prostředků nebo vytvořit novou, a pak vybrat **OK**. Pak vyberte **zkontrolovat + vytvořit**.

   ![Upravit parametry na portálu centra Azure Stack](media/azure-stack-deploy-template-portal/template-deploy4a.png)

5. Vyberte **Vytvořit**.

   ![Výběr předplatného na portálu Azure Stack hub](media/azure-stack-deploy-template-portal/template-deploy5a.png)

6. Nová dlaždice na řídicím panelu sleduje průběh nasazení šablony.

   ![Vybrat skupinu prostředků na portálu Azure Stack hub](media/azure-stack-deploy-template-portal/template-deploy6a.png)

   Šablony Azure Resource Manager můžete použít k nasazení a zřízení všech prostředků aplikace v rámci jediné koordinované operace. Šablony můžete také znovu nasadit, abyste provedli změny prostředků ve skupině prostředků. Další informace o používání šablon pomocí centra Azure Stack najdete v tématu [použití šablon Azure Resource Manager v centru Azure Stack](azure-stack-arm-templates.md).

## <a name="next-steps"></a>Další kroky

Další informace o nasazování šablon najdete v následujícím článku:

- [Nasazení šablon pomocí PowerShellu](azure-stack-deploy-template-powershell.md)
