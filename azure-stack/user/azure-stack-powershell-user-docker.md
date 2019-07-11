---
title: Používat Docker ke spouštění prostředí PowerShell ve službě Azure Stack | Dokumentace Microsoftu
description: Spusťte prostředí PowerShell ve službě Azure Stack pomocí Dockeru
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
ms.date: 07/09/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 07/09/2019
ms.openlocfilehash: 27f2b4c1817c28cf5d345f5aa9387a26cd18316b
ms.sourcegitcommit: d2df594e8346a875967e3cfb04c23562a1bd2e3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2019
ms.locfileid: "67725745"
---
# <a name="use-docker-to-run-powershell-in-azure-stack"></a>Spusťte prostředí PowerShell ve službě Azure Stack pomocí Dockeru

V tomto článku použijete k vytvoření kontejnery založené na Windows, ve kterém se spustí na verzi prostředí PowerShell, který je potřeba pro práci s různá rozhraní Docker. V Dockeru musíte použít kontejnery založené na Windows.

## <a name="docker-prerequisites"></a>Požadavky na dockeru

1. Nainstalujte [Docker](https://docs.docker.com/install/).

1. Do příkazového řádku programu, jako je Powershell nebo prostředí Bash zadejte:

    ```bash
        Docker --version
    ```

1. Budete muset spustit Docker s využitím kontejnerů Windows, které vyžadují Windows 10. Pokud spustíte Docker, přepněte na kontejnery Windows.

1. Spusťte Docker z počítače, který je připojený ke stejné doméně jako Azure Stack. Pokud používáte Azure Stack Development Kit (ASDK), je potřeba nainstalovat [VPN na vzdáleném počítači](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn).

## <a name="set-up-a-service-principal-for-using-powershell"></a>Nastavení hlavního názvu služby pro použití prostředí PowerShell

Jak pomocí prostředí PowerShell pro přístup k prostředkům ve službě Azure Stack, potřebujete instanční objekt služby ve vašem tenantovi Azure Active Directory (Azure AD). Můžete delegovat oprávnění uživatele řízením přístupu na základě role (RBAC).

1. Nastavení instančního objektu služby, postupujte podle pokynů v [poskytnout aplikace přístup k prostředkům Azure Stack tak, že vytvoříte instanční objekty](azure-stack-create-service-principals.md).

2. Poznamenejte si ID aplikace, tajný klíč a ID tenanta pro pozdější použití.

## <a name="docker---azure-stack-api-profiles-module"></a>Docker – rozhraní API služby Azure Stack profiles modulu

Soubor Dockerfile otevře Microsoft image *microsoft/windowsservercore*, který má Windows PowerShell 5.1 nainstalované. Soubor pak načte moduly Azure Stack Powershellu a NuGet a soubory ke stažení nástrojů z nástroje Azure Stack.

1. [Stáhněte si úložiště azure. stack powershellu](https://github.com/mattbriggs/azure-stack-powershell) jako soubor ZIP nebo klonování úložiště.

2. Otevření složky úložiště z terminálu.

3. Otevřete rozhraní příkazového řádku ve vašem úložišti a potom zadejte následující příkaz:

    ```bash  
    docker build --tag azure-stack-powershell .
    ```

4. Když je vytvořena na obrázku, spuštění interaktivní kontejneru tak, že zadáte:

    ```bash  
        docker run -it azure-stack-powershell powershell
    ```

5. Prostředí je připravená pro vaše rutiny.

    ```bash
    Windows PowerShell
    Copyright (C) 2016 Microsoft Corporation. All rights reserved.

    PS C:\>
    ```

6. Připojení k vaší instanci služby Azure Stack pomocí hlavního názvu služby. Teď používáte příkazový řádek Powershellu v Dockeru. 

    ```powershell
    $passwd = ConvertTo-SecureString <Secret> -AsPlainText -Force
    $pscredential = New-Object System.Management.Automation.PSCredential('<ApplicationID>', $passwd)
    Connect-AzureRmAccount -ServicePrincipal -Credential $pscredential -TenantId <TenantID>
    ```

   PowerShell vrátí objekt vašeho účtu:

    ```powershell  
    Account    SubscriptionName    TenantId    Environment
    -------    ----------------    --------    -----------
    <AccountID>    <SubName>       <TenantID>  AzureCloud
    ```

7. Test připojení tak, že vytvoříte skupinu prostředků ve službě Azure Stack.

    ```powershell  
    New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
    ```

## <a name="next-steps"></a>Další postup

-  Přečtěte si základní informace o [Azure Stack Powershellu ve službě Azure Stack](azure-stack-powershell-overview.md).
- Přečtěte si informace o [profily rozhraní API prostředí PowerShell pro](azure-stack-version-profiles.md) ve službě Azure Stack.
- Nainstalujte [Azure Stack Powershellu](../operator/azure-stack-powershell-install.md).
- Přečtěte si informace o vytváření [šablon Azure Resource Manageru](azure-stack-develop-templates.md) konzistence cloudu.
