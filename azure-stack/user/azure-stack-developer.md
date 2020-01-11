---
title: Vývoj aplikací pro centrum Azure Stack | Microsoft Docs
description: Požadavky na vývoj pro vytváření prototypů aplikací v centru Azure Stack s využitím služeb Azure.
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
ms.openlocfilehash: ba5aefa61db489f5f7063ebc4785785ba2f26f4c
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75883724"
---
# <a name="develop-for-azure-stack-hub"></a>Vývoj pro centrum Azure Stack

Můžete začít vyvíjet aplikace ještě dnes i v případě, že nemáte přístup k prostředí Azure Stack hub. Azure Stack centrum zajišťuje Microsoft Azure služby, které běží ve vašem datovém centru, což znamená, že můžete použít stejné nástroje a procesy Azure pro vývoj v Azure Stack hub.

## <a name="development-considerations"></a>Aspekty vývoje

S určitou přípravou a používáním pokynů v následujících tématech můžete pomocí Azure emulovat prostředí Azure Stack hub.

* V Azure můžete vytvářet Azure Resource Manager šablony, které lze nasadit do centra Azure Stack. Pokyny k vývoji šablon pro zajištění přenositelnosti najdete v tématu věnovaném [hlediskům šablon](azure-stack-develop-templates.md) .
* Existují rozdíly v dostupnosti služeb a verzích služeb mezi Azure a centra Azure Stack. [Modul zásad centra Azure Stack](azure-stack-policy-module.md) slouží k omezení dostupnosti služeb Azure a typů prostředků na to, co je k dispozici v centru Azure Stack. Omezení služeb zajišťuje, aby vaše aplikace spoléhaly na služby dostupné pro Azure Stack hub.
* [Šablony pro rychlý Start centra Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates) jsou běžné příklady scénářů, které ukazují, jak vyvíjet šablony nasadit do centra Azure i Azure Stack.

## <a name="next-steps"></a>Další kroky

Další informace o vývoji Azure Stack najdete v následujících článcích:

* [Osvědčené postupy pro šablony Azure Resource Manageru](azure-stack-develop-templates.md)
* [Šablony rychlý Start centra Azure Stack na GitHubu](https://github.com/Azure/AzureStack-QuickStart-Templates)
