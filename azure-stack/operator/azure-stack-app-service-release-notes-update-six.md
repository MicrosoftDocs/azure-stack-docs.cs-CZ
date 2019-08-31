---
title: App Service na Azure Stack Update 6 – zpráva k vydání verze | Microsoft Docs
description: Přečtěte si o tom, co je aktualizace šest pro App Service v Azure Stack, známých problémech a kde stáhnout aktualizaci.
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/24/2019
ms.author: anwestg
ms.reviewer: ''
ms.openlocfilehash: c142ecda309f00ee94429be3d7b8f3c6e8072668
ms.sourcegitcommit: 71d7990a2b21576c44bb2aea13ae2026e9510c55
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/30/2019
ms.locfileid: "70188214"
---
# <a name="app-service-on-azure-stack-update-6-release-notes"></a>App Service ve zprávách k vydání verze Azure Stack Update 6

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Tyto poznámky k verzi popisují vylepšení a opravy v Azure App Service na Azure Stack Update 6 a všech známých problémech. Známé problémy jsou rozděleny do potíží přímo souvisejících s nasazením, procesem aktualizace a problémy se sestavením (po instalaci).

> [!IMPORTANT]
> Před nasazením Azure App Service 1,6 použijte aktualizaci 1904 pro váš Azure Stack integrovaný systém nebo nasaďte nejnovější Azure Stack vývojovou sadu.


## <a name="build-reference"></a>Referenční informace o buildu

App Service číslo buildu Azure Stack Update 6 je **82.0.1.50**

### <a name="prerequisites"></a>Požadavky

Než začnete s nasazením, přečtěte si [dokumentaci před](azure-stack-app-service-before-you-get-started.md) začátkem.

Než začnete s upgradem Azure App Service v Azure Stack na 1,6:

- Ujistěte se, že všechny role jsou připravené ve správě Azure App Service na portálu pro správu Azure Stack.

- Zálohování App Service a hlavních databází:
  - AppService_Hosting;
  - AppService_Metering;
  - Předloha

- Zálohování sdílené složky obsahu aplikace tenanta

- Zasyndikátte si **rozšíření vlastních skriptů** **1.9.1** z Marketplace.

### <a name="new-features-and-fixes"></a>Nové funkce a opravy

Azure App Service na Azure Stack Update 6 obsahuje následující vylepšení a opravy:

- Aktualizace pro **App Service klientů, správců, funkcí portálů a nástrojů Kudu** Konzistentní s Azure Stack verze sady SDK portálu.

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
> Pokud jste poskytli App Service poskytovatele prostředků s instancí SQL Always On, musíte do [skupiny dostupnosti přidat databáze appservice_hosting a appservice_metering](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) a synchronizovat databáze, aby nedošlo ke ztrátě služeb v případě převzetí služeb při selhání databáze.

### <a name="known-issues-post-installation"></a>Známé problémy (po instalaci)

- Pokud je App Service nasazená ve stávající virtuální síti a souborový server je k dispozici pouze v privátní síti, je k dispozici pracovní proces, který se nemůže připojit k souborovému serveru, jak je uvedeno v dokumentaci k nasazení Azure Stack Azure App Service.

Pokud se rozhodnete nasadit do existující virtuální sítě a interní IP adresu pro připojení k souborovému serveru, musíte přidat odchozí pravidlo zabezpečení, které povoluje provoz SMB mezi podsítí pracovních procesů a souborovým serverem. Na portálu pro správu přejdete na WorkersNsg a přidáte odchozí pravidlo zabezpečení s následujícími vlastnostmi:
 * Zdroj: Any
 * Rozsah zdrojových portů: *
 * Cíl: Adresy IP
 * Rozsah cílových IP adres: Rozsah IP adres pro souborový server
 * Rozsah cílových portů: 445
 * Protokol: TCP
 * Akce: Allow
 * Priorita: 700
 * Název: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Známé problémy pro cloudové správce pracující Azure App Service v Azure Stack

Informace najdete v dokumentaci k [verzi Azure Stack 1908](azure-stack-release-notes-1908.md) .

### <a name="known-issues-for-tenants-deploying-applications-on-azure-app-service-on-azure-stack"></a>Známé problémy pro klienty nasazující aplikace na Azure App Service v Azure Stack

- Centrum nasazení je šedé

Klienti zatím nevyužívají centrum nasazení, což je funkce, která byla vydaná ve veřejném cloudu v pozdní 2018.  Klienti můžou pořád používat standardní metody nasazení (FTP, Nasazení webu, Git atd.) prostřednictvím portálu, rozhraní příkazového řádku a PowerShellu.

- Možnosti nasazení (Classic) uživatelské prostředí a možnosti portálu přihlašovací údaje nasazení nejsou k dispozici.

Aby bylo možné dosáhnout možností nasazení a přihlašovacích údajů pro nasazení uživatele v nasazení Azure Stack, musí mít klienti přístup k portálu pomocí tohoto formátu adresy URL https://portal.&lt: *oblast.* &gt;&lt; *Plně kvalifikovaný název domény* /? websitesExtension_oldvsts = true – to znamená, že ASDK by byla [https://portal.local.azurestack.external/?websitesExtension_oldvsts=true](https://portal.local.azurestack.external/?websitesExtension_oldvsts=true) a pak se normálně přesměrují na své aplikace. &gt;

- Azure Function monitor průběžně zobrazuje "načítání" na portálu.

Když se pokusíte monitorovat jednotlivé funkce, nebudete moct na portálu tenanta zobrazit protokol vyvolání, počet úspěchů nebo počet chyb.  Pokud chcete tuto funkci znovu povolit, přečtěte si **Function App**, navštivte **možnosti platformy**a pak přejít na **nastavení aplikace**.  Přidejte nové nastavení aplikace – název **AzureWebJobsDashboard** a nastavte hodnotu na stejnou hodnotu jako je nastavená v AzureWebJobsStorage.  Pak přejděte do zobrazení monitorování na vaší funkci a zobrazí se informace o monitorování.

## <a name="next-steps"></a>Další postup

- Přehled Azure App Service najdete v tématu [přehled Azure App Service na Azure Stack](azure-stack-app-service-overview.md).
- Další informace o tom, jak připravit nasazení App Service v Azure Stack, najdete v tématu [před zahájením práce s App Service na Azure Stack](azure-stack-app-service-before-you-get-started.md).
