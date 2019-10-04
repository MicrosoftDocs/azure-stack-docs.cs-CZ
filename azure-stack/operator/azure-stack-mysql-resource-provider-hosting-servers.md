---
title: Hostující servery MySQL na Azure Stack | Microsoft Docs
description: Postup přidání instancí MySQL pro zřizování prostřednictvím poskytovatele prostředků adaptéru MySQL
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
ms.lastreviewed: 02/28/2019
ms.openlocfilehash: 8c774d78cd03efeee830b1cbc5e726c7c53e3c57
ms.sourcegitcommit: a7207f4a4c40d4917b63e729fd6872b3dba72968
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/03/2019
ms.locfileid: "71909043"
---
# <a name="add-hosting-servers-for-the-mysql-resource-provider"></a>Přidání hostitelských serverů pro poskytovatele prostředků MySQL

Instanci hostitelského serveru MySQL můžete hostovat na virtuálním počítači v [Azure Stack](azure-stack-overview.md)nebo na virtuálním počítači mimo vaše Azure Stack prostředí, pokud se poskytovatel prostředků MySQL může připojit k instanci.

> [!NOTE]
> Poskytovatel prostředků MySQL by se měl vytvořit v předplatném výchozího poskytovatele, zatímco hostitelské servery MySQL by se měly vytvářet v fakturovatelných předplatných uživatele. Server poskytovatele prostředků by neměl být používán k hostování uživatelských databází.

Pro hostitelské servery lze použít verze MySQL 5,6, 5,7 a 8,0. MySQL RP nepodporuje ověřování caching_sha2_password; které budou přidány v další verzi. Servery MySQL 8,0 musí být nakonfigurovány tak, aby používaly mysql_native_password. MariaDB je také podporováno.

## <a name="connect-to-a-mysql-hosting-server"></a>Připojení k hostitelskému serveru MySQL

Ujistěte se, že máte přihlašovací údaje k účtu s oprávněními správce systému. Chcete-li přidat hostitelský server, postupujte podle následujících kroků:

1. Přihlaste se k portálu Azure Stack operator jako správce služby.
2. Vyberte **Všechny služby**.
3. V kategorii **prostředky pro správu** vyberte položku **MySQL hosting servery** >  **+ Přidat**. Tím se otevře dialogové okno **Přidat hostitelský server MySQL** zobrazené na následujícím snímku obrazovky.

   ![Konfigurace hostitelského serveru](./media/azure-stack-mysql-rp-deploy/mysql-add-hosting-server-2.png)

4. Zadejte podrobnosti o připojení instance serveru MySQL.

   * Pro **název hostitelského serveru MySQL**zadejte plně kvalifikovaný název domény (FQDN) nebo platnou adresu IPv4. Nepoužívejte krátký název virtuálního počítače.
   * Výchozí **uživatelské jméno** správce pro Image Bitnami MySQL dostupné na webu Azure Stack Marketplace je *root*. 
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

* Zajistěte, aby byly všechny hostitelské servery nakonfigurované pro komunikaci pomocí protokolu TLS 1,2. Viz [Konfigurace MySQL pro použití šifrovaných připojení](https://dev.mysql.com/doc/refman/5.7/en/using-encrypted-connections.html).
* Použít [transparentní šifrování dat](https://dev.mysql.com/doc/mysql-secure-deployment-guide/5.7/en/secure-deployment-data-encryption.html).
* MySQL RP nepodporuje ověřování caching_sha2_password.

## <a name="increase-backend-database-capacity"></a>Zvýšení kapacity back-end databáze

Kapacitu back-end databáze můžete zvýšit nasazením dalších serverů MySQL na portálu Azure Stack. Přidejte tyto servery do nové nebo existující SKU. Pokud přidáte server do existující SKU, ujistěte se, že jsou vlastnosti serveru stejné jako ostatní servery v SKU.

## <a name="sku-notes"></a>Poznámky SKU
Použijte název SKU, který popisuje možnosti serverů v SKU, jako je například kapacita a výkon. Název slouží jako pomůcka k tomu, aby uživatelé mohli nasadit své databáze do příslušné SKU. Například můžete použít názvy SKU k odlišení nabídek služeb následujícími charakteristikami:
  
* vysoká kapacita
* vysoký výkon
* vysoká dostupnost

Osvědčeným postupem je, že všechny hostitelské servery v SKU by měly mít stejné charakteristiky prostředků a výkonu.

SKU nelze přiřadit konkrétním uživatelům nebo skupinám.

Pokud chcete upravit SKU, otevřete **všechny služby** > **SKU** **adaptéru** > MySQL. Vyberte SKLADOVOU položku, kterou chcete upravit, proveďte potřebné změny a uložte změny kliknutím na **Uložit** . 

Pokud chcete odstranit SKU, které už nepotřebujete, přečtěte si **všechny služby** > **SKU** **adaptéru** > MySQL. Klikněte pravým tlačítkem na název SKU a vyberte **Odstranit** a odstraňte ho.

> [!IMPORTANT]
> Může trvat až hodinu, než se nové SKU zpřístupní na portálu User Portal.

## <a name="make-mysql-database-servers-available-to-your-users"></a>Zpřístupnění databázových serverů MySQL uživatelům

Vytvořte plány a nabídky, které uživatelům zpřístupní servery databáze MySQL. Přidejte do plánu službu Microsoft. MySqlAdapter a vytvořte novou kvótu. MySQL nepovoluje omezení velikosti databází.

> [!IMPORTANT]
> Může trvat až dvě hodiny, než budou nové kvóty dostupné na portálu User Portal nebo před tím, než se vynutila změněná kvóta.

## <a name="next-steps"></a>Další kroky

[Vytvoření databáze MySQL](azure-stack-mysql-resource-provider-databases.md)
