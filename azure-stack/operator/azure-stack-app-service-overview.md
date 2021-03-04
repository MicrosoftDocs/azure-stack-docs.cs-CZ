---
title: Přehled Azure App Service Azure Stack
description: Přehled Azure App Service a Azure Functions v Azure Stackm centru.
author: BryanLa
ms.topic: article
ms.date: 05/05/2020
ms.author: BryanLa
ms.reviewer: anwestg
ms.lastreviewed: 01/13/2019
ms.openlocfilehash: 78573a16d9fc376d7903badfd28bda9366ca1ddb
ms.sourcegitcommit: 2c6418ee465e67edd417961b1f5211b2e09dbd5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102116984"
---
# <a name="azure-app-service-and-azure-functions-on-azure-stack-hub-overview"></a>Přehled Azure App Service a Azure Functions v Azure Stackovém centru

Azure App Service v centru Azure Stack je nabídka typu platforma jako služba (PaaS) z Microsoft Azure dostupná v Azure Stackovém centru. Služba umožňuje vašim interním nebo externím zákazníkům vytvářet webové a Azure Functions aplikace pro libovolnou platformu nebo zařízení. Můžou vaše aplikace integrovat s místními aplikacemi a automatizovat jejich obchodní procesy. Operátoři cloudu centra Azure Stack můžou spouštět aplikace zákazníka na plně spravovaných virtuálních počítačích s jejich výběrem ze sdílených prostředků virtuálních počítačů nebo vyhrazených virtuálních počítačů.

Azure App Service umožňuje automatizovat obchodní procesy a hostovat cloudová rozhraní API. Jako jediná integrovaná služba Azure App Service umožňuje kombinovat různé komponenty (například weby, rozhraní REST API a obchodní procesy) do jediného řešení.

## <a name="why-offer-azure-app-service-on-azure-stack-hub"></a>Proč nabízet Azure App Service v centru Azure Stack?

Tady jsou některé klíčové funkce a funkce Azure App Service:

- **Více jazyků a platforem**: Azure App Service má prvotřídní podporu pro ASP.NET, Node.js, Java, php a Python. Na virtuálních počítačích App Service můžete také spouštět Windows PowerShell a další skripty nebo spustitelné soubory.
- **Optimalizace DevOps**: nastavení průběžné integrace a nasazování pomocí GitHubu, místního Gitu nebo Bitbucket. Pomocí Azure PowerShell nebo rozhraní příkazového řádku (CLI) pro více platforem vám pomůžou propagovat aktualizace prostřednictvím testovacích a přípravných prostředí a spravovat aplikace v App Service.
- **Integrace sady Visual Studio**: vyhrazené nástroje v nástroji Visual Studio zjednodušují práci při vytváření a nasazování aplikací.

## <a name="app-types-in-app-service"></a>Typy aplikací v App Service

App Service nabízí několik typů aplikací, z nichž každá je určena k hostování konkrétního typu úlohy:

- [Web Apps](/azure/app-service/overview) hostování webů, webových aplikací a rozhraní REST API.
- [Azure Functions v1](/azure/azure-functions/functions-versions) pro hostování řízených událostí a úloh bez serveru.

*Aplikace Word odkazuje* na prostředky hostování vyhrazené pro spuštění úlohy. *Webová aplikace* jako příklad, pravděpodobně jste zvyklí vzít webovou aplikaci za výpočetní prostředky i kód aplikace, který společně doručí funkce prohlížeči. V Azure App Service je webová aplikace výpočetním prostředkem, který centrum Azure Stack poskytuje pro hostování kódu vaší aplikace.

Vaše aplikace se může skládat z více App Service aplikací různých druhů. Pokud se například vaše aplikace skládá z webového front-endu a REST API back-endu, můžete:

- Nasaďte obojí (front-end i rozhraní API) do jediné webové aplikace.
- Nasadit kód front-endu do webové aplikace a kód back-endu do aplikace API.

   [![Přehled App Service s daty monitorování](media/azure-stack-app-service-overview/image01.png "Přehled App Service s daty monitorování")](media/azure-stack-app-service-overview/image01.png#lightbox)

## <a name="what-is-an-app-service-plan"></a>Co je plán služby App Service?

Poskytovatel prostředků App Service používá stejný kód, jaký Azure App Service používá, a proto sdílí některé běžné koncepty. V App Service se v kontejneru pro aplikace označuje jako *App Service plán*. Představuje sadu vyhrazených virtuálních počítačů používaných k ukládání vašich aplikací. V rámci daného předplatného můžete mít několik plánů App Service.

V Azure jsou sdílené a vyhrazené pracovní procesy. Sdílený pracovní proces podporuje hostování s vysokou hustotou a víceklientské aplikace a existuje jenom jedna sada sdílených pracovních procesů. Vyhrazené servery používá jenom jeden tenant a jsou ve třech velikostech: malá, střední a velká. Potřeby místních zákazníků není možné vždy popsat pomocí těchto podmínek. V App Service v centru Azure Stack správci poskytovatele prostředků definují vrstvy pracovních procesů, které mají být k dispozici. Na základě jedinečných potřeb hostování můžete definovat několik sad sdílených pracovních procesů nebo různých sad vyhrazených pracovních procesů. Pomocí těchto definic pracovních vrstev můžou potom definovat své vlastní cenové SKU.

## <a name="portal-features"></a>Funkce portálu

Azure App Service v centru Azure Stack používá stejné uživatelské rozhraní, které Azure App Service používá. Totéž platí pro back-end. Některé funkce jsou ale v Azure Stackovém centru zakázané. Očekávání nebo služby specifické pro Azure, které tyto funkce vyžadují, nejsou aktuálně k dispozici ve službě Azure Stack hub.

## <a name="next-steps"></a>Další kroky

- [Požadavky na nasazení služby App Service ve službě Azure Stack Hub](azure-stack-app-service-before-you-get-started.md)
- [Instalace poskytovatele prostředků Azure App Service](azure-stack-app-service-deploy.md)

Můžete také vyzkoušet jiné [služby platformy jako služby (PaaS)](service-plan-offer-subscription-overview.md), například [poskytovatele prostředků SQL Server](azure-stack-sql-resource-provider-deploy.md) a [poskytovatele prostředků MySQL](azure-stack-mysql-resource-provider-deploy.md).
