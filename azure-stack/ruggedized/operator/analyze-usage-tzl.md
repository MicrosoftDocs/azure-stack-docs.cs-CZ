---
title: Analýza využití Azure Stack a rozhraní API využití prostředků | Microsoft Docs
description: Analýza využití Azure Stack a referenčních údajů pro rozhraní API využití prostředků, které načítá informace o využití Azure Stack.
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
ms.date: 07/29/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 07/29/2020
ms.openlocfilehash: f546c1ad783ded0aa686ebdcb7e08f1427bea91e
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2020
ms.locfileid: "96939643"
---
# <a name="analyze-azure-stack-usage-with-local-usage-meters"></a>Analýza využití Azure Stack místními měřiči využití

Informace o tom, které odběry využívají prostředky uložené v místní databázi využití Správci můžou tato data načíst, abyste mohli analyzovat, kteří uživatelé spotřebovávají prostředky.

## <a name="api-call-reference"></a>Reference k volání rozhraní API

### <a name="request"></a>Žádost

Požadavek získá podrobnosti o spotřebě pro požadovaná předplatná a pro požadovaný časový rámec. Tělo žádosti není k dispozici.

Toto rozhraní API pro použití je poskytovatele API, takže volajícímu musí být v předplatném poskytovatele přiřazená role **vlastník**, **Přispěvatel** nebo **Čtenář** .

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| GET |`https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity}&subscriberId={sub1.1}&api-version=2015-06-01-preview&continuationToken={token-value}` |

### <a name="arguments"></a>Arguments

| Argument | Popis |
| --- | --- |
| `armendpoint` |Azure Resource Manager koncový bod Azure Stack prostředí. Azure Stack konvence je název koncového bodu Azure Resource Manager ve formátu `https://adminmanagement.{domain-name}` . <!-- TZLASDKFIX For example, for the Azure Stack Development Kit (ASDK), if the domain name is *local.azurestack.external*, then the Resource Manager endpoint is `https://adminmanagement.local.azurestack.external`. --> |
| `subId` |ID předplatného uživatele, který provádí volání. |
| `reportedStartTime` |Počáteční čas dotazu. Hodnota pro `DateTime` by měla být v koordinovaném světovým čase (UTC) a na začátku hodiny, například 13:00. U denní agregace nastavte tuto hodnotu na půlnoc UTC. Formát je řídicí. ISO 8601; Například, v `2015-06-16T18%3a53%3a11%2b00%3a00Z` případě, že dvojtečka je uvozena na `%3a` a znaménkem plus je uvozena tak, aby byla přičtena k `%2b` identifikátoru URI. |
| `reportedEndTime` |Koncový čas dotazu. U tohoto argumentu platí i omezení, která platí pro `reportedStartTime` . Hodnota pro `reportedEndTime` nemůže být v budoucnosti nebo aktuální datum. Pokud je, výsledek je nastaven na "zpracování není dokončeno". |
| `aggregationGranularity` |Volitelný parametr, který má dvě diskrétní možné hodnoty: **denně** a **každou hodinu**. Vzhledem k tomu, že hodnoty naznačují, vrátí jedna data v denní členitosti a druhá je hodinové řešení. Možnost **denní** je výchozí hodnota. |
| `subscriberId` |ID předplatného. Aby bylo možné získat filtrovaná data, je požadováno ID předplatného přímého tenanta poskytovatele. Pokud není zadán parametr ID předplatného, volání vrátí data o využití pro všechny přímé klienty poskytovatele. |
| `api-version` |Verze protokolu, který se používá k provedení tohoto požadavku. Tato hodnota je nastavena na `2015-06-01-preview` . |
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
|`subscriptionId` |Identifikátor předplatného Azure Stackho uživatele |
|`usageStartTime`|Čas spuštění v čase UTC sady, do které patří agregace tohoto využití.|
|`usageEndTime`|Čas ukončení UTC intervalu použití, do kterého patří agregace tohoto využití. |
|`instanceData` |Páry klíč-hodnota podrobností instance (v novém formátu):<br> `resourceUri`: Plně kvalifikované ID prostředku, které zahrnuje skupiny prostředků a název instance. <br> `location`: Oblast, ve které byla tato služba spuštěna. <br> `tags`: Značky prostředků, které jsou určeny uživatelem. <br> `additionalInfo`: Další podrobnosti o spotřebovaném prostředku; například verze operačního systému nebo typ obrázku. |
|`quantity`|Množství spotřeby prostředků, ke kterým došlo v tomto časovém rámci. |
|`meterId` |Jedinečné ID prostředku, který byl spotřebován (označovaný také jako `ResourceID` ). |

## <a name="retrieve-usage-information"></a>Načíst informace o použití

### <a name="powershell"></a>PowerShell

Pokud chcete vygenerovat data o využití, měli byste mít prostředky, na kterých běží a aktivně používají systém. Například aktivní virtuální počítač (VM) nebo účet úložiště obsahující data. Pokud si nejste jistí, jestli máte nějaké prostředky spuštěné na webu Azure Stack Marketplace, nasaďte virtuální počítač a ověřte okno monitorování virtuálního počítače, abyste se ujistili, že je spuštěný. K zobrazení dat o využití použijte následující rutiny PowerShellu:

1. [Nainstalujte PowerShell pro Azure Stack](../../operator/azure-stack-powershell-install.md).
2. [Nakonfigurujte Azure Stackho uživatele](../../user/azure-stack-powershell-configure-user.md) nebo prostředí powershellu [Azure Stack operator](../../operator/azure-stack-powershell-configure-admin.md) .
3. Pokud chcete načíst data o využití, zavolejte rutinu PowerShellu [Get-AzsSubscriberUsage](/powershell/module/azs.commerce.admin/get-azssubscriberusage) :

   ```powershell
   Get-AzsSubscriberUsage -ReportedStartTime "2017-09-06T00:00:00Z" -ReportedEndTime "2017-09-07T00:00:00Z"
   ```

### <a name="rest-api"></a>REST API

Informace o použití pro odstraněné odběry můžete shromažďovat voláním služby Microsoft. Commerce. admin.

#### <a name="return-all-tenant-usage-for-deleted-for-active-users"></a>Vrátí všechny využití tenanta pro odstraněné aktivní uživatele.

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| GET | `https://{armendpoint}/subscriptions/{subId}/providersMicrosoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={start-time}&reportedEndTime={end-endtime}&aggregationGranularity=Hourly&api-version=2015-06-01-preview` |

#### <a name="return-usage-for-deleted-or-active-tenant"></a>Vrátit využití pro odstraněné nebo aktivní tenanta

| Metoda | Identifikátor URI žádosti |
| --- | --- |
| GET |`https://{armendpoint}/subscriptions/{subId}/providersMicrosoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={start-time}&reportedEndTime={end-endtime}&aggregationGranularity=Hourly&subscriberId={subscriber-id}&api-version=2015-06-01-preview` |
