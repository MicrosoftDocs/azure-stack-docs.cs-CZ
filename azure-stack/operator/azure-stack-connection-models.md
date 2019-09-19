---
title: Azure Stack modely připojení integrovaných systémů | Microsoft Docs
description: Určení modelů připojení a dalších rozhodnutí o plánování nasazení pro Azure Stack integrovaných systémů.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2019
ms.author: mabrigg
ms.reviewer: wfayed
ms.lastreviewed: 02/21/2019
ms.openlocfilehash: de9051ceee89244182c1d6d9d5724fa80a594ae6
ms.sourcegitcommit: c196463492732218d2474d3a964f88e995272c80
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/18/2019
ms.locfileid: "71094381"
---
# <a name="azure-stack-integrated-systems-connection-models"></a>Azure Stack modely připojení integrovaných systémů
Pokud vás zajímá nákup Azure Stack integrovaného systému, je potřeba pochopit [několik důležitých informací o integraci Datacenter](azure-stack-datacenter-integration.md) pro Azure Stack nasazení, abyste zjistili, jak se systém vejde do vašeho datacentra. Kromě toho se musíte rozhodnout, jak budete Azure Stack do svého hybridního cloudového prostředí integrovat. Tento článek obsahuje přehled těchto hlavních rozhodnutí, včetně modelů připojení Azure, možností úložiště identit a možností modelu fakturace.

Pokud se rozhodnete koupit integrovaný systém, výrobce hardwaru OEM (Original Equipment Manufacturer) vám pomůže s procesem plánování podrobněji. Dodavatel hardwaru výrobce OEM také provede skutečné nasazení.

## <a name="choose-an-azure-stack-deployment-connection-model"></a>Vybrat model připojení Azure Stack nasazení
Můžete zvolit nasazení Azure Stack buď připojeného k Internetu (a k Azure), nebo odpojeni. Nasaďte připojení k Azure, abyste získali nejvíc výhod z Azure Stack, včetně hybridních scénářů mezi Azure Stack a Azure. Tato volba definuje, jaké možnosti jsou dostupné pro vaše úložiště identit (Azure Active Directory nebo Active Directory Federation Services (AD FS)) a model fakturace (průběžné platby podle fakturace nebo fakturace na základě kapacity), které jsou shrnuté v následujícím diagramu a tabulce:

![Azure Stack scénáře nasazení a fakturace](media/azure-stack-connection-models/azure-stack-scenarios.png)
  
> [!IMPORTANT]
> Toto je klíčový bod rozhodování! Výběr Active Directory Federation Services (AD FS) (AD FS) nebo Azure Active Directory (Azure AD) je jednorázové rozhodnutí, které je nutné provést v době nasazení. Tuto změnu nelze později změnit bez nutnosti opětovného nasazení celého systému.  


|Možnosti|Připojeno k Azure|Odpojeno od Azure|
|-----|:-----:|:-----:|
|Azure AD|![Podporováno](media/azure-stack-connection-models/check.png)| |
|AD FS|![Podporováno](media/azure-stack-connection-models/check.png)|![Podporováno](media/azure-stack-connection-models/check.png)|
|Fakturace na základě spotřeby|![Podporováno](media/azure-stack-connection-models/check.png)| |
|Fakturace na základě kapacity|![Podporováno](media/azure-stack-connection-models/check.png)|![Podporováno](media/azure-stack-connection-models/check.png)|
|Licencování| Poskytovatel řešení smlouva Enterprise nebo cloudu | Smlouva Enterprise |
|Oprava a aktualizace|Balíček aktualizace se dá stáhnout přímo z Internetu a Azure Stack |  Požadováno<br><br>Vyžaduje taky vyměnitelné médium.<br> a samostatné připojené zařízení |
| Registrace | Automaticky | Požadováno<br><br>Vyžaduje taky vyměnitelné médium.<br> a samostatné připojené zařízení |

Až se rozhodnete, že se model připojení Azure bude používat pro nasazení Azure Stack, je nutné provést další rozhodnutí závislá na připojení pro úložiště identit a metodu fakturace.

## <a name="next-steps"></a>Další postup

[Rozhodování související s nasazením služby Azure Stack propojené s Azure](azure-stack-connected-deployment.md)

[Rozhodování související s nasazením služby Azure Stack nepropojené s Azure](azure-stack-disconnected-deployment.md)
