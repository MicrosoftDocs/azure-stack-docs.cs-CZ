---
title: Otestujte nabídku služby Azure Stack.
description: Naučte se testovat nabídku služeb vytvořením předplatného a nasazením prostředků.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: tutorial
ms.date: 10/13/2019
ms.reviewer: shriramnat
ms.lastreviewed: 10/06/2019
ms.openlocfilehash: 63c8e37c19b46f5cabe197dd55875e9bcbd5cb12
ms.sourcegitcommit: d159652f50de7875eb4be34c14866a601a045547
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286990"
---
# <a name="tutorial-test-a-service-offering"></a>Kurz: testování nabídky služeb

V předchozím kurzu jste vytvořili nabídku pro uživatele. V tomto kurzu se dozvíte, jak otestovat tuto nabídku tím, že ji použijete k vytvoření odběru. Pak vytvoříte a nasadíte prostředky do základních služeb, které mají nárok na předplatné.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření odběru
> * Vytváření a nasazování prostředků

## <a name="prerequisites"></a>Požadavky

Před zahájením tohoto kurzu musíte splnit následující předpoklady:

- Dokončete kurz [nabídky služby pro uživatele](tutorial-offer-services.md) . V tomto kurzu se dozvíte, jak vytvořit nabídku, kterou používá tento kurz.

- Nabídka, kterou jste přihlásili k odběru v tomto kurzu, umožňuje nasazení prostředku virtuálního počítače (VM). Pokud chcete otestovat nasazení virtuálního počítače, musíte nejdřív vytvořit image virtuálního počítače na portálu Azure Stack Marketplace, a to stažením z Azure Marketplace. Pokyny najdete v tématu [stažení položek Marketplace z Azure do Azure Stack](azure-stack-download-azure-marketplace-item.md) . 

## <a name="subscribe-to-the-offer"></a>Přihlášení k odběru nabídky

1. Přihlaste se k portálu User Portal pomocí uživatelského účtu. 

   - V případě integrovaného systému se adresa URL liší v závislosti na oblasti vašeho operátoru a názvu externí domény pomocí formátu https://portal.&lt;&gt;*oblasti*&lt;*plně kvalifikovaný název domény*&gt;.
   - Pokud používáte Azure Stack Development Kit, adresa portálu se https://portal.local.azurestack.external.

1. Vyberte dlaždici **získat předplatné** .

   ![Získat předplatné](media/tutorial-test-offer/1-get-subscription.png)

1. V části **získat předplatné**zadejte do pole **Zobrazovaný název** název nového předplatného. Vyberte **nabídku**a pak zvolte nabídku, kterou jste vytvořili v předchozím kurzu, ze seznamu **vybrat nabídku** . Vyberte **Vytvořit**.

   ![Vytvoření nabídky](media/tutorial-test-offer/2-create-subscription.png)

1. Chcete-li zobrazit odběr, vyberte možnost **všechny služby**a potom v kategorii **Obecné** vyberte **odběry**. Výběrem nového předplatného zobrazíte nabídku, ke které je přidružená, a její vlastnosti.

   >[!NOTE]
   >Po přihlášení k odběru nabídky možná budete muset aktualizovat portál, abyste viděli, které služby jsou součástí nového předplatného.

::: moniker range=">=azs-1902"
## <a name="deploy-a-storage-account-resource"></a>Nasazení prostředku účtu úložiště

Z portálu User Portal zřídíte účet úložiště pomocí předplatného, které jste vytvořili v předchozí části.

1. Přihlaste se k portálu User Portal pomocí uživatelského účtu.

1. Vyberte **+ vytvořit prostředek** > **Data + úložiště** > **účet úložiště – objekt blob, soubor, tabulka, fronta**.

1. V části **vytvořit účet úložiště**zadejte následující informace:
  
   - Zadat **název**
   - Vybrat nové **předplatné**
   - Vyberte **skupinu prostředků** (nebo ji vytvořte). 
   - Vyberte **Vytvořit** a vytvořte účet úložiště.

1. Po zahájení nasazení se vrátíte na řídicí panel. Pokud chcete zobrazit nový účet úložiště, vyberte **všechny prostředky**. Vyhledejte účet úložiště a z výsledků hledání vyberte jeho název. Odtud můžete spravovat účet úložiště a jeho obsah.

## <a name="deploy-a-virtual-machine-resource"></a>Nasazení prostředku virtuálního počítače

Z portálu User Portal zřídíte virtuální počítač pomocí předplatného, které jste vytvořili v předchozí části.

1. Přihlaste se k portálu User Portal pomocí uživatelského účtu.

1. Vyberte **+ vytvořit prostředek** > **COMPUTE** > **\<image – název\>** , kde "image-Name" je název virtuálního počítače, který jste si stáhli v požadavcích.
1. V části **vytvořit virtuální počítač** / **základy**zadejte následující informace:
  
   - Zadejte **název** virtuálního počítače.
   - Zadejte **uživatelské jméno** pro účet správce.
   - Pro virtuální počítače se systémem Linux vyberte možnost heslo pro **typ ověřování**.
   - Pro účet správce zadejte **heslo** , které se bude shodovat s **heslem pro potvrzení**.
   - Vyberte nové **předplatné**.
   - Vyberte **skupinu prostředků** (nebo ji vytvořte). 
   - Vyberte **OK** a ověřte tyto informace a pokračujte.

1. V části **Zvolte velikost**, v případě potřeby seznam vyfiltrujte, vyberte položku SKU virtuálního počítače a vyberte **Vybrat**.  
1. V **Nastavení**zadejte porty, které se mají otevřít v části **Vybrat veřejné příchozí porty**a vyberte **OK**.
   > [!NOTE]
   > Výběr možnosti RDP (3389), například umožňuje vzdálené připojení k virtuálnímu počítači, když je spuštěný.
1. V části **Souhrn**Zkontrolujte své volby a pak vyberte **OK** . tím vytvoříte virtuální počítač.  
1. Po zahájení nasazení se vrátíte na řídicí panel. Pokud chcete zobrazit nový virtuální počítač, vyberte **všechny prostředky**. Vyhledejte virtuální počítač a z výsledků hledání vyberte jeho název. Odtud můžete získat přístup k virtuálnímu počítači a jeho správu.
   > [!NOTE]
   > Úplné nasazení a spuštění virtuálního počítače může trvat několik minut. Jakmile je virtuální počítač připravený k použití, změní se [stav](/azure/virtual-machines/windows/states-lifecycle) na spuštěno.

::: moniker-end

::: moniker range="<=azs-1901"
## <a name="deploy-a-virtual-machine-resource-1901-and-earlier"></a>Nasazení prostředku virtuálního počítače (1901 a starší)

Z portálu User Portal zřizujete virtuální počítač pomocí nového předplatného.

1. Přihlaste se k portálu User Portal pomocí uživatelského účtu.

1. Na řídicím panelu vyberte **+ vytvořit prostředek** > **COMPUTE** > **Windows Server 2016 Datacenter Eval**a pak vyberte **vytvořit**.

1. V části **základy**zadejte následující informace:
  
   - Zadat **název**
   - Zadejte **uživatelské jméno** .
   - Zadat **heslo**
   - Zvolit nové **předplatné**
   - Vytvořte **skupinu prostředků** (nebo vyberte některou z existujících.) 
   - Kliknutím na **tlačítko OK** uložte tyto informace.

1. V **Možnosti zvolit velikost**vyberte **a1 Standard**a pak **Vyberte**.  
1. V **Nastavení**vyberte **virtuální síť**.

1. V **Možnosti zvolit virtuální síť**vyberte **vytvořit novou**.

1. V části **vytvořit virtuální síť**přijměte všechny výchozí hodnoty a vyberte **OK**.

1. Vyberte **OK** v **Nastavení** a uložte konfiguraci sítě.

1. V části **Souhrn**vyberte **OK** a vytvořte virtuální počítač.  

1. Pokud chcete zobrazit nový virtuální počítač, vyberte **všechny prostředky**. Vyhledejte virtuální počítač a z výsledků hledání vyberte jeho název.
::: moniker-end

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření odběru
> * Vytváření a nasazování prostředků 

V dalším kroku se dozvíte, jak nasadit poskytovatele prostředků pro doplňky služby. Umožňují nabízet uživatelům ještě více služeb ve vašich plánech:

- [Nabídnout SQL na Azure Stack](azure-stack-sql-resource-provider.md)
- [Nabídka MySQL na Azure Stack](azure-stack-mysql-resource-provider.md)
- [App Service nabídky Azure Stack](azure-stack-app-service-overview.md)
