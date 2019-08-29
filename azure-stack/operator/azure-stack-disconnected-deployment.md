---
title: Rozhodnutí o odpojeném nasazení Azure pro Azure Stack integrovaných systémů | Microsoft Docs
description: Určete rozhodnutí plánování nasazení pro více uzlů Azure Stack nasazení propojených s Azure.
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
ms.lastreviewed: 12/11/2018
ms.openlocfilehash: 11bf3bd4cc670d45fc4a4c9d1421fc0c25440726
ms.sourcegitcommit: e8f7fe07b32be33ef621915089344caf1fdca3fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70118669"
---
# <a name="azure-disconnected-deployment-planning-decisions-for-azure-stack-integrated-systems"></a>Rozhodnutí týkající se plánování nasazení na odpojení Azure pro Azure Stack integrovaných systémů
Až se rozhodnete, [jak budete Azure Stack do svého hybridního cloudového prostředí integrovat](azure-stack-connection-models.md), můžete Azure Stack rozhodnutí o nasazení dokončit.

Azure Stack můžete nasadit a používat bez připojení k Internetu. V případě odpojeného nasazení ale budete omezeni na AD FS úložiště identit a model fakturace na základě kapacity. Vzhledem k tomu, že víceklientská architektura vyžaduje použití Azure Active Directory (Azure AD), víceklientské prostředí není pro odpojená nasazení podporovaná. 

Tuto možnost vyberte, pokud:
- Měli byste mít zabezpečení nebo jiná omezení, která vyžadují nasazení Azure Stack v prostředí, které není připojené k Internetu.
- Chcete zablokovat data (včetně údajů o využití), která se odesílají do Azure.
- Chcete použít Azure Stack čistě jako řešení privátního cloudu, které je nasazené na podnikový intranet a nemá zájem o hybridní scénáře.

> [!TIP]
> V některých případech se tento typ prostředí označuje také jako podmořský *scénář*.

Odpojené nasazení vám nebrání v pozdějším připojení instance Azure Stack k Azure pro scénáře hybridního virtuálního počítače tenanta. Znamená to, že nemáte připojení k Azure během nasazení nebo nechcete používat Azure AD jako úložiště identit.

## <a name="features-that-are-impaired-or-unavailable-in-disconnected-deployments"></a>Funkce, které jsou v odpojeném nasazení postižené nebo nedostupné 
Azure Stack byla navržena tak, aby fungovala nejlépe při připojení k Azure, takže je důležité si uvědomit, že některé funkce a funkce jsou v odpojeném režimu buď nespárované, nebo zcela nedostupné. 

|Funkce|Dopad v odpojeném režimu|
|-----|-----|
|Nasazení virtuálního počítače s rozšířením DSC ke konfiguraci po nasazení virtuálního počítače|Nespárované – rozšíření DSC hledá nejnovější WMF na internetu.|
|Nasazení virtuálního počítače s rozšířením Docker pro spuštění příkazů Docker|Nespárované – Docker zkontroluje nejnovější verzi na internetu a tato kontroly selže.|
|Odkazy na dokumentaci na portálu Azure Stack|Nedostupné – odkazy, jako je poskytnutí zpětné vazby, pomáhat, rychlý Start atd. použití internetové adresy URL nebude fungovat.|
|Náprava nebo zmírnění výstrahy, které odkazují na online příručku k nápravě|Nedostupné – jakékoli odkazy na nápravu výstrah, které používají internetovou adresu URL, nebudou fungovat.|
|Marketplace – možnost vybrat a přidat balíčky Galerie přímo z Azure Marketplace|Nespárované – při nasazení Azure Stack v odpojeném režimu (bez připojení k Internetu) nemůžete stáhnout položky Marketplace pomocí portálu Azure Stack. Pomocí nástroje pro syndikaci na [webu Marketplace](azure-stack-download-azure-marketplace-item.md) ale můžete stáhnout položky Marketplace do počítače, který má připojení k Internetu, a pak je přenést do prostředí Azure Stack.|
|Použití federačních účtů Azure Active Directory ke správě nasazení Azure Stack|Nedostupné – Tato funkce vyžaduje připojení k Azure. Místo toho se musí použít AD FS s místní instancí Active Directory.|
|App Services|Nespárované – WebApps může vyžadovat přístup k Internetu pro aktualizovaný obsah.|
|Rozhraní příkazového řádku (CLI)|Postižené – rozhraní příkazového řádku má omezenou funkčnost z pohledu ověřování a zřizování instančních objektů.|
|Visual Studio – Cloud Discovery|Nespárované-Cloud Discovery budou buď zjišťovat různé cloudy, nebo nebudou fungovat vůbec.|
|Visual Studio – AD FS|Nespárované – Visual Studio Enterprise a Visual Studio Code podporují ověřování AD FS.
Telemetrie|Nedostupná data telemetrie pro Azure Stack a všechny balíčky Galerie třetích stran, které jsou závislé na datech telemetrie.|
|Certifikáty|Nedostupné – vyžaduje se připojení k Internetu pro služby seznam odvolaných certifikátů (CRL) a OSCP (Online Certificate Status Protocol) v kontextu protokolu HTTPS.|
|Trezor klíčů|Nespárované – běžný případ použití pro Key Vault má mít za běhu čtení tajných kódů aplikace. Pro tuto aplikaci potřebuje objekt služby v adresáři. V Azure Active Directory mají standardní uživatelé (ne správci) standardně povolené přidávání instančních objektů. Ve službě AD (pomocí ADFS) nejsou. To představuje mezní hodnotu v rámci koncového prostředí, protože jedna z nich musí vždy projít správcem adresáře a přidat tak libovolnou aplikaci.| 

## <a name="learn-more"></a>Víc se uč
- Informace o případech použití, nákupu, partnerech a dodavatelích hardwaru OEM naleznete na stránce [Azure Stack](https://azure.microsoft.com/overview/azure-stack/) produktu.
- Informace o plánu a geografické dostupnosti pro Azure Stack integrovaných systémech najdete v dokumentu White Paper: [Azure Stack: Rozšíření Azure](https://azure.microsoft.com/resources/azure-stack-an-extension-of-azure/). 
- Další informace o Microsoft Azure Stack balení a cenách [si můžete stáhnout v souboru. PDF](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf). 

## <a name="next-steps"></a>Další postup
[Integrace sítě Datacenter](azure-stack-network.md)
