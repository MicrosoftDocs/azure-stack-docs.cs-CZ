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
ms.openlocfilehash: d5606e7904fe311a54d792a18e5d4029c709b33c
ms.sourcegitcommit: 0866555e0ed240a65595052899ef1b836dd07fbc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/10/2019
ms.locfileid: "72257753"
---
# <a name="troubleshooting-patch-and-update-issues-for-azure-stack"></a>Řešení potíží s opravou a aktualizacemi pro Azure Stack

*Platí pro: Azure Stack integrovaných systémů*

Pomocí pokynů v tomto článku můžete vyřešit problémy, které máte při aktualizaci Azure Stack.

## <a name="preparationfailed"></a>PreparationFailed

**Platí**: Tento problém se vztahuje na všechny podporované verze.

**Příčina**: při pokusu o instalaci aktualizace Azure Stack se může stát, že se stav aktualizace nezdaří a změní se stav na `PreparationFailed`. Pro systémy připojené k Internetu je to obvykle informativní jako aktualizace balíčku aktualizace, který se nedá správně stáhnout z důvodu slabého připojení k Internetu. 

**Náprava**: Tento problém můžete obejít tak, že znovu kliknete na **nainstalovat** . Pokud potíže potrvají, doporučujeme ručně odeslat balíček aktualizace pomocí oddílu [instalovat aktualizace](azure-stack-apply-updates.md?#install-updates-and-monitor-progress) .

**Výskyt**: běžné

## <a name="next-steps"></a>Další kroky

- [Aktualizovat Azure Stack](azure-stack-updates.md)  
- [Microsoft Azure Stack pomoc a podpora](azure-stack-help-and-support-overview.md)
