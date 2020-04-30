---
title: Pomoc a podpora centra Azure Stack
description: Získejte podporu pro Centrum Microsoft Azure Stack.
author: justinha
ms.topic: article
ms.date: 02/26/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 02/26/2020
ms.openlocfilehash: 20d20e5e53a8826ea0514e894bacbfeeeda6c540
ms.sourcegitcommit: 3fd4a38dc8446e0cdb97d51a0abce96280e2f7b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "82580133"
---
# <a name="azure-stack-hub-help-and-support"></a>Pomoc a podpora centra Azure Stack

::: moniker range=">= azs-2002"

Operátory centra Azure Stack můžou pomocí programu **help + support** shromažďovat diagnostické protokoly a posílat je do Microsoftu kvůli řešení problémů. **Pomoc a podpora** na portálu centra Azure Stack lze získat z portálu pro správu. Má prostředky, které pomůžou operátorům pomoci získat další informace o Azure Stack, podívejte se na možnosti podpory a získejte odbornou pomoc.  

![Jak získat přístup k nápovědě a podpoře na portálu Azure Stack hub pro správu](media/azure-stack-help-and-support/help-and-support.png)

## <a name="help-resources"></a>Prostředky pro usnadnění

Operátoři můžou použít **nápovědu a podporu** k získání dalších informací o centru Azure Stack, kontrole možností podpory a získání odborné pomoci.

### <a name="things-to-try-first"></a>Co nejdřív zkuste vyzkoušet

V horní části **okna Help + support** byste se měli vyzkoušet jako první, třeba si přečtěte o nových konceptech, dozvíte se, jak fakturace funguje, nebo zjistit, jaké možnosti podpory jsou k dispozici.

![Samoobslužná podpora ve službě Azure Stack hub](media/azure-stack-help-and-support/get-support-tiles.png)

- **Dokumentaci**. [Dokumentace k operátorovi centra Azure Stack](index.yml) zahrnuje koncepty, témata s postupy a kurzy, které ukazují, jak nabízet služby Azure Stack hub. Mezi tyto služby patří virtuální počítače, databáze SQL, webové aplikace a další.

- **Přečtěte si o fakturaci**. Získejte tipy na [používání a fakturaci](azure-stack-billing-and-chargeback.md).

- **Možnosti podpory**. Operátory centra Azure Stack si můžou vybrat z škály [možností podpory Azure](https://aka.ms/azstacksupport) , které můžou vyhovovat potřebám všech podniků.

### <a name="get-expert-help"></a>Získat odbornou pomoc

Pro integrovaný systém existuje koordinovaný proces eskalace a rozlišení mezi společností Microsoft a našimi hardwarovými partnery OEM (Original Equipment Manufacturer).

Pokud dojde k problému s cloudovou službou, podpora je poskytována prostřednictvím služby Microsoft Customer Support Services (CSS). Můžete vybrat **help** (otazník) v pravém horním rohu portálu pro správu a pak vybrat Nápověda **+ Podpora** a otevřít Nápověda a podpora **– Přehled** a Odeslat novou žádost o podporu. Při vytváření žádosti o podporu se předvede předvolba služby Azure Stack hub. Důrazně doporučujeme, aby zákazníci používali toto prostředí k odeslání lístků místo použití globálních Azure Portal.

Pokud dojde k potížím s nasazením, opravou a aktualizací, hardwarem (včetně nahraditelných jednotek v poli) a jakýmkoli softwarem v hardwarovém zařízení (jako je například software spuštěný na hostiteli životního cyklu hardwaru), obraťte se nejprve na dodavatele hardwaru výrobce OEM. U cokoli jiného kontaktujte Microsoft CSS.

![Získat profesionální nápovědu pro integrované systémy](media/azure-stack-help-and-support/get-support-integrated.png)

Pro Azure Stack Development Kit (ASDK) můžete klást otázky týkající se podpory ve [fóru Azure Stack centra MSDN](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack).

V pravém horním rohu portálu pro správu vyberte **help** (otazník) a pak kliknutím na **Nápověda + podpora** otevřete **Přehled nápovědy**a podpory, který má odkaz na fórum. Fóra MSDN se pravidelně monitorují. Vzhledem k tomu, že ASDK je zkušební prostředí, není oficiální podpora nabídnuta prostřednictvím šablon stylů CSS společnosti Microsoft.

Můžete se také obrátit na fóra MSDN a diskutovat o problémech nebo pořídit online školení a vylepšit své vlastní dovednosti.

![Získat odbornou pomoc pro centrum Azure Stack](media/azure-stack-help-and-support/get-support-cards.png)

### <a name="get-up-to-speed-with-azure-stack-hub"></a>Získejte rychlý přehled pomocí centra Azure Stack

Tato sada kurzů je přizpůsobená v závislosti na tom, jestli používáte ASDK nebo integrované systémy, abyste se svým prostředím mohli rychle dostat do rychlosti.

![Získat kurzy k podpoře centra Azure Stack](media/azure-stack-help-and-support/get-support-tutorials.png)

## <a name="diagnostic-log-collection"></a>Shromažďování protokolů diagnostiky

Diagnostické protokoly můžete společnosti Microsoft odeslat dvěma způsoby:

- [Odeslat protokoly interaktivně](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md): Pokud je tato možnost povolená, spustí se shromažďování protokolů konkrétními upozorněními na stav.
- [Odeslat protokoly nyní](azure-stack-configure-on-demand-diagnostic-log-collection-portal-tzl.md): jako časový rámec pro shromažďování protokolů můžete zvolit konkrétní posuvné okno.

![Možnosti shromažďování protokolů diagnostiky](media/azure-stack-help-and-support/banner-enable-automatic-log-collection.png)

::: moniker-end
::: moniker range="<= azs-1910"

## <a name="diagnostic-log-collection"></a>Shromažďování protokolů diagnostiky

Od verze 1907 existují dva nové způsoby, jak shromažďovat protokoly v **nápovědě a podpoře**:

- **Automatická kolekce**: Pokud je tato možnost povolená, spustí se shromažďování protokolů konkrétními upozorněními na stav.
- **Shromažďovat protokoly nyní**: můžete zvolit posuvné okno 1-4 hodin za posledních sedm dní.

![Možnosti shromažďování protokolů diagnostiky](media/azure-stack-automatic-log-collection/azure-stack-log-collection-overview.png)

Integrované systémy můžou sdílet diagnostické protokoly se službou zákaznické podpory Microsoftu (CSS). Vzhledem k tomu, že Azure Stack Development Kit (ASDK) je zkušební prostředí, není šablonou stylů CSS podporováno. Další informace najdete v tématu [Přehled shromažďování protokolů diagnostiky centra Azure Stack](azure-stack-diagnostic-log-collection-overview.md).

## <a name="help-and-support-for-earlier-releases-azure-stack-hub-pre-1905"></a>Pomoc a podpora pro starší verze Azure Stack centra (pre-1905)

Předchozí verze centra Azure Stack mají také odkaz na **pomoc a podporu** , která přesměruje na [dokumentaci k operátorovi centra Azure Stack](https://aka.ms/adminportaldocs).

![Získat kurzy k podpoře](media/azure-stack-help-and-support/get-support-previous.png)

Pokud dojde k problému s cloudovou službou, podpora je poskytována prostřednictvím služby Microsoft Customer Support Services (CSS). V pravém horním rohu portálu pro správu můžete vybrat **Nápověda** (otazník), vybrat **Nápověda a podpora**a pak vybrat **novou žádost o podporu** , která přímo odešle novou žádost o podporu pomocí šablon stylů CSS.

Pro integrovaný systém existuje koordinovaný proces eskalace a rozlišení mezi společností Microsoft a našimi partnery OEM. Pokud dojde k potížím s Cloud Services, nabídne se podpora prostřednictvím šablon stylů CSS společnosti Microsoft.

Pokud dojde k potížím s nasazením, opravou a aktualizací, hardwarem (včetně nahraditelných jednotek v poli) a jakýmkoli softwarem v hardwarovém zařízení, jako je třeba software spuštěný na hostiteli životního cyklu hardwaru, obraťte se na dodavatele hardwaru výrobce OEM. U cokoli jiného kontaktujte Microsoft CSS.

V případě ASDK můžete klást otázky související s podporou na [webu MSDN centra Azure Stack](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack).

V pravém horním rohu portálu pro správu vyberte **Nápověda** (otazník) a pak vyberte **Nová žádost o podporu** , abyste získali pomoc od expertů v komunitě centra Azure Stack. Vzhledem k tomu, že ASDK je zkušební prostředí, není oficiální podpora nabídnuta prostřednictvím šablon stylů CSS společnosti Microsoft.

::: moniker-end

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [shromažďování protokolů diagnostiky](azure-stack-diagnostic-log-collection-overview-tzl.md).
- Přečtěte si, jak [najít ID cloudu](azure-stack-find-cloud-id.md).
- Přečtěte si o [řešení potíží s Azure Stack hub](azure-stack-troubleshooting.md).
