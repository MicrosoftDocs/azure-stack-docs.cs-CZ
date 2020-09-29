---
title: Kódy chyb registrace využití a fakturace v centru Azure Stack
description: Přečtěte si informace o chybách při používání a registraci fakturace v centru Azure Stack.
author: sethmanheim
ms.topic: article
ms.date: 08/27/2020
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 06/27/2019
ms.openlocfilehash: 0c143a2a997a044e102eef63018dba5a492a3e11
ms.sourcegitcommit: 03aad17afe8519536066c735c59ad1bdfe8de083
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2020
ms.locfileid: "89041600"
---
# <a name="usage-and-billing-registration-error-codes"></a>Kódy chyb registrace využití a fakturace

Pokud jste poskytovatelem Cloud Solution Provider (CSP), můžou se při [Přidávání klientů](azure-stack-csp-ref-operations.md#add-tenant-to-registration) do registrace pro vytváření sestav pro použití s ID předplatného Azure zákazníka vyskytnout následující chybové zprávy.

## <a name="list-of-registration-error-codes"></a>Seznam kódů chyb registrace

| Chyba   | Podrobnosti  | Komentáře  |
|---|---|---|
| RegistrationNotFound | Poskytnutá registrace se nenašla. Ujistěte se, že byly správně poskytnuty následující informace:<br>1. identifikátor předplatného (zadaná hodnota: **identifikátor předplatného**),<br>2. skupina prostředků (zadaná hodnota: **Skupina prostředků**),<br>3. název registrace (zadaná hodnota: **název registrace**). | K této chybě obvykle dochází v případě, že informace ukazující na počáteční registraci nejsou správné. Pokud potřebujete ověřit skupinu prostředků a název registrace, najdete je v Azure Portal uvedením všech prostředků. Pokud najdete více než jeden registrační prostředek, podívejte se na **CloudDeploymentID** ve vlastnostech a vyberte registraci, jejíž **CloudDeploymentID** odpovídá vašemu cloudu. K vyhledání **CloudDeploymentID**můžete použít tento příkaz PowerShellu v centru Azure Stack:<br>`$azureStackStampInfo = Invoke-Command -Session $session -ScriptBlock { Get-AzureStackStampInformation }` |
| BadCustomerSubscriptionId | Zadaný **identifikátor předplatného zákazníka** a identifikátor předplatného **názvu registrace** NEpatří stejnému zprostředkovateli CSP společnosti Microsoft. Ověřte správnost identifikátoru předplatného zákazníka. ID předplatného zákazníka rozlišuje velká a malá písmena. Pokud se problém opakuje, obraťte se na podporu. | K této chybě dochází v případě, že předplatné zákazníka je předplatným CSP, ale je jiné než partner CSP, který se liší od toho, k němuž se předplatné používané při prvotní registraci použijí. Tato kontrola se zabrání situaci, která by způsobila, že se bude účtovat partner CSP, který není zodpovědný za použití centra Azure Stack. |
| InvalidCustomerSubscriptionId  | **Identifikátor předplatného zákazníka** není platný. Ujistěte se, že je k dispozici platné předplatné Azure. |   |
| CustomerSubscriptionNotFound  | V **názvu registrace**se nenašel **identifikátor předplatného zákazníka** . Ujistěte se, že se používá platné předplatné Azure a že ID předplatného bylo přidáno k registraci pomocí operace PUT. | K této chybě dochází, když se pokusíte Verity, že se tenant přidal do předplatného, ale předplatné zákazníka se nezjistilo k registraci. Zákazník není přidaný k registraci nebo ID předplatného je nesprávně napsáno. |
| UnauthorizedCspRegistration | Zadaný **název registrace** není schválený pro použití víceklientské architektury. Odešlete e-mail azstCSP@microsoft.com a zahrňte do něj název registrace, skupinu prostředků a identifikátor předplatného, který jste použili při registraci. | Aby bylo možné začít přidávat klienty do této služby, musí být registrace schválena pro více tenantů od Microsoftu. |
| CustomerSubscriptionsNotAllowed | Operace zákaznického předplatného se pro odpojené zákazníky nepodporují. Pokud chcete tuto funkci použít, proveďte znovu registraci licencování s průběžnými platbami. | Registrace, ke které se pokoušíte přidat klienty, je registrace kapacity. Takže při vytvoření registrace se `BillingModel Capacity` použil parametr. Jenom registrace s průběžnými platbami můžou přidávat klienty. Pomocí parametru se musíte znovu zaregistrovat `BillingModel PayAsYouUse` . |
| InvalidCSPSubscription | Zadaný **identifikátor předplatného zákazníka** není platné předplatné CSP. Ujistěte se, že je k dispozici platné předplatné Azure. | Tato chyba je pravděpodobně způsobena chybou typu předplatné zákazníka. |
| MetadataResolverBadGatewayError | Jeden z nadřazených serverů vrátil neočekávanou chybu. Zkuste to později. Pokud se problém opakuje, obraťte se na podporu. |

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [infrastruktuře vytváření sestav využití pro poskytovatele Cloud Solution Provider](azure-stack-csp-ref-infrastructure.md).
- Další informace o programu CSP najdete v tématu [cloudová řešení](https://partner.microsoft.com/solutions/microsoft-cloud-solutions).
- Další informace o tom, jak načíst informace o využití prostředků z centra Azure Stack, najdete [v tématu využití a fakturace v centru Azure Stack](azure-stack-billing-and-chargeback.md).
