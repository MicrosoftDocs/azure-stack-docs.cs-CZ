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
ms.openlocfilehash: 438cf2d8a34046f29d156aadc1cc82571e4b8a12
ms.sourcegitcommit: d619612f54eeba3231ed73ed149ff894f9bf838a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74993887"
---
# <a name="use-sql-databases-on-azure-stack"></a>Použití databází SQL na Azure Stack

Použijte poskytovatele prostředků SQL k nabídnutí databází SQL jako služby v [Azure Stack](azure-stack-overview.md). Po instalaci poskytovatele prostředků a jeho připojení k jedné nebo několika instancím SQL Serveru můžete vy i vaši uživatelé vytvářet:

- Databáze pro nativní aplikace v cloudu
- Weby, které používají SQL.
- Úlohy, které používají SQL.

Před instalací poskytovatele prostředků MySQL je potřeba zvážit několik omezení:

- Uživatelé mohou vytvářet a spravovat pouze jednotlivé databáze. Instance databázového serveru není pro koncové uživatele přístupná. To může omezit kompatibilitu s místními databázovými aplikacemi, které potřebují přístup k hlavní, dočasné databázi nebo k dynamické správě databází.
- Váš operátor Azure Stack zodpovídá za nasazení, aktualizaci, zabezpečení, konfiguraci a údržbu serverů a hostitelů služby SQL Database. Služba RP neposkytuje žádné funkce správy instance hostitele a databázového serveru. 
- Databáze od různých uživatelů v různých předplatných můžou být umístěné ve stejné instanci databázového serveru. RP neposkytuje žádný mechanismus pro izolaci databází na různých hostitelích nebo instancích databázového serveru.
- RP neposkytuje žádné sestavy o využití databáze pro tenanta.

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
