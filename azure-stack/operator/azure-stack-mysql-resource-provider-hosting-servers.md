---
title: Přidání hostitelských serverů MySQL do centra Azure Stack
description: Naučte se, jak přidat hostitelské servery MySQL pro zřizování prostřednictvím poskytovatele prostředků adaptéru MySQL.
author: mattbriggs
ms.topic: article
ms.date: 11/06/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 11/06/2019
ms.openlocfilehash: 6cd5d09dcfc2467bd596b94597d001c4803e1655
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76881807"
---
# <a name="add-mysql-hosting-servers-in-azure-stack-hub"></a>Přidání hostitelských serverů MySQL do centra Azure Stack

Instanci hostitelského serveru MySQL můžete hostovat na virtuálním počítači (VM) v [Azure Stackovém centru](azure-stack-overview.md)nebo na virtuálním počítači mimo prostředí Azure Stack hub, pokud se poskytovatel prostředků MySQL může připojit k instanci.

> [!NOTE]
> Poskytovatel prostředků MySQL by se měl vytvořit v předplatném výchozího poskytovatele, zatímco hostitelské servery MySQL by se měly vytvářet v fakturovatelných předplatných uživatele. Server poskytovatele prostředků by neměl být používán k hostování uživatelských databází.

Pro hostitelské servery lze použít verze MySQL 5,6, 5,7 a 8,0. MySQL RP nepodporuje caching_sha2_password ověřování; které budou přidány v další verzi. Servery MySQL 8,0 musí být nakonfigurovány tak, aby používaly mysql_native_password. MariaDB je také podporováno.

## <a name="connect-to-a-mysql-hosting-server"></a>Připojení k hostitelskému serveru MySQL

Ujistěte se, že máte přihlašovací údaje k účtu s oprávněními správce systému.

> [!NOTE]
> Pro MySQL 8,0 a novější verze není vzdálený přístup ve výchozím nastavení povolen. Musíte vytvořit nový uživatelský účet a před tím, než ho přidáte jako hostitelský server, udělit previledge vzdálený přístup k tomuto uživatelskému účtu.

Chcete-li přidat hostitelský server, postupujte podle následujících kroků:

1. Přihlaste se k portálu správce Azure Stackového centra jako správce služby.
2. Vyberte **Všechny služby**.
3. V kategorii **prostředky pro správu** vyberte možnost **MySQL hostingové servery** >  **+ Přidat**. Otevře se dialogové okno **Přidat hostitelský server MySQL** , které se zobrazí na následujícím snímku obrazovky.

   ![Konfigurace hostitelského serveru MySQL](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

4. Zadejte podrobnosti o připojení instance serveru MySQL.

   * Pro **název hostitelského serveru MySQL**zadejte plně kvalifikovaný název domény (FQDN) nebo platnou adresu IPv4. Nepoužívejte krátký název virtuálního počítače.
   * Výchozí **uživatelské jméno** správce pro Image Bitnami MySQL dostupné v tržišti služby Azure Stack hub je *root*.
   * Pokud neznáte kořenové **heslo**, přečtěte si [dokumentaci k Bitnami](https://docs.bitnami.com/azure/faq/#how-to-find-application-credentials) , kde najdete informace o tom, jak je získat.
   * Není zadaná výchozí instance MySQL, takže musíte zadat **Velikost hostitelského serveru v GB**. Zadejte velikost, která je blízko kapacity databázového serveru.
   * Ponechte výchozí nastavení pro **předplatné**.
   * Pro **skupinu prostředků**, vytvořte novou nebo použijte existující skupinu.

   > [!NOTE]
   > Pokud má klient přístup k instanci MySQL a Azure Resource Manager pro správu, můžete ho umístit pod kontrolu poskytovatele prostředků. Instance MySQL se ale **musí** přidělit výhradně poskytovateli prostředků.

5. Výběrem položky **SKU** otevřete dialog **vytvořit SKU** .

   ![Vytvoření SKU MySQL](./media/azure-stack-mysql-rp-deploy/mysql-new-sku.png)

   **Název** SKU by měl odrážet vlastnosti SKU, aby uživatelé mohli nasadit své databáze do příslušné SKU.

6. Vyberte **OK** a vytvořte SKU.
   > [!NOTE]
   > SKU může trvat až hodinu, než se na portálu zobrazí. Nemůžete vytvořit databázi, dokud není SKU nasazená a spuštěná.

7. V části **Přidat hostitelský server MySQL**vyberte **vytvořit**.

Když přidáváte servery, přiřadíte je k nové nebo existující SKU, abyste rozlišili nabídky služeb. Můžete mít například instanci Enterprise MySQL, která poskytuje vyšší databázi a automatické zálohování. Tento vysoce výkonný Server můžete vyhradit pro různá oddělení ve vaší organizaci.

## <a name="security-considerations-for-mysql"></a>Požadavky na zabezpečení MySQL

Následující informace platí pro hostitelské servery RP a MySQL:

* Zajistěte, aby byly všechny hostitelské servery nakonfigurované pro komunikaci pomocí protokolu TLS 1,1. Viz [Konfigurace MySQL pro použití šifrovaných připojení](https://dev.mysql.com/doc/refman/5.7/en/using-encrypted-connections.html).
* Použít [transparentní šifrování dat](https://dev.mysql.com/doc/mysql-secure-deployment-guide/5.7/en/secure-deployment-data-encryption.html).
* MySQL RP nepodporuje caching_sha2_password ověřování.

## <a name="increase-backend-database-capacity"></a>Zvýšení kapacity back-end databáze

Kapacitu back-endu databáze můžete zvýšit nasazením dalších serverů MySQL na portálu centra Azure Stack. Přidejte tyto servery do nové nebo existující SKU. Pokud přidáte server do existující SKU, ujistěte se, že jsou vlastnosti serveru stejné jako ostatní servery v SKU.

## <a name="sku-notes"></a>Poznámky SKU
Použijte název SKU, který popisuje možnosti serverů v SKU, jako je například kapacita a výkon. Název slouží jako pomůcka k tomu, aby uživatelé mohli nasadit své databáze do příslušné SKU. Například můžete použít názvy SKU k odlišení nabídek služeb následujícími charakteristikami:
  
* vysoká kapacita
* vysoký výkon
* vysoká dostupnost

Osvědčeným postupem je, že všechny hostitelské servery v SKU by měly mít stejné charakteristiky prostředků a výkonu.

SKU nelze přiřadit konkrétním uživatelům nebo skupinám.

Pokud chcete upravit SKU, otevřete **všechny služby** > **adaptér MySQL** > **SKU**. Vyberte SKLADOVOU položku, kterou chcete upravit, proveďte potřebné změny a uložte změny kliknutím na **Uložit** . 

Pokud chcete odstranit SKU, které už nepotřebujete, přečtěte si **všechny služby** > **adaptéru MySQL** > **SKU**. Klikněte pravým tlačítkem na název SKU a vyberte **Odstranit** a odstraňte ho.

> [!IMPORTANT]
> Může trvat až hodinu, než se nové SKU zpřístupní na portálu User Portal.

## <a name="make-mysql-database-servers-available-to-your-users"></a>Zpřístupnění databázových serverů MySQL uživatelům

Vytvořte plány a nabídky, které uživatelům zpřístupní servery databáze MySQL. Přidejte do plánu službu Microsoft. MySqlAdapter a vytvořte novou kvótu. MySQL nepovoluje omezení velikosti databází.

> [!IMPORTANT]
> Může trvat až dvě hodiny, než budou nové kvóty dostupné na portálu User Portal nebo před tím, než se vynutila změněná kvóta.

## <a name="next-steps"></a>Další kroky

[Vytvoření databáze MySQL](azure-stack-mysql-resource-provider-databases.md)
