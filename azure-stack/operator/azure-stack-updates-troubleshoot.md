---
title: Řešení potíží s aktualizacemi v Azure Stack | Microsoft Docs
description: Jako operátor Azure Stack se naučíte řešit problémy s aktualizací, aby se Azure Stack mohli co nejrychleji vrátit do produkčního prostředí.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/23/2019
ms.author: mabrigg
ms.lastreviewed: 09/23/2019
ms.reviewer: ppacent
ms.openlocfilehash: 0021776f5b3be93a82305f41a9aa70c524273e4b
ms.sourcegitcommit: e57a53caac50d1f8762307e065fe886fcda7eadf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2019
ms.locfileid: "75190879"
---
# <a name="best-practices-for-troubleshooting-azure-stack-hub-patch-and-update-issues"></a>Osvědčené postupy pro řešení potíží s problémy s aktualizacemi a aktualizacemi centra Azure Stack

Tento článek poskytuje přehled osvědčených postupů pro řešení potíží s Azure Stack opravou a aktualizací, jakož i opravy běžných problémů s opravou a aktualizací.


Proces aktualizace a aktualizace centra Azure Stack slouží k tomu, aby operátoři mohli instalovat balíčky aktualizací konzistentním a efektivnějším způsobem. V neobvyklém případě mohou nastat problémy při opravách a aktualizacích. V následujících krocích se doporučuje, abyste při procesu aktualizace a aktualizace nastavili problém:

0. **Požadavky**: Ujistěte se, že jste postupovali podle [kontrolního seznamu aktivity aktualizace](release-notes-checklist.md) a máte [nakonfigurované automatické shromažďování protokolů](azure-stack-configure-automatic-diagnostic-log-collection.md).
1. Postupujte podle kroků pro nápravu v upozornění na selhání vytvořeného při selhání aktualizace.
2. Přečtěte si [Nejčastější aktualizace centra Azure Stack a aktualizujte problémy](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates-troubleshoot#Common-Azure-Stack-Hub-patch-and-update-issues) a podle uvedeného problému proveďte doporučené akce.
3. Pokud jste nedokázali vyřešit váš problém pomocí výše uvedeného postupu, vytvořte [lístek podpory Azure Stack](azure-stack-help-and-support-overview.md). Ujistěte se, že máte [shromážděné protokoly](https://docs.microsoft.com/azure-stack/operator/azure-stack-configure-on-demand-diagnostic-log-collection) pro časový interval, ke kterému došlo k problému.

## <a name="common-azure-stack-hub-patch-and-update-issues"></a>Běžné problémy s opravou a aktualizacemi centra Azure Stack

*Platí pro: Azure Stack integrovaných systémů centra*

### <a name="preparationfailed"></a>PreparationFailed

**Platí**: Tento problém se vztahuje na všechny podporované verze.

**Příčina**: při pokusu o instalaci aktualizace Azure Stack se může stát, že se stav aktualizace nezdaří a změní se stav na `PreparationFailed`. Pro systémy připojené k Internetu je to obvykle informativní jako aktualizace balíčku aktualizace, který se nedá správně stáhnout z důvodu slabého připojení k Internetu. 

**Náprava**: Tento problém můžete obejít tak, že znovu kliknete na **nainstalovat** . Pokud potíže potrvají, doporučujeme ručně odeslat balíček aktualizace pomocí oddílu [instalovat aktualizace](azure-stack-apply-updates.md?#install-updates-and-monitor-progress) .

**Výskyt**: běžné

## <a name="next-steps"></a>Další kroky

- [Aktualizovat Azure Stack](azure-stack-updates.md)  
- [Microsoft Azure Stack pomoc a podpora](azure-stack-help-and-support-overview.md)
