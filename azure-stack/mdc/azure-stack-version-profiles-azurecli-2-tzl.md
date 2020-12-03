---
title: Správa centra Azure Stack pomocí Azure CLI | Microsoft Docs
description: Naučte se používat rozhraní příkazového řádku (CLI) pro různé platformy ke správě a nasazení prostředků v centru Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/2/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: b9d40c83c31903a582d8130dcb249fa2fca0a49e
ms.sourcegitcommit: 9ef2cdc748cf00cd3c8de90705ea0542e29ada97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/02/2020
ms.locfileid: "96525570"
---
# <a name="manage-and-deploy-resources-to-azure-stack-hub-with-azure-cli"></a>Správa prostředků a jejich nasazování do služby Azure Stack Hub pomocí Azure CLI

*Platí pro: Azure Stack integrovaných systémů centra*

Postupujte podle kroků v tomto článku a nastavte rozhraní příkazového řádku Azure Command-Line pro správu prostředků Azure Stack centra z klientských platforem Linux, Mac a Windows.

## <a name="prepare-for-azure-cli"></a>Příprava pro Azure CLI

**Aliasy aliasů virtuálních počítačů** poskytují alias, například "UbuntuLTS" nebo "Win2012Datacenter". Tento alias odkazuje na vydavatele image, nabídku, SKU a verzi jako jeden parametr při nasazování virtuálních počítačů.  

Následující část popisuje, jak nastavit aliasy virtuálního počítače jako koncového bodu.

### <a name="set-up-the-virtual-machine-aliases-endpoint"></a>Nastavte koncový bod aliasy virtuálních počítačů.

Můžete nastavit veřejně přístupný koncový bod, který hostuje soubor s aliasem virtuálního počítače. Soubor aliasu virtuálního počítače je soubor JSON, který poskytuje běžný název pro obrázek. Název použijete při nasazení virtuálního počítače jako parametru Azure CLI.

1. Pokud publikujete vlastní image, poznamenejte si informace o vydavateli, nabídce, SKU a verzi, které jste zadali během publikování. Pokud se jedná o image z webu Marketplace, můžete zobrazit informace pomocí ```Get-AzureVMImage``` rutiny.  

2. Stáhněte si [ukázkový soubor](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) z GitHubu.

3. Vytvořte účet úložiště v centru Azure Stack. Až to bude hotové, vytvořte kontejner objektů BLOB. Nastavte zásady přístupu na veřejné.  

4. Nahrajte soubor JSON do nového kontejneru. Až to uděláte, můžete zobrazit adresu URL objektu BLOB. Vyberte název objektu BLOB a potom vyberte adresu URL z vlastností objektu BLOB.

### <a name="install-or-upgrade-cli"></a>Instalace nebo upgrade rozhraní příkazového řádku

Přihlaste se k vývojové pracovní stanici a nainstalujte rozhraní příkazového řádku. Azure Stack hub vyžaduje Azure CLI verze 2,0 nebo novější. Nejnovější verze profilů rozhraní API vyžaduje aktuální verzi rozhraní příkazového řádku. Rozhraní příkazového řádku nainstalujete pomocí postupu popsaného v článku [instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) . 

Chcete-li ověřit, zda byla instalace úspěšná, otevřete okno terminálu nebo příkazového řádku a spusťte následující příkaz:

```shell
az --version
```

Měla by se zobrazit verze rozhraní příkazového řádku Azure CLI a dalších závislých knihoven, které jsou nainstalované ve vašem počítači.
    
![Rozhraní příkazového řádku Azure v umístění Pythonu centra Azure Stack](media/azure-stack-version-profiles-azurecli2/cli-python-location.png)


## <a name="windowslinux-azure-ad"></a>Windows/Linux (Azure AD)

V této části se seznámíte s nastavením rozhraní příkazového řádku, pokud používáte Azure AD jako službu pro správu identit a používáte rozhraní příkazového řádku na počítači s Windows/Linux.

### <a name="connect-to-azure-stack-hub"></a>Připojení k centru Azure Stack

1. Zaregistrujte své prostředí Azure Stackového centra spuštěním `az cloud register` příkazu.

2. Zaregistrujte své prostředí. Při spuštění použijte následující parametry `az cloud register` :

    | Hodnota | Příklad | Popis |
    | --- | --- | --- |
    | Název prostředí | AzureStackUser | Použijte `AzureStackUser`  pro uživatelské prostředí. Pokud jste operátor, zadejte `AzureStackAdmin` . |
    | Správce prostředků koncový bod | `https://management.local.azurestack.external` | <!-- TZLASDKFIX The **ResourceManagerUrl** in the ASDK is: `https://management.local.azurestack.external/`--> **ResourceManagerUrl** v integrovaných systémech je: `https://management.<region>.<fqdn>/` Pokud máte dotaz týkající se integrovaného systémového koncového bodu, obraťte se na svého operátora cloudu. |
    | Koncový bod úložiště | Local. azurestack. external | <!-- TZLASDKFIX `local.azurestack.external` is for the ASDK.--> Pro integrovaný systém použijte pro svůj systém koncový bod.  |
    | Přípona trezoru klíčů | . trezor. Local. azurestack. external | <!-- TZLASDKFIX `.vault.local.azurestack.external` is for the ASDK.--> Pro integrovaný systém použijte pro svůj systém koncový bod.  |
    | Koncový bod dokumentu aliasu pro image virtuálního počítače – | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | Identifikátor URI dokumentu, který obsahuje aliasy imagí virtuálních počítačů Další informace najdete v tématu [Nastavení koncového bodu aliasy virtuálních počítačů](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

1. Nastavte aktivní prostředí pomocí následujících příkazů.

      ```azurecli
      az cloud set -n <environmentname>
      ```

1. Aktualizujte konfiguraci prostředí tak, aby používala profil konkrétní verze rozhraní API centra Azure Stack. Chcete-li aktualizovat konfiguraci, spusťte následující příkaz:

    ```azurecli
    az cloud update --profile 2019-03-01-hybrid
   ```
 
1. Přihlaste se k prostředí Azure Stackového centra pomocí `az login` příkazu. Přihlaste se do prostředí Azure Stack hub buď jako uživatel, nebo jako instanční objekt. 

   - Přihlaste se jako *uživatel*: 

     Můžete buď zadat uživatelské jméno a heslo přímo v rámci `az login` příkazu, nebo ověřit pomocí prohlížeče. Pokud má váš účet povolené ověřování Multi-Factor Authentication, musíte to udělat:

     ```azurecli
     az login -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
     ```

     > [!NOTE]
     > Pokud má váš uživatelský účet povolený službu Multi-Factor Authentication, použijte `az login` příkaz bez zadání `-u` parametru. Spuštěním tohoto příkazu získáte adresu URL a kód, který je nutné použít k ověření.

   - Přihlaste se jako *instanční objekt*: 
    
     Než se přihlásíte, [vytvořte instanční objekt pomocí Azure Portal nebo rozhraní](../operator/azure-stack-create-service-principals.md) příkazového řádku a přiřaďte mu roli. Teď se přihlaste pomocí následujícího příkazu:

     ```azurecli  
     az login --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> --service-principal -u <Application Id of the Service Principal> -p <Key generated for the Service Principal>
     ```

### <a name="test-the-connectivity"></a>Testování připojení

Když máte všechno nastavené, pomocí rozhraní příkazového řádku můžete vytvářet prostředky v rámci centra Azure Stack. Můžete například vytvořit skupinu prostředků pro aplikaci a přidat virtuální počítač. Pomocí následujícího příkazu vytvořte skupinu prostředků s názvem "MyResourceGroup":

```azurecli
az group create -n MyResourceGroup -l local
```

Pokud je skupina prostředků úspěšně vytvořená, předchozí příkaz vytvoří výstup následujících vlastností nově vytvořeného prostředku:

![Skupina prostředků – vytvořit výstup](media/azure-stack-connect-cli/output.png)

## <a name="windowslinux-ad-fs"></a>Windows/Linux (AD FS)

V této části se dozvíte, jak nastavit rozhraní příkazového řádku, pokud jako službu pro správu identit používáte službu Active Directory federovaného Services (AD FS) a používáte CLI na počítači s Windows/Linux.


### <a name="connect-to-azure-stack-hub"></a>Připojení k centru Azure Stack

1. Zaregistrujte své prostředí Azure Stackového centra spuštěním `az cloud register` příkazu.

2. Zaregistrujte své prostředí. Při spuštění použijte následující parametry `az cloud register` :

    | Hodnota | Příklad | Popis |
    | --- | --- | --- |
    | Název prostředí | AzureStackUser | Použijte `AzureStackUser`  pro uživatelské prostředí. Pokud jste operátor, zadejte `AzureStackAdmin` . |
    | Správce prostředků koncový bod | `https://management.local.azurestack.external` | <!-- TZLASDKFIX The **ResourceManagerUrl** in the ASDK is: `https://management.local.azurestack.external/`--> **ResourceManagerUrl** v integrovaných systémech je: `https://management.<region>.<fqdn>/` Pokud máte dotaz týkající se integrovaného systémového koncového bodu, obraťte se na svého operátora cloudu. |
    | Koncový bod úložiště | Local. azurestack. external | <!-- TZLASDKFIX `local.azurestack.external` is for the ASDK.--> Pro integrovaný systém použijte pro svůj systém koncový bod.  |
    | Přípona trezoru klíčů | . trezor. Local. azurestack. external | <!-- TZLASDKFIX `.vault.local.azurestack.external` is for the ASDK.--> Pro integrovaný systém použijte pro svůj systém koncový bod.  |
    | Koncový bod dokumentu aliasu pro image virtuálního počítače – | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | Identifikátor URI dokumentu, který obsahuje aliasy imagí virtuálních počítačů Další informace najdete v tématu [Nastavení koncového bodu aliasy virtuálních počítačů](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains VM image aliases>
    ```

1. Nastavte aktivní prostředí pomocí následujících příkazů.

      ```azurecli
      az cloud set -n <environmentname>
      ```

1. Aktualizujte konfiguraci prostředí tak, aby používala profil konkrétní verze rozhraní API centra Azure Stack. Chcete-li aktualizovat konfiguraci, spusťte následující příkaz:

    ```azurecli
    az cloud update --profile 2019-03-01-hybrid
   ```

1. Přihlaste se k prostředí Azure Stackového centra pomocí `az login` příkazu. K prostředí služby Azure Stack hub se můžete přihlásit buď jako uživatel, nebo jako instanční objekt. 

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

### <a name="test-the-connectivity"></a>Testování připojení

Když máte všechno nastavené, pomocí rozhraní příkazového řádku můžete vytvářet prostředky v rámci centra Azure Stack. Můžete například vytvořit skupinu prostředků pro aplikaci a přidat virtuální počítač. Pomocí následujícího příkazu vytvořte skupinu prostředků s názvem "MyResourceGroup":

```azurecli
az group create -n MyResourceGroup -l local
```

Pokud je skupina prostředků úspěšně vytvořená, předchozí příkaz vytvoří výstup následujících vlastností nově vytvořeného prostředku:

![Skupina prostředků – vytvořit výstup](media/azure-stack-connect-cli/output.png)


## <a name="known-issues"></a>Známé problémy

Při použití rozhraní příkazového řádku v Azure Stackovém centru jsou známé problémy:

 - Interaktivní režim rozhraní příkazového řádku Například `az interactive` příkaz není v Azure Stackovém centru dosud podporován.
 - Pokud chcete získat seznam imagí virtuálních počítačů, které jsou k dispozici v Azure Stackovém centru, použijte `az vm image list --all` příkaz místo `az vm image list` příkazu. Zadáním `--all` možnosti zajistíte, že odpověď vrátí pouze obrázky, které jsou k dispozici ve vašem prostředí Azure Stack hub.
 - Aliasy imagí virtuálních počítačů, které jsou dostupné v Azure, se nedají použít pro centrum Azure Stack. Při použití imagí virtuálních počítačů musíte použít celý parametr URN (kanonický: UbuntuServer: 14.04.3-LTS: 1.0.0) místo aliasu image. Tento název URN se musí shodovat s specifikacemi obrázku odvozenými z `az vm images list` příkazu.

## <a name="next-steps"></a>Další kroky

- [Nasazení šablon pomocí Azure CLI](../user/azure-stack-deploy-template-command-line.md)
- [Povolení Azure CLI pro uživatele centra Azure Stack (operátor)](../operator/azure-stack-cli-admin.md)
- [Správa uživatelských oprávnění](../operator/azure-stack-manage-permissions.md) 