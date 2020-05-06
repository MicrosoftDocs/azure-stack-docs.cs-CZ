---
title: Zpráva k vydání verze pro Azure Stack centra aktualizace 8 App Service
description: Aktualizace 8 poznámky k verzi pro App Service v centru Azure Stack, včetně nových funkcí, oprav a známých problémů.
author: apwestgarth
manager: stefsch
ms.topic: article
ms.date: 05/05/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 5bdf06b740d8a2c12f96494c52a683f50fe31340
ms.sourcegitcommit: c263a86d371192e8ef2b80ced2ee0a791398cfb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2020
ms.locfileid: "82847805"
---
# <a name="app-service-on-azure-stack-hub-update-8-release-notes"></a>Zpráva k vydání verze pro Azure Stack centra aktualizace 8 App Service

Tyto poznámky k verzi popisují nové funkce, opravy a známé problémy v Azure App Service v centru Azure Stack aktualizace 8. Známé problémy jsou rozdělené do dvou částí: problémy související s procesem upgradu a problémy se sestavením (po instalaci).

> [!IMPORTANT]
> Před nasazením Azure App Service 1,8 použijte aktualizaci 1910 na integrovaný systém Azure Stack nebo nasaďte nejnovější Azure Stack Development Kit (ASDK).

## <a name="build-reference"></a>Odkaz na sestavení

App Service číslo buildu Azure Stack centra aktualizace 8 je **86.0.2.13**.

## <a name="prerequisites"></a>Požadavky

Než začnete s nasazením, přečtěte si téma [předpoklady pro nasazení App Service v centru Azure Stack](azure-stack-app-service-before-you-get-started.md) .

Než začnete s upgradem Azure App Service v centru Azure Stack na 1,8:

- Ujistěte se, že všechny role jsou připravené v Azure App Service správě na portálu Azure Stack správce centra.

- Zálohování App Service tajných kódů pomocí správy App Service na portálu pro správu centra Azure Stack

- Zálohování App Service a hlavních databází:
  - AppService_Hosting;
  - AppService_Metering;
  - master

- Zálohujte sdílenou složku obsahu aplikace tenanta.

  > [!Important]
  > Operátoři cloudu zodpovídají za údržbu a provoz souborového serveru a SQL Server.  Poskytovatel prostředků tyto prostředky nespravuje.  Operátor cloudu zodpovídá za zálohování databází App Service a sdílené složky obsahu tenanta.

- Zasyndikátte si **rozšíření vlastních skriptů** **1.9.3** z Marketplace centra Azure Stack.

## <a name="new-features-and-fixes"></a>Nové funkce a opravy

Azure App Service v centru Azure Stack aktualizace 8 obsahuje následující vylepšení a opravy:

- Aktualizace pro **App Service tenanta, správce, funkcí portáls a nástroje Kudu** Konzistentní s Azure Stack verze sady SDK portálu.

- Aktualizuje **modul runtime služby Azure Functions** na verzi **v 1.0.12615**.

- Aktualizace služby Core pro zlepšení spolehlivosti a chybového zasílání zpráv, které umožňují snazší diagnostiku běžných problémů.

- **Aktualizuje následující aplikační architektury a nástroje**:

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
  - 3.5.80916.15 MSDeploy
  - NodeJS 10.16.3
  - NPM 6.9.0
  - Git pro Windows 2.19.1.0

- **Aktualizace základního operačního systému všech rolí**:
  - [2019-12 kumulativní aktualizace pro Windows Server 2016 pro systémy založené na platformě x64 (KB4530689)](https://support.microsoft.com/help/4530689)

- **Podpora spravovaných disků pro nová nasazení**

Všechna nová nasazení Azure App Service v centru Azure Stack budou využívat spravované disky pro všechny virtuální počítače a sady škálování virtuálních počítačů. Všechna existující nasazení budou i nadále používat nespravované disky.

- **Protokol TLS 1,2 vynutilý moduly pro vyrovnávání zatížení front-endu**

Pro všechny aplikace se teď vynutilo **TLS 1,2** .

## <a name="known-issues-upgrade"></a>Známé problémy (upgrade)

- Upgrade se nepovede, pokud se při převzetí služeb při selhání v clusteru SQL Server Always na sekundární uzel.

Během upgradu se nachází volání kontroly existence databáze pomocí hlavního připojovacího řetězce, který se nezdařil, protože přihlášení bylo na předchozím hlavním uzlu.

Proveďte jednu z následujících akcí a v instalačním programu vyberte opakovat.

- Zkopírujte `appservice_hostingAdmin` přihlašovací údaje z nyní sekundárního uzlu SQL.

    **ANI**

- Převzetí služeb při selhání clusterem SQL na předchozí aktivní uzel.

## <a name="post-deployment-steps"></a>Kroky po nasazení

> [!IMPORTANT]
> Pokud jste poskytli App Service poskytovatele prostředků s instancí SQL Always On, je nutné [přidat databáze appservice_hosting a appservice_metering do skupiny dostupnosti](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) a synchronizovat databáze, aby nedošlo ke ztrátě služeb v případě převzetí služeb při selhání databáze.

## <a name="known-issues-post-installation"></a>Známé problémy (po instalaci)

- Pokud je App Service nasazená v existující virtuální síti a souborový server je k dispozici jenom v privátní síti, je k dispozici pracovní proces, který se nemůže připojit k souborovému serveru, jak je vyvoláno v dokumentaci k nasazení centra Azure Stack Azure App Service.

  Pokud se rozhodnete nasadit do existující virtuální sítě a interní IP adresu pro připojení k souborovému serveru, musíte přidat odchozí pravidlo zabezpečení, které povoluje provoz SMB mezi podsítí pracovních procesů a souborovým serverem. Na portálu pro správu přejdete na WorkersNsg a přidáte odchozí pravidlo zabezpečení s následujícími vlastnostmi:

  - Zdroj: Any
  - Rozsah zdrojových portů: *
  - Cíl: IP adresy
  - Rozsah cílových IP adres: rozsah IP adres pro souborový server
  - Rozsah cílových portů: 445
  - Protokol. TCP
  - Akce: Povolit
  - Priorita: 700
  - Název: Outbound_Allow_SMB445

- Nová nasazení Azure App Service v centru Azure Stack hub 1,8 vyžadují, aby se databáze převedly na databáze s omezením.

    Z důvodu regrese v této verzi je nutné při **nově** nasazených databázích App Service i databáze (appservice_hosting a appservice_metering) převést na databáze s omezením.  To **nemá** vliv na **upgradovaná** nasazení.

    > [!IMPORTANT]
    > Tento postup trvá přibližně 5-10 minut. Tento postup zahrnuje ukončení stávajících přihlašovacích relací databáze. Naplánujte výpadky migrace a ověření Azure App Service v příspěvku Azure Stack centra po migraci.

    1. Přidejte [databáze AppService (appservice_hosting a appservice_metering) do skupiny dostupnosti](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database).

    1. Povoluje databázi s omezením.

        ```sql

        sp_configure 'contained database authentication', 1;
        GO
        RECONFIGURE;
            GO
        ```

    1. Převeďte databázi na částečně obsaženou. V tomto kroku dojde k výpadku, protože všechny aktivní relace je potřeba ukončit.

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
        ```

    1. Migruje přihlášení pro uživatele databáze s omezením.

        ```sql
        USE appservice_hosting
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

        USE appservice_metering
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

    **Ověření**

    1. Zkontroluje, jestli SQL Server má povolené omezení.

        ```sql
        sp_configure  @configname='contained database authentication'
        ```

    1. Zkontroluje existující obsažené chování.

        ```sql
        SELECT containment FROM sys.databases WHERE NAME LIKE (SELECT DB_NAME())
        ```

- Nepovedlo se škálovat pracovní procesy

  Noví pracovníci nedokázali získat požadovaný připojovací řetězec databáze.  Pokud chcete tuto situaci napravit, připojte se k některé z instancí řadiče (například CN0-VM) a spusťte následující skript prostředí PowerShell:

    ```powershell

    [System.Reflection.Assembly]::LoadWithPartialName("Microsoft.Web.Hosting")
    $siteManager = New-Object Microsoft.Web.Hosting.SiteManager

    $builder = New-Object System.Data.SqlClient.SqlConnectionStringBuilder -ArgumentList (Get-AppServiceConnectionString -Type Hosting)
    $conn = New-Object System.Data.SqlClient.SqlConnection -ArgumentList $builder.ToString()

    $siteManager.RoleServers | Where-Object {$_.IsWorker} | ForEach-Object {
        $worker = $_
        $dbUserName = "WebWorker_" + $worker.Name

        if (!$siteManager.ConnectionContexts[$dbUserName]) {
            $dbUserPassword = [Microsoft.Web.Hosting.Common.Security.PasswordHelper]::GenerateDatabasePassword()

            $conn.Open()
            $command = $conn.CreateCommand()
            $command.CommandText = "CREATE USER [$dbUserName] WITH PASSWORD = '$dbUserPassword'"
            $command.ExecuteNonQuery()
            $conn.Close()

            $conn.Open()
            $command = $conn.CreateCommand()
            $command.CommandText = "ALTER ROLE [WebWorkerRole] ADD MEMBER [$dbUserName]"
            $command.ExecuteNonQuery()
            $conn.Close()

            $builder.Password = $dbUserPassword
            $builder["User ID"] = $dbUserName

            $siteManager.ConnectionContexts.Add($dbUserName, $builder.ToString())
        }
    }

    $siteManager.CommitChanges()

    ```

## <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack-hub"></a>Známé problémy pro Cloud Admins, které pracují Azure App Service v centru Azure Stack

Informace najdete v [poznámkách k verzi centra Azure Stack 1907](azure-stack-release-notes-1907.md).

## <a name="next-steps"></a>Další kroky

- Přehled Azure App Service najdete v tématu [Azure App Service a Azure Functions v článku Přehled centra pro Azure Stack](azure-stack-app-service-overview.md).
- Další informace o přípravě nasazení App Service v centru Azure Stack najdete v tématu [předpoklady pro nasazení App Service na Azure Stack hub](azure-stack-app-service-before-you-get-started.md).
