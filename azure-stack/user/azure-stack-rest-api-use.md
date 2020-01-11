---
title: Vytvoření požadavků rozhraní API na centrum Azure Stack | Microsoft Docs
description: Naučte se, jak načíst ověřování z Azure a vydávat požadavky rozhraní API na Azure Stack hub.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: sethm
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 3defbf731a2b4b89fce8f9815513456104f80ea6
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75883265"
---
<!--  cblackuk and charliejllewellyn. This is a community contribution by cblackuk-->

# <a name="make-api-requests-to-azure-stack-hub"></a>Vydávat požadavky rozhraní API na centrum Azure Stack

Rozhraní REST API služby Azure Stack Hub můžete použít k automatizaci operací, jako je přidání virtuálního počítače do cloudu centra Azure Stack.

Rozhraní API vyžadují, aby se váš klient ověřil na Microsoft Azure koncový bod přihlášení. Koncový bod vrátí token, který se použije v hlavičce všech požadavků odeslaných do rozhraní API centra Azure Stack. Microsoft Azure používá OAuth 2,0.

Tento článek popisuje příklady, které používají **Nástroj pro** vytváření Azure Stackch požadavků centra. kudrlinkou je nástroj příkazového řádku s knihovnou pro přenos dat. Tyto příklady vás provedou procesem Načtení tokenu pro přístup k rozhraním API centra Azure Stack. Většina programovacích jazyků poskytuje knihovny OAuth 2,0, které mají robustní správu tokenů a zpracovávají úlohy, jako je například aktualizace tokenu.

Projděte si celý proces použití rozhraní REST API centra Azure Stack s obecným klientem REST, jako je například **kudrlinkou**, který vám pomůže pochopit základní požadavky a co můžete očekávat v datové části odpovědi.

Tento článek nezkoumá všechny možnosti, které jsou k dispozici pro načítání tokenů, například interaktivní přihlašování nebo vytváření ID vyhrazených aplikací. Informace o těchto tématech najdete v referenčních informacích k [Azure REST API](/rest/api/).

## <a name="get-a-token-from-azure"></a>Získání tokenu z Azure

Vytvořte text požadavku formátovaný pomocí typu obsahu `x-www-form-urlencoded` k získání přístupového tokenu. ODEŠLEte požadavek do koncového bodu ověřování Azure REST a přihlášení.

### <a name="uri"></a>Identifikátor URI

```bash  
POST https://login.microsoftonline.com/{tenant id}/oauth2/token
```

**ID tenanta** je buď:

- Vaše doména tenanta, například `fabrikam.onmicrosoft.com`
- Vaše ID tenanta, například `8eaed023-2b34-4da1-9baa-8bc8c9d6a491`
- Výchozí hodnota pro klíče nezávislé na tenantovi: `common`

### <a name="post-body"></a>Tělo příspěvku

```bash  
grant_type=password
&client_id=1950a258-227b-4e31-a9cf-717495945fc2
&resource=https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155
&username=admin@fabrikam.onmicrosoft.com
&password=Password123
&scope=openid
```

Pro každou hodnotu:

- **grant_type**:  
   Typ schématu ověřování, který budete používat. V tomto příkladu je hodnota `password`.

- **prostředek**:  
   Prostředek, ke kterému token přistupuje. Prostředek můžete najít dotazem na koncový bod metadat správy centra Azure Stack. Podívejte se na část **cílové skupiny** .

- **Koncový bod správy centra Azure Stack**:

   ```bash
   https://management.{region}.{Azure Stack Hub domain}/metadata/endpoints?api-version=2015-01-01
   ```

  > [!NOTE]  
  > Pokud jste správce, který se pokouší získat přístup k rozhraní API tenanta, ujistěte se, že používáte koncový bod tenanta. například `https://adminmanagement.{region}.{Azure Stack Hub domain}/metadata/endpoints?api-version=2015-01-011`.

  Například s Azure Stack Development Kit jako koncovým bodem:

   ```bash
   curl 'https://management.local.azurestack.external/metadata/endpoints?api-version=2015-01-01'
   ```

  Odpověď:

  ```bash
  {
  "galleryEndpoint":"https://adminportal.local.azurestack.external:30015/",
  "graphEndpoint":"https://graph.windows.net/",
  "portalEndpoint":"https://adminportal.local.azurestack.external/",
  "authentication":{
     "loginEndpoint":"https://login.windows.net/",
     "audiences":["https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155"]
     }
  }
  ```

### <a name="example"></a>Příklad:

  ```bash
  https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155
  ```

- **client_id**

  Tato hodnota se pevně zakódované na výchozí hodnotu:

  ```bash
  1950a258-227b-4e31-a9cf-717495945fc2
  ```

  K dispozici jsou alternativní možnosti pro konkrétní scénáře:

  | Aplikace | ApplicationID |
  | --------------------------------------- |:-------------------------------------------------------------:|
  | LegacyPowerShell | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417 |
  | PowerShell | 1950a258-227b-4e31-a9cf-717495945fc2 |
  | WindowsAzureActiveDirectory | 00000002-0000-0000-c000-000000000000 |
  | VisualStudio | 872cd9fa-d31f-45e0-9eab-6e460a02d1f1 |
  | AzureCLI | 04b07795-8ddb-461a-bbee-02f9e1bf7b46 |

- **uživatelské jméno**

  Například účet služby Azure AD Azure Stack hub:

  ```bash
  azurestackadmin@fabrikam.onmicrosoft.com
  ```

- **Heslo**

  Azure Stack centrum pro správu Azure AD.

### <a name="example"></a>Příklad:

Požadavek:

```bash
curl -X "POST" "https://login.windows.net/fabrikam.onmicrosoft.com/oauth2/token" \
-H "Content-Type: application/x-www-form-urlencoded" \
--data-urlencode "client_id=1950a258-227b-4e31-a9cf-717495945fc2" \
--data-urlencode "grant_type=password" \
--data-urlencode "username=admin@fabrikam.onmicrosoft.com" \
--data-urlencode 'password=Password12345' \
--data-urlencode "resource=https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155"
```

Odpověď:

```bash
{
  "token_type": "Bearer",
  "scope": "user_impersonation",
  "expires_in": "3599",
  "ext_expires_in": "0",
  "expires_on": "1512574780",
  "not_before": "1512570880",
  "resource": "https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155",
  "access_token": "eyJ0eXAiOi...truncated for readability..."
}
```

## <a name="api-queries"></a>Dotazy rozhraní API

Po získání přístupového tokenu ho přidejte jako hlavičku do každého z vašich požadavků na rozhraní API. Pokud ho chcete přidat jako hlavičku, vytvořte **autorizační** hlavičku s hodnotou: `Bearer <access token>`. Příklad:

Požadavek:

```bash  
curl -H "Authorization: Bearer eyJ0eXAiOi...truncated for readability..." 'https://adminmanagement.local.azurestack.external/subscriptions?api-version=2016-05-01'
```

Odpověď:

```bash  
offerId : /delegatedProviders/default/offers/92F30E5D-F163-4C58-8F02-F31CFE66C21B
id : /subscriptions/800c4168-3eb1-406b-a4ca-919fe7ee42e8
subscriptionId : 800c4168-3eb1-406b-a4ca-919fe7ee42e8
tenantId : 9fea4606-7c07-4518-9f3f-8de9c52ab628
displayName : Default Provider Subscription
state : Enabled
subscriptionPolicies : @{locationPlacementId=AzureStack}
```

### <a name="url-structure-and-query-syntax"></a>Struktura adres URL a syntaxe dotazů

Identifikátor URI obecné žádosti se skládá z těchto: `{URI-scheme} :// {URI-host} / {resource-path} ? {query-string}`

- **Schéma identifikátoru URI**:  
Identifikátor URI označuje protokol použitý k odeslání požadavku. Například `http` nebo `https`.
- **Hostitel identifikátoru URI**:  
Hostitel Určuje název domény nebo IP adresu serveru, na kterém je hostovaný koncový bod služby REST, například `graph.microsoft.com` nebo `adminmanagement.local.azurestack.external`.
- **Cesta prostředku**:  
Cesta Určuje prostředek nebo kolekci prostředků, které mohou zahrnovat více segmentů používaných službou při určování výběru těchto prostředků. Například: `beta/applications/00003f25-7e1f-4278-9488-efc7bac53c4a/owners` lze použít k dotazování seznamu na konkrétní vlastníky aplikace v rámci kolekce aplikací.
- **Řetězec dotazu**:  
Řetězec poskytuje další jednoduché parametry, jako je například verze rozhraní API nebo kritéria výběru prostředku.

## <a name="azure-stack-hub-request-uri-construct"></a>Konstrukce identifikátoru URI žádosti centra Azure Stack

```bash
{URI-scheme} :// {URI-host} / {subscription id} / {resource group} / {provider} / {resource-path} ? {OPTIONAL: filter-expression} {MANDATORY: api-version}
```

### <a name="uri-syntax"></a>Syntaxe identifikátoru URI

```bash
https://adminmanagement.local.azurestack.external/{subscription id}/resourcegroups/{resource group}/providers/{provider}/{resource-path}?{api-version}
```

### <a name="query-uri-example"></a>Příklad identifikátoru URI dotazu

```bash
https://adminmanagement.local.azurestack.external/subscriptions/800c4168-3eb1-406b-a4ca-919fe7ee42e8/resourcegroups/system.local/providers/microsoft.infrastructureinsights.admin/regionhealths/local/Alerts?$filter=(Properties/State eq 'Active') and (Properties/Severity eq 'Critical')&$orderby=Properties/CreatedTimestamp desc&api-version=2016-05-01"
```

## <a name="next-steps"></a>Další kroky

Další informace o používání koncových bodů Azure REST najdete v referenčních informacích k [azure REST API](/rest/api/).
