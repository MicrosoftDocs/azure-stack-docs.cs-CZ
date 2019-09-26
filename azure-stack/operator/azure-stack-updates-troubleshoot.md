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
ms.openlocfilehash: 76c6c21c2a728004d2a33c04a02b905ec674b972
ms.sourcegitcommit: d967cf8cae320fa09f1e97eeb888e3db5b6e7972
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/26/2019
ms.locfileid: "71301223"
---
# <a name="troubleshooting-patch-and-update-issues-for-azure-stack"></a>Řešení potíží s opravou a aktualizacemi pro Azure Stack

*Platí pro: Azure Stack integrovaných systémů*

Pomocí pokynů v tomto článku můžete vyřešit problémy, které máte při aktualizaci Azure Stack.

## <a name="preparationfailed"></a>PreparationFailed

**Platí**: Tento problém se vztahuje na všechny podporované verze.

**Příčina:** Při pokusu o instalaci aktualizace Azure Stack se může stát, že se stav aktualizace nezdaří a změní se stav `PreparationFailed`na. To je způsobeno tím, že poskytovatel prostředků aktualizace (URP) nemůže správně přenést soubory z kontejneru úložiště do interní sdílené složky infrastruktury pro zpracování.

**Náprava**: Počínaje verzí 1901 (1.1901.0.95) můžete tento problém obejít tak, že znovu kliknete na **aktualizovat** ( **nepokračovat**). URP pak vyčistí soubory z předchozího pokusu a restartuje soubor ke stažení. Pokud potíže potrvají, doporučujeme ručně odeslat balíček aktualizace pomocí oddílu [instalovat aktualizace](azure-stack-apply-updates.md?#install-updates-and-monitor-progress) .

**Výskyt**: Společné

## <a name="next-steps"></a>Další kroky

- [Aktualizovat Azure Stack](azure-stack-updates.md)  
- [Microsoft Azure Stack pomoc a podpora](azure-stack-help-and-support-overview.md)
