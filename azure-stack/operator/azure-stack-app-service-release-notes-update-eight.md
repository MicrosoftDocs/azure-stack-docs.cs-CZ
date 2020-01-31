---
title: Zpráva k vydání verze pro Azure Stack centra aktualizace 8 App Service
description: Seznamte se s novinkami Update 8 pro App Service v centru Azure Stack, známých problémech a na tom, kde si tuto aktualizaci stáhli.
author: apwestgarth
manager: stefsch
ms.topic: article
ms.date: 01/13/2020
ms.author: anwestg
ms.reviewer: ''
ms.openlocfilehash: 639c9267a9d42b20a15bc30ab6b72706816bf7ee
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76874480"
---
# <a name="app-service-on-azure-stack-hub-update-8-release-notes"></a>Zpráva k vydání verze pro Azure Stack centra aktualizace 8 App Service

Tyto poznámky k verzi popisují vylepšení a opravy v Azure App Service v centru Azure Stack aktualizace 8 a všech známých problémech. Známé problémy jsou rozděleny do potíží přímo souvisejících s nasazením, procesem aktualizace a problémy se sestavením (po instalaci).

> [!IMPORTANT]
> Před nasazením Azure App Service 1,8 použijte aktualizaci 1910 pro váš Azure Stack integrovaný systém nebo nasaďte nejnovější Azure Stack vývojovou sadu.


## <a name="build-reference"></a>Referenční informace o buildu

App Service číslo buildu Azure Stack centra aktualizace 8 je **86.0.2.13**

### <a name="prerequisites"></a>Požadavky

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

### <a name="post-deployment-steps"></a>Kroky po nasazení

> [!IMPORTANT]
> Pokud jste poskytli App Service poskytovatele prostředků s instancí SQL Always On, je nutné [přidat databáze appservice_hosting a appservice_metering do skupiny dostupnosti](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) a synchronizovat databáze, aby nedošlo ke ztrátě služeb v případě převzetí služeb při selhání databáze.

### <a name="known-issues-post-installation"></a>Známé problémy (po instalaci)

- Pokud je App Service nasazená ve stávající virtuální síti a souborový server je k dispozici pouze v privátní síti, je k dispozici pracovní proces, který se nemůže připojit k souborovému serveru, jak je uvedeno v dokumentaci k nasazení Azure Stack Azure App Service.

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

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Známé problémy pro cloudové správce pracující Azure App Service v Azure Stack

Informace najdete v dokumentaci k [verzi Azure Stack 1907](azure-stack-release-notes-1907.md) .

## <a name="next-steps"></a>Další kroky

- Přehled Azure App Service najdete v tématu [přehled Azure App Service na Azure Stack](azure-stack-app-service-overview.md).
- Další informace o tom, jak připravit nasazení App Service v Azure Stack, najdete v tématu [před zahájením práce s App Service na Azure Stack](azure-stack-app-service-before-you-get-started.md).
