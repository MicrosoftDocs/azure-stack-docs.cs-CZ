---
title: App Service ve Azure Stack centra aktualizace 3 – zpráva k vydání verze | Microsoft Docs
description: Přečtěte si o vylepšeních, opravách a známých problémech v aktualizaci 3 pro App Service v centru Azure Stack.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 08/20/2018
ms.openlocfilehash: a21f5377e1138ab48e2478880b26ba75e939667f
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75804924"
---
# <a name="app-service-on-azure-stack-hub-update-3-release-notes"></a>Zpráva k vydání verze App Service v centru Azure Stack Center Update 3

*Platí pro: Azure Stack integrovaných systémů centra a Azure Stack Development Kit*

Tyto poznámky k verzi popisují vylepšení, opravy a známé problémy v Azure App Service v centru Azure Stack s aktualizací Update 3. Známé problémy jsou rozdělené na tři části: problémy přímo související s nasazením, problémy s procesem aktualizace a problémy se sestavením (po instalaci).

> [!IMPORTANT]
> Před nasazením Azure App Service 1,3 použijte aktualizaci 1807 pro integrovaný systém Azure Stack hub nebo nasaďte nejnovější Azure Stack Development Kit (ASDK).

## <a name="build-reference"></a>Referenční informace o buildu

App Service číslo buildu Azure Stack centra aktualizace 3 je **74.0.13698.31**.

### <a name="prerequisites"></a>Požadavky

Než začnete s nasazením, přečtěte si [požadavky pro nasazení App Service v centru Azure Stack](azure-stack-app-service-before-you-get-started.md) .

Než začnete s upgradem Azure App Service v centru Azure Stack na 1,3, zajistěte, aby všechny role byly připravené ve správě Azure App Service na portálu Azure Stack správce centra.

![Stav role App Service](media/azure-stack-app-service-release-notes-update-three/image01.png)

### <a name="new-features-and-fixes"></a>Nové funkce a opravy

Azure App Service v centru Azure Stack s aktualizací Update 3 obsahuje následující vylepšení a opravy:

- Podpora používání služby SQL Server Always On pro Azure App Service databáze poskytovatele prostředků.

- Přidání nového parametru prostředí do skriptu pomocníka Create-AADIdentityApp, který vám pomůže zaměřit se na různé oblasti Azure AD.

- Aktualizace pro **App Service klientů, správců, funkcí portálů a nástrojů Kudu** Konzistentní s verzí sady SDK portálu Azure Stack hub.

- Aktualizuje **Azure Functions runtime** na **v 1.0.11820**.

- Aktualizace služby Core pro zlepšení spolehlivosti a chybového zasílání zpráv umožňují snazší diagnostiku běžných problémů.

- **Aktualizace následujících aplikačních architektur a nástrojů**:
  - Přidáno ASP.NET Core 2.1.2
  - Přidání NodeJS 10.0.0
  - Přidání Zulu OpenJDK 8.30.0.1
  - Přidání Tomcat 8.5.31 a 9.0.8
  - Přidané verze PHP:
    - 5.6.36
    - 7.0.30
    - 7.1.17
    - 7.2.5
  - Přidání WinCache 2.0.0.8
  - Aktualizace Gitu pro Windows na v 2.17.1.2
  - Aktualizace Kudu na 74.10611.3437
  
- **Aktualizace základního operačního systému všech rolí**:
  - [Aktualizace servisního zásobníku pro Windows Server 2016 pro systémy na platformě x64 (KB4132216)](https://support.microsoft.com/help/4132216/servicing-stack-update-for-windows-10-1607-may-17-2018)
  - [2018-07 kumulativní aktualizace pro Windows Server 2016 pro systémy založené na platformě x64 (KB4338822)](https://support.microsoft.com/help/4338822/windows-10-update-kb4338822)

### <a name="post-update-steps-optional"></a>Kroky po aktualizaci (volitelné)

Pro zákazníky, kteří chtějí migrovat na databázi s omezením na existující Azure App Service v nasazeních centra Azure Stack, proveďte tyto kroky po dokončení Azure App Service aktualizace Azure Stack centra 1,3:

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

### <a name="known-issues-post-installation"></a>Známé problémy (po instalaci)

- Pokud je App Service nasazená ve stávající virtuální síti a souborový server je k dispozici jenom v privátní síti, zaměstnanci nemůžou kontaktovat souborový server. Tento problém se nazývá Azure App Service v dokumentaci k nasazení centra Azure Stack.

Pokud se rozhodnete nasadit do existující virtuální sítě a interní IP adresu pro připojení k souborovému serveru, musíte přidat odchozí pravidlo zabezpečení, které umožňuje provoz protokolu SMB mezi podsítí pracovních procesů a souborovým serverem. Na portálu pro správu přejdete na WorkersNsg a přidáte odchozí pravidlo zabezpečení s následujícími vlastnostmi:

 * Zdroj: Any
 * Rozsah zdrojových portů: *
 * Cíl: IP adresy
 * Rozsah cílových IP adres: rozsah IP adres pro souborový server
 * Rozsah cílových portů: 445
 * Protocol: TCP
 * Akce: povolení
 * Priorita: 700
 * Název: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack-hub"></a>Známé problémy pro Cloud Admins, které pracují Azure App Service v centru Azure Stack

Informace najdete v poznámkách k verzi centra Azure Stack 1807.

## <a name="next-steps"></a>Další kroky

- Přehled Azure App Service najdete v tématu [Azure App Service v tématu Přehled centra Azure Stack](azure-stack-app-service-overview.md).
- Další informace o přípravě nasazení App Service v centru Azure Stack najdete v tématu [předpoklady pro nasazení App Service na Azure Stack hub](azure-stack-app-service-before-you-get-started.md).
