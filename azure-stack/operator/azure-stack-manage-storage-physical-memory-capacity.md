---
title: Správa kapacity fyzické paměti v centru Azure Stack
description: Naučte se monitorovat a spravovat fyzickou paměť a kapacitu v Azure Stack hub.
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 1/22/2020
ms.author: inhenkel
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 615e3831cd064f8e6f04fbcf04fd5265a6f8628c
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77699146"
---
# <a name="manage-physical-memory-capacity-in-azure-stack-hub"></a>Správa kapacity fyzické paměti v centru Azure Stack

Pokud chcete zvýšit celkovou dostupnou kapacitu paměti v Azure Stackovém centru, můžete přidat více paměti. V Azure Stackovém centru se váš fyzický server označuje také jako *uzel jednotky škálování*. Všechny uzly jednotek škálování, které jsou členy jedné jednotky škálování, musí mít stejnou velikost paměti.

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

1. Zastavte Azure Stack centrum pomocí kroků popsaných v článku [spuštění a zastavení Azure Stack centra](azure-stack-start-and-stop.md) .
2. Upgradujte paměť na každém fyzickém počítači pomocí dokumentace výrobce hardwaru.
3. Pomocí postupu v článku [spuštění a zastavení Azure Stack centra](azure-stack-start-and-stop.md) spusťte Azure Stack centrum.

## <a name="next-steps"></a>Další kroky

 - Informace o tom, jak spravovat účty úložiště v centru Azure Stack, najdete v tématu [Správa účtů úložiště v centru Azure Stack](azure-stack-manage-storage-accounts.md).
 - Informace o tom, jak monitorovat a spravovat kapacitu úložiště nasazení centra Azure Stack, najdete v tématu [Správa kapacity úložiště pro centrum Azure Stack](azure-stack-manage-storage-shares.md).
