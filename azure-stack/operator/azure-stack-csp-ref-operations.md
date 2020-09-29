---
title: Registrace tenantů pro sledování využití v centru Azure Stack
description: Naučte se, jak registrovat klienty a jak je využití tenanta sledováno v Azure Stack hub.
author: sethmanheim
ms.topic: article
ms.date: 09/01/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 10/14/2019
ms.openlocfilehash: 66a21943e19cef13aa7a3986b6a058f69cc85793
ms.sourcegitcommit: 0714ce748e20065b52f8283d5dbba7ab068978d1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/01/2020
ms.locfileid: "89285494"
---
# <a name="register-tenants-for-usage-tracking-in-azure-stack-hub"></a>Registrace tenantů pro sledování využití v centru Azure Stack

Tento článek obsahuje podrobnosti o operacích registrace. Tyto operace můžete použít k těmto akcím:

- Spravujte registrace tenantů.
- Spravujte sledování využití tenanta.

## <a name="add-tenant-to-registration"></a>Přidat tenanta k registraci

Tuto operaci můžete použít, pokud chcete přidat nového tenanta k registraci. Využití tenanta se hlásí v rámci předplatného Azure připojeného k tenantovi Azure Active Directory (Azure AD).

Tuto operaci můžete také použít ke změně předplatného přidruženého k tenantovi. Pokud chcete přepsat předchozí mapování, volejte metodu PUT nebo rutinu **New-AzureRMResource** prostředí PowerShell.

K tenantovi můžete přidružit jedno předplatné Azure. Pokud se pokusíte přidat druhé předplatné do stávajícího klienta, první předplatné se přepíše.

### <a name="use-api-profiles"></a>Použití profilů rozhraní API

Následující rutiny registrace vyžadují, abyste při spuštění prostředí PowerShell zadali profil rozhraní API. Profily rozhraní API reprezentují sadu poskytovatelů prostředků Azure a jejich verzí rozhraní API. Při interakci s více cloudy Azure vám pomůžou používat správnou verzi rozhraní API. Pokud například pracujete s více cloudy při práci s globálním centrem Azure a Azure Stack, profily rozhraní API určují název, který odpovídá datu vydání verze. Použijete profil **2017-09-03** .

Další informace o centru Azure Stack a profilech rozhraní API najdete v tématu [Správa profilů verzí rozhraní API v centru Azure Stack](../user/azure-stack-version-profiles.md).

### <a name="parameters"></a>Parametry

| parametr                  | Popis |
|---                         | --- |
| registrationSubscriptionID | Předplatné Azure, které se použilo pro prvotní registraci. |
| customerSubscriptionID     | Předplatné Azure (ne Azure Stack centrum) patřící zákazníkovi, který se má zaregistrovat Musí se vytvořit v nabídce zprostředkovatel Cloud Solution Provider (CSP) prostřednictvím partnerského centra. Pokud má zákazník více než jednoho tenanta, vytvořte si předplatné pro tenanta, abyste se přihlásili ke službě Azure Stack hub. ID předplatného zákazníka rozlišuje velká a malá písmena. |
| resourceGroup              | Skupina prostředků v Azure, ve které je uložená vaše registrace. |
| registrace           | Název registrace centra Azure Stack. Je to objekt uložený v Azure. Název je obvykle ve tvaru **azurestack-CloudID**, kde **CloudID** je ID cloudu vašeho nasazení centra Azure Stack. |

> [!NOTE]  
> Klienti musí být zaregistrovaní u každého Azure Stackho nasazení centra, které používají. Pokud klient používá více než jedno centrum Azure Stack, aktualizujte počáteční registraci každého nasazení u předplatného tenanta.

### <a name="powershell"></a>PowerShell

K přidání tenanta použijte rutinu **New-AzureRmResource** . [Připojte se k Azure](/powershell/azure/get-started-azureps)a pak na příkazovém řádku se zvýšenými oprávněními spusťte následující příkaz:

```powershell  
New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>Volání rozhraní API

**Operace**: Put  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Odpověď**: 201 vytvořeno  
**Tělo odpovědi**: prázdné  

## <a name="list-all-registered-tenants"></a>Zobrazit seznam všech registrovaných tenantů

Získá seznam všech tenantů, které byly přidány k registraci.

 > [!NOTE]  
 > Pokud nebyli registrováni žádní klienti, neobdržíte odpověď.

### <a name="parameters"></a>Parametry

| parametr                  | Popis          |
|---                         | ---                  |
| registrationSubscriptionId | Předplatné Azure, které se použilo pro prvotní registraci.   |
| resourceGroup              | Skupina prostředků v Azure, ve které je uložená vaše registrace.    |
| registrace           | Název registrace vašeho nasazení centra Azure Stack. Je to objekt uložený v Azure. Název je obvykle ve formátu **azurestack-CloudID**, kde **CloudID** je ID cloudu vašeho nasazení centra Azure Stack.   |

### <a name="powershell"></a>PowerShell

Pomocí rutiny **Get-AzureRmResource zobrazíte** seznam všech registrovaných tenantů. [Připojte se k centru Azure Stack](azure-stack-powershell-configure-admin.md)a pak na příkazovém řádku se zvýšenými oprávněními spusťte následující rutinu:

```powershell
Get-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>Volání rozhraní API

Můžete získat seznam mapování všech klientů pomocí operace GET.

**Operace**: Get  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions?api-version=2017-06-01 HTTP/1.1`  
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

Můžete odebrat klienta, který byl přidán k registraci. Pokud tento tenant stále používá prostředky v centru Azure Stack, účtují se jejich využití na základě předplatného, které se používá v prvotní registraci centra Azure Stack.

### <a name="parameters"></a>Parametry

| parametr                  | Popis          |
|---                         | ---                  |
| registrationSubscriptionId | ID předplatného pro registraci.   |
| resourceGroup              | Skupina prostředků pro registraci.   |
| registrace           | Název registrace.  |
| customerSubscriptionId     | ID předplatného zákazníka. ID předplatného zákazníka rozlišuje velká a malá písmena.  |

### <a name="powershell"></a>PowerShell

K odebrání tenanta použijte rutinu **Remove-AzureRmResource** . [Připojte se k centru Azure Stack](azure-stack-powershell-configure-admin.md)a pak na příkazovém řádku se zvýšenými oprávněními spusťte následující rutinu:

```powershell
Remove-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>Volání rozhraní API

Mapování klientů můžete odebrat pomocí operace DELETE.

**Operace**: odstranit  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Odpověď**: 204 bez obsahu  
**Tělo odpovědi**: prázdné

## <a name="next-steps"></a>Další kroky

- [Jak načíst informace o využití prostředků z centra Azure Stack](azure-stack-billing-and-chargeback.md)
