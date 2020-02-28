---
title: App Service zpráva k vydání verze Azure Stack centra pro aktualizaci 6
description: Přečtěte si, co je v aktualizaci šest pro App Service v centru Azure Stack, známých problémech a na tom, kde si tuto aktualizaci stáhli.
author: apwestgarth
manager: stefsch
ms.topic: article
ms.date: 06/24/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 08/20/2019
ms.openlocfilehash: d41455823c6905a947a703412664fc52ff45e1a8
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77701118"
---
# <a name="app-service-on-azure-stack-hub-update-6-release-notes"></a>App Service zpráva k vydání verze Azure Stack centra pro aktualizaci 6

Tyto poznámky k verzi popisují vylepšení a opravy v Azure App Service v centru Azure Stack aktualizace 6 a všech známých problémech. Známé problémy jsou rozděleny do potíží přímo souvisejících s nasazením, procesem aktualizace a problémy se sestavením (po instalaci).

> [!IMPORTANT]
> Před nasazením Azure App Service 1,6 použijte aktualizaci 1904 pro integrovaný systém Azure Stack hub nebo nasaďte nejnovější Azure Stack vývojovou sadu.


## <a name="build-reference"></a>Referenční informace o buildu

App Service číslo buildu Azure Stack centra pro aktualizaci 6 je **82.0.1.50**

### <a name="prerequisites"></a>Požadavky

Než začnete s nasazením, přečtěte si [dokumentaci před](azure-stack-app-service-before-you-get-started.md) začátkem.

Než začnete s upgradem Azure App Service v centru Azure Stack na 1,6:

- Zajistěte, aby všechny role byly připravené ve správě Azure App Service na portálu Azure Stack hub pro správu.

- Zálohování App Service a hlavních databází:
  - AppService_Hosting;
  - AppService_Metering;
  - Master

- Zálohování sdílené složky obsahu aplikace tenanta

- Zasyndikátte si **rozšíření vlastních skriptů** **1.9.1** z Marketplace.

### <a name="new-features-and-fixes"></a>Nové funkce a opravy

Azure App Service v Azure Stack centra aktualizace 6 obsahuje následující vylepšení a opravy:

- Aktualizace pro **App Service klientů, správců, funkcí portálů a nástrojů Kudu** Konzistentní s verzí sady SDK portálu Azure Stack hub.

- Aktualizuje **Azure Functions runtime** na **v 1.0.12299**.

- Aktualizace služby Core pro zlepšení spolehlivosti a chybového zasílání zpráv umožňují snazší diagnostiku běžných problémů.

- **Aktualizace následujících aplikačních architektur a nástrojů**:
  - ASP.NET Core 2.2.4
  - NodeJS 10.15.2
  - Zulu OpenJDK 8.36.0.1
  - Tomcat 7.0.81
  - Tomcat 8.5.37
  - Tomcat 9.0.14
  - PHP 5.6.39
  - PHP 7.0.33
  - PHP 7.1.25
  - PHP 7.2.13
  - Aktualizace Kudu na 81.10329.3844

- **Aktualizace základního operačního systému všech rolí**:
  - [2019-04 kumulativní aktualizace pro Windows Server 2016 pro systémy založené na platformě x64 (KB4493473)](https://support.microsoft.com/help/4493473/windows-10-update-kb4493473)

### <a name="post-deployment-steps"></a>Kroky po nasazení

> [!IMPORTANT]
> Pokud jste poskytli App Service poskytovatele prostředků s instancí SQL Always On, je nutné [přidat databáze appservice_hosting a appservice_metering do skupiny dostupnosti](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) a synchronizovat databáze, aby nedošlo ke ztrátě služeb v případě převzetí služeb při selhání databáze.

### <a name="known-issues-post-installation"></a>Známé problémy (po instalaci)

- Pokud je App Service nasazená v existující virtuální síti a souborový server je k dispozici jenom v privátní síti, je k dispozici pracovní proces, který se nemůže připojit k souborovému serveru, jak je vyvoláno v dokumentaci k nasazení centra Azure Stack Azure App Service.

Pokud se rozhodnete nasadit do existující virtuální sítě a interní IP adresu pro připojení k souborovému serveru, musíte přidat odchozí pravidlo zabezpečení, které povoluje provoz SMB mezi podsítí pracovních procesů a souborovým serverem. Na portálu pro správu přejdete na WorkersNsg a přidáte odchozí pravidlo zabezpečení s následujícími vlastnostmi:
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

Informace najdete v dokumentaci k [verzi centra Azure Stack 1908](/azure-stack/operator/release-notes?view=azs-1908) .

### <a name="known-issues-for-tenants-deploying-applications-on-azure-app-service-on-azure-stack-hub"></a>Známé problémy pro klienty nasazující aplikace v Azure App Service v centru Azure Stack

- Centrum nasazení je šedé

Klienti zatím nevyužívají centrum nasazení, což je funkce, která byla vydaná ve veřejném cloudu v pozdní 2018.  Klienti můžou pořád používat standardní metody nasazení (FTP, Nasazení webu, Git atd.) prostřednictvím portálu, rozhraní příkazového řádku a PowerShellu.

- Možnosti nasazení (Classic) uživatelské prostředí a možnosti portálu přihlašovací údaje nasazení nejsou k dispozici.

Aby bylo možné dosáhnout možností nasazení a přihlašovacích údajů pro nasazení uživatele v nasazení centra Azure Stack, mají klienti přístup k portálu pomocí tohoto formátu adresy URL – https://portal.&lt;&gt;*oblasti*&lt;*plně kvalifikovaný název domény*&gt;/? websitesExtension_oldvsts = true – ASDK by se [https://portal.local.azurestack.external/?websitesExtension_oldvsts=true](https://portal.local.azurestack.external/?websitesExtension_oldvsts=true) a pak na své aplikace normálně přejít.

- Azure Function monitor průběžně zobrazuje "načítání" na portálu.

Když se pokusíte monitorovat jednotlivé funkce, zobrazí se na portálu User Portal žádný protokol vyvolání, počet úspěchů nebo počet chyb.  Pokud chcete tuto funkci znovu povolit, přečtěte si **Function App**, navštivte **možnosti platformy**a pak přejít na **nastavení aplikace**.  Přidejte nové nastavení aplikace – název **AzureWebJobsDashboard** a nastavte hodnotu na stejnou hodnotu jako je nastavená v AzureWebJobsStorage.  Pak přejděte do zobrazení monitorování na vaší funkci a zobrazí se informace o monitorování.

## <a name="next-steps"></a>Další kroky

- Přehled Azure App Service najdete v tématu [Azure App Service v tématu Přehled centra Azure Stack](azure-stack-app-service-overview.md).
- Další informace o tom, jak připravit nasazení App Service v centru Azure Stack, najdete v tématu [před zahájením práce s App Service v Azure Stackovém centru](azure-stack-app-service-before-you-get-started.md).
