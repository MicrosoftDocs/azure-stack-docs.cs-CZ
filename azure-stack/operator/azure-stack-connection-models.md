---
title: Azure Stack integrované systémy připojení modely | Dokumentace Microsoftu
description: Určení při rozhodování o několika uzly Azure Stack pro plánování nasazení.
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
ms.date: 02/12/2019
ms.author: mabrigg
ms.reviewer: wfayed
ms.lastreviewed: 02/21/2019
ms.openlocfilehash: da48a9a04c9daaf2d7a29bc2d4b300563bebd6e5
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618977"
---
# <a name="azure-stack-integrated-systems-connection-models"></a>Azure Stack integrované systémy modely připojení
Pokud máte zájem o zakoupení systémech pro Azure Stack integrované, musíte pochopit [důležité informace o integraci několika datacenter](azure-stack-datacenter-integration.md) pro nasazení Azure Stack určit, jak systém, se vejde do vašeho datového centra. Kromě toho bude nutné se rozhodnout, jak budou integrovat Azure Stack v hybridním cloudovém prostředí. Tento článek obsahuje přehled Tato hlavní rozhodnutí, včetně připojení k Azure, úložiště identit a fakturační model rozhodnutí.

Pokud se rozhodnete pro nákup integrovaného systému, pomůže dodavatele hardwaru, výrobce OEM (OEM), vás provedou velkou část procesu plánování podrobněji. Také provede nasazování ve skutečnosti.

## <a name="choose-an-azure-stack-deployment-connection-model"></a>Zvolte připojení modelu nasazení Azure Stack
Můžete nasadit Azure Stack buď připojené k Internetu (a do Azure) nebo odpojeno. Nejvíce výhod získat z Azure Stack, hybridní scénáře mezi Azure Stack a Azure, včetně byste k nasazení připojení k Azure. Tato volba určuje, jaké možnosti jsou k dispozici pro úložiště identit (Azure Active Directory nebo Active Directory Federation Services) a model fakturace (platit při použití na základě fakturace nebo založená na kapacitě fakturace) dle souhrnu v následující diagram a tabulka: 

![Azure Stack nasazení a fakturaci scénáře](media/azure-stack-connection-models/azure-stack-scenarios.png)  
  
> [!IMPORTANT]
> Toto je klíčová rozhodnutí bod! Výběr Active Directory Federation Services (AD FS) nebo Azure Active Directory (Azure AD) je jednorázový rozhodnutí, které je nutné provést v době nasazení. Nedá se změnit později bez nutnosti nového nasazení celého systému.  


|Možnosti|Připojení k Azure|Odpojení od Azure|
|-----|:-----:|:-----:|
|Azure AD|![Podporováno](media/azure-stack-connection-models/check.png)| |
|AD FS|![Podporováno](media/azure-stack-connection-models/check.png)|![Podporováno](media/azure-stack-connection-models/check.png)|
|Účtování podle využití|![Podporováno](media/azure-stack-connection-models/check.png)| |
|Účtování podle kapacity|![Podporováno](media/azure-stack-connection-models/check.png)|![Podporováno](media/azure-stack-connection-models/check.png)|
|Licencování| Smlouvy Enterprise nebo poskytovatele Cloud Solution Provider | Smlouva Enterprise |
|Opravy a aktualizace|Balíček aktualizací můžete stáhnout přímo z Internetu do služby Azure Stack |  Požaduje se<br><br>Také vyžaduje vyměnitelné médium<br> a samostatné připojené zařízení |
| Registrace | Automaticky | Požaduje se<br><br>Také vyžaduje vyměnitelné médium<br> a samostatné připojené zařízení |

Poté, co jste se rozhodli na model Azure připojení budou používat při nasazení Azure Stack, závislé na připojení a další rozhodnutí třeba identity úložiště a fakturace metodu. 

## <a name="next-steps"></a>Další postup

[Rozhodování související s nasazením služby Azure Stack propojené s Azure](azure-stack-connected-deployment.md)

[Rozhodování související s nasazením služby Azure Stack nepropojené s Azure](azure-stack-disconnected-deployment.md)
