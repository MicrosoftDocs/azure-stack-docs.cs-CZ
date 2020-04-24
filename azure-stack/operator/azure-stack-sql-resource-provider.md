---
title: Použití databází SQL
titleSuffix: Azure Stack Hub
description: Naučte se používat poskytovatele prostředků SQL Server k poskytování databází SQL jako služby v centru Azure Stack.
author: bryanla
ms.topic: article
ms.date: 10/02/2019
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 10/25/2019
ms.openlocfilehash: 15eaf26162b0d3f647d65dfab66e3d9327b2f357
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "77697140"
---
# <a name="use-sql-databases-on-azure-stack-hub"></a>Použití databází SQL v centru Azure Stack

Pomocí poskytovatele prostředků SQL můžete nabízet databáze SQL na [rozbočovači Azure Stack](azure-stack-overview.md). Po instalaci poskytovatele prostředků a jeho připojení k jedné nebo několika instancím SQL Serveru můžete vy i vaši uživatelé vytvářet:

- Databáze SQL pro nativní aplikace v cloudu
- Databáze SQL pro webové aplikace.

Omezení, která je třeba zvážit před instalací poskytovatele prostředků SQL:

- Uživatelé mohou vytvářet a spravovat pouze jednotlivé databáze. Instance databázového serveru není pro koncové uživatele přístupná. To může omezit kompatibilitu s místními databázovými aplikacemi, které potřebují přístup k hlavní, dočasné databázi nebo k dynamické správě databází.
- Váš operátor centra Azure Stack zodpovídá za nasazení, aktualizaci, zabezpečení, konfiguraci a údržbu serverů a hostitelů služby SQL Database. Služba RP neposkytuje žádné funkce správy instancí hostitele a databázového serveru.
- Databáze od různých uživatelů v různých předplatných můžou být umístěné ve stejné instanci databázového serveru. RP neposkytuje žádný mechanismus pro izolaci databází na různých hostitelích nebo instancích databázových serverů.
- RP neposkytuje žádné sestavy o využití databáze pro tenanta.

Pro tradiční SQL Server úlohy v místním prostředí se doporučuje SQL Server virtuální počítač v Azure Stack hub.

## <a name="sql-resource-provider-adapter-architecture"></a>Architektura adaptéru poskytovatele prostředků SQL

Poskytovatel prostředků se skládá z následujících součástí:

- **Virtuální počítač adaptéru poskytovatele prostředků SQL (VM)**, což je virtuální počítač s Windows serverem, na kterém běží služby poskytovatele.
- **Poskytovatel prostředků**, který zpracovává požadavky a přistupuje k databázovým prostředkům.
- **Servery, které hostují SQL Server**, které poskytují kapacitu pro databáze nazývané hostitelské servery.

Musíte vytvořit aspoň jednu instanci SQL Server nebo poskytnout přístup k externím instancím SQL Server.

> [!NOTE]
> Hostitelské servery, které jsou nainstalované v Azure Stack integrovaných systémech centra, se musí vytvořit z předplatného tenanta. Nelze je vytvořit z výchozího předplatného poskytovatele. Je nutné je vytvořit z portálu User Portal nebo pomocí prostředí PowerShell s odpovídajícím přihlášením. Všechny hostitelské servery jsou Fakturovatelné virtuální počítače a musí mít licence. Správce služby může být vlastníkem předplatného tenanta.

## <a name="next-steps"></a>Další kroky

[Nasazení poskytovatele prostředků SQL Serveru](azure-stack-sql-resource-provider-deploy.md)
