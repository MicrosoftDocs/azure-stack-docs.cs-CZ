---
title: Odeberte poskytovatele prostředků MySQL v Azure Stack | Microsoft Docs
description: Naučte se odebrat poskytovatele prostředků MySQL z nasazení Azure Stack.
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
ms.openlocfilehash: a2b55707bc05aecf8681cb866c58b0ed34fb87cd
ms.sourcegitcommit: a23b80b57668615c341c370b70d0a106a37a02da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "72682162"
---
# <a name="remove-the-mysql-resource-provider-in-azure-stack"></a>Odeberte poskytovatele prostředků MySQL v Azure Stack

Než odeberete poskytovatele prostředků MySQL, je nutné odebrat všechny závislosti zprostředkovatele. Budete také potřebovat kopii balíčku pro nasazení, který se použil k instalaci poskytovatele prostředků.

> [!NOTE]
> Odkazy ke stažení pro instalační programy poskytovatele prostředků najdete v části [nasazení požadavků poskytovatele prostředků](./azure-stack-mysql-resource-provider-deploy.md#prerequisites).

Odebrání poskytovatele prostředků MySQL neodstraní databáze tenantů z hostitelských serverů.

## <a name="dependency-cleanup"></a>Vyčištění závislosti

Před spuštěním skriptu DeployMySqlProvider. ps1 pro odebrání poskytovatele prostředků je třeba provést několik úloh čištění.

Operátor Azure Stack zodpovídá za následující úlohy čištění:

* Odstraňte všechny plány, které odkazují na adaptér MySQL.
* Odstraňte všechny kvóty, které jsou přidruženy k adaptéru MySQL.

## <a name="to-remove-the-mysql-resource-provider"></a>Odebrání poskytovatele prostředků MySQL

1. Ověřte, že jste odebrali všechny existující závislosti poskytovatele prostředků MySQL.

   > [!NOTE]
   > Odinstalace poskytovatele prostředků MySQL bude pokračovat i v případě, že závislé prostředky aktuálně používají poskytovatele prostředků.
  
2. Získejte kopii instalačního balíčku poskytovatele prostředků MySQL a potom spusťte samočinný extrahování, který extrahuje obsah do dočasného adresáře.
3. Otevřete nové okno konzoly PowerShellu se zvýšenými oprávněními a přejděte do adresáře, do kterého jste extrahovali instalační soubory poskytovatele prostředků MySQL.
4. Spusťte skript DeployMySqlProvider. ps1 pomocí následujících parametrů:
    - **Odinstalace**: Odebere poskytovatele prostředků a všechny přidružené prostředky.
    - **PrivilegedEndpoint**: IP adresa nebo název DNS privilegovaného koncového bodu.
    - **AzureEnvironment**: prostředí Azure používané pro nasazení Azure Stack. Vyžaduje se jenom pro nasazení Azure AD.
    - **CloudAdminCredential**: pověření pro správce cloudu, které je nutné pro přístup k privilegovanému koncovému bodu.
    - **DirectoryTenantID**
    - **AzCredential**: přihlašovací údaje pro účet správce služby Azure Stack. Použijte stejné přihlašovací údaje, které jste použili pro nasazení Azure Stack.

## <a name="next-steps"></a>Další kroky

[Nabídka App Services jako PaaS](azure-stack-app-service-overview.md)
