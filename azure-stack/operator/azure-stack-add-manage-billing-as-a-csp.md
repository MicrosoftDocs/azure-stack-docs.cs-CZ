---
title: Správa využití a fakturace pro Azure Stack jako Cloud Solution Provider | Microsoft Docs
description: Naučte se, jak zaregistrovat Azure Stack jako poskytovatele Cloud Solution Provider (CSP) a přidat zákazníky k fakturaci.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: e14c57cb5d0444c68619e6be5db688d7b1542e93
ms.sourcegitcommit: b2d19e12a50195bb8925879ee75c186c9604f313
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71961877"
---
# <a name="manage-usage-and-billing-for-azure-stack-as-a-cloud-solution-provider"></a>Správa využití a fakturace pro Azure Stack jako Cloud Solution Provider

*Platí pro: Azure Stack integrovaných systémů @ no__t-0

Tento článek popisuje, jak zaregistrovat Azure Stack jako poskytovatele Cloud Solution Provider (CSP) a jak přidat zákazníky.

Jako CSP pracujete s nejrůznějšími zákazníky, kteří používají Azure Stack. Každý zákazník má v Azure předplatné CSP. Je potřeba, abyste od svého Azure Stack nasměrovali na každé předplatné uživatele.

Následující obrázek ukazuje požadované kroky k výběru účtu sdílených služeb a k registraci účtu Azure pomocí účtu Azure Stack. Po registraci můžete koncovým zákazníkům připojit:

[![Proces pro povolení využití a správy jako procesu poskytovatele Cloud Solution Provider](media/azure-stack-add-manage-billing-as-a-csp/process-add-useage-as-a-csp.png "pro povolení využití a správy jako poskytovatele Cloud Solution Provider")](media/azure-stack-add-manage-billing-as-a-csp/process-add-useage-as-a-csp.png#lightbox)

## <a name="create-a-csp-or-apss-subscription"></a>Vytvoření předplatného CSP nebo APSS

### <a name="csp-subscription-types"></a>Typy předplatného CSP

Zvolte typ účtu sdílených služeb, který používáte pro službu Azure Stack. K registraci služby Azure Stack pro více tenantů je možné použít následující typy předplatných:

- Cloud Solution Provider
- Předplatné Partner Shared Services

#### <a name="azure-partner-shared-services"></a>Sdílené služby pro partnery Azure

Odběry Azure partner Shared Services (APSS) jsou upřednostňovanou volbou pro registraci v případě, že přímý CSP nebo distributor CSP funguje Azure Stack.

APSS předplatná jsou přidružená k tenantovi sdílené služby. Když zaregistrujete Azure Stack, poskytnete přihlašovací údaje pro účet, který je vlastníkem předplatného. Účet, který použijete k registraci Azure Stack, se může lišit od účtu správce, který používáte pro nasazení. Kromě toho tyto dva účty nemusí patřit do stejné domény; můžete nasadit pomocí tenanta, kterého už používáte. Můžete například použít `ContosoCSP.onmicrosoft.com` a pak se zaregistrovat pomocí jiného tenanta. například `IURContosoCSP.onmicrosoft.com`. Při provádění každodenních Azure Stackch správy musíte pamatovat na přihlášení pomocí `ContosoCSP.onmicrosoft.com`. Přihlásíte se k Azure pomocí `IURContosoCSP.onmicrosoft.com`, když potřebujete provádět operace registrace.

Popis předplatných APSS a postup, jak je vytvořit, najdete v tématu [Přidání partnerských sdílených služeb Azure](/partner-center/shared-services).

#### <a name="csp-subscriptions"></a>Předplatná CSP

Předplatná CSP jsou upřednostňovanou volbou pro registraci v případě, že prodejce CSP nebo koncový zákazník funguje Azure Stack.

## <a name="register-azure-stack"></a>Registrace Azure Stack

Pomocí předplatného APSS vytvořeného pomocí informací v předchozí části zaregistrujete Azure Stack s Azure. Další informace najdete v tématu [registrace Azure Stack s vaším předplatným Azure](azure-stack-registration.md).

## <a name="add-end-customer"></a>Přidat koncového zákazníka

Pokud chcete nakonfigurovat Azure Stack, aby se do svého předplatného CSP nahlásilo využití prostředků nového tenanta, přečtěte si téma [Přidání tenanta pro použití a fakturace na Azure Stack](azure-stack-csp-howto-register-tenants.md).

## <a name="charge-the-right-subscriptions"></a>Zpoplatnění správných předplatných

Azure Stack používá funkci nazvanou *registrace*. Registrace je objekt uložený v Azure. Registrační objekt dokumentuje, které předplatné Azure se má použít k účtování daného Azure Stack. V této části se řeší důležitost registrace.

Pomocí registrace Azure Stack můžou:

- Dopředné Azure Stack údaje o využití do Azure Commerce a vyúčtování předplatného Azure.
- Nahlásit využití každého zákazníka v jiném předplatném s nasazením víceklientské Azure Stack. Víceklientská architektura umožňuje Azure Stack podporovat různé organizace v rámci stejné instance Azure Stack.

Pro každý Azure Stack je k dispozici jedno výchozí předplatné a mnoho předplatných tenantů. Výchozím předplatným je předplatné Azure, které se účtuje, pokud není k dispozici žádný odběr konkrétního tenanta. Musí se jednat o první předplatné, které se má zaregistrovat. Aby vytváření sestav využití více tenantů fungovalo, musí být předplatné CSP nebo APSS.

Pak se registrace aktualizuje pomocí předplatného Azure pro každého tenanta, který používá Azure Stack. Předplatná klientů musí být typu CSP a musí se vystavit partnerovi, který vlastní výchozí předplatné. Nemůžete registrovat zákazníky jiné osoby.

Když Azure Stack přepošle informace o využití do globální služby Azure, služba v Azure konzultuje registraci a namapuje využití každého tenanta na příslušné předplatné tenanta. Pokud není tenant zaregistrován, toto využití se týká výchozího předplatného instance Azure Stack, ze které pochází.

Vzhledem k tomu, že odběry tenantů jsou předplatná CSP, odesílají se jejich vyúčtování partnerovi CSP a informace o použití nejsou viditelné pro koncového zákazníka.

## <a name="next-steps"></a>Další kroky

- Další informace o programu CSP najdete v tématu [program Cloud Solution Provider](https://partner.microsoft.com/solutions/microsoft-cloud-solutions).
- Další informace o tom, jak načíst informace o využití prostředků z Azure Stack, najdete [v tématu využití a fakturace v Azure Stack](azure-stack-billing-and-chargeback.md).
