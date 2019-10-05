---
title: Fakturace a vrácení peněz zákazníka v Azure Stack | Microsoft Docs
description: Přečtěte si, jak se Azure Stack uživatelům fakturovat za využití prostředků a jak se k fakturačním informacím dostanete pro účely analýzy a vrácení peněz.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/04/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 03/21/2019
ms.openlocfilehash: d3eacfa8ab4b071d44ebd3bd2ad52351b72e7f00
ms.sourcegitcommit: f91979c1613ea1aa0e223c818fc208d902b81299
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2019
ms.locfileid: "71974042"
---
# <a name="usage-and-billing-in-azure-stack"></a>Využití a fakturace v Azure Stack

Tento článek popisuje, jak se Azure Stack uživatelům fakturují využití prostředků a jak se k fakturačním informacím dostanete pro účely analýzy a vrácení peněz.

Azure Stack shromažďuje a seskupuje data o využití prostředků, které se používají, a pak tato data předává do Azure Commerce. Azure Commerce vám umožní Azure Stack využití stejným způsobem jako při používání Azure.

Můžete taky získat data o využití a exportovat je do vašeho vlastního fakturačního nebo vrácení peněz systému pomocí fakturačního adaptéru nebo ho exportovat do business intelligenceho nástroje, jako je Microsoft Power BI.

## <a name="usage-pipeline"></a>Kanál využití

Každý poskytovatel prostředků v Azure Stack odesílá data o využití podle využití prostředků. Služba využití pravidelně (každou hodinu a denně) agreguje data o využití a ukládá je do databáze využití. Azure Stack operátory a uživatelé mají přístup k uloženým datům o využití prostřednictvím rozhraní API pro využití prostředků Azure Stack.

Pokud jste [vaši instanci Azure Stack zaregistrovali v Azure](azure-stack-registration.md), Azure Stack je nakonfigurovaná tak, aby odesílala data o využití do Azure Commerce. Po nahrání dat do Azure máte k ní přístup prostřednictvím fakturačního portálu nebo pomocí rozhraní API využití prostředků Azure. Další informace o tom, jaká data o využití se oznamují do Azure, najdete v tématu [generování sestav dat o využití](azure-stack-usage-reporting.md).  

Následující obrázek ukazuje klíčové komponenty v kanálu použití:

![Kanál využití](media/azure-stack-billing-and-chargeback/usagepipeline.png)

## <a name="what-usage-information-can-i-find-and-how"></a>Jaké informace o využití můžu najít a jak?

Poskytovatelé prostředků Azure Stack (například výpočetní prostředky, úložiště a síť) generují data o využití v hodinových intervalech pro každé předplatné. Data o využití obsahují informace o použitém prostředku, jako je název prostředku, použité předplatné a využité množství. Další informace o zdrojích ID měřičů najdete v tématu [Nejčastější dotazy k rozhraní API pro využití](azure-stack-usage-related-faq.md).

Po shromáždění dat o využití se tato [zpráva oznamuje službě Azure](azure-stack-usage-reporting.md) za účelem vygenerování faktury, kterou si můžete prohlédnout na fakturačním portálu Azure.

> [!NOTE]  
> Vytváření sestav dat o využití není pro Azure Stack Development Kit (ASDK) vyžadováno a pro Azure Stack integrovaných systémových uživatelů, kteří mají licenci pod modelem kapacity. Další informace o licencování v Azure Stack naleznete v části [balení a ceníku dat](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf).

Fakturační portál Azure zobrazuje data o využití pro Fakturovatelné prostředky. Kromě fakturovatelnouch prostředků Azure Stack zachycuje data o využití pro širší sadu prostředků, ke kterým můžete přistupovat ve svém Azure Stack prostředí prostřednictvím rozhraní REST API nebo rutin PowerShellu. Azure Stack operátory můžou získat data o využití pro všechna předplatná uživatelů. Jednotliví uživatelé mohou získat pouze své informace o využití.

## <a name="usage-reporting-for-multi-tenant-cloud-solution-providers"></a>Vytváření sestav využití pro poskytovatele cloudových řešení pro více tenantů

Zprostředkovatel Cloud Solution Provider (CSP) s více klienty používá Azure Stack může chtít nahlásit jednotlivé zákaznické využití zvlášť, aby poskytovatel mohl účtovat využití různých předplatných Azure.

Každý zákazník má svou identitu reprezentovanou jiným klientem Azure Active Directory (Azure AD). Azure Stack podporuje přiřazení jednoho poskytovatele CSP ke každému tenantovi služby Azure AD. Můžete přidat klienty a jejich odběry do základní Azure Stack registrace. Základní registrace se provádí pro všechny instance Azure Stack. Pokud předplatné není pro tenanta zaregistrované, uživatel může dál používat Azure Stack a jejich použití se pošle do předplatného používaného pro základní registraci.

## <a name="next-steps"></a>Další kroky

- [Zaregistrovat s Azure Stack](azure-stack-registration.md)
- [Hlášení dat o využití služby Azure Stack do Azure](azure-stack-usage-reporting.md)
- [Rozhraní API využití prostředků poskytovatele](azure-stack-provider-resource-api.md)
- [Rozhraní API využití prostředků tenanta](azure-stack-tenant-resource-usage-api.md)
- [Nejčastější dotazy týkající se použití](azure-stack-usage-related-faq.md)
