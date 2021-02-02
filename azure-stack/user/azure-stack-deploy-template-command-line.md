---
title: Nasazení šablony pomocí příkazového řádku v centru Azure Stack
description: Naučte se používat rozhraní příkazového řádku (CLI) Azure pro různé platformy k nasazení šablon do centra Azure Stack.
author: mattbriggs
ms.topic: article
ms.date: 2/1/2021
ms.author: mabrigg
ms.reviewer: unknown
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: 38327d2e23fd74c1a5e0a197ccc2a32b65b3fa7a
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99247808"
---
# <a name="deploy-a-template-with-the-command-line-in-azure-stack-hub"></a>Nasazení šablony pomocí příkazového řádku v centru Azure Stack

K nasazení Azure Resource Manager šablon v centru Azure Stack můžete použít rozhraní příkazového řádku Azure (CLI). Šablony Azure Resource Manager nasazují a nastavují prostředky pro vaši aplikaci v jediné koordinované akci.

## <a name="deploy-template"></a>Nasazení šablony

1. Projděte si [úložiště AzureStack-Start-Templates](https://aka.ms/AzureStackGitHub) a vyhledejte šablonu **101-Create-Storage-Account** . Uložte šablonu ( `azuredeploy.json` ) a soubory parametrů `(azuredeploy.parameters.json` do umístění na místním disku, například `C:\templates\`
2. Přejděte do složky, do které jste stáhli soubory. 
3. [Instalace a připojení](azure-stack-version-profiles-azurecli2.md) k centru Azure Stack pomocí Azure CLI.
4. Aktualizujte oblast a umístění v následujícím příkazu. Použijte `local` pro parametr Location, pokud používáte ASDK. Nasazení šablony:
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
