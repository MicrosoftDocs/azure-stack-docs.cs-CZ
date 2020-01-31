---
title: Ověřit nové řešení Azure Stack
description: Naučte se, jak ověřit nové řešení Azure Stack pomocí ověřování jako služby.
author: mattbriggs
ms.topic: quickstart
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: b3e62141988f99706c652124943da0fbabf44439
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76884021"
---
# <a name="validate-a-new-azure-stack-solution"></a>Ověřit nové řešení Azure Stack

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Naučte se používat pracovní postup **ověření řešení** k certifikaci nových Azure Stack řešení.

Azure Stack řešení je hardwarový soupis materiálu (BoM), který se společně dohodl mezi společností Microsoft a partnerem po splnění požadavků certifikace loga Windows serveru. Řešení musí být znovu certifikováno, pokud došlo ke změně hardwarového kusovníku. Pokud chcete získat další otázky týkající se recertifikace řešení, obraťte se na tým na [vaashelp@microsoft.com](mailto:vaashelp@microsoft.com).

Chcete-li certifikovat vaše řešení, spusťte pracovní postup ověření řešení dvakrát. Spusťte ji jednou pro *minimální* podporovanou konfiguraci. Spusťte ji podruhé pro *maximální* podporovanou konfiguraci. Společnost Microsoft toto řešení osvědčí, pokud obě konfigurace projde všemi testy.

[!INCLUDE [azure-stack-vaas-workflow-validation-completion](includes/azure-stack-vaas-workflow-validation-completion.md)]

## <a name="create-a-solution-validation-workflow"></a>Vytvoření pracovního postupu ověřování řešení

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-solution](includes/azure-stack-vaas-workflow-step_select-solution.md)]

3. Na dlaždici **ověření řešení** vyberte **Spustit** .

    ![Dlaždice pracovního postupu ověření řešení](media/tile_validation-solution.png)

4. [!INCLUDE [azure-stack-vaas-workflow-step_naming](includes/azure-stack-vaas-workflow-step_naming.md)]

5. Vyberte **konfiguraci řešení**.
    - **Minimum**: řešení je nakonfigurované s minimálním podporovaným počtem uzlů.
    - **Maximum**: řešení je nakonfigurované s maximálním podporovaným počtem uzlů.
6. [!INCLUDE [azure-stack-vaas-workflow-step_upload-stampinfo](includes/azure-stack-vaas-workflow-step_upload-stampinfo.md)]

    ![Informace o ověření řešení](media/workflow_validation-solution_info.png)

7. [!INCLUDE [azure-stack-vaas-workflow-step_test-params](includes/azure-stack-vaas-workflow-step_test-params.md)]

    > [!NOTE]
    > Po vytvoření pracovního postupu nelze změnit parametry prostředí.

8. [!INCLUDE [azure-stack-vaas-workflow-step_tags](includes/azure-stack-vaas-workflow-step_tags.md)]
9. [!INCLUDE [azure-stack-vaas-workflow-step_submit](includes/azure-stack-vaas-workflow-step_submit.md)]
    Budete přesměrováni na stránku souhrn testů.

## <a name="run-solution-validation-tests"></a>Spustit testy ověřování řešení

Na stránce **Souhrn testů ověřování řešení** se zobrazí seznam testů požadovaných k dokončení ověření.

**Plánování** testu v pracovních postupech ověřování používá společné parametry na úrovni pracovního postupu, které jste zadali během vytváření pracovního postupu (viz [společné parametry pracovních postupů pro Azure Stack ověřování jako služby](azure-stack-vaas-parameters.md)). Pokud se některá z hodnot parametrů testu stane neplatných, musíte je znovu zadat podle pokynů v části [Úprava parametrů pracovního postupu](azure-stack-vaas-monitor-test.md#change-workflow-parameters).

> [!NOTE]
> Plánování ověřovacího testu přes existující instanci vytvoří na portálu novou instanci místo staré instance. Protokoly pro starou instanci budou zachovány, ale nebudou přístupné z portálu.  
Po úspěšném dokončení testu se akce **plánu** zakáže.

1. [!INCLUDE [azure-stack-vaas-workflow-step_select-agent](includes/azure-stack-vaas-workflow-step_select-agent.md)]

2. Vyberte následující testy:
    - Modul pro simulaci cloudu
    - Sada COMPUTE SDK Operational Suite
    - Test identifikace disku
    - Operační sada sady SDK rozšíření trezoru klíčů
    - Sada SDK pro Trezor klíčů – operační sada
    - Operační sada sady SDK sítě
    - Sada SDK pro účet úložiště – operační sada

3. V místní nabídce vyberte **plán** a otevřete tak výzvu pro plánování testovací instance.

4. Zkontrolujte parametry testu a pak vyberte **Odeslat** a naplánujte test pro spuštění.

![Naplánovat test ověřování řešení](media/workflow_validation-solution_schedule-test.png)

## <a name="next-steps"></a>Další kroky

- [Monitorování a Správa testů na portálu VaaS](azure-stack-vaas-monitor-test.md)