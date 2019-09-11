---
title: Zpráva k vydání verze Azure Stack 1908 | Microsoft Docs
description: Seznamte se s aktualizací 1908 pro Azure Stack integrovaných systémů, včetně nových, známých problémů a místa, kde si tuto aktualizaci stáhnout.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2019
ms.author: sethm
ms.reviewer: prchint
ms.lastreviewed: 08/30/2019
monikerRange: azs-1908
ms.openlocfilehash: 935a422aee68bb107e5bfe8b79a21d22ce1fffc8
ms.sourcegitcommit: 305536bfd49319455ca3ca270fe3644b1796bad1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2019
ms.locfileid: "70876613"
---
# <a name="azure-stack-1908-update"></a>Aktualizace Azure Stack 1908

*Platí pro: Azure Stack integrovaných systémů*

Tento článek popisuje obsah balíčku aktualizace 1908. Tato aktualizace obsahuje novinky a opravy pro tuto verzi Azure Stack.

> [!IMPORTANT]  
> Tento balíček aktualizace je pouze pro integrované systémy Azure Stack. Tento balíček aktualizace nevztahují na Azure Stack Development Kit.

## <a name="previous-release-notes"></a>Předchozí zpráva k vydání verze

Od verze 1908 se předchozí verze poznámek k verzi již nezobrazuje v obsahu na levé straně. Chcete-li získat přístup k starším verzím poznámky k verzi, vyberte jiný článek (například [Azure Stack přehled](azure-stack-overview.md)) a v selektoru verzí v horní části obsahu vlevo vyberte 1905, 1906, 1907 nebo 1908. Starší verze poznámky k verzi najdete v části zpráva k [archivované verzi](#archived-release-notes) .

## <a name="build-reference"></a>Referenční informace o buildu

Číslo buildu aktualizace Azure Stack 1908 je **1.1908.0.20**.

### <a name="update-type"></a>Typ aktualizace

V 1908 se základní operační systém, na kterém Azure Stack běžet, aktualizoval na Windows Server 2019. To umožňuje základní vylepšení základních funkcí a také možnost přinášet další možnosti Azure Stack v blízké budoucnosti.

Typ sestavení aktualizace Azure Stack 1908 je **plný**. V důsledku toho má aktualizace 1908 delší dobu běhu než expresní aktualizace jako 1906 a 1907. Přesné moduly runtime pro úplné aktualizace obvykle závisí na počtu uzlů, které vaše instance Azure Stack obsahuje, na kapacitě používané v systému podle úloh klientů, na síťovém připojení vašeho systému (Pokud je připojené k Internetu) a na vašem systémovém hardwaru. rozšířeného. Aktualizace 1908 měla v našem interním testování následující očekávané moduly runtime: 4 uzly – 42 hodin, 8 uzlů – 50 hodin, 12 uzlů – 60 hodin, 16 uzlů – 70 hodiny. Aktualizační moduly, které trvají déle než tyto očekávané hodnoty, nejsou Neběžné a nevyžadují akci Azure Stack operátory, pokud aktualizace neproběhne úspěšně.

Další informace o typech sestavení aktualizací najdete v tématu [Správa aktualizací v Azure Stack](azure-stack-updates.md).

- Přesné běhové moduly pro aktualizaci jsou obvykle závislé na kapacitě používané systémem v rámci úloh klientů, připojení k systémové síti (Pokud je připojeno k Internetu) a hardwarové konfiguraci systému.
- Běhové moduly, které trvají déle než obvykle, nejsou běžné a nevyžadují akci Azure Stack operátory, pokud se aktualizace nezdařila.
- Tato přibližná doba běhu je specifická pro aktualizaci 1908 a neměla by se porovnávat s jinými aktualizacemi Azure Stack.

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Co je nového

<!-- What's new, also net new experiences and features. -->

- Pro 1908 si všimněte, že základní operační systém, na kterém Azure Stack běžet, byl aktualizován na Windows Server 2019. To umožňuje základní vylepšení základních funkcí a také možnost přinášet další možnosti Azure Stack v blízké budoucnosti.
- Všechny součásti infrastruktury Azure Stack nyní fungují v režimu FIPS 140-2.
- Azure Stack operátory teď můžou odebrat uživatelská data portálu. Další informace najdete v tématu [vymazání uživatelských dat portálu z Azure Stack](azure-stack-portal-clear.md).

### <a name="improvements"></a>Vylepšen

<!-- Changes and product improvements with tangible customer-facing value. -->
- Vylepšení v oblasti šifrování uložených dat Azure Stack pro zachování tajných kódů do hardwaru Trusted Platform Module (TPM) fyzických uzlů.

### <a name="changes"></a>Změny

- Poskytovatelé hardwaru budou vycházet z balíčku rozšíření OEM 2,1 nebo novějšího ve stejnou dobu jako verze Azure Stack 1908. Balíček rozšíření OEM 2,1 nebo novější je předpokladem pro Azure Stack verze 1908. Další informace o tom, jak stáhnout balíček rozšíření OEM 2,1 nebo novější, získáte od poskytovatele hardwaru vašeho systému a v článku věnovaném [aktualizacím OEM](azure-stack-update-oem.md#oem-contact-information) .  

### <a name="fixes"></a>Opravy

- Opravili jsme problém s kompatibilitou s budoucími Azure Stack aktualizacemi OEM a problémem s nasazením virtuálního počítače pomocí uživatelských imagí zákazníka. Tento problém byl nalezen v 1907 a opraven v opravě hotfix [KB4517473](https://support.microsoft.com/en-us/help/4517473/azure-stack-hotfix-1-1907-12-44)  
- Opravili jsme problém s aktualizací firmwaru OEM a opravili jsme chybnou diagnostiku v test-AzureStack pro Fabric Ring stav. Tento problém byl nalezen v 1907 a opraven v opravě hotfix [KB4515310](https://support.microsoft.com/en-us/help/4515310/azure-stack-hotfix-1-1907-7-35)
- Opravili jsme problém s procesem aktualizace firmwaru OEM. Tento problém byl nalezen v 1907 a opraven v opravě hotfix [KB4515650](https://support.microsoft.com/en-us/help/4515650/azure-stack-hotfix-1-1907-8-37)


<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

## <a name="security-updates"></a>Aktualizace zabezpečení

Informace o aktualizacích zabezpečení v této aktualizaci Azure Stack najdete v tématu [Azure Stack aktualizace zabezpečení](azure-stack-release-notes-security-updates.md).

## <a name="update-planning"></a>Plánování aktualizací

Před použitím této aktualizace se ujistěte, že si provedete následující informace:

- [Známé problémy](azure-stack-release-notes-known-issues-1908.md)
- [Aktualizace zabezpečení](azure-stack-release-notes-security-updates.md)
- [Kontrolní seznam aktivit před a po instalaci aktualizace](azure-stack-release-notes-checklist.md)

## <a name="download-the-update"></a>Stáhnout aktualizaci.

Balíček aktualizace Azure Stack 1908 můžete stáhnout ze [stránky pro stažení Azure Stack](https://aka.ms/azurestackupdatedownload).

## <a name="hotfixes"></a>Opravy hotfix

Azure Stack pravidelně vydává opravy hotfix. Před aktualizací Azure Stack na 1908 nezapomeňte nainstalovat nejnovější Azure Stack opravu hotfix pro 1907.

Azure Stack oprav hotfix se týkají pouze Azure Stack integrovaných systémů. Nepokoušejte se instalovat opravy hotfix na ASDK.

### <a name="prerequisites-before-applying-the-1908-update"></a>Požadavky: Před použitím aktualizace 1908

Vydání verze 1908 Azure Stack se musí použít ve verzi 1907 s následujícími opravami hotfix:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack opravy hotfix 1.1907.12.44](https://support.microsoft.com/help/4517473)

Aktualizace Azure Stack 1908 vyžaduje **Azure Stack OEM verze 2,1 nebo novější** od poskytovatele hardwaru vašeho systému. Aktualizace OEM zahrnují aktualizace ovladačů a firmwaru pro váš Azure Stack systémový hardware. Další informace o použití aktualizací OEM naleznete v tématu [apply Azure Stack Updates Original Equipment Updates](azure-stack-update-oem.md) .

### <a name="after-successfully-applying-the-1908-update"></a>Po úspěšné instalaci aktualizace 1908

Po instalaci této aktualizace nainstalujte všechny příslušné opravy hotfix. Další informace najdete v našich [zásadách obsluhy](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- Pro 1908 není k dispozici žádná oprava hotfix.

## <a name="automatic-update-notifications"></a>Oznámení automatických aktualizací

Systémy, které mají přístup k Internetu ze sítě infrastruktury, uvidí na portálu operátora zprávu **k aktualizaci k dispozici** . Systémy bez přístupu k Internetu můžou stáhnout a naimportovat soubor. zip s odpovídající příponou. XML.

> [!TIP]  
> Přihlaste se k odběru následujících informačních kanálů *RSS* nebo *Atom* , abyste zachovali Azure Stack opravy hotfix:
>
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Počtu](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

## <a name="archived-release-notes"></a>Archivované poznámky k verzi

[Starší verze Azure Stack poznámky k verzi najdete v Galerii TechNet](https://aka.ms/azsarchivedrelnotes). Tato Archivovaná zpráva k vydání verze je poskytována pouze pro referenční účely a neznamená podporu těchto verzí. Informace o podpoře Azure Stack najdete v tématu [zásady pro obsluhu Azure Stack](azure-stack-servicing-policy.md). Pokud potřebujete další pomoc, obraťte se na službu zákaznické podpory společnosti Microsoft.

## <a name="next-steps"></a>Další postup

- Přehled správy aktualizací ve službě Azure Stack najdete v tématu [správy aktualizací ve službě Azure Stack přehled](azure-stack-updates.md).  
- Další informace o tom, jak použít aktualizace pomocí služby Azure Stack najdete v tématu [použití aktualizací ve službě Azure Stack](azure-stack-apply-updates.md).
- Zásady údržby pro integrované systémy Azure Stack, a co musíte udělat, aby byl váš systém v podporovaném stavu najdete v tématu [Azure Stack zásady obsluhy](azure-stack-servicing-policy.md).  
- Privilegované koncový bod (období) použít ke sledování a obnovit aktualizace, najdete v článku [monitorování aktualizací ve službě Azure Stack pomocí privilegovaných koncového bodu](azure-stack-monitor-update.md).
