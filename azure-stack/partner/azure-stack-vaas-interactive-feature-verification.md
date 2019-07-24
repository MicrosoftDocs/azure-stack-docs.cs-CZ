---
title: Testování ověřování interaktivních funkcí v Azure Stack ověřování jako služba | Microsoft Docs
description: Naučte se vytvářet interaktivní testy pro ověřování funkcí pro Azure Stack s ověřováním jako službou.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: af36943ebaa78ad1838506d6614a7fd6671afe4f
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68418470"
---
# <a name="interactive-feature-verification-testing"></a>Testování ověřování interaktivních funkcí  

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

K vyžádání testů pro váš systém můžete použít architekturu testování interaktivních funkcí. Když vyžádáte test, společnost Microsoft použije rozhraní k přípravě testů, které vyžadují ruční interaktivní kroky. Společnost Microsoft může použít rozhraní k zřetězení několika samostatných automatizovaných testů.

Tento článek popisuje jednoduchý ruční scénář. Test kontroluje nahrazení disku v Azure Stack. Rozhraní shromažďuje protokoly diagnostiky v každém kroku. Problémy můžete ladit při jejich hledání. Rozhraní také umožňuje sdílení protokolů vytvořených jinými nástroji nebo procesy a umožňuje vám poskytnout zpětnou vazbu k tomuto scénáři.

> [!Important]  
> Tento článek odkazuje na kroky k provedení identifikace disku. Toto je pouze ukázka, protože jakékoli výsledky shromážděné z pracovního postupu test Pass nelze použít pro nové ověřování řešení.

## <a name="overview-of-interactive-testing"></a>Přehled interaktivního testování

Test nahrazení disku je běžným scénářem. V tomto příkladu má test pět kroků:

1. Vytvoří nový pracovní postup pro **testovací průchod** .
2. Vyberte **test identifikace disku**.
3. Po zobrazení výzvy dokončete krok ručně.
4. Zkontroluje výsledek scénáře.
5. Odeslat výsledek testu společnosti Microsoft.

## <a name="create-a-new-test-pass"></a>Vytvořit nový test Pass

Pokud nemáte k dispozici existující testovací průchod, postupujte prosím podle pokynů pro [Plánování testu](azure-stack-vaas-schedule-test-pass.md).

## <a name="schedule-the-test"></a>Naplánování testu

1. Vyberte **test identifikace disku**.

    > [!Note]  
    > Verze testu se zvýší, protože jsou provedeny vylepšení testovacích materiálů. Nejvyšší verze by měla být vždy použita, pokud společnost Microsoft neurčí jinak.

    ![Alternativní text](media/azure-stack-vaas-interactive-feature-verification/image4.png)

1. Zadejte uživatelské jméno a heslo správce domény, a to tak, že vyberete **Upravit**.

1. Vyberte příslušného agenta spuštění testu/DVM a spusťte test na.

    ![Alternativní text](media/azure-stack-vaas-interactive-feature-verification/image5.png)

1. Vyberte **Odeslat** a spusťte test.

    ![Alternativní text](media/azure-stack-vaas-interactive-feature-verification/image6.png)

1. Přihlaste se k uživatelskému rozhraní pro interaktivní test z agenta vybraného v předchozím kroku.

    ![Alternativní text](media/azure-stack-vaas-interactive-feature-verification/image8.png)

1. Postupujte podle pokynů v **dokumentaci** a **ověření** a Projděte si pokyny od Microsoftu o tom, jak tento scénář provést.

    ![Alternativní text](media/azure-stack-vaas-interactive-feature-verification/image9.png)

1. Vyberte **Další**.

    ![Alternativní text](media/azure-stack-vaas-interactive-feature-verification/image10.png)

1. Postupujte podle pokynů a spusťte skript předkontroly.

    ![Alternativní text](media/azure-stack-vaas-interactive-feature-verification/image11.png)

1. Po úspěšném dokončení skriptu pro kontrolu a spuštění ručního scénáře (nahrazení disku) podle **dokumentace** a odkazů na **ověření** na kartě **informace** .

    ![Alternativní text](media/azure-stack-vaas-interactive-feature-verification/image12.png)

    > [!Important]  
    > Nezavírejte dialogové okno, když provádíte ruční scénář.

1. Po dokončení ručního scénáře postupujte podle pokynů ke spuštění skriptu pro kontrolu po provedení.

    ![Alternativní text](media/azure-stack-vaas-interactive-feature-verification/image13.png)

1. Po úspěšném dokončení ručního scénáře (nahrazení disku) vyberte **Další**.

    ![Alternativní text](media/azure-stack-vaas-interactive-feature-verification/image14.png)

    > [!Important]  
    > Pokud okno zavřete, test se zastaví před tím, než se dokončí.

1. Poskytněte zpětnou vazbu k testovacímu prostředí. Tyto otázky vám pomůžou vyhodnotit úspěšnost a kvalitu vydaných verzí ve scénáři.

    ![Alternativní text](media/azure-stack-vaas-interactive-feature-verification/image15.png)

1. Připojte všechny soubory protokolů, které chcete odeslat společnosti Microsoft.

    ![Alternativní text](media/azure-stack-vaas-interactive-feature-verification/image16.png)

1. Přijměte smlouvu EULA pro odeslání zpětné vazby.

1. Vyberte **Odeslat** a odešlete výsledky společnosti Microsoft.

## <a name="next-steps"></a>Další kroky

- [Monitorování a Správa testů na portálu VaaS](azure-stack-vaas-monitor-test.md)
