---
title: Vývoj aplikací pro Azure Stack | Microsoft Docs
description: Požadavky na vývoj pro vytváření prototypů aplikací v Azure Stack pomocí služeb Azure.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: d3ebc6b1-0ffe-4d3e-ba4a-388239d6cdc3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: 0f1450ff7ce6d7e4cffee3d31f80b046f07f9ac1
ms.sourcegitcommit: bbf3edbfc07603d2c23de44240933c07976ea550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2019
ms.locfileid: "71714774"
---
# <a name="develop-for-azure-stack"></a>Vývoj pro Azure Stack

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Můžete začít vyvíjet aplikace ještě dnes i v případě, že nemáte přístup k Azure Stack prostředí. Azure Stack zajišťuje Microsoft Azure služby, které běží ve vašem datovém centru, což znamená, že můžete používat stejné nástroje a procesy Azure pro vývoj na Azure Stack.

## <a name="development-considerations"></a>Aspekty vývoje

S určitou přípravou a používáním pokynů v následujících tématech můžete k emulaci Azure Stackho prostředí použít Azure.

* V Azure můžete vytvářet Azure Resource Manager šablony, které lze nasadit Azure Stack. Pokyny k vývoji šablon pro zajištění přenositelnosti najdete v tématu věnovaném [hlediskům šablon](azure-stack-develop-templates.md) .
* Existují rozdíly v dostupnosti služeb a verzích služeb mezi Azure a Azure Stack. [Modul zásad Azure Stack](azure-stack-policy-module.md) slouží k omezení dostupnosti služeb Azure a typů prostředků na to, co je k dispozici v Azure Stack. Omezení služeb zajišťuje, aby vaše aplikace spoléhaly na služby dostupné pro Azure Stack.
* [Šablony pro rychlý start Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates) jsou běžné příklady scénářů, které ukazují, jak vyvíjet Šablony nasazené pro Azure i Azure Stack.

## <a name="next-steps"></a>Další kroky

Další informace o vývoji Azure Stack najdete v následujících článcích:

* [Osvědčené postupy pro šablony Azure Resource Manageru](azure-stack-develop-templates.md)
* [Azure Stack šablon pro rychlý Start na GitHubu](https://github.com/Azure/AzureStack-QuickStart-Templates)
