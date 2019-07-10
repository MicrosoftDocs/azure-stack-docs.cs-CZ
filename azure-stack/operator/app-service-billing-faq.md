---
title: Azure App Service na fakturační Přehled služby Azure Stack a nejčastější dotazy | Dokumentace Microsoftu
description: Informace o fungování služby Azure App Service ve službě Azure Stack měří a účtují.
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: 8b17020419183067793309055a6487f31da51246
ms.sourcegitcommit: d67ae2877e3b049bf70b2567b917e55394fb8984
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67712677"
---
# <a name="azure-app-service-on-azure-stack-billing-overview-and-faq"></a>Přehled fakturace Azure Stack a nejčastější dotazy k službě Azure App Service

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Tento článek popisuje, jak se účtují operátorům cloudu pro nabídky Azure App Service ve službě Azure Stack a jak se fakturovat svým klientům pro jejich používání služby.

## <a name="billing-overview"></a>Přehled fakturace

Operátoři Azure stacku cloudu zvolit pro nasazení služby Azure App Service ve službě Azure Stack do své služby Azure Stack razítka nabízet zákazníkům možnosti tenanta služby Azure App Service a Azure Functions. Poskytovatel prostředků Azure App Service se skládá z několika typy rolí, které je možné rozdělit mezi infrastruktury a pracovní vrstvu.

Infrastrukturu role se nic neúčtuje, protože jsou potřeba pro základní provoz služby. Infrastrukturu role lze škálovat, jsou potřeba pro podporu požadavků klientů operátor cloudu. Infrastrukturu role jsou následující:

- Kontrolery
- Role pro správu
- Vydavatelé
- Front-endů

Vrstvy pracovních procesů se skládají ze dvou hlavních typů: sdílené a vyhrazené. Používání pracovních procesů je účtuje na operátor cloudu podle následujících kritérií.

## <a name="shared-workers"></a>Sdílených pracovních procesů

Sdílené zaměstnanci jsou více klientů a plány služby App Service free a shared hostitele a založenou na skutečné spotřebě Azure functions pro velký počet klientů. Generování sdílených pracovních procesů využití měřičů, když se označí jako připravené v poskytovateli prostředků služby Azure App Service.

## <a name="dedicated-workers"></a>Vyhrazené pracovní procesy

Vyhrazené pracovní procesy jsou svázány se plány služby App Service, které vytvářejí tenantů. Například v rámci S1 SKU tenanty můžete škálovat na 10 instancí ve výchozím nastavení. Když si tenant vytvoří plán služby S1 App Service, Azure App Service přidělí jednu z instancí ve škálovací vrstvy pracovního procesu malé sadě na plán služby App Service pro tohoto tenanta. Přiřazené pracovní proces už pak nejsou k dispozici pro přiřazení do jiných tenantů. Pokud klient vybere možnost škálovat plán služby App Service na 10 instancí, devět další pracovní procesy jsou odebrány z fondu k dispozici a jsou přiřazené k plánu služby App Service tenanta.

Měření jsou emitovány pro vyhrazené pracovní procesy, když budou:

- Označí jako připravené v poskytovateli prostředků služby Azure App Service.
- Přiřazené k plánu služby App Service.

Tento model fakturace umožňuje operátorům cloudu ke zřízení fondu připravené pro zákazníky bez nutnosti platit za dělníky, dokud se efektivně vyhrazenými plán služby App Service pro příslušného tenanta používat vyhrazené pracovní procesy. 

Řekněme například, že máte 20 pracovních procesů na úrovni pracovního procesu malé velikosti. Pokud máte pět zákazníků, které vytvářejí dva S1 plány služby App Service každý a každá škálovat plán služby App Service, až dvě instance, máte k dispozici žádné pracovní procesy. V důsledku toho není také žádná kapacita pro všechny zákazníky nebo novým zákazníkům škálovat na více systémů nebo vytvořit nové plány služby App Service. 

Operátoři cloudu můžete zobrazit aktuální počet dostupných pracovních procesů na vrstvu pracovního procesu zobrazením vrstvy pracovních procesů v konfiguraci služby Azure App Service v Azure stacku správu.

![App Service – obrazovky vrstvy pracovních procesů][1]

## <a name="see-customer-usage-by-using-the-azure-stack-usage-service"></a>Zobrazit využití ze strany zákazníků pomocí využití služby Azure Stack

Operátoři cloudu můžete zadávat dotazy [API využití Tenanta prostředků Azure Stack](azure-stack-tenant-resource-usage-api.md) načíst informace o využití pro své zákazníky. Můžete vyhledat všechny jednotlivé měřičů, vytvářejí služby App Service k popisu využití tenanta [nejčastější dotazy k využití](azure-stack-usage-related-faq.md). Těchto měřičů jsou pak používá k výpočtu využití podle předplatného zákazníka k výpočtu poplatků.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="how-do-i-license-the-sql-server-and-file-server-infrastructure-required-in-the-prerequisites"></a>Jak licence SQL serveru a souboru server infrastruktury potřebné v rámci požadavků?

Licencování pro SQL Server a souboru, třeba přes poskytovatele prostředků služby Azure App Service, infrastruktura serveru je zahrnuté ve službě Azure App Service ve službě Azure Stack [před zahájením práce](azure-stack-app-service-before-you-get-started.md#licensing-concerns-for-required-file-server-and-sql-server) článku.

### <a name="the-usage-faq-lists-the-tenant-meters-but-not-the-prices-for-those-meters-where-can-i-find-them"></a>Nejčastější dotazy k využití uvádí měřiče tenanta, ale ne ceny pro tyto měřiče. Kde je?

Jako operátor cloudu budete moci použít vlastní cenový model pro vaše zákazníky. Služba usage poskytuje měření využití. Potom můžete množství měřiče strhávat zákazníky na základě podle cenového modelu, který určíte. Možnost nastavit ceny umožňuje operátorům odlišil od jiných operátorům Azure stacku.

### <a name="as-a-csp-how-can-i-offer-free-and-shared-skus-for-customers-to-try-out-the-service"></a>Jako zprostředkovatel kryptografických služeb Jak můžu nabízí skladové položky free a shared můžete vyzkoušet na služby zákazníkům?

Jako operátor cloudu se vám účtovat za bezplatných a sdílených skladových položek nabídky, protože jste hostované ve sdílených pracovních procesů. Chcete-li minimalizovat náklady, můžete vertikálně snížit kapacitu vrstvy sdílených pracovních procesů na úplném. 

Například nabízí plán služby App Service free a shared SKU a, která nabízí funkce založené na spotřebě, potřebujete minimálně jedné instance A1 k dispozici. Sdílených pracovních procesů jsou víceklientské, takže může být hostitelem více zákaznických aplikací, každý jednotlivě izolované a je chráněn sandboxu služby App Service. Tímto způsobem Škálováním vrstvy sdílených pracovních serverů, můžete omezit vaše výdaj náklady na jeden virtuální procesor za měsíc.

Potom můžete vytvořit kvótu pro použití v plánu, který pouze nabízí skladové položky free a shared a omezuje počet bezplatných a sdílených plánů služby App Service, které můžete vytvořit vaše zákazníky.

## <a name="sample-scripts-to-assist-with-billing"></a>Ukázky skriptů pro účely pomoci s fakturací

Tým Azure App Service vytvoří ukázkové skripty Powershellu pro účely pomoci s dotazování na využití služby Azure Stack. Operátoři cloudu můžete použít tyto ukázkové skripty připravit své vlastní účtování pro jejich klienty. Ukázkové skripty jsou ve [úložiště nástroje pro Azure Stack](https://github.com/Azure/AzureStack-tools) v Githubu. Skripty služby App Service jsou v [služby App Service ve složce využití](https://github.com/Azure/AzureStack-Tools/tree/master/Usage/AppService).

Ukázkové skripty k dispozici jsou:

- [Get-AppServiceBillingRecords](https://github.com/Azure/AzureStack-Tools/blob/master/Usage/AppService/Get-AppServiceBillingRecords.ps1): Tento příklad načte služby Azure App Service na záznamů fakturace Azure Stack z rozhraní API využití služby Azure Stack.
- [Get-AppServiceSubscriptionUsage](https://github.com/Azure/AzureStack-Tools/blob/master/Usage/AppService/Get-AppServiceSubscriptionUsage.ps1): Tato ukázka vypočítá služby Azure App Service v Azure stacku objem využití předplatného. Tento skript vypočítá částky využití na základě dat z rozhraní API pro využití a ceny za měřiče poskytované operátor cloudu.
- [Pozastavit UserSubscriptions](https://github.com/Azure/AzureStack-Tools/blob/master/Usage/AppService/Suspend-UserSubscriptions.ps1): Tato ukázka pozastaví nebo povolí předplatná na základě limitů využití určené operátor cloudu.

## <a name="next-steps"></a>Další postup

- [Azure Stack rozhraní API využití prostředků Tenanta](azure-stack-tenant-resource-usage-api.md)

<!--Image references-->
[1]: ./media/app-service-billing-faq/app-service-worker-tiers.png
