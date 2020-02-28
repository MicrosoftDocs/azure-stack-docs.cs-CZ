---
title: Vytváření vysoce dostupných databází MySQL
titleSuffix: Azure Stack Hub
description: Naučte se, jak vytvořit hostitelský počítač poskytovatele prostředků serveru MySQL a vysoce dostupné databáze MySQL pomocí centra Azure Stack.
author: BryanLa
ms.topic: article
ms.date: 10/07/2019
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 10/23/2019
ms.openlocfilehash: 327b63e2b21ea545fd6e0d175d531862ceeac0d6
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77696919"
---
# <a name="create-highly-available-mysql-databases"></a>Vytváření vysoce dostupných databází MySQL

Jako operátor centra Azure Stack můžete nakonfigurovat serverové virtuální počítače pro hostování databází MySQL serveru. Po úspěšném vytvoření a správě clusteru MySQL pomocí centra Azure Stack můžete uživatelům, kteří se přihlásili k odběru služeb MySQL, snadno vytvořit vysoce dostupné databáze MySQL.

V tomto článku se dozvíte, jak pomocí Azure Stackch položek na webu Marketplace vytvořit [MySQL s clusterem replikace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.mysql-cluster). Toto řešení používá více virtuálních počítačů pro replikaci databází z hlavního uzlu do konfigurovatelného počtu replik. Po vytvoření se cluster dá přidat jako hostitelský server MySQL Azure Stack hub a potom můžou uživatelé vytvářet vysoce dostupné databáze MySQL.

> [!IMPORTANT]
> Položka **MySQL s replikací** Azure Stack Marketplace možná není dostupná pro všechna prostředí Azure Cloud Subscriptions. Než se pokusíte použít zbytek tohoto kurzu, ověřte, že je položka Marketplace v předplatném dostupná.

Naučíte se:

> [!div class="checklist"]
> * Vytvořte serverový cluster MySQL z položek Marketplace.
> * Vytvořte hostitelský server MySQL Azure Stack hub.
> * Vytvořte databázi MySQL s vysokou dostupností.

Vytvoří se cluster MySQL serveru se třemi virtuálními počítači a nakonfiguruje se s využitím dostupných položek Azure Stack Marketplace.

Než začnete, ujistěte se, že [poskytovatel prostředků serveru MySQL](azure-stack-mysql-resource-provider-deploy.md) byl úspěšně nainstalován a že jsou v Azure Stack Marketplace k dispozici následující položky:

> [!IMPORTANT]
> K vytvoření clusteru MySQL se vyžadují všechny následující.

- [MySQL s replikací](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/bitnami.mysql-cluster): Jedná se o šablonu řešení Bitnami, která bude použita pro nasazení clusteru MySQL.
- [Debian 8 "Jessie"](https://azuremarketplace.microsoft.com/marketplace/apps/credativ.Debian): Debian 8 "Jessie" s jádry s nevydanými porty pro Microsoft Azure poskytované credativ. Debian GNU/Linux je jednou z nejoblíbenějších distribucí systému Linux.
- [Vlastní skript pro linux 2,0](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft.custom-script-linux?tab=Overview): rozšíření vlastních skriptů je nástroj, který umožňuje provádět úlohy přizpůsobení virtuálních počítačů po zřízení virtuálního počítače. Když se toto rozšíření přidá do virtuálního počítače, může stáhnout skripty z Azure Storage a spustit je na virtuálním počítači. Úkoly rozšíření vlastních skriptů je také možné automatizovat pomocí rutin Azure PowerShell a rozhraní příkazového řádku Azure pro více platforem (xPlat CLI).
- Přístup k virtuálnímu počítači 1.4.7 rozšíření pro Linux: rozšíření pro přístup k VIRTUÁLNÍm počítačům umožňuje resetovat heslo, klíč SSH nebo konfigurace SSH, abyste mohli znovu získat přístup k vašemu VIRTUÁLNÍmu počítači. Můžete také přidat nového uživatele pomocí hesla nebo klíče SSH nebo odstranit uživatele pomocí tohoto rozšíření. Toto rozšíření cílí na virtuální počítače Linux.

Další informace o přidávání položek do webu Azure Stack Marketplace najdete v tématu [přehled Azure Stack Marketplace](azure-stack-marketplace.md).

Také budete potřebovat klienta SSH [, jako je](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html) například výstup pro přihlášení k virtuálním počítačům se systémem Linux po jejich nasazení.

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
   [!INCLUDE [azs-admin-portal](../includes/azs-user-portal.md)]

2. Pokud ještě nebyla přiřazena žádná předplatná, vyberte z řídicího panelu **získat odběr** . V okně zadejte název předplatného a potom vyberte nabídku. Doporučuje se, abyste zachovali nasazení clusteru MySQL ve vlastním předplatném, abyste zabránili nechtěnému odebrání.

3. Vyberte **\+** **vytvořit prostředek** > **COMPUTE**a pak **MySQL s replikací**.

   ![Nasazení vlastních šablon v centru Azure Stack](media/azure-stack-tutorial-mysqlrp/img1.png)

4. Poskytněte základní informace o nasazení na stránce **základy** . Zkontrolujte výchozí hodnoty a podle potřeby změňte nastavení a vyberte **OK**.

    Minimálně zadejte následující informace:

   - Název nasazení (výchozí hodnota je mymysql).
   - Kořenové heslo aplikace Zadejte alfanumerické heslo obsahující 12 znaků **bez zvláštních znaků**.
   - Název aplikační databáze (výchozí hodnota je Bitnami).
   - Počet virtuálních počítačů repliky databáze MySQL k vytvoření (výchozí hodnota je 2).
   - Vyberte předplatné, které chcete použít.
   - Vyberte skupinu prostředků, kterou chcete použít, nebo vytvořte novou.
   - Vyberte umístění (výchozí nastavení je místní pro ASDK).

     ![Základy nasazení – vytvoření MySQL s replikací](media/azure-stack-tutorial-mysqlrp/img2.png)

5. Na stránce **Konfigurace prostředí** zadejte následující informace a pak vyberte **OK**:

   - Heslo nebo veřejný klíč SSH pro ověřování pomocí protokolu Secure Shell (SSH). Pokud použijete heslo, musí obsahovat písmena, číslice a **může** obsahovat speciální znaky.
   - Velikost virtuálního počítače (výchozí nastavení je Standard D1 v2 VM).
   - Velikost datového disku v GB

     ![Konfigurace prostředí – vytvoření MySQL s replikací](media/azure-stack-tutorial-mysqlrp/img3.png)

6. Projděte si **Souhrn**nasazení. Volitelně si můžete stáhnout vlastní šablonu a parametry a pak vybrat **OK**.

   ![Shrnutí – vytvoření MySQL s replikací](media/azure-stack-tutorial-mysqlrp/img4.png)

7. Vyberte **vytvořit** na stránce **koupit** a spusťte nasazení.

   ![Koupit stránku – vytvoření MySQL s replikací](media/azure-stack-tutorial-mysqlrp/img5.png)

    > [!NOTE]
    > Nasazení bude trvat přibližně hodinu. Než budete pokračovat, ujistěte se, že nasazení bylo dokončeno a cluster MySQL byl zcela nakonfigurován.

8. Po úspěšném dokončení všech nasazení zkontrolujte položky skupiny prostředků a vyberte položku veřejné IP adresy **mysqlip** . Poznamenejte si veřejnou IP adresu a celý plně kvalifikovaný název domény veřejné IP adresy clusteru.

    Tuto IP adresu budete muset zadat do operátoru centra Azure Stack, aby mohli vytvořit hostitelský server MySQL s využitím tohoto clusteru MySQL.

### <a name="create-a-network-security-group-rule"></a>Vytvoření pravidla skupiny zabezpečení sítě

Ve výchozím nastavení není pro MySQL na hostitelském virtuálním počítači nakonfigurován žádný veřejný přístup. Aby mohl poskytovatel prostředků MySQL Azure Stack připojit a spravovat cluster MySQL, je nutné vytvořit pravidlo skupiny zabezpečení příchozí sítě (NSG).

1. Na portálu pro správu přejdete do skupiny prostředků vytvořené při nasazení clusteru MySQL a vybráním skupiny zabezpečení sítě (**výchozí-podsíť-SG**):

   ![Vybrat skupinu zabezpečení sítě na portálu pro správu centra Azure Stack](media/azure-stack-tutorial-mysqlrp/img6.png)

2. Vyberte **příchozí pravidla zabezpečení** a pak vyberte **Přidat**.

    Do pole název **cílového portu** zadejte **3306** a v poli **název** a **Popis** volitelně zadejte popis.

   ![open (otevírá)](media/azure-stack-tutorial-mysqlrp/img7.png)

3. Výběrem **Přidat** zavřete dialog příchozí pravidlo zabezpečení.

### <a name="configure-external-access-to-the-mysql-cluster"></a>Konfigurace externího přístupu ke clusteru MySQL

Než bude možné cluster MySQL přidat jako hostitele serveru MySQL Azure Stack hub, musí být povolený externí přístup.

1. Pomocí klienta SSH (Tento [příklad používá výstup](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html)) se přihlaste k primárnímu počítači MySQL z počítače, který má přístup k veřejné IP adrese. Primární název virtuálního počítače MySQL obvykle končí na **0** a má přiřazenou veřejnou IP adresu.

    Použijte veřejnou IP adresu a přihlaste se k virtuálnímu počítači pomocí uživatelského jména **Bitnami** a hesla aplikace, které jste vytvořili dřív bez speciálních znaků.

   ![LinuxLogin](media/azure-stack-tutorial-mysqlrp/bitnami1.png)

2. V okně Klient SSH pomocí následujícího příkazu zkontrolujte, že je služba Bitnami aktivní a spuštěná. Po zobrazení výzvy zadejte heslo Bitnami:

   `sudo service bitnami status`

   ![Ověřit službu Bitnami](media/azure-stack-tutorial-mysqlrp/bitnami2.png)

3. Vytvořte uživatelský účet vzdáleného přístupu, který má hostitelský server služby Azure Stack hub MySQL používat pro připojení k MySQL, a pak ukončete klienta SSH.

    Spusťte následující příkazy, abyste se přihlásili do MySQL jako kořenový adresář pomocí dříve vytvořeného kořenového hesla. Vytvořte nového uživatele správce a nahraďte *\<username\>* a *\<hesla\>* třeba pro vaše prostředí. V tomto příkladu je vytvořeným uživatelem název **sqlsa** a používá se silné heslo:

   ```mysql
   mysql -u root -p
   create user <username>@'%' identified by '<password>';
   grant all privileges on *.* to <username>@'%' with grant option;
   flush privileges;
   ```

   ![Vytvořit administrativního uživatele](media/azure-stack-tutorial-mysqlrp/bitnami3.png)

4. Zaznamenejte nové informace o uživateli MySQL.

    Toto uživatelské jméno a heslo budete muset zadat spolu s veřejnou IP adresou nebo úplným plně kvalifikovaným názvem domény veřejné IP adresy pro cluster, do operátoru centra Azure Stack, aby mohli vytvořit hostitelský server MySQL pomocí tohoto clusteru MySQL.

## <a name="create-an-azure-stack-hub-mysql-hosting-server"></a>Vytvoření hostitelského serveru MySQL Azure Stack hub

Po vytvoření a správné konfiguraci clusteru serveru MySQL musí operátor centra Azure Stack vytvořit hostitelský server Azure Stack hub pro MySQL, aby bylo možné uživatelům vytvořit databáze.

Pokud jste vytvořili skupinu prostředků clusteru MySQL (**mysqlip**), ujistěte se, že používáte veřejnou IP adresu nebo plně kvalifikovaný název domény pro veřejnou IP adresu prvního virtuálního počítače clusteru MySQL zaznamenanou dříve. Kromě toho musí operátor znát přihlašovací údaje pro ověření serveru MySQL, které jste vytvořili pro vzdálený přístup k databázi clusteru MySQL.

> [!NOTE]
> Tento krok je potřeba spustit z portálu pro správu centra Azure Stack pomocí operátoru centra Azure Stack.

Pomocí veřejné IP adresy clusteru MySQL a přihlašovacích údajů pro ověřování MySQL teď může operátor centra Azure Stack [vytvořit hostitelský server MySQL pomocí nového clusteru MySQL](azure-stack-mysql-resource-provider-hosting-servers.md#connect-to-a-mysql-hosting-server).

Také se ujistěte, že jste vytvořili plány a nabídky, aby bylo možné vytvářet databáze MySQL pro uživatele. Operátor bude muset přidat službu **Microsoft. MySqlAdapter** do plánu a vytvořit novou kvótu specifickou pro vysoce dostupné databáze. Další informace o vytváření plánů najdete v tématu [Přehled služeb, plánů, nabídek a předplatných](service-plan-offer-subscription-overview.md).

> [!TIP]
> Služba **Microsoft. MySqlAdapter** nebude k dispozici pro přidání do plánů, dokud není [nasazen poskytovatel prostředků serveru MySQL](azure-stack-mysql-resource-provider-deploy.md).

## <a name="create-a-highly-available-mysql-database"></a>Vytvoření vysoce dostupné databáze MySQL

Jakmile se cluster MySQL vytvoří a nakonfiguruje a přidá se jako hostitelský server služby Azure Stack hub MySQL pomocí operátoru centra Azure Stack, uživatel s předplatným, včetně možností databáze MySQL serveru, může vytvořit vysoce dostupné databáze MySQL podle postupujte podle kroků v této části.

> [!NOTE]
> Tyto kroky spusťte z uživatelského portálu Azure Stackového centra jako uživatel tenanta s předplatným, které poskytuje možnosti serveru MySQL (Microsoft. MySQLAdapter Service).

1. 
   [!INCLUDE [azs-user-portal](../includes/azs-user-portal.md)]

2. Vyberte **\+** **vytvořit prostředek** > **úložiště dat \+** a potom **databázi MySQL**.

    Zadejte požadované informace o vlastnostech databáze, včetně názvu, kolace, předplatného, které chcete použít, a umístění, které se má použít pro nasazení.

    ![Vytvoření databáze MySQL na portálu Azure Stack User Portal](./media/azure-stack-tutorial-mysqlrp/createdb1.png)

3. Vyberte **SKU** a pak zvolte příslušnou SKU hostitelského serveru MySQL, které chcete použít. V tomto příkladu operátor centra Azure Stack vytvořil SKU **MySQL-ha** , aby podporoval vysokou dostupnost databází clusteru MySQL.

   ![Vybrat SKU na portálu Azure Stack User Portal](./media/azure-stack-tutorial-mysqlrp/createdb2.png)

4. Vyberte **přihlašovací** > **vytvořit nové přihlášení** a pak zadat přihlašovací údaje pro ověřování MySQL, které se mají použít pro novou databázi. Po dokončení vyberte **OK** a pak **vytvořte** a zahajte proces nasazení databáze.

   ![Přidat přihlášení na portále User Portal pro Azure Stack](./media/azure-stack-tutorial-mysqlrp/createdb3.png)

5. Po úspěšném dokončení nasazení databáze MySQL zkontrolujte vlastnosti databáze a zjistěte připojovací řetězec, který se má použít pro připojení k nové vysoce dostupné databázi.

   ![Zobrazení připojovacího řetězce v portálu pro uživatele centra Azure Stack](./media/azure-stack-tutorial-mysqlrp/createdb4.png)

## <a name="next-steps"></a>Další kroky

[Aktualizace poskytovatele prostředků MySQL](azure-stack-mysql-resource-provider-update.md)
