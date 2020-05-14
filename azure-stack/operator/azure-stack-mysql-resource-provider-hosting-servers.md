---
title: Přidání hostitelských serverů MySQL do centra Azure Stack
description: Naučte se, jak přidat hostitelské servery MySQL pro zřizování prostřednictvím poskytovatele prostředků adaptéru MySQL.
author: bryanla
ms.topic: article
ms.date: 11/06/2019
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 11/06/2019
ms.openlocfilehash: 90b20ddcc129b8077cf28fa1a1a758054795de60
ms.sourcegitcommit: 4a8d7203fd06aeb2c3026d31ffec9d4fbd403613
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/12/2020
ms.locfileid: "83202507"
---
# <a name="add-mysql-hosting-servers-in-azure-stack-hub"></a>Přidání hostitelských serverů MySQL do centra Azure Stack

Instanci hostitelského serveru MySQL můžete hostovat na virtuálním počítači (VM) v [Azure Stackovém centru](azure-stack-overview.md)nebo na virtuálním počítači mimo prostředí Azure Stack hub, pokud se poskytovatel prostředků MySQL může připojit k instanci.

> [!NOTE]
> Poskytovatel prostředků MySQL by se měl vytvořit v předplatném výchozího poskytovatele, zatímco hostitelské servery MySQL by se měly vytvářet v fakturovatelných předplatných uživatele. Server poskytovatele prostředků by neměl být používán k hostování uživatelských databází.

Pro hostitelské servery lze použít verze MySQL 5,6, 5,7 a 8,0. MySQL RP nepodporuje caching_sha2_password ověřování. Servery MySQL 8,0 musí být nakonfigurovány tak, aby používaly mysql_native_password.

## <a name="prepare-a-mysql-hosting-server"></a>Příprava hostitelského serveru MySQL

### <a name="create-a-network-security-group-rule"></a>Vytvoření pravidla skupiny zabezpečení sítě

Ve výchozím nastavení není pro MySQL na hostitelském virtuálním počítači nakonfigurován žádný veřejný přístup. Aby mohl poskytovatel prostředků MySQL Azure Stack připojit a spravovat server MySQL, je nutné vytvořit pravidlo skupiny zabezpečení příchozí sítě (NSG).

1. Na portálu pro správu přejdete do skupiny prostředků vytvořené při nasazení serveru MySQL a vyberete skupinu zabezpečení sítě (**výchozí-Subnet-SG**):

   ![Vybrat skupinu zabezpečení sítě na portálu pro správu centra Azure Stack](media/azure-stack-tutorial-mysqlrp/img6.png)

2. Vyberte **příchozí pravidla zabezpečení** a pak vyberte **Přidat**.

    Do pole název **cílového portu** zadejte **3306** a v poli **název** a **Popis** volitelně zadejte popis.

   ![open](media/azure-stack-tutorial-mysqlrp/img7.png)

3. Výběrem **Přidat** zavřete dialog příchozí pravidlo zabezpečení.

### <a name="configure-external-access-to-the-mysql-hosting-server"></a>Konfigurace externího přístupu k hostitelskému serveru MySQL

Než bude možné server MySQL přidat jako hostitele serveru MySQL Azure Stack hub, musí být povolený externí přístup. Využijte Bitnami MySQL, který je k dispozici v tržišti centra Azure Stack jako příklad můžete nakonfigurovat externí přístup provedením následujících kroků.

1. Pomocí klienta SSH (Tento příklad používá výstup [) se](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)přihlaste k serveru MySQL z počítače, který má přístup k veřejné IP adrese.

    Použijte veřejnou IP adresu a přihlaste se k virtuálnímu počítači pomocí uživatelského jména a hesla aplikace, které jste vytvořili dřív bez speciálních znaků.

   ![LinuxLogin](media/azure-stack-tutorial-mysqlrp/bitnami1.png)

2. V okně Klient SSH pomocí následujícího příkazu zkontrolujte, že je služba Bitnami aktivní a spuštěná. Po zobrazení výzvy zadejte heslo Bitnami:

   `sudo service bitnami status`

   ![Ověřit službu Bitnami](media/azure-stack-tutorial-mysqlrp/bitnami2.png)

3. Pokud je hostitelský server MySQL verze 8,0 nebo vyšší, je nutné změnit metodu ověřování na **mysql_native_password**. Pokud je verze MySQL nižší než 8,0, můžete tento krok přeskočit.

   Bitnami MySQL jako příklad – konfigurační soubor je pod **/opt/Bitnami/MySQL/conf/my.CNF**. Nastavte vlastnost **default_authentication_plugin** hodnotou **mysql_native_password**.
   ```
   [mysqld]
   default_authentication_plugin=mysql_native_password
   ```
   Restartujte službu Bitnami a ujistěte se, že služba Bitnami běží správně.
   ```console
   sudo service bitnami restart
   sudo service bitnami status
   ```

4. Vytvořte uživatelský účet vzdáleného přístupu, který bude používat hostitelský server služby Azure Stack hub MySQL pro připojení k MySQL.

    Spusťte následující příkazy, abyste se přihlásili do MySQL jako kořenový adresář pomocí kořenového hesla, které se zaznamenává v *~/bitnami_credentials*. Vytvořte nového uživatele s oprávněními správce a nahraďte * \< uživatelské jméno \> * a * \< heslo \> * podle požadavků vašeho prostředí. V tomto příkladu je vytvořeným uživatelem název **sqlsa** a používá se silné heslo:

   ```sql
   mysql -u root -p
   create user <username>@'%' identified by '<password>';
   grant all privileges on *.* to <username>@'%' with grant option;
   flush privileges;
   ```

   ![Vytvořit administrativního uživatele](media/azure-stack-tutorial-mysqlrp/bitnami3.png)

5. Ujistěte se, že modul plug-in vytvořeného uživatele SQL **sqlsa** je **mysql_native_password** a pak ukončete klienta ssh.
   
   ```sql
   SELECT user,host,plugin from mysql.user;
   ```
6. Zaznamenejte nové informace o uživateli MySQL.

   Toto uživatelské jméno a heslo budou použity, když Azure Stack operátor centra vytvoří hostitelský server MySQL pomocí tohoto serveru MySQL.


## <a name="connect-to-a-mysql-hosting-server"></a>Připojení k hostitelskému serveru MySQL

Ujistěte se, že máte přihlašovací údaje k účtu s oprávněními správce systému.

> [!NOTE]
> Pro MySQL 8,0 a novější verze není vzdálený přístup ve výchozím nastavení povolen. Musíte vytvořit nový uživatelský účet a před tím, než ho přidáte jako hostitelský server, udělit previledge vzdálený přístup k tomuto uživatelskému účtu.

Chcete-li přidat hostitelský server, postupujte podle následujících kroků:

1. Přihlaste se k portálu správce Azure Stackového centra jako správce služby.
2. Vyberte **Všechny služby**.
3. V kategorii **prostředky pro správu** vyberte možnost **MySQL hostingové servery**  >  **+ Přidat**. Otevře se dialogové okno **Přidat hostitelský server MySQL** , které se zobrazí na následujícím snímku obrazovky.

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

Pokud chcete upravit SKU, otevřete **všechny služby**  >  SKU**adaptéru MySQL**  >  **SKUs**. Vyberte SKLADOVOU položku, kterou chcete upravit, proveďte potřebné změny a uložte změny kliknutím na **Uložit** . 

Pokud chcete odstranit SKU, které už nepotřebujete, přečtěte si **všechny služby**  >  SKU**adaptéru MySQL**  >  **SKUs**. Klikněte pravým tlačítkem na název SKU a vyberte **Odstranit** a odstraňte ho.

> [!IMPORTANT]
> Může trvat až hodinu, než se nové SKU zpřístupní na portálu User Portal.

## <a name="make-mysql-database-servers-available-to-your-users"></a>Zpřístupnění databázových serverů MySQL uživatelům

Vytvořte plány a nabídky, které uživatelům zpřístupní servery databáze MySQL. Přidejte do plánu službu Microsoft. MySqlAdapter a vytvořte novou kvótu. MySQL nepovoluje omezení velikosti databází.

> [!IMPORTANT]
> Může trvat až dvě hodiny, než budou nové kvóty dostupné na portálu User Portal nebo před tím, než se vynutila změněná kvóta.

## <a name="next-steps"></a>Další kroky

[Vytvoření databáze MySQL](azure-stack-mysql-resource-provider-databases.md)
