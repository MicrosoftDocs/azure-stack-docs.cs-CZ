---
title: Použití databází SQL
titleSuffix: Azure Stack Hub
description: Naučte se používat poskytovatele prostředků SQL Server k poskytování databází SQL jako služby v centru Azure Stack.
author: bryanla
ms.topic: article
ms.date: 8/19/2020
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 10/25/2019
ms.openlocfilehash: c0f599d8a63f0a1ea16e5a39303391cb1b0790a0
ms.sourcegitcommit: 8079220917523ab9ddb824e4bba3e9b091f38a9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2020
ms.locfileid: "88661584"
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
- Hostitelský server SQL můžete přesunout jenom do jiného předplatného v globálním Azure. Centrum Azure Stack nepodporuje přesun hostitelského serveru SQL na jiné předplatné.

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
