---
title: Ověření řešení v centru Azure Stack
description: Naučte se, jak ověřit nové řešení centra Azure Stack pomocí pracovního postupu ověřování řešení.
author: mattbriggs
ms.topic: quickstart
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 0218e1adc239e0f609e79a75e2fd62e364739fe9
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97868254"
---
# <a name="validate-a-solution-in-azure-stack-hub"></a>Ověření řešení v centru Azure Stack

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Naučte se používat pracovní postup **ověření řešení** k ověření nových řešení centra Azure Stack.

Řešení centra Azure Stack je hardwarový soupis materiálu (BoM), který se společně dohodl mezi společností Microsoft a partnerem po splnění požadavků certifikace loga Windows serveru. Řešení musí být znovu certifikováno, pokud došlo ke změně hardwarového kusovníku. Pokud chcete získat další otázky týkající se znovu ověření řešení, obraťte se na tým na adrese [vaashelp@microsoft.com](mailto:vaashelp@microsoft.com) .

Chcete-li ověřit své řešení, spusťte pracovní postup ověření řešení dvakrát. Spusťte ji jednou pro *minimální* podporovanou konfiguraci. Spusťte ji podruhé pro *maximální* podporovanou konfiguraci. Microsoft ověřuje řešení, pokud obě konfigurace přejdou všechny testy.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

## <a name="create-a-solution-validation-workflow"></a>Vytvoření pracovního postupu ověřování řešení

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]

2. Na dlaždici **ověření řešení** vyberte **Spustit** .

    ![Dlaždice pracovního postupu ověření řešení](media/tile_validation-solution.png)

3. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]

4. Vyberte **konfiguraci řešení**.
    - **Minimum**: řešení je nakonfigurované s minimálním podporovaným počtem uzlů.
    - **Maximum**: řešení je nakonfigurované s maximálním podporovaným počtem uzlů.
5. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]

    ![Informace o ověření řešení](media/workflow_validation-solution_info.png)

6. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > Po vytvoření pracovního postupu nejde změnit parametry prostředí.

7. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]
8. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    Budete přesměrováni na stránku souhrn testů.

## <a name="run-solution-validation-tests"></a>Spustit testy ověřování řešení

Na stránce **Souhrn testů pro ověření řešení** se zobrazí seznam testů požadovaných k dokončení ověření.

**Plánování** testu v pracovních postupech ověřování používá společné parametry na úrovni pracovního postupu, které jste zadali během vytváření pracovního postupu (viz [společné parametry pracovních postupů pro ověřování centra Azure Stack jako služba](azure-stack-vaas-parameters.md)). Pokud se některá z hodnot parametrů testu stane neplatných, musíte je znovu zadat podle pokynů v části [Úprava parametrů pracovního postupu](azure-stack-vaas-monitor-test.md#change-workflow-parameters).

> [!NOTE]
> Plánování ověřovacího testu přes existující instanci vytvoří na portálu novou instanci místo staré instance. Protokoly pro starou instanci budou zachovány, ale nejsou přístupné z portálu.<br>
Po úspěšném dokončení testu se akce **plánu** zakáže.

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](includes/azure-stack-vaas-workflow-step_select-agent.md)]

2. Vyberte následující testy:
    - Modul pro simulaci cloudu
    - Test identifikace disku

3. V místní nabídce vyberte **plán** a otevřete tak výzvu pro plánování testovací instance.

4. Zkontrolujte parametry testu a pak vyberte **Odeslat** a naplánujte test pro spuštění.

![Naplánovat test ověřování řešení](media/workflow_validation-solution_schedule-test.png)

## <a name="next-steps"></a>Další kroky

- [Monitorování a Správa testů na portálu VaaS](azure-stack-vaas-monitor-test.md)