---
title: Ověření aktualizace softwaru od Microsoftu v Azure stacku ověření jako služba | Dokumentace Microsoftu
description: Zjistěte, jak ověřit aktualizace softwaru od Microsoftu s ověřováním jako služba.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 67d17ffc7c2b8fcdb766ccf8a8248da8665a42d8
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "64297565"
---
# <a name="validate-software-updates-from-microsoft"></a>Ověření aktualizace softwaru od Microsoftu

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Microsoft pravidelně vydá aktualizace softwaru Azure Stack. Tyto aktualizace jsou k dispozici ke službě Azure Stack coengineering partnerů. Aktualizace jsou součástí zálohy veřejně k dispozici. Můžete zkontrolovat aktualizace pro vaše řešení a zasílat připomínky do Microsoftu.

Aktualizace softwaru společnosti Microsoft do služby Azure Stack jsou určeny pomocí zásady vytváření názvů, například 1803 označující aktualizace je pro. března 2018. Informace o aktualizaci zásad služby Azure Stack, tempo a verze poznámky jsou k dispozici, najdete v článku [Azure Stack zásady obsluhy](../operator/azure-stack-servicing-policy.md).

## <a name="prerequisites"></a>Požadavky

Než proces měsíční aktualizace v VaaS, měli byste znát následující položky:

- [Ověření jako klíčové koncepty služby](azure-stack-vaas-key-concepts.md)
- [Ověřovací testování interaktivní funkce](azure-stack-vaas-interactive-feature-verification.md)

## <a name="required-tests"></a>Požadované testy

Následující testy by měl provádět v uvedeném pořadí pro měsíční ověření softwaru:

1. Měsíční aktualizace ověřovací služby Azure Stack
2. Simulace modulu cloudu

## <a name="validating-software-updates"></a>Ověření aktualizace softwaru

1. Vytvořte nový **ověřování balíčku** pracovního postupu.
1. Pro požadované testy výše, postupujte podle pokynů v [testy spustit ověření balíčku](azure-stack-vaas-validate-oem-package.md#run-package-validation-tests). Naleznete v části níže další pokyny **měsíční ověření aktualizace Azure Stack** testování.

### <a name="apply-the-monthly-update"></a>Měsíční aktualizace

1. Vyberte agenta ke spuštění testů proti.
1. Plán **měsíční aktualizace ověřovací služby Azure Stack**.
1. Zadejte umístění pro balíček rozšíření pro výrobce OEM aktuálně nasazené na toto razítko a umístění pro rozšíření balíčku výrobce OEM, která se použije při aktualizaci. Ke konfiguraci adres URL pro tyto balíčky, naleznete v tématu [Správa balíčků pro ověření](azure-stack-vaas-validate-oem-package.md#managing-packages-for-validation).
1. Postupujte podle kroků v uživatelském rozhraní z vybraných agenta.

Pokud máte dotazy nebo připomínky, obraťte se na [VaaS nápovědy](mailto:vaashelp@microsoft.com).

## <a name="next-steps"></a>Další postup

- [Monitorování a správa testů na portálu VaaS](azure-stack-vaas-monitor-test.md)