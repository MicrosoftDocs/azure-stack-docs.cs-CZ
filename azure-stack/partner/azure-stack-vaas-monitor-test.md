---
title: Monitorování a Správa testů na portálu Azure Stack VaaS | Microsoft Docs
description: Sledujte a spravujte testy na portálu Azure Stack VaaS.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 87ba89298c0a5a134cb0b3b1a2b7e771151e46d9
ms.sourcegitcommit: 102ef41963b5d2d91336c84f2d6af3fdf2ce11c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73955910"
---
# <a name="monitor-and-manage-tests-in-the-vaas-portal"></a>Monitorování a Správa testů na portálu VaaS

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Po plánování testů proti vašemu Azure Stack řešení začne služba ověřování jako služby (VaaS) začínat stav spuštění testu. Tyto informace jsou k dispozici na portálu VaaS spolu s akcemi jako přeplánování a rušení testů.

## <a name="navigate-to-the-workflow-tests-summary-page"></a>Přejít na stránku souhrn testů pracovního postupu

1. Na řídicím panelu řešení vyberte existující řešení, které obsahuje alespoň jeden pracovní postup.

    ![Dlaždice pracovního postupu](media/tile_all-workflows.png)

1. Na dlaždici pracovní postup vyberte **Spravovat** . Na další stránce jsou vypsány pracovní postupy vytvořené pro vybrané řešení.

1. Vyberte název pracovního postupu a otevřete jeho souhrn testů.

## <a name="change-workflow-parameters"></a>Změnit parametry pracovního postupu

Můžete upravit [parametry testu](azure-stack-vaas-parameters.md#test-parameters) zadané během vytváření pracovního postupu pro libovolný typ pracovního postupu.

1. Na stránce s přehledem testů vyberte tlačítko **Upravit** .

1. Zadejte nové hodnoty podle [společných parametrů pracovního postupu pro Azure Stack ověřování jako služby](azure-stack-vaas-parameters.md).

1. Vyberte **Odeslat** a uložte hodnoty.

> [!NOTE]
> V pracovním postupu **test Pass** budete muset dokončit výběr testu a přejít na stránku recenze, aby bylo možné uložit nové hodnoty parametrů.

### <a name="add-tests-test-pass-only"></a>Přidat testy (jenom testovací průchod)

V pracovních postupech **test Pass** umožňují obě tlačítka **Přidat testy** a **Úpravy** naplánovat nové testy v pracovním postupu.

> [!TIP]
> Vyberte možnost **Přidat testy** , pokud chcete naplánovat pouze nové testy a nemusíte upravovat parametry pro pracovní postup **testovacího průchodu** .

## <a name="managing-test-instances"></a>Správa instancí testu

Pro neoficiální spuštění (to znamená pracovní postup **testovacího průchodu** ) obsahuje stránka Souhrn testů seznam testů naplánovaných na řešení Azure Stack.

V případě oficiálních spuštění (tj. **ověřovací** pracovní postupy) obsahuje stránka Souhrn testů seznam testů požadovaných k dokončení ověření Azure Stack řešení. Ověřovací testy jsou plánovány na této stránce.

Každá plánovaná instance testu zobrazuje následující informace:

| Column | Popis |
| --- | --- |
| Název testu | Název a verze testu. |
| Kategorie | Účel testu. |
| Vytvořeno | Čas, kdy byl test naplánován. |
| Začínáme | Čas spuštění testu. |
| Délka | Doba, po kterou byl test spuštěn. |
| Stav | Stav nebo výsledek testu. Předběžné spuštění nebo průběžné stavy jsou: `Pending`, `Running`. Stavy terminálu jsou: `Cancelled`, `Failed`, `Aborted``Succeeded`. |
| Název agenta | Název agenta, který test spustil. |
| Celkový počet operací | Celkový počet operací, které se během testu pokoušely. |
| Úspěšné operace | Počet operací, které byly během testu úspěšné. |
|  Neúspěšné operace | Počet operací, které během testu selhaly. |

### <a name="actions"></a>Akce

Každá instance testu obsahuje seznam dostupných akcí, které můžete provést při výběru místní nabídky **[...]** v tabulce instance testu.

#### <a name="view-information-about-the-test-definition"></a>Zobrazit informace o definici testu

Chcete-li zobrazit obecné informace o definici testu, vyberte možnost **Zobrazit informace** z kontextové nabídky. Sdílí se se všemi testovacími instancemi se stejným názvem a verzí.

| Test – vlastnost | Popis |
| -- | -- |
| Název testu | Název testu. |
| Testovací verze | Verze testu. |
| Vydavatel | Vydavatel testu. |
| Kategorie |  Účel testu. |
| Cílové služby | Testované služby Azure Stack. |
| Popis | Popis testu |
| Odhadovaná doba trvání (minuty) | Očekávaný modul runtime testu. |
| Odkazy | Jakékoli relevantní informace o testu nebo kontaktních bodech. |

#### <a name="view-test-instance-parameters"></a>Zobrazit parametry instance testu

V místní nabídce vyberte možnost **Zobrazit parametry** a zobrazte parametry poskytované testovací instanci v čase plánování. Citlivé řetězce, jako jsou hesla, se nezobrazují. Tato akce je k dispozici pouze pro testy, které byly naplánovány.

Toto okno obsahuje následující metadata pro všechny instance testu:

| Vlastnost instance testu | Popis |
| -- | -- |
| Název testu | Název testu. |
| Testovací verze | Verze testu. |
| ID testovací instance | Identifikátor GUID identifikující konkrétní instanci testu. |

#### <a name="view-test-instance-operations"></a>Zobrazit operace instance testu

Výběrem **Zobrazit operace** z kontextové nabídky zobrazíte podrobný stav operací provedených během testu. Tato akce je k dispozici pouze pro testy, které byly naplánovány.

![Zobrazit operace](media/manage-test_context-menu-operations.png)

#### <a name="download-logs-for-a-completed-test-instance"></a>Stažení protokolů pro dokončenou instanci testu

Vyberte možnost **Stáhnout protokoly** z kontextové nabídky a stáhněte soubor `.zip` výstup protokolů během provádění testu. Tato akce je k dispozici pouze pro testy, které byly dokončeny, to znamená test se stavem buď `Cancelled`, `Failed`, `Aborted`nebo `Succeeded`.

#### <a name="reschedule-a-test-instance-or-schedule-a-test"></a>Změna plánu instance testu nebo Naplánování testu

Plánování testů ze stránky pro správu závisí na typu pracovního postupu, pod kterým je test spuštěn.

##### <a name="test-pass-workflow"></a>Pracovní postup testovacího průchodu

V pracovním postupu test Pass **přeplánuje** instance testu znovu stejnou sadu parametrů jako původní testovací instance a *nahradí* původní výsledek včetně jeho protokolů. Při replánování budete muset znovu zadat citlivé řetězce, jako je třeba heslo.

1. V místní nabídce vyberte znovu **naplánovat** , aby se otevřela výzva k přeplánování instance testu.

1. Zadejte libovolné použitelné parametry.

1. Vyberte **Odeslat** pro opětovné naplánování instance testu a nahrazení existující instance.

##### <a name="validation-workflows"></a>Ověřovací pracovní postupy

[!INCLUDE [azure-stack-vaas-workflow-validation-section_schedule](includes/azure-stack-vaas-workflow-validation-section_schedule.md)]

#### <a name="cancel-a-test-instance"></a>Zrušení instance testu

Plánovaný test může být zrušen, pokud je jeho stav `Pending` nebo `Running`.  

1. V místní nabídce vyberte **Zrušit** a otevřete tak výzvu pro zrušení testovací instance.

1. Vyberte **Odeslat** pro zrušení testovací instance.

## <a name="next-steps"></a>Další kroky

- [Řešení potíží s ověřováním jako službou](azure-stack-vaas-troubleshoot.md)
