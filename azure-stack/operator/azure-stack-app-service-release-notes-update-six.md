---
title: App Service ve službě Azure Stack aktualizaci 6 poznámky k verzi | Dokumentace Microsoftu
description: Další informace o tom, co je v aktualizaci 6 pro službu App Service ve službě Azure Stack, známé problémy a kde se stáhnout aktualizaci.
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
ms.openlocfilehash: db403f68879efa9103e35bb3581801240c0d64d3
ms.sourcegitcommit: 1545e18a31cd715a12c7ddc3bcb173b41eb41730
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2019
ms.locfileid: "67348723"
---
# <a name="app-service-on-azure-stack-update-6-release-notes"></a>App Service v Azure stacku zpráva k vydání verze update 6

*Platí pro: Azure Stack integrované systémy a Azure Stack Development Kit*

Tyto poznámky k verzi popisují vylepšení a oprav ve službě Azure App Service v Azure stacku aktualizaci 6 a známých problémech. Známé problémy jsou rozděleny do týkající se přímo k nasazení, aktualizace a problémy se sestavením (po instalaci).

> [!IMPORTANT]
> Aktualizace 1904 do služby Azure Stack integrované systému nebo nasadit nejnovější sady Azure Stack development kit před nasazením Azure App Service 1.6.


## <a name="build-reference"></a>Referenční informace o buildu

App Service v Azure stacku aktualizaci 6 číslo sestavení je **82.0.1.50**

### <a name="prerequisites"></a>Požadavky

Odkazovat [před zahájením práce dokumentaci](azure-stack-app-service-before-you-get-started.md) před zahájením nasazení.

Před zahájením upgradu služby Azure App Service ve službě Azure Stack na 1.6:

- Zkontrolujte všechny role jsou připravené ve správě Azure App Service v portálu pro správu Azure Stack

- Zálohování služby App Service a hlavní databáze:
  - AppService_Hosting;
  - AppService_Metering;
  - Master

- Zálohování sdílené složky souboru obsahu klientskou aplikaci

- Publikování **rozšíření vlastních skriptů** verze **1.9.1** z Marketplace

### <a name="new-features-and-fixes"></a>Nové funkce a opravy

Azure App Service v Azure stacku aktualizaci 6 zahrnuje následující vylepšení a opravy:

- Aktualizace **aplikace služeb pro klienty, Admin, portály funkce a nástroje Kudu**. Konzistentní s verzí sady SDK portálu Azure Stack.

- Aktualizace **modul runtime služby Azure Functions** k **v1.0.12299**.

- Aktualizace základní službu, ke zlepšení spolehlivosti a chybových zpráv umožňuje snazší Diagnostika běžných problémů.

- **Aktualizace následujících aplikační architektury a nástroje**:
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
  - Aktualizované Kudu pro 81.10329.3844

- **Aktualizace základního operačního systému všech rolí**:
  - [2019-04 kumulativní aktualizace pro Windows Server 2016 pro systémy x64 (KB4493473)](https://support.microsoft.com/help/4493473/windows-10-update-kb4493473)

### <a name="post-deployment-steps"></a>Kroky po nasazení

> [!IMPORTANT]
> Pokud budete mít k dispozici poskytovatele prostředků App Service vždy na instanci SQL musíte [přidat do skupiny dostupnosti databáze appservice_hosting a appservice_metering](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) a synchronizaci databází, aby nedocházelo ke Služba v případě selhání databáze.

### <a name="known-issues-post-installation"></a>Známé problémy (po instalaci)

- Pracovní procesy se nám kontaktovat souborového serveru při nasazení služby App Service v existující virtuální sítě a souborový server je dostupná pouze na privátní sítě, jak je uvedeno ve službě Azure App Service v dokumentaci k nasazení Azure Stack.

Pokud jste se rozhodli nasadit do existující virtuální sítě a interní IP adresu pro připojení k souborovému serveru, je nutné přidat odchozí pravidlo zabezpečení, povolení provozu SMB mezi podsítě pracovního procesu a souborový server. Přejděte na WorkersNsg v portálu pro správu a přidat odchozí pravidlo zabezpečení s následujícími vlastnostmi:
 * Zdroj: Jakýkoli
 * Zdrojový rozsah portů: *
 * Cíl: IP adresy
 * Rozsah cílových IP adres: Rozsah IP adres pro souborový server
 * Rozsah cílových portů: 445
 * Protokol: TCP
 * Akce: Povolit
 * Priorita: 700
 * Název: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Známé problémy pro správce cloudu provoz služby Azure App Service ve službě Azure Stack

Přečtěte si dokumentaci v [zpráva k vydání verze Azure Stack 1904](azure-stack-release-notes-1904.md)

### <a name="known-issues-for-tenants-deploying-applications-on-azure-app-service-on-azure-stack"></a>Známé problémy pro klienty nasazení aplikací ve službě Azure App Service ve službě Azure Stack

- Nasazení System Center je šedá

Klienty nelze ještě využijte nasazení softwaru, což je funkce, která byla vydána ve veřejném cloudu v pozdní 2018.  Tenanty můžete dál používat metody standardní nasazení (FTP, nasazení webu, Git, atd.) prostřednictvím portálu, rozhraní příkazového řádku a Powershellu.

- Možnosti (Classic) uživatelské prostředí a nasazení přihlašovacích údajů portálu možnosti nasazení není k dispozici

Abyste dosáhli možnosti nasazení a nasazení přihlašovací údaje uživatelů v nasazení Azure Stack, tenanti by měl přístup k portálu pomocí formátu adresy URL - https://portal.&lt ; *oblast*&gt;.&lt; *Plně kvalifikovaný název domény*&gt; /? websitesExtension_oldvsts = true –, který by byl pro ASDK [ https://portal.local.azurestack.external/?websitesExtension_oldvsts=true ](https://portal.local.azurestack.external/?websitesExtension_oldvsts=true) a potom přejděte ke svým aplikacím normálně.

- "Načítání" monitorování funkce Azure průběžně zobrazuje na portálu

Při pokusu o sledování jednotlivých funkcí v portál pro klienty, zobrazí se žádný protokol volání, počet úspěchů nebo počet chyb.  Pokud chcete tuto funkci znovu povolit, přejděte na vaše **aplikace Function App**, přejděte na stránku **funkce platformy**a přejděte na **nastavení aplikace**.  Přidat nové nastavení aplikace – název **AzureWebJobsDashboard** a nastavte hodnotu na stejnou hodnotu jako sadu v AzureWebJobsStorage.  Pak přejděte do zobrazení monitorování k vaší funkci a zobrazí se informace monitorování.

## <a name="next-steps"></a>Další postup

- Přehled služby Azure App Service najdete v tématu [Azure App Service na Přehled služby Azure Stack](azure-stack-app-service-overview.md).
- Další informace o tom, jak připravit nasazení služby App Service ve službě Azure Stack najdete v tématu [před zahájením práce s App Service ve službě Azure Stack](azure-stack-app-service-before-you-get-started.md).
