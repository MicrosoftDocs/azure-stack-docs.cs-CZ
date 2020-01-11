---
title: Vytvoření virtuálního počítače s Windows pomocí portálu Azure Stack hub | Microsoft Docs
description: Naučte se vytvořit virtuální počítač s Windows serverem 2016 pomocí portálu Azure Stack hub.
services: azure-stack
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.topic: quickstart
ms.date: 10/02/2019
ms.author: mabrigg
ms.custom: mvc
ms.reviewer: kivenkat
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 53e33ed9da57209c1f937cbf72eb2c3882dfd1cd
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75878573"
---
# <a name="quickstart-create-a-windows-server-vm-with-the-azure-stack-hub-portal"></a>Rychlý Start: Vytvoření virtuálního počítače s Windows serverem pomocí portálu Azure Stack hub

Naučte se vytvořit virtuální počítač s Windows serverem 2016 pomocí portálu Azure Stack hub.

> [!NOTE]  
> Snímky obrazovky v tomto článku se aktualizují tak, aby odpovídaly uživatelskému rozhraní zavedenému ve službě Azure Stack hub verze 1808. 1808 kromě nespravovaných disků přidává podporu pro používání *spravovaných disků* . Pokud používáte starší verzi, některé image, jako je výběr disku, se budou lišit od toho, co se zobrazuje v tomto článku.  


## <a name="sign-in-to-the-azure-stack-hub-portal"></a>Přihlášení k portálu centra Azure Stack

Přihlaste se k portálu centra Azure Stack. Adresa portálu centra Azure Stack závisí na tom, k jakému produktu Azure Stack centra se připojujete:

* Pro Azure Stack Development Kit (ASDK), přejít na: https://portal.local.azurestack.external.
* V případě integrovaného systému služby Azure Stack hub přejít na adresu URL, kterou poskytl váš operátor centra Azure Stack.

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

1. Klikněte na **+ vytvořit prostředek** > **COMPUTE** > **Windows Server 2016 Datacenter –** s průběžnými platbami – pomocí > **vytvořit**. <br> Pokud nevidíte položku **Windows server 2016 Datacenter – průběžné platby** , obraťte se na svého operátora centra Azure Stack a požádejte ho, aby ho přidal na web Marketplace, jak je vysvětleno v článku [Přidání image virtuálního počítače s Windows serverem 2016 do webu služby Azure Stack hub Marketplace](../operator/azure-stack-create-and-publish-marketplace-item.md) .

    ![Postup vytvoření virtuálního počítače s Windows na portálu](media/azure-stack-quick-windows-portal/image01.png)

2. V části **základy**zadejte **název**, **uživatelské jméno**a **heslo**. Vyberte **předplatné**. Vytvořte **skupinu prostředků**nebo vyberte některou z existujících, vyberte **umístění**a klikněte na **OK**.

    ![Konfigurace základního nastavení](media/azure-stack-quick-windows-portal/image02.png)

3. V části **Velikost**vyberte **D1 Standard**a pak klikněte na **Vybrat**.  

    ![Volba velikosti virtuálního počítače](media/azure-stack-quick-windows-portal/image03.png)

4. Na stránce **Nastavení** proveďte požadované změny ve výchozích hodnotách.
   - Od centra Azure Stack verze 1808 můžete nakonfigurovat **úložiště** , kde se můžete rozhodnout používat *spravované disky*. Ve verzích před 1808 lze použít pouze nespravované disky.  

   ![Konfigurace nastavení virtuálního počítače](media/azure-stack-quick-windows-portal/image04.png)  

   Až budou vaše konfigurace připravené, pokračujte výběrem **OK** .

5. V části **Souhrn**klikněte na **OK** a vytvořte virtuální počítač.
    ![zobrazení souhrnu a vytvoření virtuálního počítače](media/azure-stack-quick-windows-portal/image05.png)

6. Pokud chcete nový virtuální počítač zobrazit, klikněte na **všechny prostředky**, vyhledejte název virtuálního počítače a potom ho vyberte ve výsledcích hledání.

    ![Viz virtuální počítač](media/azure-stack-quick-windows-portal/image06.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení používání virtuálního počítače odstraňte virtuální počítač a jeho prostředky. Provedete to tak, že na stránce VM vyberete skupinu prostředků a kliknete na **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili základní virtuální počítač s Windows serverem. Další informace o Azure Stackch virtuálních počítačů centra najdete [v informacích o virtuálních počítačích v centru Azure Stack](azure-stack-vm-considerations.md).
