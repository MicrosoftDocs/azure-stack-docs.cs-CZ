---
title: Nabízet vysoce dostupné databáze MySQL v Azure Stack hub
description: Naučte se, jak vytvořit hostitelský počítač poskytovatele prostředků serveru MySQL a vysoce dostupné databáze MySQL pomocí centra Azure Stack.
services: azure-stack
author: BryanLa
manager: femila
editor: ''
ms.service: azure-stack
ms.topic: article
ms.date: 10/07/2019
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 10/23/2018
ms.openlocfilehash: 19d8410c42c301ad05573ec27b815d5eee869843
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75813789"
---
# <a name="offer-highly-available-mysql-databases"></a>Nabízí vysoce dostupné databáze MySQL

Jako operátor centra Azure Stack můžete nakonfigurovat serverové virtuální počítače pro hostování databází MySQL serveru. Po úspěšném vytvoření clusteru MySQL a jeho správě pomocí centra Azure Stack můžete uživatelům, kteří se přihlásili k odběru služeb MySQL, snadno vytvořit vysoce dostupné databáze MySQL.

Tento článek popisuje, jak pomocí položek webu Marketplace centra Azure Stack vytvořit [MySQL s clusterem replikace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.mysql-cluster). Toto řešení používá více virtuálních počítačů pro replikaci databází z hlavního uzlu do konfigurovatelného počtu replik. Po vytvoření se cluster dá přidat jako hostitelský server MySQL Azure Stack hub a potom můžou uživatelé vytvářet vysoce dostupné databáze MySQL.

> [!IMPORTANT]
> Pro všechna prostředí Azure Cloud Subscriptions nemusí být k dispozici položka webu **MySQL s replikací** Azure Stack centra pro replikaci. Před pokusem o provedení zbývající části tohoto Tutoral ověřte, že je ve vašem předplatném dostupná položka Marketplace.

Co se naučíte:

> [!div class="checklist"]
> * Vytvoření clusteru serverů MySQL z položek Marketplace
> * Vytvoření hostitelského serveru MySQL Azure Stack hub
> * Vytvoření vysoce dostupné databáze MySQL

Vytvoří se tři clustery serveru MySQL pro virtuální počítače a nakonfigurují se pomocí dostupných položek centra Azure Stack hub. 

Než začnete, ujistěte se, že [poskytovatel prostředků serveru MySQL](azure-stack-mysql-resource-provider-deploy.md) je úspěšně nainstalovaný a že na webu Azure Stack hub Marketplace jsou k dispozici následující položky:

> [!IMPORTANT]
> K vytvoření clusteru MySQL se vyžadují všechny následující.

- [MySQL s replikací](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.mysql-cluster) Toto je šablona řešení Bitnami, která se bude používat pro nasazení clusteru MySQL.
- [Debian 8 "Jessie"](https://azuremarketplace.microsoft.com/marketplace/apps/credativ.Debian). Debian 8 "Jessie" s jádrem pro Microsoft Azure poskytovaného credativ. Debian GNU/Linux je jednou z nejoblíbenějších distribucí systému Linux.
- [Vlastní skript pro linux 2,0](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft.custom-script-linux?tab=Overview). Rozšíření vlastních skriptů je nástroj, který umožňuje provádět úlohy přizpůsobení virtuálních počítačů po zřízení virtuálního počítače. Když se toto rozšíření přidá do virtuálního počítače, může stáhnout skripty z Azure Storage a spustit je na VIRTUÁLNÍm počítači. Úkoly rozšíření vlastních skriptů je také možné automatizovat pomocí rutin Azure PowerShell a rozhraní příkazového řádku Azure pro více platforem (xPlat CLI).
- Přístup k virtuálnímu počítači pro rozšíření pro Linux 1.4.7 Rozšíření pro přístup k VIRTUÁLNÍm počítačům umožňuje resetovat heslo, klíč SSH nebo konfigurace SSH, takže můžete znovu získat přístup k vašemu VIRTUÁLNÍmu počítači. Můžete také přidat nového uživatele pomocí hesla nebo klíče SSH nebo odstranit uživatele pomocí tohoto rozšíření. Toto rozšíření cílí na virtuální počítače Linux.

Další informace o přidávání položek do tržiště centra Azure Stack najdete v tématu [Přehled centra Azure Stack na webu Marketplace](azure-stack-marketplace.md).

Také budete potřebovat klienta SSH, jako je například výstup pro přihlášení k virtuálním [počítačům se systémem](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) Linux po jejich nasazení.

## <a name="create-a-mysql-server-cluster"></a>Vytvoření clusteru serverů MySQL 
Pomocí kroků v této části můžete nasadit cluster serveru MySQL pomocí položky [MySQL s replikací](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.mysql-cluster) Marketplace. Tato šablona nasadí tři instance serveru MySQL nakonfigurované v clusteru MySQL s vysokou dostupností. Ve výchozím nastavení vytvoří následující prostředky:

- Virtuální síť
- Skupina zabezpečení sítě
- Účet úložiště
- Skupina dostupnosti
- Tři síťová rozhraní (jedna pro každý výchozí virtuální počítač)
- Veřejná IP adresa (pro primární virtuální počítač clusteru MySQL)
- Tři virtuální počítače se systémem Linux pro hostování clusteru MySQL

1. 
   [!INCLUDE [azs-admin-portal](../includes/azs-admin-portal.md)]

2. Vyberte **\+** **vytvořit prostředek** > **COMPUTE**a pak **MySQL s replikací**.

   ![Nasazení vlastní šablony](media/azure-stack-tutorial-mysqlrp/1.png)

3. Poskytněte základní informace o nasazení na stránce **základy** . Zkontrolujte výchozí hodnoty a podle potřeby změňte nastavení a klikněte na tlačítko **OK**.<br><br>Minimálně zadejte následující:
   - Název nasazení (výchozí je mymysql)
   - Kořenové heslo aplikace Zadejte alfanumerické heslo o 12 znacích **bez speciálních znaků** .
   - Název aplikační databáze (výchozí hodnota je Bitnami)
   - Počet virtuálních počítačů repliky databáze MySQL k vytvoření (výchozí hodnota je 2)
   - Vyberte předplatné, které chcete použít.
   - Vyberte skupinu prostředků, kterou chcete použít, nebo vytvořte novou.
   - Vyberte umístění (výchozí nastavení je místní pro ASDK).

   [![](media/azure-stack-tutorial-mysqlrp/2-sm.PNG "Deployment basics")](media/azure-stack-tutorial-mysqlrp/2-lg.PNG#lightbox)

4. Na stránce **Konfigurace prostředí** zadejte následující informace a pak klikněte na **OK**: 
   - Heslo nebo veřejný klíč SSH pro ověřování pomocí protokolu Secure Shell (SSH). Pokud použijete heslo, musí obsahovat písmena, číslice a **může** obsahovat speciální znaky.
   - Velikost virtuálního počítače (výchozí nastavení je Standard D1 v2 VM)
   - Velikost datového disku v GB klikněte na **OK** .

   [![](media/azure-stack-tutorial-mysqlrp/3-sm.PNG "Environment configuration")](media/azure-stack-tutorial-mysqlrp/3-lg.PNG#lightbox)

5. Projděte si **Souhrn**nasazení. Volitelně si můžete stáhnout vlastní šablonu a parametry a pak kliknout na **OK**.

   [![](media/azure-stack-tutorial-mysqlrp/4-sm.PNG "Summary")](media/azure-stack-tutorial-mysqlrp/4-lg.PNG#lightbox)

6. Nasazení spustíte kliknutím na **vytvořit** na stránce **koupit** .

   ![Nákup](media/azure-stack-tutorial-mysqlrp/5.png)

    > [!NOTE]
    > Nasazení bude trvat přibližně hodinu. Než budete pokračovat, ujistěte se, že nasazení bylo dokončeno a cluster MySQL byl zcela nakonfigurován. 

7. Po úspěšném dokončení všech nasazení zkontrolujte položky skupiny prostředků a vyberte položku veřejné IP adresy **mysqlip** . Poznamenejte si veřejnou IP adresu a celý plně kvalifikovaný název domény veřejné IP adresy clusteru.<br><br>Tuto možnost bude nutné poskytnout operátorovi centra Azure Stack, aby mohli vytvořit hostitelský server MySQL, který využívá tento cluster MySQL.


### <a name="create-a-network-security-group-rule"></a>Vytvoření pravidla skupiny zabezpečení sítě
Ve výchozím nastavení není pro MySQL na hostitelském virtuálním počítači nakonfigurován žádný veřejný přístup. Aby mohl poskytovatel prostředků MySQL Azure Stack připojit a spravovat cluster MySQL, je nutné vytvořit pravidlo skupiny zabezpečení příchozí sítě (NSG).

1. Na portálu pro správu přejděte na skupinu prostředků vytvořenou při nasazení clusteru MySQL a vyberte skupinu zabezpečení sítě (**výchozí-podsíť-SG**):

   ![open (otevírá)](media/azure-stack-tutorial-mysqlrp/6.png)

2. Vyberte **příchozí pravidla zabezpečení** a pak klikněte na **Přidat**.<br><br>Do pole název **cílového portu** zadejte **3306** a v poli **název** a **Popis** volitelně zadejte popis. Kliknutím na tlačítko Přidat zavřete dialogové okno příchozí pravidlo zabezpečení.

   ![open (otevírá)](media/azure-stack-tutorial-mysqlrp/7.png)

### <a name="configure-external-access-to-the-mysql-cluster"></a>Konfigurace externího přístupu ke clusteru MySQL
Než bude možné cluster MySQL přidat jako hostitele serveru MySQL Azure Stack hub, musí být povolený externí přístup.

1. V tomto [příkladu používá klient SSH,](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)přihlásí se k primárnímu počítači MySQL z počítače, který má přístup k veřejné IP adrese. Primární název virtuálního počítače MySQL obvykle končí na **0** a má přiřazenou veřejnou IP adresu.<br><br>Použijte veřejnou IP adresu a přihlaste se k virtuálnímu počítači pomocí uživatelského jména **Bitnami** a hesla aplikace, které jste vytvořili dřív bez speciálních znaků.

   ![LinuxLogin](media/azure-stack-tutorial-mysqlrp/bitnami1.png)


2. V okně Klient SSH pomocí následujícího příkazu zkontrolujte, že je služba Bitnami aktivní a spuštěná. Po zobrazení výzvy zadejte heslo Bitnami:

   `sudo service bitnami status`

   ![Kontrolovat službu](media/azure-stack-tutorial-mysqlrp/bitnami2.png)

3. Vytvořte uživatelský účet vzdáleného přístupu, který má hostitelský server služby Azure Stack hub MySQL používat pro připojení k MySQL, a pak ukončete klienta SSH.<br><br>Spusťte následující příkazy pro přihlášení do MySQL jako kořenového adresáře pomocí kořenového hesla, které jste vytvořili dříve, a vytvořte nového uživatele s oprávněními správce, nahraďte *\<username\>* a *\<heslo\>* třeba pro vaše prostředí. V tomto příkladu se uživatel, který má být vytvořen, jmenuje **sqlsa** a použije se silné heslo:

   ```mysql
   mysql -u root -p
   create user <username>@'%' identified by '<password>';
   grant all privileges on *.* to <username>@'%' with grant option;
   flush privileges;
   ```
   ![Vytvořit administrativního uživatele](media/azure-stack-tutorial-mysqlrp/bitnami3.png)


4. Zaznamenejte nové informace o uživateli MySQL.<br><br>Toto uživatelské jméno a heslo budete muset zadat spolu s veřejnou IP adresou nebo úplným plně kvalifikovaným názvem domény veřejné IP adresy pro cluster, do operátoru centra Azure Stack, aby mohli vytvořit hostitelský server MySQL pomocí tohoto clusteru MySQL.


## <a name="create-an-azure-stack-hub-mysql-hosting-server"></a>Vytvoření hostitelského serveru MySQL Azure Stack hub
Po vytvoření a správné konfiguraci clusteru serveru MySQL musí operátor centra Azure Stack vytvořit hostitelský server Azure Stack hub pro MySQL, aby bylo možné uživatelům vytvořit databáze. 

Pokud jste vytvořili skupinu prostředků clusteru MySQL (**mysqlip**), ujistěte se, že používáte veřejnou IP adresu nebo plně kvalifikovaný název domény pro veřejnou IP adresu prvního virtuálního počítače clusteru MySQL zaznamenanou dříve. Kromě toho bude muset operátor znát přihlašovací údaje pro ověření serveru MySQL, které jste vytvořili pro vzdálený přístup k databázi clusteru MySQL.

> [!NOTE]
> Tento krok je nutné spustit z portálu pro správu centra Azure Stack pomocí operátoru centra Azure Stack.

Pomocí veřejné IP adresy clusteru MySQL a přihlašovacích údajů pro ověřování MySQL teď může operátor centra Azure Stack [vytvořit hostitelský server MySQL pomocí nového clusteru MySQL](azure-stack-mysql-resource-provider-hosting-servers.md#connect-to-a-mysql-hosting-server). 

Také se ujistěte, že máte vytvořené plány a nabídky, aby bylo možné vytvářet databáze MySQL pro uživatele. Operátor bude muset přidat službu **Microsoft. MySqlAdapter** do plánu a vytvořit novou kvótu specifickou pro vysoce dostupné databáze. Další informace o vytváření plánů najdete v tématu [Přehled služeb, plánů, nabídek a předplatných](service-plan-offer-subscription-overview.md).

> [!TIP]
> Službu **Microsoft. MySqlAdapter** nebude možné přidat do plánů, dokud nebude [nasazený poskytovatel prostředků serveru MySQL](azure-stack-mysql-resource-provider-deploy.md).



## <a name="create-a-highly-available-mysql-database"></a>Vytvoření vysoce dostupné databáze MySQL
Po vytvoření, konfiguraci a přidání clusteru MySQL jako hostitelského serveru Azure Stack hub MySQL pomocí operátoru centra Azure Stack může uživatel s předplatným, včetně možností databáze MySQL serveru, vytvořit vysoce dostupné databáze MySQL postupujte podle kroků v této části. 

> [!NOTE]
> Tyto kroky spusťte z uživatelského portálu Azure Stackového centra jako uživatel tenanta s předplatným, které poskytuje možnosti serveru MySQL (Microsoft. MySQLAdapter Service).

1. 
   [!INCLUDE [azs-user-portal](../includes/azs-user-portal.md)]

2. Vyberte **\+** **vytvořit prostředek** > **úložiště dat \+** a potom **databázi MySQL**.<br><br>Zadejte požadované informace o vlastnostech databáze, včetně názvu, kolace, předplatného, které chcete použít, a umístění, které se má použít pro nasazení. 

   ![Vytvořit databázi MySQL](./media/azure-stack-tutorial-mysqlrp/createdb1.png)

3. Vyberte **SKU** a pak zvolte příslušnou SKU hostitelského serveru MySQL, které chcete použít. V tomto příkladu operátor centra Azure Stack vytvořil SKU **MySQL-ha** , aby podporoval vysokou dostupnost databází clusteru MySQL.

   ![Vybrat SKU](./media/azure-stack-tutorial-mysqlrp/createdb2.png)

4. Vyberte **přihlašovací** > **vytvořit nové přihlášení** a pak zadat přihlašovací údaje pro ověřování MySQL, které se mají použít pro novou databázi. Po dokončení klikněte na **OK** a pak na **vytvořit** a zahajte proces nasazení databáze.

   ![Přidat přihlašovací údaje](./media/azure-stack-tutorial-mysqlrp/createdb3.png)

5. Po úspěšném dokončení nasazení databáze MySQL zkontrolujte vlastnosti databáze a zjistěte připojovací řetězec, který se má použít pro připojení k nové vysoce dostupné databázi. 

   ![Zobrazit připojovací řetězec](./media/azure-stack-tutorial-mysqlrp/createdb4.png)

## <a name="next-steps"></a>Další kroky

[Aktualizace poskytovatele prostředků MySQL](azure-stack-mysql-resource-provider-update.md)
