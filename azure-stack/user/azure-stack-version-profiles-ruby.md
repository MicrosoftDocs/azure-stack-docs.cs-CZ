---
title: Použití profilů verzí rozhraní API s Ruby v Azure Stack hub
description: Naučte se používat profily verzí rozhraní API s Ruby v Azure Stack hub.
author: sethmanheim
ms.topic: article
ms.date: 01/23/2020
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: 1236e501ce09b5e81124f8bc87821d1b5f11cfaa
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76883099"
---
# <a name="use-api-version-profiles-with-ruby-in-azure-stack-hub"></a>Použití profilů verzí rozhraní API s Ruby v Azure Stack hub

## <a name="ruby-and-api-version-profiles"></a>Profily verze Ruby a API

Sada Ruby SDK pro centrum Azure Stack Správce prostředků poskytuje nástroje, které vám pomůžou sestavovat a spravovat infrastrukturu. Poskytovatelé prostředků v sadě SDK zahrnují výpočetní prostředky, virtuální sítě a úložiště s využitím jazyka Ruby. Profily rozhraní API v sadě Ruby SDK umožňují vývoj hybridního cloudu tím, že vám pomůžou přepínat mezi globálním prostředky a prostředky Azure v centru Azure Stack.

Profil rozhraní API je kombinací poskytovatelů prostředků a verzí služby. K kombinování různých typů prostředků můžete použít profil rozhraní API.

- Chcete-li použít nejnovější verze všech služeb, použijte **nejnovější** profil kumulativního Gem sady Azure SDK.
- Pokud chcete používat služby kompatibilní s rozbočovačem Azure Stack, použijte profil **V2019_03_01_Hybrid** nebo **V2018_03_01** kumulativního Gem sady Azure SDK.
- Pokud chcete použít nejnovější **verzi služby API** , použijte **nejnovější** profil konkrétní gem. Pokud například chcete použít nejnovější verzi služby COMPUTE **API** , použijte **nejnovější** profil Gem **COMPUTE** .
- Pokud chcete pro službu použít konkrétní **verzi rozhraní API** , použijte konkrétní verze rozhraní API definované uvnitř rozhraní gem.

> [!NOTE]
> Můžete zkombinovat všechny možnosti ve stejné aplikaci.

## <a name="install-the-azure-ruby-sdk"></a>Instalace Azure Ruby SDK

- Pomocí oficiálních pokynů nainstalujte [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
- Podle oficiálních pokynů nainstalujte [Ruby](https://www.ruby-lang.org/en/documentation/installation/).
  - Při instalaci nástroje vyberte možnost **Přidat Ruby do proměnné cesty**.
  - Po zobrazení výzvy při instalaci Ruby nainstalujte vývojovou sadu.
  - Dále nainstalujte sadu prostředků pomocí následujícího příkazu: 

       ```Ruby
       Gem install bundler
       ```

- Pokud není k dispozici, vytvořte předplatné a uložte ID předplatného pro pozdější použití. Pokyny k vytvoření předplatného najdete v článku o [Vytvoření předplatných pro nabídky v Azure Stackovém centru](../operator/azure-stack-subscribe-plan-provision-vm.md) .
- Vytvořte instanční objekt a uložte jeho ID a tajný klíč. Pokyny k vytvoření instančního objektu pro centrum Azure Stack jsou v článku [použití identity aplikace k přístupu k prostředkům](../operator/azure-stack-create-service-principals.md) .
- Ujistěte se, že váš instanční objekt má přiřazenou roli Přispěvatel/vlastník v rámci vašeho předplatného. Pokyny k přiřazení role k instančnímu objektu jsou v tématu [použití identity aplikace pro přístup k prostředkům](../operator/azure-stack-create-service-principals.md).

## <a name="install-the-rubygem-packages"></a>Instalace balíčků RubyGem

Balíčky Azure RubyGem můžete nainstalovat přímo.

```Ruby  
gem install azure_mgmt_compute
gem install azure_mgmt_storage
gem install azure_mgmt_resources
gem install azure_mgmt_network
```

Případně je můžete použít ve své Gemfile.

```Ruby
gem 'azure_mgmt_storage'
gem 'azure_mgmt_compute'
gem 'azure_mgmt_resources'
gem 'azure_mgmt_network'
```

Sada Azure Resource Manager Ruby SDK je ve verzi Preview a pravděpodobně bude mít za následek porušení změn rozhraní v nadcházejících verzích. Zvýšené číslo v dílčí verzi může označovat zásadní změny.

## <a name="use-the-azure_sdk-gem"></a>Použití azure_sdk Gem

**Azure_sdk** Gem je souhrn všech podporovaných Gems v sadě Ruby SDK. Tento Gem se skládá z **nejnovějšího** profilu , který podporuje nejnovější verzi všech služeb. Zahrnuje profily se správou verzí **V2017_03_09** a **V2019_03_01_Hybrid**, které jsou sestavené pro Azure Stack hub.

Gem kumulativního azure_sdk můžete nainstalovat pomocí následujícího příkazu:  

```Ruby  
gem install 'azure_sdk'
```

## <a name="prerequisites"></a>Požadavky

Pokud chcete použít sadu Ruby Azure SDK s Azure Stack hub, musíte zadat následující hodnoty a pak hodnoty nastavit pomocí proměnných prostředí. Chcete-li nastavit proměnné prostředí, přečtěte si pokyny uvedené v tabulce pro konkrétní operační systém.

| Hodnota | Proměnné prostředí | Popis |
| --- | --- | --- |
| ID tenanta | `AZURE_TENANT_ID` | Vaše [ID tenanta](../operator/azure-stack-identity-overview.md)centra Azure Stack. |
| ID klienta | `AZURE_CLIENT_ID` | ID aplikace instančního objektu se uložilo při vytvoření instančního objektu v předchozí části tohoto článku.  |
| ID předplatného | `AZURE_SUBSCRIPTION_ID` | [ID předplatného](../operator/service-plan-offer-subscription-overview.md#subscriptions) se používá pro přístup k nabídkám v centru Azure Stack. |
| Tajný kód klienta | `AZURE_CLIENT_SECRET` | Tajný kód aplikace instančního objektu se uložil při vytvoření objektu služby. |
| Koncový bod Resource Manageru | `ARM_ENDPOINT` | Viz [koncový bod správce prostředků centra Azure Stack](#the-azure-stack-hub-resource-manager-endpoint).  |

### <a name="the-azure-stack-hub-resource-manager-endpoint"></a>Koncový bod Správce prostředků centra Azure Stack

Microsoft Azure Správce prostředků je rozhraní pro správu, které správcům umožňuje nasazovat, spravovat a monitorovat prostředky Azure. Azure Resource Manageru dokáže zpracovat tyto úkoly, jako se skupinou, nikoli samostatně, v rámci jedné operace.

Informace o metadatech můžete získat z Správce prostředkůho koncového bodu. Koncový bod vrátí soubor JSON s informacemi potřebnými ke spuštění vašeho kódu.

 > [!NOTE]  
 > **ResourceManagerUrl** v Azure Stack Development Kit (ASDK) je: `https://management.local.azurestack.external/` **ResourceManagerUrl** v integrovaných systémech je: `https://management.region.<fqdn>/`, kde `<fqdn>` je váš plně kvalifikovaný název domény.  
 > Načtení požadovaných metadat: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`
  
 Ukázkový soubor JSON:

 ```json
 {
   "galleryEndpoint": "https://portal.local.azurestack.external:30015/",  
   "graphEndpoint": "https://graph.windows.net/",  
   "portal Endpoint": "https://portal.local.azurestack.external/",
   "authentication": {
     "loginEndpoint": "https://login.windows.net/",
     "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
   }
 }
```

### <a name="set-environment-variables"></a>Nastavení proměnných prostředí

#### <a name="microsoft-windows"></a>Microsoft Windows

Chcete-li nastavit proměnné prostředí, použijte následující formát v příkazovém řádku systému Windows:

```shell
set AZURE_TENANT_ID=<YOUR_TENANT_ID>
```

#### <a name="macos-linux-and-unix-based-systems"></a>systémy macOS, Linux a UNIX

V systémech UNIX použijte následující příkaz:

```bash
export AZURE_TENANT_ID=<YOUR_TENANT_ID>
```

## <a name="existing-api-profiles"></a>Existující profily rozhraní API

Gem kumulativního **Azure_sdk** má následující 3 profily:

- **V2019_03_01_Hybrid**: Profil sestavený pro Azure Stack centrum. Tento profil použijte pro všechny nejnovější verze služeb, které jsou k dispozici ve službě Azure Stack hub verze 1904 nebo novější.
- **V2017_03_09**: Profil sestavený pro Azure Stack centrum. Tento profil použijte pro služby, které jsou nejvíce kompatibilní s Azure Stack centra verze 1808 nebo starším.
- **Nejnovější**: Profil se skládá z nejnovějších verzí všech služeb. Použijte nejnovější verze všech služeb.

Další informace o centru Azure Stack a profilech rozhraní API najdete v tématu [Přehled profilů rozhraní API](azure-stack-version-profiles.md#summary-of-api-profiles).

## <a name="azure-ruby-sdk-api-profile-usage"></a>Použití profilu rozhraní API pro Azure Ruby SDK

Pomocí následujícího kódu vytvořte instanci klienta profilu. Tento parametr je vyžadován pouze pro Azure Stack rozbočovač nebo jiné privátní cloudy. Global Azure už má tato nastavení ve výchozím nastavení.

```Ruby  
active_directory_settings = get_active_directory_settings(ENV['ARM_ENDPOINT'])

provider = MsRestAzure::ApplicationTokenProvider.new(
  ENV['AZURE_TENANT_ID'],
  ENV['AZURE_CLIENT_ID'],
  ENV['AZURE_CLIENT_SECRET'],
  active_directory_settings
)
credentials = MsRest::TokenCredentials.new(provider)
options = {
  credentials: credentials,
  subscription_id: subscription_id,
  active_directory_settings: active_directory_settings,
  base_url: ENV['ARM_ENDPOINT']
}

# Target profile built for Azure Stack Hub
client = Azure::Resources::Profiles::V2019_03_01_Hybrid::Mgmt::Client.new(options)
```

Profilový klient se dá použít pro přístup k jednotlivým poskytovatelům prostředků, jako jsou výpočetní prostředky, úložiště a síť:

```Ruby  
# To access the operations associated with Compute
profile_client.compute.virtual_machines.get 'RESOURCE_GROUP_NAME', 'VIRTUAL_MACHINE_NAME'

# Option 1: To access the models associated with Compute
purchase_plan_obj = profile_client.compute.model_classes.purchase_plan.new

# Option 2: To access the models associated with Compute
# Notice Namespace: Azure::Profiles::<Profile Name>::<Service Name>::Mgmt::Models::<Model Name>
purchase_plan_obj = Azure::Profiles::V2019_03_01_Hybrid::Compute::Mgmt::Models::PurchasePlan.new
```

## <a name="define-azure-stack-hub-environment-setting-functions"></a>Definování funkcí nastavení prostředí centra Azure Stack

Chcete-li ověřit instanční objekt v prostředí Azure Stack hub, definujte koncové body pomocí `get_active_directory_settings()`. Tato metoda používá proměnnou prostředí **ARM_Endpoint** , kterou jste nastavili dříve:

```Ruby  
# Get Authentication endpoints using Arm Metadata Endpoints
def get_active_directory_settings(armEndpoint)
  settings = MsRestAzure::ActiveDirectoryServiceSettings.new
  response = Net::HTTP.get_response(URI("#{armEndpoint}/metadata/endpoints?api-version=1.0"))
  status_code = response.code
  response_content = response.body
  unless status_code == "200"
    error_model = JSON.load(response_content)
    fail MsRestAzure::AzureOperationError.new("Getting Azure Stack Hub Metadata Endpoints", response, error_model)
  end
  result = JSON.load(response_content)
  settings.authentication_endpoint = result['authentication']['loginEndpoint'] unless result['authentication']['loginEndpoint'].nil?
  settings.token_audience = result['authentication']['audiences'][0] unless result['authentication']['audiences'][0].nil?
  settings
end
```

## <a name="samples-using-api-profiles"></a>Ukázky pomocí profilů rozhraní API

Použijte následující ukázky na GitHubu jako odkazy k vytváření řešení pomocí profilů rozhraní API Ruby a Azure Stack hub:

- [Spravujte prostředky Azure a skupiny prostředků pomocí Ruby](https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Resources-And-Groups).
- [Správa virtuálních počítačů pomocí Ruby](https://github.com/Azure-Samples/Hybrid-Compute-Ruby-Manage-VM) (ukázka, která používá 2019-03-01 – hybridní profil pro cílení na nejnovější verze rozhraní API, které podporuje centrum Azure Stack).
- [Nasaďte virtuální počítač s povoleným SSH se šablonou v Ruby](https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Template-Deployment).

### <a name="sample-resource-manager-and-groups"></a>Ukázka Správce prostředků a skupin

Pokud chcete ukázku spustit, ujistěte se, že máte nainstalovanou Ruby. Pokud používáte Visual Studio Code, Stáhněte si také rozšíření Ruby SDK.

> [!NOTE]  
> Úložiště pro ukázku je [Hybrid-Resource-Manager-Ruby-Resources-a-Groups](https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Resources-And-Groups).

1. Naklonujte úložiště:

   ```bash
   git clone https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Resources-And-Groups.git
   ```

2. Nainstalujte závislosti pomocí sady prostředků:

   ```Bash
   cd Hybrid-Resource-Manager-Ruby-Resources-And-Groups
   bundle install
   ```

3. Vytvořte instanční objekt Azure pomocí PowerShellu a načtěte potřebné hodnoty.

   Pokyny k vytvoření instančního objektu najdete v tématu [pomocí prostředí Azure PowerShell k vytvoření instančního objektu s certifikátem](../operator/azure-stack-create-service-principals.md).

   Požadované hodnoty:

   - ID tenanta
   - ID klienta
   - Tajný klíč klienta
   - ID předplatného
   - Koncový bod Resource Manageru

   Nastavte následující proměnné prostředí pomocí informací získaných z vytvořeného objektu služby:

   - `export AZURE_TENANT_ID={your tenant ID}`
   - `export AZURE_CLIENT_ID={your client ID}`
   - `export AZURE_CLIENT_SECRET={your client secret}`
   - `export AZURE_SUBSCRIPTION_ID={your subscription ID}`
   - `export ARM_ENDPOINT={your Azure Stack Hub Resource Manager URL}`

   > [!NOTE]  
   > Ve Windows použijte místo `export``set`.

4. Zajistěte, aby byla proměnná umístění nastavena na umístění centra Azure Stack. například `LOCAL="local"`.

5. Pokud chcete cílit na správné koncové body služby Active Directory, přidejte následující řádek kódu, pokud používáte Azure Stack hub nebo jiné privátní cloudy:

   ```Ruby  
   active_directory_settings = get_active_directory_settings(ENV['ARM_ENDPOINT'])
   ```

6. V proměnné `options` přidejte nastavení služby Active Directory a základní adresu URL pro práci se službou Azure Stack hub:

   ```ruby  
   options = {
   credentials: credentials,
   subscription_id: subscription_id,
   active_directory_settings: active_directory_settings,
   base_url: ENV['ARM_ENDPOINT']
   }
   ```

7. Vytvořte profil klienta, který se zaměřuje na profil centra Azure Stack:

   ```ruby  
   client = Azure::Resources::Profiles::V2019_03_01_Hybrid::Mgmt::Client.new(options)
   ```

8. Chcete-li ověřit instanční objekt pomocí centra Azure Stack, koncové body by měly být definovány pomocí **get_active_directory_settings ()** . Tato metoda používá proměnnou prostředí **ARM_Endpoint** , kterou jste nastavili dříve:

   ```ruby  
   def get_active_directory_settings(armEndpoint)
     settings = MsRestAzure::ActiveDirectoryServiceSettings.new
     response = Net::HTTP.get_response(URI("#{armEndpoint}/metadata/endpoints?api-version=1.0"))
     status_code = response.code
     response_content = response.body
     unless status_code == "200"
       error_model = JSON.load(response_content)
       fail MsRestAzure::AzureOperationError.new("Getting Azure Stack Hub Metadata Endpoints", response, error_model)
     end
     result = JSON.load(response_content)
     settings.authentication_endpoint = result['authentication']['loginEndpoint'] unless result['authentication']['loginEndpoint'].nil?
     settings.token_audience = result['authentication']['audiences'][0] unless result['authentication']['audiences'][0].nil?
     settings
   end
   ```

9. Spusťte ukázku.

   ```Ruby
   bundle exec ruby example.rb
   ```

## <a name="next-steps"></a>Další kroky

- [Instalace PowerShellu pro centrum Azure Stack](../operator/azure-stack-powershell-install.md)
- [Konfigurace prostředí PowerShell uživatele centra Azure Stack](azure-stack-powershell-configure-user.md)  
