---
title: Účty úložiště v Azure Stack | Microsoft Docs
description: Naučte se vytvořit účet úložiště Azure Stack.
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
ms.openlocfilehash: 60336477f1dec9618fd6cc439e9d2f5d098b3399
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71829389"
---
# <a name="storage-accounts-in-azure-stack"></a>Účty úložiště v Azure Stack

Účty úložiště ve službě Azure Stack zahrnují služby Blob service a Table service a unikátní obor názvů pro datové objekty úložiště. Ve výchozím nastavení jsou data ve vašem účtu dostupná pouze pro vás, vlastníka účtu úložiště.

1. Na Azure Stack počítač ověření koncepce se přihlaste `https://adminportal.local.azurestack.external` jako [správce](../asdk/asdk-connect.md)a pak klikněte na **+ vytvořit prostředek** > **data + úložiště** > **účet**úložiště.

   ![Vytvoření účtu úložiště](media/azure-stack-provision-storage-account/image01.png)
2. V okně **vytvořit účet úložiště** zadejte název účtu úložiště. Vytvořte novou **skupinu prostředků**nebo vyberte některou z existujících. potom kliknutím na **vytvořit** vytvořte účet úložiště.

   ![Kontrola účtu úložiště](media/azure-stack-provision-storage-account/image02.png)
3. Pokud chcete zobrazit nový účet úložiště, klikněte na **všechny prostředky**, vyhledejte účet úložiště a klikněte na jeho název.

    ![Název účtu úložiště](media/azure-stack-provision-storage-account/image03.png)

### <a name="next-steps"></a>Další kroky
[Použití šablon Azure Resource Manageru](../user/azure-stack-arm-templates.md)

[Další informace o účtech Azure Storage](/azure/storage/common/storage-create-storage-account)

[Stažení Azure Stack Průvodce ověřením konzistence úložiště Azure](https://aka.ms/azurestacktp1doc)
