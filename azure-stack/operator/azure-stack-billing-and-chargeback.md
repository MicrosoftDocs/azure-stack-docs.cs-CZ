---
title: Fakturace a vrácení peněz zákazníka v centru Azure Stack
description: Přečtěte si, jak se uživatelům centra Azure Stack fakturovat za využití prostředků a jak se k fakturačním informacím dostanete pro účely analýzy a vrácení peněz.
author: sethmanheim
ms.topic: article
ms.date: 02/16/2021
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 03/21/2019
ms.openlocfilehash: 478014ac48ccc060281f8feab2271f1c5af1ed79
ms.sourcegitcommit: 34babe5abf1bceee718011b5c5c25f75e1b03b0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100562565"
---
# <a name="usage-and-billing-in-azure-stack-hub"></a>Využití a fakturace v centru Azure Stack

Tento článek popisuje, jak se Azure Stack centra uživatelů fakturují za využití prostředků a jak se k fakturačním informacím dostanete pro účely analýzy a vrácení peněz.

Centrum Azure Stack shromažďuje a seskupuje data o využití prostředků, které se používají, a pak předává tato data do Azure Commerce. Azure Commerce vám umožní Azure Stack využití centra stejným způsobem jako při používání Azure.

Můžete taky získat data o využití a exportovat je do vašeho vlastního fakturačního nebo vrácení peněz systému pomocí fakturačního adaptéru nebo ho exportovat do business intelligenceho nástroje, jako je Microsoft Power BI.

## <a name="usage-pipeline"></a>Kanál využití

Každý poskytovatel prostředků v centru Azure Stack odesílá data o využití podle využití prostředků. Služba využití pravidelně (každou hodinu a denně) agreguje data o využití a ukládá je do databáze využití. Obsluha centra Azure Stack a uživatelé mají přístup k uloženým datům o využití prostřednictvím rozhraní API využití prostředků centra Azure Stack.

Pokud jste [zaregistrovali instanci centra Azure Stack s Azure](azure-stack-registration.md), Azure Stack centrum je nakonfigurované tak, aby odesílalo data o využití do Azure Commerce. Po nahrání dat do Azure máte k ní přístup prostřednictvím fakturačního portálu nebo pomocí rozhraní API využití prostředků Azure. Další informace o tom, jaká data o využití se oznamují do Azure, najdete v tématu [generování sestav dat o využití](azure-stack-usage-reporting.md).  

Následující obrázek ukazuje klíčové komponenty v kanálu použití:

![Kanál využití](media/azure-stack-billing-and-chargeback/usagepipeline.svg)

## <a name="what-usage-information-can-i-find-and-how"></a>Jaké informace o využití můžu najít a jak?

Poskytovatelé prostředků Azure Stack hub (například výpočetní prostředky, úložiště a síť) generují data o využití v hodinových intervalech pro každé předplatné. Data o využití obsahují informace o používaném prostředku. například název prostředku, použitý odběr a použité množství. Další informace o zdrojích ID měřičů najdete v tématu [Nejčastější dotazy k rozhraní API pro využití](azure-stack-usage-related-faq.md).

Po shromáždění dat o využití se tato [zpráva oznamuje službě Azure](azure-stack-usage-reporting.md) za účelem vygenerování faktury, kterou si můžete prohlédnout na fakturačním portálu Azure.

> [!NOTE]  
> Vytváření sestav dat o využití není vyžadováno pro Azure Stack Development Kit (ASDK) a pro uživatele s integrovanými systémy pro Azure Stack centra, kteří mají licenci pod modelem kapacity. Další informace o licencování v centru Azure Stack najdete v článku o [balení a](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf)ceníku dat.

Fakturační portál Azure zobrazuje data o využití pro Fakturovatelné prostředky. Kromě fakturovatelnouch prostředků centrum Azure Stack zachytí data o využití pro širší sadu prostředků, ke kterým máte přístup v prostředí centra Azure Stack prostřednictvím rozhraní REST API nebo rutin PowerShellu. Operátory centra Azure Stack můžou získat data o využití pro všechna předplatná uživatelů. Jednotliví uživatelé mohou získat pouze své informace o využití.

## <a name="usage-reporting-for-multi-tenant-cloud-solution-providers"></a>Vytváření sestav využití pro poskytovatele cloudových řešení pro více tenantů

Zprostředkovatel Cloud Solution Provider (CSP) pomocí centra Azure Stack může chtít nahlásit jednotlivá zákaznická použití samostatně, aby poskytovatel mohl účtovat využívání různých předplatných Azure.

Každý zákazník má svou identitu reprezentovanou jiným klientem Azure Active Directory (Azure AD). Centrum Azure Stack podporuje přiřazení jednoho poskytovatele CSP ke každému tenantovi služby Azure AD. Můžete přidat klienty a jejich odběry do základní Azure Stack registrace centra. Základní registrace se provádí pro všechny instance centra Azure Stack. Pokud předplatné není pro tenanta zaregistrované, může uživatel dál používat centrum Azure Stack a jejich využití se posílá do předplatného používaného pro základní registraci.

## <a name="next-steps"></a>Další kroky

- [Registrovat do centra Azure Stack](azure-stack-registration.md)
- [Sestava Azure Stack data o využití centra do Azure](azure-stack-usage-reporting.md)
- [Rozhraní API využití prostředků poskytovatele](azure-stack-provider-resource-api.md)
- [Rozhraní API využití prostředků tenanta](azure-stack-tenant-resource-usage-api.md)
- [Nejčastější dotazy týkající se použití](azure-stack-usage-related-faq.md)
