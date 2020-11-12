---
title: App Service zpráva k vydání verze centra Azure Stack 2002 Q2
description: Přečtěte si informace o tom, co je ve vydání 2002. Q2 pro App Service v centru Azure Stack, známých problémech a kde stáhnout aktualizaci.
author: apwestgarth
manager: stefsch
ms.topic: article
ms.date: 05/05/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 04/30/2020
ms.openlocfilehash: c5e6ac0a2a500cf43cf94cbc40b2a95c58784d28
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94544713"
---
# <a name="app-service-on-azure-stack-hub-2020-q2-release-notes"></a>App Service zpráva k vydání verze centra Azure Stack 2020 Q2

Tyto poznámky k verzi popisují vylepšení a opravy v Azure App Service v Azure Stack centra 2020 a všechny známé problémy. Známé problémy jsou rozděleny do potíží přímo souvisejících s nasazením, procesem aktualizace a problémy se sestavením (po instalaci).

[!INCLUDE [Azure Stack Hub update reminder](../includes/app-service-hub-update-banner.md)]

## <a name="build-reference"></a>Referenční informace o buildu

App Service číslo buildu v centru Azure Stack 2020.2 – 2. **87.0.2.10**

## <a name="prerequisites"></a>Požadavky

Než začnete s nasazením, přečtěte si [dokumentaci před](azure-stack-app-service-before-you-get-started.md) začátkem.

Než začnete s upgradem Azure App Service v Azure Stack na 2020.2. čtvrtletí:

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

Azure App Service na Azure Stack Update Q2 obsahuje následující vylepšení a opravy:

- Aktualizace pro **App Service klientů, správců, funkcí portálů a nástrojů Kudu** Konzistentní s Azure Stack verze sady SDK portálu.

- Aktualizuje **Azure Functions runtime** na **v 1.0.13021**.

- Aktualizace služby Core pro zlepšení spolehlivosti a chybového zasílání zpráv umožňují snazší diagnostiku běžných problémů.

- **Aktualizace následujících aplikačních architektur a nástrojů** :
  - ASP.NET Framework 4.7.2
  - ASP.NET Core 3.1.3
  - ASP.NET Core modul v2 13.1.19331.0
  - PHP 7.4.2
  - Aktualizace Kudu na 86.20224.4450
  - NodeJS 
    - 8.17.0
    - 10.19.0
    - 12.13.0
    - 12.15.0
  - NPM
    - 5.6.0
    - 6.1.0
    - 6.12.0
    - 6.13.4
  
- **Aktualizace základního operačního systému všech rolí** :
  - [2020-04 kumulativní aktualizace pro Windows Server 2016 pro systémy založené na platformě x64 (KB4550929)](https://support.microsoft.com/help/4550929)
  - [2020-04 aktualizace servisního zásobníku pro Windows Server 2016 pro systémy na platformě x64 (KB4550994)](https://support.microsoft.com/help/4550994)

- **Kumulativní aktualizace pro Windows Server se teď v rámci nasazení a upgradu aplikují na role kontroleru.**

- **Aktualizované výchozí hodnoty SKU virtuálního počítače a sady škálování pro nová nasazení** : aby byla zajištěna konzistence s naší veřejnou cloudovou službou, nové nasazení Azure App Service v centru Azure Stack budou používat následující SKU pro základní počítače a sady škálování používané pro provozování poskytovatele prostředků.
  
  | Role | Minimální SKU |
  | --- | --- |
  | Kontrolér | Standard_A4_v2 – (4 jádra, 8192 MB) |
  | Správa | Standard_D3_v2 – (4 jádra, 14336 MB) |
  | Publisher | Standard_A2_v2 – (2 jádra, 4096 MB) |
  | FrontEnd | Standard_A4_v2 – (4 jádra, 8192 MB) |
  | Sdílený pracovní proces | Standard_A4_v2 – (4 jádra, 8192 MB) |
  | Malý vyhrazený pracovní proces | Standard_A1_v2 – (1 jader, 2048 MB) |
  | Střední vyhrazený pracovní proces | Standard_A2_v2 – (2 jádra, 4096 MB) |
  | Velký vyhrazený pracovní proces | Standard_A4_v2 – (4 jádra, 8192 MB) |

U nasazení ASDK můžete škálovat instance na nižší skladové jednotky a snížit tak základní a úložné potvrzení, ale dojde k snížení výkonu.

## <a name="issues-fixed-in-this-release"></a>Chyby opravené v této verzi

- Upgrade se teď dokončí, pokud se při převzetí služeb při selhání clusteru SQL Always na sekundární uzel převezme.
- Nová nasazení Azure App Service v centru Azure Stack už nevyžadují, aby se databáze ručně převedly na obsažené databáze.
- Přidání dalších pracovníků nebo instancí rolí infrastruktury se dokončí správně bez ručního zásahu.
- Přidání vlastních vrstev pracovního procesu se dokončí správně bez ručního zásahu.
- Odebrání vlastních vrstev pracovního procesu se teď dokončuje bez chyb na portálu.
- Když je na místním disku nedostatek místa, procesy už nebudou označeny jako připravené.
- Při načítání Azure Resource Manager certifikátu se zvýšil časový limit.
- Počet načtených zpráv, z protokolů serveru a zobrazených v portálu pro správu, je omezen, aby zůstal pod maximální velikostí žádosti Azure Resource Manager
- Problém s časovým limitem způsobující problémy se spouštěním služby Usage
- Problém při nasazení databáze vyřešen při vytváření lokalit CMS
- Řadiče se teď aktualizují pomocí kumulativních aktualizací Windows jako součást nasazení a upgradu.
- App Service už nezamkne operace, když se vlastní ověření domény nepovede.

## <a name="pre-update-steps"></a>Kroky před aktualizací

Projděte si [známé problémy s aktualizací](#known-issues-update) a proveďte předepsané akce.

## <a name="post-deployment-steps"></a>Kroky po nasazení

> [!IMPORTANT]
> Pokud jste poskytli App Service poskytovatele prostředků s instancí SQL Always On, je nutné [přidat databáze appservice_hosting a appservice_metering do skupiny dostupnosti](/sql/database-engine/availability-groups/windows/availability-group-add-a-database) a synchronizovat databáze, aby nedošlo ke ztrátě služeb v případě převzetí služeb při selhání databáze.

## <a name="known-issues-update"></a>Známé problémy (aktualizace)

- V situacích, kdy zákazník převedl služby appservice_hosting a databáze appservice_metering na databázi s omezením, může upgrade selhat, pokud nebyla přihlášení úspěšně migrována na obsažené uživatele.

Zákazníci, kteří převedli databáze appservice_hosting a appservice_metering na databázi s omezením nasazení a kteří neúspěšně migrovali přihlašovací údaje databáze pro obsažené uživatele, mohou nastat při selhání upgradu.  

Před upgradem Azure App Service na Azure Stack instalaci centra na 2020 Q2 musí zákazníci spustit následující skript proti SQL Server hostování appservice_hosting a appservice_metering.  **Tento skript není destruktivní a nebude způsobovat výpadky**.

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

- Klienti nemůžou vytvořit plán App Service pomocí nového App Serviceho zobrazení plánu na portálu tenanta.

Při vytváření nové aplikace můžou klienti vytvářet App Service plány během pracovního postupu vytvoření aplikace nebo při změně plánu App Service pro aktuální aplikaci nebo prostřednictvím položky na webu Marketplace pro naplánování App Service.

- Vlastní domény nejsou v odpojených prostředích podporované.

App Service provádí ověření vlastnictví domény proti veřejným koncovým bodům DNS, protože ve scénářích odpojení nejsou podporované vlastní domény.

## <a name="next-steps"></a>Další kroky

- Přehled Azure App Service najdete v tématu [přehled Azure App Service na Azure Stack](azure-stack-app-service-overview.md).
- Další informace o tom, jak připravit nasazení App Service v Azure Stack, najdete v tématu [před zahájením práce s App Service na Azure Stack](azure-stack-app-service-before-you-get-started.md).
