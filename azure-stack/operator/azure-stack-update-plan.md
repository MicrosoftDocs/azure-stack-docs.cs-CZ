---
title: Plánování aktualizace služby Azure Stack | Microsoft Docs
description: Naučte se naplánovat aktualizaci Azure Stack.
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
ms.date: 09/10/2019
ms.author: mabrigg
ms.lastreviewed: 08/23/2019
ms.reviewer: ppacent
ms.openlocfilehash: 60515f257dc8ab04e351d1353919a749e9cf4224
ms.sourcegitcommit: 79ead51be63c372b23b7fca6ffeaf95fd44de786
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2019
ms.locfileid: "71687959"
---
# <a name="plan-for-an-azure-stack-update"></a>Plánování aktualizace Azure Stack

*Platí pro: Azure Stack integrovaných systémů*

Můžete připravit Azure Stack, aby se proces aktualizace co nejrychleji provedl, takže uživatelé mají minimální dopad na uživatele. V tomto článku si Projděte kroky pro upozorňování uživatelů na možné výpadky služby a kroky, které budete chtít provést při přípravě na aktualizaci.

## <a name="notify-your-users-of-maintenance-operations"></a>Upozorněte uživatele na operace údržby

Měli byste informovat uživatele o všech operacích údržby a v případě potřeby můžete naplánovat normální časová období údržby v nepracovních hodinách. Operace údržby mohou ovlivnit úlohy tenanta i operace portálu.

## <a name="prepare-for-an-azure-stack-update"></a>Příprava na aktualizaci Azure Stack

Můžete připravit na aktualizaci tím, že zadáte všechny opravy hotfix, opravy zabezpečení a aktualizace OEM, ověřili jste stav vaší Azure Stack instance, zkontrolovali dostupnou kapacitu a zkontrolovali balíček aktualizace.

1. Přečtěte si o známých problémech. Pokyny najdete v tématu [Azure Stack známých problémů](https://docs.microsoft.com/azure-stack/operator/release-notes).

2. Zkontrolujte aktualizace zabezpečení. Seznam aktualizací najdete v tématu [Azure Stack aktualizace zabezpečení](https://docs.microsoft.com/azure-stack/operator/release-notes-security-updates).

3. Před zahájením instalace této aktualizace spusťte rutinu [test-AzureStack](https://docs.microsoft.com/azure-stack/operator/azure-stack-diagnostic-test) a ověřte stav vašich Azure Stack a vyřešte všechny zjištěné provozní problémy, včetně všech upozornění a selhání. Taky si projděte aktivní výstrahy a vyřešte ty, které vyžadují zásah.

    1. Otevřete privilegovanou relaci koncového bodu z počítače se síťovým přístupem k virtuálním počítačům s Azure Stack ERCS. Pokyny k použití PEP najdete v tématu [použití privilegovaného koncového bodu v Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint).

    2. Test-AzureStack-Group UpdateReadiness

    3. Zkontrolujte výstup a vyřešte všechny chyby stavu. Další informace najdete v tématu [spuštění ověřovacího testu Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-diagnostic-test).

4. Vyřešte problémy. Pokyny k získání dalších informací o problému s příznakem najdete v tématu monitorování stavu a výstrah. Pokyny najdete v tématu [monitorování stavu a výstrah v Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-monitor-health).

5. Použijte nejnovější opravy hotfix. Seznam nejnovějších oprav hotfix najdete v části Oprava hotfix poznámky k verzi. Po použití nejnovější opravy hotfix zopakujte kroky 3 a 4.

6. Ujistěte se, že je balíček OEM kompatibilní s verzí Azure Stack, na kterou aktualizujete. Pokud balíček OEM není kompatibilní s verzí Azure Stack, na kterou aktualizujete, budete muset před spuštěním aktualizace Azure Stack provést aktualizaci balíčku OEM. Pokyny najdete v části "použití Azure Stack aktualizací OEM (Original Equipment Manufacturer)". Zopakujte kroky 3 a 4 po instalaci aktualizace balíčku OEM.

7. Spusťte nástroj Capacity Planner. Přehled a pokyny k používání tohoto nástroje najdete v tématu [přehled Azure Stackho plánování kapacity](https://docs.microsoft.com/azure-stack/operator/azure-stack-capacity-planning-overview).

8. Projděte si balíček aktualizace. Při plánování časového intervalu pro správu a údržbu je důležité zkontrolovat konkrétní typ balíčku aktualizace vydaný od Microsoftu, který se vyvolal v poznámkách k verzi.

## <a name="next-steps"></a>Další kroky

[Použijte aktualizaci Azure Stack](azure-stack-apply-updates.md).
