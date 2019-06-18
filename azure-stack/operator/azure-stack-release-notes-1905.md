---
title: Zpráva k vydání verze Azure Stack 1905 | Dokumentace Microsoftu
description: Další informace o aktualizaci 1905 pro integrované systémy Azure Stack, včetně novinek, známé problémy a kde se stáhnout aktualizaci.
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
ms.date: 06/14/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 06/14/2019
ms.openlocfilehash: 4c7e48804b4336c658376b1c3f611dbe75cf8c1f
ms.sourcegitcommit: 593d40bccf1b2957a763017a8a2d7043f8d8315c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2019
ms.locfileid: "67152475"
---
# <a name="azure-stack-1905-update"></a>Aktualizace služby Azure Stack 1905

*Platí pro: Integrované systémy Azure Stack*

Tento článek popisuje obsah balíčku aktualizace 1905. Obsahuje novinky vylepšení a oprav pro tuto verzi služby Azure Stack. Tento článek obsahuje následující informace:

- [Popis co je nového, zabezpečení, vylepšení a oprav aktualizací](#whats-in-this-update)
- [Plánování aktualizace](#update-planning)

> [!IMPORTANT]  
> Tento balíček aktualizace je pouze pro integrované systémy Azure Stack. Tento balíček aktualizace nevztahují na Azure Stack Development Kit.

## <a name="build-reference"></a>Referenční informace o buildu

Číslo sestavení aktualizace Azure Stack 1905 je **1.1905.0.40**.

### <a name="update-type"></a>Typ aktualizace

Typ sestavení update Azure Stack 1905 je **úplné**. Aktualizace 1905 v důsledku toho se modul runtime déle než rychlé aktualizace jako 1903 a 1904. Přesné modulů runtime pro úplné aktualizace obvykle závisí na počtu uzlů, že vaše instance služby Azure Stack obsahuje, kapacita použitá ve vašem systému úlohy klientů, váš systém připojení k síti (Pokud je připojený k Internetu) a svůj hardware systému konfigurace. Aktualizace 1905 zaznamenala následující očekávané moduly runtime při interním testování: 4 uzly – 35 hodin, 8 uzlů – hodin 45, 12 uzly - 55 hodin, 16 uzlů - 70 hodin. 1905 moduly runtime trvá déle, než tyto očekávané hodnoty nejsou výjimkou a nevyžadují akci operátoři Azure stacku, pokud se aktualizace nezdaří. Další informace o aktualizaci typy sestavení, naleznete v tématu [správy aktualizací ve službě Azure Stack](azure-stack-updates.md).

## <a name="whats-in-this-update"></a>Co je v této aktualizaci

<!-- The current theme (if any) of this release. -->

<!-- What's new, also net new experiences and features. -->

- S touto aktualizací můžete aktualizace modulu ve službě Azure Stack aktualizaci firmwaru uzlů jednotek škálování. To vyžaduje balíček aktualizace dodržování předpisů z hardwarových partnerů. Kontaktujte svého partnera hardwaru podrobnosti o dostupnosti.

- 2019 systému Windows Server je teď podporovaná a k dispozici pro publikování prostřednictvím Azure Marketplace zásobníku.
S touto aktualizací systému Windows Server 2019 lze nyní úspěšně aktivovat na hostiteli 2016.

### <a name="improvements"></a>Vylepšení

<!-- Changes and product improvements with tangible customer-facing value. -->
- Jako součást vynucení protokolu TLS 1.2 ve službě Azure Stack byly aktualizovány následující rozšíření na tyto verze:

  - microsoft.customscriptextension-arm-1.9.3
  - microsoft.iaasdiagnostics-1.12.2.2
  - microsoft.antimalware-windows-arm-1.5.5.9
  - microsoft.dsc-arm-2.77.0.0
  - microsoft.vmaccessforlinux-1.5.2

  Stáhněte si prosím tyto verze rozšíření okamžitě, tak, že nová nasazení rozšíření nezdaří, když se v budoucí verzi vynucuje TLS 1.2. Vždy nastavena **autoupdateminorversion = true** tak, aby prováděla automaticky vedlejší verze aktualizace rozšíření (například 1.8 k 1.9).

- Nový **Nápověda a podpora přehled** ve službě Azure Stack portál usnadňuje operátory zkontrolujte jejich možnosti podpory, získejte odbornou pomoc a další informace o službě Azure Stack. V integrovaných systémech vytváří se žádost o podporu se předem vybere služby Azure Stack. Důrazně doporučujeme, že zákazníci používat toto prostředí k odeslání lístky spíše než pomocí globální webu Azure portal. Další informace najdete v tématu [zásobníku Nápověda a podpora Azure](azure-stack-help-and-support-overview.md).

- Když Azure více Active Directory, jichž připojí se (prostřednictvím [tento proces](azure-stack-enable-multitenancy.md)), je možné setkávat opětným spuštěním skriptu při výskytu určitých aktualizací, nebo když práva, aby bylo způsobit změny k autorizaci instančního objektu služby AAD chybí. To může způsobit různé problémy, z k zablokování přístupu pro určité funkce, další diskrétní chybám, které se obtížně trasování zpět na původní problém. Chcete-li tomu zabránit, 1905 zavádí novou funkci, která kontroluje tato oprávnění a vytvoří výstrahu, pokud jsou nalezeny některé problémy s konfigurací. Toto ověření spouští každou hodinu a zobrazí nápravné akce potřebné k vyřešení problému. Výstraha se zavře po všech tenantů v dobrém stavu.

- Vylepšení spolehlivosti operací zálohování infrastruktury během převzetí služeb při selhání.

- Nová verze [modul plug-in Azure Stack Nagios](azure-stack-integrate-monitor.md#integrate-with-nagios) je k dispozici, která používá [knihovny Azure Active Directory authentication library](/azure/active-directory/develop/active-directory-authentication-libraries) (ADAL) pro ověřování. Modul plug-in teď také podporuje nasazení v Azure Active Directory (AAD) a Active Directory Federation Services (ADFS) služby Azure Stack. Další informace najdete v tématu [Nagios modulu plug-in exchange](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details) lokality.

- Nový profil hybridní **2019-03-01hybridní** byla vydána, který podporuje nejnovější funkce v Azure stacku. Prostředí Azure PowerShell a rozhraní příkazového řádku Azure podporují **2019-03-01hybridní** profilu. .NET, Ruby, Node.js, Go a sady SDK pro Python publikovali balíčky, které podporují **2019-03-01hybridní** profilu. V příslušné dokumentaci a ukázek se aktualizovaly tak, aby odrážely změny.

- [Sady Node.js SDK](https://www.npmjs.com/search?q=2019-03-01-hybrid) teď podporuje profily rozhraní API. Balíčky, které podporují **2019-03-01hybridní** profilu publikování.

- Aktualizace služby Azure Stack 1905 přidá dvě nové role infrastruktury ke zlepšení spolehlivosti platformy a možnosti podpory:

  - **Vyzvánění infrastrukturou**: Vyzvánění infrastrukturou v budoucnu se bude hostovat kontejnerizovaných verzích existující role infrastruktury – například xrp -, které aktuálně vyžadují vlastní určené infrastrukturu virtuálních počítačů. Tím vylepšíme platformy spolehlivost a snížit počet infrastruktury virtuálních počítačů, které vyžaduje Azure Stack. Následně to snižuje celkové spotřeby zdrojů rolích Azure stacku infrastruktury v budoucnu.
  - **Aktualizační kanál podpory**: V budoucnu kanál podpory se použije ke zpracování scénářů rozšířenou podporu pro zákazníky.  

  Kromě toho jsme přidali další instance virtuálního počítače řadiče domény pro lepší dostupnost této role.

  Tyto změny se zvýší využití prostředků infrastruktury Azure stacku následujícími způsoby:
  
    | Azure Stack SKU | Zvýšení využití výpočetních | Zvýšení využití paměti |
    | -- | -- | -- |
    |4 uzly|22 vCPU|28 GB|
    |8 uzlů|38 vCPU|44 GB|
    |12 uzly|54 vCPU|60 GB|
    |16 uzlů|70 vCPU|76 GB|
  
- Teď je k rozšíření Azure Stack, která funguje na Visual Studio Code. Použití **účet Azure** rozšíření, vývojáři mohou cílit Azure Stack přihlášení a zobrazit předplatná, jakož i celou řadou dalších služeb. Rozšíření Azure Account funguje na AAD prostředí i prostředí služby AD FS a vyžaduje pouze malou změnu v nastavení sady Visual Studio Code k zadání hodnoty metadat služby Azure Stack. Další informace najdete [naleznete v dokumentaci k](../user/azure-stack-dev-start-vscode-azure.md).

### <a name="changes"></a>Změny

- Pokud chcete zvýšit spolehlivost a dostupnost během plánované i neplánované údržby scénářů, Azure Stack přidá instanci další infrastrukturu role pro službu domain services.

- S tímto během opravy aktualizace a přidání uzlu operací na hardware je ověřena k zajištění homogenní škálovací jednotku uzlů v jednotce škálování.

- Pokud plánovaného zálohování se nedaří dokončit a ekročení doby definované uchovávání dat, infrastruktura zálohování řadiče se zajistěte, aby aspoň jeden úspěšné zálohy se uchovávají. 

### <a name="fixes"></a>Opravy

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- Opravili jsme problém, ve kterém **agent hostitele výpočetního** upozornění došlo po restartování uzlu v jednotce škálování.

- Opravené problémy v marketplace správu na portálu správce, které jsme si ukázali nesprávné výsledky, když filtry byly použity a jsme si ukázali vydavatele duplicitní názvy ve filtru vydavatele. Taky vylepšili také výkon k zobrazení výsledků rychleji.

- Oprava potíží v okně k dispozici zálohování, uvedený novou zálohu k dispozici před dokončením nahrát do externího úložiště. Teď dostupné zálohování se zobrazí v seznamu po úspěšném nahrání do umístění úložiště. 

<!-- ICM: 114819337; Task: 4408136 -->
- Oprava potíží s načítáním obnovovací klíče během operace zálohování. 

<!-- Bug: 4525587 -->
- Oprava potíží s OEM aktualizovat zobrazení verzi jako nedefinované portálu operátor.

### <a name="security-updates"></a>Aktualizace zabezpečení

Informace o aktualizacích zabezpečení v této aktualizaci služby Azure Stack najdete v tématu [aktualizace zabezpečení služby Azure Stack](azure-stack-release-notes-security-updates-1905.md).

## <a name="update-planning"></a>Plánování aktualizace

Před instalací této aktualizace, ujistěte se, že ke kontrole následující informace:

- [Známé problémy](azure-stack-release-notes-known-issues-1905.md)
- [Aktualizace zabezpečení](azure-stack-release-notes-security-updates-1905.md)
- [Kontrolní seznam před a po instalaci aktualizace aktivity](azure-stack-release-notes-checklist.md)

## <a name="download-the-update"></a>Stáhnout aktualizaci.

Můžete stáhnout aktualizace balíčku Azure Stack 1905 z [stránce pro stažení služby Azure Stack](https://aka.ms/azurestackupdatedownload). Při použití nástroje pro stahování, je potřeba použít nejnovější verzi a uložená v mezipaměti kopírování z adresáře ke stažení.

## <a name="hotfixes"></a>Opravy hotfix

Azure Stack vydává opravy hotfix v pravidelných intervalech. Nezapomeňte nainstalovat nejnovější opravy hotfix Azure Stack pro 1904 před aktualizací služby Azure Stack na 1905.

Azure Stack opravy hotfix platí pouze pro integrované systémy Azure Stack; Nepokoušejte se nainstalovat ASDK opravy hotfix.

### <a name="before-applying-the-1905-update"></a>Před použitím 1905 aktualizace

1905 vydání sady Azure Stack se musí použít na verzi 1904 s následující opravy hotfix:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack opravu hotfix 1.1904.4.45](https://support.microsoft.com/help/4505688)

### <a name="after-successfully-applying-the-1905-update"></a>Po úspěšném použití 1905 aktualizace

Po instalaci této aktualizace nainstalujte všechny příslušné opravy hotfix. Další informace najdete v tématu naše [zásady obsluhy](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- Žádné opravy hotfix pro 1905 k dispozici.

## <a name="automatic-update-notifications"></a>Upozornění na automatické aktualizace

Zákazníkům se systémy, které můžete přístup k Internetu z infrastruktury sítě se zobrazí **k dispozici je aktualizace** zpráva na portálu pro operátor. Systémy bez připojení k Internetu, můžete stáhnout a naimportovat soubor .zip s odpovídající soubor XML.

> [!TIP]  
> Předplatit následující *RSS* nebo *Atom* kanály, držet krok s Azure Stack opravy hotfix:
>
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

## <a name="archived-release-notes"></a>Archivované poznámky k verzi

Můžete zobrazit [starší verze služby Azure Stack poznámky v Galerii TechNet](http://aka.ms/azsarchivedrelnotes). Tyto archivované verze poznámky jsou k dispozici pouze pro referenční účely a není určeno, podporu pro tyto verze. Informace o podpoře Azure Stack najdete v tématu [Azure Stack zásady obsluhy](azure-stack-servicing-policy.md). Potřebujete další pomoc obraťte se na službu zákaznické podpory Microsoftu.

## <a name="next-steps"></a>Další postup

- Přehled správy aktualizací ve službě Azure Stack najdete v tématu [správy aktualizací ve službě Azure Stack přehled](azure-stack-updates.md).  
- Další informace o tom, jak použít aktualizace pomocí služby Azure Stack najdete v tématu [použití aktualizací ve službě Azure Stack](azure-stack-apply-updates.md).
- Zásady údržby pro integrované systémy Azure Stack, a co musíte udělat, aby byl váš systém v podporovaném stavu najdete v tématu [Azure Stack zásady obsluhy](azure-stack-servicing-policy.md).  
- Privilegované koncový bod (období) použít ke sledování a obnovit aktualizace, najdete v článku [monitorování aktualizací ve službě Azure Stack pomocí privilegovaných koncového bodu](azure-stack-monitor-update.md).  

