---
title: Architektura identity pro Azure Stack | Microsoft Docs
description: Přečtěte si o architektuře identity pro Azure Stack a rozdílech mezi Azure AD a AD FS.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/09/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: 364028183445df7e74828605439bfd7b3784f01f
ms.sourcegitcommit: 451cfaa24b349393f36ae9d646d4d311a14dd1fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/07/2019
ms.locfileid: "72019407"
---
# <a name="identity-architecture-for-azure-stack"></a>Architektura identity pro Azure Stack

Při volbě zprostředkovatele identity, který se má používat s Azure Stack, byste měli pochopit důležité rozdíly mezi možnostmi Azure Active Directory (Azure AD) a Active Directory Federation Services (AD FS) (AD FS).

## <a name="capabilities-and-limitations"></a>Možnosti a omezení

Vámi zvolený poskytovatel identity může omezit vaše možnosti, včetně podpory pro víceklientské prostředí.

|Funkce nebo scénář        |Azure AD  |AD FS  |
|------------------------------|----------|-------|
|Připojeno k Internetu     |Ano       |volitelná,|
|Podpora víceklientské architektury     |Ano       |Ne      |
|Nabídky položek na webu Marketplace |Ano       |Ano (vyžaduje použití nástroje pro [syndikaci offline Marketplace](azure-stack-download-azure-marketplace-item.md#disconnected-or-a-partially-connected-scenario) )|
|Podpora pro Active Directory Authentication Library (ADAL) |Ano |Ano|
|Podpora pro nástroje, jako je Azure CLI, Visual Studio a PowerShell  |Ano |Ano|
|Vytváření instančních objektů pomocí Azure Portal     |Ano |Ne|
|Vytváření instančních objektů pomocí certifikátů      |Ano |Ano|
|Vytváření instančních objektů s tajnými kódy (klíče)    |Ano |Ano|
|Aplikace mohou používat službu Graph Service           |Ano |Ne|
|Aplikace můžou poskytovatele identity použít k přihlášení. |Ano |Ano (vyžaduje, aby se aplikace federovat s místními instancemi AD FS) |

## <a name="topologies"></a>Topologie

Následující části popisují různé topologie identity, které můžete použít.

### <a name="azure-ad-single-tenant-topology"></a>Azure AD: topologie jednoho tenanta

Ve výchozím nastavení se při instalaci Azure Stack a používání služby Azure AD Azure Stack používá jedna topologie s jedním tenanta.

Topologie jednoho tenanta je užitečná v těchto případech:
- Všichni uživatelé jsou součástí stejného tenanta.
- Poskytovatel služeb je hostitelem instance Azure Stack pro organizaci.

![Azure Stack topologie s jedním tenantům pomocí Azure AD](media/azure-stack-identity-architecture/single-tenant.png)

Tato topologie obsahuje následující vlastnosti:

- Azure Stack zaregistruje všechny aplikace a služby do stejného adresáře tenanta Azure AD.
- Azure Stack ověřuje pouze uživatele a aplikace z tohoto adresáře, včetně tokenů.
- Identity pro správce (cloudové operátory) a uživatele klientů jsou ve stejném tenantovi adresáře.
- Pokud chcete uživateli z jiného adresáře povolit přístup k tomuto Azure Stack prostředí, musíte [uživatele pozvat jako host](azure-stack-identity-overview.md#guest-users) do adresáře tenanta.

### <a name="azure-ad-multi-tenant-topology"></a>Azure AD: topologie s více klienty

Operátoři cloudu můžou Azure Stack nakonfigurovat tak, aby umožňovaly přístup k aplikacím z jedné nebo více organizací. Uživatelé přistupují k aplikacím prostřednictvím portálu Azure Stack User Portal. V této konfiguraci je portál pro správu (používaný operátorem cloudu) omezený jenom na uživatele z jednoho adresáře.

Topologie s více klienty je užitečná v těchto případech:

- Poskytovatel služeb chce uživatelům z více organizací dovolit přístup k Azure Stack.

![Azure Stack topologie s více klienty pomocí Azure AD](media/azure-stack-identity-architecture/multi-tenant.png)

Tato topologie obsahuje následující vlastnosti:

- Přístup k prostředkům by měl být na základě jednotlivých organizací.
- Uživatelé z jedné organizace by neměli mít oprávnění k přístupu k prostředkům uživatelům, kteří jsou mimo svou organizaci.
- Identity pro správce (cloudové operátory) můžou být v klientovi samostatného adresáře od identit pro uživatele. Toto oddělení poskytuje izolaci účtu na úrovni poskytovatele identity.
 
### <a name="ad-fs"></a>AD FS

AD FS topologie je vyžadována, pokud je splněna jedna z následujících podmínek:

- Azure Stack se nepřipojí k Internetu.
- Azure Stack se může připojit k Internetu, ale vy se rozhodnete použít AD FS pro poskytovatele identity.
  
![Azure Stack topologie pomocí AD FS](media/azure-stack-identity-architecture/adfs.png)

Tato topologie obsahuje následující vlastnosti:

- Aby bylo možné podporovat použití této topologie v produkčním prostředí, je nutné integrovat integrovanou instanci Azure Stack AD FS s existující instancí AD FS, která je založená na službě Active Directory, prostřednictvím vztahu důvěryhodnosti federace.
- Službu Graph Service v Azure Stack můžete integrovat s existující instancí služby Active Directory. Můžete také použít službu Graph API založenou na OData, která podporuje rozhraní API, která jsou konzistentní s Graph API Azure AD.

  Aby bylo možné pracovat s instancí služby Active Directory, Graph API vyžaduje přihlašovací údaje uživatele z vaší instance služby Active Directory, které mají oprávnění jen pro čtení.
  - Integrovaná instance AD FS je založena na systému Windows Server 2016.
  - Vaše AD FS a instance služby Active Directory musí být založené na Windows Serveru 2012 nebo novějším.
  
  Mezi vaší instancí služby Active Directory a integrovanou instancí AD FS nejsou interakce omezeny na OpenID připojení a mohou používat jakýkoli vzájemně podporovaný protokol.
  - Uživatelské účty se vytvářejí a spravují v místní instanci služby Active Directory.
  - Instanční objekty a registrace pro aplikace se spravují v předdefinované instanci služby Active Directory.

## <a name="next-steps"></a>Další kroky

- [Přehled identity](azure-stack-identity-overview.md)
- [Integrace Datacenter – identita](azure-stack-integrate-identity.md)
