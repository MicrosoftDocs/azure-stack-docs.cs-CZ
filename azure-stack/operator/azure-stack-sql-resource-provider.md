---
title: Použití databází SQL
titleSuffix: Azure Stack
description: Naučte se používat SQL Server poskytovatele prostředků k nabízení databází SQL jako služby v Azure Stack.
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
ms.openlocfilehash: d2ce6c0af2912a2658db80301c9a64c8e3d5c066
ms.sourcegitcommit: 62283e9826ea78b218f5d2c6c555cc44196b085d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2019
ms.locfileid: "74780724"
---
# <a name="use-sql-databases-on-azure-stack"></a>Použití databází SQL na Azure Stack

Pomocí poskytovatele prostředků SQL Server můžete nabízet databáze SQL jako službu pro [Azure Stack](azure-stack-overview.md). Po instalaci poskytovatele prostředků a jeho připojení k jedné nebo několika instancím SQL Serveru můžete vy i vaši uživatelé vytvářet:

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
