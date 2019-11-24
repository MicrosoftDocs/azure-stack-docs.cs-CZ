---
title: Nabízet vysoce dostupné databáze SQL v Azure Stack
description: Naučte se, jak vytvořit hostitelský počítač poskytovatele prostředků SQL Server a vysoce dostupné databáze SQL AlwaysOn s využitím Azure Stack.
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
ms.openlocfilehash: e5866a80367a826dd58aa39109ebbbbd9f2edce6
ms.sourcegitcommit: d159652f50de7875eb4be34c14866a601a045547
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72283350"
---
# <a name="offer-highly-available-sql-databases"></a>Nabízí vysoce dostupné databáze SQL

Jako operátor Azure Stack můžete nakonfigurovat serverové virtuální počítače pro hostování SQL Serverch databází. Po úspěšném vytvoření hostitelského serveru SQL a jeho správě Azure Stack mohou uživatelé, kteří se přihlásili k odběru služeb SQL, snadno vytvořit databáze SQL.

V tomto článku se dozvíte, jak pomocí šablony pro rychlý Start Azure Stack vytvořit [skupinu dostupnosti SQL Server AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017), přidat ji jako Azure Stack hostitelský server SQL a pak vytvořit vysoce DOSTUPNOU databázi SQL.

Co se naučíte:

> [!div class="checklist"]
> * Vytvoření skupiny dostupnosti SQL Server AlwaysOn ze šablony
> * Vytvoření hostitelského serveru s Azure Stack SQL
> * Vytvoření vysoce dostupné databáze SQL

Vytvoří se dvě skupina dostupnosti virtuálního počítače SQL Server AlwaysOn a nakonfiguruje se pomocí dostupných Azure Stack položek Marketplace. 

Než začnete, ujistěte se, že [poskytovatel prostředků SQL Server](azure-stack-sql-resource-provider-deploy.md) byl úspěšně nainstalován a že na webu Azure Stack Marketplace jsou k dispozici následující položky:

> [!IMPORTANT]
> Všechny následující jsou vyžadovány pro použití šablony Azure Stack pro rychlý Start.

- Image na webu [Windows Server 2016 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/MicrosoftWindowsServer.WindowsServer)
- SQL Server 2016 SP1 nebo SP2 (Enterprise, Standard nebo Developer) na obrázku serveru Windows Server 2016. Tento článek používá bitovou kopii [SQL Server 2016 SP2 Enterprise v systému Windows Server 2016 na](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoftsqlserver.sql2016sp2-ws2016) webu Marketplace.
- [SQL Server rozšíření IaaS](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) verze 1.2.30 nebo vyšší. Rozšíření SQL IaaS nainstaluje nezbytné komponenty, které jsou vyžadovány na webu Marketplace SQL Server pro všechny verze systému Windows. Umožňuje konfiguraci nastavení specifického pro SQL na virtuálních počítačích SQL. Pokud rozšíření není nainstalované na místním webu Marketplace, zřizování SQL se nezdaří.
- [Rozšíření vlastních skriptů pro Windows](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.CustomScriptExtension) verze 1.9.1 nebo novější. Rozšíření vlastních skriptů je nástroj, který se dá použít k automatickému spuštění úloh přizpůsobení virtuálních počítačů po nasazení.
- [Konfigurace požadovaného stavu PowerShellu (DSC)](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.DSC-arm) verze 2.76.0.0 nebo vyšší. DSC je platforma pro správu v prostředí Windows PowerShell, která umožňuje nasazovat a spravovat konfigurační data pro softwarové služby a spravovat prostředí, ve kterém se tyto služby spouštějí.

Další informace o přidávání položek do webu Azure Stack Marketplace najdete v tématu [přehled Azure Stack Marketplace](azure-stack-marketplace.md).

## <a name="create-a-sql-server-alwayson-availability-group"></a>Vytvoření skupiny dostupnosti SQL Server AlwaysOn
Pomocí kroků v této části můžete nasadit skupinu dostupnosti SQL Server AlwaysOn pomocí [šablony SQL-2016-AlwaysOn Azure Stack pro rychlý Start](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2016-alwayson). Tato šablona nasadí dvě instance SQL Server Enterprise, Standard nebo vývojáře ve skupině dostupnosti Always On. Vytvoří následující prostředky:

- Skupina zabezpečení sítě
- Virtuální síť
- Čtyři účty úložiště (jeden pro službu Active Directory (AD), jednu pro SQL, jednu pro určující sdílenou složku a jednu pro diagnostiku virtuálního počítače)
- Čtyři veřejné IP adresy (jeden pro AD, dva pro každý virtuální počítač SQL a jeden pro veřejný Nástroj pro vyrovnávání zatížení, vázaný na SQL AlwaysOn Listener)
- Jeden externí nástroj pro vyrovnávání zatížení pro virtuální počítače SQL s veřejnou IP adresou, která je vázaná na naslouchací proces SQL AlwaysOn
- Jeden virtuální počítač (Windows Server 2016) nakonfigurovaný jako řadič domény pro novou doménovou strukturu s jednou doménou
- Dva virtuální počítače (Windows Server 2016) nakonfigurované s SQL Server 2016 SP1 nebo SP2 Enterprise, Standard nebo Developer Edition a CLUSTERED. Musí se jednat o image na Marketplace.
- Jeden virtuální počítač (Windows Server 2016) nakonfigurovaný jako určující sdílená složka pro cluster
- Jedna skupina dostupnosti, která obsahuje virtuální počítače s kopií clusteru a sdílené složky pro soubory SQL  

1. 
   [!INCLUDE [azs-admin-portal](../includes/azs-admin-portal.md)]

2. Vyberte **\+** **vytvořit prostředek** > **vlastní**a potom **template Deployment**.

   ![Nasazení vlastní šablony](media/azure-stack-tutorial-sqlrp/1.png)


3. V okně **vlastní nasazení** vyberte **Upravit šablonu** > šablonu pro **rychlý Start** a pak pomocí rozevíracího seznamu dostupných vlastních šablon vyberte šablonu **SQL-2016-AlwaysOn** , klikněte na **OK**a pak na **Uložit**.

   [![](media/azure-stack-tutorial-sqlrp/2-sm.PNG "Vybrat šablonu pro rychlý Start")](media/azure-stack-tutorial-sqlrp/2-lg.PNG#lightbox)

4. V okně **vlastní nasazení** vyberte **Upravit parametry** a zkontrolujte výchozí hodnoty. Upravte hodnoty podle potřeby tak, aby poskytovaly všechny požadované informace o parametrech, a pak klikněte na **OK**.<br><br> Minimálně:

    - Zadejte složitá hesla pro parametry ADMINPASSWORD, SQLSERVERSERVICEACCOUNTPASSWORD a SQLAUTHPASSWORD.
    - Zadejte příponu DNS pro zpětné vyhledávání u všech malých písmen pro parametr DNSSUFFIX (**azurestack. external** pro instalace ASDK).
    
   [![](media/azure-stack-tutorial-sqlrp/3-sm.PNG "Upravit parametry vlastního nasazení")](media/azure-stack-tutorial-sqlrp/3-lg.PNG#lightbox)

5. V okně **vlastní nasazení** zvolte předplatné, které chcete použít, a vytvořte novou skupinu prostředků nebo vyberte existující skupinu prostředků pro vlastní nasazení.<br><br> V dalším kroku vyberte umístění skupiny prostředků (**místní** pro instalace ASDK) a potom klikněte na **vytvořit**. Vlastní nastavení nasazení se ověří a pak se nasazení spustí.

    [![](media/azure-stack-tutorial-sqlrp/4-sm.PNG "Vytvořit vlastní nasazení")](media/azure-stack-tutorial-sqlrp/4-lg.PNG#lightbox)


6. Na portálu pro správu vyberte **skupiny prostředků** a potom název skupiny prostředků, kterou jste vytvořili pro vlastní nasazení (**Skupina prostředků** pro tento příklad). Zobrazte stav nasazení, aby se zajistilo, že se všechna nasazení úspěšně dokončila.<br><br>V dalším kroku zkontrolujte položky skupiny prostředků a vyberte **SQLPIPsql\<název skupiny prostředků\>** položka veřejné IP adresy. Poznamenejte si veřejnou IP adresu a celý plně kvalifikovaný název domény veřejné IP adresy nástroje pro vyrovnávání zatížení. Tuto možnost budete muset poskytnout operátorovi Azure Stack, aby mohli vytvořit hostitelský server SQL, který využívá tuto skupinu dostupnosti SQL AlwaysOn.

   > [!NOTE]
   > Dokončení nasazení šablony bude trvat několik hodin.

   ![Vlastní nasazení je hotové.](./media/azure-stack-tutorial-sqlrp/5.png)

### <a name="enable-automatic-seeding"></a>Povolit automatické osazení
Po úspěšném nasazení šablony a konfiguraci skupiny dostupnosti AlwaysON serveru SQL je nutné povolit [Automatické osazení](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) u každé instance SQL Server ve skupině dostupnosti. 

Když vytvoříte skupinu dostupnosti s automatickým osazením, SQL Server automaticky vytvoří sekundární repliky pro každou databázi ve skupině bez jakýchkoli dalších ručních zásahů nutných k zajištění vysoké dostupnosti databází AlwaysOn.

Pomocí těchto příkazů SQL nakonfigurujte automatické osazení pro skupinu dostupnosti AlwaysOn. Nahraďte \<InstanceName\> názvem primární instance SQL Server název a < availability_group_name > s názvem skupiny dostupnosti AlwaysOn podle potřeby. 

Na primární instanci SQL:

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON '<InstanceName>'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

>  ![Primární skript instance SQL](./media/azure-stack-tutorial-sqlrp/sql1.png)

Sekundární instance SQL:

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```
>  ![Sekundární skript instance SQL](./media/azure-stack-tutorial-sqlrp/sql2.png)

### <a name="configure-contained-database-authentication"></a>Konfigurovat ověřování databáze s omezením
Před přidáním databáze s omezením do skupiny dostupnosti zajistěte, aby byla v každé instanci serveru, která hostuje repliku dostupnosti pro skupinu dostupnosti, nastavená možnost Server pro ověřování databáze s omezením na hodnotu 1. Další informace najdete v tématu věnovaném [ověřování databáze](https://docs.microsoft.com/sql/database-engine/configure-windows/contained-database-authentication-server-configuration-option?view=sql-server-2017).

Pomocí těchto příkazů nastavte možnost Server pro ověřování databáze pro každou instanci SQL Server ve skupině dostupnosti:

  ```sql
  EXEC sp_configure 'contained database authentication', 1
  GO
  RECONFIGURE
  GO
  ```
>  ![Nastavit omezení databáze k ověřování](./media/azure-stack-tutorial-sqlrp/sql3.png)

## <a name="create-an-azure-stack-sql-hosting-server"></a>Vytvoření hostitelského serveru s Azure Stack SQL
Po vytvoření a správném nakonfigurování skupiny dostupnosti SQL Server AlwayOn musí Azure Stack operátor vytvořit Azure Stack hostitelský server SQL, aby mohli uživatelé vytvořit databáze. 

Nezapomeňte použít veřejnou IP adresu nebo plně kvalifikovaný název domény pro veřejnou IP adresu služby SQL Load Balancer zaznamenanou dříve, když se vytvořila skupina prostředků skupiny dostupnosti SQL AlwaysOn (**SQLPIPsql\<název skupiny prostředků\>** ). Kromě toho potřebujete znát SQL Server přihlašovací údaje pro ověřování používané pro přístup k instancím SQL ve skupině dostupnosti AlwaysOn.

> [!NOTE]
> Tento krok je nutné spustit z portálu pro správu Azure Stack pomocí operátoru Azure Stack.

Pomocí služby Vyrovnávání zatížení skupiny dostupnosti SQL AlwaysOn a přihlašovacích údajů pro ověřování serveru SQL teď může operátor Azure Stack [vytvořit hostitelský server SQL pomocí skupiny dostupnosti SQL AlwaysOn](azure-stack-sql-resource-provider-hosting-servers.md#provide-high-availability-using-sql-always-on-availability-groups). 

Také se ujistěte, že jste vytvořili plány a nabídky pro uživatele, kteří budou mít k dispozici vytváření databází SQL AlwaysOn. Operátor bude muset přidat službu **Microsoft. SqlAdapter** do plánu a vytvořit novou kvótu specifickou pro vysoce dostupné databáze. Další informace o vytváření plánů najdete v tématu [Přehled služeb, plánů, nabídek a předplatných](service-plan-offer-subscription-overview.md).

> [!TIP]
> Služba **Microsoft. SqlAdapter** nebude dostupná pro přidání do plánů, dokud nebude [nasazený poskytovatel prostředků SQL Server](azure-stack-sql-resource-provider-deploy.md).

## <a name="create-a-highly-available-sql-database"></a>Vytvoření vysoce dostupné databáze SQL
Po vytvoření, konfiguraci a přidání skupiny dostupnosti SQL AlwaysOn jako Azure Stack hostitelského serveru SQL pomocí operátoru Azure Stack může uživatel s předplatným, včetně SQL Serverch databázových služeb, vytvářet databáze SQL podporující Funkce AlwaysOn podle kroků v této části. 

> [!NOTE]
> Tyto kroky spusťte na portálu Azure Stack User Portal jako uživatel tenanta s předplatným, které poskytuje možnosti služby SQL Server (Microsoft. SQLAdapter Service).

1. 
   [!INCLUDE [azs-user-portal](../includes/azs-user-portal.md)]

2. Vyberte **\+** **vytvořit prostředek** > **úložiště \+ data**a pak **SQL Database**.<br><br>Zadejte požadované informace o vlastnostech databáze včetně názvu, kolace, maximální velikosti a předplatného, skupiny prostředků a umístění, které se má pro nasazení použít. 

   ![Vytvoření databáze SQL](./media/azure-stack-tutorial-sqlrp/createdb1.png)

3. Vyberte **SKU** a pak zvolte odpovídající SKU hostitelského serveru SQL, které se má použít. V tomto příkladu byl operátor Azure Stack vytvořen SKU **Enterprise-ha** , aby podporoval vysokou dostupnost pro skupiny dostupnosti SQL AlwaysOn.

   ![Vybrat SKU](./media/azure-stack-tutorial-sqlrp/createdb2.png)

4. Vyberte **přihlašovací** > **vytvořit nové přihlášení** a pak zadat přihlašovací údaje pro ověřování SQL, které se mají použít pro novou databázi. Po dokončení klikněte na **OK** a pak na **vytvořit** a zahajte proces nasazení databáze.

   ![Vytvořit přihlašovací údaje](./media/azure-stack-tutorial-sqlrp/createdb3.png)

5. Po úspěšném dokončení nasazení databáze SQL zkontrolujte vlastnosti databáze a zjistěte připojovací řetězec, který se má použít pro připojení k nové vysoce dostupné databázi. 

   ![Zobrazit připojovací řetězec](./media/azure-stack-tutorial-sqlrp/createdb4.png)




## <a name="next-steps"></a>Další kroky

[Aktualizace poskytovatele prostředků SQL](azure-stack-sql-resource-provider-update.md)
