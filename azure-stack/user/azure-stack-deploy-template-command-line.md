---
title: Nasazení šablon pomocí příkazového řádku ve službě Azure Stack | Dokumentace Microsoftu
description: Další informace o použití rozhraní příkazového řádku (CLI) napříč platformami pro nasazení šablony do služby Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 9584177f-4af3-4834-864d-930b09ae0995
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: fcf84ee394c917e896bc50d5d6a97f42191451e9
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985605"
---
# <a name="deploy-templates-in-azure-stack-using-the-command-line"></a>Nasazení šablon ve službě Azure Stack pomocí příkazového řádku

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Použijte příkazový řádek pro nasazení šablony Azure Resource Manageru v prostředí Azure Stack Development Kit. Šablony Azure Resource Manageru nasadit a zřiďte všechny prostředky pro vaši aplikaci v rámci jediné koordinované operace.

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

- Další informace o nasazení šablony najdete v tématu:

[Nasazení šablon pomocí PowerShellu](azure-stack-deploy-template-powershell.md)
