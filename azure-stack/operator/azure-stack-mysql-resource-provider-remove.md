---
title: Odebrání poskytovatele prostředků MySQL v centru Azure Stack
description: Naučte se odebrat poskytovatele prostředků MySQL z nasazení centra Azure Stack.
author: bryanla
ms.topic: article
ms.date: 1/22/2020
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 11/20/2
ms.openlocfilehash: 60f8ab926bb868517c7b9feeb1c5c4325212607c
ms.sourcegitcommit: 1969a13d378cc3b8c58fea11dca8cf8c06136acd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "80302823"
---
# <a name="remove-the-mysql-resource-provider-in-azure-stack-hub"></a>Odebrání poskytovatele prostředků MySQL v centru Azure Stack

Než odeberete poskytovatele prostředků MySQL, je nutné odebrat všechny závislosti zprostředkovatele. Budete také potřebovat kopii balíčku pro nasazení, který se použil k instalaci poskytovatele prostředků.

> [!NOTE]
> Odkazy ke stažení pro instalační programy poskytovatele prostředků najdete v části [nasazení požadavků poskytovatele prostředků](./azure-stack-mysql-resource-provider-deploy.md#prerequisites).

Při odebrání poskytovatele prostředků MySQL se odstraní přidružené plány a kvóty spravované operátorem. Neodstraňují ale databáze tenantů z hostitelských serverů.

## <a name="to-remove-the-mysql-resource-provider"></a>Odebrání poskytovatele prostředků MySQL

1. Ověřte, že jste odebrali všechny existující závislosti poskytovatele prostředků MySQL.

   > [!NOTE]
   > Odinstalace poskytovatele prostředků MySQL bude pokračovat i v případě, že závislé prostředky aktuálně používají poskytovatele prostředků.
  
2. Získejte kopii instalačního balíčku poskytovatele prostředků MySQL a potom spusťte samočinný extrahování, který extrahuje obsah do dočasného adresáře.
3. Otevřete nové okno konzoly PowerShellu se zvýšenými oprávněními a přejděte do adresáře, do kterého jste extrahovali instalační soubory poskytovatele prostředků MySQL.
4. Spusťte skript DeployMySqlProvider. ps1 pomocí následujících parametrů:
    - **Odinstalace**: Odebere poskytovatele prostředků a všechny přidružené prostředky.
    - **PrivilegedEndpoint**: IP adresa nebo název DNS privilegovaného koncového bodu.
    - **AzureEnvironment**: prostředí Azure používané pro nasazení centra Azure Stack. Vyžaduje se jenom pro nasazení Azure AD.
    - **CloudAdminCredential**: pověření pro správce cloudu, které je nutné pro přístup k privilegovanému koncovému bodu.
    - **AzCredential**: přihlašovací údaje pro účet správce služby Azure Stack hub. Použijte stejné přihlašovací údaje, které jste použili k nasazení centra Azure Stack.

## <a name="next-steps"></a>Další kroky

[Nabídka App Services jako PaaS](azure-stack-app-service-overview.md)
