---
title: Pokročilé úlohy hodnocení služby Azure Stack | Dokumentace Microsoftu
description: Tento článek popisuje pokročilé úlohy hodnocení služby Azure Stack.
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 02/12/2019
ms.date: 04/29/2019
ms.author: v-jay
ms.reviewer: misainat
ms.lastreviewed: 10/16/2018
ms.openlocfilehash: 04a1dbb28c55fff15d1087bc85ca65e5dcc7b8c0
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "64290316"
---
# <a name="advanced-azure-stack-development-kit-evaluation-tasks"></a>Pokročilé úlohy hodnocení služby Azure Stack Development Kit
Po získané znalosti s možnostmi a základními funkcemi služby Azure Stack Development Kit (ASDK) můžete lépe pochopíte, další služby Azure Stack při testování pokročilejší scénáře. Tyto pokročilé úlohy hodnocení jsou plně popsány v dokumentaci k Azure Stack – operátor.

> [!NOTE]
> I když celou řadu úloh operátor jsou podporované pro ASDK i produkční nasazení Azure Stack na víc uzlů, ne všechny scénáře použití jsou podporovány pro nasazení ASDK. Zobrazit [ASDK a více uzly služby Azure Stack rozdíly](asdk-what-is.md#asdk-and-multi-node-azure-stack-differences) Další informace.

## <a name="delegate-offers-in-azure-stack"></a>Delegování nabídek v Azure Stacku
Jako operátor Azure stacku často chcete umístit jiní lidé starosti vytvoření nabídky a přihlašování uživatelů. Například pokud jste poskytovatel služeb, může být vhodné prodejci v systému k podepisování zákazníci se těmito věcmi a spravovat za vás. Nebo pokud jste součástí centrální skupina IT v organizaci, může být vhodné dceřiné společnosti k registraci uživatele bez vašeho zásahu.

[Delegování nabídek v Azure stacku](../operator/azure-stack-delegated-provider.md) vám pomůže s těmito úkoly tak, že je to možné k dosažení a správě více uživatelů, než je možné přímo.

## <a name="make-sql-databases-available-to-your-azure-stack-users"></a>Zpřístupnění databáze SQL pro vaše uživatele Azure stacku
Jako operátor Azure stacku, můžete vytvořit nabídek, které uživatelům (tenantů) vytvářet databáze SQL, které můžete použít s jejich nativně cloudové aplikace, weby a úlohy. Tím, že poskytuje tyto databáze vlastní, na vyžádání, založené na cloudu pro vaše uživatele, můžete je šetřit čas i prostředky.

Použití adaptéru poskytovatele prostředků SQL serveru do [zpřístupnit databáze SQL pro vaše uživatele Azure stacku](../operator/azure-stack-tutorial-sql-server.md) jako službu Azure Stack. Po instalaci poskytovatele prostředků připojení k jedné nebo více instancí systému SQL Server.

## <a name="make-web-and-api-apps-available-to-your-azure-stack-users"></a>Zpřístupnění webové aplikace a aplikace API pro vaše uživatele Azure stacku
Jako operátor Azure stacku, můžete vytvořit nabídek, které uživatelé mohli vytvořit Azure Functions a rozhraní API a webové aplikace (klienty). Tím, že poskytuje přístup k těmto aplikacím na vyžádání, založené na cloudu pro vaše uživatele, můžete je šetřit čas i prostředky.

Nasazení poskytovatele prostředků App Service k [zpřístupnit webových aplikací a API aplikací uživatelům Azure stacku](../operator/azure-stack-tutorial-app-service.md)

## <a name="next-steps"></a>Další postup

[Další informace o nabízení služeb pomocí integrované systémy Azure Stack](../operator/azure-stack-offer-services-overview.md)
