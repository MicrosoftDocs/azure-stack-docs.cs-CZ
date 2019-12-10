---
title: Ověřit aktualizace softwaru od Microsoftu v Azure Stack ověřování jako služba | Microsoft Docs
description: Zjistěte, jak ověřit aktualizace softwaru od Microsoftu pomocí ověřování jako služby.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/29/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 3399288b801114f076afe3b68d3f9790bfe2af29
ms.sourcegitcommit: 08d2938006b743b76fba42778db79202d7c3e1c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2019
ms.locfileid: "74954481"
---
# <a name="validate-software-updates-from-microsoft"></a>Ověřit aktualizace softwaru od Microsoftu

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Společnost Microsoft bude pravidelně vydávat aktualizace Azure Stack softwaru. Tyto aktualizace jsou k dispozici pro Azure Stack spolupracujícím partnerům. Aktualizace jsou poskytovány předem veřejně dostupným. Můžete kontrolovat aktualizace řešení a poskytnout Microsoftu zpětnou vazbu.

Aktualizace softwaru společnosti Microsoft pro Azure Stack jsou určeny pomocí konvence pojmenování, například 1803, což znamená, že je aktualizace určena pro 2018. března. Informace Azure Stack o tempo a poznámkách k verzi najdete v tématu [zásady služby Azure Stack Servicing](../operator/azure-stack-servicing-policy.md).

## <a name="prerequisites"></a>Předpoklady

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