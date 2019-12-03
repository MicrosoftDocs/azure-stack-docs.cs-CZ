---
title: Spustit a zastavit Azure Stack | Microsoft Docs
description: Zjistěte, jak spustit a vypnout Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 43BF9DCF-F1B7-49B5-ADC5-1DA3AF9668CA
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: misainat
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: e0e23ca6d469e33adbcd47bc66125d6af92f0123
ms.sourcegitcommit: 7817d61fa34ac4f6410ce6f8ac11d292e1ad807c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2019
ms.locfileid: "74689985"
---
# <a name="start-and-stop-azure-stack"></a>Spuštění a zastavení Azure Stack
Postupujte podle pokynů v tomto článku, abyste správně vypnuli a restartovali Azure Stack služby. Vypínání bude fyzicky vypnout celé Azure Stack prostředí. Spusťte své pravomoci na všech rolích infrastruktury a vrátí prostředky tenanta do stavu napájení, který existoval před vypnutím.

## <a name="stop-azure-stack"></a>Zastavit Azure Stack 

Vypněte Azure Stack pomocí následujících kroků:

1. Připravte všechny úlohy spuštěné v prostředcích tenanta Azure Stack prostředí pro nadcházející vypínání. 

2. Otevřete privilegovanou relaci koncového bodu (PEP) z počítače se síťovým přístupem k virtuálním počítačům s Azure Stack ERCS. Pokyny najdete v tématu [použití privilegovaného koncového bodu v Azure Stack](azure-stack-privileged-endpoint.md).

3. Z PEP spusťte:

    ```powershell
      Stop-AzureStack
    ```

4. Počkejte, než se vypíná všechny fyzické Azure Stack uzly.

> [!Note]  
> Stav napájení fyzického uzlu můžete ověřit podle pokynů výrobce OEM (Original Equipment Manufacturer), který dodal váš Azure Stack hardware. 

## <a name="start-azure-stack"></a>Spustit Azure Stack 

Spusťte Azure Stack pomocí následujících kroků. Postupujte podle těchto kroků bez ohledu na to, jak se Azure Stack zastavilo.

1. Zapněte na každém fyzickém uzlu v prostředí Azure Stack. Podle pokynů výrobce OEM (Original Equipment Manufacturer), který dodal hardware pro váš Azure Stack, ověřte pokyny pro fyzické uzly.

2. Počkejte, dokud se nespustí služby infrastruktury Azure Stack. Služby infrastruktury Azure Stack můžou k dokončení procesu spuštění vyžadovat dvě hodiny. Stav spuštění Azure Stack můžete ověřit pomocí [rutiny **Get-ActionStatus** ](#get-the-startup-status-for-azure-stack).

3. Zajistěte, aby se všechny prostředky tenanta vrátily do stavu před vypnutím. Úlohy spuštěné v prostředcích tenanta možná budete muset po spuštění znovu nakonfigurovat správcem úloh.

## <a name="get-the-startup-status-for-azure-stack"></a>Získat stav spuštění pro Azure Stack

Pomocí následujících kroků Získejte spuštění Azure Stack spouštěcí rutiny:

1. Otevřete privilegovanou relaci koncového bodu z počítače se síťovým přístupem k virtuálním počítačům s Azure Stack ERCS.

2. Z PEP spusťte:

    ```powershell
      Get-ActionStatus Start-AzureStack
    ```

## <a name="troubleshoot-startup-and-shutdown-of-azure-stack"></a>Řešení potíží se spouštěním a vypnutím Azure Stack

Proveďte následující kroky, pokud se infrastruktura a služby tenanta po zapnutí Azure Stack prostředí neúspěšně spustí 2 hodiny. 

1. Otevřete privilegovanou relaci koncového bodu z počítače se síťovým přístupem k virtuálním počítačům s Azure Stack ERCS.

2. Spuštěním příkazu 

    ```powershell
      Test-AzureStack
      ```

3. Zkontrolujte výstup a vyřešte všechny chyby stavu. Další informace najdete v tématu [spuštění ověřovacího testu Azure Stack](azure-stack-diagnostic-test.md).

4. Spuštěním příkazu

    ```powershell
      Start-AzureStack
    ```

5. Pokud při spuštění **Start-AzureStack** dojde k chybě, obraťte se na podporu služeb Microsoft Customer. 

## <a name="next-steps"></a>Další kroky 

Další informace o [diagnostických nástrojích Azure Stack](azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs)
