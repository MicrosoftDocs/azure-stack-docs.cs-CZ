---
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: include
ms.date: 10/10/2020
ms.reviewer: bryanla
ms.lastreviewed: 10/20/2020
ms.openlocfilehash: 0df920ef0c8063332a290ca5e95e1c01d755e548
ms.sourcegitcommit: 8c745b205ea5a7a82b73b7a9daf1a7880fd1bee9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/24/2020
ms.locfileid: "95517548"
---
Nakonec určete nejnovější vlastnosti nasazení poskytovatele prostředků a použijte je k dokončení procesu rotace tajných kódů.

### <a name="determine-deployment-properties"></a>Určení vlastností nasazení

Poskytovatelé prostředků se nasazují do prostředí centra Azure Stack jako balíček produktu s verzí. Balíčkům se přiřazuje jedinečné ID balíčku ve formátu `'<product-id>.<installed-version>'` . Kde `<product-id>` je jedinečný řetězec představující poskytovatele prostředků a `<installed-version>` představuje konkrétní verzi. Tajné kódy spojené s jednotlivými balíčky jsou uložené ve službě Azure Stack centrum Key Vault. 

Otevřete konzolu konzoly PowerShell se zvýšenými oprávněními a proveďte následující kroky, abyste určili vlastnosti potřebné k otočení tajných kódů poskytovatele prostředků:

1. Přihlaste se ke svému prostředí Azure Stackového centra pomocí přihlašovacích údajů operátora. Postup pro přihlašovací skript PowerShellu najdete v tématu [připojení k Azure Stack hub pomocí PowerShellu](../operator/azure-stack-powershell-configure-admin.md) . Ujistěte se, že používáte PowerShell rutiny AZ (místo AzureRM) a nahradíte všechny zástupné hodnoty, jako jsou adresy URL koncových bodů a název tenanta adresáře.

2. Spuštěním `Get-AzsProductDeployment` rutiny načtěte seznam nejnovějších nasazení poskytovatele prostředků. Vrácená `"value"` kolekce obsahuje element pro každého nasazeného poskytovatele prostředků. Najděte poskytovatele prostředků, který vás zajímá, a poznamenejte si hodnoty těchto vlastností:
   - `"name"` -obsahuje ID produktu poskytovatele prostředků ve druhém segmentu hodnoty. 
   - `"properties"."deployment"."version"` – obsahuje aktuálně nasazené číslo verze. 

   V následujícím příkladu si všimněte nasazení Event Hubs RP v prvním prvku kolekce, který má ID produktu `"microsoft.eventhub"` a verzi `"1.2003.0.0"` :

   ```powershell
   PS C:\WINDOWS\system32> Get-AzsProductDeployment -AsJson
   VERBOSE: GET https://adminmanagement.myregion.mycompany.com/subscriptions/ze22ca96-z546-zbc6-z566-z35f68799816/providers/Microsoft.Deployment.Admin/locations/global/productDeployments?api-version=2019-01-01 with 0-char payload
   VERBOSE: Received 2656-char response, StatusCode = OK
   {
       "value":  [
                     {
                         "id":  "/subscriptions/ze22ca96-z546-zbc6-z566-z35f68799816/providers/Microsoft.Deployment.Admin/locations/global/productDeployments/microsoft.eventhub",
                         "name":  "global/microsoft.eventhub",
                         "type":  "Microsoft.Deployment.Admin/locations/productDeployments",
                         "properties":  {
                                            "status":  "DeploymentSucceeded",
                                            "subscriptionId":  "b37ae55a-a6c6-4474-ba97-81519412adf5",
                                            "deployment":  {
                                                               "version":  "1.2003.0.0",
                                                               "actionPlanInstanceResourceId":"/subscriptions/ze22ca96-z546-zbc6-z566-z35f68799816/providers/Microsoft.Deployment.Admin/locations/global/actionplans/abcdfcd3-fef0-z1a3-z85d-z6ceb0f31e36",
                                                               "parameters":  {
   
                                                                              }
                                                           },
                                            "lastSuccessfulDeployment":  {
                                                                             "version":  "1.2003.0.0",
                                                                             "actionPlanInstanceResourceId":"/subscriptions/ze22ca96-z546-zbc6-z566-z35f68799816/providers/Microsoft.Deployment.Admin/locations/global/actionplans/abcdfcd3-fef0-z1a3-z85d-z6ceb0f31e36",
                                                                             "parameters":  {
   
                                                                                            }
                                                                         },
                                            "provisioningState":  "Succeeded"
                                        }
                     },
                     {
                     ...
                     }
                 ]
   }
   ```

3. Pomocí zřetězení ID a verze produktu poskytovatele prostředků Sestavte ID balíčku poskytovatele prostředků. Například při použití hodnot odvozených v předchozím kroku je ID balíčku Event Hubs RP `microsoft.eventhub.1.2003.0.0` . 

4. Pomocí ID balíčku odvozeného v předchozím kroku spusťte příkaz `Get-AzsProductSecret -PackageId` a načtěte seznam tajných typů používaných poskytovatelem prostředků. V vrácené `value` kolekci vyhledejte element obsahující hodnotu `"Certificate"` pro `"properties"."secretKind"` vlastnost. Tento element obsahuje vlastnosti tajného klíče certifikátu RP. Poznamenejte si název přiřazený k tomuto tajnému certifikátu, který je identifikovaný posledním segmentem této `"name"` vlastnosti, a to hned výše `"properties"` . 

   V následujícím příkladu kolekce tajných kódů vracená pro Event Hubs RP obsahuje `"Certificate"` tajný kód s názvem `aseh-ssl-gateway-pfx` . 

    ```powershell
    PS C:\WINDOWS\system32> Get-AzsProductSecret -PackageId 'microsoft.eventhub.1.2003.0.0' -AsJson
    VERBOSE: GET
    https://adminmanagement.myregion.mycompany.com/subscriptions/ze22ca96-z546-zbc6-z566-z35f68799816/providers/Microsoft.Deployment.Admin/locations/global/productPackages/microsoft.eventhub.1.2003.0.0/secrets?api-version=2019-01-01 with 0-char payload
    VERBOSE: Received 617-char response, StatusCode = OK
    {
        "value":  [
                        {
                            "id":  "/subscriptions/ze22ca96-z546-zbc6-z566-z35f68799816/providers/Microsoft.Deployment.Admin/locations/global/productPackages/microsoft.eventhub.1.2003.0.0/secrets/aseh-ssl-gateway-pfx",
                            "name":  "global/microsoft.eventhub.1.2003.0.0/aseh-ssl-gateway-pfx",
                            "type":  "Microsoft.Deployment.Admin/locations/productPackages/secrets",
                            "properties":  {
                                            "secretKind":  "Certificate",
                                            "description":  "Event Hubs gateway SSL certificate.",
                                            "expiresAfter":  "P730D",
                                            "secretDescriptor":  {
    
                                                                    },
                                            "secretState":  {
                                                                "status":  "Deployed",
                                                                "rotationStatus":  "None",
                                                                "expirationDate":  "2022-03-31T00:16:05.3068718Z"
                                                            },
                                            "provisioningState":  "Succeeded"
                                        }
                        },
                        ...
                    ]
    }
    ```

### <a name="rotate-the-secrets"></a>Otočení tajných kódů

1. Pomocí `Set-AzsProductSecret` rutiny importujte nový certifikát do Key Vault, který bude používán procesem rotace. Nahraďte zástupné hodnoty proměnných odpovídajícím způsobem před spuštěním skriptu:

   | Zástupný symbol | Popis | Příklad hodnoty |
   | ----------- | ----------- | --------------|
   | `<product-id>` | ID produktu nejnovějšího nasazení poskytovatele prostředků. | `microsoft.eventhub` |
   | `<installed-version>` | Verze nejnovějšího nasazení poskytovatele prostředků. | `1.2003.0.0` |
   | `<cert-secret-name>` | Název, pod kterým je uložený tajný kód certifikátu. | `aseh-ssl-gateway-pfx` |
   | `<cert-pfx-file-path>` | Cesta k souboru PFX certifikátu | `C:\dir\eh-cert-file.pfx` |
   | `<pfx-password>` | Heslo přiřazené k vašemu certifikátu Soubor PFX. | `strong@CertSecret6` |

   ```powershell
   $productId = '<product-id>'
   $packageId = $productId + '.' + '<installed-version>'
   $certSecretName = '<cert-secret-name>' 
   $pfxFilePath = '<cert-pfx-file-path>'
   $pfxPassword = ConvertTo-SecureString '<pfx-password>' -AsPlainText -Force   
   Set-AzsProductSecret -PackageId $packageId -SecretName $certSecretName -PfxFileName $pfxFilePath -PfxPassword $pfxPassword -Force
   ```

2. Nakonec použijte `Invoke-AzsProductRotateSecretsAction` rutinu pro otočení interních a externích tajných kódů:

   > [!NOTE]
   > Dokončení procesu rotace trvá přibližně 3,5 až 4 hodiny.

   ```powershell
   Invoke-AzsProductRotateSecretsAction -ProductId $productId
   ```
   
   Průběh střídání tajných kódů můžete monitorovat v konzole PowerShellu nebo na portálu pro správu výběrem poskytovatele prostředků ve službě Marketplace:

   [![přesměrovaná tajná – průběh](media/resource-provider-va-rotate-secrets-rotate/secret-rotation-in-progress.png)](media/resource-provider-va-rotate-secrets-rotate/secret-rotation-in-progress.png#lightbox)

