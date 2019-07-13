---
title: Zpráva k vydání verze Azure Stack. 1906 | Dokumentace Microsoftu
description: Další informace o aktualizaci. 1906 pro integrované systémy Azure Stack, včetně novinek, známé problémy a kde se stáhnout aktualizaci.
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
ms.date: 07/11/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 07/11/2019
ms.openlocfilehash: bd116e2e04f93b96655a32eb50f0057f0d5784ec
ms.sourcegitcommit: 0bb2fdeccda3183d0bf4572819a28af8ca5f244a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2019
ms.locfileid: "67863179"
---
# <a name="azure-stack-1906-update"></a>Aktualizace služby Azure Stack. 1906

*Platí pro: Integrované systémy Azure Stack*

Tento článek popisuje obsah balíčku. 1906 aktualizace. Obsahuje novinky vylepšení a oprav pro tuto verzi služby Azure Stack.

> [!IMPORTANT]  
> Tento balíček aktualizace je pouze pro integrované systémy Azure Stack. Tento balíček aktualizace nevztahují na Azure Stack Development Kit.

## <a name="build-reference"></a>Referenční informace o buildu

Je číslo sestavení aktualizace. Azure Stack 1906 **1.1906.0.30**.

### <a name="update-type"></a>Typ aktualizace

Typ sestavení. Azure Stack 1906 aktualizace je **Express**. Další informace o aktualizaci typy sestavení, najdete v článku [správy aktualizací ve službě Azure Stack](azure-stack-updates.md) článku. Očekávaná doba potřebná pro aktualizace. 1906 dokončete je přibližně 10 hodin, bez ohledu na počet fyzických uzlů ve vašem prostředí Azure Stack. Přesné aktualizovat moduly runtime bude obvykle závisí na kapacitě používané úlohy klientů, systému připojení k síti (Pokud je připojený k Internetu), ve vašem systému a vaše specifikace hardwaru systému. Moduly runtime trvá déle, než očekávaná hodnota nejsou výjimkou a nevyžadují akci operátoři Azure stacku, pokud se aktualizace nezdaří. Této aproximace modulu runtime je specifická pro aktualizace. 1906 a by neměl ve srovnání s jinými aktualizacemi Azure Stack.

## <a name="whats-in-this-update"></a>Co je v této aktualizaci

<!-- The current theme (if any) of this release. -->

<!-- What's new, also net new experiences and features. -->

- Přidání **Set-TLSPolicy** rutiny privileged koncový bod (období) k vynucení protokolu TLS 1.2 pro všechny koncové body. Další informace najdete v tématu [kontrolních mechanismů pro zabezpečení služby Azure Stack](azure-stack-security-configuration.md).

- Přidá **Get-TLSPolicy** rutiny privileged koncový bod (období) pro načtení použité zásady TLS. Další informace najdete v tématu [kontrolních mechanismů pro zabezpečení služby Azure Stack](azure-stack-security-configuration.md).

- Přidat proceduru interní tajných kódů otočení obměna interní certifikáty TLS podle potřeby během aktualizace systému.

- Přidat chránit, aby se zabránilo vypršení platnosti tajných kódů interní vynucením otočení interní tajných kódů v případě, že kritickou výstrahu na konce platnosti tajných kódů se ignoruje. To by neměl spoléhat jako regulární pracovní postup. Plánování otočení tajné klíče byste měli využít během časového období údržby. Další informace najdete v tématu [otočení tajných kódů služby Azure Stack](azure-stack-rotate-secrets.md).

- Visual Studio Code se teď podporuje nasazení Azure Stack pomocí služby AD FS.

### <a name="improvements"></a>Vylepšení

<!-- Changes and product improvements with tangible customer-facing value. -->

- **Get-GraphApplication** rutiny privileged koncový bod se teď zobrazují s kryptografickým otiskem certifikátu aktuálně používané. Tím se zlepšuje Správa certifikátů pro instanční objekty při nasazení Azure Stack se službou AD FS.

- Ověřit dostupnost AD Graphu a AD FS, včetně možnosti budou generovány výstrahy se přidaly nové pravidla monitorování stavu.

- Vylepšení spolehlivosti poskytovatele zálohování prostředků při zálohování služeb infrastruktury přesune do jiné instance.

- Optimalizace výkonu externích tajných kódů otočení procedury poskytnout jednotný čas spuštění pro usnadnění plánování časového období údržby.

- **Testovací AzureStack** rutiny teď informuje o interní tajné kódy, které se chystáte vyprší (kritické výstrahy).

- Nový parametr je k dispozici pro **Register-CustomAdfs** rutiny privileged koncový bod, který umožňuje přeskočení kontroly při konfiguraci důvěryhodnosti federace služby AD FS seznamu odvolaných certifikátů.

- Verze. 1906 zavádí lepší viditelnost do průběh aktualizace, můžete si být jistí, že se pozastavení aktualizací. Výsledkem je zvýšení celkový počet aktualizací postupy pro operátory v **aktualizovat** okno. Možná si také další aktualizace kroky děje paralelně než v předchozích aktualizací.

#### <a name="networking-updates"></a>Aktualizace sítě

- Doba zapůjčení aktualizované nastavit v DHCP respondér pro zajištění konzistence s Azure.

- Vylepšené opakování sazby u poskytovatele prostředků v případě selhání nasazení prostředků.

- Odebrat **standardní** SKU možnost z nástroje pro vyrovnávání zatížení a veřejnou IP adresu, jako, který se aktuálně nepodporuje.

### <a name="changes"></a>Změny

- Vytváření prostředí pro účet úložiště je nyní konzistentní s Azure.

- Výstraha aktivační události pro vypršení platnosti tajných kódů interní změnit:
  - Upozorňující výstrahy jsou generovány nyní 90 dnů před vypršením platnosti tajných kódů.
  - Kritické výstrahy jsou generovány nyní 30 dnů před vypršením platnosti tajných kódů.

- Aktualizované řetězců ve zprostředkovateli zálohování prostředků infrastruktury konzistentní Seznamte se s terminologií.

### <a name="fixes"></a>Opravy

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- Opravili jsme problém, kde změna velikosti spravovaného disku virtuálního počítače se nezdařilo **vnitřní chyba operace**.

- Opravili jsme problém, kdy vloží vytváření image uživatele se nezdařilo, služby, který spravuje Image je ve špatném stavu; Toto nastavení zabraňuje odstranění se nezdařilo bitové kopie a vytvoření nových imagí. Tento problém je také vyřešený v opravě hotfix 1905.

- Aktivní výstrahy týkající se u nichž vyprší platnost vnitřní tajné kódy jsou nyní automaticky zavře po úspěšném spuštění interní tajných kódů otáčení.

- Opravili jsme problém, ve kterém aktualizace trvání na kartě Historie aktualizace by trim první číslice Pokud aktualizace běžel déle než 99 hodin.

- **Aktualizace** okno obsahuje **Resume** možnost neúspěšné aktualizace.

- Uživatele a správce portály, byl opraven problém, na webu marketplace, ve kterém rozšíření Docker nesprávně vrátila hledání, ale žádná další akce lze provést, protože není k dispozici ve službě Azure Stack.

- Oprava potíží v nasazení šablony uživatelské rozhraní, které nevyplní parametry, pokud název šablon začíná podtržítkem '_'.

## <a name="security-updates"></a>Aktualizace zabezpečení

Informace o aktualizacích zabezpečení v této aktualizaci služby Azure Stack najdete v tématu [aktualizace zabezpečení služby Azure Stack](azure-stack-release-notes-security-updates-1906.md).

## <a name="update-planning"></a>Plánování aktualizace

Před instalací této aktualizace, ujistěte se, že ke kontrole následující informace:

- [Známé problémy](azure-stack-release-notes-known-issues-1906.md)
- [Aktualizace zabezpečení](azure-stack-release-notes-security-updates-1906.md)
- [Kontrolní seznam před a po instalaci aktualizace aktivity](azure-stack-release-notes-checklist.md)

## <a name="download-the-update"></a>Stáhnout aktualizaci.

Můžete stáhnout aktualizace balíčku. Azure Stack 1906 z [stránce pro stažení služby Azure Stack](https://aka.ms/azurestackupdatedownload).

## <a name="hotfixes"></a>Opravy hotfix

Azure Stack vydává opravy hotfix v pravidelných intervalech. Nezapomeňte nainstalovat nejnovější opravy hotfix Azure Stack pro 1905 před aktualizací služby Azure Stack. 1906. Po aktualizaci se některé nainstalují [dostupných oprav hotfix pro. 1906](#after-successfully-applying-the-1906-update).

Azure Stack opravy hotfix platí pouze pro integrované systémy Azure Stack; Nepokoušejte se nainstalovat ASDK opravy hotfix.

### <a name="before-applying-the-1906-update"></a>Před použitím. 1906 aktualizace

. 1906 vydání sady Azure Stack se musí použít na verzi 1905 s následující opravy hotfix:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack opravu hotfix 1.1905.3.48](https://support.microsoft.com/help/4510078)

### <a name="after-successfully-applying-the-1906-update"></a>Po použití úspěšně. 1906 aktualizace

Po instalaci této aktualizace nainstalujte všechny příslušné opravy hotfix. Další informace najdete v tématu naše [zásady obsluhy](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack opravu hotfix 1.1906.9.49](https://support.microsoft.com/help/4512794)

## <a name="automatic-update-notifications"></a>Upozornění na automatické aktualizace

Zákazníkům se systémy, které můžete přístup k Internetu z infrastruktury sítě se zobrazí **k dispozici je aktualizace** zpráva na portálu pro operátor. Systémy bez připojení k Internetu, můžete stáhnout a naimportovat soubor .zip s odpovídající soubor XML.

> [!TIP]  
> Předplatit následující *RSS* nebo *Atom* kanály, držet krok s Azure Stack opravy hotfix:
>
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

## <a name="archived-release-notes"></a>Archivované poznámky k verzi

Můžete zobrazit [starší verze služby Azure Stack poznámky v Galerii TechNet](https://aka.ms/azsarchivedrelnotes). Tyto archivované verze poznámky jsou k dispozici pouze pro referenční účely a není určeno, podporu pro tyto verze. Informace o podpoře Azure Stack najdete v tématu [Azure Stack zásady obsluhy](azure-stack-servicing-policy.md). Potřebujete další pomoc obraťte se na službu zákaznické podpory Microsoftu.

## <a name="next-steps"></a>Další postup

- Přehled správy aktualizací ve službě Azure Stack najdete v tématu [správy aktualizací ve službě Azure Stack přehled](azure-stack-updates.md).  
- Další informace o tom, jak použít aktualizace pomocí služby Azure Stack najdete v tématu [použití aktualizací ve službě Azure Stack](azure-stack-apply-updates.md).
- Zásady údržby pro integrované systémy Azure Stack, a co musíte udělat, aby byl váš systém v podporovaném stavu najdete v tématu [Azure Stack zásady obsluhy](azure-stack-servicing-policy.md).  
- Privilegované koncový bod (období) použít ke sledování a obnovit aktualizace, najdete v článku [monitorování aktualizací ve službě Azure Stack pomocí privilegovaných koncového bodu](azure-stack-monitor-update.md).  
