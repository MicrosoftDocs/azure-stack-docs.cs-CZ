---
title: Spravovat kapacitu fyzické paměti pro Azure Stack | Microsoft Docs
description: Monitorujte a spravujte dostupný prostor úložiště pro Azure Stack.
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
ms.openlocfilehash: 147bfb6ba35115f44fe24f388f36814db766c7f2
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71829450"
---
# <a name="manage-physical-memory-capacity-for-azure-stack"></a>Správa kapacity fyzické paměti pro Azure Stack

*Platí pro: Azure Stack integrovaných systémů*

Pokud chcete zvýšit celkovou dostupnou kapacitu paměti pro službu Azure Stack, můžete přidat další paměť. V Azure Stack se fyzický server označuje také jako *uzel jednotky škálování*. Všechny uzly jednotek škálování, které jsou členy jedné jednotky škálování, musí mít stejnou velikost paměti.

> [!note]  
> Než budete pokračovat, přečtěte si dokumentaci od výrobce hardwaru, kde zjistíte, jestli váš výrobce podporuje upgrade fyzické paměti. Smlouva o podpoře dodavatele hardwaru OEM může vyžadovat, aby dodavatel prováděl umístění racku fyzického serveru a aktualizaci firmwaru zařízení.

Následující vývojový diagram znázorňuje obecný proces přidání paměti do každého uzlu jednotky škálování.

![Přidat paměť do každého uzlu jednotky škálování](media/azure-stack-manage-storage-physical-capacity/process-to-add-memory-to-scale-unit.png)

## <a name="add-memory-to-an-existing-node"></a>Přidání paměti do existujícího uzlu
Následující kroky poskytují podrobný přehled procesu přidání paměti. 

> [!Warning]
> Neprovádějte tyto kroky bez odkazování na dokumentaci poskytovanou výrobcem OEM.
> 
> [!Warning]
> Celá jednotka škálování musí být vypnuta, protože se nepodporuje postupná aktualizace paměti.

1. Zastavte Azure Stack pomocí kroků popsaných v článku [spuštění a zastavení Azure Stack](azure-stack-start-and-stop.md) .
2. Upgradujte paměť na každém fyzickém počítači pomocí dokumentace výrobce hardwaru.
3. Spusťte Azure Stack podle kroků v článku [spuštění a zastavení Azure Stack](azure-stack-start-and-stop.md) .

## <a name="next-steps"></a>Další kroky

 - Informace o tom, jak spravovat účty úložiště v Azure Stack k vyhledání, obnovení a získání kapacity úložiště na základě obchodních potřeb najdete v tématu [Správa účtů úložiště v Azure Stack](azure-stack-manage-storage-accounts.md).
 - Informace o tom, Azure Stack operátor cloudu sleduje a spravuje kapacitu úložiště Azure Stack nasazení, najdete v tématu [Správa kapacity úložiště pro Azure Stack](azure-stack-manage-storage-shares.md). 
