---
title: Rozhodnutí o odpojeném nasazení Azure pro Azure Stack integrovaných systémů | Microsoft Docs
description: Přečtěte si o odpojeném nasazení Azure Stack integrovaných systémech a o rozhodnutích, která je potřeba zvážit v Azure.
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
ms.date: 11/01/2019
ms.author: mabrigg
ms.reviewer: wfayed
ms.lastreviewed: 11/01/2019
ms.openlocfilehash: 183abaa85607f0ad0c1bb9f82901b86176ae5c65
ms.sourcegitcommit: 102ef41963b5d2d91336c84f2d6af3fdf2ce11c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73955242"
---
# <a name="azure-disconnected-deployment-planning-decisions-for-azure-stack-integrated-systems"></a>Rozhodnutí týkající se plánování nasazení na odpojení Azure pro Azure Stack integrovaných systémů
Až se rozhodnete, [jak budete Azure Stack do svého hybridního cloudového prostředí integrovat](azure-stack-connection-models.md), můžete dokončit rozhodnutí o Azure Stack nasazení.

Azure Stack můžete nasadit a používat bez připojení k Internetu. V případě odpojeného nasazení ale budete omezeni na úložiště identit Active Directory Federation Services (AD FS) (AD FS) a model fakturace na základě kapacity. Vzhledem k tomu, že víceklientská architektura vyžaduje použití Azure Active Directory (Azure AD), víceklientské prostředí se pro odpojená nasazení nepodporuje.

Tuto možnost vyberte, pokud:
- Máte zabezpečení nebo jiná omezení, která vyžadují nasazení Azure Stack v prostředí, které není připojené k Internetu.
- Chcete zablokovat data (včetně údajů o využití), která se odesílají do Azure.
- Chcete použít Azure Stack čistě jako řešení privátního cloudu, které je nasazené na podnikový intranet a nemá zájem o hybridní scénáře.

> [!TIP]
> V některých případech se tento druh prostředí označuje také jako *podmořský scénář*.

Odpojené nasazení vám nebrání v pozdějším připojení instance Azure Stack k Azure pro scénáře hybridního virtuálního počítače tenanta. Znamená to, že nemáte připojení k Azure během nasazení nebo nechcete používat Azure AD jako úložiště identit.

## <a name="features-that-are-impaired-or-unavailable-in-disconnected-deployments"></a>Funkce, které jsou v odpojeném nasazení postižené nebo nedostupné 
Azure Stack byla navržena tak, aby fungovala nejlépe při připojení k Azure, takže je důležité si uvědomit, že některé funkce a funkce jsou v odpojeném režimu buď nespárované, nebo zcela nedostupné.

|Funkce|Dopad v odpojeném režimu|
|-----|-----|
|Nasazení virtuálního počítače s rozšířením DSC ke konfiguraci po nasazení virtuálního počítače|Nespárované – rozšíření DSC hledá nejnovější WMF na internetu.|
|Nasazení virtuálního počítače s rozšířením Docker pro spuštění příkazů Docker|Nespárované – Docker zkontroluje nejnovější verzi na internetu a tato kontroly selže.|
|Odkazy na dokumentaci na portálu Azure Stack|Nedostupné – odkazy, jako je poskytnutí zpětné vazby, pomáhat a rychlé spuštění, které používají internetovou adresu URL, nebudou fungovat.|
|Náprava nebo zmírnění výstrahy, které odkazují na online příručku k nápravě|Nedostupné – jakékoli odkazy na nápravu výstrah, které používají internetovou adresu URL, nebudou fungovat.|
|Marketplace – možnost vybrat a přidat balíčky Galerie přímo z Azure Marketplace|Nespárované – když nasadíte Azure Stack v odpojeném režimu, nemůžete stáhnout položky Marketplace pomocí portálu Azure Stack. Pomocí [Nástroje pro syndikaci na webu Marketplace](azure-stack-download-azure-marketplace-item.md) ale můžete stáhnout položky Marketplace do počítače, který má připojení k Internetu, a pak je přenést do prostředí Azure Stack.|
|Správa nasazení Azure Stack pomocí účtů federace Azure AD|Nedostupné – Tato funkce vyžaduje připojení k Azure. Místo toho se musí použít AD FS s místní instancí Active Directory.|
|App Services|Nespárované – WebApps může vyžadovat přístup k Internetu pro aktualizovaný obsah.|
|Rozhraní příkazového řádku (CLI)|Postižené – rozhraní příkazového řádku má omezenou funkčnost pro ověřování a zřizování instančních objektů.|
|Visual Studio – Cloud Discovery|Nespárované-Cloud Discovery budou buď zjišťovat různé cloudy, nebo nebudou fungovat vůbec.|
|Visual Studio – AD FS|Nespárované – Visual Studio Enterprise a Visual Studio Code podporují ověřování AD FS.
Telemetrie|Nedostupná data telemetrie pro Azure Stack a jakékoli balíčky Galerie třetích stran, které jsou závislé na datech telemetrie.|
|Certifikáty|Nedostupné – vyžaduje se připojení k Internetu pro služby seznam odvolaných certifikátů (CRL) a OSCP (Online Certificate Status Protocol) v kontextu protokolu HTTPS.|
|Key Vault|Nespárované – běžný případ použití pro Key Vault je, že za běhu mají číst tajné kódy aplikace. V případě tohoto případu použití aplikace potřebuje instanční objekt v adresáři. Ve výchozím nastavení ve službě Azure AD mají standardní uživatelé (ne správci) standardně povolené přidávání instančních objektů. V Azure AD (pomocí AD FS) nejsou. Toto poškození vede mezní hodnoty do koncového prostředí, protože jedna z nich musí vždy projít správcem adresáře a přidat tak libovolnou aplikaci.

## <a name="learn-more"></a>Další informace
- Informace o případech použití, nákupu, partnerech a dodavatelích hardwaru OEM naleznete na stránce [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) produktu.
- Informace o plánu a geografické dostupnosti pro Azure Stack integrovaných systémech najdete v dokumentu White Paper: [Azure Stack: rozšíření Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 
- Pokud se chcete dozvědět víc o Microsoft Azure Stack balení a cenách, [Stáhněte si soubor. PDF](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf). 

## <a name="next-steps"></a>Další kroky
[Integrace sítě Datacenter](azure-stack-network.md)
