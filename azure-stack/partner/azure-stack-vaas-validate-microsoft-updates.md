---
title: Ověřit aktualizace softwaru od Microsoftu
titleSuffix: Azure Stack Hub
description: Zjistěte, jak ověřit aktualizace softwaru od Microsoftu pomocí ověřování centra Azure Stack jako služby.
author: mattbriggs
ms.topic: tutorial
ms.date: 04/20/2020
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 46618ee2ab650842ebab9fc90b18b24131ac5d07
ms.sourcegitcommit: 32834e69ef7a804c873fd1de4377d4fa3cc60fb6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661132"
---
# <a name="validate-software-updates-from-microsoft"></a>Ověřit aktualizace softwaru od Microsoftu

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Společnost Microsoft bude pravidelně vydávat aktualizace softwaru centra Azure Stack. Tyto aktualizace jsou k dispozici pro Azure Stack spolupracujícím partnerům centra. Aktualizace jsou k dispozici před veřejně dostupnými aktualizacemi. Můžete kontrolovat aktualizace řešení a poskytnout Microsoftu zpětnou vazbu.

Aktualizace softwaru od Microsoftu do centra Azure Stack se označují pomocí zásad vytváření názvů. Například název 1803 označuje aktualizaci na březen 2018. Informace o zásadách údržby centra Azure Stack a poznámkách k verzi najdete v tématu [zásady obsluhy centra Azure Stack](../operator/azure-stack-servicing-policy.md).

## <a name="prerequisites"></a>Požadavky

Před uplatněním měsíčního procesu aktualizace v rámci ověřování jako služby (VaaS) byste měli být obeznámeni s následujícími položkami:

- [Koncepce ověřování jako klíč služby](azure-stack-vaas-key-concepts.md)

## <a name="required-tests"></a>Požadované testy

Následující testy musí být spuštěny v následujícím pořadí pro měsíční ověření softwaru:

- Pracovní postup ověření výrobce OEM

## <a name="validating-software-updates"></a>Ověřování aktualizací softwaru

1. Vytvoří nový pracovní postup **ověření balíčku** .
1. V případě požadovaných testů postupujte podle pokynů v tématu [spuštění testů pro ověření balíčku](azure-stack-vaas-validate-oem-package.md#run-package-validation-tests). Další informace o **měsíčním testu ověřování aktualizace centra Azure Stack** najdete v části níže.

Pokud máte otázky nebo obavy, obraťte se na [VaaS Help](mailto:vaashelp@microsoft.com).

## <a name="next-steps"></a>Další kroky

- [Monitorování a Správa testů na portálu VaaS](azure-stack-vaas-monitor-test.md)