---
title: Nasazení šablony pomocí příkazového řádku v Azure Stack hub | Microsoft Docs
description: Naučte se používat rozhraní příkazového řádku (CLI) Azure pro různé platformy k nasazení šablon do centra Azure Stack.
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
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: unknown
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: 25479837bd73816abae92badfcd03bb21317f84c
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76536347"
---
# <a name="deploy-a-template-with-the-command-line-in-azure-stack-hub"></a>Nasazení šablony pomocí příkazového řádku v centru Azure Stack

K nasazení Azure Resource Manager šablon v centru Azure Stack můžete použít rozhraní příkazového řádku Azure (CLI). Šablony Azure Resource Manager nasazují a nastavují prostředky pro vaši aplikaci v jediné koordinované akci.

## <a name="deploy-template"></a>Nasazení šablony

1. Projděte si [úložiště AzureStack-Start-Templates](https://aka.ms/AzureStackGitHub) a vyhledejte šablonu **101-Create-Storage-Account** . Uložte šablonu (`azuredeploy.json`) a soubory parametrů `(azuredeploy.parameters.json`) do umístění na místním disku, například `C:\templates\`
2. Přejděte do složky, do které jste stáhli soubory. 
3. [Instalace a připojení](azure-stack-version-profiles-azurecli2.md) k centru Azure Stack pomocí Azure CLI.
4. Aktualizujte oblast a umístění v následujícím příkazu. Pokud používáte ASDK, použijte `local` pro parametr Location. Nasazení šablony:
    ```azurecli
    az group create --name testDeploy --location local
    az group deployment create --resource-group testDeploy --template-file ./azuredeploy.json --parameters ./azuredeploy.parameters.json
    ```

Tento příkaz nasadí šablonu do skupiny prostředků **testDeploy** ve vaší instanci centra Azure Stack.

## <a name="validate-template-deployment"></a>Ověřit nasazení šablony

Chcete-li zkontrolovat skupinu prostředků a účet úložiště, spusťte následující příkazy rozhraní příkazového řádku:

```azurecli
az group list
az storage account list
```

## <a name="next-steps"></a>Další kroky

Naučte se [nasazovat šablony pomocí PowerShellu](azure-stack-deploy-template-powershell.md).
