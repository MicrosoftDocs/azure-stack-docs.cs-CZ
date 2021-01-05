---
title: Pokročilé úlohy hodnocení ASDK
description: Přečtěte si o pokročilých úkolech vyhodnocování Azure Stack Development KIt (ASDK).
author: PatAltimore
ms.topic: article
ms.date: 02/12/2019
ms.author: patricka
ms.reviewer: misainat
ms.lastreviewed: 10/16/2019
ms.openlocfilehash: 912fb68695d281ee83273ff49f2f2aa24e468247
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97873602"
---
# <a name="advanced-asdk-evaluation-tasks"></a>Pokročilé úlohy hodnocení ASDK
Po zjištění zkušeností s funkcemi a možnostmi služby Basic Azure Stack Development Kit (ASDK) můžete své porozumění Azure Stack dále prohloubit otestováním pokročilejších scénářů. Tyto pokročilejší úkoly hodnocení jsou plně dokumentovány v dokumentaci k operátoru Azure Stack.

> [!NOTE]
> I když se pro nasazení ASDK i produkčních i Azure Stack pro více uzlů podporuje mnoho úloh operátorů, není pro nasazení ASDK podporována podpora všech scénářů použití. Další informace najdete v tématu [ASDK a multi-node – rozdíly v Azure Stack](asdk-what-is.md#asdk-and-multi-node-azure-stack-hub-differences).

## <a name="delegate-offers-in-azure-stack"></a>Delegování nabídek v Azure Stacku
Jako operátor Azure Stack často chcete dát ostatním lidem na starosti vytváření nabídek a registraci uživatelů. Pokud jste například poskytovatelem služeb, můžete chtít, aby prodejci mohli registrovat zákazníky a spravovat je vaším jménem. Nebo pokud jste součástí centrální skupiny IT v podniku, můžete chtít, aby se pobočky mohli registrovat bez zásahu uživatele.

[Delegování nabídek v Azure Stack](../operator/azure-stack-delegated-provider.md) pomáhá s těmito úkoly, protože umožňuje oslovit a spravovat více uživatelů, než můžete přímo.

## <a name="make-sql-databases-available-to-your-azure-stack-users"></a>Zpřístupnit databáze SQL vašim uživatelům Azure Stack
Jako operátor Azure Stack můžete vytvořit nabídky, které umožní vašim uživatelům (klientům) vytvářet databáze SQL, které mohou používat se svými nativními aplikacemi, weby a úlohami v cloudu. Když uživatelům poskytnete tyto vlastní cloudové databáze na vyžádání, ušetříte tím čas a prostředky.

Pomocí adaptéru poskytovatele prostředků SQL Server můžete [zpřístupnit databáze SQL pro vaše Azure Stack uživatele](../operator/azure-stack-sql-resource-provider.md?view=azs-2002) jako službu Azure Stack. Po instalaci poskytovatele prostředků ho připojíte k jedné nebo více instancím SQL Server.

## <a name="make-web-and-api-apps-available-to-your-azure-stack-users"></a>Zpřístupnění webových a API Apps vašim uživatelům Azure Stack
Jako operátor Azure Stack můžete vytvořit nabídky, které umožní vašim uživatelům (klientům) vytvářet Azure Functions a webové aplikace a aplikace API. Poskytnutím přístupu k těmto uživatelům na vyžádání a cloudovým aplikacím můžete ušetřit čas a prostředky.

Nasaďte poskytovatele prostředků App Service, aby [weby a aplikace API byly dostupné pro vaše Azure Stack uživatele](../operator/azure-stack-app-service-overview.md?view=azs-2002).

## <a name="next-steps"></a>Další kroky

[Další informace o poskytování služeb s Azure Stack integrovanými systémy](../operator/service-plan-offer-subscription-overview.md)
