---
title: Používání databází SQL na Azure Stack | Microsoft Docs
description: Přečtěte si, jak můžete nasadit databáze SQL jako službu v Azure Stack a rychlé kroky pro nasazení SQL Serverho adaptéru poskytovatele prostředků.
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
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: 210d8e074cd8c0d62567b33b70cd75984f72d149
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71829285"
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Použití databází SQL na Microsoft Azure Stack

Použijte poskytovatele prostředků SQL Server k nabídnutí databází SQL jako služby [Azure Stack](azure-stack-overview.md). Až nainstalujete poskytovatele prostředků a připojíte ho k jedné nebo více instancím SQL Server, můžete vy a vaši uživatelé vytvořit:

- Databáze pro nativní aplikace v cloudu
- Weby, které používají SQL.
- Úlohy, které používají SQL.

Poskytovatel prostředků neposkytuje všechny možnosti správy databáze [Azure SQL Database](https://azure.microsoft.com/services/sql-database/). Například elastické fondy, které automaticky přidělují prostředky, nejsou podporovány. Poskytovatel prostředků však podporuje podobné operace vytvoření, čtení, aktualizace a odstranění (CRUD) v databázi SQL Server. 

## <a name="sql-resource-provider-adapter-architecture"></a>Architektura adaptéru poskytovatele prostředků SQL

Poskytovatel prostředků se skládá z následujících součástí:

- **Virtuální počítač adaptéru poskytovatele prostředků SQL (VM)** , což je virtuální počítač s Windows serverem, na kterém běží služby poskytovatele.
- **Poskytovatel prostředků**, který zpracovává požadavky a přistupuje k databázovým prostředkům.
- **Servery, které hostují SQL Server**, které poskytují kapacitu pro databáze nazývané hostitelské servery.

Musíte vytvořit aspoň jednu instanci SQL Server nebo poskytnout přístup k externím instancím SQL Server.

> [!NOTE]
> Hostitelské servery, které jsou nainstalované v Azure Stack integrovaných systémech, se musí vytvořit z předplatného tenanta. Nelze je vytvořit z výchozího předplatného poskytovatele. Je nutné je vytvořit z portálu tenanta nebo pomocí prostředí PowerShell s odpovídajícím přihlášením. Všechny hostitelské servery jsou Fakturovatelné virtuální počítače a musí mít licence. Správce služby může být vlastníkem předplatného tenanta.

## <a name="next-steps"></a>Další kroky

[Nasazení poskytovatele prostředků SQL Serveru](azure-stack-sql-resource-provider-deploy.md)
