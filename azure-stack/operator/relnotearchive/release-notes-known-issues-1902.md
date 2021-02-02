---
title: Aktualizace 1902 Azure Stack | Microsoft Docs
description: Seznamte se s aktualizací 1902 pro Azure Stack integrovaných systémů, včetně nových, známých problémů a místa, kde si tuto aktualizaci stáhnout.
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
ms.date: 05/30/2019
ms.author: sethm
ms.reviewer: adepue
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: 6bf973d7e232ad30738abf80396da75e62277529
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248172"
---
# <a name="azure-stack-1902-update"></a>Aktualizace Azure Stack 1902

*Platí pro: Azure Stack integrovaných systémů*

Tento článek popisuje obsah balíčku aktualizace 1902. Tato aktualizace zahrnuje vylepšení, opravy a nové funkce pro tuto verzi Azure Stack. Tento článek také popisuje známé problémy v této verzi a obsahuje odkaz na stažení aktualizace. Známé problémy jsou rozděleny do potíží přímo souvisejících s procesem aktualizace a problémy se sestavením (po instalaci).

> [!IMPORTANT]  
> Tento balíček aktualizace je určen pouze pro Azure Stack integrovaných systémů. Nepoužívejte tento balíček aktualizace na Azure Stack Development Kit.

## <a name="build-reference"></a>Referenční informace o buildu

Číslo buildu aktualizace Azure Stack 1902 je **1.1902.0.69**.

### <a name="update-type"></a>Typ aktualizace

Typ sestavení aktualizace Azure Stack 1902 je **plný**. Další informace o typech sestavení aktualizace naleznete v článku [Správa aktualizací v Azure Stack](../azure-stack-updates.md) .

## <a name="hotfixes"></a>Opravy hotfix

Azure Stack pravidelně vydává opravy hotfix. Před aktualizací Azure Stack na 1902 nezapomeňte nainstalovat [nejnovější Azure Stack opravu hotfix](#azure-stack-hotfixes) pro 1901.

Azure Stack oprav hotfix se týkají pouze Azure Stack integrovaných systémů. Nepokoušejte se instalovat opravy hotfix na ASDK.

> [!TIP]  
> Přihlaste se k odběru následujících informačních kanálů *RSS* nebo *Atom* , abyste zachovali Azure Stack opravy hotfix:
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Azure Stack opravy hotfix

- **1901**: [KB 4500636 – Azure Stack hotfix 1.1901.5.109](https://support.microsoft.com/help/4500636)
- **1902**: [KB 4500637 – Azure Stack hotfix 1.1902.3.75](https://support.microsoft.com/help/4500637)

## <a name="prerequisites"></a>Požadavky

> [!IMPORTANT]
> 1902 můžete nainstalovat přímo z verze **1.1901.0.95** nebo **1.1901.0.99** , aniž byste nejdřív nainstalovali žádnou opravu hotfix 1901. Pokud jste však nainstalovali starší opravu hotfix **1901.2.103** , je nutné před pokračováním v 1902 nainstalovat novější [opravu hotfix 1901.3.105](https://support.microsoft.com/help/4495662) .

- Před zahájením instalace této aktualizace spusťte rutinu [test-AzureStack](../azure-stack-diagnostic-test.md) s následujícími parametry, abyste ověřili stav svého Azure Stack a vyřešili jsme zjištěné provozní problémy, včetně všech upozornění a selhání. Zkontrolujte také aktivní výstrahy a vyřešte všechny akce, které vyžadují:

    ```powershell
    Test-AzureStack -Include AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

  Pokud je `AzsControlPlane` parametr zahrnut při spuštění rutiny **test-AzureStack** , zobrazí se následující selhání ve výstupu **test-AzureStack** : **selhání Azure Stack se souhrnem řídicích webů roviny ovládacího prvku**. Tuto specifickou chybu lze bezpečně ignorovat.

- Pokud Azure Stack spravuje System Center Operations Manager (SCOM), nezapomeňte aktualizovat [sadu Management Pack pro Microsoft Azure Stack](https://www.microsoft.com/download/details.aspx?id=55184) na verzi 1.0.3.11 před instalací 1902.

- Formát balíčku pro aktualizaci Azure Stack se změnil z **. bin/. exe/. XML** na **. zip/. xml** počínaje verzí 1902. Zákazníci s připojenými jednotkami škálování Azure Stack uvidí na portálu zprávu **aktualizace k dispozici** . Zákazníci, kteří nejsou připojeni, teď můžou jednoduše stáhnout a naimportovat soubor. zip s odpovídající příponou. XML.

<!-- ## New features -->

<!-- ## Fixed issues -->

## <a name="improvements"></a>Vylepšen

- Sestavení 1902 zavádí nové uživatelské rozhraní na portálu Azure Stack správce pro vytváření plánů, nabídek, kvót a plánů doplňku. Další informace, včetně snímků obrazovky, najdete v tématu [vytvoření plánů, nabídek a kvót](../azure-stack-create-plan.md).

<!-- 1460884    Hotfix: Adding StorageController service permission to talk to ClusterOrchestrator  Add node -->
- Vylepšení spolehlivosti kapacity během operace přidání uzlu při přepnutí stavu jednotky škálování z "rozšíření úložiště" na "spuštěné".

<!--
1426197 3852583: Increase Global VM script mutex wait time to accommodate enclosed operation timeout    PNU
1399240 3322580: [PNU] Optimize the DSC resource execution on the Host  PNU
1398846 Bug 3751038: ECEClient.psm1 should provide cmdlet to resume action plan instance    PNU
1398818 3685138, 3734779: ECE exception logging, VirtualMachine ConfigurePending should take node name from execution context   PNU
1381018 [1902] 3610787 - Infra VM creation should fail if the ClusterGroup already exists   PNU
-->
- Microsoft změnil formát balíčku aktualizace ze souborů. exe a. bin na soubor. zip, aby bylo možné vylepšit integritu a zabezpečení balíčku a také jednodušší správu. Nový formát přidává další spolehlivost procesu dekomprimace, který může v některých případech způsobit přípravu aktualizace na kabinu. Stejný formát balíčku platí také pro balíčky aktualizací od výrobce OEM.
- Aby bylo možné při spuštění příkazu test-AzureStack vylepšit možnosti operátorů Azure Stack, teď můžou operátory použít "test-AzureStack-Group UpdateReadiness", a to na rozdíl od předání deseti dalších parametrů po příkazu include.

  ```powershell
    Test-AzureStack -Group UpdateReadiness  
  ```  
  
- Za účelem zlepšení celkové spolehlivosti a dostupnosti základních služeb infrastruktury během procesu aktualizace bude poskytovatel prostředků nativní aktualizace v rámci plánu aktualizačních akcí zjišťovat a vyvolávat automatické globální opravy podle potřeby. Pracovní postupy globální nápravy opravy zahrnují:

  - Kontroluje se, jestli virtuální počítače infrastruktury nejsou v optimálním stavu, a pokusí se je opravit podle potřeby.
  - V rámci plánu řízení vyhledejte problémy se službou SQL a pokuste se je podle potřeby opravit.
  - Ověřte stav služby software Load Balancer (SLB) v rámci síťového adaptéru (NC) a pokuste se je opravit podle potřeby.
  - Ověřte stav služby síťového adaptéru (NC) a pokuste se ji podle potřeby opravit.
  - Ověřte stav uzlů služby Service Fabric služby ERCS (Emergency Recovery Console) a opravte je podle potřeby.
  - Ověřte stav role infrastruktury a podle potřeby ji opravte.
  - Ověřte stav uzlů Service Fabric služby Azure konzistentní úložiště (ACS) a opravte je podle potřeby.

<!-- 
1426690 [SOLNET] 3895478-Get-AzureStackLog_Output got terminated in the middle of network log   Diagnostics
1396607 3796092: Move Blob services log from Storage role to ACSBlob role to reduce the log size of Storage Diagnostics
1404529 3835749: Enable Group Policy Diagnostic Logs    Diagnostics
1436561 Bug 3949187: [Bug Fix] Remove AzsStorageSvcsSummary test from SecretRotationReadiness Test-AzureStack flag  Diagnostics
1404512 3849946: Get-AzureStackLog should collect all child folders from c:\Windows\Debug   Diagnostics 
-->
- Vylepšení diagnostických nástrojů Azure Stack pro zvýšení spolehlivosti a výkonu shromažďování protokolů. Další protokolování pro sítě a služby identit. 

<!-- 1384958    Adding a Test-AzureStack group for Secret Rotation  Diagnostics -->
- Vylepšení spolehlivosti Test-AzureStack testu připravenosti na střídání tajných klíčů.

<!-- 1404751    3617292: Graph: Remove dependency on ADWS.  Identity -->
- Vylepšení zvýšení spolehlivosti grafu při komunikaci s prostředím služby Active Directory zákazníka

<!-- 1391444    [ISE] Telemetry for Hardware Inventory - Fill gap for hardware inventory info   System info -->
- Vylepšení shromažďování inventáře hardwaru v Get-AzureStackStampInformation.

- Pro zlepšení spolehlivosti provozu v infrastruktuře ERCS se paměť pro každou instanci ERCS zvyšuje z 8 GB na 12 GB. V případě instalace Azure Stack integrovaných systémů je výsledkem zvýšení celkového počtu 12 GB.

<!-- 110303935 IcM Reported by HKEX -->
- 1902 opravuje problém ve službě síťové řadiče virtuálních počítačů, ve kterém jsou všechny virtuální počítače v určitém uzlu přepnuty do režimu offline.  Příčinou tohoto problému je zablokování v primárním stavu ztráty, kde primární nelze kontaktovat, ale role nebyla převzata do jiné instance v pořádku, která se dá vyřešit pouze kontaktováním služeb podpory Microsoftu.

> [!IMPORTANT]
> Aby se zajistilo, že proces opravy a aktualizace vede k minimálnímu množství výpadků tenanta, ujistěte se, že Azure Stack razítko v okně **kapacita** obsahuje více než 12 GB volného místa. Po úspěšné instalaci aktualizace si můžete prohlédnout toto zvýšení paměti v okně **kapacity** .

## <a name="common-vulnerabilities-and-exposures"></a>Běžné chyby zabezpečení a ohrožení

Tato aktualizace nainstaluje tyto aktualizace zabezpečení:  
- [ADV190005](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190006)
- [CVE-2019-0595](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0595)
- [CVE-2019-0596](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0596)
- [CVE-2019-0597](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0597)
- [CVE-2019-0598](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0598)
- [CVE-2019-0599](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0599)
- [CVE-2019-0600](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0600)
- [CVE-2019-0601](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0601)
- [CVE-2019-0602](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0602)
- [CVE-2019-0615](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0615)
- [CVE-2019-0616](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0616)
- [CVE-2019-0618](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0618)
- [CVE-2019-0619](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0619)
- [CVE-2019-0621](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0621)
- [CVE-2019-0623](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0623)
- [CVE-2019-0625](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0625)
- [CVE-2019-0626](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0626)
- [CVE-2019-0627](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0627)
- [CVE-2019-0628](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0628)
- [CVE-2019-0630](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0630)
- [CVE-2019-0631](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0631)
- [CVE-2019-0632](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0632)
- [CVE-2019-0633](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0633)
- [CVE-2019-0635](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0635)
- [CVE-2019-0636](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0636)
- [CVE-2019-0656](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0656)
- [CVE-2019-0659](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0659)
- [CVE-2019-0660](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0660)
- [CVE-2019-0662](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0662)
- [CVE-2019-0663](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0663)

Další informace o těchto chybách zabezpečení získáte kliknutím na výše uvedené odkazy nebo v článku znalostní báze Microsoft Knowledge Base [4487006](https://support.microsoft.com/en-us/help/4487006).

## <a name="known-issues-with-the-update-process"></a>Známé problémy s procesem aktualizace

- Při pokusu o instalaci aktualizace Azure Stack se může stát, že se stav aktualizace nezdaří a změní se stav na **PreparationFailed**. To je způsobeno tím, že poskytovatel prostředků aktualizace (URP) nemůže správně přenést soubory z kontejneru úložiště do interní sdílené složky infrastruktury pro zpracování. Počínaje verzí 1901 (1.1901.0.95) můžete tento problém obejít tak, že znovu kliknete na **aktualizovat** ( **nepokračovat**). URP pak vyčistí soubory z předchozího pokusu a znovu spustí stahování.

- Při spuštění rutiny [test-AzureStack](../azure-stack-diagnostic-test.md)se zobrazí zpráva s upozorněním z řadiče pro správu základní desky (BMC). Toto upozornění můžete klidně ignorovat.

- <!-- 2468613 - IS --> Během instalace této aktualizace se můžou zobrazit výstrahy s názvem, `Error - Template for FaultType UserAccounts.New is missing.`  které tyto výstrahy můžou bezpečně ignorovat. Výstrahy se po dokončení instalace této aktualizace automaticky zavřou.

## <a name="post-update-steps"></a>Kroky po aktualizaci

- Po instalaci této aktualizace nainstalujte všechny příslušné opravy hotfix. Další informace najdete v tématu [opravy hotfix](#hotfixes)a [zásady obsluhy](../azure-stack-servicing-policy.md).  

- Načtěte data z neaktivních šifrovacích klíčů a bezpečně je uložte mimo vaše nasazení Azure Stack. Postupujte podle [pokynů pro načtení klíčů](../azure-stack-security-bitlocker.md).

## <a name="known-issues-post-installation"></a>Známé problémy (po instalaci)

Níže jsou uvedené známé problémy po instalaci této verze buildu.

### <a name="portal"></a>Portál

<!-- 2930820 - IS ASDK --> 
- Při hledání "Docker" na portále správce i v uživatelských portálech se položka nesprávně vrátí. Není k dispozici v Azure Stack. Pokud se pokusíte ho vytvořit, zobrazí se okno s indikací chyby. 

<!-- 2931230 - IS  ASDK --> 
- Plány, které se přidají k předplatnému uživatele jako doplněk, nejde odstranit, i když z předplatného uživatele odeberete plán. Plán zůstane, dokud nebudou odstraněny také odběry, které odkazují na plán doplňku. 

<!-- TBD - IS ASDK --> 
- Nelze použít dva typy předplatného pro správu, které byly představeny s verzí 1804. Typy předplatného jsou odběry **měření** a **předplatné spotřeby**. Tyto typy předplatného jsou viditelné v nových Azure Stack prostředí od verze 1804, ale ještě nejsou připravené k použití. Měli byste dál používat výchozí typ předplatného **poskytovatele** .

<!-- 3557860 - IS ASDK --> 
- Výsledkem odstranění předplatných uživatelů jsou osamocené prostředky. Jako alternativní řešení nejprve odstraňte prostředky uživatelů nebo celou skupinu prostředků a pak odstraňte odběry uživatelů.

<!-- 1663805 - IS ASDK --> 
- Oprávnění k vašemu předplatnému nemůžete zobrazit pomocí Azure Stackch portálů. Alternativním řešením je [ověřit oprávnění pomocí PowerShellu](/powershell/module/azs.subscriptions.admin/get-azssubscriptionplan).

<!-- Daniel 3/28 -->
- Když v uživatelském portálu přejdete na objekt BLOB v rámci účtu úložiště a pokusíte se otevřít **zásady přístupu** z navigačního stromu, následné okno se nepovede načíst. Pokud chcete tento problém obejít, můžete následující rutiny PowerShellu povolit vytváření, načítání, nastavování a odstraňování zásad přístupu v uvedeném pořadí:

  - [New-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/new-azurestoragecontainerstoredaccesspolicy)
  - [Get-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/get-azurestoragecontainerstoredaccesspolicy)
  - [Set-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/set-azurestoragecontainerstoredaccesspolicy)
  - [Remove-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/remove-azurestoragecontainerstoredaccesspolicy)

<!-- ### Health and monitoring -->

### <a name="compute"></a>Výpočetní prostředky

- Při vytváření nového virtuálního počítače s Windows se může zobrazit následující chyba:

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   K této chybě dojde, pokud povolíte diagnostiku spouštění na virtuálním počítači, ale odstraníte účet úložiště diagnostiky spouštění. Pokud chcete tento problém obejít, vytvořte znovu účet úložiště se stejným názvem, jako jste použili dříve.

<!-- 2967447 - IS, ASDK, to be fixed in 1902 -->
- Prostředí pro vytvoření sady škálování virtuálních počítačů poskytuje CentOS 7,2 jako možnost nasazení. Vzhledem k tomu, že tato image není k dispozici na Azure Stack, vyberte jiný operační systém pro vaše nasazení nebo použijte šablonu Azure Resource Manager určující jinou image CentOS, která byla stažena před nasazením z webu Marketplace pomocí operátoru.  

<!-- TBD - IS ASDK --> 
- Po použití aktualizace 1902 se můžete setkat s následujícími problémy při nasazení virtuálních počítačů pomocí Managed Disks:

   - Pokud bylo předplatné vytvořeno před aktualizací 1808, může při nasazení virtuálního počítače s Managed Disks dojít k chybě s interní chybovou zprávou. Pokud chcete chybu vyřešit, postupujte podle těchto kroků u každého předplatného:
      1. Na portálu tenanta klikněte na **předplatná** a vyhledejte předplatné. Vyberte **poskytovatelé prostředků**, vyberte **Microsoft. COMPUTE** a potom klikněte na **znovu registrovat**.
      2. V rámci stejného předplatného, přejít na **Access Control (IAM)** a ověřte, že je v seznamu uvedeno **Azure Stack spravovaný disk** .
   - Pokud jste nakonfigurovali víceklientské prostředí, může se při nasazování virtuálních počítačů v rámci předplatného, které je přidružené k adresáři hosta, selhat chyba s interní chybovou zprávou. Pokud chcete chybu vyřešit, postupujte podle těchto kroků v [tomto článku](../azure-stack-enable-multitenancy.md#register-azure-stack-hub-with-the-guest-directory) a překonfigurujte všechny adresáře hostů.

- Virtuální počítač Ubuntu 18,04 vytvořený s povoleným autorizací SSH vám neumožní používat klíče SSH k přihlášení. Alternativním řešením je použít pro rozšíření pro Linux přístup k virtuálnímu počítači k implementaci klíčů SSH po zřízení nebo použití ověřování na základě hesla.

- V okně **Virtual Machine Scale Sets** nemůžete odebrat sadu škálování. Jako alternativní řešení vyberte sadu škálování, kterou chcete odebrat, a pak klikněte na tlačítko **Odstranit** v podokně **Přehled** .

- Vytváření virtuálních počítačů ve skupině dostupnosti 3 domén selhání a vytvoření instance sady škálování virtuálního počítače selže s chybou **FabricVmPlacementErrorUnsupportedFaultDomainSize** během procesu aktualizace v prostředí Azure Stack se čtyřmi uzly. Můžete vytvořit jeden virtuální počítač ve skupině dostupnosti se dvěma doménami selhání úspěšně. Vytvoření instance sady škálování však není během procesu aktualizace na 4 uzlech Azure Stack stále k dispozici.

### <a name="networking"></a>Sítě  

<!-- 3239127 - IS, ASDK -->
- Když v Azure Stackovém portálu změníte statickou IP adresu pro konfiguraci protokolu IP, která je vázaná na síťový adaptér připojený k instanci virtuálního počítače, zobrazí se zpráva s upozorněním. 

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`.

    Tuto zprávu můžete ignorovat bez obav. IP adresa se změní i v případě, že se instance virtuálního počítače nerestartuje.

<!-- 3632798 - IS, ASDK -->
- Pokud v portálu přidáte příchozí pravidlo zabezpečení a jako zdroj vyberete možnost **označení služby** , v seznamu **zdrojových značek** se zobrazí několik možností, které nejsou k dispozici pro Azure Stack. Jediné možnosti, které jsou platné v Azure Stack, jsou následující:

  - **Internet**
  - **VirtualNetwork**
  - **AzureLoadBalancer**
  
  Ostatní možnosti nejsou podporovány jako zdrojové značky v Azure Stack. Podobně pokud přidáte odchozí pravidlo zabezpečení a jako cíl vyberete **značku služby** , zobrazí se stejný seznam možností pro **zdrojovou značku** . Jedinými platnými možnostmi jsou stejné možnosti jako u **zdrojové značky**, jak je popsáno v předchozím seznamu.

- Skupiny zabezpečení sítě (skupin zabezpečení sítě) nefungují v Azure Stack stejným způsobem jako globální Azure. V Azure můžete pro jedno pravidlo NSG nastavit několik portů (pomocí portálu, PowerShellu a šablon Správce prostředků). V Azure Stack ale nemůžete na jednom pravidlu NSG nastavit víc portů prostřednictvím portálu. Pokud chcete tento problém obejít, použijte k nastavení těchto dalších pravidel šablonu Správce prostředků nebo PowerShell.

<!-- 3203799 - IS, ASDK -->
- Azure Stack nepodporuje současné připojení více než 4 síťových rozhraní k instanci virtuálního počítače bez ohledu na velikost instance.

- Pokud se v uživatelském portálu pokusíte přidat **back-end fond** do **Load Balancer**, operace se nezdaří s chybovou zprávou **se nepodařilo aktualizovat Load Balancer....**  Pokud chcete tento problém obejít, pomocí PowerShellu, CLI nebo šablony Azure Resource Manager přidružte fond back-end k prostředku nástroje pro vyrovnávání zatížení.

- Pokud se v uživatelském portálu pokusíte vytvořit **pravidlo příchozího překladu adres (NAT)** pro **Load Balancer**, operace se nezdaří s chybovou zprávou **se nepodařilo aktualizovat Load Balancer....**  Pokud chcete tento problém obejít, pomocí PowerShellu, CLI nebo šablony Azure Resource Manager přidružte fond back-end k prostředku nástroje pro vyrovnávání zatížení.

- Na portálu User Portal se v okně **vytvořit Load Balancer** zobrazí možnost vytvořit **standardní** SKU nástroje pro vyrovnávání zatížení. Tato možnost není v Azure Stack podporována.

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

Balíček aktualizací Azure Stack 1902 si můžete stáhnout [odsud.](https://aka.ms/azurestackupdatedownload) 

Pouze v případě propojených scénářů Azure Stack nasazení pravidelně kontroluje zabezpečený koncový bod a automaticky vás upozorní, pokud je k dispozici aktualizace pro Cloud. Další informace najdete v tématu [Správa aktualizací pro Azure Stack](../azure-stack-updates.md#how-to-know-an-update-is-available).

## <a name="next-steps"></a>Další kroky

- Přehled správy aktualizací v Azure Stack najdete v tématu [Správa aktualizací v Azure Stack přehledu](../azure-stack-updates.md).  
- Další informace o tom, jak použít aktualizace s Azure Stack, najdete v tématu věnovaném [použití aktualizací v Azure Stack](../azure-stack-apply-updates.md).
- Informace o zásadách obsluhy pro Azure Stack integrovaných systémech a o tom, co je potřeba udělat, abyste zachovali svůj systém v podporovaném stavu, najdete v tématu [zásady pro obsluhu Azure Stack](../azure-stack-servicing-policy.md).  
- Postup pro monitorování a obnovení aktualizací pomocí privilegovaného koncového bodu (PEP) najdete v tématu [monitorování aktualizací v Azure Stack pomocí privilegovaného koncového bodu](../azure-stack-monitor-update.md).  
