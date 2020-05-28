---
title: Použití Docker ke spuštění PowerShellu v centru Azure Stack
description: Použití Docker ke spuštění PowerShellu v centru Azure Stack
author: mattbriggs
ms.topic: article
ms.date: 5/27/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 07/09/2019
ms.openlocfilehash: 9d761114908919ef1b98436997c7225ba1544ae3
ms.sourcegitcommit: cad40ae88212cc72f40c84a1c88143ea0abb65ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84111825"
---
# <a name="use-docker-to-run-powershell-in-azure-stack-hub"></a>Použití Docker ke spuštění PowerShellu v centru Azure Stack

V tomto článku použijete Docker k vytvoření kontejnerů založených na systému Windows, ve kterém spustíte verzi PowerShellu, která je nutná pro práci s různými rozhraními. V Docker je nutné použít kontejnery založené na systému Windows.

## <a name="docker-prerequisites"></a>Požadavky Docker

1. Nainstalujte [Docker](https://docs.docker.com/install/).

1. V programu příkazového řádku, jako je například PowerShell nebo bash, zadejte:

    ```bash
        Docker --version
    ```

1. Je potřeba spustit Docker s využitím kontejnerů Windows, které vyžadují Windows 10. Když spustíte Docker, přepněte do kontejnerů Windows.

1. Spusťte Docker z počítače, který je připojený ke stejné doméně jako centrum Azure Stack. Pokud používáte Azure Stack Development Kit (ASDK), musíte nainstalovat [síť VPN na svém vzdáleném počítači](azure-stack-connect-azure-stack.md#connect-to-azure-stack-hub-with-vpn).

## <a name="set-up-a-service-principal-for-using-powershell"></a>Nastavení instančního objektu pro použití prostředí PowerShell

Pokud chcete k přístupu k prostředkům v centru Azure Stack použít PowerShell, potřebujete instanční objekt v tenantovi služby Azure Active Directory (Azure AD). Oprávnění můžete delegovat pomocí řízení přístupu na základě role uživatele (RBAC).

1. Pokud chcete nastavit instanční objekt, postupujte podle pokynů v [tématu poskytnutí přístupu aplikací k prostředkům Azure Stack centra vytvořením instančních objektů](azure-stack-create-service-principals.md).

2. Poznamenejte si ID aplikace, tajný klíč a ID tenanta pro pozdější použití.

## <a name="docker---azure-stack-hub-api-profiles-module"></a>Modul Docker – Profile rozhraní API centra Azure Stack

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
    Connect-AzureRmAccount -ServicePrincipal -Credential $pscredential -TenantId <TenantID>
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

## <a name="next-steps"></a>Další kroky

-  Přečtěte si přehled centra [Azure Stack PowerShellu v centru Azure Stack](azure-stack-powershell-overview.md).
- Přečtěte si o [profilech rozhraní API pro PowerShell](azure-stack-version-profiles.md) v centru Azure Stack.
- Nainstalujte [Powershell Azure Stack hub](../operator/azure-stack-powershell-install.md).
- Přečtěte si o vytváření [šablon Azure Resource Manager](azure-stack-develop-templates.md) pro cloudovou konzistenci.
