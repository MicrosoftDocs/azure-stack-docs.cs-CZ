---
title: Použití profilů verzí rozhraní API s rozhraním .NET v Azure Stack | Microsoft Docs
description: Naučte se používat profily verzí rozhraní API s .NET SDK v Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: af1f1351a5659447fb0f9e7ac9782fb74208e038
ms.sourcegitcommit: ebb5afb7968d3e6531ecfcde7f4bb5e4420caf02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2019
ms.locfileid: "70302379"
---
# <a name="use-api-version-profiles-with-net-in-azure-stack"></a>Použití profilů verzí API s využitím .NET v Azure stacku

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Sady .NET SDK pro Azure Resource Manageru zásobníku poskytuje nástroje, které vám pomůžou vytvářet a spravovat infrastrukturu. Poskytovatelé prostředků v sadě SDK zahrnují výpočetní prostředky, sítě, úložiště, App Services a [Key Vault](/azure/key-vault/key-vault-whatis). Sada .NET SDK obsahuje 14 balíčků NuGet. Tyto balíčky je nutné stáhnout do řešení při každém kompilování projektu. Můžete si však stáhnout konkrétního poskytovatele prostředků, kterého budete používat pro 2019-03-01-hybrid nebo 2018-03-01-Hybrid pro optimalizaci paměti aplikace. Každý balíček se skládá z poskytovatele prostředků, příslušné verze rozhraní API a rozhraní API profil, ke kterému patří. Profily rozhraní API v sadě .NET SDK umožňují vývoj pro hybridní cloudové prostředí tím, že můžete přepínat mezi globální prostředky Azure a prostředky ve službě Azure Stack pomáhá.

## <a name="net-and-api-version-profiles"></a>Profilů verzí rozhraní .NET a rozhraní API

Profil rozhraní API je kombinací poskytovatelů prostředků a verze rozhraní API. Použijte profil rozhraní API k získání nejnovější a nejvýznamnější verze každého typu prostředku v balíčku poskytovatele prostředků.

-   Chcete-li použít nejnovější verzí všech služeb, použijte **nejnovější** profilu balíčků. Tento profil je součástí **Microsoft.Azure.Management** balíček NuGet.

-   Chcete-li používat služby kompatibilní s Azure Stack, použijte jeden z následujících balíčků:
    - **Microsoft. Azure. Management. Profiles.\_Hybrid\_2019\_03 01. <*ResourceProvider*>. 0.9.0-Preview. nupkg** 
    - **Microsoft. Azure. Management. Profiles.\_Hybrid\_2018\_03 01. <*ResourceProvider*>. 0.9.0-Preview. nupkg**
    
    Ujistěte se, že **ResourceProvider** část výše uvedených balíčků NuGet se změní na poskytovateli správné.

-   Chcete-li používat nejnovější verzi rozhraní API služby, použijte **nejnovější** profil konkrétního balíčku NuGet. Pokud například chcete použít **nejnovější verzi rozhraní COMPUTE API** služby COMPUTE, použijte **nejnovější** profil balíčku **COMPUTE** . **Nejnovější** profilu je součástí **Microsoft.Azure.Management** balíček NuGet.

-   Konkrétní verze rozhraní API pro typ prostředku ve zprostředkovateli konkrétní prostředek, použití konkrétní verze rozhraní API definované uvnitř balíčku.

Můžete zkombinovat všechny možnosti ve stejné aplikaci.

## <a name="install-the-azure-net-sdk"></a>Instalace sady Azure .NET SDK

1.  Instalace Gitu. Pokyny najdete v tématu [Začínáme – instalace Git][].

2.  K instalaci správné balíčky NuGet, naleznete v tématu [Vyhledání a instalace balíčku][].

3.  Balíčky, které je potřeba nainstalovat, závisí na verzi profilu, kterou chcete použít. Názvy balíčků pro verze profilu jsou:

    1.  **Microsoft. Azure. Management. Profiles.\_Hybrid\_2019\_03 01. <*ResourceProvider*>. 0.9.0-Preview. nupkg**

    2.  **Microsoft. Azure. Management. Profiles.\_Hybrid\_2018\_03 01. <*ResourceProvider*>. 0.9.0-Preview. nupkg**

4.  K instalaci správné balíčky NuGet pro Visual Studio Code, viz následující odkaz ke stažení [Pokyny pro správce balíčků NuGet][].

5.  Pokud není k dispozici, vytvořte předplatné a uložte ID předplatného pro pozdější použití. Pokyny pro vytvoření odběru naleznete v tématu [vytvářet předplatná na nabídky ve službě Azure Stack][].

6.  Vytvoření instančního objektu a uložení ID klienta a tajný kód klienta. Pokyny k vytvoření instančního objektu pro Azure Stack najdete v tématu [Poskytují aplikace přístup ke službě Azure Stack][]. ID klienta se také označuje jako ID aplikace při vytváření instančního objektu.

7.  Ujistěte se, že má instančního objektu služby roli Přispěvatel nebo vlastník v rámci předplatného. Pokyny o tom, jak přiřadit roli instančnímu objektu služby najdete v tématu [Poskytují aplikace přístup ke službě Azure Stack][].

## <a name="prerequisites"></a>Požadavky

Použití sady .NET SDK pro Azure pomocí služby Azure Stack, musíte zadat následující hodnoty a pak nastavte hodnoty proměnné prostředí. Chcete-li nastavit proměnné prostředí, přečtěte si pokyny uvedené v tabulce pro konkrétní operační systém.

| Value                     | Proměnné prostředí   | Popis                                                                                                             |
|---------------------------|-------------------------|-------------------------------------------------------------------------------------------------------------------------|
| ID tenanta                 | `AZURE_TENANT_ID `      | Výhody služby Azure Stack [ *ID tenanta*][].                                                                          |
| ID klienta                 | `AZURE_CLIENT_ID `      | ID aplikace instančního objektu se uložilo při vytvoření instančního objektu v předchozí části tohoto článku. |
| ID předplatného           | `AZURE_SUBSCRIPTION_ID` | [ *ID předplatného* ][] je, jak získat přístup k nabídky ve službě Azure Stack.                                                      |
| Tajný kód klienta             | `AZURE_CLIENT_SECRET`   | Tajný kód aplikace instančního objektu se uložil při vytvoření objektu služby.                                      |
| Koncový bod Resource Manageru | `ARM_ENDPOINT`          | Viz [*koncový bod Správce prostředků Azure Stack*][].                                                                    |
| Location                  | `RESOURCE_LOCATION`     | Umístění pro Azure Stack.

Najít ID Tenanta pro službu Azure Stack, postupujte podle pokynů uvedených [tady](../operator/azure-stack-csp-ref-operations.md). Chcete-li nastavit proměnné prostředí, proveďte následující kroky:

### <a name="microsoft-windows"></a>Microsoft Windows

Chcete-li nastavit proměnné prostředí v příkazovém řádku Windows, použijte následující formát:

```shell
Set Azure_Tenant_ID=Your_Tenant_ID
```

### <a name="macos-linux-and-unix-based-systems"></a>macOS, Linux a systémů založených na systému Unix

V počítačích se systémem UNIX použijte následující příkaz:

```shell
Export Azure_Tenant_ID=Your_Tenant_ID
```

### <a name="the-azure-stack-resource-manager-endpoint"></a>Koncový bod Azure Stack Správce prostředků

Microsoft Azure Správce prostředků je rozhraní pro správu, které správcům umožňuje nasazovat, spravovat a monitorovat prostředky Azure. Azure Resource Manageru dokáže zpracovat tyto úkoly, jako se skupinou, nikoli samostatně, v rámci jedné operace.

Informace o metadatech můžete získat z Správce prostředkůho koncového bodu. Koncový bod vrátí soubor JSON s informacemi potřebnými ke spuštění vašeho kódu.

Mějte na paměti následující aspekty:

- **ResourceManagerUrl** je v Azure Stack Development Kit (ASDK): https://management.local.azurestack.external/

- **ResourceManagerUrl** v integrovaných systémech je: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`Načtení požadovaných metadat:`<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`

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

## <a name="existing-api-profiles"></a>Stávající profily rozhraní API

1.  **Microsoft. Azure. Management. Profiles.\_Hybrid\_2019\_03 01. <*ResourceProvider*>. 0.9.0-Preview. nupkg**: Nejnovější profil sestavený pro Azure Stack. Tento profil se používá pro služby, které jsou kompatibilní s Azure Stack, pokud jste na razítku 1904 nebo novějším.

2.  **Microsoft. Azure. Management. Profiles.\_Hybrid\_2018\_03 01. <*ResourceProvider*>. 0.9.0-Preview. nupkg**: Tento profil se používá pro služby, které jsou kompatibilní s Azure Stack pro razítka verze 1808 nebo novější.

3.  **Nejnovější**: Profil skládající se z nejnovějších verzí všech služeb. Použijte nejnovější verze všech služeb. Tento profil je součástí **Microsoft.Azure.Management** balíček NuGet.

Další informace o Azure Stack a profilech rozhraní API najdete v tématu [Přehled rozhraní API profily][].

## <a name="azure-net-sdk-api-profile-usage"></a>Použití profilu rozhraní API pro Azure .NET SDK

Následující kód by měl být použit k vytvoření instance klienta správy prostředků. Podobný kód lze použít k vytvoření instance jiného poskytovatele prostředků (například výpočetních, síťových a úložných) klientů.

```csharp
var client = new ResourceManagementClient(armEndpoint, credentials)
{
    SubscriptionId = subscriptionId
};
```

Aby bylo možné vytvořit instanci klienta, je nutné zadat parametrvevýšeuvedenémkódu.`credentials` Následující kód vygeneruje ověřovací token podle ID tenanta a instančního objektu.

```csharp
var azureStackSettings = getActiveDirectoryServiceSettings(armEndpoint);
var credentials = ApplicationTokenProvider.LoginSilentAsync(tenantId, servicePrincipalId, servicePrincipalSecret, azureStackSettings).GetAwaiter().GetResult();
```
`getActiveDirectoryServiceSettings` Volání v kódu načte Azure Stack koncové body z koncového bodu metadat. Uvádí proměnné prostředí z vytvořeného volání:

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

Pomocí těchto kroků můžete k Azure Stack úspěšnému nasazení aplikace použít balíčky NuGet profilu rozhraní API.

## <a name="samples-using-api-profiles"></a>Ukázky použití profilů rozhraní API

Následující ukázky použijte jako referenci pro vytváření řešení s využitím profilů rozhraní .NET a Azure Stack API.
- [Správa skupin prostředků](https://github.com/Azure-Samples/hybrid-resources-dotnet-manage-resource-group)
- [Správa účtů úložiště](https://github.com/Azure-Samples/hybird-storage-dotnet-manage-storage-accounts)
- [Správa virtuálního počítače](https://github.com/Azure-Samples/hybrid-compute-dotnet-manage-vm) (Tato ukázka používá 2019-03-01 – hybridní profil podporovaný Azure Stack)

## <a name="next-steps"></a>Další kroky

Další informace o profilech rozhraní API najdete v těchto tématech:

- [Správa profilů verzí API ve službě Azure Stack](azure-stack-version-profiles.md)
- [Podporuje profily verze rozhraní API poskytovatele prostředků](azure-stack-profiles-azure-resource-manager-versions.md)

  [Začínáme – instalace Git]: https://git-scm.com/book/en/v2/Getting-Started-Installing-Git
  [Vyhledání a instalace balíčku]: /nuget/tools/package-manager-ui
  [Pokyny pro správce balíčků NuGet]: https://marketplace.visualstudio.com/items?itemName=jmrog.vscode-nuget-package-manager
  [Vytvářet předplatná na nabídky ve službě Azure Stack]: ../operator/azure-stack-subscribe-plan-provision-vm.md
  [Poskytují aplikace přístup ke službě Azure Stack]: ../operator/azure-stack-create-service-principals.md
  [* tenant ID *]: ../operator/azure-stack-identity-overview.md
  [* předplatné ID *]: ../operator/azure-stack-plan-offer-quota-overview.md#subscriptions
  [* Azure Stack koncový bod Správce prostředků *]: ../user/azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint
  [Přehled rozhraní API profily]: ../user/azure-stack-version-profiles.md#summary-of-api-profiles
  [Test Project to Virtual Machine, vNet, resource groups, and storage account]: https://github.com/seyadava/azure-sdk-for-net-samples/tree/master/TestProject
  [Use Azure PowerShell to create a service principal with a certificate]: ../operator/azure-stack-create-service-principals.md
  [Run unit tests with Test Explorer.]: /visualstudio/test/run-unit-tests-with-test-explorer?view=vs-2017
