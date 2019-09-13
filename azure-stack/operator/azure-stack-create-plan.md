---
title: Vytvoření plánu v Azure Stack | Microsoft Docs
description: Jako správce cloudu vytvořte plán, který předplatitelům umožní zřídit virtuální počítače.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 06/11/2019
ms.openlocfilehash: 6e127983afe023448d6caad23cf79e8a3a289c17
ms.sourcegitcommit: 8ddd70ba5ce05c591d3fa62597981859af107c06
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2019
ms.locfileid: "70936169"
---
# <a name="create-a-plan-in-azure-stack"></a>Vytvoření plánu ve službě Azure Stack

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

[Plány Azure Stack](azure-stack-overview.md) jsou seskupení jedné nebo více služeb a jejich kvót. Jako poskytovatel můžete vytvořit plány, které nabízí uživatelům. Uživatelé si pak můžou předplatit vaše nabídky, aby mohli používat plány, služby a kvóty, které zahrnují. V tomto příkladu se dozvíte, jak vytvořit plán, který zahrnuje poskytovatele prostředků COMPUTE, sítě a úložiště. Tento plán dává předplatitelům možnost zřídit virtuální počítače.

::: moniker range=">=azs-1902"
## <a name="create-a-plan-1902-and-later"></a>Vytvoření plánu (1902 a novější)

1. Přihlaste se k [portálu správce Azure Stack](https://adminportal.local.azurestack.external).

2. Pokud chcete vytvořit plán a nabídku, ke kterým se uživatelé můžou přihlásit, vyberte **+ vytvořit prostředek**, pak **nabídky + plány**a pak na **naplánovat**.
  
   ![Vybrat plán](media/azure-stack-create-plan/select-plan.png)

3. Zobrazí se uživatelské rozhraní s kartami, které umožňuje zadat název plánu, přidat služby a definovat kvóty pro každou z vybraných služeb. Nejdůležitější je, že před tím, než se rozhodnete vytvořit, si můžete prohlédnout podrobnosti nabídky, kterou vytvoříte.

   Na kartě **základy** v novém okně **plánu** zadejte **Zobrazovaný název** a **název prostředku**. Zobrazovaný název je popisný název plánu, který mohou operátoři vidět. Všimněte si, že na portálu pro správu se podrobnosti o plánu zobrazují jenom pro operátory.

   ![Zadat podrobnosti](media/azure-stack-create-plan/plan-name.png)

4. Vytvořte novou **skupinu prostředků**nebo vyberte existující jako kontejner pro plán.

   ![Zadejte skupinu prostředků.](media/azure-stack-create-plan/resource-group.png)

5. Vyberte kartu **služby** nebo klikněte **na další: Services >** tlačítko a potom zaškrtněte políčko pro Microsoft. **COMPUTE**, **Microsoft. Network**a **Microsoft. Storage**.
  
   ![Vybrat služby](media/azure-stack-create-plan/services.png)

6. Vyberte kartu **kvóty** nebo klikněte **na další: Kvóty >** tlačítko. V části **Microsoft. Storage**zvolte buď výchozí kvótu z rozevíracího pole, nebo vyberte **vytvořit novou** a vytvořte přizpůsobenou kvótu.
  
   ![Kvóty](media/azure-stack-create-plan/quotas.png)

7. Pokud vytváříte novou kvótu, zadejte její **název** a zadejte hodnoty kvót. Vyberte **OK** a vytvořte kvótu.

   ![Nová kvóta](media/azure-stack-create-plan/new-quota.png)

8. Opakováním kroků 6 a 7 vytvořte a přiřaďte kvóty pro **Microsoft. Network** a **Microsoft. COMPUTE**. Pokud mají všechny tři služby přiřazené kvóty, budou vypadat podobně jako v následujícím příkladu.

   ![Dokončit přiřazení kvót](media/azure-stack-create-plan/all-quotas-assigned.png)

9. Vyberte možnost **zkontrolovat + vytvořit** a Prohlédněte si plán. Zkontrolujte všechny hodnoty a kvóty, abyste měli jistotu, že jsou správné. Poznamenejte si šipky rozšíření nalevo od každé dvojice služby/kvóta. Nová funkce umožňuje rozšířit kvóty ve vybraných plánech najednou a zobrazit podrobnosti o každé kvótě v plánu a vrátit se k provedení nezbytných úprav.

   ![Vytvoření plánu](media/azure-stack-create-plan/create.png)

10. Až budete připraveni, vyberte **vytvořit** a vytvořte plán.

11. Pokud chcete nový plán zobrazit, na levé straně klikněte na **všechny služby**, vyberte **plány**a pak vyhledejte plán a vyberte jeho jméno. Pokud je váš seznam prostředků dlouhý, pomocí **hledání** vyhledejte svůj plán podle názvu.
::: moniker-end

::: moniker range="<=azs-1901"
## <a name="create-a-plan-1901-and-earlier"></a>Vytvoření plánu (1901 a starší)

1. Přihlaste se k [portálu správce Azure Stack](https://adminportal.local.azurestack.external).

2. Pokud chcete vytvořit plán a nabídku, ke kterým se uživatelé můžou přihlásit, vyberte **+ Nový**, pak **nabídky + plány**a pak na **plán**.
  
   ![Vybrat plán](media/azure-stack-create-plan/select-plan1901.png)

3. V části **Nový plán**zadejte **Zobrazovaný název** a **název prostředku**. Zobrazované jméno je popisný název plánu, který můžou uživatelé vidět. Pouze správce uvidí název prostředku, který správci používají pro práci s plánem jako s Azure Resource Managerm prostředkem.

   ![Zadat podrobnosti](media/azure-stack-create-plan/plan-name1901.png)

4. Vytvořte novou **skupinu prostředků**nebo vyberte existující jako kontejner pro plán.

   ![Zadejte skupinu prostředků.](media/azure-stack-create-plan/resource-group1901.png)

5. Vyberte **služby** a potom zaškrtněte políčko **Microsoft. COMPUTE**, **Microsoft. Network**a **Microsoft. Storage**. Potom zvolte **možnost vybrat** a uložte konfiguraci. Zaškrtávací políčka se zobrazí, pokud ukazatel myši setrvá na jednotlivých možnostech.
  
   ![Vybrat služby](media/azure-stack-create-plan/services1901.png)

6. Vyberte **kvóty**, **Microsoft. Storage (místní)** a pak zvolte výchozí kvótu, nebo vyberte **vytvořit novou kvótu** a vytvořte přizpůsobenou kvótu.
  
   ![Kvóty](media/azure-stack-create-plan/quotas1901.png)

7. Pokud vytváříte novou kvótu, zadejte **název** kvóty > zadejte hodnoty kvót > vyberte **OK**. Zavře se dialogové okno **Vytvořit kvótu** .

   ![Nová kvóta](media/azure-stack-create-plan/new-quota1901.png)

   Pak vyberete novou kvótu, kterou jste vytvořili. Výběr kvóty přiřadí a zavře dialogové okno Výběr.
  
   ![Přiřadit kvótu](media/azure-stack-create-plan/assign-quota1901.png)

8. Opakováním kroků 6 a 7 vytvořte a přiřaďte kvóty pro **Microsoft. Network (Local)** a **Microsoft. COMPUTE (místní)** . Pokud mají všechny tři služby přiřazené kvóty, budou vypadat podobně jako v následujícím příkladu.

   ![Dokončit přiřazení kvót](media/azure-stack-create-plan/all-quotas-assigned1901.png)

9. V části **kvóty**zvolte **OK**a pak v části **Nový plán**vyberte **vytvořit** a vytvořte plán.

    ![Vytvoření plánu](media/azure-stack-create-plan/create1901.png)

10. Pokud chcete nový plán zobrazit, vyberte **všechny prostředky**, vyhledejte plán a vyberte jeho jméno. Pokud je váš seznam prostředků dlouhý, pomocí **hledání** vyhledejte svůj plán podle názvu.

    ![Zkontrolovat plán](media/azure-stack-create-plan/plan-overview1901.png)
::: moniker-end

## <a name="next-steps"></a>Další postup

* [Vytvoření nabídky](azure-stack-create-offer.md)
