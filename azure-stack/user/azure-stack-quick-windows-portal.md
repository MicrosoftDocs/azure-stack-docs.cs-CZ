---
title: Vytvoření virtuálního počítače s Windows pomocí portálu Azure Stack hub
description: Naučte se vytvořit virtuální počítač s Windows serverem 2016 pomocí portálu Azure Stack hub.
author: mattbriggs
ms.topic: quickstart
ms.date: 10/06/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 10/06/2020
ms.openlocfilehash: 77c7e3484186a0bea1f6220330338128aad72fad
ms.sourcegitcommit: 2d2ae0b6db2e4f43f8496b184f30cddbb08b2cbd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2020
ms.locfileid: "91815393"
---
# <a name="quickstart-create-a-windows-server-vm-with-the-azure-stack-hub-portal"></a>Rychlý Start: Vytvoření virtuálního počítače s Windows serverem pomocí portálu Azure Stack hub

Naučte se vytvořit virtuální počítač s Windows serverem 2016 pomocí portálu Azure Stack hub.

> [!NOTE]  
> Snímky obrazovky v tomto článku se aktualizují tak, aby odpovídaly uživatelskému rozhraní zavedenému ve službě Azure Stack hub verze 1808. 1808 kromě nespravovaných disků přidává podporu pro používání *spravovaných disků* . Pokud používáte starší verzi, některé image, jako je výběr disku, se budou lišit od toho, co se zobrazuje v tomto článku.  


## <a name="sign-in-to-the-azure-stack-hub-portal"></a>Přihlášení k portálu centra Azure Stack

Přihlaste se k portálu centra Azure Stack. Adresa portálu centra Azure Stack závisí na tom, k jakému produktu Azure Stack centra se připojujete:

* Pro Azure Stack Development Kit (ASDK), přejít na: `https://portal.local.azurestack.external` .
* V případě integrovaného systému služby Azure Stack hub přejít na adresu URL, kterou poskytl váš operátor centra Azure Stack.

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

1. Vyberte **vytvořit prostředek**  >  **COMPUTE**. Hledat ` Windows Server 2016 Datacenter - Pay as you use` .
    Pokud nevidíte položku **Windows Server 2016 Datacenter – průběžné platby** , obraťte se na svého operátora cloudu centra Azure Stack a požádejte ho o přidání image do tržiště centra Azure Stack. Pokyny, které může váš operátor cloudu použít k [Vytvoření a publikování vlastní položky na webu Marketplace centra Azure Stack](../operator/azure-stack-create-and-publish-marketplace-item.md).

    ![Windows Server 2016 Datacenter – Plaťte podle vašich využití](./media/azure-stack-quick-windows-portal/image1.png)

1. Vyberte **Vytvořit**.

    ![Vytvoření prostředku](./media/azure-stack-quick-windows-portal/image2.png)

1. V části **základy**zadejte **název**, **typ disku**, **uživatelské jméno**a **heslo** . Vyberte **předplatné**. Vytvořte **skupinu prostředků**nebo vyberte některou z existujících, vyberte **umístění**a pak vyberte **OK**.

    ![Vytvoření základních informací o virtuálním počítači](./media/azure-stack-quick-windows-portal/image3.png)

1. V části **Velikost**vyberte **D1_v2** a pak zvolte **Vybrat**.

    ![Vytvoření velikosti virtuálního počítače](./media/azure-stack-quick-windows-portal/image4.png)

1. Na stránce **Nastavení** proveďte požadované změny ve výchozích hodnotách. Požadované veřejné příchozí porty musíte nakonfigurovat z příslušného rozevíracího seznamu. Po dokončení vyberte **OK**.

    ![Vytvoření virtuálního počítače – nastavení](./media/azure-stack-quick-windows-portal/image5.png)

1. V části **Souhrn** vyberte **OK** a vytvořte virtuální počítač.

    ![Vytvoření virtuálního počítače – souhrn](./media/azure-stack-quick-windows-portal/image6.png)

1. Vyberte **Virtual Machines** pro kontrolu nového virtuálního počítače. Vyhledejte název virtuálního počítače a potom ve výsledcích hledání vyberte virtuální počítač.

![Vytvoření virtuálního počítače – vyhledání virtuálního počítače](./media/azure-stack-quick-windows-portal/image7.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení používání virtuálního počítače odstraňte virtuální počítač a jeho prostředky. Provedete to tak, že vyberete skupinu prostředků na stránce virtuální počítač a vyberete **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili základní virtuální počítač s Windows serverem. Další informace o Azure Stackch virtuálních počítačů centra najdete [v informacích o virtuálních počítačích v centru Azure Stack](azure-stack-vm-considerations.md).
