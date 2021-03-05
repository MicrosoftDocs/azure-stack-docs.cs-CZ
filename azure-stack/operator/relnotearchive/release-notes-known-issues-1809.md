---
title: Aktualizace 1809 Azure Stack | Microsoft Docs
description: Přečtěte si, co je nového v aktualizaci 1809 pro Azure Stack integrovaných systémů, včetně známých problémů a místa, kde si můžete aktualizaci stáhnout.
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
ms.date: 02/28/2019
ms.author: sethm
ms.reviewer: justini
ms.lastreviewed: 02/28/2019
ROBOTS: NOINDEX
ms.openlocfilehash: f98f0f28037cfc1de648f36cce20dd1c5334f93b
ms.sourcegitcommit: f194f9ca4297864500e62d8658674a0625b29d1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102186938"
---
# <a name="azure-stack-1809-update"></a>Aktualizace Azure Stack 1809

*Platí pro: Azure Stack integrovaných systémů*

Tento článek popisuje obsah balíčku aktualizace 1809. Balíček aktualizace obsahuje vylepšení, opravy a známé problémy pro tuto verzi Azure Stack. Tento článek obsahuje taky odkaz, abyste si mohli stáhnout aktualizaci. Známé problémy jsou rozdělené na problémy přímo související s procesem aktualizace a problémy se sestavením (po instalaci).

> [!IMPORTANT]  
> Tento balíček aktualizace je určen pouze pro Azure Stack integrovaných systémů. Nepoužívejte tento balíček aktualizace na Azure Stack Development Kit.

## <a name="build-reference"></a>Referenční informace o buildu

Číslo buildu aktualizace Azure Stack 1809 je **1.1809.0.90**.  

### <a name="new-features"></a>Nové funkce

Tato aktualizace obsahuje následující vylepšení pro Azure Stack:

- V této verzi Azure Stack integrované systémy podporují konfigurace uzlů 4-16. [Capacity Planner Azure Stack](https://aka.ms/azstackcapacityplanner) můžete použít k usnadnění plánování kapacity a konfigurace Azure Stack.

- <!--  2712869   | IS  ASDK -->  **Klient syslog Azure Stack (Obecná dostupnost)**  Tento klient umožňuje předávat audity, výstrahy a protokoly zabezpečení týkající se Azure Stack infrastruktury na serveru syslog nebo softwaru pro správu událostí a zabezpečení SIEM (Security Information and Event Management), a to externě Azure Stack. Klient syslog teď podporuje zadání portu, na kterém naslouchá Server syslog.

   V této verzi je klient syslog všeobecně dostupný a dá se použít v produkčním prostředí.

   Další informace najdete v tématu [Azure Stack předávání SYSLOG](../azure-stack-integrate-security.md).

- Nyní můžete [přesunout prostředek registrace](../azure-stack-registration.md#move-a-registration-resource) v Azure mezi skupinami prostředků, aniž byste ho museli znovu registrovat. Poskytovatelé Cloud Solution Provider (CSP) mohou také přesouvat registrační prostředky mezi předplatnými, pokud jsou nová i stará předplatná namapována na stejné ID partnera CSP. To nemá vliv na existující mapování tenanta zákazníka. 

- Přidání podpory pro přiřazení více IP adres na jedno síťové rozhraní.  Další podrobnosti najdete v tématu [přiřazení více IP adres k virtuálním počítačům pomocí prostředí PowerShell](/azure/virtual-network/virtual-network-multiple-ip-addresses-powershell).

### <a name="fixed-issues"></a>Opravené problémy

<!-- TBD - IS ASDK -->
- Na portálu je nyní přesný nebo nevyužitá kapacita při vytváření sestav paměťového grafu. Teď můžete spolehlivě odhadnout, kolik virtuálních počítačů je možné vytvořit.

<!-- TBD - IS ASDK --> 
- Opravili jsme problém, ve kterém jste vytvořili virtuální počítače na portálu Azure Stack User Portal a na portálu se zobrazil nesprávný počet datových disků, které se můžou připojit k virtuálnímu počítači DS Series. Virtuální počítače řady DS můžou přijmout tolik datových disků jako konfigurace Azure.

- Následující problémy se spravovanými disky jsou opraveny v 1809 a jsou opraveny také v 1808 [Azure Stack hotfix 1.1808.9.117](https://support.microsoft.com/help/4481066/): 

   <!--  2966665 IS, ASDK --> 
  - Opravili jsme problém s připojením datových disků SSD k virtuálním počítačům se spravovaným diskem (DS, DSv2, FS, Fs_V2). Chyba:  *nepovedlo se aktualizovat disky pro chybu VMName virtuálního počítače: požadovanou operaci nejde provést, protože typ účtu úložiště Premium_LRS není podporovaný pro velikost virtuálního počítače Standard_DS/Ds_V2/fs/Fs_v2)*. 
   
  - Vytvoření virtuálního počítače se spravovanými disky pomocí **createOption**: **připojit** se nepovedlo s následující chybou: *dlouhodobá operace se nezdařila se stavem "neúspěch". Další informace: došlo k vnitřní chybě spuštění.*
    ErrorCode: InternalExecutionError ErrorMessage: došlo k vnitřní chybě spuštění.
   
    Tento problém byl nyní opraven.

- <!-- 2702741 -  IS, ASDK --> Opravili jsme problém, kdy veřejné IP adresy, které byly nasazeny pomocí metody dynamického přidělení, nebyly zaručeny, aby byly zachovány po vydání Stop-Deallocate. Teď jsou zachované.

- <!-- 3078022 - IS, ASDK --> Pokud byl virtuální počítač zastavený a nepřidělený před 1808, po aktualizaci 1808 ho nebude možné znovu přidělit.  Tento problém je opravený v 1809. Instance, které byly v tomto stavu a nemohly být spuštěny, lze spustit v 1809 s touto opravou. Oprava také zabrání tomuto problému v opakovaném výskytu.

### <a name="changes"></a>Změny

<!-- 2635202 - IS, ASDK -->
- Služba zálohování infrastruktury přesouvá z [veřejné sítě infrastruktury](/azure/azure-stack/azure-stack-network) do [veřejné VIP sítě](/azure/azure-stack/azure-stack-network#public-vip-network). Zákazníci budou muset zajistit, aby služba měla přístup k umístění úložiště pro zálohování z veřejné VIP sítě.  

> [!IMPORTANT]  
> Pokud máte bránu firewall, která neumožňuje připojení z veřejné virtuální IP adresy k souborovému serveru, tato změna způsobí selhání zálohování infrastruktury s chybou, 53 síťová cesta nebyla nalezena. Jedná se o zásadní změnu, která nemá přijatelné řešení. Na základě zpětné vazby od zákazníků vrátí Microsoft tuto změnu v opravě hotfix. Další informace o dostupných opravách hotfix pro 1809 najdete v [části kroky po aktualizaci](#post-update-steps) . Jakmile je tato oprava hotfix dostupná, nezapomeňte ji po aktualizaci na 1809 použít jenom v případě, že zásady sítě neumožňují síti veřejných VIP přístup k prostředkům infrastruktury. v 1811 se tato změna použije pro všechny systémy. Pokud jste použili opravu hotfix v 1809, není vyžadována žádná další akce.  

### <a name="common-vulnerabilities-and-exposures"></a>Běžné chyby zabezpečení a ohrožení

Tato aktualizace nainstaluje tyto aktualizace zabezpečení:  

- [ADV180022](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180022)
- [CVE-2018-0965](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-0965)
- [CVE-2018-8271](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8271)
- [CVE-2018-8320](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8320)
- [CVE-2018-8330](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8330)
- [CVE-2018-8332](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8332)
- [CVE-2018-8333](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8333)
- [CVE-2018-8335](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8335)
- [CVE-2018-8392](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8392)
- [CVE-2018-8393](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8393)
- [CVE-2018-8410](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8410)
- [CVE-2018-8411](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8411)
- [CVE-2018-8413](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8413)
- [CVE-2018-8419](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8419)
- [CVE-2018-8420](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8420)
- [CVE-2018-8423](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8423)
- [CVE-2018-8424](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8424)
- [CVE-2018-8433](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8433)
- [CVE-2018-8434](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8434)
- [CVE-2018-8435](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8435)
- [CVE-2018-8438](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8438)
- [CVE-2018-8439](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8439)
- [CVE-2018-8440](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8440)
- [CVE-2018-8442](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8442)
- [CVE-2018-8443](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8443)
- [CVE-2018-8446](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8446)
- [CVE-2018-8449](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8449)
- [CVE-2018-8453](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8453)
- [CVE-2018-8455](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8455)
- [CVE-2018-8462](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8462)
- [CVE-2018-8468](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8468)
- [CVE-2018-8472](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8472)
- [CVE-2018-8475](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8475)
- [CVE-2018-8481](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8481)
- [CVE-2018-8482](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8482)
- [CVE-2018-8484](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8484)
- [CVE-2018-8486](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8486)
- [CVE-2018-8489](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8489)
- [CVE-2018-8490](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8490)
- [CVE-2018-8492](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8492)
- [CVE-2018-8493](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8493)
- [CVE-2018-8494](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8494)
- [CVE-2018-8495](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8495)
- [CVE-2018-8497](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8497)

Další informace o těchto chybách zabezpečení získáte kliknutím na výše uvedené odkazy nebo v článcích znalostní báze Microsoft Knowledge Base [4457131](https://support.microsoft.com/help/4457131) a [4462917](https://support.microsoft.com/help/4462917).

### <a name="prerequisites"></a>Požadavky

- Před použitím 1809 nainstalujte nejnovější Azure Stack opravu hotfix pro 1808. Další informace najdete v článku [KB 4481066 – Azure Stack hotfix Azure Stack hotfix 1.1808.9.117](https://support.microsoft.com/help/4481066/). Zatímco společnost Microsoft doporučuje k dispozici nejnovější opravu hotfix, je minimální verze nutná k instalaci 1809 1.1808.5.110.

  > [!TIP]  
  > Přihlaste se k odběru následujících kanálů *RR* nebo *Atom* a zachovejte se Azure Stackmi opravami hotfix:
  > - PTR https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss 
  > - Počtu https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom


- Před zahájením instalace této aktualizace spusťte rutinu [test-AzureStack](../azure-stack-diagnostic-test.md) s následujícími parametry, abyste ověřili stav svého Azure Stack a vyřešili jsme zjištěné provozní problémy, včetně všech upozornění a selhání. Zkontrolujte také aktivní výstrahy a vyřešte všechny akce, které vyžadují.  

  ```powershell
  Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
  ```

- Pokud je Azure Stack spravováno pomocí System Center Operations Manager (SCOM), nezapomeňte před použitím 1809 aktualizovat sadu Management Pack pro Microsoft Azure Stack na verzi 1.0.3.11.

### <a name="known-issues-with-the-update-process"></a>Známé problémy s procesem aktualizace

- Po spuštění rutiny [test-AzureStack](../azure-stack-diagnostic-test.md) po aktualizaci 1809 se zobrazí varovná zpráva z řadiče pro správu základní desky (BMC). Toto upozornění můžete klidně ignorovat.

- <!-- 2468613 - IS --> Během instalace této aktualizace se můžou zobrazit výstrahy s názvem *Chyba – šablona pro typ FaultType UserAccounts. New chybí.*  Tyto výstrahy můžete bezpečně ignorovat. Tyto výstrahy se po dokončení instalace této aktualizace automaticky zavřou.

- <!-- 2489559 - IS --> Nepokoušejte se během instalace této aktualizace vytvořit virtuální počítače. Další informace o správě aktualizací najdete v tématu [Správa aktualizací v Azure Stack Overview](../azure-stack-updates.md#plan-for-updates).

- <!-- 3139614 | IS --> Pokud jste použili aktualizaci Azure Stack od výrobce OEM, oznámení **aktualizace k dispozici** se nemusí zobrazit na portálu pro správu Azure Stack. Pokud chcete nainstalovat Microsoft Update, Stáhněte si ho a naimportujte ho ručně podle pokynů [v tématu použití aktualizací v Azure Stack](../azure-stack-apply-updates.md).

### <a name="post-update-steps"></a>Kroky po aktualizaci

> [!Important]  
> Získejte nasazení Azure Stack připravené pro hostitele rozšíření, který je povolený dalším balíčkem aktualizace. Připravte systém pomocí následujících pokynů, [Připravte hostitele rozšíření pro Azure Stack](../azure-stack-extension-host-prepare.md).

Po instalaci této aktualizace nainstalujte všechny příslušné opravy hotfix. Další informace najdete v následujících článcích znalostní báze a v našich [zásadách pro obsluhu](../azure-stack-servicing-policy.md).  
- [KB 4481548 – Azure Stack hotfix Azure Stack hotfix 1.1809.12.114](https://support.microsoft.com/help/4481548/)  

## <a name="known-issues-post-installation"></a>Známé problémy (po instalaci)

Níže jsou uvedené známé problémy po instalaci této verze buildu.

### <a name="portal"></a>Portál

- Technickou dokumentaci Azure Stack se zaměřuje na nejnovější verzi. Vzhledem k změnám portálu mezi verzemi, co vidíte při použití Azure Stack portály, se může lišit od toho, co vidíte v dokumentaci.

<!-- 2930718 - IS ASDK --> 
- Když v portálu pro správu přistupujete k podrobnostem každého předplatného uživatele, po zavření okna a kliknutí na **Poslední** se nezobrazí název předplatného uživatele.

<!-- 3060156 - IS ASDK --> 
- Na portále správce i uživatelské portály klikněte na nastavení portálu a vyberte **Odstranit všechna nastavení a soukromé řídicí panely** nefungují podle očekávání. Zobrazí se oznámení o chybě. 

<!-- 2930799 - IS ASDK --> 
- Na portále správce i uživatele v části **všechny služby** jsou **plány ochrany Asset DDoS** nesprávně uvedené. Není k dispozici v Azure Stack. Pokud se pokusíte ho vytvořit, zobrazí se chyba oznamující, že portál nemohl vytvořit položku Marketplace. 

<!-- 2930820 - IS ASDK --> 
- Při hledání "Docker" na portále správce i v uživatelských portálech se položka nesprávně vrátí. Není k dispozici v Azure Stack. Pokud se pokusíte ho vytvořit, zobrazí se okno s indikací chyby. 

<!-- 2967387 - IS, ASDK --> 
- Účet, který použijete pro přihlášení ke Správci Azure Stack nebo portálu User Portal, se zobrazí jako **neidentifikovaný uživatel**. Tato zpráva se zobrazí, pokud u účtu není zadáno *křestní* jméno nebo *příjmení* . Pokud chcete tento problém obejít, upravte uživatelský účet tak, aby poskytoval buď jméno nebo příjmení. Musíte se odhlásit a pak znovu přihlásit na portál.  

<!--  2873083 - IS ASDK --> 
-  Když použijete portál k vytvoření sady škálování virtuálních počítačů (VMSS), rozevírací seznam *velikost instance* se při použití aplikace Internet Explorer nenačte správně. Pokud chcete tento problém obejít, použijte jiný prohlížeč a vytvořte VMSS pomocí portálu.  

<!-- 2931230 - IS  ASDK --> 
- Plány, které se přidají k předplatnému uživatele jako doplněk, nejde odstranit, i když z předplatného uživatele odeberete plán. Plán zůstane, dokud nebudou odstraněny také odběry, které odkazují na plán doplňku. 

<!--2760466 - IS  ASDK --> 
- Při instalaci nového prostředí Azure Stack, ve kterém je spuštěna tato verze, se nemusí zobrazit výstraha, která indikuje, že se *vyžaduje aktivace* . Předtím, než budete moct použít syndikaci Marketplace, se vyžaduje [Aktivace](../azure-stack-registration.md) .  

<!-- TBD - IS ASDK --> 
- Nelze použít dva typy předplatného pro správu, které byly představeny s verzí 1804. Typy předplatného jsou odběry **měření** a **předplatné spotřeby**. Tyto typy předplatného jsou viditelné v nových Azure Stack prostředí od verze 1804, ale ještě nejsou připravené k použití. Měli byste dál používat výchozí typ předplatného **poskytovatele** .

<!-- TBD - IS ASDK --> 
- Výsledkem odstranění předplatných uživatelů jsou osamocené prostředky. Jako alternativní řešení nejprve odstraňte prostředky uživatelů nebo celou skupinu prostředků a pak odstraňte odběry uživatelů.

<!-- TBD - IS ASDK --> 
- Oprávnění k vašemu předplatnému nemůžete zobrazit pomocí Azure Stackch portálů. Alternativním řešením je ověřit oprávnění pomocí PowerShellu.


### <a name="health-and-monitoring"></a>Stav a monitorování

<!-- TBD - IS -->
- Může se stát, že se opakovaně zobrazí následující výstrahy a v Azure Stackovém systému zmizí:
   - *Instance role infrastruktury není dostupná.*
   - *Uzel jednotky škálování je offline*
   
  Spuštěním rutiny [test-AzureStack](../azure-stack-diagnostic-test.md) ověřte stav instancí rolí infrastruktury a uzly jednotek škálování. Pokud [test-AzureStack](../azure-stack-diagnostic-test.md)nezjistí žádné problémy, můžete tyto výstrahy ignorovat. Pokud se zjistí problém, můžete se pokusit spustit instanci role infrastruktury nebo uzel pomocí portálu pro správu nebo PowerShellu.

  Tento problém je opravený v nejnovější [verzi 1809 oprav hotfix](https://support.microsoft.com/help/4481548/), proto nezapomeňte nainstalovat tuto opravu hotfix, pokud k problému dochází. 

<!-- 1264761 - IS ASDK -->  
- Můžou se zobrazit výstrahy pro součást **kontroleru stavu** s následujícími podrobnostmi:  

   #1 výstrahy:
   - Název: role infrastruktury není v pořádku.
   - ZÁVAŽNOST: upozornění
   - SOUČÁST: kontroler stavu
   - Popis: skener prezenčního signálu kontroleru stavu není k dispozici. To může mít vliv na sestavy stavu a metriky.  

  #2 výstrahy:
   - Název: role infrastruktury není v pořádku.
   - ZÁVAŽNOST: upozornění
   - SOUČÁST: kontroler stavu
   - Popis: není k dispozici skener chyb řadiče stavu. To může mít vliv na sestavy stavu a metriky.

  Obě výstrahy se můžou ignorovat a v průběhu času se automaticky zavřou.  


<!-- 2812138 | IS --> 
- Může se zobrazit upozornění na součást **úložiště** s následujícími podrobnostmi:

   - Název: Chyba interní komunikace služby úložiště  
   - ZÁVAŽNOST: kritická  
   - SOUČÁST: úložiště  
   - Popis: při odesílání požadavků do následujících uzlů došlo k chybě vnitřní komunikace služby Storage.  

    Výstrahu je možné ignorovat, ale tuto výstrahu musíte zavřít ručně.

<!-- 2368581 - IS, ASDK --> 
- Operátor Azure Stack, pokud obdržíte výstrahu o nedostatku paměti a virtuální počítače klienta se nepodaří nasadit s **chybou vytvoření virtuálního počítače infrastruktury**, je možné, že Azure Stack razítko není dostupné paměti. [Capacity Planner Azure Stack](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) použijte k tomu, abyste nejlépe pochopili kapacitu dostupnou pro vaše úlohy.

### <a name="compute"></a>Výpočetní prostředky

- Při vytváření [virtuálního počítače s Dv2 Series](../../user/azure-stack-vm-considerations.md#vm-sizes)se D11 virtuální počítače 14v2 vám umožní vytvořit datové disky 4, 8, 16 a 32. V podokně vytvořit virtuální počítač se ale zobrazují datové disky 8, 16, 32 a 64.

<!-- 3235634 - IS, ASDK -->
- Nasazení virtuálních počítačů se velikostmi, které obsahují příponu **v2** ; například **Standard_A2_v2** zadejte příponu jako **Standard_A2_v2** (malá písmena v). Nepoužívejte **Standard_A2_V2** (velká písmena V). Tato funkce funguje v globálním Azure a jedná se o nekonzistenci na Azure Stack.

<!-- 3099544 - IS, ASDK --> 
- Když vytváříte nový virtuální počítač pomocí portálu Azure Stack a vyberete velikost virtuálního počítače, zobrazí se sloupec USD/měsíc s **nedostupnou** zprávou. Tento sloupec by se neměl zobrazovat; zobrazení sloupce s cenami virtuálních počítačů není v Azure Stack podporováno.

<!-- 2869209 - IS, ASDK --> 
- Při použití [rutiny **Add-AzsPlatformImage**](/powershell/module/azs.compute.admin/add-azsplatformimage?preserve-view=true&view=azurestackps-1.4.0)musíte použít parametr **-OsUri** jako identifikátor URI účtu úložiště, kam se disk nahrává. Pokud použijete místní cestu k disku, rutina se nezdařila s následující chybou: *dlouhodobá operace se nezdařila se stavem selhalo*. 

<!--  2795678 - IS, ASDK --> 
- Když použijete portál k vytvoření virtuálních počítačů ve velikosti virtuálního počítače Premium (DS, Ds_v2, FS, FSv2), virtuální počítač se vytvoří v účtu úložiště úrovně Standard. Vytvoření v účtu úložiště úrovně Standard nemá vliv na funkce, IOPs ani fakturaci. 

   Upozornění, které říkáte, můžete bezpečně ignorovat: *rozhodli jste se použít standardní disk pro velikost, která podporuje prémiové disky. To může mít vliv na výkon operačního systému a nedoporučuje se. Zvažte místo toho použití Storage úrovně Premium (SSD).*

<!-- 2967447 - IS, ASDK --> 
- Prostředí pro vytváření VMSS (Virtual Machine Scale set) poskytuje možnost nasazení na bázi CentOS 7,2. Vzhledem k tomu, že tato image není v Azure Stack k dispozici, vyberte pro nasazení jiný operační systém nebo použijte šablonu Azure Resource Manager určující jinou image CentOS, která byla stažena před nasazením z webu Marketplace pomocí operátoru.  

<!-- 2724873 - IS --> 
- Při použití rutin PowerShellu **Start-AzsScaleUnitNode** nebo  **stop-AzsScaleUnitNode** pro správu jednotek škálování se může stát, že první pokus o spuštění nebo zastavení jednotky škálování selže. Pokud rutina při prvním spuštění neprojde, spusťte rutinu podruhé. Druhý běh by měl úspěšně dokončit operaci. 

<!-- TBD - IS ASDK --> 
- Pokud zřízení rozšíření na nasazení virtuálního počítače trvá příliš dlouho, uživatelé by měli povolit časový limit zřizování namísto pokusu o zastavení procesu navrácení nebo odstranění virtuálního počítače.  

<!-- 1662991 IS ASDK --> 
- Diagnostika virtuálního počítače se systémem Linux není v Azure Stack podporována. Když nasadíte virtuální počítač se systémem Linux s povoleným diagnostikou virtuálních počítačů, nasazení se nezdařilo. Nasazení se také nepovede, pokud povolíte základní metriky virtuálního počítače se systémem Linux prostřednictvím nastavení diagnostiky.  

<!-- 2724961- IS ASDK --> 
- Když zaregistrujete poskytovatele prostředků **Microsoft. Insight** v nastaveních předplatného a vytvoříte virtuální počítač s Windows s povoleným diagnostikou HOSTOVANÉHO operačního systému, graf procenta využití procesoru na stránce Přehled virtuálních počítačů nezobrazí data metrik.

   Pokud chcete najít data metrik, jako je například graf procenta využití procesoru pro virtuální počítač, otevřete okno metriky a zobrazte všechny podporované metriky hostů virtuálních počítačů s Windows.

<!-- 3507629 - IS, ASDK --> 
- Managed Disks vytvoří dva nové [typy výpočetních kvót](../azure-stack-quota-types.md#compute-quota-types) a omezí tak maximální kapacitu spravovaných disků, které se dají zřídit. Ve výchozím nastavení je 2048 GiB přiděleno pro každý typ kvóty spravované disky. Mohou však nastat následující problémy:

   - U kvót vytvořených před aktualizací 1808 zobrazí kvóta Managed Disks 0 hodnot na portálu pro správu, i když je přidělený 2048 GiB. Hodnotu můžete zvýšit nebo snížit podle svých skutečných potřeb a Tato nově nastavená hodnota kvóty přepíše GiB výchozí hodnotu 2048.
   - Pokud hodnotu kvóty aktualizujete na 0, bude ekvivalentní výchozí hodnotě 2048 GiB. Jako alternativní řešení nastavte hodnotu kvóty na 1.

<!-- TBD - IS ASDK --> 
- Po použití aktualizace 1809 se můžete setkat s následujícími problémy při nasazení virtuálních počítačů pomocí Managed Disks:

  - Pokud bylo předplatné vytvořeno před aktualizací 1808, může při nasazení virtuálního počítače s Managed Disks dojít k chybě s interní chybovou zprávou. Pokud chcete chybu vyřešit, postupujte podle těchto kroků u každého předplatného:
     1. Na portálu tenanta klikněte na **předplatná** a vyhledejte předplatné. Klikněte na **poskytovatelé prostředků**, potom klikněte na **Microsoft. COMPUTE** a pak klikněte na **znovu registrovat**.
     2. V rámci stejného předplatného, přejít na **Access Control (IAM)** a ověřte, že je v seznamu uvedena role **AzureStack-DiskRP-Client** .
  - Pokud jste nakonfigurovali víceklientské prostředí, může se při nasazování virtuálních počítačů v rámci předplatného, které je přidružené k adresáři hosta, selhat chyba s interní chybovou zprávou. Pokud chcete chybu vyřešit, postupujte podle těchto kroků v [tomto článku](../azure-stack-enable-multitenancy.md#register-a-guest-directory) a překonfigurujte všechny adresáře hostů.

- Virtuální počítač Ubuntu 18,04 vytvořený s povoleným autorizací SSH vám neumožní používat klíče SSH k přihlášení. Alternativním řešením je, že k implementaci klíčů SSH po zřízení použijte prosím přístup k virtuálnímu počítači a použijete ověřování založené na heslech.

### <a name="networking"></a>Sítě  

<!-- 1766332 - IS ASDK --> 
- Pokud v části **sítě** kliknete na **Vytvořit VPN Gateway** pro nastavení připojení k síti VPN, bude **zásada založená** jako typ sítě VPN. Tuto možnost nevybírejte. V Azure Stack je podporována pouze možnost **na základě trasy** .

<!-- 1902460 - IS ASDK --> 
- Azure Stack podporuje jednu *bránu místní sítě* na IP adresu. To platí pro všechna předplatná tenanta. Po vytvoření prvního připojení brány místní sítě se budou zablokované následné pokusy o vytvoření prostředku brány místní sítě se stejnou IP adresou.

<!-- 16309153 - IS ASDK --> 
- Na Virtual Network, která byla vytvořena pomocí *automatického* nastavení serveru DNS, se změna na vlastní server DNS nezdařila. Aktualizovaná nastavení nejsou vložena do virtuálních počítačů v této virtuální síti.

<!-- 2529607 - IS ASDK --> 
- Během Azure Stack *rotace tajných klíčů* existuje období, ve kterém jsou veřejné IP adresy nedosažitelná po dobu dvou až pěti minut.

<!-- 2664148 - IS ASDK --> 
-   Ve scénářích, kdy klient přistupuje k virtuálním počítačům pomocí tunelového připojení VPN S2S, se může vyskytnout scénář, kdy se pokusy o připojení nezdaří, pokud byla místní podsíť přidána do brány místní sítě po vytvoření brány. 


<!-- ### SQL and MySQL-->


### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK --> 
- Před vytvořením první funkce Azure v předplatném musí uživatelé zaregistrovat poskytovatele prostředků úložiště.


### <a name="usage"></a>Využití  

<!-- TBD - IS ASDK --> 
- Data měřiče využití veřejné IP adresy zobrazují stejnou hodnotu *EventDateTime* pro každý záznam místo razítka *timedate* , které ukazuje, kdy byl záznam vytvořen. V současné době nemůžete tato data použít k provedení přesného účtování využití veřejné IP adresy.


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>Stáhnout aktualizaci

Balíček aktualizací Azure Stack 1809 si můžete stáhnout [odsud.](https://aka.ms/azurestackupdatedownload)
  

## <a name="next-steps"></a>Další kroky

- Informace o zásadách obsluhy pro Azure Stack integrovaných systémech a o tom, co je potřeba udělat, abyste zachovali svůj systém v podporovaném stavu, najdete v tématu [zásady pro obsluhu Azure Stack](../azure-stack-servicing-policy.md).  
- Postup pro monitorování a obnovení aktualizací pomocí privilegovaného koncového bodu (PEP) najdete v tématu [monitorování aktualizací v Azure Stack pomocí privilegovaného koncového bodu](../azure-stack-monitor-update.md).  
- Přehled správy aktualizací v Azure Stack najdete v tématu [Správa aktualizací v Azure Stack přehledu](../azure-stack-updates.md).  
- Další informace o tom, jak použít aktualizace s Azure Stack, najdete v tématu věnovaném [použití aktualizací v Azure Stack](../azure-stack-apply-updates.md).