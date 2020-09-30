---
title: Použití databází MySQL jako PaaS v centru Azure Stack
description: Naučte se nasadit poskytovatele prostředků MySQL a poskytovat databáze MySQL jako službu na Azure Stack hub.
author: bryanla
ms.topic: article
ms.date: 9/22/2020
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 10/25/2019
ms.openlocfilehash: 70a65f030d930960dbdd057fa130eadeb6adcf8a
ms.sourcegitcommit: 69cfff119ab425d0fbb71e38d1480d051fc91216
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91572836"
---
# <a name="use-mysql-databases-on-microsoft-azure-stack-hub"></a>Použití databází MySQL v centru Microsoft Azure Stack

Pomocí poskytovatele prostředků MySQL můžete nabízet databáze MySQL v [centru Azure Stack](azure-stack-overview.md). Po nasazení poskytovatele prostředků a jeho propojení s jednou nebo více instancemi serveru MySQL můžete vytvořit:

* Databáze MySQL pro nativní aplikace v cloudu
* Databáze MySQL pro webové aplikace.  

Před instalací poskytovatele prostředků MySQL je potřeba zvážit několik omezení:

- Uživatelé mohou vytvářet a spravovat pouze jednotlivé databáze. Instance databázového serveru není pro koncové uživatele přístupná. To může omezit kompatibilitu s místními databázovými aplikacemi, které potřebují přístup k hlavní, dočasné databázi nebo k dynamické správě databází.
- Váš operátor centra Azure Stack zodpovídá za nasazení, aktualizaci, zabezpečení, konfiguraci a údržbu databázových serverů MySQL a hostitelů. Služba RP neposkytuje žádné funkce správy instance hostitele a databázového serveru. 
- Databáze od různých uživatelů v různých předplatných můžou být umístěné ve stejné instanci databázového serveru. RP neposkytuje žádný mechanismus pro izolaci databází na různých hostitelích nebo instancích databázového serveru.
- RP neposkytuje žádné sestavy o využití databáze pro tenanta.

## <a name="mysql-resource-provider-adapter-architecture"></a>Architektura adaptéru poskytovatele prostředků MySQL

Poskytovatel prostředků má následující součásti:

* **Virtuální počítač adaptéru poskytovatele prostředků MySQL (VM)**, což je virtuální počítač s Windows serverem, na kterém běží služby poskytovatele.
* **Poskytovatel prostředků**, který zpracovává požadavky a přistupuje k databázovým prostředkům.
* **Servery, které hostují Server MySQL**poskytující kapacitu pro databáze nazývané hostitelské servery. Instance MySQL můžete vytvořit sami nebo můžete poskytnout přístup k externím instancím MySQL. [Galerie pro rychlý Start centra Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows) obsahuje ukázkovou šablonu, kterou můžete použít k těmto akcím:

  * Vytvořte pro vás Server MySQL.
  * Stáhněte a nasaďte Server MySQL z Azure Marketplace.

> [!NOTE]
> Hostitelské servery, které jsou nainstalované v Azure Stack integrovaných systémech centra, se musí vytvořit z předplatného tenanta. Nelze je vytvořit z výchozího předplatného poskytovatele. Je nutné je vytvořit z portálu User Portal nebo z relace PowerShellu s odpovídajícím přihlášením. Všechny hostitelské servery jsou Fakturovatelné virtuální počítače a musí mít licence. Správce služby může být vlastníkem předplatného tenanta.

### <a name="required-privileges"></a>Požadovaná oprávnění

Systémový účet musí mít následující oprávnění:

* **Databáze:** vytvořit, vyřadit
* **Přihlášení:** vytvořit, nastavit, vyřadit, udělit, odvolat  

## <a name="next-steps"></a>Další kroky

[Nasazení poskytovatele prostředků MySQL](azure-stack-mysql-resource-provider-deploy.md)
