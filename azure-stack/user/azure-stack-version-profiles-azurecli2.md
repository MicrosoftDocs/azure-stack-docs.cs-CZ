---
title: Připojení k Azure Stack pomocí rozhraní příkazového řádku | Dokumentace Microsoftu
description: Další informace o použití multiplatformního rozhraní příkazového řádku (CLI) ke správě a nasazování prostředků ve službě Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 05/08/2019
ms.openlocfilehash: 69eb6e676fb8c134e0184d4df7df95ba0c75e854
ms.sourcegitcommit: 879165a66ff80f1463b6bb46e2245684224a9b92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/09/2019
ms.locfileid: "65473878"
---
# <a name="use-api-version-profiles-with-azure-cli-in-azure-stack"></a>Použití profilů verzí API pomocí Azure CLI ve službě Azure Stack

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Provedením kroků v tomto článku se nastavit si rozhraní příkazového řádku Azure (CLI) ke správě prostředků Azure Stack Development Kit (ASDK) z klientských platformách Linux, Mac a Windows.

## <a name="prepare-for-azure-cli"></a>Příprava pro rozhraní příkazového řádku Azure

Budete potřebovat certifikát kořenové certifikační Autority pro službu Azure Stack používat rozhraní příkazového řádku Azure na svém vývojovém počítači. Použít certifikát ke správě prostředků prostřednictvím rozhraní příkazového řádku.

 - **Kořenový certifikát certifikační Autority Azure stacku** je vyžadována, pokud používáte rozhraní příkazového řádku z pracovní stanice mimo ASDK.  

 - **Koncový bod virtuálního počítače aliasy** poskytuje alias, jako je "UbuntuLTS" nebo "Win2012Datacenter,", který odkazuje vydavatel image, nabídky, SKU a verze jako jediný parametr při nasazování virtuálních počítačů.  

Následující části popisují, jak získat tyto hodnoty.

### <a name="export-the-azure-stack-ca-root-certificate"></a>Exportujte certifikát kořenové certifikační Autority Azure stacku

Pokud použijete integrovaný systém, není nutné exportovat certifikát kořenové certifikační Autority. Je potřeba vyexportovat kořenový certifikát certifikační Autority na ASDK.

Export kořenového certifikátu ASDK ve formátu PEM:

1. [Vytvoření virtuálního počítače Windows v Azure stacku](azure-stack-quick-windows-portal.md).

2. Přihlaste se k počítači, otevřete řádku Powershellu se zvýšenými oprávněními a spusťte následující skript:

    ```powershell  
      $label = "AzureStackSelfSignedRootCert"
      Write-Host "Getting certificate from the current user trusted store with subject CN=$label"
      $root = Get-ChildItem Cert:\CurrentUser\Root | Where-Object Subject -eq "CN=$label" | select -First 1
      if (-not $root)
      {
          Write-Error "Certificate with subject CN=$label not found"
          return
      }

    Write-Host "Exporting certificate"
    Export-Certificate -Type CERT -FilePath root.cer -Cert $root

    Write-Host "Converting certificate to PEM format"
    certutil -encode root.cer root.pem
    ```

3. Zkopírujte certifikát do svého místního počítače.


### <a name="set-up-the-virtual-machine-aliases-endpoint"></a>Nastavení koncového bodu virtuálního počítače aliasy

Můžete nastavit veřejně přístupném koncovém bodu, který je hostitelem soubor alias virtuálního počítače. Soubor alias virtuálního počítače je soubor JSON, který poskytuje běžný název pro image. Název budete používat při nasazování virtuálního počítače jako parametr příkazového řádku Azure.

1. Pokud publikujete vlastní image, poznamenejte si informace vydavatele, nabídky, SKU a verze, které jste zadali během publikování. Pokud se jedná image z marketplace, můžete zobrazit informace s použitím ```Get-AzureVMImage``` rutiny.  

2. Stáhněte si [ukázkový soubor](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) z Githubu.

3. Vytvoření účtu úložiště ve službě Azure Stack. Po dokončení, vytvořte kontejner objektů blob. Nastavit zásady přístupu k "public".  

4. Nahrajte soubor JSON do nového kontejneru. Po dokončení, který se zobrazí adresa URL objektu blob. Vyberte název objektu blob a pak vyberete adresu URL z vlastností objektu blob.

### <a name="install-or-upgrade-cli"></a>Instalace nebo upgrade rozhraní příkazového řádku

Přihlaste se k vaší pracovní stanici a nainstalovat rozhraní příkazového řádku. Azure Stack vyžaduje verzi 2.0 nebo novější z rozhraní příkazového řádku Azure. Nejnovější verze profilů rozhraní API vyžaduje aktuální verzi rozhraní příkazového řádku.  Rozhraní příkazového řádku můžete nainstalovat pomocí kroků popsaných v [instalace rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) článku. 

1. Pokud chcete ověřit, zda byla instalace úspěšná, otevřete okno příkazového řádku nebo terminálu a spusťte následující příkaz:

    ```shell
    az --version
    ```

    Měli byste vidět verzi rozhraní příkazového řádku Azure a dalších závislých knihoven nainstalovaných v počítači.

    ![Rozhraní příkazového řádku Azure na umístění Azure Stack Pythonu](media/azure-stack-version-profiles-azurecli2/cli-python-location.png)

2. Poznamenejte si umístění Pythonu rozhraní příkazového řádku. Pokud spouštíte ASDK, musíte do tohoto umístění přidávat certifikát.


## <a name="windows-azure-ad"></a>Windows (Azure AD)

Tato část vás provede procesem nastavení rozhraní příkazového řádku Pokud používáte Azure AD jako služba pro správu identit a jsou na počítači s Windows pomocí rozhraní příkazového řádku.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Důvěřovat certifikátu kořenové certifikační Autority Azure stacku

Pokud používáte ASDK, musíte důvěřovat certifikátu kořenové certifikační Autority na vzdáleném počítači. Nebude potřeba to udělat pomocí integrovaných systémů.

Důvěřovat certifikátu kořenové certifikační Autority Azure stacku, přidejte je do existujícího certifikátu Python pro pomocí Azure CLI nainstalovanou verzi Pythonu. Může používat vlastní instance jazyka Python. Azure CLI zahrnuje svou vlastní verzi Pythonu.

1. Najdete umístění certifikátu na svém počítači.  Umístění získáte spuštěním příkazu `az --version`.

2. Přejděte do složky, který obsahuje, které jste aplikaci v Pythonu rozhraní příkazového řádku. Chcete spustit tuto verzi pythonu. Pokud nastavíte Pythonu ve vašem systému CESTU, spustit jazyk Python spustí vlastní verzi jazyka Python. Místo toho můžete spustit verze používané v rozhraní příkazového řádku a přidání certifikátu do této verze. Například může být vaše rozhraní příkazového řádku Python v: `C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\`.

    Pomocí následujících příkazů:

    ```powershell  
    cd "c:\pathtoyourcliversionofpython"
    .\python -c "import certifi; print(certifi.where())"
    ```

    Poznamenejte si umístění certifikátu. Například, `C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\certifi\cacert.pem`. Konkrétní cestu bude záviset na váš operační systém a instalaci rozhraní příkazového řádku.

2. Důvěřujete certifikátu kořenové certifikační Autority Azure stacku připojením k existující certifikát Python.

    ```powershell
    $pemFile = "<Fully qualified path to the PEM certificate Ex: C:\Users\user1\Downloads\root.pem>"

    $root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
    $root.Import($pemFile)

    Write-Host "Extracting required information from the cert file"
    $md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
    $sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
    $sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

    $issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
    $subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
    $labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
    $serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
    $md5Entry     = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
    $sha1Entry    = [string]::Format("# SHA1 Fingerprint: {0}", $sha1Hash)
    $sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
    $certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")

    $rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
    $serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

    Write-Host "Adding the certificate content to Python Cert store"
    Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

    Write-Host "Python Cert store was updated to allow the Azure Stack CA root certificate"
    ```

### <a name="connect-to-azure-stack"></a>Připojení ke službě Azure Stack

1. Zaregistrovat vaším prostředím Azure Stack spuštěním `az cloud register` příkazu.

    V některých případech se směruje přímé odchozí připojení k Internetu prostřednictvím serveru proxy nebo brány firewall, která vynucuje SSL zachycení. V těchto případech `az cloud register` příkaz může selhat s chybou jako je například "Nepodařilo se získat koncových bodů z cloudu." Chcete-li tuto chybu vyřešit, můžete nastavit následující proměnné prostředí:

    ```shell  
    set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
    set ADAL_PYTHON_SSL_NO_VERIFY=1
    ```

2. Zaregistrujte vašeho prostředí. Při běhu používat následující parametry `az cloud register`.

    | Value | Příklad: | Popis |
    | --- | --- | --- |
    | Název prostředí | AzureStackUser | Použití `AzureStackUser` pro uživatelské prostředí. Pokud operátor, zadat `AzureStackAdmin`. |
    | Koncový bod Resource Manageru | https://management.local.azurestack.external | **ResourceManagerUrl** je v Azure Stack Development Kit (ASDK): `https://management.local.azurestack.external/` **ResourceManagerUrl** v integrovaných systémech je: `https://management.<region>.<fqdn>/` Načíst metadata vyžaduje: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` Pokud máte dotaz týkající se koncový bod integrovaný systém, obraťte se na váš operátor cloudu. |
    | Koncový bod úložiště | local.azurestack.external | `local.azurestack.external` je ASDK. Pro integrovaný systém můžete použít koncový bod pro váš systém.  |
    | Přípona trezor klíčů | . vault.local.azurestack.external | `.vault.local.azurestack.external` je ASDK. Pro integrovaný systém můžete použít koncový bod pro váš systém.  |
    | Image virtuálního počítače alias doc koncový bod- | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | Identifikátor URI dokumentu, který obsahuje aliasy image virtuálního počítače. Další informace najdete v tématu [### nastavení koncového bodu virtuálního počítače aliasy](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

1. Pomocí následujících příkazů nastavte aktivní prostředí.

      ```azurecli
      az cloud set -n <environmentname>
      ```

1. Aktualizujte konfiguraci vašeho prostředí použít profil pro konkrétní verze rozhraní API Azure Stack. Pokud chcete aktualizovat konfiguraci, spusťte následující příkaz:

    ```azurecli
    az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >Pokud používáte verzi služby Azure Stack před sestavením. 1808, je nutné použít profilu verze rozhraní API **2017-03-09-profile** místo profilu verze rozhraní API **2018-03-01hybridní**. Je potřeba použít nejnovější verzi Azure CLI.
 
1. Přihlaste se k prostředí Azure Stack pomocí `az login` příkazu. Můžete se přihlásit k prostředí Azure Stack jako uživatel, nebo jako [instanční objekt služby](/azure/active-directory/develop/app-objects-and-service-principals). 

   - Přihlaste se jako *uživatele*: 

     Můžete zadat uživatelské jméno a heslo přímo v rámci `az login` příkaz nebo ověřování pomocí prohlížeče. Je nutné provést ten, pokud má váš účet zapnuté vícefaktorové ověřování:

     ```azurecli
     az login -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
     ```

     > [!NOTE]
     > Pokud váš uživatelský účet má povolené ověřování službou Multi-Factor Authentication, můžete použít `az login` příkaz bez zadání `-u` parametru. Spuštění tohoto příkazu obsahuje adresu URL a kód, který je nutné použít k ověření.

   - Přihlaste se jako *instanční objekt služby*: 
    
     Před přihlášením, [vytvoření instančního objektu služby na webu Azure portal](azure-stack-create-service-principals.md) nebo rozhraní příkazového řádku a přiřaďte ho roli. Teď se přihlaste pomocí následujícího příkazu:

     ```azurecli  
     az login --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> --service-principal -u <Application Id of the Service Principal> -p <Key generated for the Service Principal>
     ```

### <a name="test-the-connectivity"></a>Otestovat připojení

Všechna nastavení pomocí CLI vytvářet prostředky v rámci služby Azure Stack. Můžete například vytvořit skupinu prostředků pro aplikaci a přidejte virtuální počítač. Chcete-li vytvořit skupinu prostředků s názvem "MyResourceGroup", použijte následující příkaz:

```azurecli
az group create -n MyResourceGroup -l local
```

Pokud skupina prostředků je úspěšně vytvořen, předchozí příkaz vypíše následující vlastnosti nově vytvořeného prostředku:

![Vytvoření výstupní skupiny prostředků](media/azure-stack-connect-cli/image1.png)

## <a name="windows-ad-fs"></a>Windows (AD FS)

Tato část vás provede procesem nastavení rozhraní příkazového řádku Pokud používáte Active Directory Federated Services (AD FS) jako služba pro správu identit a jsou na počítači s Windows pomocí rozhraní příkazového řádku.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Důvěřovat certifikátu kořenové certifikační Autority Azure stacku

Pokud používáte ASDK, musíte důvěřovat certifikátu kořenové certifikační Autority na vzdáleném počítači. Nebude potřeba to udělat pomocí integrovaných systémů.

1. Najdete umístění certifikátu na svém počítači. Umístění se může lišit v závislosti na tom, kam jste nainstalovali Python. Otevřete příkazový řádek nebo řádku Powershellu se zvýšenými oprávněními a zadejte následující příkaz:

    ```powershell  
      python -c "import certifi; print(certifi.where())"
    ```

    Poznamenejte si umístění certifikátu. Například, `~/lib/python3.5/site-packages/certifi/cacert.pem`. Konkrétní cestu bude záviset na váš operační systém a verzi Pythonu, který jste nainstalovali.

2. Důvěřujete certifikátu kořenové certifikační Autority Azure stacku připojením k existující certifikát Python.

    ```powershell
    $pemFile = "<Fully qualified path to the PEM certificate Ex: C:\Users\user1\Downloads\root.pem>"

    $root = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
    $root.Import($pemFile)

    Write-Host "Extracting required information from the cert file"
    $md5Hash    = (Get-FileHash -Path $pemFile -Algorithm MD5).Hash.ToLower()
    $sha1Hash   = (Get-FileHash -Path $pemFile -Algorithm SHA1).Hash.ToLower()
    $sha256Hash = (Get-FileHash -Path $pemFile -Algorithm SHA256).Hash.ToLower()

    $issuerEntry  = [string]::Format("# Issuer: {0}", $root.Issuer)
    $subjectEntry = [string]::Format("# Subject: {0}", $root.Subject)
    $labelEntry   = [string]::Format("# Label: {0}", $root.Subject.Split('=')[-1])
    $serialEntry  = [string]::Format("# Serial: {0}", $root.GetSerialNumberString().ToLower())
    $md5Entry     = [string]::Format("# MD5 Fingerprint: {0}", $md5Hash)
    $sha1Entry    = [string]::Format("# SHA1 Fingerprint: {0}", $sha1Hash)
    $sha256Entry  = [string]::Format("# SHA256 Fingerprint: {0}", $sha256Hash)
    $certText = (Get-Content -Path $pemFile -Raw).ToString().Replace("`r`n","`n")

    $rootCertEntry = "`n" + $issuerEntry + "`n" + $subjectEntry + "`n" + $labelEntry + "`n" + `
    $serialEntry + "`n" + $md5Entry + "`n" + $sha1Entry + "`n" + $sha256Entry + "`n" + $certText

    Write-Host "Adding the certificate content to Python Cert store"
    Add-Content "${env:ProgramFiles(x86)}\Microsoft SDKs\Azure\CLI2\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

    Write-Host "Python Cert store was updated to allow the Azure Stack CA root certificate"
    ```

### <a name="connect-to-azure-stack"></a>Připojení ke službě Azure Stack

1. Zaregistrovat vaším prostředím Azure Stack spuštěním `az cloud register` příkazu.

    V některých případech se směruje přímé odchozí připojení k Internetu prostřednictvím serveru proxy nebo brány firewall, která vynucuje SSL zachycení. V těchto případech `az cloud register` příkaz může selhat s chybou jako je například "Nepodařilo se získat koncových bodů z cloudu." Chcete-li tuto chybu vyřešit, můžete nastavit následující proměnné prostředí:

    ```shell  
    set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
    set ADAL_PYTHON_SSL_NO_VERIFY=1
    ```

2. Zaregistrujte vašeho prostředí. Při běhu používat následující parametry `az cloud register`.

    | Value | Příklad: | Popis |
    | --- | --- | --- |
    | Název prostředí | AzureStackUser | Použití `AzureStackUser` pro uživatelské prostředí. Pokud operátor, zadat `AzureStackAdmin`. |
    | Koncový bod Resource Manageru | https://management.local.azurestack.external | **ResourceManagerUrl** je v Azure Stack Development Kit (ASDK): `https://management.local.azurestack.external/` **ResourceManagerUrl** v integrovaných systémech je: `https://management.<region>.<fqdn>/` Načíst metadata vyžaduje: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` Pokud máte dotaz týkající se koncový bod integrovaný systém, obraťte se na váš operátor cloudu. |
    | Koncový bod úložiště | local.azurestack.external | `local.azurestack.external` je ASDK. Pro integrovaný systém můžete použít koncový bod pro váš systém.  |
    | Přípona trezor klíčů | . vault.local.azurestack.external | `.vault.local.azurestack.external` je ASDK. Pro integrovaný systém můžete použít koncový bod pro váš systém.  |
    | Image virtuálního počítače alias doc koncový bod- | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | Identifikátor URI dokumentu, který obsahuje aliasy image virtuálního počítače. Další informace najdete v tématu [### nastavení koncového bodu virtuálního počítače aliasy](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

1. Pomocí následujících příkazů nastavte aktivní prostředí.

      ```azurecli
      az cloud set -n <environmentname>
      ```

1. Aktualizujte konfiguraci vašeho prostředí použít profil pro konkrétní verze rozhraní API Azure Stack. Pokud chcete aktualizovat konfiguraci, spusťte následující příkaz:

    ```azurecli
    az cloud update --profile 2018-03-01-hybrid
   ```

    >[!NOTE]  
    >Pokud používáte verzi služby Azure Stack před sestavením. 1808, je nutné použít profilu verze rozhraní API **2017-03-09-profile** místo profilu verze rozhraní API **2018-03-01hybridní**. Je potřeba použít nejnovější verzi Azure CLI.

1. Přihlaste se k prostředí Azure Stack pomocí `az login` příkazu. Můžete se přihlásit k prostředí Azure Stack jako uživatel, nebo jako [instanční objekt služby](/azure/active-directory/develop/app-objects-and-service-principals). 

   - Přihlaste se jako *uživatele*:

     Můžete zadat uživatelské jméno a heslo přímo v rámci `az login` příkaz nebo ověřování pomocí prohlížeče. Je nutné provést ten, pokud má váš účet zapnuté vícefaktorové ověřování:

     ```azurecli
     az cloud register  -n <environmentname>   --endpoint-resource-manager "https://management.local.azurestack.external"  --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-active-directory-resource-id "https://management.adfs.azurestack.local/<tenantID>" --endpoint-active-directory-graph-resource-id "https://graph.local.azurestack.external/" --endpoint-active-directory "https://adfs.local.azurestack.external/adfs/" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>   --profile "2018-03-01-hybrid"
     ```

     > [!NOTE]
     > Pokud váš uživatelský účet má povolené ověřování službou Multi-Factor Authentication, můžete použít `az login` příkaz bez zadání `-u` parametru. Spuštění tohoto příkazu obsahuje adresu URL a kód, který je nutné použít k ověření.

   - Přihlaste se jako *instanční objekt služby*: 
    
     Připravte soubor .pem, který má být použit pro přihlášením instančního objektu.

     Na klientském počítači, kde byl vytvořen objekt zabezpečení a export certifikátu instančního objektu služby, jako pfx pomocí soukromého klíče umístěné na `cert:\CurrentUser\My`; certifikátu název má stejný název jako objekt zabezpečení.

     Převeďte soubor pfx na pem (použijte nástroj OpenSSL).

     Přihlaste se k rozhraní příkazového řádku:
  
     ```azurecli  
     az login --service-principal \
      -u <Client ID from the Service Principal details> \
      -p <Certificate's fully qualified name, such as, C:\certs\spn.pem>
      --tenant <Tenant ID> \
      --debug 
     ```

### <a name="test-the-connectivity"></a>Otestovat připojení

Všechna nastavení pomocí CLI vytvářet prostředky v rámci služby Azure Stack. Můžete například vytvořit skupinu prostředků pro aplikaci a přidejte virtuální počítač. Chcete-li vytvořit skupinu prostředků s názvem "MyResourceGroup", použijte následující příkaz:

```azurecli
az group create -n MyResourceGroup -l local
```

Pokud skupina prostředků je úspěšně vytvořen, předchozí příkaz vypíše následující vlastnosti nově vytvořeného prostředku:

![Vytvoření výstupní skupiny prostředků](media/azure-stack-connect-cli/image1.png)


## <a name="linux-azure-ad"></a>Linux (Azure AD)

Tato část vás provede procesem nastavení rozhraní příkazového řádku Pokud používáte Azure AD jako služba pro správu identit a jsou na počítači s Linuxem pomocí rozhraní příkazového řádku.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Důvěřovat certifikátu kořenové certifikační Autority Azure stacku

Pokud používáte ASDK, musíte důvěřovat certifikátu kořenové certifikační Autority na vzdáleném počítači. Nebude potřeba to udělat pomocí integrovaných systémů.

Důvěřujete certifikátu kořenové certifikační Autority Azure stacku připojením k existující certifikát Python.

1. Najdete umístění certifikátu na svém počítači. Umístění se může lišit v závislosti na tom, kam jste nainstalovali Python. Je potřeba mít pip a nainstalovaným modulem osobní. Můžete použít následující příkaz Pythonu na příkazovém řádku bash:

    ```bash  
    python3 -c "import certifi; print(certifi.where())"
    ```

    Poznamenejte si umístění certifikátu; například `~/lib/python3.5/site-packages/certifi/cacert.pem`. Konkrétní cesty závisí na operačním systému a verzi Pythonu, který jste nainstalovali.

2. Spusťte následující příkaz prostředí bash s cestou k vašemu certifikátu.

   - Pro vzdáleném počítači s Linuxem:

     ```bash  
     sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
     ```

   - Pro počítače s Linuxem v prostředí Azure Stack:

     ```bash  
     sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
     ```

### <a name="connect-to-azure-stack"></a>Připojení ke službě Azure Stack

Následující kroky použijte pro připojení ke službě Azure Stack:

1. Zaregistrovat vaším prostředím Azure Stack spuštěním `az cloud register` příkazu. V některých případech se směruje přímé odchozí připojení k Internetu prostřednictvím serveru proxy nebo brány firewall, která vynucuje SSL zachycení. V těchto případech `az cloud register` příkaz může selhat s chybou jako je například "Nepodařilo se získat koncových bodů z cloudu." Chcete-li tuto chybu vyřešit, můžete nastavit následující proměnné prostředí:

   ```shell
   set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
   set ADAL_PYTHON_SSL_NO_VERIFY=1
   ```

2. Zaregistrujte vašeho prostředí. Při běhu používat následující parametry `az cloud register`.

    | Value | Příklad: | Popis |
    | --- | --- | --- |
    | Název prostředí | AzureStackUser | Použití `AzureStackUser` pro uživatelské prostředí. Pokud operátor, zadat `AzureStackAdmin`. |
    | Koncový bod Resource Manageru | https://management.local.azurestack.external | **ResourceManagerUrl** je v Azure Stack Development Kit (ASDK): `https://management.local.azurestack.external/` **ResourceManagerUrl** v integrovaných systémech je: `https://management.<region>.<fqdn>/` Načíst metadata vyžaduje: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` Pokud máte dotaz týkající se koncový bod integrovaný systém, obraťte se na váš operátor cloudu. |
    | Koncový bod úložiště | local.azurestack.external | `local.azurestack.external` je ASDK. Pro integrovaný systém můžete použít koncový bod pro váš systém.  |
    | Přípona trezor klíčů | . vault.local.azurestack.external | `.vault.local.azurestack.external` je ASDK. Pro integrovaný systém můžete použít koncový bod pro váš systém.  |
    | Image virtuálního počítače alias doc koncový bod- | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | Identifikátor URI dokumentu, který obsahuje aliasy image virtuálního počítače. Další informace najdete v tématu [### nastavení koncového bodu virtuálního počítače aliasy](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

3. Nastavte aktivní prostředí. 

      ```azurecli
        az cloud set -n <environmentname>
      ```

4. Aktualizujte konfiguraci vašeho prostředí použít profil pro konkrétní verze rozhraní API Azure Stack. Pokud chcete aktualizovat konfiguraci, spusťte následující příkaz:

    ```azurecli
      az cloud update --profile 2018-03-01-hybrid
   ```

    >[!NOTE]  
    >Pokud používáte verzi služby Azure Stack před sestavením. 1808, je nutné použít profilu verze rozhraní API **2017-03-09-profile** místo profilu verze rozhraní API **2018-03-01hybridní**. Je potřeba použít nejnovější verzi Azure CLI.

5. Přihlaste se k prostředí Azure Stack pomocí `az login` příkazu. Můžete se přihlásit k prostředí Azure Stack jako uživatel, nebo jako [instanční objekt služby](/azure/active-directory/develop/app-objects-and-service-principals). 

   * Přihlaste se jako *uživatele*:

     Můžete zadat uživatelské jméno a heslo přímo v rámci `az login` příkaz nebo ověřování pomocí prohlížeče. Je nutné provést ten, pokud má váš účet zapnuté vícefaktorové ověřování:

     ```azurecli
     az login \
       -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> \
       --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
     ```

     > [!NOTE]
     > Pokud váš uživatelský účet má povolené ověřování službou Multi-Factor Authentication, můžete použít `az login` příkaz bez zadání `-u` parametru. Spuštění tohoto příkazu obsahuje adresu URL a kód, který je nutné použít k ověření.
   
   * Přihlaste se jako *instančního objektu*
    
     Před přihlášením, [vytvoření instančního objektu služby na webu Azure portal](azure-stack-create-service-principals.md) nebo rozhraní příkazového řádku a přiřaďte ho roli. Teď se přihlaste pomocí následujícího příkazu:

     ```azurecli  
     az login \
       --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> \
       --service-principal \
       -u <Application Id of the Service Principal> \
       -p <Key generated for the Service Principal>
     ```

### <a name="test-the-connectivity"></a>Otestovat připojení

Všechna nastavení pomocí CLI vytvářet prostředky v rámci služby Azure Stack. Můžete například vytvořit skupinu prostředků pro aplikaci a přidejte virtuální počítač. Chcete-li vytvořit skupinu prostředků s názvem "MyResourceGroup", použijte následující příkaz:

```azurecli
    az group create -n MyResourceGroup -l local
```

Pokud skupina prostředků je úspěšně vytvořen, předchozí příkaz vypíše následující vlastnosti nově vytvořeného prostředku:

![Vytvoření výstupní skupiny prostředků](media/azure-stack-connect-cli/image1.png)

## <a name="linux-ad-fs"></a>Linux (AD FS)

Tato část vás provede procesem nastavení rozhraní příkazového řádku Pokud používáte jako správy služby Active Directory Federated Services (AD FS) a jsou na počítači s Linuxem pomocí rozhraní příkazového řádku.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Důvěřovat certifikátu kořenové certifikační Autority Azure stacku

Pokud používáte ASDK, musíte důvěřovat certifikátu kořenové certifikační Autority na vzdáleném počítači. Nebude potřeba to udělat pomocí integrovaných systémů.

Důvěřujete certifikátu kořenové certifikační Autority Azure stacku připojením k existující certifikát Python.

1. Najdete umístění certifikátu na svém počítači. Umístění se může lišit v závislosti na tom, kam jste nainstalovali Python. Je potřeba mít pip a nainstalovaným modulem osobní. Můžete použít následující příkaz Pythonu na příkazovém řádku bash:

    ```bash  
    python3 -c "import certifi; print(certifi.where())"
    ```

    Poznamenejte si umístění certifikátu; například `~/lib/python3.5/site-packages/certifi/cacert.pem`. Konkrétní cesty závisí na operačním systému a verzi Pythonu, který jste nainstalovali.

2. Spusťte následující příkaz prostředí bash s cestou k vašemu certifikátu.

   - Pro vzdáleném počítači s Linuxem:

     ```bash  
     sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
     ```

   - Pro počítače s Linuxem v prostředí Azure Stack:

     ```bash  
     sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
     ```

### <a name="connect-to-azure-stack"></a>Připojení ke službě Azure Stack

Následující kroky použijte pro připojení ke službě Azure Stack:

1. Zaregistrovat vaším prostředím Azure Stack spuštěním `az cloud register` příkazu. V některých případech se směruje přímé odchozí připojení k Internetu prostřednictvím serveru proxy nebo brány firewall, která vynucuje SSL zachycení. V těchto případech `az cloud register` příkaz může selhat s chybou jako je například "Nepodařilo se získat koncových bodů z cloudu." Chcete-li tuto chybu vyřešit, můžete nastavit následující proměnné prostředí:

   ```shell
   set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
   set ADAL_PYTHON_SSL_NO_VERIFY=1
   ```

2. Zaregistrujte vašeho prostředí. Při běhu používat následující parametry `az cloud register`.

    | Value | Příklad: | Popis |
    | --- | --- | --- |
    | Název prostředí | AzureStackUser | Použití `AzureStackUser` pro uživatelské prostředí. Pokud operátor, zadat `AzureStackAdmin`. |
    | Koncový bod Resource Manageru | https://management.local.azurestack.external | **ResourceManagerUrl** je v Azure Stack Development Kit (ASDK): `https://management.local.azurestack.external/` **ResourceManagerUrl** v integrovaných systémech je: `https://management.<region>.<fqdn>/` Načíst metadata vyžaduje: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0` Pokud máte dotaz týkající se koncový bod integrovaný systém, obraťte se na váš operátor cloudu. |
    | Koncový bod úložiště | local.azurestack.external | `local.azurestack.external` je ASDK. Pro integrovaný systém můžete použít koncový bod pro váš systém.  |
    | Přípona trezor klíčů | . vault.local.azurestack.external | `.vault.local.azurestack.external` je ASDK. Pro integrovaný systém můžete použít koncový bod pro váš systém.  |
    | Image virtuálního počítače alias doc koncový bod- | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | Identifikátor URI dokumentu, který obsahuje aliasy image virtuálního počítače. Další informace najdete v tématu [### nastavení koncového bodu virtuálního počítače aliasy](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

3. Nastavte aktivní prostředí. 

      ```azurecli
        az cloud set -n <environmentname>
      ```

4. Aktualizujte konfiguraci vašeho prostředí použít profil pro konkrétní verze rozhraní API Azure Stack. Pokud chcete aktualizovat konfiguraci, spusťte následující příkaz:

    ```azurecli
      az cloud update --profile 2018-03-01-hybrid
   ```

    >[!NOTE]  
    >Pokud používáte verzi služby Azure Stack před sestavením. 1808, je nutné použít profilu verze rozhraní API **2017-03-09-profile** místo profilu verze rozhraní API **2018-03-01hybridní**. Je potřeba použít nejnovější verzi Azure CLI.

5. Přihlaste se k prostředí Azure Stack pomocí `az login` příkazu. Můžete se přihlásit k prostředí Azure Stack jako uživatel, nebo jako [instanční objekt služby](/azure/active-directory/develop/app-objects-and-service-principals). 

6. Přihlásit se: 

   *  Jako **uživatele** pomocí webového prohlížeče s kódem zařízení:  

   ```azurecli  
    az login --use-device-code
   ```

   > [!NOTE]  
   >Spuštěním příkazu poskytuje adresu URL a kód, který je nutné použít k ověření.

   * Jako hlavní název služby:
        
     Připravte soubor .pem, který má být použit pro přihlášením instančního objektu.

      * Na klientském počítači, kde byl vytvořen objekt zabezpečení a export certifikátu instančního objektu služby, jako pfx pomocí soukromého klíče umístěné na `cert:\CurrentUser\My`; certifikátu název má stejný název jako objekt zabezpečení.
  
      * Převeďte soubor pfx na pem (použijte nástroj OpenSSL).

     Přihlaste se k rozhraní příkazového řádku:

      ```azurecli  
      az login --service-principal \
        -u <Client ID from the Service Principal details> \
        -p <Certificate's fully qualified name, such as, C:\certs\spn.pem>
        --tenant <Tenant ID> \
        --debug 
      ```

### <a name="test-the-connectivity"></a>Otestovat připojení

Všechna nastavení pomocí CLI vytvářet prostředky v rámci služby Azure Stack. Můžete například vytvořit skupinu prostředků pro aplikaci a přidejte virtuální počítač. Chcete-li vytvořit skupinu prostředků s názvem "MyResourceGroup", použijte následující příkaz:

```azurecli
  az group create -n MyResourceGroup -l local
```

Pokud skupina prostředků je úspěšně vytvořen, předchozí příkaz vypíše následující vlastnosti nově vytvořeného prostředku:

![Vytvoření výstupní skupiny prostředků](media/azure-stack-connect-cli/image1.png)

## <a name="known-issues"></a>Známé problémy

Existují známé problémy při použití rozhraní příkazového řádku ve službě Azure Stack:

 - Interaktivní režim rozhraní příkazového řádku; například `az interactive` příkazu, ve službě Azure Stack se ještě nepodporuje.
 - Chcete-li získat seznam dostupných ve službě Azure Stack imagím virtuálních počítačů, použijte `az vm image list --all` příkaz místo `az vm image list` příkaz. Zadání `--all` možnost zajišťuje, že odpověď vrátí pouze obrázky, které jsou dostupné v prostředí Azure Stack.
 - Aliasy image virtuálního počítače, které jsou k dispozici v Azure nemusí být k dispozici ke službě Azure Stack. Při použití imagí virtuálních počítačů, musíte použít parametr celý název URN (Canonical: UbuntuServer:14.04.3-LTS:1.0.0) místo aliasu image. Tento název URN musí odpovídat specifikaci bitové kopie odvozena z `az vm images list` příkazu.

## <a name="next-steps"></a>Další postup

- [Nasazení šablon pomocí Azure CLI](azure-stack-deploy-template-command-line.md)
- [Povolení rozhraní příkazového řádku Azure pro uživatele Azure stacku (operátor)](../operator/azure-stack-cli-admin.md)
- [Správa uživatelských oprávnění](azure-stack-manage-permissions.md) 
