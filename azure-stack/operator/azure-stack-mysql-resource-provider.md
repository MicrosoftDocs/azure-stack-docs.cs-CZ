---
title: Používejte databáze MySQL jako PaaS na Azure Stack | Microsoft Docs
description: Naučte se nasadit poskytovatele prostředků MySQL a poskytovat databáze MySQL jako službu na Azure Stack.
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
ms.openlocfilehash: ceda705c4a06ac9465c3f017a87986ba6e20e4b3
ms.sourcegitcommit: e57a53caac50d1f8762307e065fe886fcda7eadf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2019
ms.locfileid: "75190858"
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>Použití databází MySQL na Microsoft Azure Stack

Pomocí poskytovatele prostředků MySQL poskytněte službu MySQL Database Service v [Azure Stack](azure-stack-overview.md). Po nasazení poskytovatele prostředků a jeho propojení s jednou nebo více instancemi serveru MySQL můžete:

* Vytvářejte databáze MySQL pomocí Azure Resource Manager šablon nasazení.
* Poskytněte databáze MySQL jako službu.  

Před instalací poskytovatele prostředků MySQL je potřeba zvážit několik omezení:

- Uživatelé mohou vytvářet a spravovat pouze jednotlivé databáze. Instance databázového serveru není pro koncové uživatele přístupná. To může omezit kompatibilitu s místními databázovými aplikacemi, které potřebují přístup k hlavní, dočasné databázi nebo k dynamické správě databází.
- Váš operátor Azure Stack zodpovídá za nasazení, aktualizaci, zabezpečení, konfiguraci a údržbu databázových serverů MySQL a hostitelů. Služba RP neposkytuje žádné funkce správy instance hostitele a databázového serveru. 
- Databáze od různých uživatelů v různých předplatných můžou být umístěné ve stejné instanci databázového serveru. RP neposkytuje žádný mechanismus pro izolaci databází na různých hostitelích nebo instancích databázového serveru.
- RP neposkytuje žádné sestavy o využití databáze pro tenanta.

## <a name="mysql-resource-provider-adapter-architecture"></a>Architektura adaptéru poskytovatele prostředků MySQL

Poskytovatel prostředků má následující součásti:

* **Virtuální počítač adaptéru poskytovatele prostředků MySQL (VM)** , což je virtuální počítač s Windows serverem, na kterém běží služby poskytovatele.
* **Poskytovatel prostředků**, který zpracovává požadavky a přistupuje k databázovým prostředkům.
* **Servery, které hostují Server MySQL**poskytující kapacitu pro databáze nazývané hostitelské servery. Instance MySQL můžete vytvořit sami nebo můžete poskytnout přístup k externím instancím MySQL. [Galerie rychlý start Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/mysql-standalone-server-windows) má ukázkovou šablonu, kterou můžete použít k těmto akcím:

  * Vytvořte pro vás Server MySQL.
  * Stáhněte a nasaďte Server MySQL z Azure Marketplace.

> [!NOTE]
> Hostitelské servery, které jsou nainstalované v Azure Stack integrovaných systémech, se musí vytvořit z předplatného tenanta. Nelze je vytvořit z výchozího předplatného poskytovatele. Je potřeba je vytvořit z portálu tenanta nebo z relace PowerShellu s odpovídajícím přihlášením. Všechny hostitelské servery jsou Fakturovatelné virtuální počítače a musí mít licence. Správce služby může být vlastníkem předplatného tenanta.

### <a name="required-privileges"></a>Požadovaná oprávnění

Systémový účet musí mít následující oprávnění:

* **Databáze:** vytvořit, vyřadit
* **Přihlášení:** vytvořit, nastavit, vyřadit, udělit, odvolat  

## <a name="next-steps"></a>Další kroky

[Nasazení poskytovatele prostředků MySQL](azure-stack-mysql-resource-provider-deploy.md)
