---
title: Zpráva k vydání verze pro Azure Stack centra aktualizace 8 App Service
description: Seznamte se s novinkami Update 8 pro App Service v centru Azure Stack, známých problémech a na tom, kde si tuto aktualizaci stáhli.
author: apwestgarth
manager: stefsch
ms.topic: article
ms.date: 02/10/2020
ms.author: anwestg
ms.reviewer: ''
ms.openlocfilehash: daa4cb85ca58a6e638d6d8a1f14ad5e9232f3d72
ms.sourcegitcommit: a76301a8bb54c7f00b8981ec3b8ff0182dc606d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2020
ms.locfileid: "77143671"
---
# <a name="app-service-on-azure-stack-hub-update-8-release-notes"></a>Zpráva k vydání verze pro Azure Stack centra aktualizace 8 App Service

Tyto poznámky k verzi popisují vylepšení a opravy v Azure App Service v centru Azure Stack aktualizace 8 a všech známých problémech. Známé problémy jsou rozděleny do potíží přímo souvisejících s nasazením, procesem aktualizace a problémy se sestavením (po instalaci).

> [!IMPORTANT]
> Před nasazením Azure App Service 1,8 použijte aktualizaci 1910 pro váš Azure Stack integrovaný systém nebo nasaďte nejnovější Azure Stack vývojovou sadu.

## <a name="build-reference"></a>Referenční informace o buildu

App Service číslo buildu Azure Stack centra aktualizace 8 je **86.0.2.13**

### <a name="prerequisites"></a>Předpoklady

Než začnete s nasazením, přečtěte si [dokumentaci před](azure-stack-app-service-before-you-get-started.md) začátkem.

Než začnete s upgradem Azure App Service v Azure Stack na 1,8:

- Ujistěte se, že všechny role jsou připravené ve správě Azure App Service na portálu pro správu Azure Stack.

- Zálohování App Service a hlavních databází:
  - AppService_Hosting;
  - AppService_Metering;
  - Master

- Zálohování sdílené složky obsahu aplikace tenanta

- Zasyndikátte si **rozšíření vlastních skriptů** **1.9.3** z Marketplace.

### <a name="new-features-and-fixes"></a>Nové funkce a opravy

Azure App Service na Azure Stack Update 8 obsahuje následující vylepšení a opravy:

- Aktualizace pro **App Service klientů, správců, funkcí portálů a nástrojů Kudu** Konzistentní s Azure Stack verze sady SDK portálu.

- Aktualizuje **Azure Functions runtime** na **v 1.0.12615**.

- Aktualizace služby Core pro zlepšení spolehlivosti a chybového zasílání zpráv umožňují snazší diagnostiku běžných problémů.

- **Aktualizace následujících aplikačních architektur a nástrojů**:
  - ASP.NET Core 3.1.0
  - ASP.NET Core 3.0.1
  - ASP.NET Core 2.2.8
  - ASP.NET Core modul v2 13.1.19331.0
  - Azul OpenJDK 8.38.0.13
  - Tomcat 7.0.94
  - Tomcat 8.5.42
  - Tomcat 9.0.21
  - PHP 7.1.32
  - PHP 7.2.22
  - PHP 7.3.9
  - Aktualizace Kudu na 85.11024.4154
  - 3\.5.80916.15 MSDeploy
  - NodeJS 10.16.3
  - NPM 6.9.0
  - Git pro Windows 2.19.1.0

- **Aktualizace základního operačního systému všech rolí**:
  - [2019-12 kumulativní aktualizace pro Windows Server 2016 pro systémy založené na platformě x64 (KB4530689)](https://support.microsoft.com/help/4530689)

- **Podpora spravovaných disků pro nová nasazení**

Všechna nová nasazení Azure App Service v centru Azure Stack využije spravované disky pro všechny Virtual Machines a Virtual Machine Scale Sets.  Všechna existující nasazení budou i nadále používat nespravované disky.

- **TLS 1,2 vynutil nástroji pro vyrovnávání zatížení front-endu**

Od této aktualizace se bude protokol **TLS 1,2** vymáhat pro všechny aplikace.

### <a name="known-issues-upgrade"></a>Známé problémy (upgrade)

- Upgrade se nepovede, pokud se při převzetí služeb při selhání na sekundární uzel v clusteru SQL Server Always On.

Během upgradu proběhne volání pro kontrolu existence databáze pomocí hlavního připojovacího řetězce, který selže, protože přihlášení bylo na předchozím hlavním uzlu.

Proveďte jednu z následujících akcí a v instalačním programu klikněte na opakovat.

- Zkopírujte appservice_hostingAdmin přihlašovací jméno z již sekundárního uzlu SQL.

**ANI**

- Převzetí služeb při selhání clusterem SQL na předchozí aktivní uzel.

### <a name="post-deployment-steps"></a>Kroky po nasazení

> [!IMPORTANT]
> Pokud jste poskytli App Service poskytovatele prostředků s instancí SQL Always On, je nutné [přidat databáze appservice_hosting a appservice_metering do skupiny dostupnosti](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) a synchronizovat databáze, aby nedošlo ke ztrátě služeb v případě převzetí služeb při selhání databáze.

### <a name="known-issues-post-installation"></a>Známé problémy (po instalaci)

- Pokud je App Service nasazená ve stávající virtuální síti a souborový server je k dispozici pouze v privátní síti, je k dispozici pracovní proces, který se nemůže připojit k souborovému serveru, jak je uvedeno v dokumentaci k nasazení Azure Stack Azure App Service.

  Pokud se rozhodnete nasadit do existující virtuální sítě a interní IP adresu pro připojení k souborovému serveru, musíte přidat odchozí pravidlo zabezpečení, které povoluje provoz SMB mezi podsítí pracovních procesů a souborovým serverem. Na portálu pro správu přejdete na WorkersNsg a přidáte odchozí pravidlo zabezpečení s následujícími vlastnostmi:
  - Zdroj: Any
  - Rozsah zdrojových portů: *
  - Cíl: IP adresy
  - Rozsah cílových IP adres: rozsah IP adres pro souborový server
  - Rozsah cílových portů: 445
  - Protocol: TCP
  - Akce: povolení
  - Priorita: 700
  - Název: Outbound_Allow_SMB445

- Nová nasazení Azure App Service v centru Azure Stack hub 1,8 vyžadují, aby se databáze převedly na databáze s omezením.

Z důvodu regrese v této verzi je nutné při **nově** nasazených databázích App Service i databáze (appservice_hosting a appservice_metering) převést na databáze s omezením.  To **nemá** vliv na **upgradovaná** nasazení.

> [!IMPORTANT]
> Tento postup trvá přibližně 5-10 minut. Tento postup zahrnuje ukončení stávajících přihlašovacích relací databáze. Plánování výpadků migrace a ověření Azure App Service v příspěvku Azure Stack centra po migraci
>
>

1. Přidejte [databáze AppService (appservice_hosting a appservice_metering) do skupiny dostupnosti](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database).

1. Povoluje databázi s omezením.

    ```sql

        sp_configure 'contained database authentication', 1;
        GO
        RECONFIGURE;
            GO
    ```

1. Převod databáze na částečně obsaženou. V tomto kroku dojde k výpadku, protože je potřeba ukončit všechny aktivní relace.

    ```sql
        /******** [appservice_metering] Migration Start********/
            USE [master];

            -- kill all active sessions
            DECLARE @kill varchar(8000) = '';  
            SELECT @kill = @kill + 'kill ' + CONVERT(varchar(5), session_id) + ';'  
            FROM sys.dm_exec_sessions
            WHERE database_id  = db_id('appservice_metering')

            EXEC(@kill);

            USE [master]  
            GO  
            ALTER DATABASE [appservice_metering] SET CONTAINMENT = PARTIAL  
            GO  

        /********[appservice_metering] Migration End********/

        /********[appservice_hosting] Migration Start********/

            -- kill all active sessions
            USE [master];

            DECLARE @kill varchar(8000) = '';  
            SELECT @kill = @kill + 'kill ' + CONVERT(varchar(5), session_id) + ';'  
            FROM sys.dm_exec_sessions
            WHERE database_id  = db_id('appservice_hosting')

            EXEC(@kill);

            -- Convert database to contained
            USE [master]  
            GO  
            ALTER DATABASE [appservice_hosting] SET CONTAINMENT = PARTIAL  
            GO  

            /********[appservice_hosting] Migration End********/
    '''

1. Migrate logins to contained database users.

    ```sql
        IF EXISTS(SELECT * FROM sys.databases WHERE Name=DB_NAME() AND containment = 1)
        BEGIN
        DECLARE @username sysname ;  
        DECLARE user_cursor CURSOR  
        FOR
            SELECT dp.name
            FROM sys.database_principals AS dp  
            JOIN sys.server_principals AS sp
                ON dp.sid = sp.sid  
                WHERE dp.authentication_type = 1 AND dp.name NOT IN ('dbo','sys','guest','INFORMATION_SCHEMA');
            OPEN user_cursor  
            FETCH NEXT FROM user_cursor INTO @username  
                WHILE @@FETCH_STATUS = 0  
                BEGIN  
                    EXECUTE sp_migrate_user_to_contained
                    @username = @username,  
                    @rename = N'copy_login_name',  
                    @disablelogin = N'do_not_disable_login';  
                FETCH NEXT FROM user_cursor INTO @username  
            END  
            CLOSE user_cursor ;  
            DEALLOCATE user_cursor ;
            END
        GO
    ```

    **Oproti**

1. Zkontroluje, jestli SQL Server má povolené omezení.

    ```sql
        sp_configure  @configname='contained database authentication'
    ```

1. Zkontroluje existující obsažené chování.

    ```sql
        SELECT containment FROM sys.databases WHERE NAME LIKE (SELECT DB_NAME())
    ```

- Nepovedlo se škálovat pracovní procesy

  Noví pracovníci nedokázali získat požadovaný připojovací řetězec databáze.  Pokud chcete tuto situaci napravit, připojte se k jedné z instancí řadiče, například CN0-VM a spusťte následující skript prostředí PowerShell:

  ```powershell
 
    [System.Reflection.Assembly]::LoadWithPartialName("Microsoft.Web.Hosting")
    $siteManager = New-Object Microsoft.Web.Hosting.SiteManager
    $builder = New-Object System.Data.SqlClient.SqlConnectionStringBuilder -ArgumentList (Get-AppServiceConnectionString -Type Hosting)
    $conn = New-Object System.Data.SqlClient.SqlConnection -ArgumentList $builder.ToString()

    $siteManager.Workers | ForEach-Object {
        $worker = $_
        $dbUserName = "WebWorker_" + $worker.Name

        if (!$siteManager.ConnectionContexts[$dbUserName]) {
            $dbUserPassword = [Microsoft.Web.Hosting.Common.Security.PasswordHelper]::GenerateDatabasePassword()
            $conn.Open()
            $command = $conn.CreateCommand()
            $command.CommandText = "CREATE USER [$dbUserName] WITH PASSWORD = '$dbUserPassword'"
            $command.ExecuteNonQuery()
            $conn.Close()
            $conn.Open()

            $command = $conn.CreateCommand()
            $command.CommandText = "ALTER ROLE [WebWorkerRole] ADD MEMBER [$dbUserName]"
            $command.ExecuteNonQuery()
            $conn.Close()

            $builder.Password = $dbUserPassword
            $builder["User ID"] = $dbUserName
            $siteManager.ConnectionContexts.Add($dbUserName, $builder.ToString())
        }
    }
    $siteManager.CommitChanges()
    ```

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Známé problémy pro cloudové správce pracující Azure App Service v Azure Stack

Informace najdete v dokumentaci k [verzi Azure Stack 1907](azure-stack-release-notes-1907.md) .

## <a name="next-steps"></a>Další kroky

- Přehled Azure App Service najdete v tématu [přehled Azure App Service na Azure Stack](azure-stack-app-service-overview.md).
- Další informace o tom, jak připravit nasazení App Service v Azure Stack, najdete v tématu [před zahájením práce s App Service na Azure Stack](azure-stack-app-service-before-you-get-started.md).
