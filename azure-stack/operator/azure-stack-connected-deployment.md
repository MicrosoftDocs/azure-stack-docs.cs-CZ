---
title: Rozhodnutí o nasazení připojená k Azure pro integrované systémy Azure Stack hub
description: Rozhodnutí o plánování nasazení pro nasazení integrovaných systémů Azure Stack hub připojená k Azure, včetně fakturace a identity, najdete v části nasazení.
author: PatAltimore
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: patricka
ms.reviewer: wfayed
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 6812ac4b0841e44b760ce3397c5a06b2051e0036
ms.sourcegitcommit: 5f3d37994b8cb63c76e54136c0cc05bc4f475950
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2021
ms.locfileid: "99495512"
---
# <a name="azure-connected-deployment-planning-decisions-for-azure-stack-hub-integrated-systems"></a>Rozhodnutí týkající se plánování nasazení spojené s Azure pro integrované systémy centra Azure Stack
Až se rozhodnete, [jak budete Azure Stack hub integrovat do svého hybridního cloudového prostředí](azure-stack-connection-models.md), můžete dokončit rozhodnutí o nasazení Azure Stack centra.

Nasazením centra Azure Stack připojeného k Azure můžete mít pro své úložiště identit buď Azure Active Directory (Azure AD), nebo Active Directory Federation Services (AD FS) (AD FS). Můžete si také vybrat z modelu fakturace: průběžné platby nebo využití na základě kapacity. Výchozím nastavením je připojené nasazení, protože umožňuje zákazníkům získat co nejvíc z centra Azure Stack, zejména v případě hybridních cloudových scénářů, které zahrnují centrum Azure i Azure Stack.

## <a name="choose-an-identity-store"></a>Výběr úložiště identity
S připojeným nasazením si můžete vybrat mezi Azure AD nebo AD FS pro úložiště identit. Odpojené nasazení, bez připojení k Internetu, může používat jenom AD FS.

Vaše volba úložiště identity nemá žádný vliv na virtuální počítače klienta. Virtuální počítače klientů si můžou zvolit úložiště identity, ke kterému se chtějí připojit, podle toho, jak se budou konfigurovat: Azure AD, Windows Server Active Directory – připojeno k doméně, pracovní skupina a tak dále. Nesouvisí s rozhodnutím poskytovatele identity centra Azure Stack.

Pokud například nasadíte virtuální počítače klienta IaaS do centra Azure Stack a chcete, aby se připojily k podnikovému Doména služby Active Directory a tam tam používaly účty, můžete si i nadále. Nemusíte používat úložiště identit Azure AD, které tady vyberete pro tyto účty.

### <a name="azure-ad-identity-store"></a>Úložiště identit Azure AD
Použití Azure AD pro úložiště identit vyžaduje dva účty Azure AD: účet globálního správce a fakturační účet. Tyto účty můžou být stejné nebo různé účty. I když použití stejného uživatelského účtu může být jednodušší a užitečné, pokud máte omezený počet účtů Azure, vaše obchodní potřeby můžou navrhnout použití dvou účtů:

1. **Účet globálního správce** (vyžaduje se jenom pro připojená nasazení). Toto je účet Azure, který se používá k vytváření aplikací a instančních objektů pro služby infrastruktury centra Azure Stack ve službě Azure AD. Tento účet musí mít oprávnění správce adresáře pro adresář, do kterého bude váš systém Azure Stack hub nasazený. Stane se jako "operátor cloudu" globálním správcem pro uživatele Azure AD a používá se pro následující úlohy:

    - K zřizování a delegování aplikací a instančních objektů pro všechny služby centra Azure Stack, které potřebují komunikovat s Azure AD a Graph API.
    - Jako účet správce služby. Tento účet je vlastníkem výchozího předplatného poskytovatele (které můžete později změnit). Pomocí tohoto účtu se můžete přihlásit k portálu správce centra Azure Stack a můžete ho použít k vytvoření nabídek a plánů, nastavení kvót a provádění dalších funkcí správy v centru Azure Stack.

> [!IMPORTANT]
> - Účet globálního správce není potřebný ke spuštění centra Azure Stack a je možné ho zakázat po nasazení.
> - Zabezpečte globální účet správce podle [osvědčených postupů popsaných tady](/azure/security/fundamentals/identity-management-best-practices).


2. **Fakturační účet** (potřebný pro připojená i odpojená nasazení). Tento účet Azure slouží k navázání fakturačního vztahu mezi vaším integrovaným systémem vašeho centra Azure Stack a back-endu Azure Commerce. Toto je účet, který se účtuje za Azure Stack poplatky za centrum. Tento účet se taky použije pro nabídky položek na webu Marketplace a dalších hybridních scénářích.

### <a name="ad-fs-identity-store"></a>AD FS úložiště identit
Tuto možnost vyberte, pokud chcete pro účty správce služby používat vlastní úložiště identit, jako je například vaše firemní služba Active Directory.  

## <a name="choose-a-billing-model"></a>Výběr modelu fakturace
Můžete si vybrat buď průběžné **platby** , nebo model fakturace **kapacity** . Nasazení fakturačního modelu s průběžnými platbami podle aktuálního využití musí být schopné hlásit využití prostřednictvím připojení do Azure nejméně jednou za 30 dní. Proto je model fakturace s průběžnými platbami podle využití dostupný jenom pro připojená nasazení.  

### <a name="pay-as-you-use"></a>Průběžné platby
S využitím modelu fakturace s průběžnými platbami podle aktuálního využití se účtují poplatky za použití v rámci předplatného Azure. Platíte jenom v případě, že používáte služby centra Azure Stack. Pokud se jedná o model, který se rozhodnete používat, budete potřebovat předplatné Azure a ID účtu přidružené k tomuto předplatnému (například serviceadmin@contoso.onmicrosoft.com ). Předplatné EA, CSP a CSL se podporují. Vytváření sestav využití se konfiguruje během [registrace centra Azure Stack](azure-stack-registration.md).

> [!NOTE]
> Ve většině případů budou Podnikoví zákazníci používat předplatná EA a poskytovatelé služeb budou používat předplatná CSP nebo CSL.

Pokud se chystáte použít předplatné CSP, Projděte si následující tabulku a určete, které předplatné CSP chcete použít, protože správný přístup závisí na konkrétním scénáři CSP:

|Scenario|Možnosti domény a předplatného|
|-----|-----|
|Jste přímý poskytovatel **CSP** nebo **nepřímo zprostředkovatel CSP** a budete pracovat s centrem Azure Stack.|Použijte předplatné CSL (Common Service Layer).<br>     nebo<br>Vytvořte tenanta Azure AD s popisným názvem v partnerském centru. &lt;Vaše organizace například>CSPAdmin s předplatným CSP Azure, které je k němu přidružené.|
|Jste **nepřímým prodejcem CSP** a budete pracovat s centrem Azure Stack.|Požádejte svého nepřímý poskytovatel CSP, aby vytvořil tenanta Azure AD pro vaši organizaci s předplatným CSP Azure, které je k němu přidruženo pomocí partnerského centra.|

### <a name="capacity-based-billing"></a>Fakturace na základě kapacity
Pokud se rozhodnete použít model fakturace kapacity, musíte si koupit skladovou jednotku plánu centra Azure Stack v závislosti na kapacitě systému. Abyste si mohli koupit správné množství, potřebujete znát počet fyzických jader v centru Azure Stack.

Fakturace kapacity vyžaduje předplatné Azure smlouva Enterprise (EA) pro registraci. Důvodem je to, že registrace nastaví dostupnost položek na webu Marketplace, které vyžadují předplatné Azure. Předplatné se nepoužívá pro Azure Stack použití centra.

## <a name="learn-more"></a>Další informace
- Informace o případech použití, nákupu, partnerech a dodavatelích hardwarových výrobců OEM najdete na stránce produktu [Azure Stack hub](https://azure.microsoft.com/overview/azure-stack/) .
- Informace o plánu a geografické dostupnosti pro integrované systémy Azure Stack hub najdete v dokumentu White Paper: [Azure Stack hub: rozšíření Azure](https://azure.microsoft.com/resources/videos/azure-friday-azure-stack-an-extension-of-azure/). 
- Pokud se chcete dozvědět víc o Microsoft Azure Stackovém balení a cenách centra, [Stáhněte si soubor. PDF](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf). 

## <a name="next-steps"></a>Další kroky
[Integrace sítě Datacenter](azure-stack-network.md)
