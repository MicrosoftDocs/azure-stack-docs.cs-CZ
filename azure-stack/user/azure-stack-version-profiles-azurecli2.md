---
title: Připojení k Azure Stack pomocí rozhraní příkazového řádku | Microsoft Docs
description: Naučte se používat rozhraní příkazového řádku (CLI) pro různé platformy ke správě a nasazení prostředků v Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/16/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/08/2019
ms.openlocfilehash: 788d0fd6479ab054568d549af2f7a4306a963d3b
ms.sourcegitcommit: 4139b507d6da98a086929da48e3b4661b70bc4f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2019
ms.locfileid: "68299443"
---
# <a name="use-api-version-profiles-with-azure-cli-in-azure-stack"></a>Použití profilů verzí rozhraní API se službou Azure CLI v systému Azure Stack

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Podle kroků v tomto článku můžete nastavit rozhraní příkazového řádku Azure (CLI) pro správu prostředků Azure Stack Development Kit (ASDK) z klientských platforem Linux, Mac a Windows.

## <a name="prepare-for-azure-cli"></a>Příprava pro Azure CLI

Pokud používáte Azure Stack Development Kit, budete potřebovat kořenový certifikát certifikační autority pro Azure Stack použití rozhraní příkazového řádku Azure CLI na vašem vývojovém počítači. Certifikát použijete ke správě prostředků přes rozhraní příkazového řádku.

 - Pokud používáte rozhraní příkazového řádku z pracovní stanice mimo ASDK, je vyžadován **kořenový certifikát CA Azure Stack** .  

 - **Aliasy aliasů virtuálních počítačů** poskytují alias, například "UbuntuLTS" nebo "Win2012Datacenter", který odkazuje na vydavatele image, nabídku, SKU a verzi jako jeden parametr při nasazování virtuálních počítačů.  

Následující části popisují, jak tyto hodnoty získat.

### <a name="export-the-azure-stack-ca-root-certificate"></a>Export kořenového certifikátu certifikační autority Azure Stack

Pokud používáte integrovaný systém, nemusíte exportovat kořenový certifikát certifikační autority. Bude nutné exportovat kořenový certifikát certifikační autority do ASDK.

Export kořenového certifikátu ASDK ve formátu PEM:

1. Získání názvu kořenového certifikátu Azure Stack:
    - Přihlaste se do tenanta Azure Stack nebo na portál pro správu.
    - Klikněte na tlačítko "zabezpečit" poblíž panelu Adresa.
    - V automaticky otevíraném okně klikněte na platné.
    - V okně certifikát klikněte na kartu cesta k certifikaci. 
    - Poznamenejte si název vašeho kořenového certifikátu Azure Stack.

    ![Azure Stack kořenový certifikát](media/azure-stack-version-profiles-azurecli2/root-cert-name.png)

2. [Vytvořte virtuální počítač s Windows na Azure Stack](azure-stack-quick-windows-portal.md).

3. Přihlaste se k počítači, otevřete příkazový řádek prostředí PowerShell se zvýšenými oprávněními a spusťte následující skript:

    ```powershell  
      $label = "<the name of your azure stack root cert from Step 1>"
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

4. Zkopírujte certifikát do místního počítače.


### <a name="set-up-the-virtual-machine-aliases-endpoint"></a>Nastavte koncový bod aliasy virtuálních počítačů.

Můžete nastavit veřejně přístupný koncový bod, který je hostitelem souboru aliasu virtuálního počítače. Soubor aliasu virtuálního počítače je soubor JSON, který poskytuje běžný název pro obrázek. Tento název použijete při nasazení virtuálního počítače jako parametru Azure CLI.

1. Pokud publikujete vlastní image, poznamenejte si informace o vydavateli, nabídce, SKU a verzi, které jste zadali během publikování. Pokud se jedná o image z webu Marketplace, můžete zobrazit informace pomocí ```Get-AzureVMImage``` rutiny.  

2. Stáhněte si [ukázkový soubor](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) z GitHubu.

3. Vytvořte účet úložiště v Azure Stack. Až to bude hotové, vytvořte kontejner objektů BLOB. Nastavte zásady přístupu na veřejné.  

4. Nahrajte soubor JSON do nového kontejneru. Až to uděláte, můžete zobrazit adresu URL objektu BLOB. Vyberte název objektu BLOB a potom vyberte adresu URL z vlastností objektu BLOB.

### <a name="install-or-upgrade-cli"></a>Instalace nebo upgrade rozhraní příkazového řádku

Přihlaste se k vývojové pracovní stanici a nainstalujte rozhraní příkazového řádku. Azure Stack vyžaduje Azure CLI verze 2,0 nebo novější. Nejnovější verze profilů rozhraní API vyžaduje aktuální verzi rozhraní příkazového řádku.  Rozhraní příkazového řádku můžete nainstalovat pomocí postupu popsaného v článku [instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) . 

1. Chcete-li ověřit, zda byla instalace úspěšná, otevřete okno terminálu nebo příkazového řádku a spusťte následující příkaz:

    ```shell
    az --version
    ```

    Měla by se zobrazit verze rozhraní příkazového řádku Azure CLI a dalších závislých knihoven, které jsou nainstalované ve vašem počítači.

    ![Rozhraní příkazového řádku Azure v umístění Azure Stack Pythonu](media/azure-stack-version-profiles-azurecli2/cli-python-location.png)

2. Poznamenejte si umístění Pythonu pro rozhraní příkazového řádku. Pokud používáte ASDK, budete muset použít toto umístění k přidání certifikátu.


## <a name="windows-azure-ad"></a>Windows (Azure AD)

Tato část vás provede nastavením CLI, pokud používáte Azure AD jako službu pro správu identit a používáte rozhraní příkazového řádku v počítači s Windows.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Důvěřovat kořenovému certifikátu certifikační autority Azure Stack

Pokud používáte ASDK, budete muset na svém vzdáleném počítači důvěřovat kořenovému certifikátu certifikační autority. To nebudete muset provádět s integrovanými systémy.

Pokud chcete důvěřovat kořenovému certifikátu certifikační autority Azure Stack, připojovat ho k existujícímu úložišti certifikátů Pythonu pro verzi Pythonu nainstalovanou pomocí Azure CLI. Možná budete pracovat s vlastní instancí Pythonu. Azure CLI obsahuje svou vlastní verzi Pythonu.

1. Najděte umístění úložiště certifikátů na vašem počítači.  Umístění můžete najít spuštěním příkazu `az --version`.

2. Přejděte do složky, která obsahuje aplikaci CLI v Pythonu. Tuto verzi Pythonu budete chtít spustit. Pokud jste v systémové cestě nastavili Python, spustí Python svou vlastní verzi Pythonu. Místo toho budete chtít spustit verzi používanou rozhraním CLI a přidat do této verze svůj certifikát. Například rozhraní CLI Python může být v: `C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\`.

    Použijte následující příkazy:

    ```powershell  
    cd "c:\pathtoyourcliversionofpython"
    .\python -c "import certifi; print(certifi.where())"
    ```

    Poznamenejte si umístění certifikátu. Například, `C:\Program Files (x86)\Microsoft SDKs\Azure\CLI2\lib\site-packages\certifi\cacert.pem`. Vaše konkrétní cesta bude záviset na vašem operačním systému a instalaci rozhraní příkazového řádku.

2. Důvěřování kořenovému certifikátu certifikační autority Azure Stack připojením k existujícímu certifikátu Pythonu.

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

1. Zaregistrujte Azure Stack prostředí spuštěním `az cloud register` příkazu.

    V některých scénářích je přímé odchozí připojení k Internetu směrováno prostřednictvím proxy serveru nebo brány firewall, která vynutila zachycení SSL. V těchto případech `az cloud register` může příkaz selhat s chybou, jako je například "nelze získat koncové body z cloudu". Tuto chybu můžete obejít tak, že nastavíte následující proměnné prostředí:

    ```shell  
    set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
    set ADAL_PYTHON_SSL_NO_VERIFY=1
    ```

2. Zaregistrujte své prostředí. Při spuštění `az cloud register`použijte následující parametry.

    | Hodnota | Příklad | Popis |
    | --- | --- | --- |
    | Název prostředí | AzureStackUser | Použijte `AzureStackUser` pro uživatelské prostředí. Pokud jste operátor, zadejte `AzureStackAdmin`. |
    | Koncový bod Resource Manageru | https://management.local.azurestack.external | **ResourceManagerUrl** v Azure Stack Development Kit (ASDK) je: `https://management.local.azurestack.external/`**ResourceManagerUrl** v integrovaných systémech je: `https://management.<region>.<fqdn>/`Načtení požadovaných metadat: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`Pokud máte dotaz týkající se integrovaného systémového koncového bodu, obraťte se na svého operátora cloudu. |
    | Koncový bod úložiště | local.azurestack.external | `local.azurestack.external`je pro rozhraní ASDK. Pro integrovaný systém budete chtít pro svůj systém použít koncový bod.  |
    | Přípona trezoru klíčů | . trezor. Local. azurestack. external | `.vault.local.azurestack.external`je pro rozhraní ASDK. Pro integrovaný systém budete chtít pro svůj systém použít koncový bod.  |
    | Koncový bod dokumentu aliasu pro image virtuálního počítače – | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | Identifikátor URI dokumentu, který obsahuje aliasy imagí virtuálních počítačů Další informace najdete v tématu [# # # Nastavení koncového bodu aliasů virtuálních počítačů](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

1. Nastavte aktivní prostředí pomocí následujících příkazů.

      ```azurecli
      az cloud set -n <environmentname>
      ```

1. Aktualizujte konfiguraci prostředí tak, aby používala konkrétní profil verze rozhraní API Azure Stack. Chcete-li aktualizovat konfiguraci, spusťte následující příkaz:

    ```azurecli
    az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >Pokud používáte verzi Azure Stack před sestavením 1808, musíte použít profil verze API **2017-03-09-Profile** , nikoli profil verze rozhraní API **2019-03-01 – Hybrid**. Budete muset použít nejnovější verzi rozhraní příkazového řádku Azure CLI.
 
1. Přihlaste se ke svému Azure Stack prostředí pomocí `az login` příkazu. K prostředí Azure Stack se můžete přihlásit buď jako uživatel, nebo jako [instanční objekt](/azure/active-directory/develop/app-objects-and-service-principals). 

   - Přihlaste se jako *uživatel*: 

     Můžete buď zadat uživatelské jméno a heslo přímo v rámci `az login` příkazu, nebo ověřit pomocí prohlížeče. Pokud má váš účet povolené ověřování Multi-Factor Authentication, musíte to udělat:

     ```azurecli
     az login -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
     ```

     > [!NOTE]
     > Pokud má váš uživatelský účet povolený vícefaktorové ověřování, můžete použít `az login` příkaz bez `-u` zadání parametru. Spuštěním tohoto příkazu získáte adresu URL a kód, který je nutné použít k ověření.

   - Přihlaste se jako *instanční objekt*: 
    
     Než se přihlásíte, [vytvořte instanční objekt pomocí Azure Portal nebo rozhraní](azure-stack-create-service-principals.md) příkazového řádku a přiřaďte mu roli. Teď se přihlaste pomocí následujícího příkazu:

     ```azurecli  
     az login --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> --service-principal -u <Application Id of the Service Principal> -p <Key generated for the Service Principal>
     ```

### <a name="test-the-connectivity"></a>Otestovat připojení

Když máte všechno nastavené, pomocí rozhraní příkazového řádku můžete vytvářet prostředky v rámci Azure Stack. Můžete například vytvořit skupinu prostředků pro aplikaci a přidejte virtuální počítač. Pomocí následujícího příkazu vytvořte skupinu prostředků s názvem "MyResourceGroup":

```azurecli
az group create -n MyResourceGroup -l local
```

Pokud je skupina prostředků úspěšně vytvořená, předchozí příkaz vytvoří výstup následujících vlastností nově vytvořeného prostředku:

![Skupina prostředků – vytvořit výstup](media/azure-stack-connect-cli/image1.png)

## <a name="windows-ad-fs"></a>Windows (AD FS)

V této části se dozvíte, jak nastavit rozhraní příkazového řádku, pokud jako službu pro správu identit používáte službu Active Directory federovaného Services (AD FS) a používáte rozhraní příkazového řádku (CLI) na počítači s Windows.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Důvěřovat kořenovému certifikátu certifikační autority Azure Stack

Pokud používáte ASDK, budete muset na svém vzdáleném počítači důvěřovat kořenovému certifikátu certifikační autority. To nebudete muset provádět s integrovanými systémy.

1. Najděte umístění certifikátu na svém počítači. Umístění se může lišit v závislosti na tom, kde jste nainstalovali Python. Otevřete příkazový řádek nebo příkazový řádek PowerShell se zvýšenými oprávněními a zadejte tento příkaz:

    ```powershell  
      python -c "import certifi; print(certifi.where())"
    ```

    Poznamenejte si umístění certifikátu. Například, `~/lib/python3.5/site-packages/certifi/cacert.pem`. Vaše konkrétní cesta bude záviset na vašem operačním systému a verzi Pythonu, kterou jste nainstalovali.

2. Důvěřování kořenovému certifikátu certifikační autority Azure Stack připojením k existujícímu certifikátu Pythonu.

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

1. Zaregistrujte Azure Stack prostředí spuštěním `az cloud register` příkazu.

    V některých scénářích je přímé odchozí připojení k Internetu směrováno prostřednictvím proxy serveru nebo brány firewall, která vynutila zachycení SSL. V těchto případech `az cloud register` může příkaz selhat s chybou, jako je například "nelze získat koncové body z cloudu". Tuto chybu můžete obejít tak, že nastavíte následující proměnné prostředí:

    ```shell  
    set AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1 
    set ADAL_PYTHON_SSL_NO_VERIFY=1
    ```

2. Zaregistrujte své prostředí. Při spuštění `az cloud register`použijte následující parametry.

    | Value | Příklad | Popis |
    | --- | --- | --- |
    | Název prostředí | AzureStackUser | Použijte `AzureStackUser` pro uživatelské prostředí. Pokud jste operátor, zadejte `AzureStackAdmin`. |
    | Koncový bod Resource Manageru | https://management.local.azurestack.external | **ResourceManagerUrl** v Azure Stack Development Kit (ASDK) je: `https://management.local.azurestack.external/`**ResourceManagerUrl** v integrovaných systémech je: `https://management.<region>.<fqdn>/`Načtení požadovaných metadat: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`Pokud máte dotaz týkající se integrovaného systémového koncového bodu, obraťte se na svého operátora cloudu. |
    | Koncový bod úložiště | local.azurestack.external | `local.azurestack.external`je pro rozhraní ASDK. Pro integrovaný systém budete chtít pro svůj systém použít koncový bod.  |
    | Přípona trezoru klíčů | . trezor. Local. azurestack. external | `.vault.local.azurestack.external`je pro rozhraní ASDK. Pro integrovaný systém budete chtít pro svůj systém použít koncový bod.  |
    | Koncový bod dokumentu aliasu pro image virtuálního počítače – | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | Identifikátor URI dokumentu, který obsahuje aliasy imagí virtuálních počítačů Další informace najdete v tématu [# # # Nastavení koncového bodu aliasů virtuálních počítačů](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

1. Nastavte aktivní prostředí pomocí následujících příkazů.

      ```azurecli
      az cloud set -n <environmentname>
      ```

1. Aktualizujte konfiguraci prostředí tak, aby používala konkrétní profil verze rozhraní API Azure Stack. Chcete-li aktualizovat konfiguraci, spusťte následující příkaz:

    ```azurecli
    az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >Pokud používáte verzi Azure Stack před sestavením 1808, musíte použít profil verze API **2017-03-09-Profile** , nikoli profil verze rozhraní API **2019-03-01 – Hybrid**. Budete muset použít nejnovější verzi rozhraní příkazového řádku Azure CLI.

1. Přihlaste se ke svému Azure Stack prostředí pomocí `az login` příkazu. K prostředí Azure Stack se můžete přihlásit buď jako uživatel, nebo jako [instanční objekt](/azure/active-directory/develop/app-objects-and-service-principals). 

   - Přihlaste se jako *uživatel*:

     Můžete buď zadat uživatelské jméno a heslo přímo v rámci `az login` příkazu, nebo ověřit pomocí prohlížeče. Pokud má váš účet povolené ověřování Multi-Factor Authentication, musíte to udělat:

     ```azurecli
     az cloud register  -n <environmentname>   --endpoint-resource-manager "https://management.local.azurestack.external"  --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>   --profile "2019-03-01-hybrid"
     ```

     > [!NOTE]
     > Pokud má váš uživatelský účet povolený vícefaktorové ověřování, můžete použít `az login` příkaz bez `-u` zadání parametru. Spuštěním tohoto příkazu získáte adresu URL a kód, který je nutné použít k ověření.

   - Přihlaste se jako *instanční objekt*: 
    
     Připravte soubor. pem, který se má použít pro přihlašovací objekty instančního objektu.

     V klientském počítači, kde byl vytvořen objekt zabezpečení, exportujte certifikát instančního objektu jako PFX s privátním klíčem umístěným `cert:\CurrentUser\My`na adrese; název certifikátu má stejný název jako objekt zabezpečení.

     Převeďte PFX na PEM (použijte nástroj OpenSSL).

     Přihlaste se k rozhraní příkazového řádku:
  
     ```azurecli  
     az login --service-principal \
      -u <Client ID from the Service Principal details> \
      -p <Certificate's fully qualified name, such as, C:\certs\spn.pem>
      --tenant <Tenant ID> \
      --debug 
     ```

### <a name="test-the-connectivity"></a>Otestovat připojení

Když máte všechno nastavené, pomocí rozhraní příkazového řádku můžete vytvářet prostředky v rámci Azure Stack. Můžete například vytvořit skupinu prostředků pro aplikaci a přidejte virtuální počítač. Pomocí následujícího příkazu vytvořte skupinu prostředků s názvem "MyResourceGroup":

```azurecli
az group create -n MyResourceGroup -l local
```

Pokud je skupina prostředků úspěšně vytvořená, předchozí příkaz vytvoří výstup následujících vlastností nově vytvořeného prostředku:

![Skupina prostředků – vytvořit výstup](media/azure-stack-connect-cli/image1.png)


## <a name="linux-azure-ad"></a>Linux (Azure AD)

Tato část vás provede nastavením CLI, pokud používáte Azure AD jako službu pro správu identit a používáte rozhraní příkazového řádku v počítači se systémem Linux.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Důvěřovat kořenovému certifikátu certifikační autority Azure Stack

Pokud používáte ASDK, budete muset na svém vzdáleném počítači důvěřovat kořenovému certifikátu certifikační autority. To nebudete muset provádět s integrovanými systémy.

Důvěřování kořenovému certifikátu certifikační autority Azure Stack připojením k existujícímu certifikátu Pythonu.

1. Najděte umístění certifikátu na svém počítači. Umístění se může lišit v závislosti na tom, kde jste nainstalovali Python. Budete muset mít nainstalovaný PIP a modul certifi. Z příkazového řádku bash můžete použít následující příkaz Pythonu:

    ```bash  
    python3 -c "import certifi; print(certifi.where())"
    ```

    Poznamenejte si umístění certifikátu; například `~/lib/python3.5/site-packages/certifi/cacert.pem`. Vaše konkrétní cesta závisí na vašem operačním systému a verzi Pythonu, kterou jste nainstalovali.

2. Spusťte následující příkaz bash s cestou k vašemu certifikátu.

   - Pro vzdálený počítač se systémem Linux:

     ```bash  
     sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
     ```

   - Pro počítač se systémem Linux v prostředí Azure Stack:

     ```bash  
     sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
     ```

### <a name="connect-to-azure-stack"></a>Připojení ke službě Azure Stack

Pomocí následujících kroků se připojte k Azure Stack:

1. Zaregistrujte Azure Stack prostředí spuštěním `az cloud register` příkazu. V některých scénářích je přímé odchozí připojení k Internetu směrováno prostřednictvím proxy serveru nebo brány firewall, která vynutila zachycení SSL. V těchto případech `az cloud register` může příkaz selhat s chybou, jako je například "nelze získat koncové body z cloudu". Tuto chybu můžete obejít tak, že nastavíte následující proměnné prostředí:

   ```shell
   export AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1
   export ADAL_PYTHON_SSL_NO_VERIFY=1
   ```

2. Zaregistrujte své prostředí. Při spuštění `az cloud register`použijte následující parametry.

    | Hodnota | Příklad | Popis |
    | --- | --- | --- |
    | Název prostředí | AzureStackUser | Použijte `AzureStackUser` pro uživatelské prostředí. Pokud jste operátor, zadejte `AzureStackAdmin`. |
    | Koncový bod Resource Manageru | https://management.local.azurestack.external | **ResourceManagerUrl** v Azure Stack Development Kit (ASDK) je: `https://management.local.azurestack.external/`**ResourceManagerUrl** v integrovaných systémech je: `https://management.<region>.<fqdn>/`Načtení požadovaných metadat: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`Pokud máte dotaz týkající se integrovaného systémového koncového bodu, obraťte se na svého operátora cloudu. |
    | Koncový bod úložiště | local.azurestack.external | `local.azurestack.external`je pro rozhraní ASDK. Pro integrovaný systém budete chtít pro svůj systém použít koncový bod.  |
    | Přípona trezoru klíčů | . trezor. Local. azurestack. external | `.vault.local.azurestack.external`je pro rozhraní ASDK. Pro integrovaný systém budete chtít pro svůj systém použít koncový bod.  |
    | Koncový bod dokumentu aliasu pro image virtuálního počítače – | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | Identifikátor URI dokumentu, který obsahuje aliasy imagí virtuálních počítačů Další informace najdete v tématu [# # # Nastavení koncového bodu aliasů virtuálních počítačů](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

3. Nastavte aktivní prostředí. 

      ```azurecli
        az cloud set -n <environmentname>
      ```

4. Aktualizujte konfiguraci prostředí tak, aby používala konkrétní profil verze rozhraní API Azure Stack. Chcete-li aktualizovat konfiguraci, spusťte následující příkaz:

    ```azurecli
      az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >Pokud používáte verzi Azure Stack před sestavením 1808, musíte použít profil verze API **2017-03-09-Profile** , nikoli profil verze rozhraní API **2019-03-01 – Hybrid**. Budete muset použít nejnovější verzi rozhraní příkazového řádku Azure CLI.

5. Přihlaste se ke svému Azure Stack prostředí pomocí `az login` příkazu. K prostředí Azure Stack se můžete přihlásit buď jako uživatel, nebo jako [instanční objekt](/azure/active-directory/develop/app-objects-and-service-principals). 

   * Přihlaste se jako *uživatel*:

     Můžete buď zadat uživatelské jméno a heslo přímo v rámci `az login` příkazu, nebo ověřit pomocí prohlížeče. Pokud má váš účet povolené ověřování Multi-Factor Authentication, musíte to udělat:

     ```azurecli
     az login \
       -u <Active directory global administrator or user account. For example: username@<aadtenant>.onmicrosoft.com> \
       --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com>
     ```

     > [!NOTE]
     > Pokud má váš uživatelský účet povolený vícefaktorové ověřování, můžete použít `az login` příkaz bez `-u` zadání parametru. Spuštěním tohoto příkazu získáte adresu URL a kód, který je nutné použít k ověření.
   
   * Přihlášení jako *instanční objekt*
    
     Než se přihlásíte, [vytvořte instanční objekt pomocí Azure Portal nebo rozhraní](azure-stack-create-service-principals.md) příkazového řádku a přiřaďte mu roli. Teď se přihlaste pomocí následujícího příkazu:

     ```azurecli  
     az login \
       --tenant <Azure Active Directory Tenant name. For example: myazurestack.onmicrosoft.com> \
       --service-principal \
       -u <Application Id of the Service Principal> \
       -p <Key generated for the Service Principal>
     ```

### <a name="test-the-connectivity"></a>Otestovat připojení

Když máte všechno nastavené, pomocí rozhraní příkazového řádku můžete vytvářet prostředky v rámci Azure Stack. Můžete například vytvořit skupinu prostředků pro aplikaci a přidejte virtuální počítač. Pomocí následujícího příkazu vytvořte skupinu prostředků s názvem "MyResourceGroup":

```azurecli
    az group create -n MyResourceGroup -l local
```

Pokud je skupina prostředků úspěšně vytvořená, předchozí příkaz vytvoří výstup následujících vlastností nově vytvořeného prostředku:

![Skupina prostředků – vytvořit výstup](media/azure-stack-connect-cli/image1.png)

## <a name="linux-ad-fs"></a>Linux (AD FS)

V této části se dozvíte, jak nastavit rozhraní příkazového řádku, pokud používáte službu Active Directory federovaným Services (AD FS) jako službu pro správu a používáte rozhraní příkazového řádku v počítači se systémem Linux.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Důvěřovat kořenovému certifikátu certifikační autority Azure Stack

Pokud používáte ASDK, budete muset na svém vzdáleném počítači důvěřovat kořenovému certifikátu certifikační autority. To nebudete muset provádět s integrovanými systémy.

Důvěřování kořenovému certifikátu certifikační autority Azure Stack připojením k existujícímu certifikátu Pythonu.

1. Najděte umístění certifikátu na svém počítači. Umístění se může lišit v závislosti na tom, kde jste nainstalovali Python. Budete muset mít nainstalovaný PIP a modul certifi. Z příkazového řádku bash můžete použít následující příkaz Pythonu:

    ```bash  
    python3 -c "import certifi; print(certifi.where())"
    ```

    Poznamenejte si umístění certifikátu; například `~/lib/python3.5/site-packages/certifi/cacert.pem`. Vaše konkrétní cesta závisí na vašem operačním systému a verzi Pythonu, kterou jste nainstalovali.

2. Spusťte následující příkaz bash s cestou k vašemu certifikátu.

   - Pro vzdálený počítač se systémem Linux:

     ```bash  
     sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
     ```

   - Pro počítač se systémem Linux v prostředí Azure Stack:

     ```bash  
     sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
     ```

### <a name="connect-to-azure-stack"></a>Připojení ke službě Azure Stack

Pomocí následujících kroků se připojte k Azure Stack:

1. Zaregistrujte Azure Stack prostředí spuštěním `az cloud register` příkazu. V některých scénářích je přímé odchozí připojení k Internetu směrováno prostřednictvím proxy serveru nebo brány firewall, která vynutila zachycení SSL. V těchto případech `az cloud register` může příkaz selhat s chybou, jako je například "nelze získat koncové body z cloudu". Tuto chybu můžete obejít tak, že nastavíte následující proměnné prostředí:

   ```shell
   export AZURE_CLI_DISABLE_CONNECTION_VERIFICATION=1
   export ADAL_PYTHON_SSL_NO_VERIFY=1
   ```

2. Zaregistrujte své prostředí. Při spuštění `az cloud register`použijte následující parametry.

    | Value | Příklad | Popis |
    | --- | --- | --- |
    | Název prostředí | AzureStackUser | Použijte `AzureStackUser` pro uživatelské prostředí. Pokud jste operátor, zadejte `AzureStackAdmin`. |
    | Koncový bod Resource Manageru | https://management.local.azurestack.external | **ResourceManagerUrl** v Azure Stack Development Kit (ASDK) je: `https://management.local.azurestack.external/`**ResourceManagerUrl** v integrovaných systémech je: `https://management.<region>.<fqdn>/`Načtení požadovaných metadat: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`Pokud máte dotaz týkající se integrovaného systémového koncového bodu, obraťte se na svého operátora cloudu. |
    | Koncový bod úložiště | local.azurestack.external | `local.azurestack.external`je pro rozhraní ASDK. Pro integrovaný systém budete chtít pro svůj systém použít koncový bod.  |
    | Přípona trezoru klíčů | . trezor. Local. azurestack. external | `.vault.local.azurestack.external`je pro rozhraní ASDK. Pro integrovaný systém budete chtít pro svůj systém použít koncový bod.  |
    | Koncový bod dokumentu aliasu pro image virtuálního počítače – | https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json | Identifikátor URI dokumentu, který obsahuje aliasy imagí virtuálních počítačů Další informace najdete v tématu [# # # Nastavení koncového bodu aliasů virtuálních počítačů](#set-up-the-virtual-machine-aliases-endpoint). |

    ```azurecli  
    az cloud register -n <environmentname> --endpoint-resource-manager "https://management.local.azurestack.external" --suffix-storage-endpoint "local.azurestack.external" --suffix-keyvault-dns ".vault.local.azurestack.external" --endpoint-vm-image-alias-doc <URI of the document which contains virtual machine image aliases>
    ```

3. Nastavte aktivní prostředí. 

      ```azurecli
        az cloud set -n <environmentname>
      ```

4. Aktualizujte konfiguraci prostředí tak, aby používala konkrétní profil verze rozhraní API Azure Stack. Chcete-li aktualizovat konfiguraci, spusťte následující příkaz:

    ```azurecli
      az cloud update --profile 2019-03-01-hybrid
   ```

    >[!NOTE]  
    >Pokud používáte verzi Azure Stack před sestavením 1808, musíte použít profil verze API **2017-03-09-Profile** , nikoli profil verze rozhraní API **2019-03-01 – Hybrid**. Budete muset použít nejnovější verzi rozhraní příkazového řádku Azure CLI.

5. Přihlaste se ke svému Azure Stack prostředí pomocí `az login` příkazu. K prostředí Azure Stack se můžete přihlásit buď jako uživatel, nebo jako [instanční objekt](/azure/active-directory/develop/app-objects-and-service-principals). 

6. Přihlásit se: 

   *  Jako **uživatel** používající webový prohlížeč s kódem zařízení:  

   ```azurecli  
    az login --use-device-code
   ```

   > [!NOTE]  
   >Spuštěním příkazu získáte adresu URL a kód, který je nutné použít k ověření.

   * Jako instanční objekt:
        
     Připravte soubor. pem, který se má použít pro přihlašovací objekty instančního objektu.

      * V klientském počítači, kde byl vytvořen objekt zabezpečení, exportujte certifikát instančního objektu jako PFX s privátním klíčem umístěným `cert:\CurrentUser\My`na adrese; název certifikátu má stejný název jako objekt zabezpečení.
  
      * Převeďte PFX na PEM (použijte nástroj OpenSSL).

     Přihlaste se k rozhraní příkazového řádku:

      ```azurecli  
      az login --service-principal \
        -u <Client ID from the Service Principal details> \
        -p <Certificate's fully qualified name, such as, C:\certs\spn.pem>
        --tenant <Tenant ID> \
        --debug 
      ```

### <a name="test-the-connectivity"></a>Otestovat připojení

Když máte všechno nastavené, pomocí rozhraní příkazového řádku můžete vytvářet prostředky v rámci Azure Stack. Můžete například vytvořit skupinu prostředků pro aplikaci a přidejte virtuální počítač. Pomocí následujícího příkazu vytvořte skupinu prostředků s názvem "MyResourceGroup":

```azurecli
  az group create -n MyResourceGroup -l local
```

Pokud je skupina prostředků úspěšně vytvořená, předchozí příkaz vytvoří výstup následujících vlastností nově vytvořeného prostředku:

![Skupina prostředků – vytvořit výstup](media/azure-stack-connect-cli/image1.png)

## <a name="known-issues"></a>Známé problémy

Při použití rozhraní příkazového řádku v Azure Stack se vyskytly známé problémy:

 - Interaktivní režim rozhraní příkazového řádku; například `az interactive` příkaz není v Azure Stack ještě podporován.
 - Pokud chcete získat seznam imagí virtuálních počítačů, které jsou k dispozici v `az vm image list --all` Azure Stack, použijte příkaz `az vm image list` místo příkazu. Zadáním `--all` možnosti zajistíte, že odpověď vrátí pouze obrázky, které jsou k dispozici v prostředí Azure Stack.
 - Aliasy imagí virtuálních počítačů, které jsou k dispozici v Azure, se nemusí pro Azure Stack použít. Při použití imagí virtuálních počítačů musíte použít celý parametr URN (kanonický: UbuntuServer: 14.04.3-LTS: 1.0.0) místo aliasu image. Tento název URN se musí shodovat s specifikacemi obrázku odvozenými z `az vm images list` příkazu.

## <a name="next-steps"></a>Další postup

- [Nasazení šablon pomocí Azure CLI](azure-stack-deploy-template-command-line.md)
- [Povolení Azure CLI pro uživatele Azure Stack (operátor)](../operator/azure-stack-cli-admin.md)
- [Správa uživatelských oprávnění](azure-stack-manage-permissions.md) 
