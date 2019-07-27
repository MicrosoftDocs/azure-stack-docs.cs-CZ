---
title: Zpráva k vydání verze Azure Stack 1905 | Microsoft Docs
description: Seznamte se s aktualizací 1905 pro Azure Stack integrovaných systémů, včetně nových, známých problémů a místa, kde si tuto aktualizaci stáhnout.
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
ms.openlocfilehash: 68b83ac38da20f341242618a1cfa62a9838a0ada
ms.sourcegitcommit: c2690b2dd36918ff3e47e359cac926128bb83101
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "68544088"
---
# <a name="azure-stack-1905-update"></a>Aktualizace Azure Stack 1905

*Platí pro: Azure Stack integrovaných systémů*

Tento článek popisuje obsah balíčku aktualizace 1905. Tato aktualizace obsahuje novinky a opravy pro tuto verzi Azure Stack. Tento článek obsahuje následující informace:

- [Popis novinek, vylepšení, oprav a aktualizací zabezpečení](#whats-in-this-update)
- [Plánování aktualizací](#update-planning)

> [!IMPORTANT]  
> Tento balíček aktualizace je pouze pro integrované systémy Azure Stack. Tento balíček aktualizace nevztahují na Azure Stack Development Kit.

## <a name="build-reference"></a>Referenční informace o buildu

Číslo buildu aktualizace Azure Stack 1905 je **1.1905.0.40**.

### <a name="update-type"></a>Typ aktualizace

Typ sestavení aktualizace Azure Stack 1905 je **plný**. V důsledku toho má aktualizace 1905 delší dobu běhu než expresní aktualizace jako 1903 a 1904. Přesné moduly runtime pro úplné aktualizace obvykle závisí na počtu uzlů, které vaše instance Azure Stack obsahuje, na kapacitě používané v systému podle úloh klientů, na síťovém připojení vašeho systému (Pokud je připojené k Internetu) a na vašem systémovém hardwaru. rozšířeného. Aktualizace 1905 měla v našem interním testování následující očekávané moduly runtime: 4 uzly – 35 hodin, 8 uzlů – 45 hodin, 12 uzlů – 55 hodin, 16 uzlů – 70 hodiny. 1905 za běhu trvající delší dobu než tyto očekávané hodnoty nejsou běžné a nevyžadují akci Azure Stack operátory, pokud se aktualizace nezdařila. Další informace o typech sestavení aktualizací najdete v tématu [Správa aktualizací v Azure Stack](azure-stack-updates.md).

## <a name="whats-in-this-update"></a>Co je v této aktualizaci

<!-- The current theme (if any) of this release. -->

<!-- What's new, also net new experiences and features. -->

- V této aktualizaci může aktualizační stroj v Azure Stack aktualizovat firmware uzlů jednotek škálování. To vyžaduje kompatibilní balíček aktualizace od hardwarových partnerů. Obraťte se na svého hardwarového partnera, který vám umožní získat podrobnosti o dostupnosti.

- Windows Server 2019 je teď podporovaný a dostupný pro syndikátování prostřednictvím Azure Stack Marketplace.
V této aktualizaci se teď dá úspěšně aktivovat Windows Server 2019 na hostiteli 2016.

- Nové [rozšíření Visual Studio Code účtu Azure](../user/azure-stack-dev-start-vscode-azure.md) umožňuje vývojářům cílit Azure Stack přihlášením a zobrazením předplatných a také mnoha dalších služeb. Rozšíření účtu Azure funguje na obou prostředích Azure Active Directory (Azure AD) i AD FS a vyžaduje jenom malou změnu v Visual Studio Code nastavení uživatele. Aby bylo možné spustit v tomto prostředí, Visual Studio Code vyžaduje, aby byl objekt služby udělen oprávnění. Provedete to tak, že naimportujete skript identity a v Azure Stack spustíte rutiny určené pro [více tenantů](../operator/azure-stack-enable-multitenancy.md). To vyžaduje aktualizaci domovského adresáře a registraci adresáře klienta hosta pro každý adresář. Po aktualizaci na 1905 nebo novější se zobrazí výstraha, aby se aktualizoval tenant domovského adresáře, pro který je zahrnutý Visual Studio Code instančního objektu. 

### <a name="improvements"></a>Vylepšen

<!-- Changes and product improvements with tangible customer-facing value. -->
- V rámci vynucování TLS 1,2 na Azure Stack se následující rozšíření aktualizovala na tyto verze:

  - microsoft.customscriptextension-arm-1.9.3
  - microsoft.iaasdiagnostics-1.12.2.2
  - microsoft.antimalware-windows-arm-1.5.5.9
  - microsoft.dsc-arm-2.77.0.0
  - microsoft.vmaccessforlinux-1.5.2

  Stáhněte si prosím tyto verze rozšíření hned, aby nová nasazení rozšíření nebyla úspěšná, když se TLS 1,2 vynutil v budoucí verzi. Vždy nastavte **autoUpgradeMinorVersion = true** , aby se automaticky prováděla aktualizace dílčí verze na rozšíření (například 1,8 až 1,9).

- Nový **Přehled Nápověda a podpora** na portálu Azure Stack usnadňuje operátorům kontrolovat jejich možnosti podpory, získat odbornou pomoc a získat další informace o Azure Stack. V případě integrovaných systémů bude při vytváření žádosti o podporu předem vybraná služba Azure Stack. Důrazně doporučujeme, aby zákazníci používali toto prostředí k odeslání lístků místo použití globálních Azure Portal. Další informace najdete v tématu [Azure Stack nápovědě a podpoře](azure-stack-help-and-support-overview.md).

- Při připojování více adresářů Azure Active Directory (prostřednictvím [tohoto procesu](azure-stack-enable-multitenancy.md)) je možné opomíjení znovu spustit skript, když dojde k určitým aktualizacím, nebo když změny v autorizaci instančního objektu služby Azure AD způsobují chybějící práva. To může způsobit různé problémy, od blokovaného přístupu k určitým funkcím až po více diskrétních selhání, u kterých je obtížné sledovat původní problém. Chcete-li tomu zabránit, 1905 zavádí novou funkci, která kontroluje tato oprávnění a vytvoří výstrahu, když budou zjištěny určité problémy s konfigurací. Toto ověření se spouští každou hodinu a zobrazuje nápravné akce potřebné k vyřešení problému. Výstraha se zavře, jakmile jsou všichni klienti v dobrém stavu.

- Zlepšená spolehlivost operací zálohování infrastruktury během převzetí služeb při selhání.

- K dispozici je nová verze [modulu plug-in Azure Stack Nagios](azure-stack-integrate-monitor.md#integrate-with-nagios) , která pro ověřování používá [knihovny Azure Active Directory Authentication](/azure/active-directory/develop/active-directory-authentication-libraries) Library (ADAL). Modul plug-in teď podporuje taky nasazení služby Azure AD a Active Directory Federation Services (AD FS) (AD FS) Azure Stack. Další informace najdete na webu [Exchange plugin Nagios](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details) .

- Nový hybridní profil **2019-03-01 –** byl vydán hybrid, který podporuje všechny nejnovější funkce v Azure Stack. Azure PowerShell i rozhraní příkazového řádku Azure podporují **hybridní profil 2019-03-01** . Sady SDK pro .NET, Ruby, Node. js, přejít a Python obsahují publikované balíčky podporující profil **2019-03-01-Hybrid** . Příslušná dokumentace a některé ukázky byly aktualizovány, aby odrážely změny.

- [Sada SDK pro Node. js](https://www.npmjs.com/search?q=2019-03-01-hybrid) teď podporuje profily rozhraní API. Jsou publikovány balíčky podporující profil **2019-03-01-Hybrid** .

- 1905 Azure Stack aktualizace přináší dvě nové role infrastruktury pro zlepšení spolehlivosti a podpory platforem:

  - **Kanál infrastruktury**: V budoucnu bude kanál infrastruktury hostovat verze stávajících rolí infrastruktury (například XRP), které aktuálně vyžadují vlastní určené virtuální počítače infrastruktury. Tím se Vylepšete spolehlivost platformy a sníží se počet virtuálních počítačů infrastruktury, které Azure Stack vyžaduje. Tím se následně omezí celková spotřeba prostředků Azure Stack rolí infrastruktury v budoucnu.
  - **Aktualizační kanál podpory**: V budoucnu se ke zpracování lepších scénářů podpory pro zákazníky použije aktualizační kanál podpory.  

  Kromě toho jsme přidali další instanci virtuálního počítače řadiče domény, aby se zlepšila dostupnost této role.

  Tyto změny zvyšují spotřebu prostředků Azure Stack infrastruktury následujícími způsoby:
  
    | SKU Azure Stack | Zvýšení výpočetní spotřeby | Zvýšení spotřeby paměti |
    | -- | -- | -- |
    |4 uzly|22 vCPU|28 GB|
    |8 uzlů|38 vCPU|44 GB|
    |12 uzlů|54 vCPU|60 GB|
    |16 uzlů|70 vCPU|76 GB|
  
### <a name="changes"></a>Změny

- Aby bylo možné zvýšit spolehlivost a dostupnost během plánovaných a neplánovaných scénářů údržby, Azure Stack přidá další instanci role infrastruktury pro doménové služby.

- Během této aktualizace během operace opravit a přidat uzel se ověří hardware, aby se zajistila homogenní uzly jednotek škálování v rámci jednotky škálování.

- Pokud se naplánované zálohy nedaří dokončit a dojde k překročení definované doby uchovávání, kontroler zálohování infrastruktury zajistí, že se zachová aspoň jedna úspěšná záloha. 

### <a name="fixes"></a>Opravy

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- Opravili jsme problém, ve kterém se zobrazilo upozornění **agenta COMPUTE Host** po restartování uzlu v jednotce škálování.

- Opravili jsme problémy se správou Marketplace na portálu pro správu, který ukázal nesprávné výsledky při použití filtrů, a v filtru vydavatele se zobrazily duplicitní názvy vydavatelů. Také vylepšení výkonu pro rychlejší zobrazení výsledků.

- Opravili jsme problém v okně dostupná záloha, které uvádí nové dostupné zálohování, než se dokončí odeslání do umístění externího úložiště. Po úspěšném nahrání do umístění úložiště se teď dostupná záloha zobrazí v seznamu. 

<!-- ICM: 114819337; Task: 4408136 -->
- Opravili jsme problém při načítání obnovovacích klíčů během operace zálohování. 

<!-- Bug: 4525587 -->
- Oprava potíží s aktualizací OEM, která zobrazuje verzi jako nedefinované, na portálu operátora

### <a name="security-updates"></a>Aktualizace zabezpečení

Informace o aktualizacích zabezpečení v této aktualizaci Azure Stack najdete v tématu [Azure Stack aktualizace zabezpečení](azure-stack-release-notes-security-updates-1905.md).

## <a name="update-planning"></a>Plánování aktualizací

Před použitím této aktualizace se ujistěte, že si provedete následující informace:

- [Známé problémy](azure-stack-release-notes-known-issues-1905.md)
- [Aktualizace zabezpečení](azure-stack-release-notes-security-updates-1905.md)
- [Kontrolní seznam aktivit před a po instalaci aktualizace](azure-stack-release-notes-checklist.md)

## <a name="download-the-update"></a>Stáhnout aktualizaci.

Balíček aktualizace Azure Stack 1905 můžete stáhnout ze [stránky pro stažení Azure Stack](https://aka.ms/azurestackupdatedownload). Při použití nástroje pro stahovací program se ujistěte, že používáte nejnovější verzi a nikoli kopii uloženou v mezipaměti z adresáře pro stahování.

## <a name="hotfixes"></a>Opravy hotfix

Azure Stack pravidelně vydává opravy hotfix. Před aktualizací Azure Stack na 1905 nezapomeňte nainstalovat nejnovější Azure Stack opravu hotfix pro 1904.

Azure Stack oprav hotfix se týkají pouze Azure Stack integrovaných systémů. Nepokoušejte se instalovat opravy hotfix na ASDK.

### <a name="before-applying-the-1905-update"></a>Před použitím aktualizace 1905

Vydání verze 1905 Azure Stack se musí použít ve verzi 1904 s následujícími opravami hotfix:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack opravy hotfix 1.1904.4.45](https://support.microsoft.com/help/4505688)

### <a name="after-successfully-applying-the-1905-update"></a>Po úspěšné instalaci aktualizace 1905

Po instalaci této aktualizace nainstalujte všechny příslušné opravy hotfix. Další informace najdete v našich [zásadách obsluhy](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack opravy hotfix 1.1905.3.48](https://support.microsoft.com/help/4510078)

## <a name="automatic-update-notifications"></a>Oznámení automatických aktualizací

Zákazníci se systémy, které mají přístup k Internetu z sítě infrastruktury, uvidí na portálu operátora zprávu **aktualizace k dispozici** . Systémy bez přístupu k Internetu můžou stáhnout a naimportovat soubor. zip s odpovídající příponou. XML.

> [!TIP]  
> Přihlaste se k odběru následujících informačních kanálů *RSS* nebo *Atom* , abyste zachovali Azure Stack opravy hotfix:
>
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Počtu](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

## <a name="archived-release-notes"></a>Archivované poznámky k verzi

[Starší verze Azure Stack poznámky k verzi najdete v Galerii TechNet](http://aka.ms/azsarchivedrelnotes). Tato Archivovaná zpráva k vydání verze je poskytována pouze pro referenční účely a neznamená podporu těchto verzí. Informace o podpoře Azure Stack najdete v tématu [zásady pro obsluhu Azure Stack](azure-stack-servicing-policy.md). Pokud potřebujete další pomoc, obraťte se na službu zákaznické podpory společnosti Microsoft.

## <a name="next-steps"></a>Další kroky

- Přehled správy aktualizací ve službě Azure Stack najdete v tématu [správy aktualizací ve službě Azure Stack přehled](azure-stack-updates.md).  
- Další informace o tom, jak použít aktualizace pomocí služby Azure Stack najdete v tématu [použití aktualizací ve službě Azure Stack](azure-stack-apply-updates.md).
- Zásady údržby pro integrované systémy Azure Stack, a co musíte udělat, aby byl váš systém v podporovaném stavu najdete v tématu [Azure Stack zásady obsluhy](azure-stack-servicing-policy.md).  
- Privilegované koncový bod (období) použít ke sledování a obnovit aktualizace, najdete v článku [monitorování aktualizací ve službě Azure Stack pomocí privilegovaných koncového bodu](azure-stack-monitor-update.md).  

