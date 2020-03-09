---
title: Osvědčené postupy pro ověřování Azure Stack
description: Seznamte se s osvědčenými postupy ověřování jako služby.
author: mattbriggs
ms.topic: article
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019
ROBOTS: NOINDEX
ms.openlocfilehash: b3d911a07c7a234ce2284055ff67a7ca46f567a2
ms.sourcegitcommit: 1fa0140481a483e5c27f602386fe1fae77ad29f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78364733"
---
# <a name="azure-stack-validation-best-practices"></a>Osvědčené postupy pro ověřování Azure Stack

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Tento článek popisuje osvědčené postupy pro správu prostředků v rámci ověřování jako služby (VaaS). Přehled prostředků VaaS najdete v tématu věnovaném [principům ověřování jako klíč služby](azure-stack-vaas-key-concepts.md).

## <a name="solution-management"></a>Správa řešení

### <a name="naming-convention-for-vaas-solutions"></a>Konvence pojmenování pro řešení VaaS

Používejte konzistentní konvence vytváření názvů pro všechna řešení registrovaná v VaaS. Název řešení může být například sestaven z vlastností hardwaru níže, následovně:

|Název produktu | Jedinečný hardwarový prvek 1 | Jedinečný hardwarový prvek 2 | Název řešení
|---|---|---|---|
Moje řešení XYZ |  Všechny flash | Můj přepínač X01 | MySolutionXYZ_AllFlash_MySwitchX01

### <a name="when-to-create-a-new-vaas-solution"></a>Kdy vytvořit nové řešení VaaS

Používejte stejné řešení VaaS při spouštění pracovních postupů se stejnou skladovou jednotkou hardwaru. Nové řešení VaaS by se mělo vytvořit jenom v případě, že dojde ke změně SKU hardwaru.

## <a name="workflow-management"></a>Správa pracovních postupů

### <a name="naming-convention-for-vaas-workflows"></a>Konvence pojmenování pro pracovní postupy VaaS

Používejte konzistentní konvence pojmenování pro všechna spuštění pracovního postupu VaaS. Například můžete vytvořit název pracovního postupu z vlastností sestavení níže následujícím způsobem:

|Číslo sestavení (hlavní) | Datum | Velikost řešení | Název pracovního postupu
|---|---|---| ---|
1808 | 081518 | 4NODE | 1808_081518_4NODE

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [principech ověření jako Key služby](azure-stack-vaas-key-concepts.md)
