---
title: Použití profilů verzí rozhraní API s Java v Azure Stack | Microsoft Docs
description: Naučte se používat profily verzí rozhraní API pomocí Java v Azure Stack.
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
ms.date: 10/01/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: 7d2a98656671a5e6f59d46054e074e45bb9d3c89
ms.sourcegitcommit: d159652f50de7875eb4be34c14866a601a045547
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72282887"
---
# <a name="use-api-version-profiles-with-java-in-azure-stack"></a>Použití profilů verzí rozhraní API s Java v Azure Stack

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Sada Java SDK pro Azure Stack Správce prostředků poskytuje nástroje, které vám pomůžou sestavovat a spravovat infrastrukturu. Poskytovatelé prostředků v sadě SDK zahrnují výpočetní prostředky, sítě, úložiště, App Services a [Key Vault](/azure/key-vault/key-vault-whatis).

Java SDK zahrnuje profily rozhraní API, včetně závislostí v souboru **pom. XML** , který načte správné moduly v souboru **. Java** . Jako profil Azure ale můžete přidat několik profilů jako závislosti, jako je například **2019-03-01 – hybridní**nebo **nejnovější**. Pomocí těchto závislostí načtete správný modul, takže když vytvoříte typ prostředku, můžete si z těchto profilů vybrat verzi rozhraní API, kterou chcete použít. To vám umožní používat v Azure nejnovější verze, ale vyvíjí se s nejaktuálnější verzí rozhraní API pro Azure Stack.

Použití sady Java SDK umožňuje skutečné prostředí pro vývojáře hybridního cloudu. Profily rozhraní API v Java SDK umožňují vývoj hybridního cloudu tím, že vám pomůže přepínat mezi globálním prostředky a prostředky Azure v Azure Stack.

## <a name="java-and-api-version-profiles"></a>Profily verzí Java a API

Profil rozhraní API je kombinací poskytovatelů prostředků a verzí rozhraní API. Použijte profil rozhraní API k získání nejnovější a nejvýznamnější verze každého typu prostředku v balíčku poskytovatele prostředků.

- Chcete-li použít nejnovější verze všech služeb, použijte jako závislost **nejnovější** profil.

  - Pokud chcete použít nejnovější profil, je tato závislost **com. Microsoft. Azure**.

  - Pokud chcete používat nejnovější podporované služby, které jsou dostupné v Azure Stack, použijte **model COM. Microsoft. Azure. Profiles @ no__t-12019 @ no__t-203 @ no__t-301 @ no__t-4hybrid** Profile.

    - Profil je zadán v souboru **pom. XML** jako závislost, která automaticky načítá moduly, pokud zvolíte správnou třídu v rozevíracím seznamu (stejně jako v případě rozhraní .NET).

  - Závislosti se zobrazí takto:

     ```xml
     <dependency>
     <groupId>com.microsoft.azure.profile_2019_03_01_hybrid</groupId>
     <artifactId>azure</artifactId>
     <version>1.0.0-beta</version>
     </dependency>
     ```

  - Pokud chcete použít konkrétní verze rozhraní API pro typ prostředku v konkrétním poskytovateli prostředků, použijte konkrétní verze rozhraní API definované pomocí technologie IntelliSense.

Můžete zkombinovat všechny možnosti ve stejné aplikaci.

## <a name="install-the-azure-java-sdk"></a>Instalace sady Azure Java SDK

Pomocí těchto kroků nainstalujete Java SDK:

1. Pomocí oficiálních pokynů nainstalujte Git. Viz [Začínáme – instalace Gitu](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

2. Postupujte podle pokynů a nainstalujte [sadu Java SDK](https://zulu.org/download/) a [Maven](https://maven.apache.org/). Správná verze je verze 8 sady Java Developer Kit. Správná verze Apache Maven je 3,0 nebo vyšší. K dokončení rychlého startu musí být proměnná prostředí `JAVA_HOME` nastavená na umístění instalace sady Java Development Kit. Další informace najdete v tématu [Vytvoření první funkce pomocí jazyků Java a Maven](/azure/azure-functions/functions-create-first-java-maven).

3. Chcete-li nainstalovat správné balíčky závislostí, otevřete soubor **pom. XML** v aplikaci Java. Přidejte závislost, jak je znázorněno v následujícím kódu:

   ```xml  
   <dependency>
   <groupId>com.microsoft.azure.profile_2019_03_01_hybrid</groupId>
   <artifactId>azure</artifactId>
   <version>1.0.0-beta</version>
   </dependency>
   ```

4. Sada balíčků, které je třeba nainstalovat, závisí na verzi profilu, kterou chcete použít. Názvy balíčků pro verze profilu jsou:

   - **com. Microsoft. Azure. Profiles @ no__t-12019 @ no__t-203 @ no__t-301 @ no__t-4hybrid**
   - **com. Microsoft. Azure**
     - **nejnovější**

5. Pokud není k dispozici, vytvořte odběr a uložte si ID předplatného pro pozdější použití. Pokyny k vytvoření předplatného najdete [v tématu Vytvoření předplatných nabídek v Azure Stack](../operator/azure-stack-subscribe-plan-provision-vm.md).

6. Vytvořte instanční objekt a uložte ID klienta a tajný klíč klienta. Pokyny k vytvoření instančního objektu pro Azure Stack najdete v tématu [poskytnutí přístupu aplikací k Azure Stack](../operator/azure-stack-create-service-principals.md). ID klienta se také označuje jako ID aplikace při vytváření instančního objektu.

7. Ujistěte se, že váš instanční objekt má v předplatném roli Přispěvatel/vlastník. Pokyny k přiřazení role k instančnímu objektu najdete v tématu [poskytnutí přístupu k aplikacím Azure Stack](../operator/azure-stack-create-service-principals.md).

## <a name="prerequisites"></a>Požadavky

Pokud chcete používat sadu Azure Java SDK s Azure Stack, musíte zadat následující hodnoty a potom nastavit hodnoty pomocí proměnných prostředí. Chcete-li nastavit proměnné prostředí, přečtěte si pokyny uvedené v tabulce pro váš operační systém.

| Hodnota                     | Proměnné prostředí | Popis                                                                                                                                                                                                          |
| ------------------------- | --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ID tenanta                 | `AZURE_TENANT_ID`            | Vaše [ID tenanta](../operator/azure-stack-identity-overview.md)Azure Stack.                                                          |
| ID klienta                 | `AZURE_CLIENT_ID`             | ID aplikace instančního objektu se uložilo při vytvoření instančního objektu v předchozí části.                                                                                              |
| ID předplatného           | `AZURE_SUBSCRIPTION_ID`      | [ID předplatného](../operator/service-plan-offer-subscription-overview.md#subscriptions) se používá pro přístup k nabídkám v Azure Stack.                |
| Tajný kód klienta             | `AZURE_CLIENT_SECRET`        | Tajný kód aplikace instančního objektu uložený při vytvoření objektu služby                                                                                                                                   |
| Správce prostředků koncový bod | `ARM_ENDPOINT`              | Viz článek [Azure Stack správce prostředků koncového bodu](../user/azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint) . |
| Umístění                  | `RESOURCE_LOCATION`    | **Místní** pro Azure Stack.                                                                                                                                                                                                |

Pokud chcete zjistit ID tenanta pro vaši Azure Stack, přečtěte si pokyny [zde](../operator/azure-stack-csp-ref-operations.md). Chcete-li nastavit proměnné prostředí, použijte postupy v následujících částech:

### <a name="microsoft-windows"></a>Microsoft Windows

Chcete-li nastavit proměnné prostředí v příkazovém řádku systému Windows, použijte následující formát:

```shell
Set AZURE_TENANT_ID=<Your_Tenant_ID>
```

### <a name="macos-linux-and-unix-based-systems"></a>systémy macOS, Linux a UNIX

V počítačích se systémem UNIX použijte následující příkaz:

```shell
Export AZURE_TENANT_ID=<Your_Tenant_ID>
```

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Důvěřovat kořenovému certifikátu certifikační autority Azure Stack

Pokud používáte Azure Stack Development Kit (ASDK), musíte důvěřovat kořenovému certifikátu certifikační autority na svém vzdáleném počítači. Kořenový certifikát certifikační autority nemusíte důvěřovat s Azure Stack integrovanými systémy.

#### <a name="windows"></a>Windows

1. Exportujte certifikát podepsaný svým držitelem Azure Stack do plochy.

1. V příkazovém řádku změňte adresář na `%JAVA_HOME%\bin`.

1. Spusťte následující příkaz:

   ```shell
   .\keytool.exe -importcert -noprompt -file <location of the exported certificate here> -alias root -keystore %JAVA_HOME%\lib\security\cacerts -trustcacerts -storepass changeit
   ```

### <a name="the-azure-stack-resource-manager-endpoint"></a>Koncový bod Azure Stack Správce prostředků

Azure Resource Manager je rozhraní pro správu, které správcům umožňuje nasazovat, spravovat a monitorovat prostředky Azure. Azure Resource Manager může tyto úlohy v jedné operaci zpracovat jako skupinu, nikoli jednotlivě.

Informace o metadatech můžete získat z Správce prostředkůho koncového bodu. Koncový bod vrátí soubor JSON s informacemi potřebnými ke spuštění vašeho kódu.

Vezměte na vědomí následující skutečnosti:

- **ResourceManagerUrl** v ASDK je: `https://management.local.azurestack.external/`.

- **ResourceManagerUrl** v integrovaných systémech je: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`.

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
      "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
      }
}
```

## <a name="existing-api-profiles"></a>Existující profily rozhraní API

- **com. Microsoft. Azure. Profile @ no__t-12019 @ no__t-203 @ no__t-301 @ no__t-4hybrid**: nejnovější profil sestavený pro Azure Stack. Tento profil se používá pro služby, které jsou kompatibilní s Azure Stack, pokud používáte 1904 nebo novější.

- **com. Microsoft. Azure. Profile @ no__t-12018 @ no__t-203 @ no__t-301 @ no__t-4hybrid**: Profil sestavený pro Azure Stack. Použijte tento profil pro služby, aby byly kompatibilní s Azure Stack verze 1808 nebo novější.

- **com. Microsoft. Azure**: Profil skládající se z nejnovějších verzí všech služeb. Použijte nejnovější verze všech služeb.

Další informace o Azure Stack a profilech rozhraní API najdete v tématu [Přehled profilů rozhraní API](../user/azure-stack-version-profiles.md#summary-of-api-profiles).

## <a name="azure-java-sdk-api-profile-usage"></a>Použití profilu rozhraní API pro Azure Java SDK

Následující kód ověřuje instanční objekt v Azure Stack. Vytvoří token pomocí ID tenanta a základu ověřování, která je specifická pro Azure Stack:

```java
AzureTokenCredentials credentials = new ApplicationTokenCredentials(client, tenant, key, AZURE_STACK)
                    .withDefaultSubscriptionID(subscriptionID);
Azure azureStack = Azure.configure()
                    .withLogLevel(com.microsoft.rest.LogLevel.BASIC)
                    .authenticate(credentials, credentials.defaultSubscriptionID());
```

Tento kód umožňuje používat závislosti profilu rozhraní API k úspěšnému nasazení vaší aplikace Azure Stack.

## <a name="define-azure-stack-environment-setting-functions"></a>Definování funkcí nastavení prostředí Azure Stack

Chcete-li zaregistrovat Azure Stack Cloud se správnými koncovými body, použijte následující kód:

```java
// Get Azure Stack cloud endpoints
final HashMap<String, String> settings = getActiveDirectorySettings(armEndpoint);

AzureEnvironment AZURE_STACK = new AzureEnvironment(new HashMap<String, String>() {
                {
                    put("managementEndpointUrl", settings.get("audience"));
                    put("resourceManagerEndpointUrl", armEndpoint);
                    put("galleryEndpointUrl", settings.get("galleryEndpoint"));
                    put("activeDirectoryEndpointUrl", settings.get("login_endpoint"));
                    put("activeDirectoryResourceID", settings.get("audience"));
                    put("activeDirectoryGraphResourceID", settings.get("graphEndpoint"));
                    put("storageEndpointSuffix", armEndpoint.substring(armEndpoint.indexOf('.')));
                    put("keyVaultDnsSuffix", ".vault" + armEndpoint.substring(armEndpoint.indexOf('.')));
                }
            });
```

Volání `getActiveDirectorySettings` v předchozím kódu načte koncové body z koncových bodů metadat. Uvádí proměnné prostředí z vytvořeného volání:

```java
public static HashMap<String, String> getActiveDirectorySettings(String armEndpoint) {

    HashMap<String, String> adSettings = new HashMap<String, String>();
    try {

        // create HTTP Client
        HttpClient httpClient = HttpClientBuilder.create().build();

        // Create new getRequest with below mentioned URL
        HttpGet getRequest = new HttpGet(String.format("%s/metadata/endpoints?api-version=1.0",
                             armEndpoint));

        // Add additional header to getRequest which accepts application/xml data
        getRequest.addHeader("accept", "application/xml");

        // Execute request and catch response
        HttpResponse response = httpClient.execute(getRequest);

        // Check for HTTP response code: 200 = success
        if (response.getStatusLine().getStatusCode() != 200) {
            throw new RuntimeException("Failed : HTTP error code : " + response.getStatusLine().getStatusCode());
        }

        String responseStr = EntityUtils.toString(response.getEntity());
        JSONObject responseJson = new JSONObject(responseStr);
        adSettings.put("galleryEndpoint", responseJson.getString("galleryEndpoint"));
        JSONObject authentication = (JSONObject) responseJson.get("authentication");
        String audience = authentication.get("audiences").toString().split("\"")[1];
        adSettings.put("login_endpoint", authentication.getString("loginEndpoint"));
        adSettings.put("audience", audience);
        adSettings.put("graphEndpoint", responseJson.getString("graphEndpoint"));

    } catch (ClientProtocolException cpe) {
        cpe.printStackTrace();
        throw new RuntimeException(cpe);
    } catch (IOException ioe) {
        ioe.printStackTrace();
        throw new RuntimeException(ioe);
    }
    return adSettings;
}
```

## <a name="samples-using-api-profiles"></a>Ukázky pomocí profilů rozhraní API

Následující ukázky GitHubu použijte jako reference k vytváření řešení pomocí profilů rozhraní .NET a Azure Stack API:

- [Správa skupin prostředků](https://github.com/Azure-Samples/Hybrid-resources-java-manage-resource-group)

- [Správa účtů úložiště](https://github.com/Azure-Samples/hybrid-storage-java-manage-storage-accounts)

- [Správa virtuálního počítače](https://github.com/Azure-Samples/hybrid-compute-java-manage-vm) (aktualizovaného s 2019-03-01 – hybridní profil)

### <a name="sample-unit-test-project"></a>Ukázkový projekt testování částí

1. Naklonujte úložiště pomocí následujícího příkazu:

   ```shell
   git clone https://github.com/Azure-Samples/Hybrid-resources-java-manage-resource-group.git`
   ```

2. Vytvořte instanční objekt Azure a přiřaďte roli pro přístup k předplatnému. Pokyny k vytvoření instančního objektu najdete v tématu [použití Azure PowerShell k vytvoření instančního objektu s certifikátem](../operator/azure-stack-create-service-principals.md).

3. Načtěte následující požadované proměnné prostředí:

   - `AZURE_TENANT_ID`
   - `AZURE_CLIENT_ID`
   - `AZURE_CLIENT_SECRET`
   - `AZURE_SUBSCRIPTION_ID`
   - `ARM_ENDPOINT`
   - `RESOURCE_LOCATION`

4. Nastavte následující proměnné prostředí pomocí informací načtených z instančního objektu, který jste vytvořili pomocí příkazového řádku:

   - `export AZURE_TENANT_ID={your tenant ID}`
   - `export AZURE_CLIENT_ID={your client ID}`
   - `export AZURE_CLIENT_SECRET={your client secret}`
   - `export AZURE_SUBSCRIPTION_ID={your subscription ID}`
   - `export ARM_ENDPOINT={your Azure Stack Resource Manager URL}`
   - `export RESOURCE_LOCATION={location of Azure Stack}`

   V systému Windows použijte místo **exportu**příkaz **set** .

5. Pro načtení Azure Resource Manager koncových bodů metadat použijte funkci `getActiveDirectorySettings`.

    ```java
    // Get Azure Stack cloud endpoints
    final HashMap<String, String> settings = getActiveDirectorySettings(armEndpoint);
    ```

6. V souboru **pom. XML** přidejte následující závislost pro použití **2019-03-01-Hybrid** Profile pro Azure Stack. Tato závislost instaluje moduly přidružené k tomuto profilu pro poskytovatele prostředků COMPUTE, sítě, úložiště, Key Vault a App Services:

   ```xml
   <dependency>
   <groupId>com.microsoft.azure.profile_2019_03_01_hybrid</groupId>
   <artifactId>azure</artifactId>
   <vers1s.0.0-beta</version>
   </dependency>
   ```

7. Do příkazového řádku, který se otevřel pro nastavení proměnných prostředí, zadejte následující příkaz:

   ```shell
   mvn clean compile exec:java
   ```

## <a name="next-steps"></a>Další kroky

Další informace o profilech rozhraní API najdete v těchto tématech:

- [Profily verzí v Azure Stack](azure-stack-version-profiles.md)
- [Verze rozhraní API poskytovatele prostředků podporované profily](azure-stack-profiles-azure-resource-manager-versions.md)
