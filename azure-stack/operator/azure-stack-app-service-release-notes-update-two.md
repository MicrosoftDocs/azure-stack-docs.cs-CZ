---
title: Zpráva k vydání verze Azure Stack centra aktualizace 2 App Service
description: Přečtěte si o vylepšeních, opravách a známých problémech v aktualizaci Update 2 pro App Service v centru Azure Stack.
author: bryanla
manager: stefsch
ms.topic: article
ms.date: 03/25/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 05/18/2019
ms.openlocfilehash: 2c80aafe3665b6ecb31e701483a5e903d0471e42
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77703413"
---
# <a name="app-service-on-azure-stack-hub-update-2-release-notes"></a>Zpráva k vydání verze Azure Stack centra aktualizace 2 App Service

Tyto poznámky k verzi popisují vylepšení, opravy a známé problémy v Azure App Service v Azure Stack centra aktualizace 2. Známé problémy jsou rozdělené na tři části: problémy přímo související s nasazením, problémy s procesem aktualizace a problémy se sestavením (po instalaci).

> [!IMPORTANT]
> Před nasazením Azure App Service 1,2 použijte aktualizaci 1804 pro integrovaný systém Azure Stack hub nebo nasaďte nejnovější Azure Stack Development Kit (ASDK).

## <a name="build-reference"></a>Referenční informace o buildu

App Service číslo buildu Azure Stack centra aktualizace 2 je **72.0.13698.10**.

### <a name="prerequisites"></a>Požadavky

> [!IMPORTANT]
> Nová nasazení Azure App Service v centru Azure Stack nyní vyžadují [certifikát se zástupným znakem tři subjektu](azure-stack-app-service-before-you-get-started.md#get-certificates) z důvodu vylepšení způsobu, jakým se v Azure App Service zpracovává jednotné přihlašování pro Kudu. Nový předmět je: **\*. SSO. AppService.\<region\>.\<domainname\>.\<rozšíření\>**

Než začnete s nasazením, přečtěte si [požadavky pro nasazení App Service v centru Azure Stack](azure-stack-app-service-before-you-get-started.md) .

### <a name="new-features-and-fixes"></a>Nové funkce a opravy

Azure App Service v Azure Stack centra aktualizace 2 obsahuje následující vylepšení a opravy:

- Aktualizace pro **App Service klientů, správců, funkcí portálů a nástrojů Kudu** Konzistentní s verzí sady SDK portálu Azure Stack hub.

- Aktualizuje **Azure Functions runtime** na **v 1.0.11612**.

- Aktualizace služby Core pro zlepšení spolehlivosti a chybového zasílání zpráv umožňují snazší diagnostiku běžných problémů.

- **Aktualizace následujících aplikačních architektur a nástrojů**:
  - Přidáno .NET Framework 4.7.1
  - Přidané verze **Node. js** :
    - NodeJS 6.12.3
    - NodeJS 8.9.4
    - NodeJS 8.10.0
    - NodeJS 8.11.1
  - Přidané verze **npm** :
    - 5.6.0
  - Součásti .NET Core se aktualizovaly tak, aby byly konzistentní s Azure App Service ve veřejném cloudu.
  - Aktualizovaný Kudu

- Automatické prohození funkce slotů nasazení je povolené – [konfiguruje se automatické prohození](https://docs.microsoft.com/azure/app-service/deploy-staging-slots#configure-auto-swap).

- Testování v produkční funkci je povolené – [Úvod do testování v produkčním](https://azure.microsoft.com/resources/videos/introduction-to-azure-websites-testing-in-production-with-galin-iliev/)prostředí.

- Proxy služby Azure Functions povoleno – [práce s proxy služby Azure Functions](https://docs.microsoft.com/azure/azure-functions/functions-proxies).

- Přidala se podpora uživatelského rozhraní rozšíření Správce App Service pro:
  - Rotace tajných klíčů
  - Rotace certifikátu
  - Rotace přihlašovacích údajů systému
  - Rotace připojovacího řetězce

### <a name="known-issues-post-installation"></a>Známé problémy (po instalaci)

- Pokud je App Service nasazená ve stávající virtuální síti a souborový server je k dispozici jenom v privátní síti, zaměstnanci nemůžou kontaktovat souborový server.

Pokud se rozhodnete nasadit do existující virtuální sítě a interní IP adresu pro připojení k souborovému serveru, musíte přidat odchozí pravidlo zabezpečení, které umožňuje provoz protokolu SMB mezi podsítí pracovních procesů a souborovým serverem. Na portálu pro správu přejdete na WorkersNsg a přidáte odchozí pravidlo zabezpečení s následujícími vlastnostmi:

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

Informace najdete v dokumentaci k [verzi centra Azure Stack 1804](azure-stack-update-1903.md) .

## <a name="next-steps"></a>Další kroky

- Přehled Azure App Service najdete v tématu [Azure App Service v tématu Přehled centra Azure Stack](azure-stack-app-service-overview.md).
- Další informace o přípravě nasazení App Service v centru Azure Stack najdete v tématu [předpoklady pro nasazení App Service na Azure Stack hub](azure-stack-app-service-before-you-get-started.md).
