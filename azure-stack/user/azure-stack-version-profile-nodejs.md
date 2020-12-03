---
title: Použití profilů verzí rozhraní API s Node.js v centru Azure Stack
description: Přečtěte si o používání profilů verzí rozhraní API s Node.js v centru Azure Stack.
author: mattbriggs
ms.topic: article
ms.date: 12/2/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: c6b781b61a8738bd86379d9fb035680189693565
ms.sourcegitcommit: 9ef2cdc748cf00cd3c8de90705ea0542e29ada97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96525927"
---
# <a name="use-api-version-profiles-with-nodejs-software-development-kit-sdk-in-azure-stack-hub"></a>Použití profilů verzí rozhraní API se sadou Node.js SDK (Software Development Kit) v centru Azure Stack

## <a name="nodejs-and-api-version-profiles"></a>Profily verze Node.js a API

Node.js SDK můžete použít k vytvoření a správě infrastruktury pro aplikace. Profily rozhraní API v sadě Node.js SDK pro vaše hybridní cloudová řešení vám umožní přepínat mezi globálním prostředky Azure a prostředky centra Azure Stack. Můžete kódovat jednou a potom cílit na globální centrum Azure i Azure Stack. 

V tomto článku můžete jako vývojářský nástroj použít [Visual Studio Code](https://code.visualstudio.com/) . Visual Studio Code může ladit sadu Node.js SDK a umožňuje aplikaci spustit a aplikaci nasdílet do své instance služby Azure Stack hub. Můžete ladit z Visual Studio Code nebo prostřednictvím okna terminálu, který spouští příkaz `node <nodefile.js>` .

## <a name="the-nodejs-sdk"></a>Sada Node.js SDK

Sada Node.js SDK poskytuje nástroje pro Správce prostředků centra Azure Stack. Poskytovatelé prostředků v sadě SDK zahrnují výpočetní prostředky, sítě, úložiště, služby App Service a trezor klíčů. K dispozici je 10 klientských knihoven poskytovatele prostředků, které můžete nainstalovat do aplikace node.js. Můžete si také stáhnout, který poskytovatel prostředků použijete pro **2018-03-01-Hybrid** nebo **2019-03-01-profil** , aby se optimalizoval paměť pro vaši aplikaci. Každý modul obsahuje poskytovatele prostředků, příslušnou verzi rozhraní API a profil rozhraní API. 

Profil rozhraní API je kombinací poskytovatelů prostředků a verzí rozhraní API. Profil rozhraní API můžete použít k získání nejnovější a nejstabilní verze každého typu prostředku v balíčku poskytovatele prostředků.

  -   Chcete-li používat nejnovější verze všech služeb, použijte **nejnovější** profil balíčků.

  -   Pokud chcete používat služby kompatibilní s Azure Stack hub, použijte **\@ Azure/ARM-Resources-Profile-Hybrid-2019-03-01** nebo **\@ Azure/ARM-Storage-Profile-2019-03 -01-Hybrid**

### <a name="packages-in-npm"></a>Balíčky v npm

Každý poskytovatel prostředků má svůj vlastní balíček. Balíček můžete získat z [registr npm](https://www.npmjs.com/package/@azure/arm-storage-profile-2019-03-01-hybrid).

Můžete najít tyto balíčky:

| Poskytovatel prostředků | Balíček |
| --- | --- |
| [App Service](https://www.npmjs.com/package/@azure/arm-appservice-profile-2019-03-01-hybrid) | @azure/arm-appservice-profile-2019-03-01-hybrid |
| [Předplatná Azure Resource Manager](https://www.npmjs.com/package/@azure/arm-subscriptions-profile-hybrid-2019-03-01) | @azure/arm-subscriptions-profile-hybrid-2019-03-01  |
| [Zásady Azure Resource Manager](https://www.npmjs.com/package/@azure/arm-policy-profile-hybrid-2019-03-01) | @azure/arm-policy-profile-hybrid-2019-03-01
| [Azure Resource Manager DNS](https://www.npmjs.com/package/@azure/arm-dns-profile-2019-03-01-hybrid) | @azure/arm-dns-profile-2019-03-01-hybrid  |
| [Autorizace](https://www.npmjs.com/package/@azure/arm-authorization-profile-2019-03-01-hybrid) | @azure/arm-authorization-profile-2019-03-01-hybrid  |
| [Výpočetní služby](https://www.npmjs.com/package/@azure/arm-compute-profile-2019-03-01-hybrid) | @azure/arm-compute-profile-2019-03-01-hybrid |
| [Storage](https://www.npmjs.com/package/@azure/arm-storage-profile-2019-03-01-hybrid) | @azure/arm-storage-profile-2019-03-01-hybrid |
| [Síť](https://www.npmjs.com/package/@azure/arm-network-profile-2019-03-01-hybrid) | @azure/arm-network-profile-2019-03-01-hybrid |
| [Prostředky](https://www.npmjs.com/package/@azure/arm-resources-profile-hybrid-2019-03-01) | @azure/arm-resources-profile-hybrid-2019-03-01 |
 | [Keyvault](https://www.npmjs.com/package/@azure/arm-keyvault-profile-2019-03-01-hybrid) | @azure/arm-keyvault-profile-2019-03-01-hybrid |

Chcete-li použít nejnovější verzi služby API-Version, použijte **nejnovější** profil konkrétní klientské knihovny. Pokud například chcete použít nejnovější verzi služby Resources-API, použijte `azure-arm-resource` profil **klientské knihovny správy prostředků.** balíček.

Použijte konkrétní verze rozhraní API definované v balíčku pro konkrétní verze rozhraní API pro poskytovatele prostředků.

  > [!NOTE]  
  > Můžete zkombinovat všechny možnosti ve stejné aplikaci.

## <a name="install-the-nodejs-sdk"></a>Instalace sady Node.js SDK

1. Nainstalujte Git. Pokyny najdete v tématu [Začínáme – instalace Gitu](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

2. Nainstalujte nebo upgradujte na aktuální verzi [Node.js](https://nodejs.org/en/download/). Node.js také zahrnuje Správce balíčků [npm](https://www.npmjs.com/) JavaScriptu.

3. Nainstalujte nebo upgradujte [Visual Studio Code](https://code.visualstudio.com/) a nainstalujte [Node.js rozšíření](https://code.visualstudio.com/docs/nodejs/nodejs-debugging) pro Visual Studio Code.

2.  Nainstalujte klientské balíčky pro správce prostředků centra Azure Stack. Další informace najdete v tématu [instalace klientských knihoven](https://www.npmjs.com/package/@azure/arm-keyvault-profile-2019-03-01-hybrid).

3.  Balíčky, které je potřeba nainstalovat, závisí na verzi profilu, kterou chcete použít. Seznam poskytovatelů prostředků najdete v části [balíčky v npm](#packages-in-npm) .

4. Nainstalujte klientskou knihovnu poskytovatele prostředků pomocí npm. Z příkazového řádku spusťte příkaz: `npm install <package-name>` . Například můžete spustit `npm install @azure/arm-authorization-profile-2019-03-01-hybrid` pro instalaci knihovny zprostředkovatele prostředků autorizace.

5.  Vytvořte si předplatné a poznamenejte si ID předplatného, když použijete sadu SDK. Pokyny najdete v tématu [Vytvoření předplatných nabídek v centru Azure Stack](/azure/azure-stack/azure-stack-subscribe-plan-provision-vm).

6.  Vytvořte instanční objekt a uložte ID klienta a tajný klíč klienta. ID klienta se také označuje jako ID aplikace při vytváření instančního objektu. Pokyny najdete v tématu [poskytnutí přístupu aplikací k rozbočovači Azure Stack](../operator/azure-stack-create-service-principals.md).

7.  Ujistěte se, že váš instanční objekt má v předplatném roli Přispěvatel/vlastník. Pokyny k přiřazení role k instančnímu objektu najdete v tématu [poskytnutí přístupu aplikací k rozbočovači Azure Stack](../operator/azure-stack-create-service-principals.md).

### <a name="nodejs-prerequisites"></a>Node.js předpoklady 

Pokud chcete použít Node.js Azure SDK s centrem Azure Stack, musíte zadat následující hodnoty a pak nastavit hodnoty pomocí proměnných prostředí. Chcete-li nastavit proměnné prostředí, přečtěte si pokyny uvedené v tabulce pro váš operační systém.

| Hodnota | Proměnné prostředí | Popis |
| --- | --- | --- |
| ID tenanta | \_ID tenanta | Hodnota [ID tenanta](/azure/azure-stack/azure-stack-identity-overview)centra Azure Stack. |
| ID klienta | \_ID klienta | ID aplikace instančního objektu se uložilo při vytvoření instančního objektu v předchozí části tohoto dokumentu.  |
| ID předplatného | \_ID předplatného Azure \_ [ID předplatného](../operator/service-plan-offer-subscription-overview.md#subscriptions) je způsob, jakým přistupujete k nabídkám centra Azure Stack.  |
| Tajný klíč klienta | \_tajný klíč aplikace | Tajný kód aplikace instančního objektu uložený při vytvoření instančního objektu |
| Správce prostředků koncový bod | \_koncový bod ARM | Viz [koncový bod správce prostředků centra Azure Stack](/azure/azure-stack/user/azure-stack-version-profiles-ruby#the-azure-stack-hub-resource-manager-endpoint). |

#### <a name="set-your-environmental-variables-for-nodejs"></a>Nastavení proměnných prostředí pro Node.js

Nastavení proměnných prostředí:

  - **Microsoft Windows**  

    Chcete-li nastavit proměnné prostředí, použijte v příkazovém řádku systému Windows následující formát:
      
      `set Tenant_ID=<Your_Tenant_ID>`

  - **systémy macOS, Linux a UNIX**  

    Chcete-li nastavit proměnné prostředí, v příkazovém řádku bash použijte následující formát:

    `export Azure_Tenant_ID=<Your_Tenant_ID>`

**Koncový bod Správce prostředků centra Azure Stack**

Microsoft Azure Správce prostředků je rozhraní pro správu, které správcům umožňuje nasazovat, spravovat a monitorovat prostředky Azure. Azure Resource Manager může tyto úlohy v jedné operaci zpracovat jako skupinu, nikoli jednotlivě.

Informace o metadatech můžete získat z Správce prostředkůho koncového bodu. Koncový bod vrátí soubor JSON s informacemi potřebnými ke spuštění vašeho kódu.

> [!NOTE]  
> **ResourceManagerUrl** v Azure Stack Development Kit (ASDK) je: `https://management.local.azurestack.external` **ResourceManagerUrl** v integrovaných systémech je: `https://management.region.<fqdn>/` , kde `<fqdn>` je váš plně kvalifikovaný název domény.
Načtení požadovaných metadat: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`

Ukázkový soubor JSON:

```JSON  
{
    "galleryEndpoint": "https://portal.local.azurestack.external/",

    "graphEndpoint": "https://graph.windowsNode.js/",

    "portal Endpoint": "https://portal.local.azurestack.external/",

    "authentication": {

        "loginEndpoint": "https://login.windowsNode.js/",

        "audiences": ["https://management.<yourtenant>.onmicrosoft.com/"]

    }

}
```

### <a name="existing-api-profiles"></a>Existující profily rozhraní API

-  **\@Azure/ARM-resourceprovider-Profile-2019-03 -01 – Hybrid**

    Nejnovější profil sestavený pro centrum Azure Stack. Tento profil se používá pro služby, které jsou kompatibilní s Azure Stackm rozbočovačem, pokud jste na 1808 razítko nebo dál.

-  **\@Azure – ARM – prostředek**

    Profil se skládá z nejnovějších verzí všech služeb. Použijte nejnovější verze všech služeb v Azure.

Další informace o centru Azure Stack a profilech rozhraní API najdete v tématu [Přehled profilů rozhraní API](/azure/azure-stack/user/azure-stack-version-profiles#summary-of-api-profiles).

### <a name="azure-nodejs-sdk-api-profile-usage"></a>Použití profilu rozhraní API pro Azure Node.js SDK

K vytvoření instance klienta profilu by se měly použít následující řádky. Tento parametr je vyžadován pouze pro Azure Stack rozbočovač nebo jiné privátní cloudy. Globální Azure už má ve výchozím nastavení tato nastavení s @azure-arm-resource nebo @azure-arm-storage .

```Node.js  
var ResourceManagementClient = require('@azure/arm-resources-profile-hybrid-2019-03-01').ResourceManagementClient;

var StorageManagementClient = require('@azure/arm-storage-profile-2019-03-01-hybrid').StorageManagementClient;
````

Následující kód je nutný k ověření instančního objektu v centru Azure Stack. Vytvoří token podle ID tenanta a základ ověřování, který je specifický pro Azure Stack hub.

```Node.js  
var clientId = process.env['AZURE_CLIENT_ID'];
var tenantId = process.env['AZURE_TENANT_ID']; //"adfs"
var secret = process.env['AZURE_CLIENT_SECRET'];
var subscriptionId = process.env['AZURE_SUBSCRIPTION_ID'];
var base_url = process.env['ARM_ENDPOINT'];
var resourceClient, storageClient;
```

To vám umožní pomocí klientské knihovny profil rozhraní API nasadit vaši aplikaci úspěšně do centra Azure Stack.

Níže uvedený fragment kódu používá koncový bod Azure Resource Manager, který zadáte pro instanci centra Azure Stack a shromažďuje data zobrazená výše, jako je koncový bod galerie, koncový bod grafu, cílové skupiny a koncový bod portálu.

```Node.js  
var map = {};
const fetchUrl = base_url + 'metadata/endpoints?api-version=1.0'
```

## <a name="environment-settings"></a>Nastavení prostředí

Chcete-li ověřit instanční objekt v prostředí Azure Stack hub, použijte následující kód: použití tohoto kódu a nastavení proměnných prostředí v příkazovém řádku automaticky vygeneruje toto mapování pro vývojáře.

```Node.js  
function main() {
  var initializePromise = initialize();
  initializePromise.then(function (result) {
    var userDetails = result;
    console.log("Initialized user details");
    // Use user details from here
    console.log(userDetails)
    map["name"] = "AzureStack"
    map["portalUrl"] = userDetails.portalEndpoint 
    map["resourceManagerEndpointUrl"] = base_url 
    map["galleryEndpointUrl"] = userDetails.galleryEndpoint 
    map["activeDirectoryEndpointUrl"] = userDetails.authentication.loginEndpoint.slice(0, userDetails.authentication.loginEndpoint.lastIndexOf("/") + 1) 
    map["activeDirectoryResourceId"] = userDetails.authentication.audiences[0] 
    map["activeDirectoryGraphResourceId"] = userDetails.graphEndpoint 
    map["storageEndpointSuffix"] = "." + base_url.substring(base_url.indexOf('.'))  
    map["keyVaultDnsSuffix"] = ".vault" + base_url.substring(base_url.indexOf('.')) 
    map["managementEndpointUrl"] = userDetails.authentication.audiences[0] 
    map["validateAuthority"] = "false"
    Environment.Environment.add(map);
```

## <a name="samples-using-api-profiles"></a>Ukázky pomocí profilů rozhraní API

Následující ukázky můžete použít jako referenci pro vytváření řešení s Node.js a profily rozhraní API centra Azure Stack. Ukázky můžete získat z GitHubu v následujících úložištích:

- [Začínáme s poskytovatelem prostředků uzlu úložiště](https://github.com/Azure-Samples/hybrid-storage-nodejs-create-storageaccount)
- [Správa výpočetního uzlu](https://github.com/Azure-Samples/Hybrid-compute-nodejs-create-vm)
- [Prostředky a skupiny uzlu Resource Manageru](https://github.com/Azure-Samples/Hybrid-resourcegroups-nodejs-manageresources)

### <a name="sample-create-storage-account"></a>Ukázka vytvoření účtu úložiště 

1.  Naklonujte úložiště.

    ```bash  
    git clone https://github.com/sijuman/storage-node-resource-provider-getting-started.git
    ```

1. `cd` do klonu úložiště.

2.  Vytvořte instanční objekt Azure a přiřaďte roli pro přístup k předplatnému. Pokyny najdete v tématu [použití Azure PowerShell k vytvoření instančního objektu s certifikátem](/azure/azure-stack/azure-stack-create-service-principals).

3.  Načíst následující požadované hodnoty:
    - ID tenanta
    - ID klienta (ID aplikace)
    - Tajný klíč klienta
    - Azure Subscription ID
    - Koncový bod Správce prostředků centra Azure Stack

4.  Nastavte následující proměnné prostředí pomocí informací, které jste získali z instančního objektu, který jste vytvořili pomocí příkazového řádku:

    > [!NOTE]  
    > Ve Windows použijte místo **exportu** **sadu set** .

    ```bash  
    export TENANT_ID=<your tenant id>
    export CLIENT_ID=<your client id>
    export APPLICATION_SECRET=<your client secret>K
    export AZURE_SUBSCRIPTION_ID=<your subscription id>
    export ARM_ENDPOINT=<your Azure Stack Hub Resource manager URL>
    ```

5.  Otevřete `index.js` soubor ukázkové aplikace.

6.  Nastavte proměnnou umístění na umístění centra Azure Stack. Například, `LOCAL = "local"`.

7.  Nastavte přihlašovací údaje, které vám umožní ověřit Azure Stack centra. Tato část kódu je obsažena v této ukázce souboru index.js.

    ```Node.js  
    var clientId = process.env['CLIENT_ID'];
    var tenantId = process.env['TENANT_ID']; //"adfs"
    var secret = process.env['APPLICATION_SECRET'];
    var subscriptionId = process.env['AZURE_SUBSCRIPTION_ID'];
    var base_url = process.env['ARM_ENDPOINT'];
    var resourceClient, storageClient;
    ```

8.  Kontroluje, zda jste nastavili správné klientské knihovny pomocí kombinace klientských knihoven uvedených výše. V této ukázce jsme použili následující:

    ```Node.js
    var ResourceManagementClient = require('@azure/arm-resources-profile-hybrid-2019-03-01').ResourceManagementClient;
    var StorageManagementClient = require('@azure/arm-storage-profile-2019-03-01-hybrid').StorageManagementClient;
    ```

9.  Pomocí [npm modulů hledání](https://www.npmjs.com/package/@azure/arm-keyvault-profile-2019-03-01-hybrid)vyhledejte **2019-03-01 – Hybrid** a nainstalujte balíčky přidružené k tomuto profilu pro poskytovatele prostředků COMPUTE, sítě, úložiště, trezoru klíčů a App Services.

    To se dá udělat tak, že otevřete příkazový řádek, přesměrujete ho na kořenovou složku úložiště a spustíte `npm install @azure/arm-keyvault-profile-2019-03-01-hybrid` pro každého poskytovatele prostředků.

10.  Na příkazovém řádku spusťte příkaz `npm install` pro instalaci všech modulů node.js.

11.  Spusťte ukázku.

        ```Node.js  
        node index.js
        ```

12.  Pro vyčištění po index.js spusťte čisticí skript.

        ```Node.js  
        Node cleanup.js <resourceGroupName> <storageAccountName>
        ```

13.  Ukázka byla úspěšně dokončena. Další informace o ukázce najdete níže.

### <a name="what-does-indexjs-do"></a>Co index.js udělat?

Ukázka vytvoří nový účet úložiště, vypíše účty úložiště v předplatném nebo skupině prostředků, vypíše klíče účtu úložiště, znovu vygeneruje klíče účtu úložiště, získá vlastnosti účtu úložiště, aktualizuje SKLADOVOU položku účtu úložiště a ověří dostupnost názvu účtu úložiště.

Ukázka se spustí přihlášením pomocí instančního objektu a vytvořením **ResourceManagementClient** a **StorageManagementClient** objektů pomocí vašich PŘIHLAŠOVACÍch údajů a ID předplatného.

Ukázka pak nastaví skupinu prostředků, ve které se vytvoří nový účet úložiště.

```Node.js  
function createResourceGroup(callback) {
  var groupParameters = { location: location, tags: { sampletag: 'sampleValue' } };
  console.log('\nCreating resource group: ' + resourceGroupName);
  return resourceClient.resourceGroups.createOrUpdate(resourceGroupName, groupParameters, callback);
}
```

### <a name="create-a-new-storage-account"></a>Vytvoření nového účtu úložiště

V dalším kroku vytvoří ukázka nový účet úložiště, který je přidružený ke skupině prostředků vytvořené v předchozím kroku.

V takovém případě se název účtu úložiště vygeneruje náhodně, aby se zajistila jedinečnost. Pokud ale v předplatném už existuje účet se stejným názvem, volání k vytvoření nového účtu úložiště se nezdaří.

```Node.js  
var createParameters = {
    location: location,
    sku: {
        name: accType,
    },
    kind: 'Storage',
    tags: {
        tag1: 'val1',
        tag2: 'val2'
    }
};


console.log('\\n--&gt;Creating storage account: ' + storageAccountName + ' with parameters:\\n' + util.inspect(createParameters));

return storageClient.storageAccounts.create(resourceGroupName, storageAccountName, createParameters, callback);
```

### <a name="list-storage-accounts-in-the-subscription-or-resource-group"></a>Výpis účtů úložiště v předplatném nebo skupině prostředků

Ukázka obsahuje seznam všech účtů úložiště v daném předplatném:

```Node.js  
console.log('\\n--&gt;Listing storage accounts in the current subscription.');

return storageClient.storageAccounts.list(callback);

It also lists storage accounts in the resource group:

    console.log('\\n--&gt;Listing storage accounts in the resourceGroup : ' + resourceGroupName);

return storageClient.storageAccounts.listByResourceGroup(resourceGroupName, callback);
```

### <a name="read-and-regenerate-storage-account-keys"></a>Čtení a obnovení klíčů účtu úložiště

Ukázka obsahuje klíče účtu úložiště pro nově vytvořený účet úložiště a skupinu prostředků:

```Node.js  
console.log('\\n--&gt;Listing storage account keys for account: ' + storageAccountName);

return storageClient.storageAccounts.listKeys(resourceGroupName, storageAccountName, callback);
```

Také znovu vygeneruje přístupové klíče účtu:

```Node.js  
console.log('\\n--&gt;Regenerating storage account keys for account: ' + storageAccountName);

return storageClient.storageAccounts.regenerateKey(resourceGroupName, storageAccountName, 'key1', callback);
```

### <a name="get-storage-account-properties"></a>Získat vlastnosti účtu úložiště

Ukázka přečte vlastnosti účtu úložiště:

```Node.js  
console.log('\\n--&gt;Getting info of storage account: ' + storageAccountName);

return storageClient.storageAccounts.getProperties(resourceGroupName, storageAccountName, callback);
```

### <a name="check-storage-account-name-availability"></a>Ověřit dostupnost názvu účtu úložiště

Ukázka ověří, jestli je daný název účtu úložiště dostupný v Azure:

```Node.js  
console.log('\\n--&gt;Checking if the storage account name : ' + storageAccountName + ' is available.');

return storageClient.storageAccounts.checkNameAvailability(storageAccountName, callback);
```

### <a name="delete-the-storage-account-and-resource-group"></a>Odstraní účet úložiště a skupinu prostředků.

Spuštění cleanup.js odstraní účet úložiště, který ukázka vytvořila:

```Node.js  
console.log('\\nDeleting storage account : ' + storageAccountName);
return storageClient.storageAccounts.deleteMethod(resourceGroupName, storageAccountName, callback);
```

Odstraní také skupinu prostředků, kterou vytvořila Ukázka:

```Node.js  
console.log('\\nDeleting resource group: ' + resourceGroupName);

return resourceClient.resourceGroups.deleteMethod(resourceGroupName, callback);
```

## <a name="next-steps"></a>Další kroky

Další informace o profilech rozhraní API najdete v těchto tématech:

- [Správa profilů verzí rozhraní API v centru Azure Stack](azure-stack-version-profiles.md)
- [Verze rozhraní API poskytovatele prostředků podporované profily](azure-stack-profiles-azure-resource-manager-versions.md)
