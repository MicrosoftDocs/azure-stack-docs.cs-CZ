---
title: Účty úložiště ve službě Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit účet úložiště Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2019
ms.author: mabrigg
ms.lastreviewed: 01/18/2019
ms.openlocfilehash: 77d4963d2e3e468cb2de1e41a5c483e0339a3449
ms.sourcegitcommit: 889fd09e0ab51ad0e43552a800bbe39dc9429579
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65782466"
---
# <a name="storage-accounts-in-azure-stack"></a>Účty úložiště v Azure Stack

Účty úložiště ve službě Azure Stack zahrnují služby Blob a Table Service a unikátní obor názvů pro datové objekty úložiště. Ve výchozím nastavení jsou data ve vašem účtu dostupná pouze pro vás, vlastníka účtu úložiště.

1. Na počítači Azure Stack POC, přihlaste se k `https://adminportal.local.azurestack.external` jako [správce](../asdk/asdk-connect.md)a potom klikněte na tlačítko **+ vytvořit prostředek** > **Data + úložiště**  >  **Účtu úložiště**.

   ![Vytvoření účtu úložiště](media/azure-stack-provision-storage-account/image01.png)
2. V **vytvořit účet úložiště** okno, zadejte název účtu úložiště. Vytvořte nový **skupiny prostředků**, nebo vyberte existující skupinu a potom klikněte na tlačítko **vytvořit** k vytvoření účtu úložiště.

   ![Zkontrolujte svůj účet úložiště](media/azure-stack-provision-storage-account/image02.png)
3. Váš nový účet úložiště, klikněte na **všechny prostředky**, vyhledejte účet úložiště a klikněte na jeho název.

    ![Název svého účtu úložiště](media/azure-stack-provision-storage-account/image03.png)

### <a name="next-steps"></a>Další postup
[Použití šablon Azure Resource Manageru](../user/azure-stack-arm-templates.md)

[Další informace o účtech Azure storage](/azure/storage/common/storage-create-storage-account)

[Stáhnout příručku o ověřování úložišť konzistentních s Azure Azure Stack](https://aka.ms/azurestacktp1doc)
