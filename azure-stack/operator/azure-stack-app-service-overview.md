---
title: Přehled Azure Stack App Service | Microsoft Docs
description: Přehled App Service v Azure Stack
services: azure-stack
documentationcenter: ''
author: BryanLa
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: anwestg
ms.lastreviewed: 10/16/2018
ms.openlocfilehash: 698d38e971d37457d50637cb2e7e2ffe88890c20
ms.sourcegitcommit: 7d7a4c8c46613b6104caf23763bfd2275f6a826b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2019
ms.locfileid: "70808217"
---
# <a name="app-service-on-azure-stack-overview"></a>Přehled App Service v Azure Stacku

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Azure App Service v Azure Stack je nabídka typu platforma jako služba (PaaS) z Microsoft Azure dostupná na Azure Stack. Služba umožňuje vašim interním nebo externím zákazníkům vytvářet webové aplikace, rozhraní API a Azure Functions aplikací pro libovolnou platformu nebo zařízení. Můžou vaše aplikace integrovat s místními aplikacemi a automatizovat jejich obchodní procesy. Provozovatelé cloudu Azure Stack můžou spouštět aplikace zákazníka na plně spravovaných virtuálních počítačích s jejich výběrem ze sdílených prostředků virtuálních počítačů nebo vyhrazených virtuálních počítačů.

Azure App Service umožňuje automatizovat obchodní procesy a hostovat cloudová rozhraní API. Jako jediná integrovaná služba Azure App Service umožňuje kombinovat různé komponenty, jako jsou weby, rozhraní REST API a obchodní procesy, do jediného řešení.

## <a name="why-offer-azure-app-service-on-azure-stack"></a>Proč nabízet Azure App Service Azure Stack?

Toto jsou některé klíčové funkce a možnosti služby App Service:

- **Více jazyků a platforem**: App Service má první třídu podpora pro ASP.NET, Node. js, Java, PHP a Python. V App Service virtuálních počítačích můžete také spouštět prostředí Windows PowerShell a další skripty nebo spustitelné soubory.
- **Optimalizace DevOps**: Nastavte průběžnou integraci a nasazování pomocí GitHubu, místního Gitu nebo BitBucket. Pomocí Azure PowerShell nebo rozhraní příkazového řádku (CLI) pro více platforem vám pomůžou propagovat aktualizace prostřednictvím testovacích a přípravných prostředí a spravovat aplikace v App Service.
- **Integrace sady Visual Studio**: Vyhrazené nástroje v aplikaci Visual Studio zjednodušují práci při vytváření a nasazování aplikací.

## <a name="app-types-in-app-service"></a>Typy aplikací v App Service

App Service nabízí několik typů aplikací, z nichž každá je určena k hostování konkrétního zatížení:

- [Web Apps](/azure/app-service/overview) hostování webů a webových aplikací.
- [API Apps](/azure/app-service/overview) pro hostování rozhraní REST API.
- Azure Functions pro hostování řízených událostí a úloh bez serveru.

*Aplikace Word odkazuje* na prostředky hostování vyhrazené pro spuštění úlohy. *Webová aplikace* jako příklad, pravděpodobně jste zvyklí vzít webovou aplikaci za výpočetní prostředky i kód aplikace, který společně doručí funkce prohlížeči. V App Service je webová aplikace výpočetním prostředkem, který Azure Stack poskytuje pro hostování kódu vaší aplikace.

Vaše aplikace se může skládat z více App Service aplikací různých druhů. Pokud se například vaše aplikace skládá z webového front-endu a REST API back-endu, můžete:

- Nasazení obou (front-end i rozhraní API) do jedné webové aplikace
- Nasadit kód front-endu do webové aplikace a kód back-endu do aplikace API.

   [![Přehled App Service s daty monitorování](media/azure-stack-app-service-overview/image01.png "Přehled App Service s daty monitorování")](media/azure-stack-app-service-overview/image01.png#lightbox)

## <a name="what-is-an-app-service-plan"></a>Co je plán App Service?

Poskytovatel prostředků App Service používá stejný kód, jaký Azure App Service používá, a proto sdílí některé běžné koncepty. V App Service se v kontejneru pro aplikace označuje jako *App Service plán*. Představuje sadu vyhrazených virtuálních počítačů používaných k ukládání vašich aplikací. V rámci daného předplatného můžete mít několik plánů App Service.

V Azure jsou sdílené a vyhrazené pracovní procesy. Sdílený pracovní proces podporuje hostování s vysokou hustotou a víceklientské aplikace a existuje jenom jedna sada sdílených pracovních procesů. Vyhrazené servery používá jenom jeden tenant a jsou ve třech velikostech: malá, střední a velká. Potřeby místních zákazníků není možné vždy popsat pomocí těchto podmínek. V App Service na Azure Stack můžou správci poskytovatele prostředků definovat vrstvy pracovních procesů, které mají být dostupné. Na základě jedinečných potřeb hostování můžete definovat několik sad sdílených pracovních procesů nebo různých sad vyhrazených pracovních procesů. Pomocí těchto definic pracovních vrstev můžou potom definovat své vlastní cenové SKU.

## <a name="portal-features"></a>Funkce portálu

App Service v Azure Stack používá stejné uživatelské rozhraní, které Azure App Service používá. Totéž platí pro back-end. Některé funkce jsou ale v Azure Stack zakázané. Očekávání nebo služby specifické pro Azure, které tyto funkce vyžadují, nejsou aktuálně k dispozici v Azure Stack.

## <a name="next-steps"></a>Další kroky

- [Předpoklady pro nasazení App Service v Azure Stack](azure-stack-app-service-before-you-get-started.md)
- [Instalace poskytovatele prostředků App Service](azure-stack-app-service-deploy.md)

Můžete také vyzkoušet jiné [služby platformy jako služby (PaaS)](azure-stack-offer-services-overview.md), například [poskytovatele prostředků SQL Server](azure-stack-sql-resource-provider-deploy.md) a [poskytovatele prostředků MySQL](azure-stack-mysql-resource-provider-deploy.md).
