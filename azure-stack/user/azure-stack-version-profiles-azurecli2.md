---
title: Správa centra Azure Stack pomocí Azure CLI
description: Naučte se používat rozhraní příkazového řádku (CLI) pro různé platformy ke správě a nasazení prostředků v centru Azure Stack.
author: mattbriggs
ms.topic: article
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/16/2020
ms.openlocfilehash: 2e3c25e282a84a0e41d6c69d0af6e2f352bbfba5
ms.sourcegitcommit: 52c934f5eeb5fcd8e8f2ce3380f9f03443d1e445
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2021
ms.locfileid: "97974009"
---
# <a name="install-azure-cli-on-azure-stack-hub"></a>Instalace rozhraní příkazového řádku Azure CLI do centra Azure Stack

Rozhraní příkazového řádku Azure můžete nainstalovat pro správu centra Azure Stack s počítači se systémem Windows nebo Linux. Tento článek vás provede kroky instalace a nastavení Azure CLI.

## <a name="install-azure-cli"></a>Instalace rozhraní příkazového řádku Azure CLI

1. Přihlaste se k vývojové pracovní stanici a nainstalujte rozhraní příkazového řádku. Azure Stack hub vyžaduje Azure CLI verze 2,0 nebo novější. 

2. Rozhraní příkazového řádku můžete nainstalovat pomocí postupu popsaného v článku [instalace Azure CLI](/cli/azure/install-azure-cli) . 

3. Chcete-li ověřit, zda byla instalace úspěšná, otevřete okno terminálu nebo příkazového řádku a spusťte následující příkaz:

    ```shell
    az --version
    ```

    Měla by se zobrazit verze rozhraní příkazového řádku Azure CLI a dalších závislých knihoven, které jsou nainstalované ve vašem počítači.

    ![Rozhraní příkazového řádku Azure v umístění Pythonu centra Azure Stack](media/azure-stack-version-profiles-azurecli2/cli-python-location.png)

2. Poznamenejte si umístění Pythonu pro rozhraní příkazového řádku. Pokud používáte ASDK, musíte k přidání certifikátu použít toto umístění. Pokyny k nastavení certifikátů pro instalaci rozhraní příkazového řádku na ASDK najdete v tématu [nastavení certifikátů pro Azure CLI v Azure Stack Development Kit](../asdk/asdk-cli.md).

## <a name="connect-with-azure-cli"></a>Připojení pomocí Azure CLI

### <a name="azure-ad-on-windows"></a>[Azure AD ve Windows](#tab/ad-win)

V této části se dozvíte, jak nastavit rozhraní příkazového řádku, pokud používáte Azure AD jako službu pro správu identit a používáte rozhraní příkazového řádku v počítači s Windows.

#### <a name="connect-to-azure-stack-hub"></a>Připojení k centru Azure Stack

1. Pokud používáte ASDK, důvěřovat kořenovému certifikátu certifikační autority centra Azure Stack. Pokyny najdete v tématu [důvěryhodnost certifikátu](../asdk/asdk-cli.md#trust-the-certificate).

2. Zaregistrujte své prostředí Azure Stackového centra spuštěním `az cloud register` příkazu.

3. Zaregistrujte své prostředí. Při spuštění použijte následující parametry `az cloud register` :

      | Hodnota | Příklad | Popis |
      | --- | --- | --- |
      | Název prostředí | AzureStackUser | Použijte `AzureStackUser`  pro uživatelské prostředí. Pokud jste operátor, zadejte `AzureStackAdmin` . |
      | Správce prostředků koncový bod | `https://management.contoso.onmicrosoft.com` | **ResourceManagerUrl** v ASDK je: `https://management.contoso.onmicrosoft.com/` **ResourceManagerUrl** v integrovaných systémech je: `https://management.<region>.<fqdn>/` Pokud máte dotaz týkající se integrovaného systémového koncového bodu, obraťte se na svého operátora cloudu. |
      | Koncový bod úložiště | local.contoso.onmicrosoft.com | `local.azurestack.external` je pro rozhraní ASDK. Pro integrovaný systém použijte pro svůj systém koncový bod.  |
      | Přípona trezoru klíčů | . vault.contoso.onmicrosoft.com | `.vault.local.azurestack.external` je pro rozhraní ASDK. Pro integrovaný systém použijte pro svůj systém koncový bod.  |
      | ID prostředku grafu služby Active Directory koncového bodu | https://graph.windows.net/ | Identifikátor prostředku služby Azure Active Directory. |
    
      ```azurecli  
      az cloud register `
          -n <environmentname> `
          --endpoint-resource-manager "https://management.<region>.<fqdn>" `
          --suffix-storage-endpoint "<fqdn>" `
          --suffix-keyvault-dns ".vault.<fqdn>" `
          --endpoint-active-directory-graph-resource-id "https://graph.windows.net/"
      ```

    Odkaz na [příkaz Register](https://docs.microsoft.com/cli/azure/cloud?view=azure-cli-latest#az_cloud_register) najdete v referenční dokumentaci k Azure CLI.


4. Nastavte aktivní prostředí pomocí následujících příkazů.

      ```azurecli
      az cloud set -n <environmentname>
      ```

5. Aktualizujte konfiguraci prostředí tak, aby používala profil konkrétní verze rozhraní API centra Azure Stack. Chcete-li aktualizovat konfiguraci, spusťte následující příkaz:

    ```azurecli
    az cloud update --profile 2019-03-01-hybrid
   ```
 
6. Přihlaste se k prostředí Azure Stackového centra pomocí `az login` příkazu.

    K prostředí centra Azure Stack se můžete přihlásit pomocí přihlašovacích údajů uživatele nebo pomocí [instančního objektu](/azure/active-directory/develop/app-objects-and-service-principals) , který vám poskytl operátor cloudu. 

   - Přihlaste se jako *uživatel*: 

     Můžete buď zadat uživatelské jméno a heslo přímo v rámci `az login` příkazu, nebo ověřit pomocí prohlížeče. Pokud má váš účet povolené ověřování Multi-Factor Authentication, musíte to udělat:

     ```azurecli
     az login -u "user@contoso.onmicrosoft.com" -p 'Password123!' --tenant contoso.onmicrosoft.com
     ```

     > [!NOTE]
     > Pokud má váš uživatelský účet povolený službu Multi-Factor Authentication, použijte `az login` příkaz bez zadání `-u` parametru. Spuštěním tohoto příkazu získáte adresu URL a kód, který je nutné použít k ověření.

   - Přihlaste se jako *instanční objekt*: 
    
        Než se přihlásíte, [vytvořte instanční objekt pomocí Azure Portal nebo rozhraní](../operator/azure-stack-create-service-principals.md) příkazového řádku a přiřaďte mu roli. Teď se přihlaste pomocí následujícího příkazu:
    
        ```azurecli  
        az login `
          --tenant <Azure Active Directory Tenant name. `
                    For example: myazurestack.onmicrosoft.com> `
        --service-principal `
          -u <Application Id of the Service Principal> `
          -p <Key generated for the Service Principal>
        ```
    
7. Ověřte, že je vaše prostředí správně nastavené a že vaše prostředí je aktivním cloudem.

      ```azurecli
          az cloud list --output table
      ```

Měli byste vidět, že je vaše prostředí uvedené  a je aktivní `true` . Příklad:

```azurecli  
IsActive    Name               Profile
----------  -----------------  -----------------
False       AzureCloud         2019-03-01-hybrid
False       AzureChinaCloud    latest
False       AzureUSGovernment  latest
False       AzureGermanCloud   latest
True        AzureStackUser     2019-03-01-hybrid
```

#### <a name="test-the-connectivity"></a>Testování připojení

Když máte všechno nastavené, pomocí rozhraní příkazového řádku můžete vytvářet prostředky v rámci centra Azure Stack. Můžete například vytvořit skupinu prostředků pro aplikaci a přidat virtuální počítač. Pomocí následujícího příkazu vytvořte skupinu prostředků s názvem "MyResourceGroup":

```azurecli
az group create -n MyResourceGroup -l local
```

Pokud je skupina prostředků úspěšně vytvořená, předchozí příkaz vytvoří výstup následujících vlastností nově vytvořeného prostředku:

![Skupina prostředků – vytvořit výstup](media/azure-stack-connect-cli/image1.png)

### <a name="ad-fs-on-windows"></a>[AD FS ve Windows](#tab/adfs-win)

V této části se dozvíte, jak nastavit rozhraní příkazového řádku, pokud jako službu pro správu identit používáte službu Active Directory federovaného Services (AD FS) a používáte rozhraní příkazového řádku (CLI) na počítači s Windows.

#### <a name="connect-to-azure-stack-hub"></a>Připojení k centru Azure Stack


1. Pokud používáte ASDK, důvěřovat kořenovému certifikátu certifikační autority centra Azure Stack. Pokyny najdete v tématu [důvěryhodnost certifikátu](../asdk/asdk-cli.md#trust-the-certificate).

2. Zaregistrujte své prostředí Azure Stackového centra spuštěním `az cloud register` příkazu.

3. Zaregistrujte své prostředí. Při spuštění použijte následující parametry `az cloud register` :

    | Hodnota | Příklad | Popis |
    | --- | --- | --- |
    | Název prostředí | AzureStackUser | Použijte `AzureStackUser`  pro uživatelské prostředí. Pokud jste operátor, zadejte `AzureStackAdmin` . |
    | Správce prostředků koncový bod | `https://management.local.azurestack.external` | **ResourceManagerUrl** v ASDK je: `https://management.local.azurestack.external/` **ResourceManagerUrl** v integrovaných systémech je: `https://management.<region>.<fqdn>/` Pokud máte dotaz týkající se integrovaného systémového koncového bodu, obraťte se na svého operátora cloudu. |
    | Koncový bod úložiště | Local. azurestack. external | `local.azurestack.external` je pro rozhraní ASDK. Pro integrovaný systém použijte pro svůj systém koncový bod.  |
    | Přípona trezoru klíčů | . trezor. Local. azurestack. external | `.vault.local.azurestack.external` je pro rozhraní ASDK. Pro integrovaný systém použijte pro svůj systém koncový bod.  |
    | Koncový bod dokumentu aliasu pro image virtuálního počítače – | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | Identifikátor URI dokumentu, který obsahuje aliasy imagí virtuálních počítačů Další informace najdete v tématu [Nastavení koncového bodu aliasu virtuálního počítače](../asdk/asdk-cli.md#set-up-the-virtual-machine-alias-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

4. Nastavte aktivní prostředí pomocí následujících příkazů.

      ```azurecli
      az cloud set -n <environmentname>
      ```

5. Aktualizujte konfiguraci prostředí tak, aby používala profil konkrétní verze rozhraní API centra Azure Stack. Chcete-li aktualizovat konfiguraci, spusťte následující příkaz:

    ```azurecli
    az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >Pokud používáte verzi centra Azure Stack před sestavením 1808, musíte použít profil verze API **2017-03-09-Profile** , nikoli profil verze rozhraní API **2019-03-01-Hybrid**. Musíte také použít nejnovější verzi Azure CLI.

6. Přihlaste se k prostředí Azure Stackového centra pomocí `az login` příkazu. K prostředí služby Azure Stack hub se můžete přihlásit buď jako uživatel, nebo jako [instanční objekt](/azure/active-directory/develop/app-objects-and-service-principals). 

   - Přihlaste se jako *uživatel*:

     Můžete buď zadat uživatelské jméno a heslo přímo v rámci `az login` příkazu, nebo ověřit pomocí prohlížeče. Pokud má váš účet povolené ověřování Multi-Factor Authentication, musíte to udělat:

     ```azurecli
     az cloud register  -n <environmentname>   --endpoint-resource-manager "https://management.local.azurestack.external"  --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>   --profile "2019-03-01-hybrid"
     ```

     > [!NOTE]
     > Pokud má váš uživatelský účet povolený službu Multi-Factor Authentication, použijte `az login` příkaz bez zadání `-u` parametru. Spuštěním tohoto příkazu získáte adresu URL a kód, který je nutné použít k ověření.

   - Přihlaste se jako *instanční objekt*: 
    
     Připravte soubor. pem, který se má použít pro přihlašovací objekty instančního objektu.

     V klientském počítači, v němž byl vytvořen objekt zabezpečení, exportujte certifikát instančního objektu jako PFX s privátním klíčem umístěným na adrese `cert:\CurrentUser\My` . Název certifikátu má stejný název jako objekt zabezpečení.

     Převeďte PFX na PEM (použijte nástroj OpenSSL).

     Přihlaste se k rozhraní příkazového řádku:
  
     ```azurecli  
     az login --service-principal \
      -u <Client ID from the Service Principal details> \
      -p <Certificate's fully qualified name, such as, C:\certs\spn.pem>
      --tenant <Tenant ID> \
      --debug 
     ```

#### <a name="test-the-connectivity"></a>Testování připojení

Když máte všechno nastavené, pomocí rozhraní příkazového řádku můžete vytvářet prostředky v rámci centra Azure Stack. Můžete například vytvořit skupinu prostředků pro aplikaci a přidat virtuální počítač. Pomocí následujícího příkazu vytvořte skupinu prostředků s názvem "MyResourceGroup":

```azurecli
az group create -n MyResourceGroup -l local
```

Pokud je skupina prostředků úspěšně vytvořená, předchozí příkaz vytvoří výstup následujících vlastností nově vytvořeného prostředku:

![Skupina prostředků – vytvořit výstup](media/azure-stack-connect-cli/image1.png)

### <a name="azure-ad-on-linux"></a>[Azure AD v systému Linux](#tab/ad-lin)

V této části se seznámíte s nastavením rozhraní příkazového řádku, pokud používáte Azure AD jako službu pro správu identit a používáte rozhraní příkazového řádku v počítači se systémem Linux.

#### <a name="connect-to-azure-stack-hub"></a>Připojení k centru Azure Stack

Pomocí následujících kroků se připojte k centru Azure Stack:


1. Pokud používáte ASDK, důvěřovat kořenovému certifikátu certifikační autority centra Azure Stack. Pokyny najdete v tématu [důvěryhodnost certifikátu](../asdk/asdk-cli.md#trust-the-certificate).

2. Zaregistrujte své prostředí Azure Stackového centra spuštěním `az cloud register` příkazu.

3. Zaregistrujte své prostředí. Při spuštění použijte následující parametry `az cloud register` :

    | Hodnota | Příklad | Popis |
    | --- | --- | --- |
    | Název prostředí | AzureStackUser | Použijte `AzureStackUser`  pro uživatelské prostředí. Pokud jste operátor, zadejte `AzureStackAdmin` . |
    | Správce prostředků koncový bod | `https://management.local.azurestack.external` | **ResourceManagerUrl** v ASDK je: `https://management.local.azurestack.external/` **ResourceManagerUrl** v integrovaných systémech je: `https://management.<region>.<fqdn>/` Pokud máte dotaz týkající se integrovaného systémového koncového bodu, obraťte se na svého operátora cloudu. |
    | Koncový bod úložiště | Local. azurestack. external | `local.azurestack.external` je pro rozhraní ASDK. Pro integrovaný systém použijte pro svůj systém koncový bod.  |
    | Přípona trezoru klíčů | . trezor. Local. azurestack. external | `.vault.local.azurestack.external` je pro rozhraní ASDK. Pro integrovaný systém použijte pro svůj systém koncový bod.  |
    | Koncový bod dokumentu aliasu pro image virtuálního počítače – | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | Identifikátor URI dokumentu, který obsahuje aliasy imagí virtuálních počítačů Další informace najdete v tématu [Nastavení koncového bodu aliasu virtuálního počítače](../asdk/asdk-cli.md#set-up-the-virtual-machine-alias-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

4. Nastavte aktivní prostředí. 

      ```azurecli
        az cloud set -n <environmentname>
      ```

5. Aktualizujte konfiguraci prostředí tak, aby používala profil konkrétní verze rozhraní API centra Azure Stack. Chcete-li aktualizovat konfiguraci, spusťte následující příkaz:

    ```azurecli
      az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >Pokud používáte verzi centra Azure Stack před sestavením 1808, musíte použít profil verze API **2017-03-09-Profile** , nikoli profil verze rozhraní API **2019-03-01-Hybrid**. Musíte také použít nejnovější verzi Azure CLI.

6. Přihlaste se k prostředí Azure Stackového centra pomocí `az login` příkazu. K prostředí služby Azure Stack hub se můžete přihlásit buď jako uživatel, nebo jako [instanční objekt](/azure/active-directory/develop/app-objects-and-service-principals). 

   * Přihlaste se jako *uživatel*:

     Můžete buď zadat uživatelské jméno a heslo přímo v rámci `az login` příkazu, nebo ověřit pomocí prohlížeče. Pokud má váš účet povolené ověřování Multi-Factor Authentication, musíte to udělat:

     ```azurecli
     az login \
       -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> \
       --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
     ```

     > [!NOTE]
     > Pokud má váš uživatelský účet povolený vícefaktorové ověřování, můžete použít `az login` příkaz bez zadání `-u` parametru. Spuštěním tohoto příkazu získáte adresu URL a kód, který je nutné použít k ověření.
   
   * Přihlášení jako *instanční objekt*
    
     Než se přihlásíte, [vytvořte instanční objekt pomocí Azure Portal nebo rozhraní](../operator/azure-stack-create-service-principals.md) příkazového řádku a přiřaďte mu roli. Teď se přihlaste pomocí následujícího příkazu:

     ```azurecli  
     az login \
       --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> \
       --service-principal \
       -u <Application Id of the Service Principal> \
       -p <Key generated for the Service Principal>
     ```

#### <a name="test-the-connectivity"></a>Testování připojení

Když máte všechno nastavené, pomocí rozhraní příkazového řádku můžete vytvářet prostředky v rámci centra Azure Stack. Můžete například vytvořit skupinu prostředků pro aplikaci a přidat virtuální počítač. Pomocí následujícího příkazu vytvořte skupinu prostředků s názvem "MyResourceGroup":

```azurecli
    az group create -n MyResourceGroup -l local
```

Pokud je skupina prostředků úspěšně vytvořená, předchozí příkaz vytvoří výstup následujících vlastností nově vytvořeného prostředku:

![Skupina prostředků – vytvořit výstup](media/azure-stack-connect-cli/image1.png)

### <a name="ad-fs-linux"></a>[AD FS Linux](#tab/adfs-lin)

V této části se dozvíte, jak nastavit rozhraní příkazového řádku, pokud jako službu pro správu používáte službu Active Directory federovaného Services (AD FS) a používáte rozhraní příkazového řádku (CLI) na počítači se systémem Linux.

#### <a name="connect-to-azure-stack-hub"></a>Připojení k centru Azure Stack

Pomocí následujících kroků se připojte k centru Azure Stack:

1. Pokud používáte ASDK, důvěřovat kořenovému certifikátu certifikační autority centra Azure Stack. Pokyny najdete v tématu [důvěryhodnost certifikátu](../asdk/asdk-cli.md#trust-the-certificate).

2. Zaregistrujte své prostředí Azure Stackového centra spuštěním `az cloud register` příkazu.

3. Zaregistrujte své prostředí. Při spuštění použijte následující parametry `az cloud register` .

    | Hodnota | Příklad | Popis |
    | --- | --- | --- |
    | Název prostředí | AzureStackUser | Použijte `AzureStackUser`  pro uživatelské prostředí. Pokud jste operátor, zadejte `AzureStackAdmin` . |
    | Správce prostředků koncový bod | `https://management.local.azurestack.external` | **ResourceManagerUrl** v ASDK je: `https://management.local.azurestack.external/` **ResourceManagerUrl** v integrovaných systémech je: `https://management.<region>.<fqdn>/` Pokud máte dotaz týkající se integrovaného systémového koncového bodu, obraťte se na svého operátora cloudu. |
    | Koncový bod úložiště | Local. azurestack. external | `local.azurestack.external` je pro rozhraní ASDK. Pro integrovaný systém použijte pro svůj systém koncový bod.  |
    | Přípona trezoru klíčů | . trezor. Local. azurestack. external | `.vault.local.azurestack.external` je pro rozhraní ASDK. Pro integrovaný systém použijte pro svůj systém koncový bod.  |
    | Koncový bod dokumentu aliasu pro image virtuálního počítače – | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | Identifikátor URI dokumentu, který obsahuje aliasy imagí virtuálních počítačů Další informace najdete v tématu [Nastavení koncového bodu aliasu virtuálního počítače](../asdk/asdk-cli.md#set-up-the-virtual-machine-alias-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

4. Nastavte aktivní prostředí. 

      ```azurecli
        az cloud set -n <environmentname>
      ```

5. Aktualizujte konfiguraci prostředí tak, aby používala profil konkrétní verze rozhraní API centra Azure Stack. Chcete-li aktualizovat konfiguraci, spusťte následující příkaz:

    ```azurecli
      az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >Pokud používáte verzi centra Azure Stack před sestavením 1808, musíte použít profil verze API **2017-03-09-Profile** , nikoli profil verze rozhraní API **2019-03-01-Hybrid**. Musíte také použít nejnovější verzi Azure CLI.

6. Přihlaste se k prostředí Azure Stackového centra pomocí `az login` příkazu. K prostředí služby Azure Stack hub se můžete přihlásit buď jako uživatel, nebo jako [instanční objekt](/azure/active-directory/develop/app-objects-and-service-principals). 

7. Přihlásit se: 

   *  Jako **uživatel** používající webový prohlížeč s kódem zařízení:  

   ```azurecli  
    az login --use-device-code
   ```

   > [!NOTE]  
   >Spuštěním příkazu získáte adresu URL a kód, který je nutné použít k ověření.

   * Jako instanční objekt:
        
     Připravte soubor. pem, který se má použít pro přihlašovací objekty instančního objektu.

      * V klientském počítači, v němž byl vytvořen objekt zabezpečení, exportujte certifikát instančního objektu jako PFX s privátním klíčem umístěným na adrese `cert:\CurrentUser\My` . Název certifikátu má stejný název jako objekt zabezpečení.
  
      * Převeďte PFX na PEM (použijte nástroj OpenSSL).

     Přihlaste se k rozhraní příkazového řádku:

      ```azurecli  
      az login --service-principal \
        -u <Client ID from the Service Principal details> \
        -p <Certificate's fully qualified name, such as, C:\certs\spn.pem>
        --tenant <Tenant ID> \
        --debug 
      ```

#### <a name="test-the-connectivity"></a>Testování připojení

Když máte všechno nastavené, pomocí rozhraní příkazového řádku můžete vytvářet prostředky v rámci centra Azure Stack. Můžete například vytvořit skupinu prostředků pro aplikaci a přidat virtuální počítač. Pomocí následujícího příkazu vytvořte skupinu prostředků s názvem "MyResourceGroup":

```azurecli
  az group create -n MyResourceGroup -l local
```

Pokud je skupina prostředků úspěšně vytvořená, předchozí příkaz vytvoří výstup následujících vlastností nově vytvořeného prostředku:

![Skupina prostředků – vytvořit výstup](media/azure-stack-connect-cli/image1.png)

### <a name="known-issues"></a>Známé problémy

Při použití rozhraní příkazového řádku v Azure Stackovém centru jsou známé problémy:

 - Interaktivní režim rozhraní příkazového řádku Například `az interactive` příkaz není v Azure Stackovém centru dosud podporován.
 - Pokud chcete získat seznam imagí virtuálních počítačů, které jsou k dispozici v Azure Stackovém centru, použijte `az vm image list --all` příkaz místo `az vm image list` příkazu. Zadáním `--all` možnosti zajistíte, že odpověď vrátí pouze obrázky, které jsou k dispozici ve vašem prostředí Azure Stack hub.
 - Aliasy imagí virtuálních počítačů, které jsou dostupné v Azure, se nedají použít pro centrum Azure Stack. Při použití imagí virtuálních počítačů musíte použít celý parametr URN (kanonický: UbuntuServer: 14.04.3-LTS: 1.0.0) místo aliasu image. Tento název URN se musí shodovat s specifikacemi obrázku odvozenými z `az vm images list` příkazu.

---

## <a name="next-steps"></a>Další kroky

- [Nasazení šablon pomocí Azure CLI](azure-stack-deploy-template-command-line.md)
- [Povolení Azure CLI pro uživatele centra Azure Stack (operátor)](../operator/azure-stack-cli-admin.md)
- [Správa uživatelských oprávnění](azure-stack-manage-permissions.md) 
