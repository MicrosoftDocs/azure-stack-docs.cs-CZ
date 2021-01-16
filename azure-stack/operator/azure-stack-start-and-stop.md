---
title: Spustit a zastavit
titleSuffix: Azure Stack Hub
description: Naučte se spouštět a zastavovat centrum Azure Stack.
author: PatAltimore
ms.topic: how-to
ms.date: 03/04/2020
ms.author: patricka
ms.reviewer: misainat
ms.lastreviewed: 10/15/2019
ms.openlocfilehash: 4f9a6e7ddd52ece2b417221ded8afe9d92c16bca
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2021
ms.locfileid: "98255720"
---
# <a name="start-and-stop-azure-stack-hub"></a>Spuštění a zastavení centra Azure Stack

Postupujte podle pokynů v tomto článku, abyste správně vypnuli a restartovali Azure Stack služby centra. *Zastavení* se fyzicky vypne a vypíná celé prostředí Azure Stack hub. *Zahajte* pravomoci na všech rolích infrastruktury a vrátí prostředky tenanta do stavu napájení, který existoval před vypnutím.

## <a name="stop-azure-stack-hub"></a>Zastavit Azure Stack centra

Zastavte nebo vypněte centrum Azure Stack pomocí následujících kroků:

1. Připravte všechny úlohy spuštěné v prostředcích tenanta prostředí Azure Stack hub pro nadcházející vypínání.

2. Otevřete privilegovanou relaci koncového bodu (PEP) z počítače se síťovým přístupem k virtuálním počítačům ERCS centra Azure Stack. Pokyny najdete v tématu [použití privilegovaného koncového bodu v centru Azure Stack](azure-stack-privileged-endpoint.md).

3. Z PEP spusťte:

    ```powershell
      Stop-AzureStack
    ```

4. Počkejte, než se vypíná všechny uzly Azure Stack centra fyzického centra.

> [!Note]
> Stav napájení fyzického uzlu můžete ověřit podle pokynů výrobce OEM (Original Equipment Manufacturer), který dodal hardware centra Azure Stack.

## <a name="start-azure-stack-hub"></a>Spustit centrum Azure Stack

Spusťte Azure Stack centrum pomocí následujících kroků. Postupujte podle těchto kroků bez ohledu na to, jak se centrum Azure Stack zastavilo.

1. Zapněte všechny fyzické uzly v prostředí Azure Stack hub. Podle pokynů výrobce OEM, který dodal hardware pro centrum Azure Stack, ověřte, jestli jsou pro fyzické uzly pokyny pro zapnutí.

2. Počkejte, dokud se nespustí služby infrastruktury centra Azure Stack. Služby infrastruktury centra Azure Stack můžou k dokončení procesu spuštění vyžadovat dvě hodiny. Pomocí [rutiny **Get-ActionStatus**](#get-the-startup-status-for-azure-stack-hub)můžete ověřit stav spuštění centra Azure Stack.

3. Zajistěte, aby se všechny prostředky tenanta vrátily do stavu před vypnutím. Úlohy spuštěné v prostředcích tenanta možná budete muset po spuštění znovu nakonfigurovat správcem úloh.

## <a name="get-the-startup-status-for-azure-stack-hub"></a>Získá stav spuštění centra Azure Stack.

Pomocí následujících kroků Získejte spuštění spouštěcí rutiny centra Azure Stack:

1. Otevřete privilegovanou relaci koncového bodu z počítače se síťovým přístupem k ERCSm virtuálním počítačům centra Azure Stack.

2. Z PEP spusťte:

    ```powershell
      Get-ActionStatus Start-AzureStack
    ```

## <a name="troubleshoot-startup-and-shutdown-of-azure-stack-hub"></a>Řešení potíží se spouštěním a vypnutím centra Azure Stack

Následující kroky proveďte v případě, že se infrastruktura a služby tenanta po zapnutí prostředí Azure Stackého centra nespustí dvě hodiny úspěšně.

1. Otevřete privilegovanou relaci koncového bodu z počítače se síťovým přístupem k ERCSm virtuálním počítačům centra Azure Stack.

2. Spusťte tento příkaz:

    ```powershell
      Test-AzureStack
      ```

3. Zkontrolujte výstup a vyřešte všechny chyby stavu. Další informace najdete v tématu [spuštění ověřovacího testu centra Azure Stack](azure-stack-diagnostic-test.md).

4. Spusťte tento příkaz:

    ```powershell
      Start-AzureStack
    ```

5. Pokud při spuštění **Start-AzureStack** dojde k chybě, obraťte se na podpora Microsoftu.

## <a name="next-steps"></a>Další kroky

Další informace o [diagnostických nástrojích centra Azure Stack](./diagnostic-log-collection.md)