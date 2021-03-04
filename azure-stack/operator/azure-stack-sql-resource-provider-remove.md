---
title: Odebrat poskytovatele prostředků SQL
titleSuffix: Azure Stack Hub
description: Naučte se, jak odebrat poskytovatele prostředků SQL z nasazení centra Azure Stack.
author: bryanla
ms.topic: article
ms.date: 10/02/2019
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 11/20/2019
ms.openlocfilehash: 67c6331111e18ec04f3084b4c34a971120733569
ms.sourcegitcommit: b844c19d1e936c36a85f450b7afcb02149589433
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "101839976"
---
# <a name="remove-the-sql-resource-provider"></a>Odebrat poskytovatele prostředků SQL

Před odebráním poskytovatele prostředků SQL je nutné odebrat všechny závislosti zprostředkovatele. Budete také potřebovat kopii balíčku pro nasazení, který se použil k instalaci poskytovatele prostředků.

> [!NOTE]
> Odkazy ke stažení pro instalační programy poskytovatele prostředků najdete v části [nasazení požadavků poskytovatele prostředků](./azure-stack-sql-resource-provider-deploy.md#prerequisites).

Při odebrání poskytovatele prostředků SQL se odstraní přidružené plány a kvóty spravované operátorem. Neodstraňují ale databáze tenantů z hostitelských serverů.

## <a name="to-remove-the-sql-resource-provider"></a>Odebrání poskytovatele prostředků SQL

1. Ověřte, že jste odebrali všechny existující závislosti poskytovatele prostředků SQL.

   > [!NOTE]
   > Odinstalace poskytovatele prostředků SQL bude pokračovat i v případě, že závislé prostředky aktuálně používají poskytovatele prostředků.
  
2. Získejte kopii instalačního balíčku poskytovatele prostředků SQL a potom spusťte samočinný extrahování, který extrahuje obsah do dočasného adresáře.

3. Otevřete nové okno konzoly PowerShellu se zvýšenými oprávněními a přejděte do adresáře, do kterého jste extrahovali instalační soubory poskytovatele prostředků SQL.

> [!IMPORTANT]
> Důrazně doporučujeme, abyste před spuštěním skriptu vymazat mezipaměť pomocí procesu Clear **-AzureRmContext-Scope CurrentUser** a **clear-AzureRmContext-Scope** .


4. Spusťte skript DeploySqlProvider.ps1 pomocí následujících parametrů:

    * **Odinstalace**: Odebere poskytovatele prostředků a všechny přidružené prostředky.
    * **PrivilegedEndpoint**: IP adresa nebo název DNS privilegovaného koncového bodu.
    * **AzureEnvironment**: prostředí Azure používané pro nasazení centra Azure Stack. Vyžaduje se jenom pro nasazení Azure AD.
    * **CloudAdminCredential**: přihlašovací údaje pro správce cloudu, které jsou nezbytné pro přístup k privilegovanému koncovému bodu.
    * **AzCredential**: přihlašovací údaje pro účet správce služby Azure Stack hub. Použijte stejné přihlašovací údaje, které jste použili k nasazení centra Azure Stack. Pokud účet, který používáte se službou AzCredential, vyžaduje vícefaktorové ověřování (MFA), skript se nezdaří.

## <a name="next-steps"></a>Další kroky

[Nabídka App Services jako PaaS](azure-stack-app-service-overview.md)
