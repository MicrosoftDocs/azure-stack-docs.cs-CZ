---
title: Modely připojení integrovaných systémů Azure Stack hub
description: Zjistěte modely připojení a další rozhodnutí plánování nasazení pro integrované systémy Azure Stack hub.
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: inhenkel
ms.reviewer: wfayed
ms.lastreviewed: 02/21/2019
ms.openlocfilehash: 4e0fb77bc6f6f174d474a123611dbf529e814b0a
ms.sourcegitcommit: 1fa0140481a483e5c27f602386fe1fae77ad29f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2020
ms.locfileid: "78364040"
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
|Azure AD|![Podporuje se](media/azure-stack-connection-models/check.png)| |
|AD FS|![Podporuje se](media/azure-stack-connection-models/check.png)|![Podporuje se](media/azure-stack-connection-models/check.png)|
|Fakturace na základě spotřeby|![Podporuje se](media/azure-stack-connection-models/check.png)| |
|Fakturace na základě kapacity|![Podporuje se](media/azure-stack-connection-models/check.png)|![Podporuje se](media/azure-stack-connection-models/check.png)|
|Licencování| Poskytovatel řešení smlouva Enterprise nebo cloudu | Smlouva Enterprise |
|Oprava a aktualizace|Balíček aktualizace se dá stáhnout přímo z Internetu do centra Azure Stack. |  Požaduje se<br><br>Vyžaduje taky vyměnitelné médium.<br> a samostatné připojené zařízení |
| Registrace | Automatickou | Požaduje se<br><br>Vyžaduje taky vyměnitelné médium.<br> a samostatné připojené zařízení |

Až se rozhodnete, že se model připojení Azure bude používat pro nasazení centra Azure Stack, je nutné provést další rozhodnutí závislá na připojení pro úložiště identit a metodu fakturace.

## <a name="next-steps"></a>Další kroky

[Rozhodnutí o nasazení centra Azure Stack připojená k Azure](azure-stack-connected-deployment.md)

[Rozhodnutí o nasazení centra Azure Stack odpojená od Azure](azure-stack-disconnected-deployment.md)
