---
title: App Service ve Azure Stack centra aktualizace 7 – poznámky k verzi | Microsoft Docs
description: Seznamte se s novinkami Update 7 pro App Service v centru Azure Stack, známých problémech a na tom, kde si tuto aktualizaci stáhli.
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
ms.date: 10/11/2019
ms.author: anwestg
ms.reviewer: ''
ms.openlocfilehash: ca1206a23f4f275304b975164dd757f274cc000c
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2020
ms.locfileid: "75804499"
---
# <a name="app-service-on-azure-stack-hub-update-7-release-notes"></a>Zpráva k vydání verze pro Azure Stack centra aktualizace 7 App Service

*Platí pro: Azure Stack integrovaných systémů centra a Azure Stack Development Kit*

Tyto poznámky k verzi popisují vylepšení a opravy v Azure App Service ve službě Azure Stack centra aktualizace 7 a jakékoli známé problémy. Známé problémy jsou rozděleny do potíží přímo souvisejících s nasazením, procesem aktualizace a problémy se sestavením (po instalaci).

> [!IMPORTANT]
> Před nasazením Azure App Service 1,7 použijte aktualizaci 1907 pro integrovaný systém Azure Stack hub nebo nasaďte nejnovější Azure Stack vývojovou sadu.


## <a name="build-reference"></a>Referenční informace o buildu

App Service číslo buildu Azure Stack centra aktualizace 7 je **84.0.2.10**

### <a name="prerequisites"></a>Požadavky

Než začnete s nasazením, přečtěte si [dokumentaci před](azure-stack-app-service-before-you-get-started.md) začátkem.

Než začnete s upgradem Azure App Service v centru Azure Stack na 1,7:

- Zajistěte, aby všechny role byly připravené ve správě Azure App Service na portálu Azure Stack hub pro správu.

- Zálohování App Service a hlavních databází:
  - AppService_Hosting;
  - AppService_Metering;
  - Master

- Zálohování sdílené složky obsahu aplikace tenanta

- Zasyndikátte si **rozšíření vlastních skriptů** **1.9.3** z Marketplace.

### <a name="new-features-and-fixes"></a>Nové funkce a opravy

Azure App Service v centru Azure Stack s aktualizací Update 7 obsahuje následující vylepšení a opravy:

- Řešení chyby zabezpečení v [CVE-2019-1372](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-1372) vzdálené spuštění kódu

- Aktualizace pro **App Service klientů, správců, funkcí portálů a nástrojů Kudu** Konzistentní s verzí sady SDK portálu Azure Stack hub.

- Aktualizuje **Azure Functions runtime** na **v 1.0.12582**.

- Aktualizace služby Core pro zlepšení spolehlivosti a chybového zasílání zpráv umožňují snazší diagnostiku běžných problémů.

- **Aktualizace následujících aplikačních architektur a nástrojů**:
  - ASP.NET Core 2.2.46
  - Zul OpenJDK 8.38.0.13
  - Tomcat 7.0.94
  - Tomcat 8.5.42
  - Tomcat 9.0.21
  - PHP 5.6.40
  - PHP 7.3.6
  - Aktualizace Kudu na 82.10503.3890

- **Aktualizace základního operačního systému všech rolí**:
  - [2019-08 kumulativní aktualizace pro Windows Server 2016 pro systémy založené na platformě x64 (KB4512495)](https://support.microsoft.com/help/4512495)

- **Na portálu User Portal jsou nyní povolena omezení přístupu**:
  - Od tohoto vydání můžou uživatelé nakonfigurovat omezení přístupu pro své aplikace Web/API/Functions podle dokumentace publikované v dokumentaci – [Azure App Service omezení přístupu](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions), **Poznámka**: Azure App Service v centru pro Azure Stack nepodporuje koncové body služby.

- **Obnovena funkčnost možností nasazení (Classic)** :
  - Uživatelé můžou znovu použít možnosti nasazení (Classic) ke konfiguraci nasazení svých aplikací z GitHubu, BitBucket, Dropboxu, OneDrivu, místních i externích úložišť a k nastavení přihlašovacích údajů nasazení pro své aplikace.

- **Monitorování funkcí Azure** je správně nakonfigurované.

- **Chování web Windows Update**: na základě zpětné vazby od zákazníků jsme změnili způsob, jakým jsou web Windows Update nakonfigurovaná na role App Service z aktualizace 7:
  - Tři režimy:
    - **Zakázaná** služba web Windows Update zakázaná, Windows se bude aktualizovat s KB, která se dodává s Azure App Service v Azure Stackch vydáních centra.
    - Služba **automatické** web Windows Update povolena a web Windows Update určuje, jak a kdy se má aktualizovat.
    - Služba **Managed** -web Windows Update je zakázaná, Azure App Service při zahájení provádění jednotlivých rolí provede web Windows Update cyklus.

  **Nové** Nasazení – služba web Windows Update je ve výchozím nastavení zakázaná.

  **Existující** Nasazení – Pokud jste v řadiči změnili nastavení, hodnota se teď změní z **false** na **zakázaná** a předchozí hodnota **true** se změní na **automaticky** .

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

Informace najdete v dokumentaci k [verzi centra Azure Stack 1907](azure-stack-release-notes-1907.md) .

## <a name="next-steps"></a>Další kroky

- Přehled Azure App Service najdete v tématu [Azure App Service v tématu Přehled centra Azure Stack](azure-stack-app-service-overview.md).
- Další informace o tom, jak připravit nasazení App Service v centru Azure Stack, najdete v tématu [před zahájením práce s App Service v Azure Stackovém centru](azure-stack-app-service-before-you-get-started.md).
