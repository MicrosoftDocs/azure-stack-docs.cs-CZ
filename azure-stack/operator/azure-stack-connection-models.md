---
title: Modely připojení integrovaných systémů Azure Stack hub | Microsoft Docs
description: Zjistěte modely připojení a další rozhodnutí plánování nasazení pro integrované systémy Azure Stack hub.
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
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: wfayed
ms.lastreviewed: 02/21/2019
ms.openlocfilehash: 53057c5235422f7d5ec016fcd8f9f3286c41dc58
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76534069"
---
# <a name="azure-stack-hub-integrated-systems-connection-models"></a>Modely připojení integrovaných systémů Azure Stack hub
Pokud vás zajímá nákup Azure Stack integrovaného systému centra, je potřeba pochopit [několik důležitých informací o integraci Datacenter](azure-stack-datacenter-integration.md) pro Azure Stack nasazení centra, abyste zjistili, jak se systém vejde do vašeho datacentra. Kromě toho se musíte rozhodnout, jak budete integrovat Azure Stack hub do svého hybridního cloudového prostředí. Tento článek obsahuje přehled těchto hlavních rozhodnutí, včetně modelů připojení Azure, možností úložiště identit a možností modelu fakturace.

Pokud se rozhodnete koupit integrovaný systém, výrobce hardwaru OEM (Original Equipment Manufacturer) vám pomůže s procesem plánování podrobněji. Dodavatel hardwaru výrobce OEM také provede skutečné nasazení.

## <a name="choose-an-azure-stack-hub-deployment-connection-model"></a>Zvolit model připojení nasazení centra Azure Stack hub
Můžete zvolit nasazení centra Azure Stack buď připojeného k Internetu (a do Azure), nebo odpojena. Nasaďte připojení do Azure, abyste získali nejvíc výhod z centra Azure Stack, včetně hybridních scénářů mezi centrem Azure Stack a Azure. Tato volba definuje, jaké možnosti jsou dostupné pro vaše úložiště identit (Azure Active Directory nebo Active Directory Federation Services (AD FS)) a model fakturace (průběžné platby podle fakturace nebo fakturace na základě kapacity), které jsou shrnuté v následujícím diagramu a tabulce:

![Scénáře nasazení a fakturace centra Azure Stack](media/azure-stack-connection-models/azure-stack-scenarios.png)
  
> [!IMPORTANT]
> Toto je klíčový bod rozhodování! Výběr Active Directory Federation Services (AD FS) (AD FS) nebo Azure Active Directory (Azure AD) je jednorázové rozhodnutí, které je nutné provést v době nasazení. Tuto změnu nelze později změnit bez nutnosti opětovného nasazení celého systému.  


|Možnosti|Připojeno k Azure|Odpojeno od Azure|
|-----|:-----:|:-----:|
|Azure AD|![Podporováno](media/azure-stack-connection-models/check.png)| |
|AD FS|![Podporováno](media/azure-stack-connection-models/check.png)|![Podporováno](media/azure-stack-connection-models/check.png)|
|Fakturace na základě spotřeby|![Podporováno](media/azure-stack-connection-models/check.png)| |
|Fakturace na základě kapacity|![Podporováno](media/azure-stack-connection-models/check.png)|![Podporováno](media/azure-stack-connection-models/check.png)|
|Správa licencí| Poskytovatel řešení smlouva Enterprise nebo cloudu | Smlouva Enterprise |
|Oprava a aktualizace|Balíček aktualizace se dá stáhnout přímo z Internetu do centra Azure Stack. |  Požaduje se<br><br>Vyžaduje taky vyměnitelné médium.<br> a samostatné připojené zařízení |
| Registrace | Automaticky | Požaduje se<br><br>Vyžaduje taky vyměnitelné médium.<br> a samostatné připojené zařízení |

Až se rozhodnete, že se model připojení Azure bude používat pro nasazení centra Azure Stack, je nutné provést další rozhodnutí závislá na připojení pro úložiště identit a metodu fakturace.

## <a name="next-steps"></a>Další kroky

[Rozhodnutí o nasazení centra Azure Stack připojená k Azure](azure-stack-connected-deployment.md)

[Rozhodnutí o nasazení centra Azure Stack odpojená od Azure](azure-stack-disconnected-deployment.md)
