---
title: Nasazení šablony pomocí příkazového řádku v Azure Stack | Microsoft Docs
description: Naučte se používat rozhraní příkazového řádku (CLI) Azure pro různé platformy k nasazení šablon do Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: article
ms.date: 10/07/2019
ms.author: mabrigg
ms.reviewer: unknown
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: 7b3daaefd8fa7e7bce9c6d5708e664911fc906fe
ms.sourcegitcommit: 7226979ece29d9619c959b11352be601562b41d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2019
ms.locfileid: "72304086"
---
# <a name="deploy-a-template-with-the-command-line-in-azure-stack"></a>Nasazení šablony pomocí příkazového řádku v Azure Stack

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

K nasazení Azure Resource Manager šablon v Azure Stack můžete použít rozhraní příkazového řádku Azure (CLI). Šablony Azure Resource Manager nasazují a nastavují prostředky pro vaši aplikaci v jediné koordinované akci.

## <a name="deploy-template"></a>Nasazení šablony

1. Projděte si [úložiště AzureStack-Start-Templates](https://aka.ms/AzureStackGitHub) a vyhledejte šablonu **101-Create-Storage-Account** . Uložte šablonu (`azuredeploy.json`) a soubory parametrů `(azuredeploy.parameters.json`) do umístění na místním disku, například `C:\templates\`.
2. Přejděte do složky, do které jste stáhli soubory. 
3. [Instalace a připojení](azure-stack-version-profiles-azurecli2.md) k Azure Stack pomocí Azure CLI.
4. Aktualizujte oblast a umístění v následujícím příkazu. Pokud používáte ASDK, použijte pro parametr Location hodnotu `local`. Nasazení šablony:
    ```azurecli
    az group create --name testDeploy --location local
    az group deployment create --resource-group testDeploy --template-file ./azuredeploy.json --parameters ./azuredeploy.parameters.json
    ```

Tento příkaz nasadí šablonu do skupiny prostředků **testDeploy** ve vaší instanci Azure Stack.

## <a name="validate-template-deployment"></a>Ověřit nasazení šablony

Chcete-li zkontrolovat skupinu prostředků a účet úložiště, spusťte následující příkazy rozhraní příkazového řádku:

```azurecli
az group list
az storage account list
```

## <a name="next-steps"></a>Další kroky

Naučte se [nasazovat šablony pomocí PowerShellu](azure-stack-deploy-template-powershell.md).
