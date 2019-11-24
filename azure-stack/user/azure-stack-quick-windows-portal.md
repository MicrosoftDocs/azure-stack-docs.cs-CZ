---
title: Vytvoření virtuálního počítače s Windows pomocí portálu Azure Stack | Microsoft Docs
description: Naučte se vytvořit virtuální počítač s Windows serverem 2016 pomocí Azure Stackového portálu.
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
ms.openlocfilehash: 95fef782ca7efe09f7c93fbf0e28e81ed34d8166
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71823931"
---
# <a name="quickstart-create-a-windows-server-vm-with-the-azure-stack-portal"></a>Rychlý Start: Vytvoření virtuálního počítače s Windows serverem pomocí Azure Stackového portálu

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Naučte se vytvořit virtuální počítač s Windows serverem 2016 pomocí Azure Stackového portálu.

> [!NOTE]  
> Snímky obrazovky v tomto článku se aktualizují tak, aby odpovídaly uživatelskému rozhraní zavedenému s Azure Stack verze 1808. 1808 kromě nespravovaných disků přidává podporu pro používání *spravovaných disků* . Pokud používáte starší verzi, některé image, jako je výběr disku, se budou lišit od toho, co se zobrazuje v tomto článku.  


## <a name="sign-in-to-the-azure-stack-portal"></a>Přihlášení k portálu Azure Stack

Přihlaste se k portálu Azure Stack. Adresa Azure Stackového portálu závisí na tom, ke kterému Azure Stack produktu se připojujete:

* Pro Azure Stack Development Kit (ASDK), přejít na: https://portal.local.azurestack.external.
* V případě Azure Stack integrovaného systému, přejít na adresu URL, kterou zadal operátor Azure Stack.

## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

1. Klikněte na **+ vytvořit prostředek** > **COMPUTE** > **Windows Server 2016 Datacenter –** s průběžnými platbami – pomocí > **vytvořit**. <br> Pokud nevidíte položku **Windows server 2016 Datacenter – průběžné platby** , kontaktujte operátor Azure Stack a požádejte ho, aby ho přidal na web Marketplace, jak je vysvětleno v článku [Přidání image virtuálního počítače s Windows serverem 2016 do Azure Stack webu Marketplace](../operator/azure-stack-create-and-publish-marketplace-item.md) .

    ![Postup vytvoření virtuálního počítače s Windows na portálu](media/azure-stack-quick-windows-portal/image01.png)

2. V části **základy**zadejte **název**, **uživatelské jméno**a **heslo**. Vyberte **předplatné**. Vytvořte **skupinu prostředků**nebo vyberte některou z existujících, vyberte **umístění**a klikněte na **OK**.

    ![Konfigurace základního nastavení](media/azure-stack-quick-windows-portal/image02.png)

3. V části **Velikost**vyberte **D1 Standard**a pak klikněte na **Vybrat**.  

    ![Volba velikosti virtuálního počítače](media/azure-stack-quick-windows-portal/image03.png)

4. Na stránce **Nastavení** proveďte požadované změny ve výchozích hodnotách.
   - Od verze Azure Stack 1808 můžete nakonfigurovat **úložiště** , kde se můžete rozhodnout používat *spravované disky*. Ve verzích před 1808 lze použít pouze nespravované disky.  

   ![Konfigurace nastavení virtuálního počítače](media/azure-stack-quick-windows-portal/image04.png)  

   Až budou vaše konfigurace připravené, pokračujte výběrem **OK** .

5. V části **Souhrn**klikněte na **OK** a vytvořte virtuální počítač.
    ![zobrazení souhrnu a vytvoření virtuálního počítače](media/azure-stack-quick-windows-portal/image05.png)

6. Pokud chcete nový virtuální počítač zobrazit, klikněte na **všechny prostředky**, vyhledejte název virtuálního počítače a potom ho vyberte ve výsledcích hledání.

    ![Viz virtuální počítač](media/azure-stack-quick-windows-portal/image06.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Po dokončení používání virtuálního počítače odstraňte virtuální počítač a jeho prostředky. Provedete to tak, že na stránce VM vyberete skupinu prostředků a kliknete na **Odstranit**.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili základní virtuální počítač s Windows serverem. Další informace o Azure Stack virtuálních počítačů najdete [v informacích o virtuálních počítačích v Azure Stack](azure-stack-vm-considerations.md).
