---
title: Vývoj aplikací pro Azure Stack | Dokumentace Microsoftu
description: Aspekty vývoje pro vytváření prototypů aplikací ve službě Azure Stack
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
ms.date: 05/21/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: 4273326001dc79abb7ea522c3bd4c01e46bceb2b
ms.sourcegitcommit: 6fcd5df8b77e782ef72f0e1419f1f75ec8c16c04
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2019
ms.locfileid: "65991276"
---
# <a name="develop-for-azure-stack"></a>Vývoj pro Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Můžete začít vyvíjet aplikace už dnes, i když nemáte přístup k prostředí Azure Stack. Protože Azure Stack přináší služby Microsoft Azure, které běží ve vašem datovém centru, můžete použít podobné procesy a nástroje pro vývoj v Azure stacku, stejně jako s Azure.

## <a name="development-considerations"></a>Aspekty vývoje

Některé přípravy a pomocí pokynů v následujících tématech můžete použít Azure k emulaci prostředí Azure Stack.

* V Azure můžete vytvořit šablony Azure Resource Manageru, které je možné nasadit do služby Azure Stack. Zobrazit [aspekty šablon](azure-stack-develop-templates.md) pokyny k vývoji šablon pro zajištění přenositelnosti.
* Existují rozdíly v dostupnosti služeb a správa verzí služby mezi Azure a Azure Stack. Můžete použít [modul zásad služby Azure Stack](azure-stack-policy-module.md) omezit typy dostupnosti a prostředků služby Azure na to, co je k dispozici ve službě Azure Stack. Omezení služeb zajišťuje, že vaše aplikace využívají služby Azure Stack k dispozici.
* [Šablon rychlý start Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates) jsou běžné příklady scénáře, které ukazují, jak vyvíjet šablony, které je možné nasadit do Azure a Azure Stack.

## <a name="next-steps"></a>Další postup

Další informace o vývoji pro Azure Stack najdete v následujících článcích:

* [Azure Resource Manageru šablony osvědčené postupy](azure-stack-develop-templates.md)
* [Šablony pro rychlý start Azure Stack na Githubu](https://github.com/Azure/AzureStack-QuickStart-Templates)