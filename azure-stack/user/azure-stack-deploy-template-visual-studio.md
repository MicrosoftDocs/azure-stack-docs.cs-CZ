---
title: Nasazení šablon pomocí sady Visual Studio v centru Azure Stack | Microsoft Docs
description: Naučte se, jak nasadit šablony pomocí sady Visual Studio v centru Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 628da2ae-64cc-42e0-b8b7-a6a3724cb974
ms.service: azure-stack
ms.custom: vs-azure
ms.workload: azure-vs
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: 86cd483b2416dc613ac74cdca77e7bf3f94b1fe6
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76536279"
---
# <a name="deploy-templates-in-azure-stack-hub-using-visual-studio"></a>Nasazení šablon v centru Azure Stack pomocí sady Visual Studio

Pomocí sady Visual Studio můžete nasadit šablony Azure Resource Manager do centra Azure Stack.

## <a name="to-deploy-a-template"></a>Nasazení šablony

1. [Instalace a připojení](azure-stack-install-visual-studio.md) k centru Azure Stack pomocí sady Visual Studio.
2. Otevřete sadu Visual Studio.
3. Vyberte **soubor**a potom vyberte **Nový**. V **novém projektu**vyberte **skupinu prostředků Azure**.
4. Zadejte **název** nového projektu a pak vyberte **OK**.
5. V části **Vybrat šablonu Azure**vyberte v rozevíracím seznamu možnost **rychlý Start centra Azure Stack** .
6. Vyberte **101-vytvořit-účet úložiště**a pak vyberte **OK**.
7. V novém projektu rozbalte uzel **šablony** v **Průzkumník řešení** , aby se zobrazily dostupné šablony.
8. V **Průzkumník řešení**vyberte název vašeho projektu a pak vyberte **nasadit**. Vyberte **nové nasazení**.
9. V části **nasadit do skupiny prostředků**použijte rozevírací seznam **předplatné** k výběru předplatného centra Microsoft Azure Stack.
10. V seznamu **Skupina prostředků** vyberte existující skupinu prostředků nebo vytvořte novou.
11. V seznamu **umístění skupiny prostředků** zvolte umístění a pak vyberte **nasadit**.
12. V části **Upravit parametry**zadejte hodnoty parametrů (které se liší šablonou) a pak vyberte **Uložit**.

## <a name="next-steps"></a>Další kroky

* [Nasazení šablon pomocí příkazového řádku](azure-stack-deploy-template-command-line.md)
* [Vývoj šablon pro centrum Azure Stack](azure-stack-develop-templates.md)
