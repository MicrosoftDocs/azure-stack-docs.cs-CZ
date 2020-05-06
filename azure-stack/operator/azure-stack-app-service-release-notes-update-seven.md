---
title: Zpráva k vydání verze pro Azure Stack centra aktualizace 7 App Service
description: Update 7 poznámky k verzi pro App Service v centru Azure Stack, včetně nových funkcí, oprav a známých problémů.
author: apwestgarth
manager: stefsch
ms.topic: article
ms.date: 10/11/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 08/20/2019
ms.openlocfilehash: 824dd1304f027c1348eff2faad29d3f187e9732f
ms.sourcegitcommit: c263a86d371192e8ef2b80ced2ee0a791398cfb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2020
ms.locfileid: "82847737"
---
# <a name="app-service-on-azure-stack-hub-update-7-release-notes"></a>Zpráva k vydání verze pro Azure Stack centra aktualizace 7 App Service

Tyto poznámky k verzi popisují nové funkce, opravy a známé problémy v Azure App Service v centru Azure Stack Update 7. Známé problémy jsou rozdělené do dvou částí: problémy související s procesem upgradu a problémy se sestavením (po instalaci).

> [!IMPORTANT]
> Před nasazením Azure App Service 1,7 použijte aktualizaci 1910 na integrovaný systém Azure Stack nebo nasaďte nejnovější Azure Stack Development Kit (ASDK).

## <a name="build-reference"></a>Odkaz na sestavení

App Service číslo buildu Azure Stack centra aktualizace 7 je **84.0.2.10**.

## <a name="prerequisites"></a>Požadavky

Než začnete s nasazením, přečtěte si téma [předpoklady pro nasazení App Service v centru Azure Stack](azure-stack-app-service-before-you-get-started.md) .

Než začnete s upgradem Azure App Service v centru Azure Stack na 1,7:

- Ujistěte se, že všechny role jsou připravené v Azure App Service správě na portálu Azure Stack správce centra.

- Zálohování App Service tajných kódů pomocí správy App Service na portálu pro správu centra Azure Stack

- Zálohování App Service a hlavních databází:
  - AppService_Hosting;
  - AppService_Metering;
  - master

- Zálohujte sdílenou složku obsahu aplikace tenanta.

  > [!Important]
  > Operátoři cloudu zodpovídají za údržbu a provoz souborového serveru a SQL Server.  Poskytovatel prostředků tyto prostředky nespravuje.  Operátor cloudu zodpovídá za zálohování databází App Service a sdílené složky obsahu tenanta.

- Zasyndikátte si **rozšíření vlastních skriptů** **1.9.3** z Marketplace centra Azure Stack.

## <a name="new-features-and-fixes"></a>Nové funkce a opravy

Azure App Service v centru Azure Stack s aktualizací Update 7 obsahuje následující vylepšení a opravy:

- Řešení chyby zabezpečení v [CVE-2019-1372](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-1372) vzdálené spuštění kódu.

- Aktualizace pro **App Service tenanta, správce, funkcí portáls a nástroje Kudu** Konzistentní s verzí sady SDK portálu Azure Stack hub.

- Aktualizuje **Azure Functions runtime** na **v 1.0.12582**.

- Aktualizace služby Core pro zlepšení spolehlivosti a chybového zasílání zpráv umožňují snazší diagnostiku běžných problémů.

- **Aktualizuje následující aplikační architektury a nástroje**:

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
  - Uživatelé teď můžou nakonfigurovat omezení přístupu pro svoje aplikace Web/API/Functions podle dokumentace publikované v dokumentaci – [Azure App Service omezení přístupu](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions).
  
  > [!NOTE]
  > Azure App Service v centru Azure Stack nepodporuje koncové body služby.

- **Obnovena funkčnost možností nasazení (Classic)**:
  - Uživatelé můžou znovu použít možnosti nasazení (Classic) ke konfiguraci nasazení svých aplikací z GitHubu, BitBucket, Dropboxu, OneDrivu, místního a externího úložiště a nastavení přihlašovacích údajů nasazení pro své aplikace.

- **Monitorování funkcí Azure** je správně nakonfigurované.

- **Chování služby Windows Update**: na základě zpětné vazby od zákazníků jsme změnili způsob, jakým web Windows Update konfigurace App Service role z aktualizace 7:
  - Tři režimy:
    - **Zakázaná** služba web Windows Update zakázaná, Windows se aktualizuje s využitím KB, která se dodává s Azure App Service v Azure Stackch vydáních centra.
    - Služba **automatické** web Windows Update povolena a web Windows Update určuje, jak a kdy se má aktualizovat.
    - Služba **Managed** -web Windows Update je zakázaná, Azure App Service při zahájení provádění jednotlivých rolí provede web Windows Update cyklus.

  **Nové** Nasazení – služba web Windows Update je ve výchozím nastavení zakázaná.

  **Existující** Nasazení – Pokud jste v řadiči změnili nastavení, hodnota se změní z **false** na **disabled** a předchozí hodnota **true** se změní na **automaticky**.

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

Informace najdete v dokumentaci k [verzi centra Azure Stack 1907](azure-stack-release-notes-1907.md) .

## <a name="next-steps"></a>Další kroky

- Přehled Azure App Service najdete v tématu [Azure App Service a Azure Functions v článku Přehled centra pro Azure Stack](azure-stack-app-service-overview.md).
- Další informace o přípravě nasazení App Service v centru Azure Stack najdete v tématu [předpoklady pro nasazení App Service na Azure Stack hub](azure-stack-app-service-before-you-get-started.md).
