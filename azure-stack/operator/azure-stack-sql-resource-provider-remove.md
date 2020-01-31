---
title: Odebrat poskytovatele prostředků SQL
titleSuffix: Azure Stack Hub
description: Naučte se, jak odebrat poskytovatele prostředků SQL z nasazení centra Azure Stack.
author: mattbriggs
ms.topic: article
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 11/20/2018
ms.openlocfilehash: 5d8335e69c35c209e444d88f41ce76a3fae94bb0
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76882792"
---
# <a name="remove-the-sql-resource-provider"></a>Odebrat poskytovatele prostředků SQL

Před odebráním poskytovatele prostředků SQL je nutné odebrat všechny závislosti zprostředkovatele. Budete také potřebovat kopii balíčku pro nasazení, který se použil k instalaci poskytovatele prostředků.

> [!NOTE]
> Odkazy ke stažení pro instalační programy poskytovatele prostředků najdete v části [nasazení požadavků poskytovatele prostředků](./azure-stack-sql-resource-provider-deploy.md#prerequisites).

Odebrání poskytovatele prostředků SQL neodstraní databáze tenantů z hostitelských serverů.

## <a name="dependency-cleanup"></a>Vyčištění závislosti

Před spuštěním skriptu DeploySqlProvider. ps1 pro odebrání poskytovatele prostředků je třeba provést několik úloh čištění.

Operátor centra Azure Stack zodpovídá za následující úlohy čištění:

* Odstraňte všechny plány, které odkazují na adaptér SQL.
* Odstraňte všechny kvóty, které jsou spojeny s adaptérem SQL.

## <a name="to-remove-the-sql-resource-provider"></a>Odebrání poskytovatele prostředků SQL

1. Ověřte, že jste odebrali všechny existující závislosti poskytovatele prostředků SQL.

   > [!NOTE]
   > Odinstalace poskytovatele prostředků SQL bude pokračovat i v případě, že závislé prostředky aktuálně používají poskytovatele prostředků.
  
2. Získejte kopii instalačního balíčku poskytovatele prostředků SQL a potom spusťte samočinný extrahování, který extrahuje obsah do dočasného adresáře.

3. Otevřete nové okno konzoly PowerShellu se zvýšenými oprávněními a přejděte do adresáře, do kterého jste extrahovali instalační soubory poskytovatele prostředků SQL.

4. Spusťte skript DeploySqlProvider. ps1 pomocí následujících parametrů:

    * **Odinstalace**: Odebere poskytovatele prostředků a všechny přidružené prostředky.
    * **PrivilegedEndpoint**: IP adresa nebo název DNS privilegovaného koncového bodu.
    * **AzureEnvironment**: prostředí Azure používané pro nasazení centra Azure Stack. Vyžaduje se jenom pro nasazení Azure AD.
    * **CloudAdminCredential**: přihlašovací údaje pro správce cloudu, které jsou nezbytné pro přístup k privilegovanému koncovému bodu.
    * **AzCredential**: přihlašovací údaje pro účet správce služby Azure Stack hub. Použijte stejné přihlašovací údaje, které jste použili k nasazení centra Azure Stack.

## <a name="next-steps"></a>Další kroky

[Nabídka App Services jako PaaS](azure-stack-app-service-overview.md)
