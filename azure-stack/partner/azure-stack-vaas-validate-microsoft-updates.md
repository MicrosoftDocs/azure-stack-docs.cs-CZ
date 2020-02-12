---
title: Ověřit aktualizace softwaru od Microsoftu v Azure Stack ověřování centra jako služba
description: Zjistěte, jak ověřit aktualizace softwaru od Microsoftu pomocí ověřování jako služby.
author: mattbriggs
ms.topic: tutorial
ms.date: 10/29/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 8e09160245551ee83f631360931c8e70bac4318e
ms.sourcegitcommit: a76301a8bb54c7f00b8981ec3b8ff0182dc606d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2020
ms.locfileid: "77143905"
---
# <a name="validate-software-updates-from-microsoft"></a>Ověřit aktualizace softwaru od Microsoftu

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Společnost Microsoft bude pravidelně vydávat aktualizace softwaru centra Azure Stack. Tyto aktualizace jsou k dispozici pro Azure Stack spolupracujícím partnerům centra. Aktualizace jsou poskytovány předem veřejně dostupným. Můžete kontrolovat aktualizace řešení a poskytnout Microsoftu zpětnou vazbu.

Aktualizace softwaru od Microsoftu do centra Azure Stack se označují pomocí konvence pojmenování, například 1803 označující aktualizaci aktualizace pro březen 2018. Informace o dostupných zásadách aktualizace centra Azure Stack tempo a poznámkách k verzi najdete v tématu [zásady obsluhy centra Azure Stack](../operator/azure-stack-servicing-policy.md).

## <a name="prerequisites"></a>Předpoklady

Před uplatněním měsíčního procesu aktualizace v VaaS byste měli být obeznámeni s následujícími položkami:

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