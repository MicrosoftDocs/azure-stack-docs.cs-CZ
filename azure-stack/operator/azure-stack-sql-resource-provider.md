---
title: Použití databází SQL v Azure stacku | Dokumentace Microsoftu
description: Zjistěte, jak nasadit SQL databáze jako služba v Azure stacku a rychlé kroky pro nasazení adaptéru poskytovatele prostředků SQL serveru.
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: quying
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: 2a558bc6276384cd78c1d20bfbaf09ebd9860fa1
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "64293121"
---
# <a name="use-sql-databases-on-microsoft-azure-stack"></a>Použití databází SQL v Microsoft Azure Stack

Pomocí poskytovatele prostředků SQL serveru nabízí databáze SQL jako služba [Azure Stack](azure-stack-overview.md). Po instalaci poskytovatele prostředků a připojte ho k jedné nebo více instancí systému SQL Server, můžete vytvořit vy a vaši uživatelé:

- Databáze pro aplikace nativní pro cloud.
- Weby, které používají SQL.
- Úlohy, které používají SQL.

Poskytovatel prostředků neobsahuje všechny databáze schopnosti správy [Azure SQL Database](https://azure.microsoft.com/services/sql-database/). Například elastických fondů, které automaticky přidělit prostředky nejsou podporovány. Ale podporuje zprostředkovatel prostředků, podobně jako vytvoření, čtení, aktualizace a odstranění (CRUD) operací v databázi serveru SQL Server. 

## <a name="sql-resource-provider-adapter-architecture"></a>Architektura adaptéru poskytovatele prostředků SQL

Poskytovatel prostředků se skládá z následujících součástí:

- **SQL prostředků poskytovatele adaptér virtuálního počítače (VM)**, což je virtuální počítač Windows Server, na kterém běží služby poskytovatele.
- **Poskytovatel prostředků**, která zpracovává požadavky a prostředky databáze přístupy.
- **Servery, které hostují SQL Server**, které poskytují kapacity pro databáze názvem hostitelskými servery.

Musíte vytvořit alespoň jednu instanci systému SQL Server nebo poskytovat přístup k externí instance systému SQL Server.

> [!NOTE]
> Hostitelské servery, které jsou nainstalované ve službě Azure Stack integrované systémy musí být vytvořené z tenanta předplatného. Nelze vytvořit z výchozí předplatné poskytovatele. Musí se vytvořit portál pro klienty nebo pomocí prostředí PowerShell s odpovídající přihlášení. Všechny hostitelské servery jsou fakturovatelná virtuální počítače a musí mít licence. Správce služby může být vlastníkem předplatného tenanta.

## <a name="next-steps"></a>Další postup

[Nasazení poskytovatele prostředků SQL Serveru](azure-stack-sql-resource-provider-deploy.md)
