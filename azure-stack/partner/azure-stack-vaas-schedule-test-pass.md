---
title: Použití ověřování jako služby pro Azure Stackho portálu k naplánování prvního testu | Microsoft Docs
description: K naplánování prvního testu použijte ověřování jako službu Azure Stackového portálu.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ms.openlocfilehash: bf377234b71683e6c56bef030cd5de5b4a06a743
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68418381"
---
# <a name="scheduling-a-test"></a>Plánování testu

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Naplánujte test na portálu ověřování jako služby (VaaS) pro vaše řešení Azure Stack. Řešení VaaS představuje řešení Azure Stack s konkrétním hardwarovým materiálem (BoM). Můžete naplánovat test, který zkontroluje, že váš hardware může běžet Azure Stack.

Chcete-li zkontrolovat řešení, vytvořte pracovní postup pro test. Pracovní postup VaaS funguje v kontextu řešení VaaS. Představuje sadu testovacích sad, které vykonávají funkce Azure Stackho nasazení na vašem hardwaru. Přidejte parametry prostředí vašeho řešení a vyberte jeden nebo více testů, které chcete spustit ve vašem řešení.

I když lze pracovní postup testovacího průchodu použít ke spuštění libovolného testu, který poskytuje VaaS, včetně testů z pracovních postupů ověření, nejsou výsledky z pracovního postupu test Pass považovány za *oficiální*. Informace o oficiálních ověřovacích pracovních postupech najdete v tématu [pracovní postupy](azure-stack-vaas-key-concepts.md#workflows).

## <a name="prerequisites"></a>Požadavky

Než budete postupovat podle tohoto rychlého startu, měli byste dokončit následující položky:

- [Nastavení ověřování jako prostředků služby](azure-stack-vaas-set-up-resources.md)
- [Nasazení místního agenta](azure-stack-vaas-local-agent.md) Požadovanou
- [Koncepce ověřování jako klíč služby](azure-stack-vaas-key-concepts.md) Požadovanou

## <a name="start-a-workflow"></a>Spustit pracovní postup

![Přihlaste se na portál VaaS](media/vaas_portalsignin.png)

Přihlaste se k portálu, vyberte nebo vytvořte řešení a pak vyberte řešení.

1. Přihlaste se k [portálu VaaS](https://azurestackvalidation.com).
2. Zadejte název existujícího řešení nebo vyberte **nové řešení** a vytvořte nové řešení. Pokyny najdete v tématu [vytvoření řešení na portálu VaaS](azure-stack-vaas-key-concepts.md#create-a-solution-in-the-vaas-portal).
3. Na dlaždici **test Passes** (spustit) vyberte **začít** .

## <a name="specify-parameters"></a>Zadat parametry

![Alternativní text](media/vaas_test_pass_parameters.png)

Zadejte parametry, které se vztahují na všechny testy v rámci pracovního postupu.

1. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]
2. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]
3. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]
4. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]
5. Vyberte možnost **Další** a vyberte testy, které chcete naplánovat.

## <a name="select-tests-to-run"></a>Vyberte testy, které chcete spustit.

Vybrané testy budou naplánovány po vytvoření pracovního postupu.

1. Vyberte testy, které chcete spustit ve vašem pracovním postupu.

    Pokud chcete přepsat společné parametry (tj. parametry uvedené v předchozí části) pro libovolný test, vyberte v odkazu **Upravit** vedle zadat nové hodnoty.

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](includes/azure-stack-vaas-workflow-step_select-agent.md)]

1. Kliknutím na tlačítko **Další** Projděte pracovní postup.

## <a name="review-and-submit"></a>Zkontrolovat a odeslat

Dokončit vytvoření pracovního postupu

1. Zkontrolujte zobrazené informace.

    Služba vytvoří pracovní postup s poskytnutými informacemi a vybrané testy budou naplánovány.

    Pokud se cokoli zdá být nesprávného, použijte **Předchozí** tlačítka pro přechod na předchozí oddíl.

1. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]

## <a name="next-steps"></a>Další postup

- [Monitorování a Správa testů na portálu VaaS](azure-stack-vaas-monitor-test.md)
