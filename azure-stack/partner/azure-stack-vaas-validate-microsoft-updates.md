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
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 0a507b7488a34715e528b6bbf291fec9832ef027
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68418282"
---
# <a name="validate-software-updates-from-microsoft"></a>Ověřit aktualizace softwaru od Microsoftu

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Společnost Microsoft bude pravidelně vydávat aktualizace Azure Stack softwaru. Tyto aktualizace jsou k dispozici Azure Stack spolutechnickým partnerům. Aktualizace jsou poskytovány předem veřejně dostupným. Můžete kontrolovat aktualizace řešení a poskytnout Microsoftu zpětnou vazbu.

Aktualizace softwaru společnosti Microsoft pro Azure Stack jsou určeny pomocí konvence pojmenování, například 1803, což znamená, že je aktualizace určena pro 2018. března. Informace Azure Stack o tempo a poznámkách k verzi najdete v tématu Zásady služby Azure Stack Servicing.

## <a name="prerequisites"></a>Požadavky

Před uplatněním měsíčního procesu aktualizace v VaaS byste měli být obeznámeni s následujícími položkami:

- [Koncepce ověřování jako klíč služby](azure-stack-vaas-key-concepts.md)
- [Testování ověřování interaktivních funkcí](azure-stack-vaas-interactive-feature-verification.md)

## <a name="required-tests"></a>Požadované testy

Následující testy by se měly provádět v rámci měsíčního ověření softwaru v následujícím pořadí:

1. Ověřování aktualizací Azure Stack měsíčně
2. Modul pro simulaci cloudu

## <a name="validating-software-updates"></a>Ověřování aktualizací softwaru

1. Vytvoří nový pracovní postup **ověření balíčku** .
1. V případě požadovaných testů postupujte podle pokynů v tématu [spuštění testů pro ověření balíčku](azure-stack-vaas-validate-oem-package.md#run-package-validation-tests). Další informace o měsíčním testu **ověřování aktualizace Azure Stack** najdete v části níže.

### <a name="apply-the-monthly-update"></a>Použít měsíční aktualizaci

1. Vyberte agenta, u kterého mají být provedeny testy.
1. Naplánovat **měsíční Azure Stack ověřování aktualizace**.
1. Zadejte umístění balíčku rozšíření OEM aktuálně nasazeného na razítku a umístění balíčku rozšíření OEM, který bude použit během aktualizace. Pokud chcete nakonfigurovat adresy URL pro tyto balíčky, přečtěte si téma [Správa balíčků pro ověřování](azure-stack-vaas-validate-oem-package.md#managing-packages-for-validation).
1. Postupujte podle kroků v uživatelském rozhraní vybraného agenta.

Pokud máte otázky nebo obavy, obraťte se na [VaaS Help](mailto:vaashelp@microsoft.com).

## <a name="next-steps"></a>Další postup

- [Monitorování a Správa testů na portálu VaaS](azure-stack-vaas-monitor-test.md)