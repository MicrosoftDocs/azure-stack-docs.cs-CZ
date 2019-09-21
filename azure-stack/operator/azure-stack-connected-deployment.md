---
title: Rozhodnutí o nasazení připojená k Azure pro Azure Stack integrovaných systémů | Microsoft Docs
description: Rozhodnutí týkající se plánování nasazení Azure Stack integrovaných systémů spojených s Azure, včetně fakturace a identity, najdete v části nasazení.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/13/2019
ms.author: mabrigg
ms.reviewer: wfayed
ms.lastreviewed: 11/05/2018
ms.openlocfilehash: fa90091f93556cd313fa8e4e21bfe0fd24011e38
ms.sourcegitcommit: 3af71025e85fc53ce529de2f6a5c396b806121ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/20/2019
ms.locfileid: "71159145"
---
# <a name="azure-connected-deployment-planning-decisions-for-azure-stack-integrated-systems"></a>Rozhodnutí týkající se plánování nasazení spojené s Azure pro Azure Stack integrovaných systémů
Až se rozhodnete, [jak budete Azure Stack do svého hybridního cloudového prostředí integrovat](azure-stack-connection-models.md), můžete Azure Stack rozhodnutí o nasazení dokončit.

Nasazení Azure Stack připojeného k Azure znamená, že pro své úložiště identity můžete mít buď Azure Active Directory (Azure AD), nebo Active Directory Federation Services (AD FS) (AD FS). Můžete si také vybrat z modelu fakturace: průběžné platby nebo využití na základě kapacity. Výchozím nastavením je připojené nasazení, protože umožňuje zákazníkům získat co nejvíc Azure Stack, zejména v případě hybridních cloudových scénářů, které zahrnují Azure i Azure Stack.

## <a name="choose-an-identity-store"></a>Výběr úložiště identity
S připojeným nasazením si můžete vybrat mezi Azure AD nebo AD FS pro úložiště identit. Odpojené nasazení, bez připojení k Internetu, může používat jenom AD FS.

Vaše volba úložiště identity nemá žádný vliv na virtuální počítače klienta. Virtuální počítače klientů si můžou zvolit úložiště identity, ke kterému se chtějí připojit, podle toho, jak se budou konfigurovat: Azure AD, Windows Server Active Directory – připojeno k doméně, pracovní skupina atd. Nesouvisí s Azure Stack rozhodnutím poskytovatele identity.

Pokud například nasadíte virtuální počítače klienta IaaS nad Azure Stack a chcete, aby se připojily k podnikové Doména služby Active Directory a tam tam používaly účty, můžete si i nadále. Nemusíte používat úložiště identit Azure AD, které tady vyberete pro tyto účty.

### <a name="azure-ad-identity-store"></a>Úložiště identit Azure AD
Použití Azure AD pro úložiště identit vyžaduje dva účty Azure AD: účet globálního správce a fakturační účet. Tyto účty můžou být stejné nebo různé účty. I když použití stejného uživatelského účtu může být jednodušší a užitečné, pokud máte omezený počet účtů Azure, vaše obchodní potřeby můžou navrhnout použití dvou účtů:

1. **Účet globálního správce** (vyžaduje se jenom pro připojená nasazení). Toto je účet Azure, který se používá k vytváření aplikací a instančních objektů pro služby Azure Stack infrastruktury ve službě Azure AD. Tento účet musí mít oprávnění správce adresáře pro adresář, do kterého bude váš Azure Stack systém nasazený. Stane se jako "operátor cloudu" globálním správcem pro uživatele Azure AD a používá se pro následující úlohy:

    - K zřizování a delegování aplikací a instančních objektů pro všechny Azure Stack služby, které potřebují pracovat s Azure AD a Graph API.
    - Jako účet správce služby. Tento účet je vlastníkem výchozího předplatného poskytovatele (které můžete později změnit). Pomocí tohoto účtu se můžete přihlásit k portálu pro správu Azure Stack a můžete ho použít k vytvoření nabídek a plánů, nastavení kvót a provádění dalších funkcí správy v Azure Stack.

2. **Fakturační účet** (vyžadováno pro připojená i odpojená nasazení). Tento účet Azure slouží k navázání fakturačního vztahu mezi vaším Azure Stack integrovaným systémem a back-endu Azure Commerce. Jedná se o účet, který se účtuje za Azure Stack poplatky. Tento účet se taky použije pro nabídky položek na webu Marketplace a dalších hybridních scénářích.

### <a name="ad-fs-identity-store"></a>AD FS úložiště identit
Tuto možnost vyberte, pokud chcete pro účty správce služby používat vlastní úložiště identit, jako je například vaše firemní služba Active Directory.  

## <a name="choose-a-billing-model"></a>Výběr modelu fakturace
Můžete si vybrat buď průběžné **platby** , nebo model fakturace **kapacity** . Nasazení fakturačního modelu s průběžnými platbami podle aktuálního využití musí být schopné hlásit využití prostřednictvím připojení do Azure nejméně jednou za 30 dní. Proto je model fakturace s průběžnými platbami podle využití dostupný jenom pro připojená nasazení.  

### <a name="pay-as-you-use"></a>Průběžné platby
S využitím modelu fakturace s průběžnými platbami podle aktuálního využití se účtují poplatky za použití v rámci předplatného Azure. Platíte jenom při používání služby Azure Stack Services. Pokud se jedná o model, který se rozhodnete používat, budete potřebovat předplatné Azure a ID účtu přidružené k tomuto předplatnému (například serviceadmin@contoso.onmicrosoft.com). Předplatné EA, CSP a CSL se podporují. Vytváření sestav využití se konfiguruje během [registrace Azure Stack](azure-stack-registration.md).

> [!NOTE]
> Ve většině případů budou Podnikoví zákazníci používat předplatná EA a poskytovatelé služeb budou používat předplatná CSP nebo CSL.

Pokud se chystáte použít předplatné CSP, Projděte si následující tabulku a určete, které předplatné CSP chcete použít, protože správný přístup závisí na konkrétním scénáři CSP:

|Scénář|Možnosti domény a předplatného|
|-----|-----|
|Jste přímý poskytovatel **CSP** nebo **nepřímo zprostředkovatel CSP**a budete pracovat s Azure Stack|Použijte předplatné CSL (Common Service Layer).<br>     nebo<br>Vytvořte tenanta Azure AD s popisným názvem v partnerském centru. &lt;Vaše organizace například > CSPAdmin s předplatným CSP Azure, které je k němu přidružené.|
|Jste **nepřímým prodejcem CSP**a budete pracovat s Azure Stack|Požádejte svého nepřímý poskytovatel CSP, aby vytvořil tenanta Azure AD pro vaši organizaci s předplatným CSP Azure, které je k němu přidruženo pomocí partnerského centra.|

### <a name="capacity-based-billing"></a>Fakturace na základě kapacity
Pokud se rozhodnete použít model fakturace kapacity, musíte si koupit Azure Stack SKU plánu kapacity na základě kapacity vašeho systému. Abyste si mohli koupit správné množství, potřebujete znát počet fyzických jader v Azure Stack.

Fakturace kapacity vyžaduje předplatné Azure smlouva Enterprise (EA) pro registraci. Důvodem je to, že registrace nastaví dostupnost položek na webu Marketplace, které vyžadují předplatné Azure. Předplatné se nepoužívá k použití Azure Stack.

## <a name="learn-more"></a>Víc se uč
- Informace o případech použití, nákupu, partnerech a dodavatelích hardwaru OEM naleznete na stránce [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) produktu.
- Informace o plánu a geografické dostupnosti pro Azure Stack integrovaných systémech najdete v dokumentu White Paper: [Azure Stack: Rozšíření Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 
- Pokud se chcete dozvědět víc o Microsoft Azure Stack balení a cenách, [Stáhněte si soubor. PDF](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf). 

## <a name="next-steps"></a>Další kroky
[Integrace sítě Datacenter](azure-stack-network.md)