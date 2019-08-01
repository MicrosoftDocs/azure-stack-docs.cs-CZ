---
title: Zpráva k vydání verze Azure Stack 1906 | Microsoft Docs
description: Seznamte se s aktualizací 1906 pro Azure Stack integrovaných systémů, včetně nových, známých problémů a místa, kde si tuto aktualizaci stáhnout.
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
ms.date: 07/15/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 07/15/2019
ms.openlocfilehash: ffd6436b9e7bf27ef069ecdc44ff2d6efadfc795
ms.sourcegitcommit: d96adbb821175167f6a4c8f3aba305981d7e7c3e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2019
ms.locfileid: "68685560"
---
# <a name="azure-stack-1906-update"></a>Aktualizace Azure Stack 1906

*Platí pro: Azure Stack integrovaných systémů*

Tento článek popisuje obsah balíčku aktualizace 1906. Tato aktualizace obsahuje novinky a opravy pro tuto verzi Azure Stack.

> [!IMPORTANT]  
> Tento balíček aktualizace je pouze pro integrované systémy Azure Stack. Tento balíček aktualizace nevztahují na Azure Stack Development Kit.

## <a name="build-reference"></a>Referenční informace o buildu

Číslo buildu aktualizace Azure Stack 1906 je **1.1906.0.30**.

### <a name="update-type"></a>Typ aktualizace

Typ sestavení aktualizace Azure Stack 1906 je **Express**. Další informace o typech sestavení aktualizace naleznete v článku [Správa aktualizací v Azure Stack](azure-stack-updates.md) . Očekávaný čas potřebný k dokončení aktualizace 1906 je přibližně 10 hodin, bez ohledu na počet fyzických uzlů v prostředí Azure Stack. Přesné běhové moduly pro aktualizace budou obvykle záviset na kapacitě používané v systému podle zatížení klientů, připojení k systémové síti (Pokud je připojeno k Internetu) a specifikacemi hardwaru systému. Běhové moduly trvající déle než očekávaná hodnota nejsou běžné a nevyžadují akci Azure Stack operátory, pokud se aktualizace nezdařila. Tato přibližná doba běhu je specifická pro aktualizaci 1906 a neměla by se porovnávat s jinými aktualizacemi Azure Stack.

## <a name="whats-in-this-update"></a>Co je v této aktualizaci

<!-- The current theme (if any) of this release. -->

<!-- What's new, also net new experiences and features. -->

- Do privilegovaného koncového bodu (PEP) se přidala rutina **set-TLSPolicy** , která vynutí TLS 1,2 u všech koncových bodů. Další informace najdete v tématu [Azure Stack kontroly zabezpečení](azure-stack-security-configuration.md).

- Do privilegovaného koncového bodu (PEP) se přidala rutina **Get-TLSPolicy** , která načte použité zásady TLS. Další informace najdete v tématu [Azure Stack kontroly zabezpečení](azure-stack-security-configuration.md).

- Přidání interního postupu pro rotaci tajných kódů pro otočení vnitřních certifikátů TLS podle požadavků při aktualizaci systému.

- Přidání ochrany, která zabraňuje vypršení platnosti interních tajných kódů tím, že vynucuje interní rotaci tajných kódů pro případ, že kritická výstraha na vypršení platnosti Tato činnost by se neměla spoléhat jako na běžný operační postup. Rotace tajných kódů by měla být naplánována během časového období údržby. Další informace najdete v tématu [Azure Stack rotaci tajných klíčů](azure-stack-rotate-secrets.md).

- Visual Studio Code se teď podporuje s nasazením Azure Stack pomocí AD FS.

### <a name="improvements"></a>Vylepšen

<!-- Changes and product improvements with tangible customer-facing value. -->

- Rutina **Get-GraphApplication** v privilegovaném koncovém bodu teď zobrazuje kryptografický otisk aktuálně používaného certifikátu. To zlepšuje správu certifikátů pro instanční objekty, když Azure Stack nasadíte pomocí AD FS.

- Byla přidána nová pravidla monitorování stavu, která ověřují dostupnost služby AD Graph a AD FS, včetně možnosti vyvolat výstrahy.

- Vylepšení spolehlivosti poskytovatele prostředků zálohování, když se služba infrastruktura zálohování přesune na jinou instanci.

- Optimalizace výkonu pro postup při rotaci externích tajných klíčů, který poskytuje jednotnou dobu provádění, která usnadňuje plánování časového období údržby.

- Rutina **test-AzureStack** nyní hlásí interní tajné klíče, jejichž platnost brzy vyprší (kritické výstrahy).

- V rámci privilegovaného koncového bodu je k dispozici nový parametr pro rutinu **Register-CustomAdfs** , která umožňuje přeskočí kontrolu seznamu odvolaných certifikátů při konfiguraci důvěryhodnosti federace pro AD FS.

- Vydání 1906 přináší lepší přehled o průběhu aktualizace, takže můžete mít jistotu, že se aktualizace nepozastavuje. Výsledkem je zvýšení celkového počtu kroků aktualizace zobrazených u operátorů v okně **aktualizace** . Můžete si také všimnout dalších kroků aktualizace, které probíhají paralelně než v předchozích aktualizacích.

#### <a name="networking-updates"></a>Aktualizace sítě

- Byla aktualizována doba zapůjčení nastavená v respondéru DHCP, aby byla konzistentní s Azure.

- Vylepšené sazby opakování pro poskytovatele prostředků ve scénáři selhání nasazení prostředků.

- Odebrali jsme možnost **Standard** SKU z nástroje pro vyrovnávání zatížení i z veřejné IP adresy, protože se v tuto chvíli nepodporuje.

### <a name="changes"></a>Změny

- Vytvoření prostředí účtu úložiště je teď konzistentní s Azure.

- Aktivační události změněné výstrahy pro vypršení platnosti interních tajných kódů:
  - Upozornění na upozornění se teď zvyšují 90 dní před vypršením platnosti tajných kódů.
  - Kritické výstrahy jsou nyní vyvolány 30 dní před vypršením platnosti tajných kódů.

- Aktualizované řetězce v poskytovateli prostředků zálohování infrastruktury pro konzistentní terminologii.

### <a name="fixes"></a>Opravy

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- Opravili jsme problém, kdy změna velikosti virtuálního počítače spravovaného disku selhala s **chybou vnitřní operace**.

- Opravili jsme problém, kdy vytvoření image uživatele, která selhala, umístí službu, která spravuje image, je ve špatném stavu. Tím se odstraní neúspěšné image a vytváření nových imagí. Tato oprava je také opravena v 1905.

- Aktivní výstrahy týkající se vypršení platnosti interních tajných kódů se teď po úspěšném provedení interního přestupnosti automaticky zavřou.

- Opravili jsme problém, ve kterém doba aktualizace na kartě Historie aktualizace ořízne první číslici, pokud byla aktualizace spuštěná déle než 99 hodin.

- Okno **aktualizace** obsahuje možnost **obnovení** pro neúspěšné aktualizace.

- V portálu pro správu a uživatele byly vyřešeny problémy v Marketplace, ve kterých bylo rozšíření Docker nesprávně vráceno ze služby Search, ale nebylo možné provést žádnou další akci, protože není k dispozici v Azure Stack.

- Opravili jsme problém v uživatelském rozhraní nasazení šablony, které neplní parametry, pokud název šablony začíná podtržítkem _.

- Opravili jsme problém, kdy prostředí pro vytváření sady škálování virtuálních počítačů poskytuje CentOS 7,2 jako možnost nasazení. CentOS 7,2 není na Azure Stack k dispozici. Jako naši možnost pro nasazení teď poskytujeme CentOS 7,5.

- V okně **Virtual Machine Scale Sets** teď můžete odebrat sadu škálování.

## <a name="security-updates"></a>Aktualizace zabezpečení

Informace o aktualizacích zabezpečení v této aktualizaci Azure Stack najdete v tématu [Azure Stack aktualizace zabezpečení](azure-stack-release-notes-security-updates-1906.md).

## <a name="update-planning"></a>Plánování aktualizací

Před použitím této aktualizace se ujistěte, že si provedete následující informace:

- [Známé problémy](azure-stack-release-notes-known-issues-1906.md)
- [Aktualizace zabezpečení](azure-stack-release-notes-security-updates-1906.md)
- [Kontrolní seznam aktivit před a po instalaci aktualizace](azure-stack-release-notes-checklist.md)

## <a name="download-the-update"></a>Stáhnout aktualizaci.

Balíček aktualizace Azure Stack 1906 můžete stáhnout ze [stránky pro stažení Azure Stack](https://aka.ms/azurestackupdatedownload).

## <a name="hotfixes"></a>Opravy hotfix

Azure Stack pravidelně vydává opravy hotfix. Před aktualizací Azure Stack na 1906 nezapomeňte nainstalovat nejnovější Azure Stack opravu hotfix pro 1905. Po aktualizaci nainstalujte všechny [dostupné opravy hotfix pro 1906](#after-successfully-applying-the-1906-update).

Azure Stack oprav hotfix se týkají pouze Azure Stack integrovaných systémů. Nepokoušejte se instalovat opravy hotfix na ASDK.

### <a name="before-applying-the-1906-update"></a>Před použitím aktualizace 1906

Vydání verze 1906 Azure Stack se musí použít ve verzi 1905 s následujícími opravami hotfix:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack opravy hotfix 1.1905.3.48](https://support.microsoft.com/help/4510078)

### <a name="after-successfully-applying-the-1906-update"></a>Po úspěšné instalaci aktualizace 1906

Po instalaci této aktualizace nainstalujte všechny příslušné opravy hotfix. Další informace najdete v našich [zásadách obsluhy](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack opravy hotfix 1.1906.11.52](https://support.microsoft.com/help/4513119)

## <a name="automatic-update-notifications"></a>Oznámení automatických aktualizací

Zákazníci se systémy, které mají přístup k Internetu z sítě infrastruktury, uvidí na portálu operátora zprávu **aktualizace k dispozici** . Systémy bez přístupu k Internetu můžou stáhnout a naimportovat soubor. zip s odpovídající příponou. XML.

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
