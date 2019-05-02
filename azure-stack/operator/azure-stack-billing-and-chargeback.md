---
title: Fakturace zákazníkům a vrácení peněz ve službě Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak načíst informace o využití prostředků ze služby Azure Stack.
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
ms.date: 03/21/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 03/21/2019
ms.openlocfilehash: 8e47022d826d5983369fe7849cbde8c00f18838e
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "64290976"
---
# <a name="usage-and-billing-in-azure-stack"></a>Využití a fakturace ve službě Azure Stack

Tento článek popisuje, jak uživatelům Azure stacku se vám účtovat využití prostředků a jak je fakturační údaje k němu přistupovat k analýzám a poplatek za zpět.

Azure Stack shromažďuje a skupiny prostředků, které se používají data o využití. Azure Stack pak předá tato data do Azure Commerce. Azure Commerce vám účtuje za využití služby Azure Stack stejným způsobem, jakým se vám účtuje za využití Azure.

Můžete také získat data o využití a export na vlastní fakturace nebo proplacení zálohování systému pomocí fakturační adaptér, nebo je exportovat do nástroje business intelligence, jako je Microsoft Power BI.

## <a name="usage-pipeline"></a>Použití kanálu

Každý poskytovatel prostředků ve službě Azure Stack odesílá data o využití za využití prostředků. Služba usage pravidelně (po hodinách a každý den) agreguje data o využití a uloží je do databáze využití. Azure Stack operátory a uživatelé můžou k datům uložené využití prostřednictvím rozhraní API využití prostředků služby Azure Stack.

Pokud máte [vaší instance služby Azure Stack a registrované v Azure](azure-stack-registration.md ), Azure Stack je nakonfigurovaná k odesílání dat o využití do Azure Commerce. Po nahrání dat do Azure můžete přistupovat prostřednictvím fakturačním portálu nebo pomocí Azure resource využití rozhraní API. Další informace o jaká data o využití se oznamuje službě Azure najdete v tématu [generování sestav dat využití](azure-stack-usage-reporting.md).  

Následující obrázek ukazuje klíčové součásti v kanálu využití:

![Použití kanálu](media/azure-stack-billing-and-chargeback/usagepipeline.png)

## <a name="what-usage-information-can-i-find-and-how"></a>Využití informací můžete najít a jak?

Azure Stack poskytovatele prostředků (jako jsou výpočty, úložiště a síť) generování dat o využití v hodinových intervalech pro každé předplatné. Data o využití obsahuje informace o prostředek, který používá, jako je například název prostředku, předplatné použité a množství. Další informace o ID prostředků ty najdete v tématu [nejčastější dotazy k používání rozhraní API](azure-stack-usage-related-faq.md).

Po shromáždil data o využití je [hlášených Azure](azure-stack-usage-reporting.md) k vygenerování faktury, které lze zobrazit pomocí fakturačním portálu Azure.

> [!NOTE]  
> Generování sestav dat využití není potřeba pro Azure Stack Development Kit (ASDK) a pro Azure Stack integrované systému, kteří licencí v rámci modelu kapacity. Další informace o licencování ve službě Azure Stack, najdete v článku [balení a ceny informační list](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf).

Fakturačním portálu Azure zobrazuje data o využití za fakturovatelnou prostředky. Kromě fakturovatelné prostředky služby Azure Stack zaznamená data o využití pro pestřejší škálu prostředků, které se zobrazí ve vašem prostředí Azure Stack prostřednictvím rozhraní REST API nebo rutiny prostředí PowerShell. Operátoři Azure stacku můžete získat data využití pro všechna předplatná uživatele. Jednotlivým uživatelům můžete získat pouze o jejich vlastní použití.

## <a name="usage-reporting-for-multitenant-cloud-service-providers"></a>Používání vytváření sestav pro víceklientskou poskytovatelů cloudových služeb

Více tenantů Cloud Service Provider (CSP), který má mnoho zákazníků pomocí služby Azure Stack může být vhodné pro zasílání zpráv o využití jednotlivých zákazníků samostatně, tak, aby zprostředkovatel může účtovat využití do různých předplatných Azure.

Každý zákazník má svou identitu reprezentována na jiného tenanta Azure Active Directory (Azure AD). Azure Stack podporuje přiřazení k jednomu předplatnému CSP ke každému tenantovi Azure AD. Základní registrace Azure Stack můžete přidat tenantů a jejich předplatného. Základní registrace je provést v rámci všech instancí služby Azure Stack. Pokud předplatné není zaregistrované pro tenanta, uživatel může dál používat Azure Stack a jejich používání se odešlou do předplatného, které použijete pro základní registrace.

## <a name="next-steps"></a>Další postup

- [Registrovat pomocí služby Azure Stack](azure-stack-registration.md)
- [Hlášení dat o využití služby Azure Stack do Azure](azure-stack-usage-reporting.md)
- [Rozhraní API využití prostředků poskytovatele](azure-stack-provider-resource-api.md)
- [Rozhraní API využití prostředků tenanta](azure-stack-tenant-resource-usage-api.md)
- [Nejčastější dotazy souvisí s využitím](azure-stack-usage-related-faq.md)