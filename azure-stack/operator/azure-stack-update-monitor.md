---
title: Monitorování aktualizací pomocí PowerShellu v centru Azure Stack
description: Naučte se monitorovat aktualizace pomocí PowerShellu v centru Azure Stack.
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 03/04/2020
ms.author: inhenkel
ms.lastreviewed: 08/23/2019
ms.reviewer: ppacent
ms.openlocfilehash: bee3cbf3ab3faedbe54212c67dada1faa746071a
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94545257"
---
# <a name="monitor-updates-with-powershell-in-azure-stack-hub"></a>Monitorování aktualizací pomocí PowerShellu v centru Azure Stack

Pomocí koncových bodů správy centra Azure Stack můžete monitorovat a spravovat vaše aktualizace. Jsou přístupné pomocí PowerShellu. Pokyny k nastavení prostředí PowerShell v centru Azure Stack najdete v tématu [instalace PowerShellu pro Azure Stack hub](powershell-install-az-module.md).

Ke správě aktualizací můžete použít následující rutiny PowerShellu:

| Rutina | Popis |
|------------------------------------------------------|-------------|
| [Get-AzsUpdate](/powershell/module/azs.update.admin/get-azsupdate?view=azurestackps-1.8.0) | Získejte seznam dostupných aktualizací. |
| [Get-AzsUpdateLocation](/powershell/module/azs.update.admin/get-azsupdatelocation?view=azurestackps-1.8.0)| Získá seznam umístění aktualizací. |
| [Get-AzsUpdateRun](/powershell/module/azs.update.admin/get-azsupdaterun?view=azurestackps-1.8.0) | Získá seznam běhů aktualizace.  |
| [Install-AzsUpdate](/powershell/module/azs.update.admin/install-azsupdate?view=azurestackps-1.8.0) | Použijte konkrétní aktualizaci v umístění aktualizace. |
| [Pokračovat – AzsUpdateRun](/powershell/module/azs.update.admin/resume-azsupdaterun?view=azurestackps-1.8.0) | Obnoví předchozí spuštěnou aktualizaci, která se nezdařila. |

## <a name="get-a-list-of-update-runs"></a>Získat seznam spuštění aktualizací

Chcete-li získat seznam spuštění aktualizací, spusťte následující příkaz:

```powershell
Get-AzsUpdateRun -UpdateName Microsoft1.0.180302.1
```

## <a name="resume-a-failed-update-operation"></a>Obnoví neúspěšnou operaci aktualizace.

Pokud se aktualizace nepovede, můžete pokračovat ve spuštění aktualizace, kde skončila, spuštěním následujícího příkazu:

```powershell
Get-AzsUpdateRun -Name 5173e9f4-3040-494f-b7a7-738a6331d55c -UpdateName Microsoft1.0.180305.1 | Resume-AzsUpdateRun
```

## <a name="troubleshoot"></a>Řešení potíží

Další informace o řešení potíží s aktualizacemi najdete v tématu [řešení potíží s Azure Stack](azure-stack-troubleshooting.md).

## <a name="next-steps"></a>Další kroky

- [Správa aktualizací v centru Azure Stack](./azure-stack-updates.md)
