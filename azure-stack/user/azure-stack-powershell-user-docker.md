---
title: Použití Docker ke spuštění PowerShellu v centru Azure Stack
description: Použití Docker ke spuštění PowerShellu v centru Azure Stack
author: mattbriggs
ms.topic: how-to
ms.date: 8/17/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 8/17/2020
ms.openlocfilehash: c05f35a9ef5ad059bdf50d721acd2811fa908370
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90573730"
---
# <a name="use-docker-to-run-powershell-for-azure-stack-hub"></a>Použití Docker ke spuštění PowerShellu pro Azure Stack hub

V tomto článku můžete pomocí Docker vytvořit kontejner, na kterém běží verze PowerShellu, která je nutná pro práci s různými rozhraními. Pokyny k používání modulů AzureRM a nejnovějších modulů AZ modules najdete v tématu. AzureRM vyžaduje kontejner založený na systému Windows. AZ používá kontejner založený na systému Linux.

## <a name="docker-prerequisites"></a>Požadavky Docker

### <a name="install-docker"></a>Instalace Dockeru

1. Nainstalujte [Docker](https://docs.docker.com/install/).

1. V programu příkazového řádku, jako je například PowerShell nebo bash, zadejte:

    ```bash
    docker --version
    ```

### <a name="set-up-a-service-principal-for-using-powershell"></a>Nastavení instančního objektu pro použití prostředí PowerShell

Pokud chcete k přístupu k prostředkům v centru Azure Stack použít PowerShell, potřebujete instanční objekt v tenantovi služby Azure Active Directory (Azure AD). Oprávnění můžete delegovat pomocí řízení přístupu na základě role uživatele (RBAC). Možná budete muset požádat o instanční objekt od operátora cloudu.

1. Pokud chcete nastavit instanční objekt, postupujte podle pokynů v [tématu poskytnutí přístupu aplikací k prostředkům Azure Stack centra vytvořením instančních objektů](../operator/azure-stack-create-service-principals.md?view=azs-2002).

2. Poznamenejte si ID aplikace, tajný kód, ID tenanta a ID objektu pro pozdější použití.

## <a name="run-powershell-in-docker"></a>Spustit PowerShell v Docker

### <a name="azurerm-modules"></a>[Moduly AzureRM](#tab/rm)

V těchto pokynech spustíte image kontejneru se systémem Windows a nainstalujete PowerShell a požadované moduly pro Azure Stack hub.

1. Je potřeba spustit Docker s využitím kontejnerů Windows, které vyžadují Windows 10. Když spustíte Docker, přepněte do kontejnerů Windows. Image podporující AZ Module budou vyžadovat Docker 17,05 nebo novější.

1. Spusťte Docker z počítače, který je připojený ke stejné doméně jako centrum Azure Stack. Pokud používáte Azure Stack Development Kit (ASDK), musíte nainstalovat [síť VPN na svém vzdáleném počítači](azure-stack-connect-azure-stack.md#connect-to-azure-stack-hub-with-vpn).

### <a name="install-azure-stack-hub-azurerm-module-on-a-windows-container"></a>Azure Stack instalace modulu AzureRM hub na kontejner Windows

Souboru Dockerfile otevře Microsoft Image *Microsoft/windowsservercore*, ve které je nainstalovaný Windows PowerShell 5,1. Soubor potom načte NuGet a moduly PowerShellu centra Azure Stack a stáhne nástroje z nástroje Azure Stack hub.

1. [Stáhněte úložiště Azure-Stack-PowerShell](https://github.com/Azure-Samples/azure-stack-hub-powershell-in-docker.git) jako soubor ZIP nebo naklonujte úložiště.

2. Otevřete složku úložiště z terminálu.

3. V úložišti otevřete rozhraní příkazového řádku a potom zadejte následující příkaz:

    ```bash  
    docker build --tag azure-stack-powershell .
    ```

4. Po vytvoření image spusťte interaktivní kontejner zadáním:

    ```bash  
    docker run -it azure-stack-powershell powershell
    ```

    Poznamenejte si název kontejneru. Stejný kontejner můžete použít místo toho, aby se pokaždé vytvářel nový kontejner, a to spuštěním následujícího příkazu Docker:

    ```bash  
        docker exec -it "Container name" powershell
    ```

5. Prostředí je připravené na vaše rutiny.

    ```bash
    Windows PowerShell
    Copyright (C) 2016 Microsoft Corporation. All rights reserved.

    PS C:\>
    ```

6. Připojte se k instanci centra Azure Stack pomocí instančního objektu. Nyní používáte příkazový řádek PowerShellu v Docker. 

    ```powershell
    $passwd = ConvertTo-SecureString <Secret> -AsPlainText -Force
    $pscredential = New-Object System.Management.Automation.PSCredential('<ApplicationID>', $passwd)
    Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint <Your Azure Resource Manager endoint>
    Add-AzureRmAccount -EnvironmentName "AzureStackUser" -TenantId <TenantID> -ServicePrincipal -Credential $pscredential
    ```

   PowerShell vrátí váš objekt účtu:

    ```powershell  
    Account    SubscriptionName    TenantId    Environment
    -------    ----------------    --------    -----------
    <AccountID>    <SubName>       <TenantID>  AzureCloud
    ```

7. Otestujte připojení vytvořením skupiny prostředků v centru Azure Stack.

    ```powershell  
    New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
    ```

### <a name="az-modules"></a>[AZ modules](#tab/az)

V těchto pokynech spustíte image kontejneru se systémem Linux, která obsahuje PowerShell a požadované moduly pro Azure Stack hub.

1. Je potřeba spustit Docker pomocí kontejneru Linux. Když spustíte Docker, přepněte na kontejnery Linux.

1. Spusťte Docker z počítače, který je připojený ke stejné doméně jako centrum Azure Stack. Pokud používáte Azure Stack Development Kit (ASDK), musíte nainstalovat [síť VPN na svém vzdáleném počítači](azure-stack-connect-azure-stack.md#connect-to-azure-stack-hub-with-vpn).


## <a name="install-azure-stack-hub-az-module-on-a-linux-container"></a>Instalace centra Azure Stack AZ Module v kontejneru Linux

1. Z příkazového řádku spusťte následující příkaz Docker, který spustí PowerShell v kontejneru Ubuntu:

    ```bash
    docker run -it mcr.microsoft.com/azurestack/powershell
    ```

    Můžete spustit Ubuntu, Debian nebo CentOS. V úložišti GitHubu můžete najít následující soubory Docker, [azurestack-PowerShell](https://github.com/Azure/azurestack-powershell). Nejnovější změny souborů Docker najdete v úložišti GitHubu. Jednotlivé operační systémy jsou označeny. Nahraďte tag, oddíl za dvojtečkou, značkou pro požadovaný operační systém.

    | Linux | Image Dockeru |
    | --- | --- |
    | Ubuntu | `docker run -it mcr.microsoft.com/azurestack/powershell:ubuntu-18.04` |
    | Debian | `docker run -it mcr.microsoft.com/azurestack/powershell:debian-9` |
    | Centos | `docker run -it mcr.microsoft.com/azurestack/powershell:centos-7` |

2. Prostředí je připravené na vaše rutiny. Otestujte připojení k prostředí tím, že se přihlásíte a pak spustíte `Test-AzureStack.ps1` .

    Nejdřív vytvořte svoje přihlašovací údaje instančního objektu. Budete potřebovat **tajný** kód a **ID aplikace**. Také budete potřebovat **ID objektu** při spuštění `Test-AzureStack.ps1` služby ke kontrole kontejneru. Možná budete muset požádat o instanční objekt od operátora cloudu.

    Zadejte následující rutiny pro vytvoření objektu zásad služby:

    ```powershell  
    $passwd = ConvertTo-SecureString <Secret> -AsPlainText -Force
    $pscredential = New-Object System.Management.Automation.PSCredential('<ApplicationID>', $passwd)
    ```

5. Připojte se k prostředí spuštěním následujícího skriptu s následujícími hodnotami z vaší instance centra Azure Stack.

    | Hodnota | Popis |
    | --- | --- |
    | Název prostředí. | Název prostředí centra Azure Stack. |
    | Správce prostředků koncový bod | Adresa URL pro Správce prostředků. Pokud ho neznáte, obraťte se na svého operátora cloudu. Bude vypadat přibližně takto: `https://management.region.domain.com`. | 
    | ID tenanta adresáře | ID adresáře tenanta centra Azure Stack. | 
    | Přihlašovací údaj | Objekt, který obsahuje objekt služby. V tomto případě `$pscredential` .  |

    ```powershell
    ./Login-Environment.ps1 -Name <String> -ResourceManagerEndpoint <resource manager endpoint> -DirectoryTenantId <String> -Credential $pscredential
    ```

   PowerShell vrátí váš objekt účtu.

7. Otestujte prostředí spuštěním `Test-AzureStack.ps1` skriptu v kontejneru. Zadejte ID instančního **objektu**služby. Pokud neurčíte ID objektu, skript bude stále spuštěn, ale bude pouze Testovat moduly klienta (uživatel) a selže v modulech, které vyžadují oprávnění správce.

    ```powershell  
    ./Test-AzureStack.ps1 <Object ID>
    ```

---

## <a name="next-steps"></a>Další kroky

- Přečtěte si přehled centra [Azure Stack PowerShellu v centru Azure Stack](azure-stack-powershell-overview.md).
- Přečtěte si o [profilech rozhraní API pro PowerShell](azure-stack-version-profiles.md) v centru Azure Stack.
- Nainstalujte [PowerShell Azure Stack hub](../operator/azure-stack-powershell-install.md).
- Přečtěte si o vytváření [šablon Azure Resource Manager](azure-stack-develop-templates.md) pro cloudovou konzistenci.
