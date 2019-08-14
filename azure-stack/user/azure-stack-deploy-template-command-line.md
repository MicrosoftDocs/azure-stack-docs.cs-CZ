---
title: Nasazení šablony pomocí příkazového řádku v Azure Stack | Microsoft Docs
description: Naučte se používat rozhraní příkazového řádku (CLI) Azure pro různé platformy k nasazení šablon do Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: article
ms.date: 08/13/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: da17e80c802e210d53effbad8f264b1a4019e6e0
ms.sourcegitcommit: aefcf9c61bd8089a0aaa569af7643e5e15f4947c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68991845"
---
# <a name="deploy-a-template-with-the-command-line-in-azure-stack"></a>Nasazení šablony pomocí příkazového řádku v Azure Stack

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

K nasazení Azure Resource Manager šablon v Azure Stack můžete použít rozhraní příkazového řádku Azure (CLI). Šablony Azure Resource Manager nasazují a zřídí prostředky pro vaši aplikaci v rámci jediné koordinované operace.

## <a name="before-you-begin"></a>Před zahájením

- [Instalace a připojení](azure-stack-version-profiles-azurecli2.md) k Azure Stack pomocí Azure CLI.
- Stáhněte soubory *azuredeploy. JSON* a *azuredeploy. Parameters. JSON* z [ukázkové šablony pro vytvoření účtu úložiště](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account).

## <a name="deploy-template"></a>Nasazení šablony

Přejděte do složky, do které byly tyto soubory staženy, a spusťte následující příkaz pro nasazení šablony:

```azurecli
az group create "cliRG" "local" -f azuredeploy.json -d "testDeploy" -e azuredeploy.parameters.json
```

Tento příkaz nasadí šablonu do skupiny prostředků **cliRG** ve výchozím umístění Azure Stack pro ověření koncepce.

## <a name="validate-template-deployment"></a>Ověřit nasazení šablony

Pokud chcete zobrazit tuto skupinu prostředků a účet úložiště, použijte následující příkazy rozhraní příkazového řádku:

```azurecli
az group list

az storage account list
```

## <a name="next-steps"></a>Další postup

Naučte se [nasazovat šablony pomocí PowerShellu](azure-stack-deploy-template-powershell.md).
