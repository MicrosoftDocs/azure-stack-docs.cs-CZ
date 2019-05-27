---
title: Azure App Service na přehled fakturace Azure Stack a nejčastější dotazy | Dokumentace Microsoftu
description: Podrobnosti o měří a účtují se poplatky za služby Azure App Service ve službě Azure Stack.
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 05/23/2019
ms.openlocfilehash: 7b11f00c98c6433ad035b6190789276ea5aa6fa7
ms.sourcegitcommit: be5382f715a9c1c18c660b630d8fcd823f13aae3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/24/2019
ms.locfileid: "66213015"
---
# <a name="azure-app-service-on-azure-stack-billing-overview-and-faq"></a>Přehled fakturace Azure Stack a nejčastější dotazy k službě Azure App Service

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Pokyny v tomto dokumentu ukazuje, jak se účtují operátorům cloudu pro nabídky Azure App Service ve službě Azure Stack a jak se můžete zase fakturovat svým klientům za využití této služby.

## <a name="billing-overview"></a>Přehled fakturace

Operátoři Azure stacku cloudu zvolit pro nasazení služby Azure App Service ve službě Azure Stack do své služby Azure Stack razítko aby bylo možné nabízet zákazníkům možnosti tenanta služby Azure App Service a Azure Functions.  Poskytovatel prostředků Azure App Service se skládá z několika typy rolí, které je možné rozdělit mezi infrastruktury a pracovní vrstvu.

Infrastrukturu role se nic neúčtuje podle potřeby pro základní provoz služby.  Infrastrukturu role lze škálovat, jsou potřeba pro podporu požadavků klientů operátor cloudu.  Infrastrukturu role jsou následující:

- Kontrolery
- Role pro správu
- Vydavatelé
- Front-endů

Vrstvy pracovních procesů se skládají ze dvou hlavních typů: sdílené a vyhrazené. Používání pracovních procesů je účtuje na operátor cloudu podle následujících kritérií.

## <a name="shared-workers"></a>Sdílených pracovních procesů

Sdílené zaměstnanci jsou více tenantů a plány služby App Service free a shared hostitele a založenou na skutečné spotřebě Azure Functions pro velký počet klientů. Generování sdílených pracovních procesů využití měřičů, když se označí jako připravené v poskytovateli prostředků služby Azure App Service.

## <a name="dedicated-workers"></a>Vyhrazené pracovní procesy

Vyhrazené pracovní procesy jsou svázány se plány služby App Service, které vytvářejí tenantům. Například v rámci S1 SKU, ve výchozím nastavení klienty můžete škálovat na 10 instancí ve výchozím nastavení. Proto když si tenant vytvoří plán služby S1 App Service, Azure App Service přidělí jednu z instancí ve škálovací vrstvy pracovního procesu malé sadě na plán služby App Service pro tohoto tenanta. Následně přiřazené pracovní proces už nejsou k dispozici pro přiřazení do jiných tenantů.  Pokud klient vybere možnost škálovat plán služby App Service na 10 instancí, pak další 9 pracovní procesy jsou odebrány z fondu k dispozici a jsou přiřazené k plánu služby App Service tenanta.

Měření jsou emitovány pro vyhrazené pracovní procesy, když jsou:

- Označí jako připravené v poskytovateli prostředků služby Azure App Service.
- Přiřazené k plánu služby App Service.

Proto se tento model fakturace umožňuje operátorům cloudu ke zřízení fondu připravené pro zákazníky bez nutnosti platit za dělníky, dokud jejich tenanta plán služby app service efektivně vyhrazené používat vyhrazené pracovní procesy. Například pokud máte 20 pracovních procesů na úrovni pracovního procesu malé velikosti a pak máte 5 pět zákazníků, vytvořte dva S1 plány služby App Service každý a každá škálovat plán služby App Service, až dvě instance, pak budete mít k dispozici žádné pracovní procesy. V důsledku toho také nebudou žádné kapacity pro všechny zákazníky nebo novým zákazníkům škálovat na více systémů nebo vytvořit nové plány služby App Service. Operátoři cloudu můžete zobrazit aktuální počet dostupných pracovních procesů na vrstvu pracovního procesu zobrazením vrstvy pracovních procesů v konfiguraci služby Azure App Service v Azure stacku správu.

![Vrstvy pracovních procesů služby App Service][1]

## <a name="see-customer-usage-using-the-azure-stack-usage-service"></a>Zobrazit využití ze strany zákazníků pomocí využití služby Azure Stack

Operátorům cloudu můžete zadávat dotazy [rozhraní API využití tenanta služby Azure Stack](azure-stack-tenant-resource-usage-api.md) načíst využití informace pro zákazníky a můžete vyhledat všechny jednotlivé měřiče této službě App Service vysílá k popisu využití tenanta [nejčastější dotazy k využití ](azure-stack-usage-related-faq.md). Těchto měřičů je pak možné vypočítat využití za předplatné zákazníka, které lze použít k výpočtu poplatků.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="how-do-i-license-the-sql-server-and-file-server-infrastructure-required-in-the-pre-requisites"></a>Jak mám licenci SQL serveru a souboru server infrastruktury potřebné v požadavcích

Licencování pro SQL a soubor nutná infrastruktura serveru, od poskytovatele prostředků služby Azure App Service, je zahrnuté ve službě Azure App Service ve službě Azure Stack – [před zahájením práce](azure-stack-app-service-before-you-get-started.md#licensing-concerns-for-required-file-server-and-sql-server) článku.

### <a name="the-usage-faq-lists-the-tenant-meters-but-not-the-prices-for-those-meters-where-can-i-find-them"></a>Nejčastější dotazy k využití uvádí měřiče tenanta, ale ne ceny těchto měřičů, kde je můžou najít

Operátoři cloudu jsou zdarma použít vlastní cenový model jejich koncovým zákazníkům. Služba usage poskytuje měření využití a operátor cloudu musí pomocí měřiče množství platby prostřednictvím svých zákazníků na základě podle cenového modelu, které určují. S tuto možnost nastavit ceny umožňuje operátory k odlišení od jiných operátorům Azure stacku.

## <a name="next-steps"></a>Další postup

- [Rozhraní API využití Tenanta služby Azure Stack](azure-stack-tenant-resource-usage-api.md)

<!--Image references-->
[1]: ./media/app-service-billing-faq/app-service-worker-tiers.png