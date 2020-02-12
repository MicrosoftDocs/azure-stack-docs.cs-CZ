---
title: Princip ověřování centra Azure Stack jako klíčových konceptů služby
description: Popisuje klíčové koncepty v Azure Stack ověřování pomocí centra jako služba.
author: mattbriggs
ms.topic: article
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 972b0be328f80a82ed3c12076a1f540c52b53ea7
ms.sourcegitcommit: a76301a8bb54c7f00b8981ec3b8ff0182dc606d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2020
ms.locfileid: "77143760"
---
# <a name="validation-as-a-service-key-concepts"></a>Koncepce ověřování jako klíč služby

Tento článek popisuje klíčové koncepty v rámci ověřování jako služby (VaaS).

## <a name="solutions"></a>Řešení

Řešení VaaS představuje řešení centra Azure Stack s konkrétním hardwarovým materiálem (BoM). Řešení VaaS slouží jako kontejner pro pracovní postupy, které běží na řešení centra Azure Stack.

### <a name="create-a-solution-in-the-vaas-portal"></a>Vytvoření řešení na portálu VaaS

1. Přihlaste se k [portálu VaaS](https://azurestackvalidation.com).
2. Na řídicím panelu řešení vyberte **nové řešení**.
3. Zadejte název řešení. Návrhy pojmenování najdete v tématu [konvence pojmenování pro řešení VaaS](azure-stack-vaas-best-practice.md#naming-convention-for-vaas-solutions).
4. Vyberte **Save (Uložit** ) a vytvořte řešení.

## <a name="workflows"></a>Pracovní postupy

Pracovní postup VaaS funguje v kontextu řešení VaaS. Představuje sadu testovacích sad, které vykonávají funkce nasazení centra Azure Stack. Pracovní postup by měl být vytvořen pro všechna nasazení nebo aktualizace softwaru Azure Stackho řešení centra.

Pracovní postupy jsou rozdělené do kategorií podle typu testovacího scénáře. V neoficiálním testování vám pracovní postup **test Pass** umožňuje vybrat testy ze všech dostupných VaaSch materiálů. V oficiálních testech pracovní postupy **ověření** cílí na konkrétní testovací scénáře, které jsou vybrány společností Microsoft.

![Dlaždice pracovního postupu VaaS](media/tile_all-workflows.png)

> [!NOTE]
> Pracovní postup **ověření balíčku** v současné době podporuje dva scénáře: [ověřit balíčky OEM](azure-stack-vaas-validate-oem-package.md) a [Ověřit aktualizace softwaru od Microsoftu](azure-stack-vaas-validate-microsoft-updates.md).

Další informace o typech pracovních postupů najdete v tématu [co je ověřování jako služba pro Azure Stack hub?](azure-stack-vaas-overview.md).

### <a name="getting-started-with-vaas-workflows"></a>Začínáme s pracovními postupy VaaS

1. Na řídicím panelu řešení vytvořte nové řešení nebo vyberte některý z existujících. Tím se aktualizuje a aktivuje dlaždice pracovního postupu.
2. Chcete-li vytvořit nový pracovní postup, vyberte možnost **začít** na libovolné dlaždici. Informace specifické pro jednotlivé pracovní postupy najdete v následujících článcích:
    - Test Pass: [rychlý Start: použití ověřování jako portálu služby k naplánování prvního testu](azure-stack-vaas-schedule-test-pass.md)
    - Ověřování řešení: [ověření nového řešení centra Azure Stack](azure-stack-vaas-validate-solution-new.md)
    - Ověření balíčku (měsíční aktualizace): [ověření aktualizací softwaru od Microsoftu](azure-stack-vaas-validate-microsoft-updates.md)
    - Ověření balíčku (podepisování balíčku): [ověření balíčků OEM](azure-stack-vaas-validate-oem-package.md)

3. Pokud chcete spravovat nebo sledovat existující pracovní postup, vyberte na dlaždici pracovní postup možnost **Spravovat** . Vyberte název pracovního postupu a použijte tlačítko **Upravit** k zobrazení vlastností nebo změně společných parametrů testu.

Další informace o vlastnostech a parametrech pracovního postupu najdete v tématu [společné parametry pracovního postupu pro Azure Stack ověřování centra jako služby](azure-stack-vaas-parameters.md).

## <a name="tests"></a>Testy

Test v VaaS se skládá ze sady operací, které běží na řešení centra Azure Stack. Testy mají různé zamýšlené účely identifikované kategorií, jako je například funkce nebo spolehlivost, a cílí na jednu nebo více služeb centra Azure Stack. Každý test definuje svou vlastní sadu parametrů, z nichž některé jsou určeny společnými parametry obsahujícího pracovního postupu.

Další informace o správě a monitorování testů najdete v tématu [monitorování a Správa testů na portálu VaaS](azure-stack-vaas-monitor-test.md).

Další informace o parametrech testu najdete v tématu [společné parametry pracovního postupu pro Azure Stack ověřování centra jako služby](azure-stack-vaas-parameters.md).

## <a name="agents"></a>Agenti

Spuštění testu VaaS agentem. Testy VaaS spouštějí dva typy agentů:

- **Cloudový agent**. Toto je výchozí agent dostupný v VaaS. Nevyžaduje se žádné nastavení, ale vyžaduje, aby bylo připojení vázané na vaše prostředí a koncové body centra Azure Stack musí být přeložitelný z Internetu. Některé testy nejsou kompatibilní s cloudovým agentem.
- **Místní agent**. To vám umožní spustit ověřování ve scénářích, kdy připojení k vašemu prostředí není proveditelné. Některé testy vyžadují provádění prostřednictvím místního agenta.

Místní agenti nejsou vázáni na žádná konkrétní centra Azure Stack nebo řešení VaaS. V souladu s osvědčenými postupy by se měly spouštět mimo Azure Stack centrálního prostředí.

Pokyny k přidání místního agenta najdete v tématu [nasazení místního agenta](azure-stack-vaas-local-agent.md).

## <a name="next-steps"></a>Další kroky

- [Osvědčené postupy pro ověřování jako službu](azure-stack-vaas-best-practice.md)