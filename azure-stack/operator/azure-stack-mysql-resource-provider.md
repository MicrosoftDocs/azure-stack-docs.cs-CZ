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
ms.openlocfilehash: 8f498896489a0c217b1f7c51ec4dda6b493ddda7
ms.sourcegitcommit: b5eb024d170f12e51cc852aa2c72eabf26792d8d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2019
ms.locfileid: "72534031"
---
# <a name="use-mysql-databases-on-microsoft-azure-stack"></a>Použití databází MySQL na Microsoft Azure Stack

Databáze MySQL se běžně používají s weby a podporují mnoho platforem webu. Můžete například vytvořit weby WordPress pomocí doplňku poskytovatele prostředků App Services (PaaS).

Po nasazení poskytovatele prostředků můžete:

* Vytvářejte servery a databáze MySQL pomocí Azure Resource Manager šablon nasazení.
* Poskytněte databáze MySQL jako službu.  

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
