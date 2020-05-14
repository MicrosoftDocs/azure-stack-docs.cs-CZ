---
title: App Service zpráva k vydání verze Azure Stack centra pro aktualizaci 6
description: 'Aktualizace 6: zpráva k vydání verze pro App Service v centru Azure Stack, včetně nových funkcí, oprav a známých problémů.'
author: apwestgarth
manager: stefsch
ms.topic: article
ms.date: 06/24/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 08/20/2019
ms.openlocfilehash: 974b338ff7da9bd4442d4aadc02d475bd01686df
ms.sourcegitcommit: ddcd083430ca905653d412dc2f7b813218d79509
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/13/2020
ms.locfileid: "83374626"
---
# <a name="app-service-on-azure-stack-hub-update-6-release-notes"></a>App Service zpráva k vydání verze Azure Stack centra pro aktualizaci 6

Tyto poznámky k verzi popisují nové funkce, opravy a známé problémy v Azure App Service v centru Azure Stack Update 6. Známé problémy jsou rozdělené do dvou částí: problémy související s procesem upgradu a problémy se sestavením (po instalaci).

> [!IMPORTANT]
> Před nasazením Azure App Service 1,6 použijte aktualizaci 1904 pro integrovaný systém Azure Stack hub nebo nasaďte nejnovější Azure Stack Development Kit (ASDK).

## <a name="build-reference"></a>Referenční informace o buildu

App Service číslo buildu Azure Stack centra aktualizace č. 6 je **82.0.1.50**.

## <a name="prerequisites"></a>Požadavky

Než začnete s nasazením, přečtěte si téma [předpoklady pro nasazení App Service v centru Azure Stack](azure-stack-app-service-before-you-get-started.md) .

Než začnete s upgradem Azure App Service v centru Azure Stack na 1,6:

- Ujistěte se, že všechny role jsou připravené v Azure App Service správě na portálu Azure Stack správce centra.

- Zálohování App Service tajných kódů pomocí správy App Service na portálu pro správu centra Azure Stack

- Zálohování App Service a hlavních databází:
  - AppService_Hosting;
  - AppService_Metering;
  - master

- Zálohujte sdílenou složku obsahu aplikace tenanta.

  > [!Important]
  > Operátoři cloudu zodpovídají za údržbu a provoz souborového serveru a SQL Server.  Poskytovatel prostředků tyto prostředky nespravuje.  Operátor cloudu zodpovídá za zálohování databází App Service a sdílené složky obsahu tenanta.

- Zasyndikátte si **rozšíření vlastních skriptů** **1.9.1** z Marketplace centra Azure Stack.

## <a name="new-features-and-fixes"></a>Nové funkce a opravy

Azure App Service v Azure Stack centra aktualizace 6 obsahuje následující vylepšení a opravy:

- Aktualizace pro **App Service tenanta, správce, funkcí portáls a nástroje Kudu** Konzistentní s verzí sady SDK portálu Azure Stack hub.

- Aktualizuje **Azure Functions runtime** na **v 1.0.12299**.

- Aktualizace služby Core pro zlepšení spolehlivosti a chybového zasílání zpráv umožňují snazší diagnostiku běžných problémů.

- **Aktualizuje následující aplikační architektury a nástroje**:

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

## <a name="post-deployment-steps"></a>Kroky po nasazení

> [!IMPORTANT]
> Pokud jste poskytli App Service poskytovatele prostředků s instancí SQL Always On, je nutné [přidat databáze appservice_hosting a appservice_metering do skupiny dostupnosti](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) a synchronizovat databáze, aby nedošlo ke ztrátě služeb v případě převzetí služeb při selhání databáze.

## <a name="known-issues-post-installation"></a>Známé problémy (po instalaci)

- Pokud je App Service nasazená v existující virtuální síti a souborový server je k dispozici jenom v privátní síti, je k dispozici pracovní proces, který se nemůže připojit k souborovému serveru, jak je vyvoláno v dokumentaci k nasazení centra Azure Stack Azure App Service.

Pokud se rozhodnete nasadit do existující virtuální sítě a interní IP adresu pro připojení k souborovému serveru, musíte přidat odchozí pravidlo zabezpečení, které povoluje provoz SMB mezi podsítí pracovních procesů a souborovým serverem. Na portálu pro správu přejdete na WorkersNsg a přidáte odchozí pravidlo zabezpečení s následujícími vlastnostmi:

* Zdroj: Any
* Rozsah zdrojových portů: *
* Cíl: IP adresy
* Rozsah cílových IP adres: rozsah IP adres pro souborový server
* Rozsah cílových portů: 445
* Protokol. TCP
* Akce: Povolit
* Priorita: 700
* Název: Outbound_Allow_SMB445

## <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack-hub"></a>Známé problémy pro Cloud Admins, které pracují Azure App Service v centru Azure Stack

Informace najdete v [poznámkách k verzi centra Azure Stack 1908](/azure-stack/operator/release-notes?view=azs-1908).

## <a name="known-issues-for-tenants-deploying-applications-on-azure-app-service-on-azure-stack-hub"></a>Známé problémy pro klienty nasazující aplikace v Azure App Service v centru Azure Stack

- Centrum nasazení je šedé nebo není k dispozici.

    Klienti zatím nemůžou používat centrum nasazení, což je funkce, která byla vydaná ve veřejném cloudu v pozdní 2018. Klienti můžou pořád používat standardní metody nasazení (FTP, Nasazení webu, Git a tak dále) prostřednictvím portálu, rozhraní příkazového řádku a PowerShellu.

- Možnosti nasazení a možnosti portálu přihlašovací údaje pro nasazení (Classic) nejsou k dispozici.

    Aby se dosáhlo možností nasazení a přihlašovacích údajů k nasazení uživatelského rozhraní v nasazení centra Azure Stack, klienti by měli mít přístup k portálu pomocí tohoto formátu adresy URL: `https://portal.&lt;*region*&gt;.&lt;*FQDN*&gt;/?websitesExtension_oldvsts=true` – to znamená, že by měl ASDK `https://portal.local.azurestack.external/?websitesExtension_oldvsts=true` a pak přejít ke svým aplikacím.

- Azure Function monitor průběžně zobrazuje "načítání" na portálu.

    Když se pokusíte monitorovat jednotlivé funkce na portálu User Portal, uvidíte, že nedošlo k žádnému protokolu vyvolání, počtu úspěchů nebo počtu chyb. Pokud chcete tuto funkci znovu povolit, přečtěte si **Function App**, navštivte **možnosti platformy**a pak přejít na **nastavení aplikace**.  Přidejte nové nastavení aplikace s názvem **AzureWebJobsDashboard** a nastavte hodnotu na stejnou hodnotu, jako je nastavena v AzureWebJobsStorage. Pak přejděte do zobrazení monitorování na vaší funkci a zobrazí se informace o monitorování.

## <a name="next-steps"></a>Další kroky

- Přehled Azure App Service najdete v tématu [Azure App Service a Azure Functions v článku Přehled centra pro Azure Stack](azure-stack-app-service-overview.md).
- Další informace o přípravě na nasazení App Service v Azure Stack najdete v tématu [předpoklady pro nasazení App Service na Azure Stack hub](azure-stack-app-service-before-you-get-started.md).