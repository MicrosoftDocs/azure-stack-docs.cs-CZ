---
title: Monitorování aktualizací v Azure Stack pomocí prostředí PowerShell | Microsoft Docs
description: Naučte se monitorovat aktualizace v Azure Stack pomocí PowerShellu.
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
ms.openlocfilehash: a724320bd066e6d46e33e6bdc9c80687cfb97736
ms.sourcegitcommit: 55ec59f831a98c42a4e9ff0dd954bf10adb98ff1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2019
ms.locfileid: "74540285"
---
# <a name="monitor-updates-in-azure-stack-using-powershell"></a>Monitorování aktualizací v Azure Stack pomocí prostředí PowerShell

*Platí pro: Azure Stack integrovaných systémů*

Pomocí koncových bodů správy Azure Stack můžete monitorovat a spravovat aktualizace. Jsou přístupné pomocí PowerShellu. Pokyny k nastavení prostředí PowerShell v Azure Stack najdete v tématu [instalace PowerShellu pro Azure Stack](azure-stack-powershell-install.md).

Ke správě aktualizací můžete použít tuto rutinu prostředí PowerShell:

| Rutiny | Popis |
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

-   [Správa aktualizací v Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates)