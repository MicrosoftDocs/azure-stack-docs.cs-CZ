---
title: Vytvoření účtů úložiště v centru Azure Stack | Microsoft Docs
titleSuffix: Azure Stack Hub
description: Naučte se vytvářet účty úložiště v centru Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/2/2019
ms.author: mabrigg
ms.lastreviewed: 01/18/2019
ms.openlocfilehash: 0319ab52f27eb6dd269db0eb161727a623841961
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75809878"
---
# <a name="create-storage-accounts-in-azure-stack-hub"></a>Vytvoření účtů úložiště v centru Azure Stack

Účty úložiště v centru Azure Stack zahrnují služby BLOB a Table a jedinečný obor názvů pro datové objekty úložiště. Ve výchozím nastavení jsou data ve vašem účtu dostupná pouze pro vás, vlastníka účtu úložiště.

1. V počítači Azure Stackového centra pro ověření, přihlaste se k `https://adminportal.local.azurestack.external` jako [správce](../asdk/asdk-connect.md)a pak klikněte na **+ vytvořit prostředek** > **data + úložiště** > **účet úložiště**.

   ![Vytvoření účtu úložiště na portálu pro správu centra Azure Stack](media/azure-stack-provision-storage-account/image01.png)

2. V okně **vytvořit účet úložiště** zadejte název účtu úložiště. Vytvořte novou **skupinu prostředků**nebo vyberte některou z existujících. potom kliknutím na **vytvořit** vytvořte účet úložiště.

   ![Kontrola účtu úložiště na portálu pro správu centra Azure Stack](media/azure-stack-provision-storage-account/image02.png)

3. Pokud chcete zobrazit nový účet úložiště, klikněte na **všechny prostředky**, vyhledejte účet úložiště a klikněte na jeho název.

    ![Název účtu úložiště na portálu pro správu centra Azure Stack](media/azure-stack-provision-storage-account/image03.png)

### <a name="next-steps"></a>Další kroky

- [Použití šablon Azure Resource Manageru](../user/azure-stack-arm-templates.md)
- [Další informace o účtech Azure Storage](/azure/storage/common/storage-create-storage-account)
- [Stažení průvodce pro ověřování úložiště konzistentního s Azure Stack centra Azure](https://aka.ms/azurestacktp1doc)
