---
title: Koncepce ověřování Azure Stack jako klíč služby | Microsoft Docs
description: Popisuje klíčové koncepty Azure Stack ověřování jako služby.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 1ffb66bb1abe9f2ffb64162f834c682df2aaa609
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68418401"
---
# <a name="validation-as-a-service-key-concepts"></a>Koncepce ověřování jako klíč služby

Tento článek popisuje klíčové koncepty v rámci ověřování jako služby (VaaS).

## <a name="solutions"></a>Řešení

Řešení VaaS představuje řešení Azure Stack s konkrétním hardwarovým materiálem (BoM). Řešení VaaS slouží jako kontejner pro pracovní postupy, které běží na řešení Azure Stack.

### <a name="create-a-solution-in-the-vaas-portal"></a>Vytvoření řešení na portálu VaaS

1. Přihlaste se k [portálu VaaS](https://azurestackvalidation.com).
2. Na řídicím panelu řešení vyberte **nové řešení**.
3. Zadejte název řešení. Návrhy pojmenování najdete v tématu konvence pojmenování [pro řešení VaaS](azure-stack-vaas-best-practice.md#naming-convention-for-vaas-solutions).
4. Vyberte **Save (Uložit** ) a vytvořte řešení.

## <a name="workflows"></a>Workflows

Pracovní postup VaaS funguje v kontextu řešení VaaS. Představuje sadu testovacích sad, které vykonávají funkce nasazení Azure Stack. Pracovní postup by měl být vytvořen pro každé nasazení nebo aktualizaci softwaru Azure Stack řešení.

Pracovní postupy jsou rozdělené do kategorií podle typu testovacího scénáře. V neoficiálním testování vám pracovní postup **test Pass** umožňuje vybrat testy ze všech dostupných VaaSch materiálů. V oficiálních testech pracovní postupy **ověření** cílí na konkrétní testovací scénáře, které jsou vybrány společností Microsoft.

![Dlaždice pracovního postupu VaaS](media/tile_all-workflows.png)

> [!NOTE]
> Pracovní postup **ověření balíčku** v současné době podporuje dva scénáře: [Ověří balíčky OEM](azure-stack-vaas-validate-oem-package.md) a [ověří aktualizace softwaru od Microsoftu](azure-stack-vaas-validate-microsoft-updates.md).

Další informace o typech pracovních postupů najdete v tématu [co je ověřování jako služba pro Azure Stack?](azure-stack-vaas-overview.md).

### <a name="getting-started-with-vaas-workflows"></a>Začínáme s pracovními postupy VaaS

1. Na řídicím panelu řešení vytvořte nové řešení nebo vyberte některý z existujících. Tím se aktualizuje a aktivuje dlaždice pracovního postupu.
2. Chcete-li vytvořit nový pracovní postup, vyberte možnost **začít** na libovolné dlaždici. Informace specifické pro jednotlivé pracovní postupy najdete v následujících článcích:
    - Průchod testu: [Rychlé zprovoznění: Použití ověřování jako portálu služby k naplánování prvního testu](azure-stack-vaas-schedule-test-pass.md)
    - Ověřování řešení: [Ověření nového řešení Azure Stack](azure-stack-vaas-validate-solution-new.md)
    - Ověření balíčku (měsíční aktualizace): [Ověřit aktualizace softwaru od Microsoftu](azure-stack-vaas-validate-microsoft-updates.md)
    - Ověření balíčku (podpis balíčku): [Ověřit balíčky OEM](azure-stack-vaas-validate-oem-package.md)

3. Pokud chcete spravovat nebo sledovat existující pracovní postup, vyberte na dlaždici pracovní postup možnost **Spravovat** . Vyberte název pracovního postupu a použijte tlačítko **Upravit** k zobrazení vlastností nebo změně společných parametrů testu.

Další informace o vlastnostech a parametrech pracovního postupu najdete v tématu [společné parametry pracovních postupů pro Azure Stack ověřování jako služby](azure-stack-vaas-parameters.md).

## <a name="tests"></a>Testy

Test v VaaS se skládá ze sady operací, které běží na řešení Azure Stack. Testy mají různé zamýšlené účely identifikované kategorií, jako je například funkce nebo spolehlivost, a cílí na jednu nebo více služeb Azure Stack. Každý test definuje svou vlastní sadu parametrů, z nichž některé jsou určeny společnými parametry obsahujícího pracovního postupu.

Další informace o správě a monitorování testů najdete v tématu [monitorování a Správa testů na portálu VaaS](azure-stack-vaas-monitor-test.md).

Další informace o parametrech testu najdete v tématu [společné parametry pracovních postupů pro Azure Stack ověřování jako služby](azure-stack-vaas-parameters.md).

## <a name="agents"></a>Agenti

Spuštění testu VaaS agentem. Testy VaaS spouštějí dva typy agentů:

- **Cloudový agent**. Toto je výchozí agent dostupný v VaaS. Není potřeba žádné nastavení, ale vyžaduje se připojení k vašemu prostředí a Azure Stack koncové body musí být přeložitelný z Internetu. Některé testy nejsou kompatibilní s cloudovým agentem.
- **Místní agent**. To vám umožní spustit ověřování ve scénářích, kdy připojení k vašemu prostředí není proveditelné. Některé testy vyžadují provádění prostřednictvím místního agenta.

Místní agenti nejsou vázáni na žádná konkrétní Azure Stack ani VaaS řešení. V souladu s osvědčenými postupy by měly být spuštěny mimo Azure Stack prostředí.

Pokyny k přidání místního agenta najdete v tématu [nasazení místního agenta](azure-stack-vaas-local-agent.md).

## <a name="next-steps"></a>Další kroky

- [Osvědčené postupy pro ověřování jako službu](azure-stack-vaas-best-practice.md)