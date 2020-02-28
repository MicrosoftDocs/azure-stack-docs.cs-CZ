---
title: Vytváření vysoce dostupných databází SQL
titleSuffix: Azure Stack Hub
description: Naučte se, jak vytvořit hostitelský počítač poskytovatele prostředků SQL Server a vysoce dostupné databáze SQL AlwaysOn pomocí centra Azure Stack.
author: BryanLa
ms.topic: article
ms.date: 10/07/2019
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 10/23/2019
ms.openlocfilehash: bd62be6a7a2990a7a405dd5c5e1ff44e64007b6f
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77696791"
---
# <a name="create-highly-available-sql-databases-with-azure-stack-hub"></a>Vytváření vysoce dostupných databází SQL pomocí centra Azure Stack

Jako operátor centra Azure Stack můžete nakonfigurovat virtuální počítače serveru pro hostování SQL Serverch databází. Po vytvoření a správě hostitelského serveru SQL pomocí centra Azure Stack mohou uživatelé, kteří se přihlásili k odběru služeb SQL, snadno vytvořit databáze SQL.

V tomto článku se dozvíte, jak pomocí šablony pro rychlý Start centra Azure Stack vytvořit [skupinu dostupnosti SQL Server AlwaysOn](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017), přidat ji Azure Stack jako hostitelský server SQL centra a pak vytvořit vysoce DOSTUPNOU databázi SQL.

Naučíte se:

> [!div class="checklist"]
> * Vytvořte skupinu dostupnosti SQL Server AlwaysOn ze šablony.
> * Vytvořte Azure Stack hostitelský server SQL hub.
> * Vytvořte databázi SQL s vysokou dostupností.

Vytvoří se dvě skupina dostupnosti virtuálního počítače SQL Server AlwaysOn a nakonfiguruje se pomocí dostupných Azure Stack položek Marketplace.

Než začnete, ujistěte se, že [poskytovatel prostředků SQL Server](azure-stack-sql-resource-provider-deploy.md) byl úspěšně nainstalován a zda jsou na Azure Stack Marketplace k dispozici následující položky:

> [!IMPORTANT]
> K použití šablony pro rychlý Start centra Azure Stack se vyžadují všechny níže uvedené.

- Image na webu [Windows Server 2016 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/MicrosoftWindowsServer.WindowsServer)
- SQL Server 2016 SP1 nebo SP2 (Enterprise, Standard nebo Developer) na obrázku serveru Windows Server 2016. Tento článek používá bitovou kopii [SQL Server 2016 SP2 Enterprise v systému Windows Server 2016 na](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoftsqlserver.sql2016sp2-ws2016) webu Marketplace.
- [SQL Server rozšíření IaaS](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension) verze 1.2.30 nebo vyšší. Rozšíření SQL IaaS nainstaluje nezbytné komponenty, které jsou vyžadovány na webu Marketplace SQL Server pro všechny verze systému Windows. Umožňuje konfiguraci nastavení specifického pro SQL na virtuálních počítačích SQL (virtuálních počítačů). Pokud rozšíření není nainstalované na místním webu Marketplace, zřizování SQL se nezdaří.
- [Rozšíření vlastních skriptů pro Windows](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.CustomScriptExtension) verze 1.9.1 nebo novější. Rozšíření vlastních skriptů je nástroj, který se dá použít k automatickému spuštění úloh přizpůsobení virtuálních počítačů po nasazení.
- [Konfigurace požadovaného stavu PowerShellu (DSC)](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.DSC-arm) verze 2.76.0.0 nebo vyšší. DSC je platforma pro správu v prostředí Windows PowerShell, která umožňuje nasazovat a spravovat konfigurační data pro softwarové služby. Platforma také spravuje prostředí, ve kterém se tyto služby spouštějí.

Další informace o přidávání položek do webu Azure Stack Marketplace najdete v tématu [Přehled centra Azure Stack na webu Marketplace](azure-stack-marketplace.md).

## <a name="create-a-sql-server-alwayson-availability-group"></a>Vytvoření skupiny dostupnosti SQL Server AlwaysOn

Pomocí kroků v této části nasaďte SQL Server skupinu dostupnosti AlwaysOn pomocí [šablony pro rychlý Start centra SQL-2016-alwayson Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2016-alwayson). Tato šablona nasadí dvě instance SQL Server Enterprise, Standard nebo vývojáře ve skupině dostupnosti Always On. Vytvoří následující prostředky:

- Skupina zabezpečení sítě.
- Virtuální síť.
- Čtyři účty úložiště (jeden pro službu Active Directory (AD), jednu pro SQL, jednu pro určující sdílenou složku a jeden pro diagnostiku virtuálních počítačů).
- Čtyři veřejné IP adresy (jeden pro AD, dva pro každý virtuální počítač SQL a jeden pro veřejný Nástroj pro vyrovnávání zatížení, vázaný na SQL AlwaysOn Listener).
- Jeden externí nástroj pro vyrovnávání zatížení pro virtuální počítače SQL s veřejnou IP adresou, která je vázaná na naslouchací proces SQL AlwaysOn.
- Jeden virtuální počítač (Windows Server 2016) nakonfigurovaný jako řadič domény pro novou doménovou strukturu s jednou doménou.
- Dva virtuální počítače (Windows Server 2016) nakonfigurované s SQL Server 2016 SP1 nebo SP2 Enterprise, Standard nebo Developer Edition a CLUSTERED. Musí se jednat o image na Marketplace.
- Jeden virtuální počítač (Windows Server 2016) nakonfigurovaný jako určující sdílená složka pro cluster.
- Jedna skupina dostupnosti, která obsahuje virtuální počítače s kopií clusteru a sdílené složky pro SQL.

1. 
   [!INCLUDE [azs-admin-portal](../includes/azs-admin-portal.md)]

2. Vyberte **\+** **vytvořit prostředek** > **vlastní**a potom **template Deployment**.

   ![Nasazení vlastních šablon na portálu Azure Stack hub správce](media/azure-stack-tutorial-sqlrp/1.png)

3. V okně **vlastní nasazení** vyberte **Upravit šablonu** > šablonu pro **rychlý Start** a pak pomocí rozevíracího seznamu dostupných vlastních šablon vyberte šablonu **SQL-2016-AlwaysOn** . Vyberte **OK**a pak **Uložit**.

   [![Upravit šablonu na portálu pro správu centra Azure Stack](media/azure-stack-tutorial-sqlrp/2-sm.PNG "Vybrat šablonu pro rychlý Start")](media/azure-stack-tutorial-sqlrp/2-lg.PNG#lightbox)

4. V okně **vlastní nasazení** vyberte **Upravit parametry** a zkontrolujte výchozí hodnoty. Upravte hodnoty podle potřeby tak, aby poskytovaly všechny požadované informace o parametrech, a pak vyberte **OK**.

    Minimálně:
    - Zadejte složitá hesla pro parametry ADMINPASSWORD, SQLSERVERSERVICEACCOUNTPASSWORD a SQLAUTHPASSWORD.
    - Zadejte příponu DNS pro zpětné vyhledávání u všech malých písmen pro parametr DNSSUFFIX (**azurestack. external** pro instalace ASDK).
    
   [![Upravit parametry na portálu pro správu centra Azure Stack](media/azure-stack-tutorial-sqlrp/3-sm.PNG "Upravit parametry vlastního nasazení")](media/azure-stack-tutorial-sqlrp/3-lg.PNG#lightbox)

5. V okně **vlastní nasazení** zvolte předplatné, které chcete použít, a vytvořte novou skupinu prostředků nebo vyberte existující skupinu prostředků pro vlastní nasazení.

    V dalším kroku vyberte umístění skupiny prostředků (**místní** pro instalace ASDK) a potom klikněte na **vytvořit**. Vlastní nastavení nasazení se ověří a pak se nasazení spustí.

    [![Výběr předplatného na portálu pro správu centra Azure Stack](media/azure-stack-tutorial-sqlrp/4-sm.PNG "Vytvořit vlastní nasazení")](media/azure-stack-tutorial-sqlrp/4-lg.PNG#lightbox)

6. Na portálu pro správu vyberte **skupiny prostředků** a potom název skupiny prostředků, kterou jste vytvořili pro vlastní nasazení (**Skupina prostředků** pro tento příklad). Zobrazte stav nasazení, aby se zajistilo, že se všechna nasazení úspěšně dokončila.
    
    V dalším kroku zkontrolujte položky skupiny prostředků a vyberte **SQLPIPsql\<název skupiny prostředků\>** položka veřejné IP adresy. Poznamenejte si veřejnou IP adresu a celý plně kvalifikovaný název domény veřejné IP adresy nástroje pro vyrovnávání zatížení. Tuto možnost musíte poskytnout operátorovi centra Azure Stack, aby mohli vytvořit hostitelský server SQL, který využívá tuto skupinu dostupnosti SQL AlwaysOn.

   > [!NOTE]
   > Dokončení nasazení šablony bude trvat několik hodin.

   ![Vlastní nasazení bylo dokončeno na portálu pro správu centra Azure Stack](./media/azure-stack-tutorial-sqlrp/5.png)

### <a name="enable-automatic-seeding"></a>Povolit automatické osazení

Po úspěšném nasazení šablony a konfiguraci skupiny dostupnosti AlwaysON serveru SQL je nutné povolit [Automatické osazení](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) u každé instance SQL Server ve skupině dostupnosti.

Když vytvoříte skupinu dostupnosti s automatickým osazením, SQL Server automaticky vytvoří sekundární repliky pro každou databázi ve skupině bez nutnosti jakéhokoli jiného ručního zásahu. Tato míra zajišťuje vysokou dostupnost databází AlwaysOn.

Pomocí těchto příkazů SQL nakonfigurujte automatické osazení pro skupinu dostupnosti AlwaysOn. V případě potřeby nahraďte `<InstanceName>` primární instancí SQL Server název a `<availability_group_name>` s názvem skupiny dostupnosti AlwaysOn.

Na primární instanci SQL:

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON '<InstanceName>'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

![Primární skript instance SQL](./media/azure-stack-tutorial-sqlrp/sql1.png)

Sekundární instance SQL:

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```

![Sekundární skript instance SQL](./media/azure-stack-tutorial-sqlrp/sql2.png)

### <a name="configure-contained-database-authentication"></a>Konfigurovat ověřování databáze s omezením

Před přidáním databáze s omezením do skupiny dostupnosti zajistěte, aby byla v každé instanci serveru, která hostuje repliku dostupnosti pro skupinu dostupnosti, nastavená možnost Server pro ověřování databáze s omezením na hodnotu 1. Další informace najdete v tématu věnovaném [ověřování databáze](https://docs.microsoft.com/sql/database-engine/configure-windows/contained-database-authentication-server-configuration-option?view=sql-server-2017).

Pomocí těchto příkazů nastavte možnost Server pro ověřování databáze pro každou instanci SQL Server ve skupině dostupnosti:

  ```sql
  EXEC sp_configure 'contained database authentication', 1
  GO
  RECONFIGURE
  GO
  ```

![Nastavit omezení databáze k ověřování](./media/azure-stack-tutorial-sqlrp/sql3.png)

## <a name="create-an-azure-stack-hub-sql-hosting-server"></a>Vytvoření hostitelského serveru SQL centra Azure Stack

Po vytvoření a správné konfiguraci skupiny dostupnosti SQL Server AlwayOn je potřeba, aby operátor centra Azure Stack vytvořil hostitelský server SQL pro Azure Stack hub. Hostitelský server SQL zpřístupňuje uživatelům další kapacitu k vytváření databází.

Nezapomeňte použít veřejnou IP adresu nebo plně kvalifikovaný název domény pro veřejnou IP adresu služby SQL Load Balancer zaznamenanou dříve, když se vytvořila skupina prostředků skupiny dostupnosti SQL AlwaysOn (**SQLPIPsql\<název skupiny prostředků\>** ). Kromě toho potřebujete znát SQL Server přihlašovací údaje pro ověřování používané pro přístup k instancím SQL ve skupině dostupnosti AlwaysOn.

> [!NOTE]
> Tento krok je potřeba spustit z portálu pro správu centra Azure Stack pomocí operátoru centra Azure Stack.

Pomocí služby Vyrovnávání zatížení skupiny dostupnosti SQL AlwaysOn a přihlašovacích údajů pro ověřování SQL pro vyrovnávání zatížení může operátor centra Azure Stack [vytvořit hostitelský server SQL pomocí skupiny dostupnosti SQL AlwaysOn](azure-stack-sql-resource-provider-hosting-servers.md#provide-high-availability-using-sql-always-on-availability-groups). 

Také se ujistěte, že jste vytvořili plány a nabídky pro uživatele, kteří budou mít k dispozici vytváření databází SQL AlwaysOn. Operátor bude muset přidat službu **Microsoft. SqlAdapter** do plánu a vytvořit novou kvótu specifickou pro vysoce dostupné databáze. Další informace o vytváření plánů najdete v tématu [Přehled služeb, plánů, nabídek a předplatných](service-plan-offer-subscription-overview.md).

> [!TIP]
> Služba **Microsoft. SqlAdapter** nebude k dispozici pro přidání do plánů, dokud není [nasazen poskytovatel prostředků SQL Server](azure-stack-sql-resource-provider-deploy.md).

## <a name="create-a-highly-available-sql-database"></a>Vytvoření vysoce dostupné databáze SQL

Po vytvoření, nakonfigurování a přidání skupiny dostupnosti SQL AlwaysOn Azure Stack jako hostitelského serveru SQL hub pomocí operátoru centra Azure Stack může uživatel s předplatným, včetně SQL Serverch funkcí databáze, vytvářet databáze SQL. Podpora funkcí AlwaysOn. Tyto databáze mohou vytvořit podle kroků v této části.

> [!NOTE]
> Tyto kroky spusťte na portálu Azure Stack User Portal jako uživatel tenanta s předplatným, které poskytuje funkce služby SQL Server (Microsoft. SQLAdapter).

1. 
   [!INCLUDE [azs-user-portal](../includes/azs-user-portal.md)]

2. Vyberte **\+** **vytvořit prostředek** > **úložiště \+ data**a pak **SQL Database**.

    Zadejte požadované informace o vlastnosti databáze. Tyto informace zahrnují název, kolaci, maximální velikost a předplatné, skupinu prostředků a umístění, které se má pro nasazení použít.

   ![Vytvoření databáze SQL na portále User Portal centra Azure Stack](./media/azure-stack-tutorial-sqlrp/createdb1.png)

3. Vyberte **SKU** a pak zvolte odpovídající SKU hostitelského serveru SQL, které se má použít. V tomto příkladu operátor centra Azure Stack vytvořil SKU **Enterprise-ha** , aby podporoval vysokou dostupnost pro skupiny dostupnosti SQL AlwaysOn.

   ![Vybrat SKU na portálu Azure Stack User Portal](./media/azure-stack-tutorial-sqlrp/createdb2.png)

4. Vyberte **přihlašovací** > **vytvořit nové přihlášení** a pak zadat přihlašovací údaje pro ověřování SQL, které se mají použít pro novou databázi. Po dokončení vyberte **OK** a pak **vytvořte** a zahajte proces nasazení databáze.

   ![Vytvoření přihlašovacích údajů v uživatelském portálu Azure Stack hub](./media/azure-stack-tutorial-sqlrp/createdb3.png)

5. Po úspěšném dokončení nasazení databáze SQL zkontrolujte vlastnosti databáze a zjistěte připojovací řetězec, který se má použít pro připojení k nové vysoce dostupné databázi.

   ![Zobrazení připojovacího řetězce v portálu pro uživatele centra Azure Stack](./media/azure-stack-tutorial-sqlrp/createdb4.png)

## <a name="next-steps"></a>Další kroky

[Aktualizace poskytovatele prostředků SQL](azure-stack-sql-resource-provider-update.md)
