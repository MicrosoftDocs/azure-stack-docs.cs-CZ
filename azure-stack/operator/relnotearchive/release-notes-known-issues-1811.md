---
title: Aktualizace 1811 Azure Stack | Microsoft Docs
description: Seznamte se s aktualizací 1811 pro Azure Stack integrovaných systémů, včetně nových, známých problémů a místa, kde si tuto aktualizaci stáhnout.
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
ms.date: 04/09/2019
ms.author: sethm
ms.reviewer: adepue
ms.lastreviewed: 02/28/2019
ms.openlocfilehash: 3c852102b386cc0a625f628ed8c75319b112dfbd
ms.sourcegitcommit: f9be5640dd445b3d926c9ce3e2165e96c72ece89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2021
ms.locfileid: "100009260"
---
# <a name="azure-stack-1811-update"></a>Aktualizace Azure Stack 1811

*Platí pro: Azure Stack integrovaných systémů*

Tento článek popisuje obsah balíčku aktualizace 1811. Balíček aktualizace obsahuje vylepšení, opravy a nové funkce pro tuto verzi Azure Stack. Tento článek také popisuje známé problémy v této verzi a obsahuje odkaz, který vám umožní stáhnout aktualizaci. Známé problémy jsou rozděleny do potíží přímo souvisejících s procesem aktualizace a problémy se sestavením (po instalaci).

> [!IMPORTANT]  
> Tento balíček aktualizace je určen pouze pro Azure Stack integrovaných systémů. Nepoužívejte tento balíček aktualizace na Azure Stack Development Kit.

## <a name="build-reference"></a>Referenční informace o buildu

Číslo buildu aktualizace Azure Stack 1811 je **1.1811.0.101**.

## <a name="hotfixes"></a>Opravy hotfix

Azure Stack pravidelně vydává opravy hotfix. Před aktualizací Azure Stack na 1811 nezapomeňte nainstalovat [nejnovější Azure Stack opravu hotfix](#azure-stack-hotfixes) pro 1809.

### <a name="azure-stack-hotfixes"></a>Azure Stack opravy hotfix

- **1809**: [KB 4481548 – Azure Stack hotfix 1.1809.12.114](https://support.microsoft.com/help/4481548/)
- **1811**: není k dispozici žádná aktuální oprava hotfix.

## <a name="prerequisites"></a>Požadavky

> [!IMPORTANT]
> Během instalace aktualizace 1811 je nutné zajistit, aby byly všechny instance portálu pro správu zavřeny. Uživatelský portál může zůstat otevřený, ale portál pro správu musí být uzavřený.

- Získejte nasazení Azure Stack připravené pro hostitele rozšíření Azure Stack. Připravte systém pomocí následujících pokynů: [Příprava na hostitele rozšíření pro Azure Stack](../azure-stack-extension-host-prepare.md). 
 
- Před aktualizací na 1811 nainstalujte [nejnovější Azure Stack opravu hotfix](#azure-stack-hotfixes) pro 1809.

- Před zahájením instalace této aktualizace spusťte rutinu [test-AzureStack](../azure-stack-diagnostic-test.md) s následujícími parametry, abyste ověřili stav svého Azure Stack a vyřešili jsme zjištěné provozní problémy, včetně všech upozornění a selhání. Zkontrolujte také aktivní výstrahy a vyřešte všechny akce, které vyžadují.  

    ```powershell
    Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

    Pokud nemáte splněné požadavky na hostitele rozšíření, zobrazí se ve `Test-AzureStack` výstupu následující zpráva: 
  
    `To proceed with installation of the 1811 update, you will need to import 
    the SSL certificates required for Extension Host, which simplifies network 
    integration and increases the security posture of Azure Stack. Refer to this 
    link to prepare for Extension Host:
    https://docs.microsoft.com/azure-stack/operator/azure-stack-extension-host-prepare`

- Aktualizace Azure Stack 1811 vyžaduje, abyste správně naimportovali povinné certifikáty hostitele rozšíření do prostředí Azure Stack. Chcete-li pokračovat v instalaci aktualizace 1811, je nutné importovat certifikáty SSL vyžadované pro hostitele rozšíření. Informace o importu certifikátů najdete v [této části](../azure-stack-extension-host-prepare.md#import-extension-host-certificates).

    Pokud ignorujete všechna upozornění a stále se rozhodnete nainstalovat aktualizaci 1811, aktualizace se nezdařila přibližně 1 hodinu s následující zprávou:   
 
    `The required SSL certificates for the Extension Host have not been found.
    The Azure Stack update will halt. Refer to this link to prepare for 
    Extension Host: https://docs.microsoft.com/azure-stack/operator/azure-stack-extension-host-prepare,
    then resume the update.
    Exception: The Certificate path does not exist: [certificate path here]` 
 
    Po správném importování povinných hostitelských certifikátů rozšíření můžete aktualizaci 1811 obnovit z portálu pro správu. Zatímco společnost Microsoft upozorňuje Azure Stack operátory k naplánování časového období údržby během procesu aktualizace, selhání kvůli chybějícím certifikátům hostitele rozšíření by nemělo mít vliv na stávající úlohy nebo služby.  

    Během instalace této aktualizace není v průběhu konfigurace hostitele rozšíření k dispozici Azure Stack User Portal. Konfigurace hostitele rozšíření může trvat až 5 hodin. Během této doby můžete zkontrolovat stav aktualizace nebo obnovit instalaci neúspěšné aktualizace pomocí [Azure Stack PowerShellu s oprávněními správce nebo privilegovaného koncového bodu](../azure-stack-monitor-update.md).

- Pokud je Azure Stack spravováno pomocí System Center Operations Manager (SCOM), nezapomeňte před použitím 1811 aktualizovat sadu Management Pack pro Microsoft Azure Stack na verzi 1.0.3.11.

## <a name="new-features"></a>Nové funkce

Tato aktualizace obsahuje následující nové funkce a vylepšení pro Azure Stack:

- V této verzi je [hostitel rozšíření](../azure-stack-extension-host-prepare.md) povolený. Hostitel rozšíření zjednodušuje integraci sítě a zlepšuje stav zabezpečení Azure Stack.

- Přidání podpory pro ověřování zařízení pomocí služby Active Directory federovaným Services (AD FS) při použití Azure CLI konkrétně Další informace najdete v tématu [použití profilů verzí rozhraní API pomocí Azure CLI v systému Azure Stack](../../user/azure-stack-version-profiles-azurecli2.md)

- Přidání podpory pro instanční objekty pomocí tajného klíče klienta se službou Active Directory federovaným Services (AD FS). Další informace najdete v tématu [Vytvoření instančního objektu pro AD FS](../azure-stack-create-service-principals.md#manage-an-azure-ad-app-identity).

- Tato verze přidává podporu pro následující verze služby Azure Storage Service API: **2017-07-29**, **2017-11-09**. Do těchto Azure Storage verzí rozhraní API poskytovatele prostředků se přidá taky podpora: **2016-05-01**, **2016-12-01**, **2017-06-01** a **2017-10-01**. Další informace najdete v tématu [Azure Stack Storage: rozdíly a požadavky](../../user/azure-stack-acs-differences.md).

- Přidali jsme nové příkazy privilegovaného koncového bodu, které aktualizují a odstraňují Principy služby AD FS. Další informace najdete v tématu [Vytvoření instančního objektu pro AD FS](../azure-stack-create-service-principals.md#manage-an-azure-ad-app-identity).

- Byly přidány nové operace uzlu jednotky škálování, které umožňují operátoru Azure Stack spustit, zastavit a vypnout uzel jednotky škálování. Další informace najdete v tématu [akce uzlu škálování jednotky v Azure Stack](../azure-stack-node-actions.md).

- Přidáno okno vlastností nové oblasti, které zobrazuje podrobnosti o registraci prostředí. Tyto informace můžete zobrazit kliknutím na dlaždici **Správa oblastí** na výchozím řídicím panelu na portálu pro správu a následným výběrem možnosti **vlastnosti**.

- Přidání nového příkazu privilegovaného koncového bodu pro aktualizaci přihlašovacích údajů řadiče pro správu základní desky pomocí uživatelského jména a hesla, který se používá ke komunikaci s fyzickými počítači. Další informace najdete v tématu [aktualizace \( přihlašovacích údajů řadiče pro správu základní desky (BMC)](../azure-stack-rotate-secrets.md).

- Do pravého horního rohu správce a uživatelských portálů se přizpůsobila možnost přístupu k plánu Azure, podobně jako je k dispozici v Azure Portal.

- Přidali jsme vylepšené prostředí pro správu Marketplace pro odpojené uživatele. Proces nahrávání pro publikování položky Marketplace v odpojeném prostředí je zjednodušený pro jeden krok, místo aby se nahrála image a balíček Marketplace samostatně. Nahraný produkt se zobrazí také v okně Správa Marketplace.

- V této verzi se zmenší časový interval pro správu a údržbu pro rotaci tajných kódů tím, že během [Azure Stackho rozšiřování tajných](../azure-stack-rotate-secrets.md)kódů přidáte možnost Otočit jenom externí certifikáty

- [Prostředí Azure Stack PowerShell](../azure-stack-powershell-install.md) bylo aktualizováno na verzi 1.6.0. Tato aktualizace zahrnuje podporu pro nové funkce související s úložištěm v Azure Stack. Další informace najdete v poznámkách k verzi pro [modul správy Azure Stack 1.6.0 v Galerie prostředí PowerShell](https://www.powershellgallery.com/packages/AzureStack/1.6.0) informace o aktualizaci nebo instalaci Azure Stack PowerShellu najdete v tématu [instalace powershellu pro Azure Stack](../azure-stack-powershell-install.md).

- Managed Disks je teď ve výchozím nastavení povolený, když vytváříte virtuální počítače pomocí portálu Azure Stack. V části [známé problémy](#known-issues-post-installation) najdete další kroky potřebné k tomu, aby Managed disks nedocházelo k selháním při vytváření virtuálních počítačů.

- Tato verze zavádí akce **oprav** výstrah pro operátor Azure Stack. Některé výstrahy v 1811 poskytují tlačítko **opravit** v upozornění, které můžete vybrat k vyřešení problému. Další informace najdete v tématu [monitorování stavu a výstrah v Azure Stack](../azure-stack-monitor-health.md).

- Aktualizace možnosti aktualizace v Azure Stack. Mezi vylepšení aktualizací patří: 
  - Karty, které rozdělí aktualizace z historie aktualizací pro lepší sledování probíhajících aktualizací a dokončených aktualizací.
  - Rozšířené vizualizace stavu v části Essentials s novými ikonami a rozložením pro aktuální verze a verze OEM a také datum poslední aktualizace.
  - Odkaz **Zobrazit** pro sloupec poznámky k verzi převezme uživatele přímo do dokumentace specifické pro danou aktualizaci místo na stránce Obecná aktualizace.
  - Karta **Historie aktualizace** slouží k určení časů spuštění pro jednotlivé aktualizace a také k možnostem rozšířeného filtrování.  
  - Jednotky škálování Azure Stack, které jsou k dispozici, budou stále automaticky získávat **dostupné aktualizace** , jakmile budou k dispozici.
  - Jednotky škálování Azure Stack, které nejsou připojené, můžou naimportovat aktualizace stejně jako dřív. 
  - V procesu nejsou žádné změny ke stažení protokolů JSON z portálu. Azure Stack operátory uvidí postup při rozbalování kroků.

    Další informace najdete v tématu [Apply Updates in Azure Stack](../azure-stack-apply-updates.md).


## <a name="fixed-issues"></a>Opravené problémy

<!-- TBD - IS ASDK --> 
- Opravili jsme problém, kdy data měřiče využití veřejné IP adresy ukázaly stejnou hodnotu **EventDateTime** pro každý záznam místo razítka **timedate** , které ukazuje, kdy byl záznam vytvořen. Tato data teď můžete použít k přesnému zúčtování využití veřejné IP adresy.

<!-- 3099544 – IS, ASDK --> 
- Opravili jsme problém, ke kterému došlo při vytváření nového virtuálního počítače pomocí portálu Azure Stack. Výběr velikosti virtuálního počítače způsobil, že sloupec **USD/měsíc** zobrazuje **nedostupnou** zprávu. Tento sloupec se již nezobrazuje; zobrazení sloupce s cenami virtuálních počítačů není v Azure Stack podporováno.

<!-- 2930718 - IS ASDK --> 
- Opravili jsme problém, kdy portál pro správu při přístupu k podrobnostem každého předplatného uživatele po zavření okna a kliknutí na **Poslední** nezobrazuje název předplatného uživatele. Nyní se zobrazí název předplatného uživatele.

<!-- 3060156 - IS ASDK --> 
- Opravili jsme problém na portále správce i uživatele: klikněte na nastavení portálu a vyberte **Odstranit všechna nastavení a soukromé řídicí panely** nefungují podle očekávání a zobrazí se oznámení o chybě. Tato možnost teď funguje správně.

<!-- 2930799 - IS ASDK --> 
- Opravili jsme problém na portále správce i uživatele: v části **všechny služby** byly **plány ochrany Asset DDoS** nesprávně uvedené. Není k dispozici v Azure Stack. Výpis byl odebrán.
 
<!--2760466 – IS  ASDK --> 
- Opravili jsme problém, ke kterému došlo při instalaci nového prostředí Azure Stack, ve kterém se výstraha, která indikuje, že se **vyžaduje aktivace** , nezobrazila. Nyní se zobrazí správně.

<!--1236441 – IS  ASDK --> 
- Opravili jsme problém, který zabránil použití zásad RBAC pro skupinu uživatelů při používání služby AD FS.

<!--3463840 - IS, ASDK --> 
- Opravili jsme problém se selháním zálohování infrastruktury kvůli nedostupnému souborovému serveru ze sítě veřejné VIP. Tato oprava přesune službu zálohování infrastruktury zpátky do veřejné sítě infrastruktury. Pokud jste použili nejnovější [Azure Stack opravu hotfix pro 1809](#azure-stack-hotfixes) , která tento problém řeší, aktualizace 1811 neprovede žádné další úpravy. 

<!-- 2967387 – IS, ASDK --> 
- Opravili jsme problém, kdy se účet, který jste použili pro přihlášení ke Správci Azure Stack nebo k portálu User Portal, zobrazil jako **neidentifikovaný uživatel**. Tato zpráva se zobrazí, pokud u účtu nebylo zadáno buď **křestní** jméno, nebo **příjmení** .   

<!--  2873083 - IS ASDK --> 
- Opravili jsme problém, kdy použití portálu k vytvoření sady škálování virtuálních počítačů (VMSS) způsobilo, že se rozevírací nabídka **velikost instance** při použití Internet Exploreru nenačte správně. Tento prohlížeč teď funguje správně.  

<!-- 3190553 - IS ASDK -->
- Opravili jsme problém, který generoval výstrahy proti chybám, které signalizují, že instance role infrastruktury není k dispozici nebo že uzel jednotky se změnil na offline.

<!-- 2724961 - IS ASDK -->
- Fiexed problém, ve kterém se stránka s přehledem virtuálního počítače nedá správně zobrazit v grafu metriky virtuálních počítačů. 

## <a name="changes"></a>Změny

- Nový způsob zobrazení a úprav kvót v plánu se zavádí v 1811. Další informace najdete v tématu [zobrazení stávající kvóty](../azure-stack-quota-types.md#view-an-existing-quota).

<!-- 3083238 IS -->
- Vylepšení zabezpečení v této aktualizaci vede ke zvýšení velikosti zálohy role adresářové služby. Aktualizované pokyny k změně velikosti pro umístění externího úložiště najdete v [dokumentaci k zálohování infrastruktury](../azure-stack-backup-reference.md#storage-location-sizing). Tato změna má za následek delší dobu k dokončení zálohování kvůli větší velikosti přenosu dat. Tato změna se týká integrovaných systémů. 

- Existující rutina PEP pro načtení klíčů pro obnovení BitLockeru se přejmenuje v 1811, od Get-AzsCsvsRecoveryKeys k Get-AzsRecoveryKeys. Další informace o tom, jak načíst klíče pro obnovení BitLockeru, najdete v tématu [pokyny k načtení klíčů](../azure-stack-security-bitlocker.md).

## <a name="common-vulnerabilities-and-exposures"></a>Běžné chyby zabezpečení a ohrožení

Tato aktualizace nainstaluje tyto aktualizace zabezpečení:  

- [CVE-2018-8256](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8256)
- [CVE-2018-8407](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8407)
- [CVE-2018-8408](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8408)
- [CVE-2018-8415](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8415)
- [CVE-2018-8417](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8417)
- [CVE-2018-8450](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8450)
- [CVE-2018-8471](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8471)
- [CVE-2018-8476](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8476)
- [CVE-2018-8485](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8485)
- [CVE-2018-8544](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8544)
- [CVE-2018-8547](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8547)
- [CVE-2018-8549](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8549)
- [CVE-2018-8550](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8550)
- [CVE-2018-8553](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8553)
- [CVE-2018-8561](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8561)
- [CVE-2018-8562](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8562)
- [CVE-2018-8565](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8565)
- [CVE-2018-8566](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8566)
- [CVE-2018-8584](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8584)

Další informace o těchto chybách zabezpečení získáte kliknutím na výše uvedené odkazy nebo v článku znalostní báze Microsoft Knowledge Base [4478877](https://support.microsoft.com/help/4478877).

## <a name="known-issues-with-the-update-process"></a>Známé problémy s procesem aktualizace

- Když spustíte rutinu **Get-AzureStackLog** prostředí PowerShell po spuštění rutiny **test-AzureStack** ve stejné relaci PEP (Privileged Endpoint), rutina **Get-AzureStackLog** se nezdařila. Pokud chcete tento problém obejít, zavřete relaci PEP, ve které jste provedli **test-AzureStack**, a potom otevřete novou relaci pro spuštění rutiny **Get-AzureStackLog**.

- Během instalace aktualizace 1811 se ujistěte, že se během této doby zavřou všechny instance portálu pro správu. Uživatelský portál může zůstat otevřený, ale portál pro správu musí být uzavřený.

- Při spuštění rutiny [test-AzureStack](../azure-stack-diagnostic-test.md), pokud **AzsInfraRoleSummary** nebo **AzsPortalApiSummary** test dojde k chybě, budete vyzváni ke spuštění rutiny **test-AzureStack** s `-Repair` příznakem.  Pokud spustíte tento příkaz, dojde k chybě s následující chybovou zprávou:  `Unexpected exception getting Azure Stack health status. Cannot bind argument to parameter 'TestResult' because it is null.`  Tento problém bude opraven v budoucí verzi.

- Během instalace aktualizace 1811 není během konfigurace hostitele rozšíření k dispozici portál Azure Stack použít portál. Konfigurace hostitele rozšíření může trvat až 5 hodin. Během této doby můžete zkontrolovat stav aktualizace nebo obnovit instalaci neúspěšné aktualizace pomocí [Azure Stack PowerShellu s oprávněními správce nebo privilegovaného koncového bodu](../azure-stack-monitor-update.md). 

- Během instalace aktualizace 1811 nemusí být řídicí panel uživatelského portálu dostupný a může dojít ke ztrátě vlastního nastavení. Řídicí panel můžete po dokončení aktualizace obnovit na výchozí nastavení tak, že otevřete nastavení portálu a vyberete **Obnovit výchozí nastavení**.

- Při spuštění rutiny [test-AzureStack](../azure-stack-diagnostic-test.md)se zobrazí zpráva s upozorněním z řadiče pro správu základní desky (BMC). Toto upozornění můžete klidně ignorovat.

- <!-- 2468613 - IS --> Během instalace této aktualizace se můžou zobrazit výstrahy s názvem, `Error – Template for FaultType UserAccounts.New is missing.`  které tyto výstrahy můžou bezpečně ignorovat. Výstrahy se po dokončení instalace této aktualizace automaticky zavřou.

- <!-- 3139614 | IS --> Pokud jste od výrobce OEM použili aktualizaci Azure Stack, na portálu Azure Stack správce se nemusí zobrazovat oznámení **aktualizace k dispozici** . Pokud chcete nainstalovat Microsoft Update, Stáhněte si ho a naimportujte ho ručně podle pokynů [v tématu použití aktualizací v Azure Stack](../azure-stack-apply-updates.md).

## <a name="post-update-steps"></a>Kroky po aktualizaci

- Po instalaci této aktualizace nainstalujte všechny příslušné opravy hotfix. Další informace najdete v tématu [opravy hotfix](#hotfixes)a [zásady obsluhy](../azure-stack-servicing-policy.md).  

- Načtěte data z neaktivních šifrovacích klíčů a bezpečně je uložte mimo vaše nasazení Azure Stack. Postupujte podle [pokynů pro načtení klíčů](../azure-stack-security-bitlocker.md).

## <a name="known-issues-post-installation"></a>Známé problémy (po instalaci)

Níže jsou uvedené známé problémy po instalaci této verze buildu.

### <a name="portal"></a>Portál

<!-- 2930820 - IS ASDK --> 
- Při hledání "Docker" na portále správce i v uživatelských portálech se položka nesprávně vrátí. Není k dispozici v Azure Stack. Pokud se pokusíte ho vytvořit, zobrazí se okno s indikací chyby. 

<!-- 2931230 – IS  ASDK --> 
- Plány, které se přidají k předplatnému uživatele jako doplněk, nejde odstranit, i když z předplatného uživatele odeberete plán. Plán zůstane, dokud nebudou odstraněny také odběry, které odkazují na plán doplňku. 

<!-- TBD - IS ASDK --> 
- Nelze použít dva typy předplatného pro správu, které byly představeny s verzí 1804. Typy předplatného jsou odběry **měření** a **předplatné spotřeby**. Tyto typy předplatného jsou viditelné v nových Azure Stack prostředí od verze 1804, ale ještě nejsou připravené k použití. Měli byste dál používat výchozí typ předplatného **poskytovatele** .

<!-- TBD - IS ASDK --> 
- Výsledkem odstranění předplatných uživatelů jsou osamocené prostředky. Jako alternativní řešení nejprve odstraňte prostředky uživatelů nebo celou skupinu prostředků a pak odstraňte odběry uživatelů.

<!-- TBD - IS ASDK --> 
- Oprávnění k vašemu předplatnému nemůžete zobrazit pomocí Azure Stackch portálů. Alternativním řešením je [ověřit oprávnění pomocí PowerShellu](/powershell/module/azs.subscriptions.admin/get-azssubscriptionplan).

### <a name="health-and-monitoring"></a>Stav a monitorování

<!-- 1264761 - IS ASDK -->  
- Můžou se zobrazit výstrahy pro součást **kontroleru stavu** s následujícími podrobnostmi:  

  - #1 výstrahy:
     - Název: role infrastruktury není v pořádku.
     - ZÁVAŽNOST: upozornění
     - SOUČÁST: kontroler stavu
     - Popis: skener prezenčního signálu kontroleru stavu není k dispozici. To může mít vliv na sestavy stavu a metriky.  

  - #2 výstrahy:
     - Název: role infrastruktury není v pořádku.
     - ZÁVAŽNOST: upozornění
     - SOUČÁST: kontroler stavu
     - Popis: není k dispozici skener chyb řadiče stavu. To může mít vliv na sestavy stavu a metriky.

    Obě výstrahy se můžou ignorovat. V průběhu času se automaticky zavřou.  

### <a name="compute"></a>Compute

- Když vytváříte nový virtuální počítač s Windows, musíte pro pokračování v okně **Nastavení** vybrat veřejný port pro příchozí spojení. V 1811 se toto nastavení vyžaduje, ale nemá žádný vliv. Důvodem je to, že tato funkce závisí na Azure Firewall, která není implementována v Azure Stack. Můžete vybrat možnost **žádné veřejné příchozí porty** ani žádná z dalších možností, abyste mohli pokračovat v vytváření virtuálních počítačů. Nastavení nebude mít žádný vliv.

- Při vytváření nového virtuálního počítače s Windows se může zobrazit následující chyba:

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   K této chybě dojde, pokud povolíte diagnostiku spouštění na virtuálním počítači, ale odstraníte účet úložiště diagnostiky spouštění. Pokud chcete tento problém obejít, vytvořte znovu účet úložiště se stejným názvem, jako jste použili dříve.

- Při vytváření [virtuálního počítače s Dv2 Series](../../user/azure-stack-vm-considerations.md#vm-sizes)se D11 virtuální počítače 14v2 vám umožní vytvořit datové disky 4, 8, 16 a 32. V podokně vytvořit virtuální počítač se ale zobrazují datové disky 8, 16, 32 a 64.

- Záznamy o využití v Azure Stack můžou obsahovat neočekávanou velikost písmen. například:

   `{"Microsoft.Resources":{"resourceUri":"/subscriptions/<subid>/resourceGroups/ANDREWRG/providers/Microsoft.Compute/
   virtualMachines/andrewVM0002","location":"twm","tags":"null","additionalInfo":
   "{\"ServiceType\":\"Standard_DS3_v2\",\"ImageType\":\"Windows_Server\"}"}}`

   V tomto příkladu by měl být název skupiny prostředků **AndrewRG**. Tuto nekonzistenci můžete bezpečně ignorovat.

<!-- 3235634 – IS, ASDK -->
- Nasazení virtuálních počítačů se velikostmi, které obsahují příponu **v2** ; například **Standard_A2_v2** zadejte příponu jako **Standard_A2_v2** (malá písmena v). Nepoužívejte **Standard_A2_V2** (velká písmena V). Tato funkce funguje v globálním Azure a jedná se o nekonzistenci na Azure Stack.

<!-- 2869209 – IS, ASDK --> 
- Při použití [rutiny **Add-AzsPlatformImage**](/powershell/module/azs.compute.admin/add-azsplatformimage)musíte použít parametr **-OsUri** jako identifikátor URI účtu úložiště, kam se disk nahrává. Pokud použijete místní cestu k disku, rutina se nezdařila s následující chybou: 

    `Long running operation failed with status 'Failed'`

<!--  2795678 – IS, ASDK --> 
- Když použijete portál k vytváření virtuálních počítačů ve velikosti virtuálního počítače Premium (DS, Ds_v2, FS, FSv2), virtuální počítač se vytvoří v účtu úložiště úrovně Standard. Vytvoření v účtu úložiště úrovně Standard nemá vliv na funkce, IOPs ani fakturaci. Můžete bezpečně ignorovat upozornění, které říká: 

    `You've chosen to use a standard disk on a size that supports premium disks. This could impact operating system performance and is not recommended. Consider using premium storage (SSD) instead.`

<!-- 2967447 - IS, ASDK --> 
- Prostředí pro vytváření VMSS (Virtual Machine Scale set) poskytuje možnost nasazení na bázi CentOS 7,2. Vzhledem k tomu, že tato image není k dispozici na Azure Stack, vyberte jiný operační systém pro vaše nasazení nebo použijte šablonu Azure Resource Manager určující jinou image CentOS, která byla stažena před nasazením z webu Marketplace pomocí operátoru.  

<!-- 2724873 - IS --> 
- Při použití rutin PowerShellu **Start-AzsScaleUnitNode** nebo  **stop-AzsScaleUnitNode** pro správu jednotek škálování se může stát, že první pokus o spuštění nebo zastavení jednotky škálování selže. Pokud rutina při prvním spuštění neprojde, spusťte rutinu podruhé. Druhý běh by měl úspěšně dokončit operaci. 

<!-- TBD - IS ASDK --> 
- Pokud zřízení rozšíření na nasazení virtuálního počítače trvá moc dlouho, nechte časový limit zřízení místo toho, abyste se pokoušeli zastavit proces navrácení nebo odstranění virtuálního počítače.  

<!-- 1662991 IS ASDK --> 
- Diagnostika virtuálního počítače se systémem Linux není v Azure Stack podporována. Když nasadíte virtuální počítač se systémem Linux s povoleným diagnostikou virtuálních počítačů, nasazení se nezdařilo. Nasazení se také nepovede, pokud povolíte základní metriky virtuálního počítače se systémem Linux prostřednictvím nastavení diagnostiky.  

<!-- 3507629 - IS, ASDK --> 
- Managed Disks vytvoří dva nové [typy výpočetních kvót](../azure-stack-quota-types.md#compute-quota-types) a omezí tak maximální kapacitu spravovaných disků, které se dají zřídit. Ve výchozím nastavení je 2048 GiB přiděleno pro každý typ kvóty spravované disky. Mohou však nastat následující problémy:

   - U kvót vytvořených před aktualizací 1808 zobrazí kvóta Managed Disks 0 hodnot na portálu pro správu, i když je přidělený 2048 GiB. Hodnotu můžete zvýšit nebo snížit podle svých skutečných potřeb a Tato nově nastavená hodnota kvóty přepíše GiB výchozí hodnotu 2048.
   - Pokud hodnotu kvóty aktualizujete na 0, bude ekvivalentní výchozí hodnotě 2048 GiB. Jako alternativní řešení nastavte hodnotu kvóty na 1.

<!-- TBD - IS ASDK --> 
- Po použití aktualizace 1811 se můžete setkat s následujícími problémy při nasazení virtuálních počítačů pomocí Managed Disks:

   - Pokud bylo předplatné vytvořeno před aktualizací 1808, může při nasazení virtuálního počítače s Managed Disks dojít k chybě s interní chybovou zprávou. Pokud chcete chybu vyřešit, postupujte podle těchto kroků u každého předplatného:
      1. Na portálu tenanta klikněte na **předplatná** a vyhledejte předplatné. Vyberte **poskytovatelé prostředků**, vyberte **Microsoft. COMPUTE** a potom klikněte na **znovu registrovat**.
      2. V rámci stejného předplatného, přejít na **Access Control (IAM)** a ověřte, že je v seznamu uvedena role **AzureStack-DiskRP-Client** .
   - Pokud jste nakonfigurovali víceklientské prostředí, může se při nasazování virtuálních počítačů v rámci předplatného, které je přidružené k adresáři hosta, selhat chyba s interní chybovou zprávou. Pokud chcete chybu vyřešit, postupujte podle těchto kroků v [tomto článku](../azure-stack-enable-multitenancy.md#register-a-guest-directory) a překonfigurujte všechny adresáře hostů.

- Virtuální počítač Ubuntu 18,04 vytvořený s povoleným autorizací SSH vám neumožní používat klíče SSH k přihlášení. Alternativním řešením je použít pro rozšíření pro Linux přístup k virtuálnímu počítači k implementaci klíčů SSH po zřízení nebo použití ověřování na základě hesla.

### <a name="networking"></a>Sítě  

<!-- 1766332 - IS ASDK --> 
- Pokud v části **sítě** kliknete na **Vytvořit VPN Gateway** pro nastavení připojení k síti VPN, bude **zásada založená** jako typ sítě VPN. Tuto možnost nevybírejte. V Azure Stack je podporována pouze možnost **na základě trasy** .

<!-- 1902460 - IS ASDK --> 
- Azure Stack podporuje jednu *bránu místní sítě* na IP adresu. To platí pro všechna předplatná tenanta. Po vytvoření prvního připojení brány místní sítě se další pokusy o vytvoření prostředku brány místní sítě se stejnou IP adresou odmítnou.

<!-- 16309153 - IS ASDK --> 
- Ve virtuální síti, která byla vytvořena pomocí **automatického** nastavení serveru DNS, se změna na vlastní server DNS nezdařila. Aktualizovaná nastavení nejsou vložena do virtuálních počítačů v této virtuální síti.

<!-- 2529607 - IS ASDK --> 
- Během Azure Stack *rotace tajných klíčů* existuje období, ve kterém jsou veřejné IP adresy nedosažitelné po dobu dvou až pěti minut.

<!-- 2664148 - IS ASDK --> 
- Ve scénářích, kdy tenant přistupuje k virtuálním počítačům pomocí tunelového propojení VPN S2S, se může vyskytnout scénář, ve kterém se pokusy o připojení nezdaří, pokud byla místní podsíť přidána do brány místní sítě poté, co byla brána již vytvořena. 

- Když v Azure Stackovém portálu změníte statickou IP adresu pro konfiguraci protokolu IP, která je vázaná na síťový adaptér připojený k instanci virtuálního počítače, zobrazí se zpráva s upozorněním. 

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`. 

    Tuto zprávu můžete ignorovat bez obav. IP adresa se změní i v případě, že se instance virtuálního počítače nerestartuje.

- Na portálu se v okně **Vlastnosti sítě** nachází odkaz na **platná pravidla zabezpečení** pro každý síťový adaptér. Pokud vyberete tento odkaz, otevře se nové okno, které zobrazuje chybovou zprávu `Not Found.` k této chybě dochází, protože Azure Stack ještě nepodporují **platná pravidla zabezpečení**.

- Pokud v portálu přidáte příchozí pravidlo zabezpečení a jako zdroj vyberete možnost **označení služby** , v seznamu **zdrojových značek** se zobrazí několik možností, které nejsou k dispozici pro Azure Stack. Jediné možnosti, které jsou platné v Azure Stack, jsou následující:

  - **Internet**
  - **VirtualNetwork**
  - **AzureLoadBalancer**
  
    Ostatní možnosti nejsou podporovány jako zdrojové značky v Azure Stack. Podobně pokud přidáte odchozí pravidlo zabezpečení a jako cíl vyberete **značku služby** , zobrazí se stejný seznam možností pro **zdrojovou značku** . Jedinými platnými možnostmi jsou stejné možnosti jako u **zdrojové značky**, jak je popsáno v předchozím seznamu.

- Rutina **New-AzureRmIpSecPolicy** prostředí PowerShell nepodporuje nastavení **DHGroup24** pro `DHGroup` parametr.

- Skupiny zabezpečení sítě (skupin zabezpečení sítě) nefungují v Azure Stack stejným způsobem jako globální Azure. V Azure můžete pro jedno pravidlo NSG nastavit několik portů (pomocí portálu, PowerShellu a šablon Správce prostředků). V Azure Stack nemůžete na jednom pravidlu NSG nastavit víc portů prostřednictvím portálu. Pokud chcete tento problém obejít, použijte k nastavení těchto dalších pravidel šablonu Správce prostředků.

### <a name="infrastructure-backup"></a>Zálohování infrastruktury

<!--scheduler config lost, bug 3615401, new issue in 1811,  hectorl-->
<!-- TSG: https://www.csssupportwiki.com/index.php/Azure_Stack/KI/Backup_scheduler_configuration_lost --> 
- Po povolení automatického zálohování přejde služba plánovače neočekávaně do stavu zakázáno. Služba kontroleru zálohování zjistí, že automatické zálohování jsou zakázané, a na portálu pro správu se zobrazí upozornění. Toto upozornění je očekáváno, je-li automatické zálohování zakázáno. 
    - Příčina: k tomuto problému dochází kvůli chybě ve službě, která má za následek ztrátu konfigurace Scheduleru. Tato chyba nemění umístění úložiště, uživatelské jméno, heslo nebo šifrovací klíč.   
    - Náprava: Chcete-li tento problém zmírnit, otevřete okno nastavení kontroleru zálohování v poskytovateli prostředků Infrastructure Backup a vyberte možnost **Povolit automatické zálohování**. Ujistěte se, že jste nastavili požadovanou frekvenci a dobu uchování.
    - Výskyt: nízká 

<!-- ### SQL and MySQL-->

### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK --> 
- Před vytvořením první funkce Azure v předplatném je nutné zaregistrovat poskytovatele prostředků úložiště.


<!-- ### Usage -->

 
<!-- #### Identity -->
<!-- #### Marketplace -->

### <a name="syslog"></a>Syslog

- Konfigurace syslog není trvale prováděna prostřednictvím cyklu aktualizace, což způsobí ztrátu konfigurace klienta syslog a zastavení zpráv syslog. Tento problém se vztahuje na všechny verze Azure Stack od GA klienta syslog (1809). Pokud chcete tento problém obejít, překonfigurujte klienta syslog po použití aktualizace Azure Stack.

## <a name="download-the-update"></a>Stáhnout aktualizaci

Balíček aktualizací Azure Stack 1811 si můžete stáhnout [odsud.](https://aka.ms/azurestackupdatedownload) 

Pouze v případě propojených scénářů Azure Stack nasazení pravidelně kontroluje zabezpečený koncový bod a automaticky vás upozorní, pokud je k dispozici aktualizace pro Cloud. Další informace najdete v tématu [Správa aktualizací pro Azure Stack](../azure-stack-updates.md#how-to-know-an-update-is-available).

## <a name="next-steps"></a>Další kroky

- Informace o zásadách obsluhy pro Azure Stack integrovaných systémech a o tom, co je potřeba udělat, abyste zachovali svůj systém v podporovaném stavu, najdete v tématu [zásady pro obsluhu Azure Stack](../azure-stack-servicing-policy.md).  
- Postup pro monitorování a obnovení aktualizací pomocí privilegovaného koncového bodu (PEP) najdete v tématu [monitorování aktualizací v Azure Stack pomocí privilegovaného koncového bodu](../azure-stack-monitor-update.md).  
- Přehled správy aktualizací v Azure Stack najdete v tématu [Správa aktualizací v Azure Stack přehledu](../azure-stack-updates.md).  
- Další informace o tom, jak použít aktualizace s Azure Stack, najdete v tématu věnovaném [použití aktualizací v Azure Stack](../azure-stack-apply-updates.md).  