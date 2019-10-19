---
title: Správa kapacity fyzické paměti v Azure Stack | Microsoft Docs
description: Naučte se monitorovat a spravovat fyzickou paměť a kapacitu v Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 84518E90-75E1-4037-8D4E-497EAC72AAA1
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: f72ad1b86bf8ef84e64f79603c27d14571b00838
ms.sourcegitcommit: b5eb024d170f12e51cc852aa2c72eabf26792d8d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72534164"
---
# <a name="manage-physical-memory-capacity-in-azure-stack"></a>Správa kapacity fyzické paměti v Azure Stack

*Platí pro: Azure Stack integrovaných systémů*

Chcete-li zvýšit celkovou dostupnou kapacitu paměti v Azure Stack, můžete přidat více paměti. V Azure Stack se fyzický server označuje také jako *uzel jednotky škálování*. Všechny uzly jednotek škálování, které jsou členy jedné jednotky škálování, musí mít stejnou velikost paměti.

> [!note]  
> Než budete pokračovat, přečtěte si dokumentaci výrobce hardwaru a zjistěte, jestli váš výrobce podporuje upgrade fyzické paměti. Smlouva o podpoře dodavatele hardwaru OEM může vyžadovat, aby dodavatel prováděl umístění racku fyzického serveru a aktualizaci firmwaru zařízení.

Následující vývojový diagram znázorňuje obecný proces přidání paměti do každého uzlu jednotky škálování.

![Proces přidání paměti do každého uzlu jednotky škálování](media/azure-stack-manage-storage-physical-capacity/process-to-add-memory-to-scale-unit.png)

## <a name="add-memory-to-an-existing-node"></a>Přidání paměti do existujícího uzlu
Následující kroky obsahují podrobný přehled procesu přidání paměti.

> [!Warning]
> Tento postup neprovádějte bez odkazování na dokumentaci poskytovanou výrobcem OEM.
> 
> [!Warning]
> Celá jednotka škálování musí být vypnutá, protože při upgradu do paměti se nepodporuje postupná aktualizace.

1. Zastavte Azure Stack pomocí kroků popsaných v článku [spuštění a zastavení Azure Stack](azure-stack-start-and-stop.md) .
2. Upgradujte paměť na každém fyzickém počítači pomocí dokumentace výrobce hardwaru.
3. Spusťte Azure Stack podle kroků v článku [spuštění a zastavení Azure Stack](azure-stack-start-and-stop.md) .

## <a name="next-steps"></a>Další kroky

 - Informace o tom, jak spravovat účty úložiště v Azure Stack, najdete v tématu [Správa účtů úložiště v Azure Stack](azure-stack-manage-storage-accounts.md).
 - Informace o tom, jak monitorovat a spravovat kapacitu úložiště pro nasazení Azure Stack, najdete v tématu [Správa kapacity úložiště pro Azure Stack](azure-stack-manage-storage-shares.md).
