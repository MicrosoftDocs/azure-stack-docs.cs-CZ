---
title: Použití profilů verzí rozhraní API s Pythonem v Azure Stack | Microsoft Docs
description: Naučte se používat profily verzí rozhraní API s Pythonem v Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/16/2019
<!-- dev: viananth -->
ms.openlocfilehash: bf44716c160948f3deafdc8afb87b9b6d49f9eb5
ms.sourcegitcommit: 3d14ae30ce3ee44729e5419728cce14b3000e968
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71814438"
---
# <a name="use-api-version-profiles-with-python-in-azure-stack"></a>Použití profilů verzí rozhraní API s Pythonem v Azure Stack

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Python SDK podporuje profily verzí rozhraní API pro cílení na různé cloudové platformy, jako je Azure Stack a globální Azure. Profily rozhraní API můžete používat při vytváření řešení pro hybridní cloud.

Pokyny v tomto článku vyžadují předplatné Microsoft Azure. Pokud ho nemáte, můžete získat [bezplatný zkušební účet](https://go.microsoft.com/fwlink/?LinkId=330212).

## <a name="python-and-api-version-profiles"></a>Profily verze Pythonu a rozhraní API

Python SDK podporuje následující profily rozhraní API:

- **nejnovější**  
    Tento profil cílí na nejnovější verze rozhraní API pro všechny poskytovatele služeb na platformě Azure.
- **2019-03-01-hybrid**  
    Tento profil cílí na nejnovější verze rozhraní API pro všechny poskytovatele prostředků na platformě Azure Stack pro verze 1904 nebo novější.
- **2018-03-01-hybrid**  
    Tento profil cílí na nejvíce kompatibilní verze rozhraní API pro všechny poskytovatele prostředků na platformě Azure Stack.
- **2017-03-09-profile**  
    Tento profil cílí na nejvíce kompatibilní verze rozhraní API zprostředkovatelů prostředků, které podporuje Azure Stack.

   Další informace o profilech rozhraní API a Azure Stack najdete v tématu [Správa profilů verzí rozhraní API v Azure Stack](azure-stack-version-profiles.md).

## <a name="install-the-azure-python-sdk"></a>Instalace sady Azure Python SDK

1. Nainstalujte git z [oficiální lokality](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
2. Pokyny k instalaci sady Python SDK najdete v tématu [Azure pro vývojáře v Pythonu](/python/azure/python-sdk-azure-install?view=azure-python).
3. Pokud není k dispozici, vytvořte odběr a uložte ID předplatného, které chcete později použít. Pokyny k vytvoření předplatného najdete [v tématu Vytvoření předplatných nabídek v Azure Stack](../operator/azure-stack-subscribe-plan-provision-vm.md).
4. Vytvořte instanční objekt a uložte jeho ID a tajný klíč. Pokyny o tom, jak vytvořit instanční objekt pro Azure Stack najdete v tématu [poskytují aplikacím přístup ke službě Azure Stack](../operator/azure-stack-create-service-principals.md).
5. Ujistěte se, že má instančního objektu služby roli Přispěvatel nebo vlastník v rámci předplatného. Pokyny k přiřazení role k instančnímu objektu najdete v tématu [poskytnutí přístupu k aplikacím Azure Stack](../operator/azure-stack-create-service-principals.md).

## <a name="prerequisites"></a>Požadavky

Pokud chcete použít sadu Python Azure SDK s Azure Stack, musíte zadat následující hodnoty a potom nastavit hodnoty pomocí proměnných prostředí. Chcete-li nastavit proměnné prostředí, postupujte podle pokynů v následující tabulce pro konkrétní operační systém.

| Value | Proměnné prostředí | Popis |
|---------------------------|-----------------------|-------------------------------------------------------------------------------------------------------------------------|
| ID tenanta | `AZURE_TENANT_ID` | Vaše [ID tenanta](../operator/azure-stack-identity-overview.md)Azure Stack. |
| ID klienta | `AZURE_CLIENT_ID` | ID aplikace instančního objektu se uložilo při vytvoření instančního objektu v předchozí části tohoto článku. |
| ID předplatného | `AZURE_SUBSCRIPTION_ID` | [ID předplatného](../operator/azure-stack-plan-offer-quota-overview.md#subscriptions) se používá pro přístup k nabídkám v Azure Stack. |
| Tajný kód klienta | `AZURE_CLIENT_SECRET` | Tajný kód aplikace instančního objektu se uložil při vytvoření objektu služby. |
| Koncový bod Resource Manageru | `ARM_ENDPOINT` | Viz článek [Azure Stack správce prostředků koncového bodu](azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint) . |
| Umístění prostředku | `AZURE_RESOURCE_LOCATION` | Umístění prostředku vašeho Azure Stackho prostředí.

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Důvěřovat kořenovému certifikátu certifikační autority Azure Stack

Pokud používáte ASDK, musíte explicitně důvěřovat kořenovému certifikátu certifikační autority na vzdáleném počítači. Kořenový certifikát certifikační autority nemusíte důvěřovat s Azure Stack integrovanými systémy.

#### <a name="windows"></a>Windows

1. Najděte umístění úložiště certifikátů Pythonu na vašem počítači. Umístění se může lišit v závislosti na tom, kde jste nainstalovali Python. Otevřete příkazový řádek nebo příkazový řádek PowerShellu se zvýšenými oprávněními a zadejte tento příkaz:

    ```PowerShell  
      python -c "import certifi; print(certifi.where())"
    ```

    Poznamenejte si umístění úložiště certifikátů. například **~/lib/python3.5/site-Packages/certifi/CAcert.pem**. Vaše konkrétní cesta závisí na vašem operačním systému a verzi Pythonu, kterou jste nainstalovali.

2. Důvěřovat kořenovému certifikátu certifikační autority Azure Stack připojením k existujícímu certifikátu Pythonu:

    ```powershell
    $pemFile = "<Fully qualified path to the PEM certificate; for ex: C:\Users\user1\Downloads\root.pem>"

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
    Add-Content "${env:ProgramFiles(x86)}\Python35\Lib\site-packages\certifi\cacert.pem" $rootCertEntry

    Write-Host "Python Cert store was updated to allow the Azure Stack CA root certificate"
    ```

> [!NOTE]  
> Pokud používáte **virtualenv** pro vývoj pomocí sady Python SDK, jak je uvedeno v následující části [spuštění ukázky Pythonu](#run-the-python-sample) , musíte do úložiště certifikátů virtuálního prostředí přidat předchozí certifikát. Cesta může vypadat podobně jako: `..\mytestenv\Lib\site-packages\certifi\cacert.pem`.

## <a name="python-samples-for-azure-stack"></a>Ukázky Pythonu pro Azure Stack

Některé ukázky kódu, které jsou k dispozici pro Azure Stack pomocí sady Python SDK:

- [Správa prostředků a skupin prostředků](https://azure.microsoft.com/resources/samples/hybrid-resourcemanager-python-manage-resources/)
- [Spravovat účet úložiště](https://azure.microsoft.com/resources/samples/hybrid-storage-python-manage-storage-account/)
- [Správa virtuálních počítačů](https://azure.microsoft.com/resources/samples/hybrid-compute-python-manage-vm/): Tato ukázka používá **2019-03-01 – hybridní** profil, který cílí na nejnovější verze rozhraní API podporované nástrojem Azure Stack.

## <a name="manage-virtual-machine-sample"></a>Ukázka správy virtuálního počítače

K provádění běžných úloh správy virtuálních počítačů ve vašem Azure Stack použijte následující ukázku kódu Pythonu. Ukázka kódu ukazuje, jak:

- Vytváření virtuálních počítačů:
  - Vytvoření virtuálního počítače s Linuxem
  - Vytvoření virtuálního počítače s Windows
- Aktualizace virtuálního počítače:
  - Rozbalení jednotky
  - Označení virtuálního počítače
  - Připojit datové disky
  - Odpojení datových disků
- Provozování virtuálního počítače:
  - Spuštění virtuálního počítače
  - Zastavení virtuálního počítače
  - Restartování virtuálního počítače
- Vypsat virtuální počítače
- Odstranění virtuálního počítače

Pokud si chcete prohlédnout kód, který provádí tyto operace, přečtěte si funkci **run_example ()** ve skriptu Python **example.py** v úložišti GitHub [Hybrid-COMPUTE-Python-manage-VM](https://github.com/Azure-Samples/Hybrid-Compute-Python-Manage-VM).

Každá operace má jasně popisek s komentářem a funkcí tisku. Příklady nejsou nutně v pořadí uvedeném v tomto seznamu.

## <a name="run-the-python-sample"></a>Spuštění ukázky Pythonu

1. [Nainstalujte Python](https://www.python.org/downloads/) , pokud ještě není nainstalovaný. Tato ukázka (a sada SDK) je kompatibilní s Pythonem 2,7, 3,4, 3,5 a 3,6.

2. Obecné doporučení pro vývoj v jazyce Python je použití virtuálního prostředí. Další informace najdete v [dokumentaci k Pythonu](https://docs.python.org/3/tutorial/venv.html).

3. Instalace a inicializace virtuálního prostředí s modulem **venv** v Pythonu 3 (je nutné nainstalovat [virtualenv](https://pypi.python.org/pypi/virtualenv) pro Python 2,7):

    ```bash
    python -m venv mytestenv # Might be "python3" or "py -3.6" depending on your Python installation
    cd mytestenv
    source bin/activate      # Linux shell (Bash, ZSH, etc.) only
    ./scripts/activate       # PowerShell only
    ./scripts/activate.bat   # Windows CMD only
    ```

4. Naklonujte úložiště:

    ```bash
    git clone https://github.com/Azure-Samples/Hybrid-Compute-Python-Manage-VM.git
    ```

5. Nainstalujte závislosti pomocí **PIP**:

    ```bash
    cd Hybrid-Compute-Python-Manage-VM
    pip install -r requirements.txt
    ```

6. Vytvořte [instanční objekt](../operator/azure-stack-create-service-principals.md) pro práci s Azure Stack. Ujistěte se, že váš instanční objekt má v předplatném [roli Přispěvatel/vlastník](../operator/azure-stack-create-service-principals.md#assign-a-role) .

7. Nastavte následující proměnné a exportujte tyto proměnné prostředí do aktuálního prostředí:

    ```bash
    export AZURE_TENANT_ID={your tenant id}
    export AZURE_CLIENT_ID={your client id}
    export AZURE_CLIENT_SECRET={your client secret}
    export AZURE_SUBSCRIPTION_ID={your subscription id}
    export ARM_ENDPOINT={your AzureStack Resource Manager Endpoint}
    export AZURE_RESOURCE_LOCATION={your AzureStack Resource location}
    ```

8. Pokud chcete tuto ukázku spustit, Ubuntu 16,04-LTS a WindowsServer 2012-R2 image DataCenter musí být k dispozici na webu Azure Stack Marketplace. Tyto Image je možné [stáhnout z Azure](../operator/azure-stack-download-azure-marketplace-item.md)nebo přidat do [úložiště imagí platformy](../operator/azure-stack-add-vm-image.md).

9. Spusťte ukázku:

    ```python
    python example.py
    ```

## <a name="next-steps"></a>Další kroky

- [Centrum pro vývoj v Pythonu pro Azure](https://azure.microsoft.com/develop/python/)
- [Dokumentace k Azure Virtual Machines](https://azure.microsoft.com/services/virtual-machines/)
- [Studijní cesta pro Virtual Machines](/learn/paths/deploy-a-website-with-azure-virtual-machines/)
