---
title: App Service v poznámce k verzi Azure Stack Update 4 | Microsoft Docs
description: Přečtěte si o vylepšeních, opravách a známých problémech v aktualizaci Update 4 pro App Service v Azure Stack.
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
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 9098fb61c0d4edcb534bd7b9d07b4727c953df8d
ms.sourcegitcommit: 245a4054a52e54d5989d6148fbbe386e1b2aa49c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/13/2019
ms.locfileid: "70974894"
---
# <a name="app-service-on-azure-stack-update-4-release-notes"></a>Zpráva k vydání verze App Service ve Azure Stack Update 4

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Tyto poznámky k verzi popisují vylepšení, opravy a známé problémy v Azure App Service na Azure Stack Update 4. Známé problémy jsou rozdělené na tři části: problémy přímo související s nasazením, problémy s procesem aktualizace a problémy se sestavením (po instalaci).

> [!IMPORTANT]
> Před nasazením Azure App Service 1,4 použijte aktualizaci 1809 na integrovaný systém Azure Stack nebo nasaďte nejnovější Azure Stack Development Kit (ASDK).

## <a name="build-reference"></a>Referenční informace o buildu

App Service číslo sestavení Azure Stack Update 4 je **78.0.13698.5**

### <a name="prerequisites"></a>Požadavky

Než začnete s nasazením, přečtěte si [požadavky pro nasazení App Service v Azure Stack](azure-stack-app-service-before-you-get-started.md) .

Než začnete s upgradem Azure App Service v Azure Stack na 1,4:

- Ujistěte se, že všechny role jsou připravené ve správě Azure App Service na portálu Azure Stack správce.

- Zálohování App Service a hlavních databází:
  - AppService_Hosting;
  - AppService_Metering;
  - Předloha

- Zálohujte sdílenou složku obsahu aplikace tenanta.

- Zasyndikátte **rozšíření vlastních skriptů** verze **1,9** z Azure Marketplace.

### <a name="new-features-and-fixes"></a>Nové funkce a opravy

Azure App Service na Azure Stack Update 4 zahrnuje následující vylepšení a opravy:

- Řešení pro chybu zabezpečení skriptování XSS (více lokalit) v [CVE 2018-8600](https://aka.ms/CVE20188600) .

- Byla přidána podpora verze rozhraní API App Service 2018-02-01.

- Aktualizace pro **App Service klientů, správců, funkcí portálů a nástrojů Kudu** Konzistentní s Azure Stack verze sady SDK portálu.

- Aktualizuje **Azure Functions runtime** na **v 1.0.11959**.

- Aktualizace služby Core pro zlepšení spolehlivosti a chybového zasílání zpráv umožňují snazší diagnostiku běžných problémů.

- **Aktualizuje následující aplikační architektury a nástroje**:
  - Přidání NodeJS 10.6.0
  - Přidání NPM 6.1.0
  - Přidání Zulu OpenJDK 8.31.0.2
  - Přidání Tomcat 8.5.34 a 9.0.12
  - Přidané verze PHP:
    - 5.6.37
    - 7.0.31
    - 7.1.20
    - 7.2.8
  - Aktualizace na verze Pythonu:
    - 2.7.15
    - 3.6.6
  - Aktualizace Gitu pro Windows na v 2.17.1.2
  - Aktualizace Kudu na 78.11022.3613
  
- **Aktualizace základního operačního systému všech rolí**:
  - [2018-10 kumulativní aktualizace pro Windows Server 2016 pro systémy založené na platformě x64 (KB4462928)](https://support.microsoft.com/help/4462928/windows-10-update-kb4462928)

- Vyřešil se problém s ověřením šablony při nasazování položek galerie WordPress, DNN a sady CMS.

- Vyřešil se problém s konfigurací, když Azure Stack otáčí klientský certifikát Azure Resource Manager.

- Obnovená funkce v nastavení sdílení prostředků mezi zdroji na portálu App Service tenant.

- V App Service portálu pro správu se teď zobrazí chybová zpráva, když se rovina řízení poskytovatele prostředků nemůže připojit k nakonfigurované instanci SQL Server.

- Ujistěte se, že je koncový bod zadaný v připojovacím řetězci vlastního úložiště, pokud je zadaný v nové aplikaci Function App.

### <a name="post-deployment-steps"></a>Kroky po nasazení

> [!IMPORTANT]  
> Pokud jste poskytli App Service poskytovatele prostředků s instancí SQL Always On, musíte do [skupiny dostupnosti přidat databáze appservice_hosting a appservice_metering](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) a synchronizovat databáze, aby nedošlo ke ztrátě služeb v nástroji. událost převzetí služeb při selhání databáze.

### <a name="post-update-steps-optional"></a>Kroky po aktualizaci (volitelné)

Pro zákazníky, kteří chtějí migrovat na databázi s omezením na existující Azure App Service v nasazeních Azure Stack proveďte tyto kroky po dokončení Azure App Service aktualizace Azure Stack 1,4:

> [!IMPORTANT]
> Postup migrace trvá přibližně 5-10 minut. Tento postup zahrnuje ukončení stávajících přihlašovacích relací databáze. Naplánujte výpadky migrace a ověření Azure App Service Azure Stack příspěvku po migraci. Pokud jste tyto kroky dokončili po aktualizaci na Azure App Service Azure Stack 1,3, pak tyto kroky nejsou potřeba.

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

- Pokud je App Service nasazená ve stávající virtuální síti a souborový server je k dispozici jenom v privátní síti, zaměstnanci nemůžou kontaktovat souborový server. Tento problém se zavolá v dokumentaci pro nasazení Azure App Service Azure Stack.

Pokud se rozhodnete nasadit do existující virtuální sítě a interní IP adresu pro připojení k souborovému serveru, musíte přidat odchozí pravidlo zabezpečení, které umožňuje provoz protokolu SMB mezi podsítí pracovních procesů a souborovým serverem. Na portálu pro správu přejdete na WorkersNsg a přidáte odchozí pravidlo zabezpečení s následujícími vlastnostmi:

 * Zdroj: Any
 * Rozsah zdrojových portů: *
 * Cíl: IP adresy
 * Rozsah cílových IP adres: Rozsah IP adres pro souborový server
 * Rozsah cílových portů: 445
 * Protokol: TCP
 * Akce: Allow
 * Priorita: 700
 * Název: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Známé problémy pro cloudové správce pracující Azure App Service v Azure Stack

Informace najdete v dokumentaci k [verzi Azure Stack 1809](azure-stack-update-1903.md) .

## <a name="next-steps"></a>Další postup

- Přehled Azure App Service najdete v tématu [přehled Azure App Service na Azure Stack](azure-stack-app-service-overview.md).
- Další informace o přípravě na nasazení App Service v Azure Stack najdete v tématu [předpoklady pro nasazení App Service na Azure Stack](azure-stack-app-service-before-you-get-started.md).
