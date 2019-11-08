---
title: Vytvoření účtů úložiště v Azure Stack | Microsoft Docs
titleSuffix: Azure Stack
description: Naučte se vytvářet účty úložiště v Azure Stack.
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
ms.openlocfilehash: 449d9e39b650e6f7ccd91f4703709ea033e7a5dc
ms.sourcegitcommit: ca358ea5c91a0441e1d33f540f6dbb5b4d3c92c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73802340"
---
# <a name="create-storage-accounts-in-azure-stack"></a>Vytvoření účtů úložiště v Azure Stack

Účty úložiště ve službě Azure Stack zahrnují služby Blob service a Table service a unikátní obor názvů pro datové objekty úložiště. Ve výchozím nastavení jsou data ve vašem účtu dostupná pouze pro vás, vlastníka účtu úložiště.

1. V počítači Azure Stack ověření koncepce se přihlaste do `https://adminportal.local.azurestack.external` jako [správce](../asdk/asdk-connect.md)a pak klikněte na **+ vytvořit prostředek** > **data + úložiště** > **účet úložiště**.

   ![Vytvoření účtu úložiště na portálu Azure Stack správce](media/azure-stack-provision-storage-account/image01.png)

2. V okně **vytvořit účet úložiště** zadejte název účtu úložiště. Vytvořte novou **skupinu prostředků**nebo vyberte některou z existujících. potom kliknutím na **vytvořit** vytvořte účet úložiště.

   ![Kontrola účtu úložiště na portálu Azure Stack správce](media/azure-stack-provision-storage-account/image02.png)

3. Pokud chcete zobrazit nový účet úložiště, klikněte na **všechny prostředky**, vyhledejte účet úložiště a klikněte na jeho název.

    ![Název účtu úložiště na portálu Azure Stack správce](media/azure-stack-provision-storage-account/image03.png)

### <a name="next-steps"></a>Další kroky

- [Použití šablon Azure Resource Manageru](../user/azure-stack-arm-templates.md)
- [Další informace o účtech Azure Storage](/azure/storage/common/storage-create-storage-account)
- [Stažení Azure Stack Průvodce ověřením konzistence úložiště Azure](https://aka.ms/azurestacktp1doc)
