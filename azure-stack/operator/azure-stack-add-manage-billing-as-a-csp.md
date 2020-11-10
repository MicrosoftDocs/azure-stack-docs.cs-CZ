---
title: Správa využití centra Azure Stack a fakturace jako poskytovatel Cloud Solution Provider
description: Naučte se registrovat Azure Stack hub jako poskytovatele Cloud Solution Provider (CSP) a přidat zákazníky k fakturaci.
author: sethmanheim
ms.topic: article
ms.date: 11/09/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 10/15/2019
ms.openlocfilehash: 0c3e82753835adfb70be13f43a14c20701881885
ms.sourcegitcommit: 980be7813e6f39fb59926174a5d3e0d392b04293
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2020
ms.locfileid: "94414074"
---
# <a name="manage-usage-and-billing-for-azure-stack-hub-as-a-cloud-solution-provider"></a>Správa využití a fakturace pro centra Azure Stack jako poskytovatele Cloud Solution Provider

Tento článek popisuje, jak zaregistrovat Azure Stack hub jako poskytovatele Cloud Solution Provider (CSP) a jak přidat zákazníky.

Jako CSP pracujete s nejrůznějšími zákazníky pomocí centra Azure Stack. Každý zákazník má v Azure předplatné CSP. Je potřeba, abyste od svého centra Azure Stack nasměrovali na každé předplatné uživatele.

Následující obrázek ukazuje požadované kroky k výběru účtu sdílených služeb a k registraci účtu Azure pomocí účtu služby Azure Stack hub. Po registraci můžete koncovým zákazníkům připojit:

[![Proces pro povolení používání a správy jako poskytovatele Cloud Solution Provider](media/azure-stack-add-manage-billing-as-a-csp/process-add-useage-as-a-csp.svg "Proces pro povolení používání a správy jako poskytovatele Cloud Solution Provider")](media/azure-stack-add-manage-billing-as-a-csp/process-add-useage-as-a-csp.svg)

## <a name="create-a-csp-or-apss-subscription"></a>Vytvoření předplatného CSP nebo APSS

### <a name="csp-subscription-types"></a>Typy předplatného CSP

Vyberte typ účtu sdílených služeb, který používáte pro Azure Stack hub. Typy předplatných, které se dají použít k registraci Azure Stackho centra pro více tenantů, jsou:

- Program Cloud Solution Provider
- Předplatné Partner Shared Services

Vytvořili jsme výukové video, které vám pomůže pochopit, jak spravovat vaše práva pro více tenantů:

> [!VIDEO https://www.youtube.com/embed/ZP6jkbLeS34]

#### <a name="azure-partner-shared-services"></a>Azure Partner Shared Services

Předplatné Azure partner Shared Services (APSS) jsou upřednostňovanou volbou pro registraci v případě, že přímý CSP nebo distributor CSP funguje Azure Stack hub.

APSS předplatná jsou přidružená k tenantovi sdílené služby. Při registraci centra Azure Stack zadáte přihlašovací údaje pro účet, který je vlastníkem předplatného. Účet, který použijete k registraci centra Azure Stack, se může lišit od účtu správce, který používáte pro nasazení. Kromě toho tyto dva účty nemusí patřit do stejné domény; můžete nasadit pomocí tenanta, kterého už používáte. Můžete například použít `ContosoCSP.onmicrosoft.com` a zaregistrovat se pomocí jiného tenanta, například `IURContosoCSP.onmicrosoft.com` . Abyste se mohli přihlásit pomocí `ContosoCSP.onmicrosoft.com` každodenní správy centra Azure Stack, musíte si uvědomit, že se přihlašujete. Přihlásíte se k Azure pomocí, `IURContosoCSP.onmicrosoft.com` kdy potřebujete provádět operace registrace.

Popis předplatných APSS a postup, jak je vytvořit, najdete v tématu [Přidání partnerských sdílených služeb Azure](/partner-center/shared-services).

#### <a name="csp-subscriptions"></a>Předplatná CSP

Předplatná CSP jsou upřednostňovanou volbou pro registraci v případě, že prodejce CSP nebo koncový zákazník funguje Azure Stack hub.

## <a name="register-azure-stack-hub"></a>Registrace centra Azure Stack

Pomocí předplatného APSS vytvořeného pomocí informací v předchozí části zaregistrujete Azure Stack centrum s Azure. Další informace najdete v tématu [registrace centra Azure Stack s vaším předplatným Azure](azure-stack-registration.md).

## <a name="add-end-customer"></a>Přidat koncového zákazníka

Pokud chcete nakonfigurovat centrum Azure Stack tak, aby se do svého předplatného CSP nahlásilo využití prostředků nového tenanta, přečtěte si téma [Přidání tenanta pro využití a fakturace do centra Azure Stack](azure-stack-csp-howto-register-tenants.md).

## <a name="charge-the-right-subscriptions"></a>Zpoplatnění správných předplatných

Centrum Azure Stack používá funkci nazvanou *registrace*. Registrace je objekt uložený v Azure. Registrační objekt dokumentuje, které předplatné Azure se má použít k účtování na dané centrum Azure Stack. V této části se řeší důležitost registrace.

Pomocí registrace Azure Stack centrum může:

- Dopředné [Azure Stack údaje o využití centra](azure-stack-billing-and-chargeback.md) do Azure Commerce a vyúčtování předplatného Azure.
- Nahlásit využití každého zákazníka v jiném předplatném pomocí nasazení Azure Stackho centra pro více tenantů. Víceklientská architektura umožňuje Azure Stack rozbočovači podporovat různé organizace ve stejné instanci centra Azure Stack.

Pro každé centrum Azure Stack existuje jedno výchozí předplatné a mnoho předplatných tenantů. Výchozím předplatným je předplatné Azure, které se účtuje, pokud není k dispozici žádný odběr konkrétního tenanta. Musí se jednat o první předplatné, které se má zaregistrovat. Aby vytváření sestav využití více tenantů fungovalo, musí být předplatné CSP nebo APSS.

Pak se registrace aktualizuje pomocí předplatného Azure pro každého tenanta, který používá centrum Azure Stack. Předplatná klientů musí být typu CSP a musí se vystavit partnerovi, který vlastní výchozí předplatné. Nemůžete registrovat zákazníky jiné osoby.

Když Azure Stack centrum přepošle informace o využití do globálního Azure, služba v Azure propojí registraci a namapuje využití každého tenanta na příslušné předplatné tenanta. Pokud tenant není zaregistrovaný, toto využití se týká výchozího předplatného pro instanci služby Azure Stack hub, ze které pochází.

Vzhledem k tomu, že odběry tenantů jsou předplatná CSP, odesílají se jejich vyúčtování partnerovi CSP a informace o použití nejsou viditelné pro koncového zákazníka.

## <a name="next-steps"></a>Další kroky

- Další informace o programu CSP najdete v tématu [program Cloud Solution Provider](https://partner.microsoft.com/solutions/microsoft-cloud-solutions).
- Další informace o tom, jak načíst informace o využití prostředků z centra Azure Stack, najdete [v tématu využití a fakturace v centru Azure Stack](azure-stack-billing-and-chargeback.md).
