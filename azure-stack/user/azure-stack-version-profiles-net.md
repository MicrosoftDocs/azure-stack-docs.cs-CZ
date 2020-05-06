---
title: Použití profilů verzí rozhraní API s .NET v Azure Stackovém centru
description: Naučte se používat profily verzí rozhraní API s .NET SDK v Azure Stack hub.
author: sethmanheim
ms.topic: article
ms.date: 05/05/2020
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: bb13b13f48aa9ccab231e255e0dcb5547b61615a
ms.sourcegitcommit: 70c344b3c9c63f8c12867b2cdfdd1794fcc518dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/05/2020
ms.locfileid: "82835913"
---
# <a name="use-api-version-profiles-with-net-in-azure-stack-hub"></a>Použití profilů verzí rozhraní API s .NET v Azure Stackovém centru

Sada .NET SDK pro Správce prostředků centra Azure Stack poskytuje nástroje, které vám pomůžou při sestavování a správě vaší infrastruktury. Poskytovatelé prostředků v sadě SDK zahrnují výpočetní prostředky, sítě, úložiště, App Services a [Key Vault](/azure/key-vault/key-vault-whatis). Sada .NET SDK obsahuje 14 balíčků NuGet. Tyto balíčky je nutné stáhnout do řešení při každém kompilování projektu. Můžete si však stáhnout konkrétního poskytovatele prostředků, který budete používat pro **2019-03-01-Hybrid** nebo **2018-03-01-hybrids** pro optimalizaci paměti aplikace. Každý balíček obsahuje poskytovatele prostředků, příslušnou verzi rozhraní API a profil rozhraní API, ke kterému patří. Profily rozhraní API v sadě .NET SDK umožňují vývoj hybridního cloudu tím, že vám pomůžou přepínat mezi globálním prostředky a prostředky Azure v centru Azure Stack.

## <a name="net-and-api-version-profiles"></a>Profily verzí rozhraní .NET a rozhraní API

Profil rozhraní API je kombinací poskytovatelů prostředků a verzí rozhraní API. Použijte profil rozhraní API k získání nejnovější a nejvýznamnější verze každého typu prostředku v balíčku poskytovatele prostředků.

- Chcete-li používat nejnovější verze všech služeb, použijte **nejnovější** profil balíčků. Tento profil je součástí balíčku NuGet **Microsoft. Azure. Management** .

- Pokud chcete používat služby kompatibilní s Azure Stack hub, použijte jeden z následujících balíčků:
  - **Microsoft. Azure. Management. Profiles.\_Hybrid\_2019\_03 01. <*ResourceProvider*>.0.9.0-Preview. nupkg**
  - **Microsoft. Azure. Management. Profiles.\_Hybrid\_2018\_03 01. <*ResourceProvider*>.0.9.0-Preview. nupkg**

  Zajistěte, aby se část **ResourceProvider** výše uvedeného balíčku NuGet změnila na správného poskytovatele.

- Chcete-li použít nejnovější verzi rozhraní API služby, použijte **nejnovější** profil konkrétního balíčku NuGet. Pokud například chcete použít **nejnovější verzi rozhraní COMPUTE API** služby COMPUTE, použijte **nejnovější** profil balíčku **COMPUTE** . **Nejnovější** profil je součástí balíčku NuGet **Microsoft. Azure. Management** .

- Pokud chcete použít konkrétní verze rozhraní API pro typ prostředku v konkrétním poskytovateli prostředků, použijte konkrétní verze rozhraní API definované v balíčku.

Můžete zkombinovat všechny možnosti ve stejné aplikaci.

## <a name="install-the-azure-net-sdk"></a>Instalace sady Azure .NET SDK

- Nainstalujte Git. Pokyny najdete v tématu [Začínáme – instalace Gitu][].

- Chcete-li nainstalovat správné balíčky NuGet, přečtěte si téma [vyhledání a instalace balíčku][].

- Balíčky, které je potřeba nainstalovat, závisí na verzi profilu, kterou chcete použít. Názvy balíčků pro verze profilu jsou:

  - **Microsoft. Azure. Management. Profiles.\_Hybrid\_2019\_03 01. <*ResourceProvider*>.0.9.0-Preview. nupkg**

  - **Microsoft. Azure. Management. Profiles.\_Hybrid\_2018\_03 01. <*ResourceProvider*>.0.9.0-Preview. nupkg**

- Chcete-li nainstalovat správné balíčky NuGet pro Visual Studio Code, přečtěte si následující odkaz a Stáhněte si [pokyny správce balíčků NuGet][].

- Pokud není k dispozici, vytvořte odběr a uložte ID předplatného, které chcete použít později. Informace o tom, jak vytvořit předplatné, najdete [v tématu Vytvoření předplatných nabídek v centru Azure Stack][].

- Vytvořte instanční objekt a uložte ID klienta a tajný klíč klienta. Informace o tom, jak vytvořit instanční objekt pro centrum Azure Stack, najdete v tématu [poskytnutí přístupu aplikací k centru Azure Stack][]. ID klienta se také označuje jako ID aplikace při vytváření instančního objektu.

- Ujistěte se, že váš instanční objekt má v předplatném roli Přispěvatel/vlastník. Informace o tom, jak přiřadit roli k instančnímu objektu, najdete v tématu [poskytnutí přístupu aplikací k rozbočovači služby Azure Stack][].

## <a name="prerequisites"></a>Požadavky

Pokud chcete použít sadu .NET Azure SDK s Azure Stack hub, musíte zadat následující hodnoty a pak hodnoty nastavit pomocí proměnných prostředí. Chcete-li nastavit proměnné prostředí, přečtěte si pokyny uvedené v tabulce pro konkrétní operační systém.

| Hodnota                     | Proměnné prostředí   | Popis                                                                                                             |
|---------------------------|-------------------------|-------------------------------------------------------------------------------------------------------------------------|
| ID tenanta                 | `AZURE_TENANT_ID `      | Hodnota [*ID tenanta*][]centra Azure Stack.                                                                          |
| ID klienta                 | `AZURE_CLIENT_ID `      | ID aplikace instančního objektu se uložilo při vytvoření instančního objektu v předchozí části tohoto článku. |
| ID předplatného           | `AZURE_SUBSCRIPTION_ID` | [*ID předplatného*][] je způsob, jakým přistupujete k nabídkám centra Azure Stack.                                                      |
| Tajný klíč klienta             | `AZURE_CLIENT_SECRET`   | Tajný kód aplikace instančního objektu se uložil při vytvoření objektu služby.                                      |
| Správce prostředků koncový bod | `ARM_ENDPOINT`          | Viz [*koncový bod správce prostředků centra Azure Stack*][].                                                                    |
| Umístění                  | `RESOURCE_LOCATION`     | Umístění centra Azure Stack.

Pokud chcete najít ID tenanta pro centrum Azure Stack, postupujte podle pokynů [v tomto článku](../operator/azure-stack-csp-ref-operations.md). Chcete-li nastavit proměnné prostředí, postupujte následovně:

### <a name="windows"></a>Windows

Chcete-li nastavit proměnné prostředí v příkazovém řádku systému Windows, použijte následující formát:

```shell
set Azure_Tenant_ID=Your_Tenant_ID
```

### <a name="macos-linux-and-unix-based-systems"></a>Systémy MacOS, Linux a UNIX

V systémech UNIX použijte následující příkaz:

```shell
export Azure_Tenant_ID=Your_Tenant_ID
```

### <a name="the-azure-stack-hub-resource-manager-endpoint"></a>Koncový bod Správce prostředků centra Azure Stack

Azure Resource Manager je rozhraní pro správu, které správcům umožňuje nasazovat, spravovat a monitorovat prostředky Azure. Azure Resource Manager může tyto úlohy v jedné operaci zpracovat jako skupinu, nikoli jednotlivě.

Informace o metadatech můžete získat z Správce prostředkůho koncového bodu. Koncový bod vrátí soubor JSON s informacemi potřebnými ke spuštění vašeho kódu.

Vezměte na vědomí následující skutečnosti:

- **ResourceManagerUrl** v Azure Stack Development Kit (ASDK) je: https://management.local.azurestack.external/.

- **ResourceManagerUrl** v integrovaných systémech je: `https://management.region.<fqdn>/`, kde `<fqdn>` je váš plně kvalifikovaný název domény.
Načtení požadovaných metadat: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`.

Ukázkový soubor JSON:

```json
{
   "galleryEndpoint": "https://portal.local.azurestack.external:30015/",
   "graphEndpoint": "https://graph.windows.net/",
   "portal Endpoint": "https://portal.local.azurestack.external/",
   "authentication": 
      {
         "loginEndpoint": "https://login.windows.net/",
         "audiences": ["https://management.yourtenant.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
      }
}
```

## <a name="existing-api-profiles"></a>Existující profily rozhraní API

- **Microsoft. Azure. Management. Profiles.\_Hybrid\_2019\_03 01. *<ResourceProvider*>.0.9.0-preview. nupkg**: nejnovější profil sestavený pro Azure Stack hub. Tento profil se používá pro služby, které jsou nejvíce kompatibilní s Azure Stack hub, pokud používáte verzi 1904 nebo novější.

- **Microsoft. Azure. Management. Profiles.\_Hybrid\_2018\_03 01. *<ResourceProvider*>.0.9.0-Preview. nupkg**: použijte tento profil pro služby, aby byly kompatibilní s Azure Stack hub pro verze 1808 nebo novější.

- **Nejnovější**: Profil skládající se z nejnovějších verzí všech služeb. Použijte nejnovější verze všech služeb. Tento profil je součástí balíčku NuGet **Microsoft. Azure. Management** .

Další informace o centru Azure Stack a profilech rozhraní API najdete v tématu [Přehled profilů rozhraní API][].

## <a name="azure-net-sdk-api-profile-usage"></a>Použití profilu rozhraní API pro Azure .NET SDK

Pomocí následujícího kódu vytvořte instanci klienta správy prostředků. Podobný kód lze použít k vytvoření instance jiných klientů poskytovatele prostředků (například výpočetní prostředky, sítě a úložiště).

```csharp
var client = new ResourceManagementClient(armEndpoint, credentials)
{
    SubscriptionId = subscriptionId;
};
```

Aby `credentials` bylo možné vytvořit instanci klienta, je nutné zadat parametr v tomto kódu. Následující kód vygeneruje ověřovací token podle ID tenanta a instančního objektu:

```csharp
var azureStackSettings = getActiveDirectoryServiceSettings(armEndpoint);
var credentials = ApplicationTokenProvider.LoginSilentAsync(tenantId, servicePrincipalId, servicePrincipalSecret, azureStackSettings).GetAwaiter().GetResult();
```

`getActiveDirectoryServiceSettings` Volání v kódu načte z koncového bodu metadat koncové body centra Azure Stack. Uvádí proměnné prostředí z vytvořeného volání:

```csharp
public static ActiveDirectoryServiceSettings getActiveDirectoryServiceSettings(string armEndpoint)
{
    var settings = new ActiveDirectoryServiceSettings();
    try
    {
        var request = (HttpWebRequest)HttpWebRequest.Create(string.Format("{0}/metadata/endpoints?api-version=1.0", armEndpoint));
        request.Method = "GET";
        request.UserAgent = ComponentName;
        request.Accept = "application/xml";
        using (HttpWebResponse response = (HttpWebResponse)request.GetResponse())
        {
            using (StreamReader sr = new StreamReader(response.GetResponseStream()))
            {
                var rawResponse = sr.ReadToEnd();
                var deserialized = JObject.Parse(rawResponse);
                var authenticationObj = deserialized.GetValue("authentication").Value<JObject>();
                var loginEndpoint = authenticationObj.GetValue("loginEndpoint").Value<string>();
                var audiencesObj = authenticationObj.GetValue("audiences").Value<JArray>();
                settings.AuthenticationEndpoint = new Uri(loginEndpoint);
                settings.TokenAudience = new Uri(audiencesObj[0].Value<string>());
                settings.ValidateAuthority = loginEndpoint.TrimEnd('/').EndsWith("/adfs", StringComparison.OrdinalIgnoreCase) ? false : true;
            }
        }
    }
    catch (Exception ex)
    {
        Console.WriteLine(String.Format("Could not get AD service settings. Exception: {0}", ex.Message));
    }
    return settings;
}
```

Tyto kroky umožňují použít balíčky NuGet profilu rozhraní API k úspěšnému nasazení vaší aplikace do centra Azure Stack.

## <a name="samples-using-api-profiles"></a>Ukázky pomocí profilů rozhraní API

Následující ukázky můžete použít jako referenci pro vytváření řešení s využitím profilů rozhraní API .NET a Azure Stack hub:

- [Správa skupin prostředků](https://github.com/Azure-Samples/hybrid-resources-dotnet-manage-resource-group)
- [Správa účtů úložiště](https://github.com/Azure-Samples/hybird-storage-dotnet-manage-storage-accounts)
- [Správa virtuálního počítače](https://github.com/Azure-Samples/hybrid-compute-dotnet-manage-vm): v této ukázce se používá **hybridní profil 2019-03-01** podporovaný centrem Azure Stack.

## <a name="next-steps"></a>Další kroky

Další informace o profilech rozhraní API:

- [Správa profilů verzí rozhraní API v centru Azure Stack](azure-stack-version-profiles.md)
- [Verze rozhraní API poskytovatele prostředků podporované profily](azure-stack-profiles-azure-resource-manager-versions.md)

  [Začínáme – instalace Gitu]: https://git-scm.com/book/en/v2/Getting-Started-Installing-Git
  [Hledání a instalace balíčku]: /nuget/tools/package-manager-ui
  [Pokyny pro správce balíčků NuGet]: https://marketplace.visualstudio.com/items?itemName=jmrog.vscode-nuget-package-manager
  [Vytváření předplatných nabídek v centru Azure Stack]: ../operator/azure-stack-subscribe-plan-provision-vm.md
  [Poskytnutí přístupu k aplikacím Azure Stack hub]: ../operator/azure-stack-create-service-principals.md
  [* ID tenanta *]: ../operator/azure-stack-identity-overview.md
  [* ID předplatného *]: ../operator/service-plan-offer-subscription-overview.md#subscriptions
  [* Azure Stack centra Správce prostředků koncový bod *]: ../user/azure-stack-version-profiles-ruby.md#the-azure-stack-hub-resource-manager-endpoint
  [Shrnutí profilů rozhraní API]: ../user/azure-stack-version-profiles.md#summary-of-api-profiles
  [Test Project to Virtual Machine, vNet, resource groups, and storage account]: https://github.com/seyadava/azure-sdk-for-net-samples/tree/master/TestProject
  [Use Azure PowerShell to create a service principal with a certificate]: ../operator/azure-stack-create-service-principals.md
  [Run unit tests with Test Explorer.]: /visualstudio/test/run-unit-tests-with-test-explorer?view=vs-2017
