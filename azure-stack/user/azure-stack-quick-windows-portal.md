---
title: Vytvoření virtuálního počítače s Windows pomocí portálu Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit virtuální počítač (VM) Windows serveru 2016 s portálem Azure Stack.
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 05/16/2019
ms.author: mabrigg
ms.custom: mvc
ms.reviewer: kivenkat
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: cc9a6baa3c71e58c2671b1f1b221e18a0c4f38c1
ms.sourcegitcommit: b36d078e699c7924624b79641dbe9021af9606ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67816171"
---
# <a name="quickstart-create-a-windows-server-vm-with-the-azure-stack-portal"></a>Rychlý start: Vytvoření virtuálního počítače s Windows serverem pomocí portálu Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Zjistěte, jak vytvořit virtuální počítač (VM) Windows serveru 2016 s použitím portálu Azure Stack.

> [!NOTE]  
> Snímky obrazovky v tomto článku jsou aktualizovány tak, aby odpovídaly uživatelského rozhraní, které se zavedly s verzí Azure Stack. 1808. Přidá podporu pro použití. 1808 *spravované disky* kromě nespravované disky. Pokud používáte starší verzi, budou některé obrázky, jako je výběr disku, jiné než je zobrazeno v tomto článku.  


## <a name="sign-in-to-the-azure-stack-portal"></a>Přihlaste se k portálu Azure Stack

Přihlaste se k portálu Azure Stack. Adresa na portálu Azure Stack závisí, který produkt Azure Stack se připojujete k:

* Pro Azure Stack Development Kit (ASDK), přejděte na: https://portal.local.azurestack.external.
* Pro systém Azure Stack integrované přejděte na adresu URL, kterou poskytuje vaší operátory Azure stacku.

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

1. Klikněte na tlačítko **+ vytvořit prostředek** > **Compute** > **systému Windows Server 2016 Datacenter - platba jako využití**  >   **Vytvoření**. <br> Pokud se nezobrazí **systému Windows Server 2016 Datacenter - platba jako využití** záznamu, obraťte se na vaše operátory Azure stacku a požádejte si ho podle pokynů v přidají na webu Marketplace [přidat bitovou kopii virtuálního počítače s Windows serverem 2016 Azure marketplace zásobníku](../operator/azure-stack-create-and-publish-marketplace-item.md) článku.

    ![Postup vytvoření virtuálního počítače s Windows na portálu](media/azure-stack-quick-windows-portal/image01.png)

2. V části **Základy**, zadejte **název**, **uživatelské jméno**, a **heslo**. Zvolte **předplatné**. Vytvoření **skupiny prostředků**, nebo vyberte existující z nich, vyberte **umístění**a potom klikněte na tlačítko **OK**.

    ![Konfigurace základního nastavení](media/azure-stack-quick-windows-portal/image02.png)

3. V části **velikost**vyberte **D1 Standard**a potom klikněte na **vyberte**.  

    ![Zvolte velikost virtuálního počítače](media/azure-stack-quick-windows-portal/image03.png)

4. Na **nastavení** stránce, proveďte požadované změny výchozích hodnot.
   - Počínaje verzí Azure Stack. 1808, můžete nakonfigurovat **úložiště** kde je možné použít *spravované disky*. Ve verzích před. 1808 je možné jenom nespravované disky.  

   ![Konfigurace nastavení virtuálního počítače](media/azure-stack-quick-windows-portal/image04.png)  

   Pokud vaše konfigurace jsou připravené, vyberte **OK** pokračujte.

5. V části **Souhrn**, klikněte na tlačítko **OK** vytvoření virtuálního počítače.
    ![Zobrazení souhrnu a vytvoření virtuálního počítače](media/azure-stack-quick-windows-portal/image05.png)

6. Nový virtuální počítač, klikněte na **všechny prostředky**, vyhledejte název virtuálního počítače a pak vyberte ve výsledcích hledání.

    ![Zobrazit virtuální počítač](media/azure-stack-quick-windows-portal/image06.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Jakmile budete hotovi, používají daný virtuální počítač, odstraňte virtuální počítač a její prostředky. Uděláte to tak, vyberte skupinu prostředků, na stránce virtuální počítač a klikněte na tlačítko **odstranit**.

## <a name="next-steps"></a>Další postup

V tomto rychlém startu jste nasadili základní virtuální počítač s Windows serverem. Další informace o virtuálních počítačích Azure Stack, [aspekty virtuálních počítačů ve službě Azure Stack](azure-stack-vm-considerations.md).
