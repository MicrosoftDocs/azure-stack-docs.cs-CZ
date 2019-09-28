---
title: Registrovat klienty pro sledování využití v Azure Stack | Microsoft Docs
description: Naučte se, jak registrovat klienty a jak je využití tenanta sledováno v Azure Stack.
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
ms.date: 09/17/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 01/08/2019
ms.openlocfilehash: a9e0dd05195d7ece62689aa8b5971cf72a6e3713
ms.sourcegitcommit: c2ea4ffb42563c26faaf2993ba7b484bcb6d5cb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2019
ms.locfileid: "71342836"
---
# <a name="register-tenants-for-usage-tracking-in-azure-stack"></a>Registrace tenantů pro sledování využití v Azure Stack

*Platí pro: Azure Stack integrovaných systémů*

Tento článek obsahuje podrobnosti o operacích registrace. Tyto operace můžete použít k těmto akcím:

- Spravovat registrace tenantů
- Spravovat sledování využití tenanta

## <a name="add-tenant-to-registration"></a>Přidat tenanta k registraci

Tuto operaci můžete použít, pokud chcete přidat nového tenanta k registraci. Využití tenanta se hlásí v rámci předplatného Azure připojeného k tenantovi Azure Active Directory (Azure AD).

Tuto operaci můžete také použít ke změně předplatného přidruženého k tenantovi. Volání metody PUT nebo **New-AzureRMResource** přepíše předchozí mapování.

K tenantovi můžete přidružit jedno předplatné Azure. Pokud se pokusíte přidat druhé předplatné do stávajícího klienta, první předplatné se přepíše.

### <a name="use-api-profiles"></a>Použití profilů rozhraní API

Následující rutiny registrace vyžadují, abyste při spuštění prostředí PowerShell zadali profil rozhraní API. Profily rozhraní API reprezentují sadu poskytovatelů prostředků Azure a jejich verzí rozhraní API. Při interakci s více cloudy Azure vám pomůžou používat správnou verzi rozhraní API. Pokud například pracujete s více cloudy při práci s globálním prostředím Azure a Azure Stack, profily rozhraní API určují název, který odpovídá datu vydání verze. Použijete profil **2017-09-03** .

Další informace o Azure Stack a profilech rozhraní API najdete v tématu [Správa profilů verzí rozhraní API v Azure Stack](../user/azure-stack-version-profiles.md).

### <a name="parameters"></a>Parametry

| Parametr                  | Popis |
|---                         | --- |
| registrationSubscriptionID | Předplatné Azure, které se použilo pro prvotní registraci. |
| customerSubscriptionID     | Předplatné Azure (není Azure Stack) patřící zákazníkovi k registraci. Musí se vytvořit v nabídce zprostředkovatel Cloud Solution Provider (CSP) prostřednictvím partnerského centra. Pokud má zákazník více než jednoho tenanta, vytvořte si předplatné pro tenanta, abyste se přihlásili k Azure Stack. |
| resourceGroup              | Skupina prostředků v Azure, ve které je uložená vaše registrace. |
| registrationName           | Název registrace Azure Stack. Je to objekt uložený v Azure. Název je obvykle ve tvaru **azurestack-CloudID**, kde **CloudID** je ID cloudu nasazení Azure Stack. |

> [!NOTE]  
> Klienti musí být zaregistrovaní u každého Azure Stack nasazení, které používají. Pokud tenant používá více než jeden Azure Stack, aktualizujte počáteční registraci každého nasazení u předplatného tenanta.

### <a name="powershell"></a>PowerShell

K přidání tenanta použijte rutinu **New-AzureRmResource** . [Připojte se k Azure Stack](azure-stack-powershell-configure-admin.md)a potom na příkazovém řádku se zvýšenými oprávněními použijte následující rutinu:

```powershell
New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01 -Properties
```

### <a name="api-call"></a>Volání rozhraní API

**Operace**: PUT  
**RequestURI**:`subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  /providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Odpověď**: 201 vytvořeno  
**Tělo odpovědi**: Prázdné  

## <a name="list-all-registered-tenants"></a>Zobrazit seznam všech registrovaných tenantů

Získá seznam všech tenantů, které byly přidány k registraci.

 > [!NOTE]  
 > Pokud nebyli registrováni žádní klienti, neobdržíte odpověď.

### <a name="parameters"></a>Parametry

| Parametr                  | Popis          |
|---                         | ---                  |
| registrationSubscriptionId | Předplatné Azure, které se použilo pro prvotní registraci.   |
| resourceGroup              | Skupina prostředků v Azure, ve které je uložená vaše registrace.    |
| registrationName           | Název registrace Azure Stack nasazení. Je to objekt uložený v Azure. Název je obvykle ve formátu **azurestack-CloudID**, kde **CloudID** je ID cloudu nasazení Azure Stack.   |

### <a name="powershell"></a>PowerShell

Pomocí rutiny **Get-AzureRmResource zobrazíte** seznam všech registrovaných tenantů. [Připojte se k Azure Stack](azure-stack-powershell-configure-admin.md)a pak na příkazovém řádku se zvýšenými oprávněními spusťte následující rutinu:

```powershell
Get-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>Volání rozhraní API

Můžete získat seznam mapování všech klientů pomocí operace GET.

**Operace**: GET  
**RequestURI**:`subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  
/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions?  
api-version=2017-06-01 HTTP/1.1`  
**Odpověď**: 200  
**Tělo odpovědi**:

```json
{
    "value": [{
            "id": " subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{ cspSubscriptionId 1}",
            "name": " cspSubscriptionId 1",
            "type": "Microsoft.AzureStack\customerSubscriptions",
            "properties": { "tenantId": "tId1" }
        },
        {
            "id": " subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{ cspSubscriptionId 2}",
            "name": " cspSubscriptionId2 ",
            "type": "Microsoft.AzureStack\customerSubscriptions",
            "properties": { "tenantId": "tId2" }
        }
    ],
    "nextLink": "{originalRequestUrl}?$skipToken={opaqueString}"
}
```

## <a name="remove-a-tenant-mapping"></a>Odebrání mapování tenanta

Můžete odebrat klienta, který byl přidán k registraci. Pokud tento tenant stále používá prostředky na Azure Stack, účtuje se jejich využití na základě předplatného, které se používá při prvotní registraci Azure Stack.

### <a name="parameters"></a>Parametry

| Parametr                  | Popis          |
|---                         | ---                  |
| registrationSubscriptionId | ID předplatného pro registraci.   |
| resourceGroup              | Skupina prostředků pro registraci.   |
| registrationName           | Název registrace.  |
| customerSubscriptionId     | ID předplatného zákazníka.  |

### <a name="powershell"></a>PowerShell

K odebrání tenanta použijte rutinu **Remove-AzureRmResource** . [Připojte se k Azure Stack](azure-stack-powershell-configure-admin.md)a pak na příkazovém řádku se zvýšenými oprávněními spusťte následující rutinu:

```powershell
Remove-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>Volání rozhraní API

Mapování klientů můžete odebrat pomocí operace DELETE.

**Operace**: DELETE  
**RequestURI**:`subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  
/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Odpověď**: 204 bez obsahu  
**Tělo odpovědi**: Prázdné

## <a name="next-steps"></a>Další kroky

- [Jak načíst informace o využití prostředků z Azure Stack](azure-stack-billing-and-chargeback.md)
