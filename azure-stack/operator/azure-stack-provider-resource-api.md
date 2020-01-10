---
title: Rozhraní API pro využití prostředků poskytovatele centra Azure Stack | Microsoft Docs
description: Referenční informace k rozhraní API pro využití prostředků, které načítá informace o využití centra Azure Stack.
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
ms.date: 01/07/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 01/25/2018
ms.openlocfilehash: f7d39ce1c2a33a6a693a56a6c5c86b7e1275993d
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75810065"
---
# <a name="provider-resource-usage-api"></a>Rozhraní API využití prostředků poskytovatele

Pojem *zprostředkovatel* se vztahuje na správce služeb a na všechny delegované zprostředkovatele. Operátory centra Azure Stack a delegovaní zprostředkovatelé můžou používat rozhraní API Využití poskytovatele k zobrazení využití jejich přímých klientů. Například, jak je znázorněno v následujícím diagramu, P0 může zavolat rozhraní API zprostředkovatele, aby získal informace o přímých použitích P1 a P2 a P1 může volat informace o použití na P3 a P4.

![Koncepční model hierarchie zprostředkovatele](media/azure-stack-provider-resource-api/image1.png)

## <a name="api-call-reference"></a>Reference k volání rozhraní API

### <a name="request"></a>Žádost

Požadavek získá podrobnosti o spotřebě pro požadovaná předplatná a pro požadovaný časový rámec. Tělo žádosti není k dispozici.

Toto rozhraní API pro použití je poskytovatele API, takže volajícímu musí být v předplatném poskytovatele přiřazená role **vlastník**, **Přispěvatel**nebo **Čtenář** .

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| GET |`https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity}&subscriberId={sub1.1}&api-version=2015-06-01-preview&continuationToken={token-value}` |

### <a name="arguments"></a>Argumenty

| Argument | Popis |
| --- | --- |
| `armendpoint` |Azure Resource Manager koncový bod vašeho prostředí centra Azure Stack. Azure Stack konvence centra je název koncového bodu Azure Resource Manager ve formátu `https://adminmanagement.{domain-name}`. Například pro Azure Stack Development Kit (ASDK), pokud je název domény *Local. azurestack. external*, pak je `https://adminmanagement.local.azurestack.external`koncový bod správce prostředků. |
| `subId` |ID předplatného uživatele, který provádí volání. |
| `reportedStartTime` |Počáteční čas dotazu. Hodnota `DateTime` by měla být v koordinovaném univerzálním čase (UTC) a na začátku hodiny. například 13:00. U denní agregace nastavte tuto hodnotu na půlnoc UTC. Formát je řídicí. ISO 8601; například `2015-06-16T18%3a53%3a11%2b00%3a00Z`, kde je dvojtečka uvozena `%3a` a znak plus je uvozen na `%2b`, aby byl popisný identifikátor URI. |
| `reportedEndTime` |Koncový čas dotazu. Omezení, která platí pro `reportedStartTime`, se vztahují i na tento argument. Hodnota pro `reportedEndTime` nemůže být v budoucnosti nebo v aktuálním datu. Pokud je, výsledek je nastaven na "zpracování není dokončeno". |
| `aggregationGranularity` |Volitelný parametr, který má dvě diskrétní možné hodnoty: **denně** a **každou hodinu**. Vzhledem k tomu, že hodnoty naznačují, vrátí jedna data v denní členitosti a druhá je hodinové řešení. Možnost **denní** je výchozí hodnota. |
| `subscriberId` |ID předplatného. Aby bylo možné získat filtrovaná data, je požadováno ID předplatného přímého tenanta poskytovatele. Pokud není zadán parametr ID předplatného, volání vrátí data o využití pro všechny přímé klienty poskytovatele. |
| `api-version` |Verze protokolu, který se používá k provedení tohoto požadavku. Tato hodnota je nastavená na `2015-06-01-preview`. |
| `continuationToken` |Token načtený z posledního volání poskytovatele rozhraní API využití Tento token je nutný, pokud je odpověď větší než 1 000 řádků. Slouží jako záložka pro průběh. Pokud token není k dispozici, data se načítají od začátku dne nebo hodiny na základě předané členitosti. |

### <a name="response"></a>Odpověď

```http
GET
/subscriptions/sub1/providers/Microsoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00&reportedEndTime=2015-06-01T00%3a00%3a00%2b00%3a00&aggregationGranularity=Daily&subscriberId=sub1.1&api-version=1.0
```

```json
{
"value": [
{

"id":
"/subscriptions/sub1.1/providers/Microsoft.Commerce.Admin/UsageAggregate/sub1.1-

meterID1",
"name": "sub1.1-meterID1",
"type": "Microsoft.Commerce.Admin/UsageAggregate",

"properties": {
"subscriptionId":"sub1.1",
"usageStartTime": "2015-03-03T00:00:00+00:00",
"usageEndTime": "2015-03-04T00:00:00+00:00",
"instanceData":"{\"Microsoft.Resources\":{\"resourceUri\":\"resourceUri1\",\"location\":\"Alaska\",\"tags\":null,\"additionalInfo\":null}}",
"quantity":2.4000000000,
"meterId":"meterID1"

}
},

. . .
```

### <a name="response-details"></a>Podrobnosti odpovědi

| Argument | Popis |
| --- | --- |
|`id` |Jedinečné ID agregace využití |
|`name` |Název agregace použití |
|`type` |Definice prostředků. |
|`subscriptionId` |Identifikátor předplatného uživatele centra Azure Stack. |
|`usageStartTime`|Čas spuštění v čase UTC sady, do které patří agregace tohoto využití.|
|`usageEndTime`|Čas ukončení UTC intervalu použití, do kterého patří agregace tohoto využití. |
|`instanceData` |Páry klíč-hodnota podrobností instance (v novém formátu):<br> `resourceUri`: plně kvalifikované ID prostředku, které zahrnuje skupiny prostředků a název instance. <br> `location`: oblast, ve které byla tato služba spuštěna. <br> `tags`: značky prostředků, které jsou určeny uživatelem. <br> `additionalInfo`: Další podrobnosti o spotřebovaném prostředku; například verze operačního systému nebo typ obrázku. |
|`quantity`|Množství spotřeby prostředků, ke kterým došlo v tomto časovém rámci. |
|`meterId` |Jedinečné ID spotřebovaného prostředku (označuje se také jako `ResourceID`). |

## <a name="retrieve-usage-information"></a>Načíst informace o použití

### <a name="powershell"></a>PowerShell

Pokud chcete vygenerovat data o využití, měli byste mít prostředky, na kterých běží a aktivně používají systém. Například aktivní virtuální počítač (VM) nebo účet úložiště obsahující data. Pokud si nejste jistí, jestli máte nějaké prostředky spuštěné v tržišti centra Azure Stack, nasaďte virtuální počítač a ověřte okno monitorování virtuálního počítače, abyste se ujistili, že je spuštěný. K zobrazení dat o využití použijte následující rutiny PowerShellu:

1. [Nainstalujte PowerShell pro centrum Azure Stack](azure-stack-powershell-install.md).
2. [Nakonfigurujte Azure Stackho uživatele centra](../user/azure-stack-powershell-configure-user.md) nebo prostředí PowerShell pro [operátor centra Azure Stack](azure-stack-powershell-configure-admin.md) .
3. Pokud chcete načíst data o využití, zavolejte rutinu PowerShellu [Get-AzsSubscriberUsage](/powershell/module/azs.commerce.admin/get-azssubscriberusage) :

   ```powershell
   Get-AzsSubscriberUsage -ReportedStartTime "2017-09-06T00:00:00Z" -ReportedEndTime "2017-09-07T00:00:00Z"
   ```

### <a name="rest-api"></a>Rozhraní REST API

Informace o použití pro odstraněné odběry můžete shromažďovat voláním služby **Microsoft. Commerce. admin** .

#### <a name="return-all-tenant-usage-for-deleted-for-active-users"></a>Vrátí všechny využití tenanta pro odstraněné aktivní uživatele.

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| GET | `https://{armendpoint}/subscriptions/{subId}/providersMicrosoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={start-time}&reportedEndTime={end-endtime}&aggregationGranularity=Hourly&api-version=2015-06-01-preview` |

#### <a name="return-usage-for-deleted-or-active-tenant"></a>Vrátit využití pro odstraněné nebo aktivní tenanta

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| GET |`https://{armendpoint}/subscriptions/{subId}/providersMicrosoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={start-time}&reportedEndTime={end-endtime}&aggregationGranularity=Hourly&subscriberId={subscriber-id}&api-version=2015-06-01-preview` |

## <a name="next-steps"></a>Další kroky

- [Referenční informace k rozhraní API využití prostředků tenanta](azure-stack-tenant-resource-usage-api.md)
- [Nejčastější dotazy týkající se použití](azure-stack-usage-related-faq.md)
