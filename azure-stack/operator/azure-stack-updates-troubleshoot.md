---
title: Řešení potíží s aktualizacemi v centru Azure Stack
description: Jako operátor centra Azure Stack se naučíte řešit problémy s aktualizací, aby se Azure Stack centrum mohl co nejrychleji vrátit do produkčního prostředí.
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 03/04/2020
ms.author: inhenkel
ms.lastreviewed: 09/23/2019
ms.reviewer: ppacent
ms.openlocfilehash: 6cf5d83b9fdf71ba68506c760112635910d26eb3
ms.sourcegitcommit: 53efd12bf453378b6a4224949b60d6e90003063b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2020
ms.locfileid: "79512311"
---
# <a name="best-practices-for-troubleshooting-azure-stack-hub-patch-and-update-issues"></a>Osvědčené postupy pro řešení potíží s problémy s aktualizacemi a aktualizacemi centra Azure Stack

Tento článek poskytuje přehled osvědčených postupů pro řešení potíží s aktualizacemi centra Azure Stack a problémy s aktualizací, jakož i opravy běžných problémů s aktualizacemi a aktualizacemi.


Proces aktualizace a aktualizace centra Azure Stack slouží k tomu, aby operátoři mohli instalovat balíčky aktualizací konzistentním a efektivnějším způsobem. V neobvyklém případě mohou nastat problémy během procesu aktualizace a aktualizace. V následujících krocích se doporučuje, abyste při procesu aktualizace a aktualizace nastavili problém:

0. **Požadavky**: Ujistěte se, že jste postupovali podle [kontrolního seznamu aktivity aktualizace](release-notes-checklist.md) a máte [nakonfigurované automatické shromažďování protokolů](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md).
1. Postupujte podle kroků pro nápravu v upozornění na selhání vytvořeného při selhání aktualizace.
2. Přečtěte si [Nejčastější aktualizace centra Azure Stack a aktualizujte problémy](#common-azure-stack-hub-patch-and-update-issues) a podle uvedeného problému proveďte doporučené akce.
3. Pokud jste nedokázali problém vyřešit pomocí výše uvedeného postupu, vytvořte [lístek podpory centra Azure Stack](azure-stack-help-and-support-overview-tzl.md). Ujistěte se, že máte [shromážděné protokoly](https://docs.microsoft.com/azure-stack/operator/azure-stack-configure-on-demand-diagnostic-log-collection) pro časový interval, ke kterému došlo k problému.

## <a name="common-azure-stack-hub-patch-and-update-issues"></a>Běžné problémy s opravou a aktualizacemi centra Azure Stack

*Platí pro: Azure Stack integrovaných systémů centra*

### <a name="preparationfailed"></a>PreparationFailed

**Platí**: Tento problém se vztahuje na všechny podporované verze.

**Příčina**: při pokusu o instalaci aktualizace centra Azure Stack se může stát, že se stav aktualizace nezdaří a změní se stav na `PreparationFailed`. Pro systémy připojené k Internetu je to obvykle informativní jako aktualizace balíčku aktualizace, který se nedá správně stáhnout z důvodu slabého připojení k Internetu. 

**Náprava**: Tento problém můžete obejít tak, že znovu kliknete na **nainstalovat** . Pokud potíže potrvají, doporučujeme ručně odeslat balíček aktualizace pomocí oddílu [instalovat aktualizace](azure-stack-apply-updates.md?#install-updates-and-monitor-progress) .

**Výskyt**: běžné

## <a name="next-steps"></a>Další kroky

- [Aktualizace centra Azure Stack](azure-stack-updates.md)  
- [Pomoc a podpora centra Microsoft Azure Stack](azure-stack-help-and-support-overview-tzl.md)
