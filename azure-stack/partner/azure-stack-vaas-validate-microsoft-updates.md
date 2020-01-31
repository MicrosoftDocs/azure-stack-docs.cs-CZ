---
title: Ověřit aktualizace softwaru od Microsoftu v Azure Stack ověřování jako služba
description: Zjistěte, jak ověřit aktualizace softwaru od Microsoftu pomocí ověřování jako služby.
author: mattbriggs
ms.topic: tutorial
ms.date: 10/29/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 5e4438f0c3a95b2254422a428741a28b685cb9f5
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76884007"
---
# <a name="validate-software-updates-from-microsoft"></a>Ověřit aktualizace softwaru od Microsoftu

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Společnost Microsoft bude pravidelně vydávat aktualizace Azure Stack softwaru. Tyto aktualizace jsou k dispozici pro Azure Stack spolupracujícím partnerům. Aktualizace jsou poskytovány předem veřejně dostupným. Můžete kontrolovat aktualizace řešení a poskytnout Microsoftu zpětnou vazbu.

Aktualizace softwaru společnosti Microsoft pro Azure Stack jsou určeny pomocí konvence pojmenování, například 1803, což znamená, že je aktualizace určena pro 2018. března. Informace Azure Stack o tempo a poznámkách k verzi najdete v tématu [zásady služby Azure Stack Servicing](../operator/azure-stack-servicing-policy.md).

## <a name="prerequisites"></a>Požadavky

Před uplatněním měsíčního procesu aktualizace v VaaS byste měli být obeznámeni s následujícími položkami:

- [Koncepce ověřování jako klíč služby](azure-stack-vaas-key-concepts.md)

## <a name="required-tests"></a>Požadované testy

Následující testy musí být spuštěny v následujícím pořadí pro měsíční ověření softwaru:

- Pracovní postup ověření výrobce OEM

## <a name="validating-software-updates"></a>Ověřování aktualizací softwaru

1. Vytvoří nový pracovní postup **ověření balíčku** .
1. V případě požadovaných testů postupujte podle pokynů v tématu [spuštění testů pro ověření balíčku](azure-stack-vaas-validate-oem-package.md#run-package-validation-tests). Další informace o **měsíčním testu ověřování aktualizace Azure Stack** najdete v části níže.

Pokud máte otázky nebo obavy, obraťte se na [VaaS Help](mailto:vaashelp@microsoft.com).

## <a name="next-steps"></a>Další kroky

- [Monitorování a Správa testů na portálu VaaS](azure-stack-vaas-monitor-test.md)