---
title: Zpráva k vydání verze Azure Stack 1908 | Microsoft Docs
description: Další informace o aktualizacích integrovaných systémů Azure Stack 1908
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
ms.date: 10/29/2019
ms.author: sethm
ms.reviewer: prchint
ms.lastreviewed: 10/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 0137ec7491d84394b049095b1830c22ddcc7d922
ms.sourcegitcommit: f194f9ca4297864500e62d8658674a0625b29d1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102187193"
---
# <a name="azure-stack-updates-1908-release-notes"></a>Azure Stack aktualizace: zpráva k vydání verze 1908

*Platí pro: Azure Stack integrovaných systémů*

Tento článek popisuje obsah balíčků aktualizací Azure Stack. Tato aktualizace obsahuje novinky a opravy pro tuto verzi Azure Stack.

Pokud chcete získat přístup k poznámkám k verzi pro jinou verzi, použijte rozevírací nabídku selektor verzí nad obsahem vlevo.

> [!IMPORTANT]  
> Tento balíček aktualizace je určen pouze pro Azure Stack integrovaných systémů. Nepoužívejte tento balíček aktualizace na Azure Stack Development Kit.

> [!IMPORTANT]  
> Pokud je vaše instance Azure Stack za více než dvěma aktualizacemi, je považována za nedodržující předpisy. Aby bylo možné [získat podporu, musíte aktualizovat aspoň minimální podporovanou verzi](../azure-stack-servicing-policy.md#keep-your-system-under-support).

## <a name="update-planning"></a>Plánování aktualizací

Před použitím této aktualizace se ujistěte, že si provedete následující informace:

- [Známé problémy](known-issues-1908.md)
- [Aktualizace zabezpečení](../release-notes-security-updates.md)
- [Kontrolní seznam aktivit před a po instalaci aktualizace](../release-notes-checklist.md)

Nápovědu k řešení potíží s aktualizacemi a procesu aktualizace najdete v tématu [řešení potíží se opravami a aktualizacemi pro Azure Stack](../azure-stack-troubleshooting.md).

## <a name="1908-build-reference"></a>1908 – odkaz na sestavení

Číslo buildu aktualizace Azure Stack 1908 je **1.1908.4.33**.

### <a name="update-type"></a>Typ aktualizace

V 1908 se základní operační systém, na kterém Azure Stack běžet, aktualizoval na Windows Server 2019. To umožňuje základní vylepšení základních funkcí a také možnost přinášet další možnosti Azure Stack v blízké budoucnosti.

Typ sestavení aktualizace Azure Stack 1908 je **plný**. V důsledku toho má aktualizace 1908 delší dobu běhu než expresní aktualizace jako 1906 a 1907. Přesné moduly runtime pro úplné aktualizace obvykle závisí na počtu uzlů, které vaše instance Azure Stack obsahuje, na kapacitě používané v systému podle úloh klientů, na síťovém připojení vašeho systému (Pokud je připojené k Internetu) a na hardwarové konfiguraci vašeho systému. Aktualizace 1908 měla během interního testování následující očekávané moduly runtime: 4 uzly-42 hodiny, 8 uzlů – 50 hodin, 12 uzlů – 60 hodiny, 16 uzlů – 70 hodin. Aktualizační moduly, které trvají déle než tyto očekávané hodnoty, nejsou Neběžné a nevyžadují akci Azure Stack operátory, pokud aktualizace neproběhne úspěšně.

Další informace o typech sestavení aktualizací najdete v tématu [Správa aktualizací v Azure Stack](../azure-stack-updates.md).

- Přesné běhové moduly pro aktualizaci jsou obvykle závislé na kapacitě používané systémem v rámci úloh klientů, připojení k systémové síti (Pokud je připojeno k Internetu) a hardwarové konfiguraci systému.
- Běhové moduly, které trvají déle než obvykle, nejsou běžné a nevyžadují akci Azure Stack operátory, pokud se aktualizace nezdařila.
- Tato přibližná doba běhu je specifická pro aktualizaci 1908 a neměla by se porovnávat s jinými aktualizacemi Azure Stack.

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Novinky

<!-- What's new, also net new experiences and features. -->

- Pro 1908 si všimněte, že základní operační systém, na kterém Azure Stack běžet, byl aktualizován na Windows Server 2019. To umožňuje základní vylepšení základních funkcí a také možnost přinášet další možnosti Azure Stack v blízké budoucnosti.
- Všechny součásti infrastruktury Azure Stack nyní fungují v režimu FIPS 140-2.
- Azure Stack operátory teď můžou odebrat uživatelská data portálu. Další informace najdete v tématu [vymazání uživatelských dat portálu z Azure Stack](../azure-stack-portal-clear.md).

### <a name="improvements"></a>Vylepšen

<!-- Changes and product improvements with tangible customer-facing value. -->
- Vylepšení v oblasti šifrování uložených dat Azure Stack pro zachování tajných kódů do hardwaru Trusted Platform Module (TPM) fyzických uzlů.

### <a name="changes"></a>Změny

- Poskytovatelé hardwaru budou vycházet z balíčku rozšíření OEM 2,1 nebo novějšího ve stejnou dobu jako verze Azure Stack 1908. Balíček rozšíření OEM 2,1 nebo novější je předpokladem pro Azure Stack verze 1908. Další informace o tom, jak stáhnout balíček rozšíření OEM 2,1 nebo novější, získáte od poskytovatele hardwaru vašeho systému a v článku věnovaném [aktualizacím OEM](../azure-stack-update-oem.md#oem-contact-information) .  

### <a name="fixes"></a>Opravy

- Opravili jsme problém s kompatibilitou s budoucími Azure Stack aktualizacemi OEM a problémem s nasazením virtuálního počítače pomocí uživatelských imagí zákazníka. Tento problém byl nalezen v 1907 a opraven v opravě hotfix [KB4517473](https://support.microsoft.com/en-us/help/4517473/azure-stack-hotfix-1-1907-12-44)  
- Opravili jsme problém s aktualizací firmwaru OEM a opravili jsme chybnou diagnostiku v Test-AzureStack pro stav Fabric Ring. Tento problém byl nalezen v 1907 a opraven v opravě hotfix [KB4515310](https://support.microsoft.com/en-us/help/4515310/azure-stack-hotfix-1-1907-7-35)
- Opravili jsme problém s procesem aktualizace firmwaru OEM. Tento problém byl nalezen v 1907 a opraven v opravě hotfix [KB4515650](https://support.microsoft.com/en-us/help/4515650/azure-stack-hotfix-1-1907-8-37)

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

## <a name="security-updates"></a>Aktualizace zabezpečení

Informace o aktualizacích zabezpečení v této aktualizaci Azure Stack najdete v tématu [Azure Stack aktualizace zabezpečení](../release-notes-security-updates.md).

## <a name="download-the-update"></a><a name="download-the-update-1908"></a>Stáhnout aktualizaci

Balíček aktualizace Azure Stack 1908 můžete stáhnout ze [stránky pro stažení Azure Stack](https://aka.ms/azurestackupdatedownload).

## <a name="hotfixes"></a>Opravy hotfix

Azure Stack pravidelně vydává opravy hotfix. Před aktualizací Azure Stack na 1908 nezapomeňte nainstalovat nejnovější Azure Stack opravu hotfix pro 1907.

Azure Stack oprav hotfix se týkají pouze Azure Stack integrovaných systémů. Nepokoušejte se instalovat opravy hotfix na ASDK.

### <a name="prerequisites-before-applying-the-1908-update"></a>Požadavky: před instalací aktualizace 1908

Vydání verze 1908 Azure Stack se musí použít ve verzi 1907 s následujícími opravami hotfix:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack opravy hotfix 1.1907.17.54](https://support.microsoft.com/help/4523826)

Aktualizace Azure Stack 1908 vyžaduje **Azure Stack OEM verze 2,1 nebo novější** od poskytovatele hardwaru vašeho systému. Aktualizace OEM zahrnují aktualizace ovladačů a firmwaru pro váš Azure Stack systémový hardware. Další informace o použití aktualizací OEM naleznete v tématu [apply Azure Stack Updates Original Equipment Updates](../azure-stack-update-oem.md) .

### <a name="after-successfully-applying-the-1908-update"></a>Po úspěšné instalaci aktualizace 1908

Po instalaci této aktualizace nainstalujte všechny příslušné opravy hotfix. Další informace najdete v našich [zásadách obsluhy](../azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack opravy hotfix 1.1908.6.37](https://support.microsoft.com/help/4527372)