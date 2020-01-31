---
title: Monitorování aktualizací v centru Azure Stack pomocí PowerShellu
description: Naučte se monitorovat aktualizace v centru Azure Stack pomocí PowerShellu.
author: mattbriggs
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.lastreviewed: 08/23/2019
ms.reviewer: ppacent
ms.openlocfilehash: 12c3de988fde0dc7b5abd35bfa07c504f8c60c4b
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76882700"
---
# <a name="monitor-updates-in-azure-stack-hub-using-powershell"></a>Monitorování aktualizací v centru Azure Stack pomocí PowerShellu

Pomocí koncových bodů správy centra Azure Stack můžete monitorovat a spravovat vaše aktualizace. Jsou přístupné pomocí PowerShellu. Pokyny k nastavení prostředí PowerShell v centru Azure Stack najdete v tématu [instalace PowerShellu pro Azure Stack hub](azure-stack-powershell-install.md).

Ke správě aktualizací můžete použít tuto rutinu prostředí PowerShell:

| Rutina | Popis |
|------------------------------------------------------|-------------|
| [Get-AzsUpdate](https://docs.microsoft.com/powershell/module/azs.update.admin/Get-AzsUpdate?view=azurestackps-1.8.0) | Získejte seznam dostupných aktualizací. |
| [Get-AzsUpdateLocation](https://docs.microsoft.com/powershell/module/azs.update.admin/Get-AzsUpdateLocation?view=azurestackps-1.8.0)| Získá seznam umístění aktualizací. |
| [Get-AzsUpdateRun](https://docs.microsoft.com/powershell/module/azs.update.admin/Get-AzsUpdateRun?view=azurestackps-1.8.0) | Získá seznam běhů aktualizace.  |
| [Install-AzsUpdate](https://docs.microsoft.com/powershell/module/azs.update.admin/Install-AzsUpdate?view=azurestackps-1.8.0) | Použijte konkrétní aktualizaci v umístění aktualizace. |
| [Pokračovat – AzsUpdateRun](https://docs.microsoft.com/powershell/module/azs.update.admin/Resume-AzsUpdateRun?view=azurestackps-1.8.0) | Obnoví předchozí spuštěnou aktualizaci, která se nezdařila. |

## <a name="get-a-list-of-update-runs"></a>Získat seznam spuštění aktualizací

Získání seznamu příkazu pro aktualizaci spuštění:

```powershell
Get-AzsUpdateRun -UpdateName Microsoft1.0.180302.1
```

## <a name="resume-a-failed-update-operation"></a>Obnoví neúspěšnou operaci aktualizace.

Pokud se aktualizace nepovede, můžete pokračovat ve spuštění aktualizace, kde skončila, spuštěním následujícího příkazu:

```powershell
Get-AzsUpdateRun -Name 5173e9f4-3040-494f-b7a7-738a6331d55c -UpdateName Microsoft1.0.180305.1 | Resume-AzsUpdateRun
```

## <a name="next-steps"></a>Další kroky

-   [Správa aktualizací v centru Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates)
