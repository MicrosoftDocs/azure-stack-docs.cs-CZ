---
title: Testování ověřování interaktivních funkcí
titleSuffix: Azure Stack Hub
description: Naučte se vytvářet interaktivní testy pro ověřování funkcí pro Azure Stack centra s ověřováním jako službou.
author: mattbriggs
ms.topic: tutorial
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ea2193b29dce09db47d87444400f0d6c5b22dbae
ms.sourcegitcommit: 4e1c948ae4a498bd730543b0704bbc2b0d88e1ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2020
ms.locfileid: "77625335"
---
# <a name="interactive-feature-verification-testing"></a>Testování ověřování interaktivních funkcí  

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

K vyžádání testů pro váš systém můžete použít architekturu testování interaktivních funkcí. Když vyžádáte test, společnost Microsoft použije rozhraní k přípravě testů, které vyžadují ruční interaktivní kroky. Společnost Microsoft může použít rozhraní k zřetězení několika samostatných automatizovaných testů.

Tento článek popisuje jednoduchý ruční scénář. Test kontroluje nahrazení disku v Azure Stackovém centru. Rozhraní shromažďuje protokoly diagnostiky v každém kroku. Problémy můžete ladit při jejich hledání. Rozhraní také umožňuje sdílení protokolů vytvořených jinými nástroji nebo procesy a umožňuje vám poskytovat zpětnou vazbu k tomuto scénáři.

> [!Important]  
> Tento článek odkazuje na kroky k provedení testu identifikace disku. Toto je pouze ukázka a jakékoli výsledky shromážděné z pracovního postupu test Pass nelze použít pro nové ověřování řešení.

## <a name="overview-of-interactive-testing"></a>Přehled interaktivního testování

Test nahrazení disku je běžným scénářem. V tomto příkladu má test pět kroků:

1. Vytvoří nový pracovní postup pro **testovací průchod** .
2. Vyberte **test identifikace disku**.
3. Po zobrazení výzvy dokončete krok ručně.
4. Zkontroluje výsledek scénáře.
5. Odeslat výsledek testu společnosti Microsoft.

## <a name="create-a-new-test-pass"></a>Vytvořit nový test Pass

Pokud nemáte k dispozici existující testovací průchod, postupujte podle pokynů pro [Plánování testu](azure-stack-vaas-schedule-test-pass.md).

## <a name="schedule-the-test"></a>Naplánování testu

1. Vyberte **test identifikace disku**.

    > [!Note]  
    > Verze testu se zvýší, protože jsou provedeny vylepšení testovacích materiálů. Nejvyšší verze by měla být vždy použita, pokud společnost Microsoft neurčí jinak.

    ![Test identifikace disku – interaktivní testování v centru Azure Stack](media/azure-stack-vaas-interactive-feature-verification/image4.png)

2. Vyberte **Upravit** a zadejte uživatelské jméno a heslo správce domény.

3. Vyberte příslušného agenta spuštění testu/DVM a spusťte test na.

    ![Vybrat agenta spuštění testu – interaktivní testování v centru Azure Stack](media/azure-stack-vaas-interactive-feature-verification/image5.png)

4. Vyberte **Odeslat** a spusťte test.

    ![Kontrola a odeslání testu – interaktivní testování v centru Azure Stack](media/azure-stack-vaas-interactive-feature-verification/image6.png)

5. Přihlaste se k uživatelskému rozhraní pro interaktivní test z agenta vybraného v předchozím kroku.

    ![Test identifikace disku – interaktivní testování v centru Azure Stack](media/azure-stack-vaas-interactive-feature-verification/image8.png)

6. Postupujte podle pokynů v **dokumentaci** a **ověření** a Projděte si pokyny od Microsoftu o tom, jak tento scénář provést.

    ![Dokumentace a ověřovací odkazy v testu identifikace disku](media/azure-stack-vaas-interactive-feature-verification/image9.png)

7. Vyberte **Další**.

    ![Vybrat další – interaktivní testování v centru Azure Stack](media/azure-stack-vaas-interactive-feature-verification/image10.png)

8. Postupujte podle pokynů a spusťte skript předkontroly.

    ![Spuštění předkontrolního skriptu – interaktivní testování v centru Azure Stack](media/azure-stack-vaas-interactive-feature-verification/image11.png)

9. Po úspěšném dokončení skriptu předkontroly spusťte ruční scénář (nahrazení disku), jak je popsáno v **dokumentaci** a na odkazech na **ověření** na kartě **informace** .

    ![Spuštění ručního scénáře – interaktivní testování v centru Azure Stack](media/azure-stack-vaas-interactive-feature-verification/image12.png)

    > [!Important]  
    > Při provádění ručního scénáře Nezavírejte dialogové okno.

10. Po dokončení ručního scénáře postupujte podle pokynů ke spuštění skriptu pro kontrolu po provedení.

    ![Spuštění skriptu postcheck – interaktivní testování v centru Azure Stack](media/azure-stack-vaas-interactive-feature-verification/image13.png)

11. Po úspěšném dokončení ručního scénáře (nahrazení disku) vyberte **Odeslat**.

    ![Odeslat test identifikace jednotky – interaktivní testování v centru Azure Stack](media/azure-stack-vaas-interactive-feature-verification/image14.png)

    > [!Important]  
    > Pokud okno zavřete, test se zastaví před dokončením.

12. Poskytněte zpětnou vazbu k testovacímu prostředí. Tyto otázky vám pomůžou vyhodnotit úspěšnost a kvalitu vydaných verzí ve scénáři.

    ![Poskytnutí zpětné vazby pro interaktivní testování v centru Azure Stack](media/azure-stack-vaas-interactive-feature-verification/image15.png)

13. Připojte všechny soubory protokolů, které chcete odeslat společnosti Microsoft.

    ![Připojit soubory protokolu – interaktivní testování v centru Azure Stack](media/azure-stack-vaas-interactive-feature-verification/image16.png)

14. Přijměte smlouvu EULA pro odeslání zpětné vazby.

15. Vyberte **Odeslat** a odešlete výsledky společnosti Microsoft.

## <a name="next-steps"></a>Další kroky

- [Monitorování a Správa testů na portálu pro ověřování centra Azure Stack](azure-stack-vaas-monitor-test.md)
