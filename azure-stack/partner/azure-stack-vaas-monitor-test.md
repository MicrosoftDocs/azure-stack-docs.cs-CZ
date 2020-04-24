---
title: Správa testů na portálu pro ověřování centra Azure Stack
titleSuffix: Azure Stack Hub
description: Naučte se spravovat testy na portálu pro ověřování centra Azure Stack.
author: mattbriggs
ms.topic: tutorial
ms.date: 04/20/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 68c3ec6dadd71c5e72ecf0c9efa3592f9c4f9ec7
ms.sourcegitcommit: 32834e69ef7a804c873fd1de4377d4fa3cc60fb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661230"
---
# <a name="monitor-and-manage-tests-in-the-vaas-portal"></a>Monitorování a Správa testů na portálu VaaS

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Po naplánování testů proti vašemu řešení centra Azure Stack začne služba ověřování jako služby (VaaS) stav spuštění testu vytváření sestav. Tyto informace jsou k dispozici na portálu pro ověřování centra Azure Stack společně s akcemi jako přeplánování a rušení testů.

## <a name="navigate-to-the-workflow-tests-summary-page"></a>Přejít na stránku souhrn testů pracovního postupu

1. Na řídicím panelu řešení vyberte existující řešení, které obsahuje alespoň jeden pracovní postup.

    ![Dlaždice pracovního postupu na řídicím panelu řešení](media/tile_all-workflows.png)

1. Na dlaždici pracovní postup vyberte **Spravovat** . Na další stránce jsou vypsány pracovní postupy vytvořené pro vybrané řešení.

1. Vyberte název pracovního postupu a otevřete jeho souhrn testů.

## <a name="change-workflow-parameters"></a>Změnit parametry pracovního postupu

Můžete upravit [parametry testu](azure-stack-vaas-parameters.md#test-parameters) zadané během vytváření pracovního postupu pro libovolný typ pracovního postupu.

1. Na stránce s přehledem testů vyberte **Upravit**.

1. Zadejte nové hodnoty podle [společných parametrů pracovního postupu pro Azure Stack ověřování centra jako služby](azure-stack-vaas-parameters.md).

1. Vyberte **Odeslat** a uložte hodnoty.

> [!NOTE]
> V pracovním postupu **test Pass** musíte dokončit výběr testu a přejít na stránku recenze, než budete moci uložit nové hodnoty parametrů.

### <a name="add-tests-test-pass-only"></a>Přidat testy (jenom testovací průchod)

V pracovních postupech **test Pass** umožňují obě tlačítka **Přidat testy** a **Úpravy** naplánovat nové testy v pracovním postupu.

> [!TIP]
> Vyberte možnost **Přidat testy** , pokud chcete naplánovat nové testy a nemusíte upravovat parametry pro pracovní postup **testovacího průchodu** .

## <a name="manage-test-instances"></a>Spravovat instance testů

V případě neoficiálních spuštění (pracovní postup **testovacího průchodu** ) se zobrazí stránka s přehledem testů naplánovaných na řešení Azure Stack hub.

V případě oficiálních spuštění ( **ověřovací** pracovní postupy) je na stránce Souhrn testů uveden seznam testů požadovaných k dokončení ověřování řešení Azure Stack hub. Ověřovací testy jsou plánovány na této stránce.

Každá plánovaná instance testu zobrazuje následující informace:

| Sloupec | Popis |
| --- | --- |
| Název testu | Název a verze testu. |
| Kategorie | Účel testu. |
| Vytvořeno | Čas, kdy byl test naplánován. |
| Bylo zahájeno | Čas spuštění testu. |
| Doba trvání | Doba, po kterou byl test spuštěn. |
| Status | Stav nebo výsledek testu. Předběžné spuštění nebo průběžné stavy jsou: `Pending`,. `Running` Stavy terminálů jsou: `Cancelled`, `Failed`, `Aborted`, `Succeeded`. |
| Název agenta | Název agenta, který test spustil. |
| Celkem operací | Celkový počet operací, které se během testu pokoušely. |
| Úspěšné operace | Počet operací, které byly během testu úspěšné. |
|  Neúspěšné operace | Počet operací, které během testu selhaly. |

### <a name="actions"></a>Akce

Každá instance testu obsahuje seznam dostupných akcí, které můžete provést při výběru místní nabídky **[...]** v tabulce instance testu.

#### <a name="view-information-about-the-test-definition"></a>Zobrazit informace o definici testu

Chcete-li zobrazit obecné informace o definici testu, vyberte možnost **Zobrazit informace** z kontextové nabídky. Tyto informace sdílí každá testovací instance se stejným názvem a verzí.

| Test – vlastnost | Popis |
| -- | -- |
| Název testu | Název testu. |
| Testovací verze | Verze testu. |
| Vydavatel | Vydavatel testu. |
| Kategorie |  Účel testu. |
| Cílové služby | Testované služby centra Azure Stack. |
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

Vyberte možnost **Stáhnout protokoly** z kontextové nabídky a Stáhněte si `.zip` soubor výstupů protokolů během provádění testu. Tato akce je k dispozici pouze pro testy, které byly dokončeny, což znamená, že `Cancelled`mají `Failed`stav `Aborted`buď, `Succeeded`, nebo.

#### <a name="reschedule-a-test-instance-or-schedule-a-test"></a>Změna plánu instance testu nebo Naplánování testu

Plánování testů ze stránky pro správu závisí na typu pracovního postupu, pod kterým je test spuštěn.

##### <a name="test-pass-workflow"></a>Pracovní postup testovacího průchodu

V pracovním postupu test Pass **přeplánuje** instance testu znovu stejnou sadu parametrů jako původní testovací instance a *nahradí* původní výsledek včetně jeho protokolů. Při replánování musíte zadat citlivé řetězce jako hesla.

1. V místní nabídce vyberte znovu **naplánovat** , aby se otevřela výzva k přeplánování instance testu.

1. Zadejte libovolné použitelné parametry.

1. Vyberte **Odeslat** pro opětovné naplánování instance testu a nahrazení existující instance.

##### <a name="validation-workflows"></a>Ověřovací pracovní postupy

[!INCLUDE [azure-stack-vaas-workflow-validation-section_schedule](includes/azure-stack-vaas-workflow-validation-section_schedule.md)]

#### <a name="cancel-a-test-instance"></a>Zrušení instance testu

Plánovaný test lze zrušit, pokud je `Pending` jeho stav nebo. `Running`  

1. V místní nabídce vyberte **Zrušit** a otevřete tak výzvu pro zrušení testovací instance.

1. Vyberte **Odeslat** pro zrušení testovací instance.

## <a name="next-steps"></a>Další kroky

- [Řešení potíží s ověřováním jako službou](azure-stack-vaas-troubleshoot.md)
