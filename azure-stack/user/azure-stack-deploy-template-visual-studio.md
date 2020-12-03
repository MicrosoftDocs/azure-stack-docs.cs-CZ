---
title: Nasazení šablon pomocí sady Visual Studio v centru Azure Stack
description: Naučte se, jak nasadit šablony pomocí sady Visual Studio v centru Azure Stack.
author: mattbriggs
ms.topic: article
ms.date: 12/2/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: 894e04e8e7b54d9e87d51af93c98f9abfd074aee
ms.sourcegitcommit: 9ef2cdc748cf00cd3c8de90705ea0542e29ada97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96525349"
---
# <a name="deploy-templates-in-azure-stack-hub-using-visual-studio"></a>Nasazení šablon v centru Azure Stack pomocí sady Visual Studio

Pomocí sady Visual Studio můžete nasadit šablony Azure Resource Manager do centra Azure Stack.

## <a name="to-deploy-a-template"></a>Nasazení šablony

1. [Instalace a připojení](azure-stack-install-visual-studio.md) k centru Azure Stack pomocí sady Visual Studio.
2. Otevřete sadu Visual Studio.
3. Vyberte **soubor** a potom vyberte **Nový**. V **novém projektu** vyberte **skupinu prostředků Azure**.
4. Zadejte **název** nového projektu a pak vyberte **OK**.
5. V části **Vybrat šablonu Azure** vyberte v rozevíracím seznamu možnost **rychlý Start centra Azure Stack** .
6. Vyberte **101-vytvořit-účet úložiště** a pak vyberte **OK**.
7. V novém projektu rozbalte uzel **šablony** v **Průzkumník řešení** , aby se zobrazily dostupné šablony.
8. V **Průzkumník řešení** vyberte název vašeho projektu a pak vyberte **nasadit**. Vyberte **nové nasazení**.
9. V části **nasadit do skupiny prostředků** použijte rozevírací seznam **předplatné** k výběru předplatného centra Microsoft Azure Stack.
10. V seznamu **Skupina prostředků** vyberte existující skupinu prostředků nebo vytvořte novou.
11. V seznamu **umístění skupiny prostředků** zvolte umístění a pak vyberte **nasadit**.
12. V části **Upravit parametry** zadejte hodnoty parametrů (které se liší šablonou) a pak vyberte **Uložit**.

## <a name="next-steps"></a>Další kroky

* [Nasazení šablon pomocí příkazového řádku](azure-stack-deploy-template-command-line.md)
* [Vývoj šablon pro centrum Azure Stack](azure-stack-develop-templates.md)
