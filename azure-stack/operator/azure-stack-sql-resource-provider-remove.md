---
title: Odebrat poskytovatele prostředků SQL
titleSuffix: Azure Stack
description: Naučte se, jak odebrat poskytovatele prostředků SQL z nasazení Azure Stack.
services: azure-stack
documentationCenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 11/20/2018
ms.openlocfilehash: a172b56c43dafd637a66fa8354c2e06e06a67b98
ms.sourcegitcommit: 08d2938006b743b76fba42778db79202d7c3e1c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/09/2019
ms.locfileid: "74954447"
---
# <a name="remove-the-sql-resource-provider"></a>Odebrat poskytovatele prostředků SQL

Před odebráním poskytovatele prostředků SQL je nutné odebrat všechny závislosti zprostředkovatele. Budete také potřebovat kopii balíčku pro nasazení, který se použil k instalaci poskytovatele prostředků.

> [!NOTE]
> Odkazy ke stažení pro instalační programy poskytovatele prostředků najdete v části [nasazení požadavků poskytovatele prostředků](./azure-stack-sql-resource-provider-deploy.md#prerequisites).

Odebrání poskytovatele prostředků SQL neodstraní databáze tenantů z hostitelských serverů.

## <a name="dependency-cleanup"></a>Vyčištění závislosti

Před spuštěním skriptu DeploySqlProvider. ps1 pro odebrání poskytovatele prostředků je třeba provést několik úloh čištění.

Operátor Azure Stack zodpovídá za následující úlohy čištění:

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
    * **AzureEnvironment**: prostředí Azure používané pro nasazení Azure Stack. Vyžaduje se jenom pro nasazení Azure AD.
    * **CloudAdminCredential**: přihlašovací údaje pro správce cloudu, které jsou nezbytné pro přístup k privilegovanému koncovému bodu.
    * **AzCredential**: přihlašovací údaje pro účet správce služby Azure Stack. Použijte stejné přihlašovací údaje, které jste použili pro nasazení Azure Stack.

## <a name="next-steps"></a>Další kroky

[Nabídka App Services jako PaaS](azure-stack-app-service-overview.md)
