---
title: Reference k rozhraní API využití prostředků tenanta
titleSuffix: Azure Stack
description: Referenční informace o rozhraních API pro využití prostředků, které načítají informace o využití centra Azure Stack.
author: sethmanheim
ms.topic: article
ms.date: 08/25/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: d6e31a08badcaa171a2a6c30171ec8f2573e4b76
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94545529"
---
# <a name="tenant-resource-usage-api-reference"></a>Referenční informace k rozhraní API využití prostředků tenanta

Tenant může pomocí rozhraní API tenanta zobrazit data o využití prostředků v tenantovi. Tato rozhraní API jsou konzistentní s rozhraními API využití Azure.

Pomocí rutiny [Get-UsageAggregates](/powershell/module/Az.usageaggregates/get-usageaggregates) prostředí Windows PowerShell můžete získat data o využití, stejně jako v Azure.

## <a name="api-call"></a>Volání rozhraní API

### <a name="request"></a>Žádost

Požadavek získá podrobnosti o spotřebě pro požadovaná předplatná a pro požadovaný časový rámec. Tělo žádosti není k dispozici.

| **Metoda** | **Identifikátor URI žádosti** |
| --- | --- |
| GET |https://{armendpoint}/Subscriptions/{subId}/Providers/Microsoft. obchod/usageAggregates? reportedStartTime = {reportedStartTime} &reportedEndTime = {reportedEndTime} &aggregationGranularity = {členitost} &API-Version = 2015-06 -01-Preview&token continuationtoken = {token-Value} |

### <a name="parameters"></a>Parametry

| **Parametr** | **Popis** |
| --- | --- |
| Armendpoint |Azure Resource Manager koncový bod vašeho prostředí centra Azure Stack. Azure Stack konvence centra je název Azure Resource Manager koncového bodu ve formátu `https://management.{domain-name}` . Například pro vývojovou sadu je název domény Local. azurestack. external a pak je koncový bod Správce prostředků `https://management.local.azurestack.external` . |
| subId |ID předplatného uživatele, který provádí volání. Toto rozhraní API můžete použít jenom k dotazování na použití jednoho předplatného. Poskytovatelé můžou použít rozhraní API využití prostředků poskytovatele k dotazování na využití pro všechny klienty. |
| reportedStartTime |Počáteční čas dotazu. Hodnota pro *DateTime* by měla být v UTC a na začátku hodiny; například 13:00. U denní agregace nastavte tuto hodnotu na půlnoc UTC. Formát je řídicí. ISO 8601; Příklad **: 2015-06-16T18% 3A53% 3a11% 2b00% 3a00Z** , kde dvojtečka je uvozená na% 3a a plus je uvozena na% 2b, aby bylo uživatelsky přívětivé URI. |
| reportedEndTime |Koncový čas dotazu. Omezení vztahující se na **reportedStartTime** platí také pro tento parametr. Hodnota pro **reportedEndTime** nemůže být v budoucnu. |
| aggregationGranularity |Volitelný parametr, který má dvě diskrétní možné hodnoty: **denně** a **každou hodinu**. Vzhledem k tomu, že hodnoty naznačují, vrátí jedna data v denní členitosti a druhá je hodinové řešení. Možnost **denní** je výchozí hodnota. |
| verze-api |Verze protokolu, který se používá k provedení tohoto požadavku. Je nutné použít **verzi 2015-06-01-Preview**. |
| Token continuationtoken |Token načtený z posledního volání poskytovatele rozhraní API využití Tento token je nutný, pokud je odpověď větší než 1 000 řádků. Funguje jako záložka pro průběh. Pokud není k dispozici, data se načítají od začátku dne nebo hodiny na základě předané členitosti. |

### <a name="response"></a>Odpověď

```html
GET
/subscriptions/sub1/providers/Microsoft.Commerce/UsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00&reportedEndTime=2015-06-01T00%3a00%3a00%2b00%3a00&aggregationGranularity=Daily&api-version=1.0
```

```json
{
"value": [
{

"id":
"/subscriptions/sub1/providers/Microsoft.Commerce/UsageAggregate/sub1-meterID1",
"name": "sub1-meterID1",
"type": "Microsoft.Commerce/UsageAggregate",

"properties": {
"subscriptionId":"sub1",
"usageStartTime": "2015-03-03T00:00:00+00:00",
"usageEndTime": "2015-03-04T00:00:00+00:00",
"instanceData":"{\"Microsoft.Resources\":{\"resourceUri\":\"resourceUri1\",\"location\":\"Alaska\",\"tags\":null,\"additionalInfo\":null}}",
"quantity":2.4000000000,
"meterId":"meterID1"

}
},

...
```

### <a name="response-details"></a>Podrobnosti odpovědi

| **Parametr** | **Popis** |
| --- | --- |
| id |Jedinečné ID agregace využití |
| name |Název agregace použití |
| typ |Definice prostředků. |
| subscriptionId |Identifikátor předplatného uživatele Azure |
| usageStartTime |Čas spuštění v čase UTC sady, do které patří agregace tohoto využití. |
| usageEndTime |Čas ukončení UTC intervalu použití, do kterého patří agregace tohoto využití. |
| instanceData |Páry klíč-hodnota podrobností instance (v novém formátu):<br>  *resourceUri* : plně kvalifikované ID prostředku, včetně skupin prostředků a názvu instance. <br>  *umístění* : oblast, ve které byla tato služba spuštěna. <br>  *značky* : značky prostředků, které uživatel zadá. <br>  *AdditionalInfo* : Další podrobnosti o spotřebovaném prostředku. Například verze operačního systému nebo typ bitové kopie. |
| quantity |Množství spotřeby prostředků, ke kterým došlo v tomto časovém rámci. |
| meterId |Jedinečné ID prostředku, který byl spotřebován (označovaný také jako **ResourceID** ). |

## <a name="next-steps"></a>Další kroky

- [Rozhraní API využití prostředků poskytovatele](azure-stack-provider-resource-api.md)
- [Nejčastější dotazy týkající se použití](azure-stack-usage-related-faq.md)
