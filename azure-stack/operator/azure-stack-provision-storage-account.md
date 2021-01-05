---
title: Vytvoření účtů úložiště v centru Azure Stack
titleSuffix: Azure Stack Hub
description: Naučte se vytvářet účty úložiště v centru Azure Stack.
author: PatAltimore
ms.topic: how-to
ms.date: 1/22/2020
ms.author: patricka
ms.lastreviewed: 01/18/2019
ms.openlocfilehash: 953ba2c8ee1c5d5950b4f42b0771d5c0976c2d89
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97869538"
---
# <a name="create-storage-accounts-in-azure-stack-hub"></a>Vytvoření účtů úložiště v centru Azure Stack

Účty úložiště v centru Azure Stack zahrnují služby BLOB a Table a jedinečný obor názvů pro datové objekty úložiště. Ve výchozím nastavení jsou data ve vašem účtu dostupná pouze pro vás, vlastníka účtu úložiště.

1. V počítači Azure Stackového centra ověření, přihlaste se k `https://adminportal.local.azurestack.external` účtu jako [správce](../asdk/asdk-connect.md)a pak klikněte na **+ vytvořit prostředek**  >  **data + úložiště**  >  **účet** úložiště.

   ![Vytvoření účtu úložiště na portálu pro správu centra Azure Stack](media/azure-stack-provision-storage-account/image01.png)

2. V okně **vytvořit účet úložiště** zadejte název účtu úložiště. Vytvořte novou **skupinu prostředků** nebo vyberte některou z existujících. potom kliknutím na **vytvořit** vytvořte účet úložiště.

   ![Kontrola účtu úložiště na portálu pro správu centra Azure Stack](media/azure-stack-provision-storage-account/image02.png)

3. Pokud chcete zobrazit nový účet úložiště, klikněte na **všechny prostředky**, vyhledejte účet úložiště a klikněte na jeho název.

    ![Název účtu úložiště na portálu pro správu centra Azure Stack](media/azure-stack-provision-storage-account/image03.png)

### <a name="next-steps"></a>Další kroky

- [Použití šablon Azure Resource Manageru](../user/azure-stack-arm-templates.md)
- [Informace o účtech Azure Storage](/azure/storage/common/storage-create-storage-account)
- [Stažení průvodce pro ověřování úložiště konzistentního s Azure Stack centra Azure](https://aka.ms/azurestacktp1doc)
