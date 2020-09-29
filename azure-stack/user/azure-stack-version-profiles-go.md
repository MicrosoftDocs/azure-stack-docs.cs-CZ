---
title: Použití profilů verzí rozhraní API s nástrojem přejít v Azure Stack hub
description: Naučte se používat profily verzí rozhraní API s nástrojem přejít v Azure Stack hub.
author: sethmanheim
ms.topic: article
ms.date: 09/02/2020
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/26/2019
ms.openlocfilehash: 3b8df9de2975c7ba0e6eefdb10a2731cd5d47ca6
ms.sourcegitcommit: 7c01ab4b2e2250a7acd67d1c5ba27d15c1e8bce0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2020
ms.locfileid: "89448669"
---
# <a name="use-api-version-profiles-with-go-in-azure-stack-hub"></a>Použití profilů verzí rozhraní API s nástrojem přejít v Azure Stack hub

## <a name="go-and-version-profiles"></a>Profily verzí a

Profil je kombinací různých typů prostředků s různými verzemi z různých služeb. Použití profilu vám pomůže kombinovat a porovnat mezi různými typy prostředků. Profily můžou poskytovat následující výhody:

- Stabilita vaší aplikace při zamykání na konkrétní verze rozhraní API
- Kompatibilita vaší aplikace s Azure Stack hub a místními datacentry Azure.

V sadě SDK pro cestu jsou profily k dispozici v cestě k profilům. Čísla verzí profilů jsou označená ve formátu **RRRR-MM-DD** . Nejnovější verze profilu rozhraní API centra Azure Stack je **2019-03-01** pro Azure Stack centra verze 1904 nebo novější. Pokud chcete z profilu importovat danou službu, importujte odpovídající modul z profilu. Pokud například chcete importovat **výpočetní** službu z profilu **2019-03-01** , použijte následující kód:

```go
import "github.com/Azure/azure-sdk-for-go/profiles/2019-03-01/compute/mgmt/compute"
```

## <a name="install-the-azure-sdk-for-go"></a>Instalace Azure SDK for Go

1. Nainstalujte Git. Pokyny najdete v tématu [Začínáme – instalace Gitu](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
2. Nainstalujte [jazyk programovacího jazyka](https://golang.org/dl). Profily rozhraní API pro Azure vyžadují verzi verze 1,9 nebo novější.
3. Nainstalujte sadu Azure SDK a její závislosti spuštěním následujícího příkazu bash:

   ```bash
   go get -u -d github.com/Azure/azure-sdk-for-go/...
   ```

### <a name="the-go-sdk"></a>Sada SDK přejít

Další informace o sadě Azure cestách SDK najdete na následujících odkazech:

- Sada Azure SDK pro [instalaci Azure SDK pro go](/go/azure/azure-sdk-go-install).
- Azure přejít SDK je veřejně dostupný na GitHubu v úložišti [Azure-SDK-for-na cestách](https://github.com/Azure/azure-sdk-for-go) .

### <a name="go-autorest-dependencies"></a>Přejít – autov závislosti na REST

Sada SDK sady SDK závisí na modulech Azure **Přejít – AutoRest** k posílání žádostí REST na Azure Resource Manager koncových bodů. Musíte importovat závislosti modulu Azure **Přejít-AutoRest** z Azure do [Azure – AutoRest na GitHubu](https://github.com/Azure/go-autorest). Příkazy install bash najdete v části **install** .

## <a name="how-to-use-go-sdk-profiles-on-azure-stack-hub"></a>Jak používat profily sady SDK pro cestách v centru Azure Stack

Chcete-li spustit ukázku kódu jazyka přejít v Azure Stackovém centru, postupujte podle následujících kroků:

1. Nainstalujte Azure SDK pro Go a jeho závislosti. Pokyny najdete v předchozí části [instalace Azure SDK pro go](#install-the-azure-sdk-for-go).
2. Získat informace o metadatech z Správce prostředkůho koncového bodu. Koncový bod vrátí soubor JSON s informacemi potřebnými ke spuštění kódu vašeho přechodu.

   > [!NOTE]  
   > **ResourceManagerUrl** v Azure Stack Development Kit (ASDK) je:`https://management.local.azurestack.external/`  
   > **ResourceManagerUrl** v integrovaných systémech je:`https://management.<region>.<fqdn>/`  
   > Načtení požadovaných metadat: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`
  
   Ukázkový soubor JSON:

   ```json
   { "galleryEndpoint": "https://portal.local.azurestack.external:30015/",  
     "graphEndpoint": "https://graph.windows.net/",  
     "portal Endpoint": "https://portal.local.azurestack.external/",
     "authentication": {
       "loginEndpoint": "https://login.windows.net/",
       "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
     }
   }
   ```

3. Pokud není k dispozici, vytvořte odběr a uložte ID předplatného, které chcete použít později. Informace o vytváření předplatného najdete [v tématu Vytvoření předplatných nabídek v centru Azure Stack](../operator/azure-stack-subscribe-plan-provision-vm.md).

4. Vytvořte instanční objekt, který používá tajný klíč klienta, s oborem **předplatného** a rolí **vlastníka** . Uložte ID objektu služby a tajný klíč. Informace o vytváření instančního objektu pro centrum Azure Stack najdete v tématu [použití identity aplikace pro přístup k prostředkům](../operator/azure-stack-create-service-principals.md). Vaše prostředí centra Azure Stack je teď nastavené.

5. Importujte modul služby z profilu sady SDK přejít do kódu. Aktuální verze profilu centra Azure Stack je **2019-03-01**. Pokud například chcete importovat síťový modul z typu profilu **2019-03-01** , použijte následující kód:

   ```go
   package main
    import "github.com/Azure/azure-sdk-for-go/profiles/2019-03-01/network/mgmt/network"
   ```

6. Ve své funkci vytvořte a ověřte klienta pomocí **nového** volání klientské funkce. Chcete-li vytvořit klienta virtuální sítě, použijte následující kód:  

   ```go
   package main

   import "github.com/Azure/azure-sdk-for-go/profiles/2019-03-01/network/mgmt/network"

   func main() {
      vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
      vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
   ```

   Nastavte `<baseURI>` na hodnotu **ResourceManagerUrl** použitou v kroku 2. Nastavte `<subscriptionID>` na **SubscriptionId** hodnotu uloženou z kroku 3.

   Chcete-li vytvořit token, přečtěte si další část.  

7. Vyvolejte metody rozhraní API pomocí klienta, kterého jste vytvořili v předchozím kroku. Pokud například chcete vytvořit virtuální síť pomocí klienta z předchozího kroku, přečtěte si následující příklad:

   ```go
   package main

   import "github.com/Azure/azure-sdk-for-go/profiles/2019-03-01/network/mgmt/network"
   func main() {
   vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
   vnetClient .Authorizer = autorest.NewBearerAuthorizer(token)

   vnetClient .CreateOrUpdate( )
   ```

Úplný příklad vytvoření virtuální sítě v centru Azure Stack s využitím profilu sady SDK na cestách najdete v [příkladu](#example).

## <a name="authentication"></a>Authentication

Chcete-li získat vlastnost **autorizovat** z Azure Active Directory pomocí sady SDK, nainstalujte moduly automatického **REST** . Tyto moduly by měly být již nainstalovány s instalací sady SDK "Přejít na sadu SDK". Pokud ne, nainstalujte [ověřovací balíček z GitHubu](https://github.com/Azure/go-autorest/tree/master/autorest/adal).

Pro klienta prostředků musí být povolený autorizačníer. Existují různé způsoby, jak získat tokeny autorizace v centru Azure Stack pomocí přihlašovacích údajů klienta:

1. Pokud je k dispozici instanční objekt s rolí vlastníka v předplatném, přeskočte tento krok. V opačném případě se podívejte na téma [použití identity aplikace pro přístup k prostředkům](../operator/azure-stack-create-service-principals.md) , kde najdete pokyny k vytvoření instančního objektu, který používá tajný klíč klienta, a nápovědu k přiřazení role "vlastník" oboru pro vaše předplatné. Nezapomeňte zachytit ID a tajný klíč aplikace instančního objektu.

2. Importujte balíček **ADAL** z možnosti **Přejít-AutoRest** do kódu.

   ```go
   package main
   import "github.com/Azure/go-autorest/autorest/adal"
   ```

3. Vytvořte **oauthConfig** pomocí metody NewOAuthConfig z modulu **ADAL** .

   ```go
   package main

   import "github.com/Azure/go-autorest/autorest/ada1"

   func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
   }
   ```

   Nastavte `<activeDirectoryEndpoint>` na hodnotu `loginEndpoint` vlastnosti z `ResourceManagerUrl` metadat načtených v předchozí části tohoto dokumentu. Nastavte `<tenantID>` hodnotu na ID tenanta centra Azure Stack.

4. Nakonec vytvořte token objektu služby pomocí `NewServicePrincipalToken` metody z modulu **ADAL** :

   ```go
   package main

   import "github.com/Azure/go-autorest/autorest/adal"

   func CreateToken() (adal.OAuthTokenProvider, error) {
       var token adal.OAuthTokenProvider
       oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
       token, err = adal.NewServicePrincipalToken(
           *oauthConfig,
           clientID,
           clientSecret,
           activeDirectoryResourceID)
       return token, err
   ```

    Nastavte `<activeDirectoryResourceID>` na jednu z hodnot v seznamu "cílová" z metadat **ResourceManagerUrl** získaných v předchozí části tohoto článku.
    Nastaví `<clientID>` se na ID aplikace instančního objektu uložené při vytvoření instančního objektu v předchozí části tohoto článku.
    Nastaví `<clientSecret>` se na tajný klíč aplikace instančního objektu uložený při vytvoření instančního objektu v předchozí části tohoto článku.

## <a name="example"></a>Příklad

Tento příklad ukazuje ukázku kódu pro přechod, který vytváří virtuální síť v centru Azure Stack. Kompletní příklady sady SDK pro sadu SDK najdete v části [úložiště ukázek pro Azure přejděte na Azure](https://github.com/Azure-Samples/azure-sdk-for-go-samples). Ukázky centra Azure Stack jsou k dispozici v rámci hybridní cesty v rámci složek služby úložiště.

> [!NOTE]  
> Chcete-li spustit kód v tomto příkladu, ověřte, zda použité předplatné má poskytovatele **síťových** prostředků uvedené jako **registrované**. Pokud ho chcete ověřit, hledejte předplatné na portálu centra Azure Stack a vyberte **poskytovatelé prostředků.**

1. Importujte požadované balíčky do kódu. K importu síťového modulu použijte nejnovější dostupný profil v Azure Stack hub:

   ```go
   package main

   import (
       "context"
       "fmt"
       "github.com/Azure/azure-sdk-for-go/profiles/2019-03-01/network/mgmt/network"
       "github.com/Azure/go-autorest/autorest"
       "github.com/Azure/go-autorest/autorest/adal"
       "github.com/Azure/go-autorest/autorest/to"
   )
   ```

2. Definujte proměnné prostředí. Pokud chcete vytvořit virtuální síť, musíte mít skupinu prostředků.

   ```go
   var (
       activeDirectoryEndpoint = "yourLoginEndpointFromResourceManagerUrlMetadata"
       tenantID = "yourAzureStackTenantID"
       clientID = "yourServicePrincipalApplicationID"
       clientSecret = "yourServicePrincipalSecret"
       activeDirectoryResourceID = "yourAudienceFromResourceManagerUrlMetadata"
       subscriptionID = "yourSubscriptionID"
       baseURI = "yourResourceManagerURL"
       resourceGroupName = "existingResourceGroupName"
   )
   ```

3. Teď, když jste definovali proměnné prostředí, přidejte metodu pro vytvoření ověřovacího tokenu pomocí balíčku **ADAL** . Další informace o ověřování najdete v předchozí části.

   ```go
   //CreateToken creates a service principal token
   func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
      token, err = adal.NewServicePrincipalToken(
          *oauthConfig,
          clientID,
          clientSecret,
          activeDirectoryResourceID)
      return token, err
   }
   ```

4. Přidejte `main` metodu. `main`Metoda nejprve získá token pomocí metody, která je definována v předchozím kroku. Pak vytvoří klienta pomocí síťového modulu z profilu. Nakonec vytvoří virtuální síť.

   ```go
   package main

   import (
      "context"
      "fmt"
      "github.com/Azure/azure-sdk-for-go/profiles/2019-03-01/network/mgmt/network"
      "github.com/Azure/go-autorest/autorest"
      "github.com/Azure/go-autorest/autorest/adal"
      "github.com/Azure/go-autorest/autorest/to"
   )

   var (
      activeDirectoryEndpoint = "yourLoginEndpointFromResourceManagerUrlMetadata"
      tenantID = "yourAzureStackTenantID"
      clientID = "yourServicePrincipalApplicationID"
      clientSecret = "yourServicePrincipalSecret"
      activeDirectoryResourceID = "yourAudienceFromResourceManagerUrlMetadata"
     subscriptionID = "yourSubscriptionID"
     baseURI = "yourResourceManagerURL"
     resourceGroupName = "existingResourceGroupName"
   )

   //CreateToken creates a service principal token
   func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
      token, err = adal.NewServicePrincipalToken(
          *oauthConfig,
          clientID,
          clientSecret,
          activeDirectoryResourceID)
      return token, err
   }

   func main() {
      token, _ := CreateToken()
      vnetClient := network.NewVirtualNetworksClientWithBaseURI(baseURI, subscriptionID)
      vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
      future, _ := vnetClient.CreateOrUpdate(
          context.Background(),
          resourceGroupName,
          "sampleVnetName",
          network.VirtualNetwork{
              Location: to.StringPtr("local"),
              VirtualNetworkPropertiesFormat: &network.VirtualNetworkPropertiesFormat{
                  AddressSpace: &network.AddressSpace{
                      AddressPrefixes: &[]string{"10.0.0.0/8"},
                  },
                  Subnets: &[]network.Subnet{
                      {
                          Name: to.StringPtr("subnetName"),
                          SubnetPropertiesFormat: &network.SubnetPropertiesFormat{
                              AddressPrefix: to.StringPtr("10.0.0.0/16"),
                          },
                      },
                  },
              },
          })
      err := future.WaitForCompletionRef(context.Background(), vnetClient.Client)
      if err != nil {
          fmt.Printf(err.Error())
          return
      }
   }
   ```

K dispozici jsou některé ukázky kódu pro centra Azure Stack s využitím sady SDK pro:

- [Vytvořit virtuální počítač](https://github.com/Azure-Samples/Hybrid-Compute-Go-Create-VM)
- [Plán pro datastorage](https://github.com/Azure-Samples/Hybrid-Storage-Go-Dataplane)
- [Použijte Managed disks](https://github.com/Azure-Samples/Hybrid-Compute-Go-ManagedDisks) (ukázka, která používá profil 2019-03-01, který cílí na nejnovější verze rozhraní API podporované centrem Azure Stack).

## <a name="next-steps"></a>Další kroky

- [Instalace PowerShellu pro centrum Azure Stack](../operator/azure-stack-powershell-install.md)
- [Konfigurace prostředí PowerShell uživatele centra Azure Stack](azure-stack-powershell-configure-user.md)
