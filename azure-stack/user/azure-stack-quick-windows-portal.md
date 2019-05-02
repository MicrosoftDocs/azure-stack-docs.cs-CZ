---
title: Azure Stack rychlý Start – vytvoření virtuálního počítače s Windows
description: Azure Stack rychlý Start – vytvoření virtuálního počítače s Windows pomocí portálu
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: unknown
ms.custom: mvc
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 47a2e2090a4bc6f4d72f5288f31a6e1cb53bdac7
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985970"
---
# <a name="quickstart-create-a-windows-server-virtual-machine-with-the-azure-stack-portal"></a>Rychlý start: vytvoření virtuálního počítače s Windows serverem pomocí portálu Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Virtuální počítač Windows Server 2016 můžete vytvořit pomocí portálu Azure Stack. Postupujte podle kroků v tomto článku, jak vytvořit a používat virtuální počítač.

> [!NOTE]  
> Obrázky obrazovky v tomto článku jsou aktualizovány tak, aby odpovídaly uživatelského rozhraní, které se zavedly s verzí Azure Stack. 1808. Přidá podporu pro použití. 1808 *spravované disky* kromě nespravované disky. Pokud používáte starší verzi, budou některé obrázky podobný výběr disku, jiné než je zobrazeno v tomto článku.  


## <a name="sign-in-to-the-azure-stack-portal"></a>Přihlaste se k portálu Azure Stack

Přihlaste se k portálu Azure Stack. Adresa na portálu Azure Stack závisí, který produkt Azure Stack se připojujete k:

* Pro Azure Stack Development Kit (ASDK) přejděte na: https://portal.local.azurestack.external.
* Pro systém Azure Stack integrované přejděte na adresu URL, kterou poskytuje vaší operátory Azure stacku.

## <a name="create-a-virtual-machine"></a>Vytvoření virtuálního počítače

1. Klikněte na tlačítko **+ vytvořit prostředek** > **Compute** > **systému Windows Server 2016 Datacenter - platba jako využití**  >   **Vytvoření**. Pokud nevidíte **systému Windows Server 2016 Datacenter - platba jako využití** záznamu, obraťte se na vaše operátory Azure stacku. Položit si ho podle pokynů v přidají na webu Marketplace [lze přidat image virtuálního počítače s Windows serverem 2016 do Tržiště Azure Stack](../operator/azure-stack-create-and-publish-marketplace-item.md) článku.

    ![Postup vytvoření virtuálního počítače s Windows na portálu](media/azure-stack-quick-windows-portal/image01.png)
2. V části **Základy**, zadejte **název**, **uživatelské jméno**, a **heslo**. Zvolte **předplatné**. Vytvoření **skupiny prostředků**, nebo vyberte existující z nich, vyberte **umístění**a potom klikněte na tlačítko **OK**.

    ![Konfigurace základního nastavení](media/azure-stack-quick-windows-portal/image02.png)
3. V části **velikost** vyberte **D1 Standard**a potom klikněte na **vyberte**.  
    ![Zvolte velikost virtuálního počítače](media/azure-stack-quick-windows-portal/image03.png)

4. Na **nastavení** stránce, proveďte požadované změny výchozích hodnot.
   - Počínaje verzí Azure Stack. 1808, můžete nakonfigurovat **úložiště** kde je možné použít *spravované disky*. Starší než verze. 1808 lze použít pouze nespravované disky.  
   ![Konfigurace nastavení virtuálního počítače](media/azure-stack-quick-windows-portal/image04.png)  
   Pokud vaše konfigurace jsou připravené, vyberte **OK** pokračujte.

5. V části **Souhrn**, klikněte na tlačítko **OK** k vytvoření virtuálního počítače.
    ![Zobrazení souhrnu a vytvoření virtuálního počítače](media/azure-stack-quick-windows-portal/image05.png)

6. Nový virtuální počítač, klikněte na **všechny prostředky**, vyhledejte název virtuálního počítače a potom klikněte na jeho název ve výsledcích hledání.
    ![Zobrazit virtuální počítač](media/azure-stack-quick-windows-portal/image06.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Jakmile budete hotovi, pomocí virtuálního počítače, odstraňte virtuální počítač a její prostředky. Uděláte to tak, vyberte skupinu prostředků, na stránce virtuální počítač a klikněte na tlačítko **odstranit**.

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste nasadili základní virtuální počítač Windows Server. Další informace o virtuálních počítačích Azure Stack, [důležité informace týkající se virtuálních počítačů ve službě Azure Stack](azure-stack-vm-considerations.md).
