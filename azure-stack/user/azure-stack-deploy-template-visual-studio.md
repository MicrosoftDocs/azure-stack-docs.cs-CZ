---
title: Nasazení šablon pomocí sady Visual Studio v Azure Stack | Microsoft Docs
description: Naučte se, jak nasadit šablony pomocí sady Visual Studio v Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 628da2ae-64cc-42e0-b8b7-a6a3724cb974
ms.service: azure-stack
ms.custom: vs-azure
ms.workload: azure-vs
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: c84965295abd1218458f46e998dcec8b1b2fdc88
ms.sourcegitcommit: b3dac698f2e1834491c2f9af56a80e95654f11f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/30/2019
ms.locfileid: "68658601"
---
# <a name="deploy-templates-in-azure-stack-using-visual-studio"></a>Nasazení šablon v Azure Stack pomocí sady Visual Studio

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Aplikaci Visual Studio lze použít k nasazení Azure Resource Manager šablon pro Azure Stack.

## <a name="to-deploy-a-template"></a>Nasazení šablony

1. [Instalace a připojení](azure-stack-install-visual-studio.md) k Azure Stack pomocí sady Visual Studio.
2. Otevřít Visual Studio.
3. Vyberte **soubor**a potom vyberte **Nový**. V **novém projektu**vyberte **skupinu prostředků Azure**.
4. Zadejte **název** nového projektu a pak vyberte **OK**.
5. V části **Vybrat šablonu Azure**vyberte v rozevíracím seznamu **Azure Stack rychlý Start** .
6. Vyberte **101-vytvořit-účet úložiště**a pak vyberte **OK**.
7. V novém projektu rozbalte uzel **šablony** v **Průzkumník řešení** , aby se zobrazily dostupné šablony.
8. V **Průzkumník řešení**vyberte název vašeho projektu a pak vyberte **nasadit**. Vyberte **nové nasazení**.
9. V části **nasadit do skupiny prostředků**použijte rozevírací seznam **předplatné** k výběru předplatného Microsoft Azure Stack.
10. V seznamu **Skupina prostředků** vyberte existující skupinu prostředků nebo vytvořte novou.
11. V seznamu **umístění skupiny prostředků** zvolte umístění a pak vyberte **nasadit**.
12. V části **Upravit parametry**zadejte hodnoty parametrů (které se liší šablonou) a pak vyberte **Uložit**.

## <a name="next-steps"></a>Další postup

* [Nasazení šablon pomocí příkazového řádku](azure-stack-deploy-template-command-line.md)
* [Vývoj šablon pro Azure Stack](azure-stack-develop-templates.md)
