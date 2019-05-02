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
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: 2f0d311851abe1c0fb01ec08dc82626805b35eb1
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "64298588"
---
# <a name="develop-for-azure-stack"></a>Vývoj pro Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Můžete začít vyvíjet aplikace už dnes, i když nemáte přístup k prostředí Azure Stack. Protože Azure Stack přináší služby Microsoft Azure, které běží ve vašem datovém centru, můžete použít podobné procesy a nástroje pro vývoj pro Azure Stack, stejně jako s Azure.

## <a name="development-considerations"></a>Aspekty vývoje

Některé přípravy a pomocí pokynů v následujících tématech můžete použít Azure k emulaci prostředí Azure Stack.

* V Azure můžete vytvořit šablony Azure Resource Manageru, které je možné nasadit do služby Azure Stack. Zobrazit [aspekty šablon](azure-stack-develop-templates.md) pokyny k vývoji šablon pro zajištění přenositelnosti.
* Existují rozdíly v dostupnosti služeb a správa verzí služby mezi Azure a Azure Stack. Můžete použít [modul zásad služby Azure Stack](azure-stack-policy-module.md) omezit typy dostupnosti a prostředků služby Azure na to, co je k dispozici ve službě Azure Stack. Omezení služeb zajišťuje, že vaše aplikace využívají služby Azure Stack k dispozici.
* [Šablon rychlý start Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates) jsou běžné příklady scénáře, které ukazují, jak vyvíjet šablony, které je možné nasadit do Azure a Azure Stack.

## <a name="next-steps"></a>Další postup

Další informace o vývoji pro Azure Stack najdete v následujících článcích:

* [Azure Resource Manageru šablony osvědčené postupy](azure-stack-develop-templates.md)
* [Šablony pro rychlý start Azure Stack na Githubu](https://github.com/Azure/AzureStack-QuickStart-Templates)