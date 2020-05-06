---
title: Architektura identity pro centrum Azure Stack
description: Seznamte se s architekturou identity pro centrum Azure Stack a rozdíly mezi Azure AD a AD FS.
author: BryanLa
ms.topic: conceptual
ms.date: 04/10/2020
ms.author: bryanla
ms.reviewer: fiseraci
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: d21df4d1a16f7ea56ec02a1aa1e7821bb7fe4484
ms.sourcegitcommit: c263a86d371192e8ef2b80ced2ee0a791398cfb7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2020
ms.locfileid: "82848230"
---
# <a name="identity-architecture-for-azure-stack-hub"></a>Architektura identity pro centrum Azure Stack

Při volbě zprostředkovatele identity, který se má používat s Azure Stack hub, byste měli pochopit důležité rozdíly mezi možnostmi Azure Active Directory (Azure AD) a Active Directory Federation Services (AD FS) (AD FS).

## <a name="capabilities-and-limitations"></a>Možnosti a omezení

Vámi zvolený poskytovatel identity může omezit vaše možnosti, včetně podpory pro víceklientské prostředí.

|Funkce nebo scénář        |Azure AD  |AD FS  |
|------------------------------|----------|-------|
|Připojeno k Internetu     |Ano       |Nepovinné|
|Podpora víceklientské architektury     |Ano       |Ne      |
|Nabídky položek na webu Marketplace |Ano       |Ano (vyžaduje použití nástroje pro [syndikaci offline Marketplace](azure-stack-download-azure-marketplace-item.md?pivots=state-disconnected) )|
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

Ve výchozím nastavení se při instalaci centra Azure Stack a používání služby Azure AD používá služba Azure Stack hub k jednomu tenantovi topologie.

Topologie jednoho tenanta je užitečná v těchto případech:
- Všichni uživatelé jsou součástí stejného tenanta.
- Poskytovatel služeb je hostitelem instance centra Azure Stack pro organizaci.

![Topologie Single-tenant centra Azure Stack s Azure AD](media/azure-stack-identity-architecture/single-tenant.svg)

Tato topologie obsahuje následující vlastnosti:

- Centrum Azure Stack registruje všechny aplikace a služby do stejného adresáře tenanta Azure AD.
- Centrum Azure Stack ověřuje pouze uživatele a aplikace z tohoto adresáře, včetně tokenů.
- Identity pro správce (cloudové operátory) a uživatele klientů jsou ve stejném tenantovi adresáře.
- Pokud chcete uživateli z jiného adresáře povolit přístup k tomuto Azure Stack centrálnímu prostředí, musíte [uživatele pozvat jako host](azure-stack-identity-overview.md#guest-users) do adresáře tenanta.

### <a name="azure-ad-multi-tenant-topology"></a>Azure AD: topologie s více klienty

Operátoři cloudu můžou nakonfigurovat centrum Azure Stack tak, aby umožňovalo přístup k aplikacím z jedné nebo více organizací. Uživatelé přistupují k aplikacím prostřednictvím portálu pro uživatele centra Azure Stack. V této konfiguraci je portál pro správu (používaný operátorem cloudu) omezený jenom na uživatele z jednoho adresáře.

Topologie s více klienty je užitečná v těchto případech:

- Poskytovatel služeb chce uživatelům z více organizací dovolit přístup k centru Azure Stack.

![Topologie víceklientské architektury centra Azure Stack s Azure AD](media/azure-stack-identity-architecture/multi-tenant.svg)

Tato topologie obsahuje následující vlastnosti:

- Přístup k prostředkům by měl být na základě jednotlivých organizací.
- Uživatelé z jedné organizace by neměli mít oprávnění k přístupu k prostředkům uživatelům, kteří jsou mimo svou organizaci.
- Identity pro správce (cloudové operátory) můžou být v klientovi samostatného adresáře od identit pro uživatele. Toto oddělení poskytuje izolaci účtu na úrovni poskytovatele identity.
 
### <a name="ad-fs"></a>AD FS

AD FS topologie je vyžadována, pokud je splněna jedna z následujících podmínek:

- Centrum Azure Stack se nepřipojuje k Internetu.
- Centrum Azure Stack se může připojit k Internetu, ale vy se rozhodnete používat AD FS pro poskytovatele identity.
  
![Azure Stack topologie centra pomocí AD FS](media/azure-stack-identity-architecture/adfs.svg)

Tato topologie obsahuje následující vlastnosti:

- Aby bylo možné podporovat použití této topologie v produkčním prostředí, je nutné integrovat integrovanou instanci AD FS centra Azure Stack s existující instancí AD FS, která je založená na službě Active Directory, prostřednictvím vztahu důvěryhodnosti federace.
- Službu Graph Service můžete integrovat do centra Azure Stack s existující instancí služby Active Directory. Můžete také použít službu Graph API založenou na OData, která podporuje rozhraní API, která jsou konzistentní s Graph API Azure AD.

  Aby bylo možné pracovat s instancí služby Active Directory, Graph API vyžaduje přihlašovací údaje uživatele z vaší instance služby Active Directory, které mají oprávnění jen pro čtení.
  - Integrovaná instance AD FS je založena na systému Windows Server 2016.
  - Vaše AD FS a instance služby Active Directory musí být založené na Windows Serveru 2012 nebo novějším.
  
  Mezi vaší instancí služby Active Directory a integrovanou instancí AD FS nejsou interakce omezeny na OpenID připojení a mohou používat jakýkoli vzájemně podporovaný protokol.
  - Uživatelské účty se vytvářejí a spravují v místní instanci služby Active Directory.
  - Instanční objekty a registrace pro aplikace se spravují v předdefinované instanci služby Active Directory.

## <a name="next-steps"></a>Další kroky

- [Přehled identity](azure-stack-identity-overview.md)
- [Integrace Datacenter – identita](azure-stack-integrate-identity.md)
