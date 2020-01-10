---
title: Použití Docker ke spuštění PowerShellu v centru Azure Stack | Microsoft Docs
description: Použití Docker ke spuštění PowerShellu v centru Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: article
ms.date: 10/10/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 07/09/2019
ms.openlocfilehash: e55fd18babea30d0b004c1219d8ce4842f4750bd
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75819467"
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

1. [Stáhněte úložiště Azure-Stack-PowerShell](https://github.com/mattbriggs/azure-stack-powershell) jako soubor ZIP nebo naklonujte úložiště.

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
