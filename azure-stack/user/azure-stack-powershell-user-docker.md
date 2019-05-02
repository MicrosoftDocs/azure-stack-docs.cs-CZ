---
title: Spusťte prostředí PowerShell pro Azure Stack pomocí Dockeru | Dokumentace Microsoftu
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
ms.date: 04/25/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 0eacd2c5a058bca68e86f2d34df8d3cc91987c1c
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985546"
---
# <a name="use-docker-to-run-powershell"></a>Spusťte prostředí PowerShell pomocí Dockeru

Docker můžete použít k vytvoření založené na Windows kontejnery, ve kterých chcete spustit konkrétní verzi Powershellu potřebné pro práci různá rozhraní. Kontejnery Windows podle musíte použít v Dockeru.

## <a name="docker-prerequisites"></a>Požadavky na dockeru

1. Nainstalujte [Docker](https://docs.docker.com/install/).
2. Otevřete příkazový řádek, jako je Powershell nebo prostředí Bash a zadejte:

    ```bash
        Docker -version
    ```

3. Budete muset spustit Docker pomocí kontejnerů Windows, které vyžadují Windows 10. Při spuštění Docker, přepněte na kontejnery Windows.

4. Docker je nutné spustit z počítače připojené ke stejné doméně jako Azure Stack. Pokud používáte ASDK, je potřeba nainstalovat [VPN na vzdáleném počítači](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn).

## <a name="service-principals-for-using-powershell"></a>Instanční objekty pro pomocí Powershellu

Potřebujete instanční objekt služby ve vašem tenantovi Azure AD použít PowerShell k přístupu k prostředku ve službě Azure Stack. Můžete delegovat oprávnění prostřednictvím řízení přístupu na základě rolí uživatele.

1. Nastavení vaší hlavní podle pokynů v článku [poskytnout aplikace přístup k prostředkům Azure Stack tak, že vytvoříte instanční objekty](azure-stack-create-service-principals.md).
2. Poznamenejte si ID aplikace, tajný klíč a ID vašeho tenanta.

## <a name="docker---azure-stack-api-profiles-module"></a>Docker – modul profily rozhraní API služby Azure Stack

Soubor Dockerfile se otevře microsoft image Microsoft/windowsservercore, který má Windows PowerShell 5.1 nainstalované. Soubor pak načte NuGet, moduly Azure Stack Powershellu a soubory ke stažení nástrojů z nástroje Azure Stack.

1. Toto úložiště jako ZIP stáhněte nebo naklonujte úložiště:  
[https://github.com/mattbriggs/azure-stack-powershell](https://github.com/mattbriggs/azure-stack-powershell)

2. Otevření složky úložiště z terminálu.

3. Otevřete rozhraní příkazového řádku do vašeho úložiště a zadejte:

    ```bash  
    docker build --tag azure-stack-powershell .
    ```

4. Když na obrázku je sestavený Build, může začít interaktivní kontejneru. Zadejte:

    ```bash  
        docker run -it azure-stack-powershell powershell
    ```

5. Prostředí je připravená pro vaše rutiny.

    ```bash
    Windows PowerShell
    Copyright (C) 2016 Microsoft Corporation. All rights reserved.

    PS C:\>
    ```

6. Připojení do služby Azure Stack pomocí instančního objektu. Teď používáte příkazový řádek Powershellu v Dockeru. 

    ```Powershell
    $passwd = ConvertTo-SecureString <Secret> -AsPlainText -Force
    $pscredential = New-Object System.Management.Automation.PSCredential('<ApplicationID>', $passwd)
    Connect-AzureRmAccount -ServicePrincipal -Credential $pscredential -TenantId <TenantID>
    ```

   PowerShell vrátí objekt vašeho účtu:

    ```PowerShell  
    Account    SubscriptionName    TenantId    Environment
    -------    ----------------    --------    -----------
    <AccountID>    <SubName>       <TenantID>  AzureCloud
    ```

7. Test připojení tak, že vytvoříte skupinu prostředků ve službě Azure Stack.

    ```PowerShell  
    New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
    ```

## <a name="next-steps"></a>Další postup

-  Přečtěte si základní informace o [Azure Stack Powershellu ve službě Azure Stack](azure-stack-powershell-overview.md).
- Přečtěte si informace o [profily rozhraní API prostředí PowerShell pro](azure-stack-version-profiles.md) v Azure stacku.
- [Instalace Azure Stack Powershellu](../operator/azure-stack-powershell-install.md).
- Přečtěte si informace o vytváření [šablon Azure Resource Manageru](azure-stack-develop-templates.md) konzistence cloudu.