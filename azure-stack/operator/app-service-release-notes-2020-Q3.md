---
title: Zpráva k vydání verze 2002. App Service v centru Azure Stack
description: Přečtěte si informace o tom, co je ve vydání 2002. čtvrtletí pro App Service v centru Azure Stack, známých problémech a kde stáhnout aktualizaci.
author: apwestgarth
manager: stefsch
ms.topic: article
ms.date: 10/28/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 10/23/2020
ms.openlocfilehash: 55c8e18f3bbd36a0fef4cfb745ab9d40651e7533
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94548657"
---
# <a name="app-service-on-azure-stack-hub-2020-q3-release-notes"></a>Zpráva k vydání verze 2020. App Service v centru Azure Stack

Tyto poznámky k verzi popisují vylepšení a opravy v Azure App Service v centru Azure Stack 2020, Q3 a všech známých problémech. Známé problémy jsou rozděleny do potíží přímo souvisejících s nasazením, procesem aktualizace a problémy se sestavením (po instalaci).

[!INCLUDE [Azure Stack Hub update reminder](../includes/app-service-hub-update-banner.md)]

## <a name="build-reference"></a>Referenční informace o buildu

App Service číslo buildu v centru Azure Stack 2020 Q3 je **89.0.2.15**

## <a name="prerequisites"></a>Požadavky

Než začnete s nasazením, přečtěte si [dokumentaci před](azure-stack-app-service-before-you-get-started.md) začátkem.

Než začnete s upgradem Azure App Service v Azure Stack na 2020. čtvrtletí:

- Zajistěte, aby všechny role byly připravené ve správě Azure App Service na portálu Azure Stack hub pro správu.

- Zálohování App Service tajných kódů pomocí správy App Service na portálu pro správu centra Azure Stack

- Zálohování App Service a hlavních databází:
  - AppService_Hosting;
  - AppService_Metering;
  - Hlavní

- Zálohování sdílené složky obsahu aplikace tenanta

  > [!Important]
  > Operátoři cloudu zodpovídají za údržbu a provoz souborového serveru a SQL Server.  Poskytovatel prostředků tyto prostředky nespravuje.  Operátor cloudu zodpovídá za zálohování databází App Service a sdílené složky obsahu tenanta.

- Zasyndikátte si **rozšíření vlastních skriptů** **1.9.3** z Marketplace.

## <a name="updates"></a>Aktualizace

Azure App Service na Azure Stack Update Q3 obsahuje následující vylepšení a opravy:

- Aktualizace pro **App Service klientů, správců, funkcí portálů a nástrojů Kudu** Konzistentní s Azure Stack verze sady SDK portálu.

- Přidání prostředí pro webové a funkční aplikace na celé obrazovce

- Nové prostředí Azure Functionsového portálu v souladu s Web Apps

- Aktualizuje **Azure Functions runtime** na **v 1.0.13154**.

- Aktualizace služby Core pro zlepšení spolehlivosti a chybového zasílání zpráv umožňují snazší diagnostiku běžných problémů.

- **Aktualizace následujících aplikačních architektur a nástrojů** :
  - ASP.NET Core 2.1.22
  - ASP.NET Core 2.2.14
  - ASP.NET Core 3.1.8
  - ASP.NET Core modul v2 13.1.19331.0
  - Azul OpenJDK
    - 8.42.0.23
    - 8.44.0.11
    - 11.35.15
    - 11.37.17
  - Oblé 7.55.1
  - Git pro Windows 2.28.0.1
  - 3.5.90702.36 MSDeploy
  - NodeJS
    - 14.10.1
  - NPM
    - 6.14.8
  - PHP 7.4.5
  - Tomcat
    - 8.5.47
    - 8.5.51
    - 9.0.273
    - 9.0.31
  - Aktualizace Kudu na 90.21005.4823

- **Aktualizace základního operačního systému všech rolí** :
  - [2020-10 kumulativní aktualizace pro Windows Server 2016 pro systémy založené na platformě x64 (KB4580346)](https://support.microsoft.com/help/4580346)
  - [2020-09 aktualizace servisního zásobníku pro Windows Server 2016 pro systémy na platformě x64 (KB4576750)](https://support.microsoft.com/help/4576750)
  - 1.325.755.0 definice Defenderu

- **Kumulativní aktualizace pro Windows Server se teď v rámci nasazení a upgradu aplikují na role kontroleru.**

## <a name="issues-fixed-in-this-release"></a>Chyby opravené v této verzi

- Klienti teď můžou vytvořit plán App Service pomocí nového App Serviceho zobrazení plánu na portálu tenanta.

- Klienti můžou spravovat certifikáty pro své aplikace na portálu tenanta.

- Monitorování funkcí teď může načítat data z koncových bodů úložiště vynucujících TLS 1,2.

- Při instalaci se přesunul krok čekání na servery pro správu mimo krok nasazení cloudu, aby se zlepšila spolehlivost nasazení a upgradu.

## <a name="pre-update-steps"></a>Kroky před aktualizací

Projděte si [známé problémy s aktualizací](#known-issues-update) a proveďte předepsané akce.

## <a name="post-deployment-steps"></a>Kroky po nasazení

> [!IMPORTANT]
> Pokud jste poskytli App Service poskytovatele prostředků s instancí SQL Always On, je nutné [přidat databáze appservice_hosting a appservice_metering do skupiny dostupnosti](/sql/database-engine/availability-groups/windows/availability-group-add-a-database) a synchronizovat databáze, aby nedošlo ke ztrátě služeb v případě převzetí služeb při selhání databáze.

## <a name="known-issues-update"></a>Známé problémy (aktualizace)

- V situacích, kdy zákazník převedl služby appservice_hosting a databáze appservice_metering na databázi s omezením, může upgrade selhat, pokud nebyla přihlášení úspěšně migrována na obsažené uživatele.

Zákazníci, kteří převedli databáze appservice_hosting a appservice_metering na databázi s omezením nasazení a kteří neúspěšně migrovali přihlašovací údaje databáze pro obsažené uživatele, mohou nastat při selhání upgradu.  

Před upgradem Azure App Service na Azure Stack instalaci centra na 2020. čtvrtletí musí zákazníci spustit následující skript proti SQL Server hostování appservice_hosting a appservice_metering.  **Tento skript není destruktivní a nebude způsobovat výpadky**.

Tento skript musí být spuštěný za následujících podmínek.

- Uživatel, který má oprávnění správce systému, například účet SQL SA;
- Pokud používáte SQL Always On, ujistěte se, že se skript spouští z instance SQL, která obsahuje všechna App Service přihlašovacích údajů ve formuláři:

    - appservice_hosting_FileServer
    - appservice_hosting_HostingAdmin
    - appservice_hosting_LoadBalancer
    - appservice_hosting_Operations
    - appservice_hosting_Publisher
    - appservice_hosting_SecurePublisher
    - appservice_hosting_WebWorkerManager
    - appservice_metering_Common
    - appservice_metering_Operations
    - Všechna přihlášení do webworker – které jsou ve formuláři WebWorker_<instance ip address>

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

## <a name="known-issues-post-installation"></a>Známé problémy (po instalaci)

- Pokud je App Service nasazená ve stávající virtuální síti a souborový server je k dispozici pouze v privátní síti, je k dispozici pracovní proces, který se nemůže připojit k souborovému serveru, jak je uvedeno v dokumentaci k nasazení Azure Stack Azure App Service.

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

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Známé problémy pro cloudové správce pracující Azure App Service v Azure Stack

- Vlastní domény nejsou v odpojených prostředích podporované.

App Service provádí ověření vlastnictví domény proti veřejným koncovým bodům DNS, protože ve scénářích odpojení nejsou podporované vlastní domény.

## <a name="next-steps"></a>Další kroky

- Přehled Azure App Service najdete v tématu [přehled Azure App Service na Azure Stack](azure-stack-app-service-overview.md).
- Další informace o tom, jak připravit nasazení App Service v Azure Stack, najdete v tématu [před zahájením práce s App Service na Azure Stack](azure-stack-app-service-before-you-get-started.md).
