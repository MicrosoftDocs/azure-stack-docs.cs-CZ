---
title: Zpráva k vydání verze v App Service Azure Stack centra aktualizace 5
description: Přečtěte si o vylepšeních, opravách a známých problémech v aktualizaci 5 pro App Service v centru Azure Stack.
author: bryanla
manager: stefsch
ms.topic: article
ms.date: 03/25/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 42a87396caeb4392b14e88dd122f78396efb8ead
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "77695491"
---
# <a name="app-service-on-azure-stack-hub-update-5-release-notes"></a>Zpráva k vydání verze v App Service Azure Stack centra aktualizace 5

Tyto poznámky k verzi popisují vylepšení, opravy a známé problémy v Azure App Service v Azure Stack centra aktualizace 5. Známé problémy jsou rozdělené na tři části: problémy přímo související s nasazením, problémy s procesem aktualizace a problémy se sestavením (po instalaci).

> [!IMPORTANT]
> Před nasazením Azure App Service 1,5 použijte aktualizaci 1901 pro integrovaný systém Azure Stack hub nebo nasaďte nejnovější Azure Stack Development Kit (ASDK).

## <a name="build-reference"></a>Odkaz na sestavení

App Service číslo buildu Azure Stack centra aktualizace 5 je **80.0.2.15**.

### <a name="prerequisites"></a>Požadavky

Než začnete s nasazením, přečtěte si [požadavky pro nasazení App Service v centru Azure Stack](azure-stack-app-service-before-you-get-started.md) .

Než začnete s upgradem Azure App Service v centru Azure Stack na 1,5:

- Ujistěte se, že všechny role jsou připravené ve správě Azure App Service na portálu Azure Stack správce centra.

- Zálohování App Service a hlavních databází:
  - AppService_Hosting;
  - AppService_Metering;
  - Hlavní

- Zálohujte sdílenou složku obsahu aplikace tenanta.

- Zasyndikátte si **rozšíření vlastních skriptů** **1.9.1** z Azure Marketplace.

### <a name="new-features-and-fixes"></a>Nové funkce a opravy

Azure App Service v centru Azure Stack s aktualizací Update 5 zahrnuje následující vylepšení a opravy:

- Aktualizace pro **App Service klientů, správců, funkcí portálů a nástrojů Kudu** Konzistentní s verzí sady SDK portálu Azure Stack hub.

- Aktualizuje **Azure Functions runtime** na **v 1.0.12205**.

- Aktualizace **Kudu nástrojů** pro řešení problémů se stylem a funkcemi pro zákazníky, kteří provozují **odpojený** Azure Stack hub. 

- Aktualizace služby Core pro zlepšení spolehlivosti a chybového zasílání zpráv umožňují snazší diagnostiku běžných problémů.

- **Aktualizuje následující aplikační architektury a nástroje**:
  - Přidáno ASP.NET Core 2.1.6 a 2.2.0
  - Přidání NodeJS 10.14.1
  - Přidání NPM 6.4.1
  - Aktualizace Kudu na 79.20129.3767
  
- **Aktualizace základního operačního systému všech rolí**:
  - [2019-02 kumulativní aktualizace pro Windows Server 2016 pro systémy založené na platformě x64 (KB4487006)](https://support.microsoft.com/help/4487006/windows-10-update-kb4487006)

### <a name="post-deployment-steps"></a>Kroky po nasazení

> [!IMPORTANT]  
> Pokud jste poskytli App Service poskytovatele prostředků s instancí SQL Always On, je *nutné* [přidat databáze appservice_hosting a appservice_metering do skupiny dostupnosti](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) a synchronizovat databáze, aby nedošlo ke ztrátě služeb v případě převzetí služeb při selhání databáze.

### <a name="post-update-steps"></a>Kroky po aktualizaci

Pro zákazníky, kteří chtějí migrovat na databázi s omezením na existující Azure App Service v nasazeních centra Azure Stack, proveďte tyto kroky po dokončení Azure App Service aktualizace Azure Stack centra 1,5:

> [!IMPORTANT]
> Postup migrace trvá přibližně 5-10 minut. Tento postup zahrnuje ukončení stávajících přihlašovacích relací databáze. Naplánujte výpadky migrace a ověření Azure App Service v příspěvku Azure Stack centra po migraci. Pokud jste tyto kroky dokončili po aktualizaci na Azure App Service v centru Azure Stack 1,3, tyto kroky se nevyžadují.

1. Přidejte [databáze AppService (appservice_hosting a appservice_metering) do skupiny dostupnosti](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database).

1. Povoluje databázi s omezením.
    ```sql

        sp_configure 'contained database authentication', 1;
        GO
        RECONFIGURE;
            GO
    ```

1. Převod databáze na částečně obsažený převod bude mít za následek výpadky, protože všechny aktivní relace je potřeba ukončit.

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

### <a name="known-issues-post-installation"></a>Známé problémy (po instalaci)

- Pokud je App Service nasazená ve stávající virtuální síti a souborový server je k dispozici jenom v privátní síti, zaměstnanci nemůžou kontaktovat souborový server. Tento problém se nazývá Azure App Service v dokumentaci k nasazení centra Azure Stack.

Pokud se rozhodnete nasadit do existující virtuální sítě a interní IP adresu pro připojení k souborovému serveru, musíte přidat odchozí pravidlo zabezpečení, které umožňuje provoz protokolu SMB mezi podsítí pracovních procesů a souborovým serverem. Na portálu pro správu přejdete na WorkersNsg a přidáte odchozí pravidlo zabezpečení s následujícími vlastnostmi:

 * Zdroj: Any
 * Rozsah zdrojových portů: *
 * Cíl: IP adresy
 * Rozsah cílových IP adres: rozsah IP adres pro souborový server
 * Rozsah cílových portů: 445
 * Protokol. TCP
 * Akce: Povolit
 * Priorita: 700
 * Název: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack-hub"></a>Známé problémy pro Cloud Admins, které pracují Azure App Service v centru Azure Stack

Informace najdete v [poznámkách k verzi centra Azure Stack 1809](azure-stack-update-1903.md).

## <a name="next-steps"></a>Další kroky

- Přehled Azure App Service najdete v tématu [Azure App Service v tématu Přehled centra Azure Stack](azure-stack-app-service-overview.md).
- Další informace o tom, jak připravit nasazení App Service v centru Azure Stack, najdete v tématu [předpoklady pro nasazení App Service na Azure Stack hub](azure-stack-app-service-before-you-get-started.md).
