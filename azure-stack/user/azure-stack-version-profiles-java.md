---
title: Použití profilů verzí API v Javě ve službě Azure Stack | Dokumentace Microsoftu
description: Další informace o použití profilů verzí API v Javě ve službě Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: 90c4665b1519e078015f9a6096417c2d97425da9
ms.sourcegitcommit: 889fd09e0ab51ad0e43552a800bbe39dc9429579
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2019
ms.locfileid: "65782746"
---
# <a name="use-api-version-profiles-with-java-in-azure-stack"></a>Použití profilů verzí API s Javou v Azure stacku

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Sada Java SDK pro Azure Resource Manageru zásobníku poskytuje nástroje, které vám pomůžou vytvářet a spravovat infrastrukturu. Poskytovatelé prostředků v sadě SDK patří výpočty, sítě, úložiště, aplikační služby, a [KeyVault](/azure/key-vault/key-vault-whatis). Sada Java SDK zahrnuje profily rozhraní API včetně závislostí v, který načte správné moduly v souboru .Java, který soubor Pom.xml. Ale můžete přidat více profilů jako závislosti, jako **2018-03-01hybridní**, nebo **nejnovější**, jako profil Azure. Pomocí těchto závislostí načte správný modul tak, aby při vytváření vašeho typu prostředku, budete moct vybrat, kterou verzi rozhraní API z těchto profilů, které chcete použít. To umožňuje používat nejnovější verze v Azure, při vývoji na základě aktuální verze rozhraní API pro službu Azure Stack. Pomocí sady Java SDK poskytuje prostředí pro vývojáře skutečně hybridní cloud. Profily rozhraní API v jazyce Java SDK povolit hybridní cloudový vývoj díky ní si můžete přepínat mezi globální prostředky Azure a prostředky ve službě Azure Stack.

## <a name="java-and-api-version-profiles"></a>Profily verze Javy a rozhraní API

Profil rozhraní API je kombinací poskytovatelů prostředků a verze rozhraní API. Profil rozhraní API můžete použít k získání nejnovější, většina stabilní verzi každého typu prostředku v balíčku poskytovatele prostředků.

- Chcete-li používat nejnovější verze všech služeb, použijte **nejnovější** profil jako závislost.

  - Pokud chcete používat nejnovější profil, závislost je **com.microsoft.azure**.

  - Chcete-li použít služby kompatibilní s Azure Stack, použijte **com.microsoft.azure.profile\_2018\_03\_01\_hybridní** profilu.

    - Toto je třeba zadat do souboru Pom.xml jako závislost, která automaticky načte moduly Pokud zvolíte správné třídy z rozevíracího seznamu stejně jako s .NET.

    - Horní části každého modulu, který se zobrazí takto:      `Import com.microsoft.azure.management.resources.v2018_03_01.ResourceGroup`

  - Závislosti vypadat následovně:

     ```xml
     <dependency>
     <groupId>com.microsoft.azure.profile_2018_03_01_hybrid</groupId>
     <artifactId>azure</artifactId>
     <version>1.0.0-beta</version>
     </dependency>
     ```

  - Konkrétní verze rozhraní API pro typ prostředku ve zprostředkovateli konkrétní prostředek, použití konkrétní verze rozhraní API, které jsou definované pomocí Intellisense.

Všimněte si, že můžete kombinovat všechny možnosti ve stejné aplikaci.

## <a name="install-the-azure-java-sdk"></a>Instalace sady Azure Java SDK

Instalace sady Java SDK, postupujte následovně:

1. Postupujte podle pokynů oficiální nainstalujte Git. Pokyny najdete v tématu [Začínáme - instalaci Gitu](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

2. Postupujte podle pokynů k instalaci [sady Java SDK](https://zulu.org/download/) a [Maven](https://maven.apache.org/). Správná verze je verze 8 Java Developer Kit. Správné Apache Maven je verze 3.0 nebo vyšší. Proměnná prostředí JAVA_HOME musí nastavit na umístění instalace sady Java Development Kit k dokončení tohoto rychlého startu. Další informace najdete v tématu [vytvoření první funkce pomocí Javy a Mavenu](/azure/azure-functions/functions-create-first-java-maven).

3. K instalaci správné závislé balíčky, otevřete soubor Pom.xml ve vaši aplikaci v Javě. Přidáte závislost, jak je znázorněno v následujícím kódu:

   ```xml  
   <dependency>
   <groupId>com.microsoft.azure.profile_2018_03_01_hybrid</groupId>
   <artifactId>azure</artifactId>
   <version>1.0.0-beta</version>
   </dependency>
   ```

4. Sadu balíčků, které je potřeba nainstalovat závisí na verzi profilu, který chcete použít. Názvy balíčků pro profil verze jsou:

   - **com.microsoft.azure.profile\_2018\_03\_01\_hybrid**
   - **com.microsoft.azure**
     - **nejnovější**

5. Pokud není k dispozici, vytvořte předplatné a uložit pro pozdější použití. ID předplatného. Pokyny pro vytvoření odběru naleznete v tématu [vytvářet předplatná na nabídky ve službě Azure Stack](../operator/azure-stack-subscribe-plan-provision-vm.md).

6. Vytvoření instančního objektu a uložit klienta, ID a tajný kód klienta. Pokyny o tom, jak vytvořit instanční objekt pro Azure Stack najdete v tématu [poskytují aplikacím přístup ke službě Azure Stack](../operator/azure-stack-create-service-principals.md). Všimněte si, že ID klienta se také označuje jako ID aplikace při vytváření instančního objektu.

7. Ujistěte se, že má instančního objektu služby roli Přispěvatel nebo vlastník v rámci předplatného. Pokyny o tom, jak přiřadit roli instančnímu objektu služby najdete v tématu [poskytují aplikacím přístup ke službě Azure Stack](../operator/azure-stack-create-service-principals.md).

## <a name="prerequisites"></a>Požadavky

Použití sady Java SDK pro Azure pomocí služby Azure Stack, musíte zadat následující hodnoty a pak nastavte hodnoty proměnné prostředí. Chcete-li nastavit proměnné prostředí, přečtěte si pokyny pod tabulkou pro váš operační systém.

| Hodnota                     | Proměnné prostředí | Popis                                                                                                                                                                                                          |
| ------------------------- | --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| ID tenanta                 | AZURE_TENANT_ID            | Výhody služby Azure Stack [ID tenanta](../operator/azure-stack-identity-overview.md).                                                          |
| ID klienta                 | AZURE_CLIENT_ID             | Služba ID instančního objektu aplikace neuloží, když se objekt služby byl vytvořen v předchozí části.                                                                                              |
| ID předplatného           | AZURE_SUBSCRIPTION_ID      | [ID předplatného](../operator/azure-stack-plan-offer-quota-overview.md#subscriptions) je, jak získat přístup k nabídky ve službě Azure Stack.                |
| Tajný kód klienta             | AZURE_CLIENT_SECRET        | Služba hlavní tajný klíč aplikace neuloží, když se vytvoří nový instanční objekt služby.                                                                                                                                   |
| Koncový bod Resource Manageru | ARM_ENDPOINT              | Zobrazit [koncový bod Azure Stack Resource Manager](../user/azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint). |
| Location                  | RESOURCE_LOCATION    | **Místní** pro Azure Stack.                                                                                                                                                                                                |

ID tenanta služby Azure Stack najdete v tématu pokynů [tady](../operator/azure-stack-csp-ref-operations.md). Chcete-li nastavit proměnné prostředí, postupujte takto:

### <a name="microsoft-windows"></a>Microsoft Windows

Chcete-li nastavit proměnné prostředí v příkazovém řádku Windows, použijte následující formát:

```shell
Set AZURE_TENANT_ID=<Your_Tenant_ID>
```

### <a name="macos-linux-and-unix-based-systems"></a>macOS, Linux a systémů založených na systému Unix

V systémech Unix založené použijte následující příkaz:

```shell
Export AZURE_TENANT_ID=<Your_Tenant_ID>
```

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Důvěřovat certifikátu kořenové certifikační Autority Azure stacku

Pokud používáte ASDK, musí důvěřovat certifikátu kořenové certifikační Autority na vzdáleném počítači. Nebude potřeba to udělat pomocí integrovaných systémů.

#### <a name="windows"></a>Windows

1. Exportujte certifikát podepsaný svým držitelem Azure Stack na plochu.

1. V příkazovém řádku změňte adresář na % JAVA_HOME%\bin.

1. Spusťte následující příkaz:

   ```shell
   .\keytool.exe -importcert -noprompt -file <location of the exported certificate here> -alias root -keystore %JAVA_HOME%\lib\security\cacerts -trustcacerts -storepass changeit
   ```

### <a name="the-azure-stack-resource-manager-endpoint"></a>Koncový bod Azure Stack Resource Manageru

Microsoft Azure Resource Manageru je systém správy, který vám umožňuje správcům nasadit, spravovat a monitorovat prostředky Azure. Azure Resource Manageru dokáže zpracovat tyto úkoly, jako se skupinou, nikoli samostatně, v rámci jedné operace.

Z koncového bodu Resource Manageru můžete získat informace o metadatech. Koncový bod vrátí soubor JSON s informacemi potřebnými pro spouštění vašeho kódu.

Mějte na paměti následující aspekty:

- **ResourceManagerUrl** je v Azure Stack Development Kit (ASDK): https://management.local.azurestack.external/.

- **ResourceManagerUrl** v integrovaných systémech je: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`.

Načíst metadata, požadované: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`.

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

## <a name="existing-api-profiles"></a>Stávající profily rozhraní API

- **com.microsoft.azure.profile\_2018\_03\_01\_hybrid**: Nejnovější profil vytvořené pro Azure Stack. Použijte tento profil služby bude nejvíc kompatibilní se systémem Azure Stack, dokud jsou na razítku. 1808 a další.

- **com.microsoft.azure**: Profil, který se skládá z nejnovější verze všech služeb. Použijte nejnovější verze všech služeb.

Další informace o profilech rozhraní API a služby Azure Stack, najdete v článku [souhrn rozhraní API profily](../user/azure-stack-version-profiles.md#summary-of-api-profiles).

## <a name="azure-java-sdk-api-profile-usage"></a>Využití Azure profil rozhraní API sady Java SDK

Následující kód se ověřuje instanční objekt služby ve službě Azure Stack. Vytvoří token pomocí ID tenanta a základní ověřování, který je specifický pro Azure Stack:

```java
AzureTokenCredentials credentials = new ApplicationTokenCredentials(client, tenant, key, AZURE_STACK)
                    .withDefaultSubscriptionID(subscriptionID);
Azure azureStack = Azure.configure()
                    .withLogLevel(com.microsoft.rest.LogLevel.BASIC)
                    .authenticate(credentials, credentials.defaultSubscriptionID());
```

To umožňuje použít profil závislosti rozhraní API pro nasazení aplikace úspěšně do služby Azure Stack.

## <a name="define-azure-stack-environment-setting-functions"></a>Definování funkcí, nastavení prostředí Azure Stack

Zaregistrovat správné koncové body cloudu služby Azure Stack, použijte následující kód:

```java
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

`getActiveDirectorySettings` Volání v následujícím kódu načte z koncových bodů metadat koncových bodů. Uvádí proměnné prostředí z volání, které tvoří:

```java
public static HashMap<String, String>
getActiveDirectorySettings(String armEndpoint) {

HashMap<String, String> adSettings = new HashMap<String, String>();

try {

// create HTTP Client
HttpClient httpClient = HttpClientBuilder.create().build();

// Create new getRequest with below mentioned URL
HttpGet getRequest = new
HttpGet(String.format("%s/metadata/endpoints?api-version=1.0",
armEndpoint));

// Add additional header to getRequest which accepts application/xml data
getRequest.addHeader("accept", "application/xml");

// Execute request and catch response
HttpResponse response = httpClient.execute(getRequest);
```

## <a name="samples-using-api-profiles"></a>Ukázky použití profilů rozhraní API

Následující ukázky na Githubu jako reference můžete použít k vytváření řešení pomocí profilů .NET a rozhraní API služby Azure Stack:

- [Správa skupin prostředků](https://github.com/Azure-Samples/Hybrid-resources-java-manage-resource-group)

- [Správa účtů úložiště](https://github.com/Azure-Samples/hybrid-storage-java-manage-storage-accounts)

- [Správa virtuálního počítače](https://github.com/Azure-Samples/hybrid-compute-java-manage-vm)

### <a name="sample-unit-test-project"></a>Ukázkový projekt testování částí

1. Naklonujte úložiště pomocí následujícího příkazu:

   `git clone https://github.com/Azure-Samples/Hybrid-resources-java-manage-resource-group.git`

2. Vytvoření instančního objektu Azure a přiřazení role přístupu k předplatnému. Pokyny k vytvoření instančního objektu najdete v tématu [pomocí prostředí Azure PowerShell k vytvoření instančního objektu s certifikátem](../operator/azure-stack-create-service-principals.md).

3. Načtěte následující hodnoty proměnné požadované prostředí:

   - AZURE_TENANT_ID
   - AZURE_CLIENT_ID
   - AZURE_CLIENT_SECRET
   - AZURE_SUBSCRIPTION_ID
   - ARM_ENDPOINT
   - RESOURCE_LOCATION

4. Nastavte následující proměnné prostředí pomocí informace, které jste získali z instanční objekt že vytvoříte pomocí příkazového řádku:

   - Export AZURE_TENANT_ID = {ID vašeho tenanta}
   - Export AZURE_CLIENT_ID = {ID klienta}
   - Export AZURE_CLIENT_SECRET = {váš tajný klíč klienta}
   - Export AZURE_SUBSCRIPTION_ID = {ID předplatného}
   - Export ARM_ENDPOINT = {zásobníku Resource Manageru adresy URL Azure}
   - Export RESOURCE_LOCATION = {umístění služby Azure Stack}

   Ve Windows, použijte **nastavit** místo **exportovat**.

5. Použít `getactivedirectorysettings` kód pro načtení metadat koncového bodu arm a použití klienta HTTP se nastavit informace o koncovém bodu.

   ```java
   public static HashMap<String, String> getActiveDirectorySettings(String armEndpoint) {
   HashMap<String, String> adSettings = new HashMap<String,> String>();

   try {

   // create HTTP Client
   HttpClient httpClient = HttpClientBuilder.create().build();

   // Create new getRequest with below mentioned URL
   HttpGet getRequest = new
   HttpGet(String.format("%s/metadata/endpoints?api-version=1.0", armEndpoint));

   // Add additional header to getRequest which accepts application/xml data
   getRequest.addHeader("accept", "application/xml");

   // Execute request and catch response
   HttpResponse response = httpClient.execute(getRequest);
   ```

6. Do souboru Pom.xml přidejte následující závislost používat **2018-03-01hybridní** profil pro službu Azure Stack. Tato závislost instaluje moduly spojený s tímto profilem pro poskytovatele prostředků Compute, sítě, úložiště, trezor klíčů a App Services:

   ```xml
   <dependency>
   <groupId>com.microsoft.azure.profile_2018_03_01_hybrid</groupId>
   <artifactId>azure</artifactId>
   <vers1s.0.0-beta</version>
   </dependency>
   ```

7. Na příkazovém řádku, který byl otevřen k nastavení proměnných prostředí zadejte následující příkaz:

   ```shell
   mvn clean compile exec:java
   ```

## <a name="next-steps"></a>Další postup

Další informace o profilech rozhraní API najdete v tématu:

- [Profilů verzí ve službě Azure Stack](azure-stack-version-profiles.md)
- [Podporuje profily verze rozhraní API poskytovatele prostředků](azure-stack-profiles-azure-resource-manager-versions.md)
