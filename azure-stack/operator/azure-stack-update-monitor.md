---
title: Monitorování aktualizací v centru Azure Stack pomocí PowerShellu | Microsoft Docs
description: Naučte se monitorovat aktualizace v centru Azure Stack pomocí PowerShellu.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2019
ms.author: mabrigg
ms.lastreviewed: 08/23/2019
ms.reviewer: ppacent
ms.openlocfilehash: 91d5d0e7c63be8a192113d26818380983c668aa7
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75813568"
---
# <a name="monitor-updates-in-azure-stack-hub-using-powershell"></a>Monitorování aktualizací v centru Azure Stack pomocí PowerShellu

*Platí pro: Azure Stack integrovaných systémů centra*

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
