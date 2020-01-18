---
title: Přehled a časté otázky týkající se fakturace centra Azure Stack Azure App Service | Microsoft Docs
description: Podrobnosti o tom, jak se Azure App Service v centru Azure Stack měří a účtuje.
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
ms.openlocfilehash: f45cda455f0c13fd2e6c5cb431fb3c60d54dce03
ms.sourcegitcommit: ba2fd47e74adebe1fcbb489d059a2c27d59b179c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2020
ms.locfileid: "76256321"
---
# <a name="azure-app-service-on-azure-stack-hub-billing-overview-and-faq"></a>Přehled a nejčastější dotazy týkající se fakturace centra Azure Stack Azure App Service

V tomto článku se dozvíte, jak se operátoři cloudu účtují za nabídku Azure App Service v centru Azure Stack a jak můžou vyúčtováním svých tenantů jejich používání.

## <a name="billing-overview"></a>Přehled fakturace

Operátoři cloudu Azure Stack hub zvolit nasazení Azure App Service v Azure Stack hub do svého Azure Stack razítka centra, aby nabídli možnosti tenanta Azure App Service a Azure Functions zákazníkům. Poskytovatel prostředků Azure App Service se skládá z několika typů rolí, které je možné rozdělit mezi infrastrukturu a úrovně pracovních procesů.

Role infrastruktury se neúčtují, protože jsou nutné pro základní provozní operace služby. Role infrastruktury se dají škálovat podle potřeby, aby se podporovaly požadavky na klienty cloudového operátora. Role infrastruktury jsou následující:

- Kontrolery
- Role správy
- Vydavatelé
- Front-endy

Vrstvy pracovního procesu se skládají ze dvou hlavních typů: Shared a vyhrazené. Využití pracovního procesu se fakturuje jako výchozí předplatné poskytovatele cloudu, a to podle následujících kritérií.

## <a name="shared-workers"></a>Sdílené pracovní procesy

Sdílené pracovní procesy jsou víceklientské a hostitelské a sdílené App Service plány a služby Azure Functions založené na spotřebě pro mnoho klientů. Sdílené pracovní procesy generují měřiče využití, pokud jsou označeny jako připravené v poskytovateli prostředků Azure App Service.

## <a name="dedicated-workers"></a>Vyhrazené pracovní procesy

Vyhrazené pracovní procesy jsou vázané na plány App Service vytvořené klienty. Například v SKU S1 se klienti můžou ve výchozím nastavení škálovat na 10 instancí. Když tenant vytvoří plán App Service S1, Azure App Service přidělí jednu z instancí v malém měřítku vrstvy pracovního procesu nastavené na plán App Service daného tenanta. Přiřazený pracovník už není k dispozici pro přiřazení k žádným jiným klientům. Pokud se klient rozhodne škálovat App Service plán na 10 instancí, odebere se z dostupného fondu devět dalších pracovních procesů a přiřadí se k plánu App Service tenanta.

Plynoměry se generují pro vyhrazené pracovní procesy, když jsou:

- V Azure App Service poskytovatel prostředků je označený jako připravený.
- Přiřazeno k plánu App Service.

Tento model fakturace umožňuje operátorům cloudu zřídit fond vyhrazených pracovníků, kteří budou pro zákazníky připraveni použít bez placení zaměstnanců, dokud nebudou efektivně rezervováni plánem App Service svého tenanta. 

Řekněme například, že máte 20 pracovních procesů na úrovni malé pracovní procesy. Pokud máte pět zákazníků, kteří vytvoří dva plány App Service S1 a každý rozsah App Service naplánuje na dvě instance, nemáte k dispozici žádné pracovní procesy. V důsledku toho není k dispozici žádná kapacita pro žádné z vašich zákazníků ani nové zákazníky, kteří by mohli škálovat nebo vytvářet nové plány App Service. 

Operátoři cloudu můžou zobrazit aktuální počet dostupných pracovních procesů na úrovni pracovního procesu, a to tak, že si prohlížíte vrstvy pracovních procesů v Azure App Service konfiguraci Azure Stack správy centra.

![App Service – obrazovka vrstev pracovního procesu][1]

## <a name="see-customer-usage-by-using-the-azure-stack-hub-usage-service"></a>Podívejte se na zákaznická využití pomocí služby Azure Stack hub Usage.

Operátoři cloudu se můžou dotazovat na [rozhraní API využití prostředků tenanta centra Azure Stack](azure-stack-tenant-resource-usage-api.md) a získat informace o využití pro své zákazníky. Můžete najít všechny měřiče, které App Service emitují k popisu využití tenanta v [nejčastějších dotazech k používání](azure-stack-usage-related-faq.md). Tyto měřiče se pak použijí k výpočtu nákladů na předplatné zákazníka za účelem výpočtu poplatků.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="how-do-i-license-the-sql-server-and-file-server-infrastructure-required-in-the-prerequisites"></a>Návody licence SQL Server a infrastruktura souborového serveru požadovaná v požadavcích?

Licencování pro SQL Server a infrastrukturu souborového serveru, kterou vyžaduje poskytovatel prostředků Azure App Service, je popsaná v Azure App Service na Azure Stack hub [před zahájením práce](azure-stack-app-service-before-you-get-started.md#licensing-concerns-for-required-file-server-and-sql-server) s článkem.

### <a name="the-usage-faq-lists-the-tenant-meters-but-not-the-prices-for-those-meters-where-can-i-find-them"></a>V části Nejčastější dotazy o využití se zobrazují měřiče klientů, ale ne ceny za tyto měřiče. Kde je můžu najít?

Jako operátor cloudu můžete zákazníkům používat vlastní cenový model. Služba využití poskytuje měření využití. Množství měřiče pak můžete použít k účtování zákazníků na základě cenového modelu, který určíte. Možnost nastavit ceny umožňuje operátorům odlišit od ostatních operátorů centra Azure Stack.

### <a name="as-a-csp-how-can-i-offer-free-and-shared-skus-for-customers-to-try-out-the-service"></a>Jak se jako CSP dá nabídnout zákazníkům bezplatné a sdílené skladové jednotky, aby si ji mohli vyzkoušet?

Jako operátor cloudu se účtují náklady na bezplatné a sdílené skladové položky, protože jsou hostované ve sdílených pracovních procesech. K minimalizaci těchto nákladů můžete zvolit možnost horizontálního navýšení kapacity úrovně sdíleného pracovního procesu na minimum. 

Pokud například chcete nabízet bezplatné a sdílené skladové jednotky plánů App Service a nabízet funkce založené na spotřebě, potřebujete minimálně jednu dostupnou instanci a1. Sdílené pracovní procesy jsou víceklientské, takže mohou hostovat více zákaznických aplikací, jednotlivě izolované a chráněné App Service izolovaným prostorem (sandbox). Změnou měřítka sdílené pracovní vrstvy tímto způsobem můžete nákladům omezit na cenu jednoho vCPU měsíčně.

Pak můžete zvolit vytvoření kvóty pro použití v plánu, který nabízí jenom bezplatné a sdílené skladové položky a omezuje počet bezplatných a sdílených App Service plánů, které zákazník může vytvořit.

## <a name="sample-scripts-to-assist-with-billing"></a>Ukázkové skripty, které vám pomůžou s fakturací

Tým Azure App Service vytvořil ukázkové skripty PowerShellu, které vám pomůžou s dotazování služby využití centra Azure Stack. Operátoři cloudu můžou tyto ukázkové skripty použít k přípravě jejich fakturace pro své klienty. Ukázkové skripty jsou v [úložišti nástrojů centra Azure Stack](https://github.com/Azure/AzureStack-tools) v GitHubu. Skripty App Service jsou ve [složce AppService v části použití](https://aka.ms/aa6zku8).

K dispozici jsou tyto ukázkové skripty:

- [Get-AppServiceBillingRecords](https://aka.ms/aa6zku2): Tato ukázka NAČTE z rozhraní API Azure Stack využití centra Azure Stack Azure App Service záznamy fakturace na rozbočovači.
- [Get-AppServiceSubscriptionUsage](https://aka.ms/aa6zku6): Tato ukázka vypočítá Azure App Service v Azure Stack využití centra na základě předplatného. Tento skript vypočítá množství využití na základě dat z rozhraní API využití a cen zadaných za měřič cloudového operátora.
- [Suspend-UserSubscriptions](https://aka.ms/aa6zku7): Tato ukázka pozastaví nebo povolí odběry na základě omezení využití určeného operátorem cloudu.

## <a name="next-steps"></a>Další kroky

- [Rozhraní API využití prostředků tenanta centra Azure Stack](azure-stack-tenant-resource-usage-api.md)

<!--Image references-->
[1]: ./media/app-service-billing-faq/app-service-worker-tiers.png
