---
title: Použít ověření jako služba pro portálu Azure Stack k naplánování vaší první test | Dokumentace Microsoftu
description: Používejte ověření jako služba pro portálu Azure Stack k naplánování prvního testu.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ms.openlocfilehash: 3fb5c3deeddb6f3ee381ca45df76feebf3405b21
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "64300502"
---
# <a name="scheduling-a-test"></a>Plánování testu

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Plánování testů při ověřování jako služby (VaaS) portál pro řešení Azure Stack. Řešení VaaS představuje řešení Azure Stack konkrétní hardware kusovník (BoM). Můžete naplánovat testu ke kontrole, že můžete spustit váš hardware Azure Stack.

Pokud chcete zkontrolovat vaše řešení, vytvoření pracovního postupu pro test. Pracovní postup VaaS pracuje v rámci kontextu VaaS řešení. Představuje sadu testovacích sad, které vykonávají funkce pro nasazení služby Azure Stack na hardwaru. Přidejte parametry životního prostředí vašeho řešení a vyberte jeden nebo více testů ke spuštění ve vašem řešení.

Při průchodu testu pracovní postup je možné spustit jakýkoli test poskytované VaaS, včetně testů z pracovních postupů ověřování, se nepovažují výsledky z pracovního postupu průchodu testů *oficiální*. Informace o pracovních postupech oficiální ověřování najdete v tématu [pracovních postupů](azure-stack-vaas-key-concepts.md#workflows).

## <a name="prerequisites"></a>Požadavky

Než budete postupovat podle tohoto rychlého startu, musí dokončit následující položky:

- [Nastavení ověření jako prostředky služby](azure-stack-vaas-set-up-resources.md)
- [Nasazení místního agenta](azure-stack-vaas-local-agent.md) (povinné)
- [Ověření jako klíčové koncepty služby](azure-stack-vaas-key-concepts.md) (povinné)

## <a name="start-a-workflow"></a>Spustit pracovní postup

![Přihlaste se do portálu VaaS](media/vaas_portalsignin.png)

Přihlaste se k portálu, vyberte nebo vytváření řešení a vyberte řešení.

1. Přihlaste se k [VaaS portál](https://azurestackvalidation.com).
2. Zadejte název existujícího řešení nebo vyberte **nové řešení** k vytvoření nového řešení. Pokyny najdete v tématu [vytvořit řešení na portálu VaaS](azure-stack-vaas-key-concepts.md#create-a-solution-in-the-vaas-portal).
3. Vyberte **Start** na **úspěšných testů** dlaždici.

## <a name="specify-parameters"></a>Zadejte parametry

![Alternativní Text](media/vaas_test_pass_parameters.png)

Zadejte parametry, které platí pro všechny testy v rámci pracovního postupu.

1. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]
2. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]
3. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]
4. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]
5. Vyberte **Další** pro výběr testů, které chcete naplánovat.

## <a name="select-tests-to-run"></a>Vybrat testy ke spuštění

Po vytvoření pracovního postupu bude naplánována testy, které jste vybrali.

1. Vyberte testy, které chcete spustit v pracovním postupu.

    Pokud chcete přepsat společné parametry (to znamená, že parametry uvedené v předchozí části) pro jakýkoli test, vyberte na **upravit** odkaz potom můžete zadat nové hodnoty.

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](includes/azure-stack-vaas-workflow-step_select-agent.md)]

1. Vyberte **Další** Kontrola pracovního postupu.

## <a name="review-and-submit"></a>Zkontrolovat a odeslat

Dokončení vytvoření pracovního postupu.

1. Zkontrolujte zobrazené informace.

    Služba vytvoří pracovního postupu pomocí zadaných informací a bude naplánováno vybrané testy.

    Pokud se zobrazí hodnota nesprávné, použijte **předchozí** tlačítka přejít na předchozí části.

1. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]

## <a name="next-steps"></a>Další postup

- [Monitorování a správa testů na portálu VaaS](azure-stack-vaas-monitor-test.md)
