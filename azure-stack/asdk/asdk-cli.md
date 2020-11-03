---
title: Nastavují se certifikáty pro Azure CLI na Azure Stack Development Kit (ASDK)? | Dokumentace Microsoftu
description: Přečtěte si informace o nastavení certifikátů pro rozhraní příkazového řádku Azure v Azure Stack Development Kit Azure Stack Development Kit.
author: mattbriggs
ms.topic: how-to
ms.date: 10/26/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/26/2020
ms.openlocfilehash: 8b55fea8ed9692da34234e2633bdb43e92de46eb
ms.sourcegitcommit: b960df16e84ec9fbccfce772102b91f0b7ae7060
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2020
ms.locfileid: "93294403"
---
# <a name="setting-up-certificates-for-azure-cli-on-azure-stack-development-kit"></a>Nastavení certifikátů pro Azure CLI v Azure Stack Development Kit

Podle kroků v tomto článku nastavte rozhraní příkazového řádku Azure Command-Line pro správu prostředků Azure Stack Development Kit (ASDK) z klientských platforem Linux, Mac a Windows.

Tento článek popisuje získání certifikátů a jejich důvěřování na počítači pro vzdálenou správu. Informace o instalaci Azure CLI a připojení k prostředí najdete v tématu Instalace rozhraní příkazového [řádku Azure CLI do centra Azure Stack](/azure-stack/user/azure-stack-version-profiles-azurecli2).

## <a name="prepare-for-azure-cli"></a>Příprava pro Azure CLI

Pro ASDK potřebujete kořenový Azure Stack certifikát certifikační autority, aby bylo možné na svém vývojovém počítači používat Azure CLI. Certifikát použijete ke správě prostředků přes rozhraní příkazového řádku.

 - Pokud používáte rozhraní příkazového řádku z pracovní stanice mimo ASDK, vyžaduje se **kořenový certifikát CA centra Azure Stack** .  

 - **Aliasy aliasů virtuálních počítačů** poskytují alias, například "UbuntuLTS" nebo "Win2012Datacenter". Tento alias odkazuje na vydavatele image, nabídku, SKU a verzi jako jeden parametr při nasazování virtuálních počítačů.  

Následující části popisují, jak tyto hodnoty získat.

## <a name="export-the-azure-stack-hub-ca-root-certificate"></a>Export kořenového certifikátu certifikační autority centra Azure Stack

Pokud chcete použít rozhraní příkazového řádku Azure s ASDK, exportujte kořenový certifikát certifikační autority.

Export kořenového certifikátu ASDK ve formátu PEM:

1. Získání názvu kořenového certifikátu centra Azure Stack:
    1. Přihlaste se k portálu Azure Stack nebo uživateli centra pro správu.
    2. V blízkosti panelu Adresa vyberte možnost **zabezpečení** .
    3. V automaticky otevíraném okně vyberte **platné**.
    4. V okně certifikát vyberte kartu **cesta k certifikaci** .
    5. Poznamenejte si název kořenového certifikátu centra Azure Stack.

    ![Kořenový certifikát centra Azure Stack](../user/media/azure-stack-version-profiles-azurecli2/root-cert-name.png)

2. [Vytvořte virtuální počítač s Windows na rozbočovači Azure Stack](../user/azure-stack-quick-windows-portal.md).

3. Přihlaste se k virtuálnímu počítači, otevřete příkazový řádek prostředí PowerShell se zvýšenými oprávněními a spusťte následující skript:

    ```powershell  
      $label = "<the name of your Azure Stack Hub root cert from Step 1>"
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

## <a name="set-up-the-virtual-machine-alias-endpoint"></a>Nastavení koncového bodu aliasu virtuálního počítače

Můžete nastavit veřejně přístupný koncový bod, který hostuje soubor s aliasem virtuálního počítače. Soubor aliasu virtuálního počítače je soubor JSON, který poskytuje běžný název pro obrázek. Název použijete při nasazení virtuálního počítače jako parametru Azure CLI.

1. Pokud publikujete vlastní image, poznamenejte si informace o vydavateli, nabídce, SKU a verzi, které jste zadali během publikování. Pokud se jedná o image z webu Marketplace, můžete zobrazit informace pomocí ```Get-AzureVMImage``` rutiny.  

2. Stáhněte si [ukázkový soubor](https://raw.githubusercontent.com/Azure/azure-rest-api-specs/master/arm-compute/quickstart-templates/aliases.json) z GitHubu.

3. Vytvořte účet úložiště v centru Azure Stack. Až to bude hotové, vytvořte kontejner objektů BLOB. Nastavte zásady přístupu na veřejné.  

4. Nahrajte soubor JSON do nového kontejneru. Až to uděláte, můžete zobrazit adresu URL objektu BLOB. Vyberte název objektu BLOB a potom vyberte adresu URL z vlastností objektu BLOB.


## <a name="trust-the-certificate"></a>Důvěřovat certifikátu

Pokud chcete používat Azure CLI s aSDK, musíte důvěřovat kořenovému certifikátu certifikační autority na svém vzdáleném počítači.

### <a name="windows"></a>[Windows](#tab/win)

1. Najděte umístění certifikátu na svém počítači. Umístění se může lišit v závislosti na tom, kde jste nainstalovali Python. Otevřete příkazový řádek nebo příkazový řádek PowerShell se zvýšenými oprávněními a zadejte tento příkaz:

    ```powershell  
      python -c "import certifi; print(certifi.where())"
    ```

    Poznamenejte si umístění certifikátu. Například, `~/lib/python3.5/site-packages/certifi/cacert.pem`. Vaše konkrétní cesta závisí na vašem operačním systému a verzi Pythonu, kterou jste nainstalovali.

2. Důvěřování kořenovému certifikátu certifikační autority centra Azure Stack tak, že ho připojíte k existujícímu certifikátu Pythonu.

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

    Write-Host "Python Cert store was updated to allow the Azure Stack Hub CA root certificate"
    ```

Pokyny k instalaci a připojení pomocí Azure CLI najdete v tématu Instalace rozhraní příkazového [řádku Azure CLI do centra Azure Stack](/azure-stack/user/azure-stack-version-profiles-azurecli2).

### <a name="linux"></a>[Linux](#tab/lin)

Při nastavování CLI důvěřujete kořenovému certifikátu CA centra Azure Stack tím, že ho připojíte k existujícímu certifikátu Pythonu.

1. Najděte umístění certifikátu na svém počítači. Umístění se může lišit v závislosti na tom, kde jste nainstalovali Python. Musíte mít nainstalovaný PIP a modul certifi. Z příkazového řádku bash použijte následující příkaz Pythonu:

    ```bash  
    az --version
    ```

    Poznamenejte si umístění certifikátu. Například, `~/lib/python3.5/site-packages/certifi/cacert.pem`. Vaše konkrétní cesta závisí na vašem operačním systému a verzi Pythonu, kterou jste nainstalovali.

2. Spusťte následující příkaz bash s cestou k vašemu certifikátu.

   - Pro vzdálený počítač se systémem Linux:

     ```bash  
     sudo cat PATH_TO_PEM_FILE >> ~/<yourpath>/cacert.pem
     ```

   - Pro počítač se systémem Linux v prostředí Azure Stack hub:

     ```bash  
     sudo cat /var/lib/waagent/Certificates.pem >> ~/<yourpath>/cacert.pem
     ```

Pokyny k instalaci a připojení pomocí Azure CLI najdete v tématu Instalace rozhraní příkazového [řádku Azure CLI do centra Azure Stack](/azure-stack/user/azure-stack-version-profiles-azurecli2).

---

## <a name="next-steps"></a>Další kroky

[Instalace rozhraní příkazového řádku Azure CLI do centra Azure Stack](../user/azure-stack-version-profiles-azurecli2.md)