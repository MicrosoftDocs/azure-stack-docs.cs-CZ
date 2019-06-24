---
title: MySQL, který je hostitelem serverů v Azure stacku | Dokumentace Microsoftu
description: Přidání instancí MySQL pro zřizování prostřednictvím poskytovatele prostředků MySQL adaptéru
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
ms.date: 03/26/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 02/28/2019
ms.openlocfilehash: 9469904ddcb27d5526e35a33091e0ea3e54600f8
ms.sourcegitcommit: 104ccafcb72a16ae7e91b154116f3f312321cff7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2019
ms.locfileid: "67308612"
---
# <a name="add-hosting-servers-for-the-mysql-resource-provider"></a>Přidání hostitelské servery pro poskytovatele prostředků MySQL

MySQL, který je hostitelem instance serveru na virtuálním počítači (VM), můžete hostovat v [Azure Stack](azure-stack-overview.md), nebo mimo prostředí Azure Stack, dokud poskytovatele prostředků MySQL můžete připojit k instanci virtuálního počítače.

> [!NOTE]
> Poskytovatele prostředků MySQL musí být vytvořené v rámci předplatného poskytovatele výchozí při hostitelské servery MySQL by měl být vytvořen v fakturovatelná, předplatná uživatelů. Server zprostředkovatele prostředků není vhodné používat pro hostování databází uživatelů.

Verze MySQL 5.6, 5.7 a 8.0, lze pro hostitelské servery. Poskytovatele prostředků MySQL nepodporuje ověřování caching_sha2_password; který bude přidán v další vydané verzi. Servery MySQL 8.0 musí být nakonfigurovány pro použití mysql_native_password. MariaDB je také podporována.

## <a name="connect-to-a-mysql-hosting-server"></a>Připojení k hostování serveru MySQL

Ujistěte se, že máte přihlašovací údaje pro účet s oprávněními správce systému. Přidání hostitelského serveru, postupujte podle těchto kroků:

1. Přihlaste se k portálu Azure Stack operátor jako správce služby.
2. Vyberte **Všechny služby**.
3. V části **prostředky pro správu** vyberte kategorii **hostování servery MySQL** >  **+ přidat**. Tím se otevře **přidat Server pro hostování MySQL** dialogového okna, je znázorněno na následujícím snímku obrazovky.

   ![Konfigurace serveru pro hostování](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

4. Zadejte podrobnosti připojení vaší instance serveru MySQL.

   * Pro **názvem serveru MySQL hostování**, zadejte plně kvalifikovaný název domény (FQDN) nebo platná adresa IPv4. Nepoužívejte krátký název virtuálního počítače.
   * Výchozí správce **uživatelské jméno** pro Bitnami MySQL Image dostupných v Tržišti Azure Stack je *kořenové*. 
   * Pokud si nejste jisti kořenové **heslo**, najdete v článku [Bitnami dokumentaci](https://docs.bitnami.com/azure/faq/#how-to-find-application-credentials) postup, jak se dá stáhnout. 
   * Výchozí instanci MySQL není k dispozici, takže budete muset zadat **velikost hostování služby v GB**. Zadejte velikost, která je blízko kapacity databázového serveru.
   * Zachovat výchozí nastavení pro **předplatné**.
   * Pro **skupiny prostředků**, vytvořte novou, nebo použijte existující skupinu.

   > [!NOTE]
   > Pokud instanci MySQL přístupný tenanta a správce Azure Resource Manageru, můžete ji umístit pod kontrolou zprostředkovatele prostředků. Ale instanci MySQL **musí** přidělit jenom pro poskytovatele prostředků.

5. Vyberte **SKU** otevřít **vytvořit SKU** dialogového okna.

   ![Vytvoří skladová jednotka MySQL](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png)

   SKU **název** by měly odrážet vlastnosti skladové Položce, takže uživatelé můžou nasazovat své databáze do příslušné SKU.

6. Vyberte **OK** vytvoření skladové Položce.
   > [!NOTE]
   > SKU může trvat až hodinu, uvidí na portálu. Nelze vytvořit databázi, dokud SKU není nasazená a běží.

7. V části **přidat Server pro hostování MySQL**vyberte **vytvořit**.

Jak budete přidávat servery, můžete je přiřadíte k nové nebo existující skladové položky k rozlišení nabídek služeb. Například můžete mít instanci MySQL enterprise, která poskytuje zvýšenou databázovou a automatické zálohování. Tento server výkonné pro různá oddělení můžete registrovat ve vaší organizaci.

## <a name="security-considerations-for-mysql"></a>Informace o zabezpečení pro MySQL

Následující informace platí pro RP a MySQL hostitelské servery:

* Ujistěte se, že všechny hostitelské servery jsou nakonfigurované pro komunikaci pomocí protokolu TLS 1.2. Zobrazit [konfigurace MySQL šifrované připojení](https://dev.mysql.com/doc/refman/5.7/en/using-encrypted-connections.html).
* Využívat [transparentní šifrování dat](https://dev.mysql.com/doc/mysql-secure-deployment-guide/5.7/en/secure-deployment-data-encryption.html).
* Poskytovatele prostředků MySQL nepodporuje caching_sha2_password ověřování.

## <a name="increase-backend-database-capacity"></a>Zvýšení kapacity databáze back-endu

Nasazením další servery MySQL na portálu Azure Stack můžete zvýšit kapacitu databáze back-endu. Tyto servery přidáte do nové nebo existující skladové položky. Pokud přidáte server do existující skladové položky, ujistěte se, že vlastnosti serveru jsou stejné jako ostatní servery ve skladové Položce.

## <a name="sku-notes"></a>Skladová položka poznámky
Použijte název SKU, která popisuje možnosti servery ve skladové Položce, jako je například kapacitu a výkon. Název slouží jako vodítko k poskytování pomoci uživatelům nasadit své databáze do příslušné SKU. Například můžete použít názvy SKU k rozlišení nabídky služeb následujícími vlastnostmi:
  
* vysoké kapacity
* vysoce výkonné
* Vysoká dostupnost

Jako osvědčený postup musí všechny hostitelské servery v SKU mají stejné vlastnosti prostředků a výkonu.

Skladové položky nelze přiřadit konkrétní uživatelé nebo skupiny.

Chcete-li upravit skladovou jednotku, přejděte na **všechny služby** > **MySQL adaptér** > **SKU**. Vyberte SKU, které chcete změnit, proveďte potřebné změny a klikněte na tlačítko **Uložit** uložte změny. 

Pokud chcete odstranit skladovou Položku, který už je nepotřebujete, přejděte na **všechny služby** > **MySQL adaptér** > **skladové položky**. Klikněte pravým tlačítkem na název skladové položky a vyberte **odstranit** ho odstranit.

> [!IMPORTANT]
> To může trvat až hodinu nové SKU bude k dispozici na portálu user portal.

## <a name="make-mysql-database-servers-available-to-your-users"></a>Servery MySQL database zpřístupnit uživatelům

Vytvořte plány a nabídky pro databázové servery MySQL zpřístupnit uživatelům. Přidat službu Microsoft.MySqlAdapter plánu a vytvořit novou kvótu. MySQL nepovoluje omezení velikosti databáze.

> [!IMPORTANT]
> Může trvat až dvě hodiny pro nové kvóty bude k dispozici na portálu user portal nebo předtím, než se vynucuje změněné kvóty.

## <a name="next-steps"></a>Další postup

[Vytvoření databáze MySQL](azure-stack-mysql-resource-provider-databases.md)
