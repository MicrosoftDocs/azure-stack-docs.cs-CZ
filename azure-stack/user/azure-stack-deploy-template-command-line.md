---
title: Nasazení šablon pomocí příkazového řádku ve službě Azure Stack | Dokumentace Microsoftu
description: Další informace o použití multiplatformního rozhraní příkazového řádku (CLI) k nasazení šablony do služby Azure Stack.
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
ms.date: 05/09/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: 7db00c7ec295f64255dcd378030a68c68f81219d
ms.sourcegitcommit: c755c7eac0f871960f9290591421cf5990b9e734
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/09/2019
ms.locfileid: "65506138"
---
# <a name="deploy-templates-in-azure-stack-using-the-command-line"></a>Nasazení šablon ve službě Azure Stack pomocí příkazového řádku

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Nasazení šablon Azure Resource Manageru ve službě Azure Stack pomocí rozhraní příkazového řádku Azure (CLI). Šablony Azure Resource Manageru, nasazení a zřizování prostředků pro vaši aplikaci v rámci jediné koordinované operace.

## <a name="before-you-begin"></a>Než začnete

- [Nainstalujte a připojte](azure-stack-version-profiles-azurecli2.md) do služby Azure Stack pomocí Azure CLI.
- Stažení souborů *azuredeploy.json* a *azuredeploy.parameters.json* z [vytvořit příklad šablony úložiště účtu](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account).

## <a name="deploy-template"></a>Nasazení šablony

Přejděte do složky, do které tyto soubory byly staženy a spusťte následující příkaz k nasazení šablony:

```azurecli
az group create "cliRG" "local" -f azuredeploy.json -d "testDeploy" -e azuredeploy.parameters.json
```

Tento příkaz nasadí šablony do skupiny prostředků **cliRG** ve výchozím umístění Azure Stack POC.

## <a name="validate-template-deployment"></a>Ověření šablony nasazení

Pokud chcete zobrazit tento prostředek skupiny a účet úložiště, použijte následující příkazy rozhraní příkazového řádku:

```azurecli
az group list

az storage account list
```

## <a name="next-steps"></a>Další postup

Další informace o [nasazení šablon pomocí prostředí PowerShell](azure-stack-deploy-template-powershell.md).
