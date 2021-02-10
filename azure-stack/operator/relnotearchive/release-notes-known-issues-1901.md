---
title: Aktualizace 1901 Azure Stack | Microsoft Docs
description: Seznamte se s aktualizací 1901 pro Azure Stack integrovaných systémů, včetně nových, známých problémů a místa, kde si tuto aktualizaci stáhnout.
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
ms.lastreviewed: 03/27/2019
ms.openlocfilehash: d3225284c8352771042742f8c8d94e4ae01abe2c
ms.sourcegitcommit: f9be5640dd445b3d926c9ce3e2165e96c72ece89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2021
ms.locfileid: "100009243"
---
# <a name="azure-stack-1901-update"></a>Aktualizace Azure Stack 1901

*Platí pro: Azure Stack integrovaných systémů*

Tento článek popisuje obsah balíčku aktualizace 1901. Tato aktualizace zahrnuje vylepšení, opravy a nové funkce pro tuto verzi Azure Stack. Tento článek také popisuje známé problémy v této verzi a obsahuje odkaz na stažení aktualizace. Známé problémy jsou rozděleny do potíží přímo souvisejících s procesem aktualizace a problémy se sestavením (po instalaci).

> [!IMPORTANT]  
> Tento balíček aktualizace je určen pouze pro Azure Stack integrovaných systémů. Nepoužívejte tento balíček aktualizace na Azure Stack Development Kit.

## <a name="build-reference"></a>Referenční informace o buildu

Číslo buildu aktualizace Azure Stack 1901 je **1.1901.0.95** nebo **1.1901.0.99** po 26. února 2019. Podívejte se na následující poznámku:

> [!IMPORTANT]  
> Společnost Microsoft zjistila problém, který může mít dopad na aktualizace z 1811 (1.1811.0.101) na 1901 a vydal aktualizovaný balíček 1901 pro vyřešení problému: Build 1.1901.0.99, aktualizované z 1.1901.0.95. Zákazníci, kteří už aktualizaci na 1.1901.0.95, nepotřebují provádět další akce.
>
> Připojení zákazníci, kteří jsou na 1811, automaticky uvidí nový balíček 1901 (1.1901.0.99) dostupný na portálu pro správu a po jeho instalaci by ho měli nainstalovat. Odpojení zákazníci mohou stáhnout a importovat nový balíček 1901 pomocí stejného procesu [popsaného zde](../azure-stack-apply-updates.md).
>
> Při instalaci dalšího úplného balíčku nebo balíčku oprav hotfix nebudou mít vliv na zákazníky s kteroukoli z těchto verzí 1901.

## <a name="hotfixes"></a>Opravy hotfix

Azure Stack pravidelně vydává opravy hotfix. Před aktualizací Azure Stack na 1901 nezapomeňte nainstalovat [nejnovější Azure Stack opravu hotfix](#azure-stack-hotfixes) pro 1811.

Azure Stack oprav hotfix se týkají pouze Azure Stack integrovaných systémů. Nepokoušejte se instalovat opravy hotfix na ASDK.

> [!TIP]  
> Přihlaste se k odběru následujících informačních kanálů *RSS* nebo *Atom* , abyste zachovali Azure Stack opravy hotfix:
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Azure Stack opravy hotfix

Pokud už máte 1901 a zatím jste nenainstalovali žádné opravy hotfix, můžete nainstalovat 1902 přímo, aniž byste nejdřív nainstalovali opravu hotfix 1901.

- **1811**: není k dispozici žádná aktuální oprava hotfix.
- **1901**: [KB 4500636 – Azure Stack hotfix 1.1901.5.109](https://support.microsoft.com/help/4500636)

## <a name="prerequisites"></a>Požadavky

> [!IMPORTANT]
> Před aktualizací na 1901 nainstalujte [nejnovější Azure Stack opravu hotfix](#azure-stack-hotfixes) pro 1811 (pokud existuje). Pokud už máte 1901 a zatím jste nenainstalovali žádné opravy hotfix, můžete nainstalovat 1902 přímo, aniž byste nejdřív nainstalovali opravu hotfix 1901.

- Před zahájením instalace této aktualizace spusťte rutinu [test-AzureStack](../azure-stack-diagnostic-test.md) s následujícími parametry, abyste ověřili stav svého Azure Stack a vyřešili jsme zjištěné provozní problémy, včetně všech upozornění a selhání. Zkontrolujte také aktivní výstrahy a vyřešte všechny akce, které vyžadují:

    ```powershell
    Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

- Pokud je Azure Stack spravováno pomocí System Center Operations Manager (SCOM), nezapomeňte před použitím 1901 aktualizovat sadu Management Pack pro Microsoft Azure Stack na verzi 1.0.3.11.

## <a name="new-features"></a>Nové funkce

Tato aktualizace obsahuje následující nové funkce a vylepšení pro Azure Stack:

- Spravované image na Azure Stack umožňují vytvořit objekt spravované image na zobecněném virtuálním počítači (nespravované a spravované), který umožňuje vytvářet virtuální počítače se spravovanými disky jenom dál. Další informace najdete v tématu [Azure Stack Managed disks](../../user/azure-stack-managed-disk-considerations.md#managed-images).

- **AzureRm 2.4.0**
   * **AzureRm. Profile**  
         Oprava chyby – `Import-AzureRmContext` správně se rekonstruovat uložený token.  
   * **AzureRm. Resources**  
         Oprava chyby – `Get-AzureRmResource` pro dotaz nerozlišovat velikost písmen podle typu prostředku.  
   * **Azure.Storage**  
         Souhrnný modul AzureRm nyní zahrnuje již publikovanou verzi 4.5.0 podporující **rozhraní API-version 2017-07-29**.  
   * **AzureRm. Storage**  
         Souhrnný modul AzureRm nyní zahrnuje již publikovanou verzi 5.0.4 podporující **rozhraní API-version 2017-10-01**.  
   * **AzureRm. COMPUTE**  
         Do a byly přidány jednoduché sady parametrů v `New-AzureRmVM` a `New-AzureRmVmss` , `-Image` parametr podporuje určení uživatelských imagí.  
   * **AzureRm. Insights**  
         Souhrnný modul AzureRm nyní zahrnuje již publikovanou verzi 5.1.5 podporující **rozhraní API-version 2018-01-01** pro metriky, typy prostředků definice metriky.

- **AzureStack 1.7.1** Tato vydaná verze změny Podrobnosti o zásadních změnách najdete v tématu https://aka.ms/azspshmigration171.
   * **AZS. Backup. admin – modul**  
         Změna způsobující chybu: Zálohování se mění na režim šifrování založené na certifikátu. Podpora symetrických klíčů je zastaralá.  
   * **AZS. Fabric. admin – modul**  
         `Get-AzsInfrastructureVolume` se již nepoužívá. Použijte novou rutinu `Get-AzsVolume` .  
         `Get-AzsStorageSystem` se již nepoužívá.  Použijte novou rutinu `Get-AzsStorageSubSystem` .  
         `Get-AzsStoragePool` se již nepoužívá. `StorageSubSystem`Objekt obsahuje vlastnost Capacity.  
   * **Modul Azs.Compute.Admin**  
         Oprava chyby- `Add-AzsPlatformImage` , `Get-AzsPlatformImage` : volání `ConvertTo-PlatformImageObject` pouze v cestě pro úspěch.  
         Opravu chyb- `Add-AzsVmExtension` , `Get-AzsVmExtension` : volání ConvertTo-VmExtensionObject pouze v cestě pro úspěch.  
   * **Modul Azs.Storage.Admin**  
         Oprava chyby – Pokud chybí zadání, použije se pro novou kvótu úložiště výchozí nastavení.

Referenční informace o aktualizovaných modulech najdete v tématu [Azure Stack Reference k modulům](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.6.0&viewFallbackFrom=azurestackps-1.7.0&preserve-view=true).

## <a name="fixed-issues"></a>Opravené problémy

- Opravili jsme problém, kdy se portál ukázal jako možnost vytvářet brány VPN založené na zásadách, které nejsou podporované v Azure Stack. Tato možnost byla odebrána z portálu.

<!-- 16523695 - IS, ASDK -->
- Opravili jsme problém, ve kterém po aktualizaci nastavení DNS pro vaši Virtual Network **použití služby Azure Stack DNS** na **vlastní DNS**. instance se neaktualizovaly novým nastavením.

- <!-- 3235634 - IS, ASDK -->
  Opravili jsme problém, kdy se nasazují virtuální počítače s velikostmi, které obsahují příponu **v2** . například **Standard_A2_v2** nutné zadat příponu jako **Standard_A2_v2** (malá písmena v). Stejně jako u globálních Azure můžete nyní použít **Standard_A2_V2** (velká písmena V).

<!--  2795678 - IS, ASDK --> 
- Opravili jsme problém, který vygeneroval upozornění při použití portálu k vytvoření virtuálních počítačů ve velikosti virtuálního počítače úrovně Premium (DS, Ds_v2, FS, FSv2). Virtuální počítač se vytvořil v účtu úložiště úrovně Standard. I když to neovlivnilo funkci, IOPs nebo fakturaci, bylo upozornění vyřešeno.

<!-- 1264761 - IS ASDK -->  
- Opravili jsme problém se součástí **kontroleru stavu** , která generovala následující výstrahy. Upozornění se můžou bezpečně ignorovat:

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


<!-- 3507629 - IS, ASDK --> 
- Opravili jsme problém při nastavení hodnoty kvóty Managed Disks v části [typy kvót výpočetních kvót](../azure-stack-quota-types.md#compute-quota-types) na hodnotu 0, je ekvivalentem výchozí hodnoty 2048 GIB. Nyní je respektována hodnota nulová kvóty.

<!-- 2724873 - IS --> 
- Opravili jsme problém, když pomocí rutin PowerShellu **Start-AzsScaleUnitNode** nebo  **stop-AzsScaleUnitNode** budete spravovat jednotky škálování, při prvním pokusu o spuštění nebo zastavení jednotky škálování se nemusí zdařit.

<!-- 2724961- IS ASDK --> 
- Opravili jsme problém, kdy jste v nastavení předplatného zaregistrovali poskytovatele prostředků **Microsoft. Insight** a vytvořili jste virtuální počítač s Windows s povoleným diagnostikou HOSTOVANÉHO operačního systému, ale v grafu procento využití procesoru na stránce Přehled virtuálních počítačů se nezobrazují data metrik. Data se nyní zobrazí správně.

- Opravili jsme problém, ve kterém při spuštění rutiny **Get-AzureStackLog** došlo k chybě, když spustíte **test-AzureStack** ve stejné relaci PEP (Privileged Endpoint). Teď můžete použít stejnou relaci PEP, ve které jste provedli rutinu **test-AzureStack**.

<!-- bug 3615401, IS -->
- Opravili jsme problém s automatickými zálohováními, kde by služba plánovače neočekávaně přešla do stavu zakázáno. 

<!--2850083, IS ASDK -->
- Odebrali jste tlačítko **reset Gateway** z portálu Azure Stack, což vyvolalo chybu, pokud bylo na tlačítko kliknuto. Toto tlačítko nefunguje v Azure Stack bez funkce, protože Azure Stack má více než vyhrazené instance virtuálních počítačů pro každé VPN Gateway klienta, takže se odebral, aby nedocházelo k nejasnostem. 

<!-- 3209594, IS ASDK -->
- Z okna **vlastností sítě** se odebral odkaz **efektivní pravidla zabezpečení** , protože tato funkce není v Azure Stack podporovaná. Když má odkaz k dispozici, měl by to dojem, že tato funkce byla podporovaná, ale nefunguje. Pro zmírnění nejasností jsme odebrali odkaz.

<!-- 3139614 | IS -->
- Opravili jsme problém, kdy po použití aktualizace pro Azure Stack od výrobce OEM se v portálu Azure Stack správce nezobrazilo oznámení **aktualizace k dispozici** .

## <a name="changes"></a>Změny

<!-- 3083238 IS -->
- Vylepšení zabezpečení v této aktualizaci vede ke zvýšení velikosti zálohy role adresářové služby. Aktualizované pokyny k změně velikosti pro umístění externího úložiště najdete v tématu dokumentace k zálohování infrastruktury. /Azure-Stack-Backup-reference.MD # úložiště – určení velikosti umístění). Tato změna má za následek delší dobu k dokončení zálohování kvůli větší velikosti přenosu dat. Tato změna se týká integrovaných systémů. 

- Od ledna 2019 můžete nasadit clustery Kubernetes ve službě Active Directory federované služby (AD FS) registrované Azure Stack razítka (přístup k Internetu se vyžaduje). Podle pokynů [zde](../azure-stack-solution-template-kubernetes-cluster-add.md) Stáhněte novou položku Kubernetes Marketplace. Pokud chcete nasadit cluster Kubernetes, postupujte podle pokynů [zde](../../user/azure-stack-solution-template-kubernetes-adfs.md) . Všimněte si nových parametrů, které určují, jestli je cílový systém přidaný nebo AD FS registrovaný. Pokud je AD FS, jsou k dispozici nová pole pro zadání parametrů Key Vault, ve kterých je certifikát nasazení uložený.

   I když podpora AD FS podporuje, nasazení clusterů Kubernetes vyžaduje přístup k Internetu.

- Po instalaci aktualizací nebo oprav hotfix do Azure Stack mohou být zavedeny nové funkce, které vyžadují, aby byla udělena nová oprávnění pro jednu nebo více aplikací identity. Udělení těchto oprávnění vyžaduje přístup správce k domovskému adresáři, a proto ho nelze automaticky provést. Příklad:

   ```powershell
   $adminResourceManagerEndpoint = "https://adminmanagement.<region>.<domain>"
   $homeDirectoryTenantName = "<homeDirectoryTenant>.onmicrosoft.com" # This is the primary tenant Azure Stack is registered to

   Update-AzsHomeDirectoryTenant -AdminResourceManagerEndpoint $adminResourceManagerEndpoint `
     -DirectoryTenantName $homeDirectoryTenantName -Verbose
   ```

- Existují nové aspekty pro přesné plánování Azure Stack kapacity. V případě aktualizace 1901 má nyní limit celkový počet Virtual Machines, které je možné vytvořit.  Tento limit má být dočasný, aby nedošlo k nestabilitě řešení. Zdroj problému se stabilitou s větším počtem virtuálních počítačů se řeší, ale konkrétní časová osa pro nápravu se ještě nezjistila. V případě aktualizace 1901 teď existuje limit počtu virtuálních počítačů, které jsou 60, a to s celkovým omezením řešení 700 na serveru.  Například limit 8 serverových Azure Stackch virtuálních počítačů by byl 480 (8 × 60).  U 12 až 16 serverových Azure Stack řešení by byl limit 700. Toto omezení se vytvořilo tak, aby se zajistilo splnění všech důležitých informací o výpočetní kapacitě, jako je například rezerva odolnosti a virtuální procesor, což je fyzický poměr, který by měl obsluha na razítku udržovat. Další informace najdete v nové verzi plánovače kapacity.  
V případě, že se dosáhlo limitu škálování virtuálního počítače, vrátí se následující chybové kódy jako výsledek: VMsPerScaleUnitLimitExceeded, VMsPerScaleUnitNodeLimitExceeded. 
 

- Verze rozhraní COMPUTE API se zvýšila na 2017-12-01.

- Záloha infrastruktury nyní vyžaduje certifikát pouze s veřejným klíčem (. CER) pro šifrování zálohovaných dat. Podpora symetrického šifrovacího klíče je zastaralá od začátku v 1901. Pokud je před aktualizací na 1901 nakonfigurovaná záloha infrastruktury, zůstanou šifrovací klíče na svém místě. K aktualizaci nastavení zálohování budete mít k dispozici alespoň 2 další aktualizace s podporou zpětné kompatibility. Další informace najdete v tématu [osvědčené postupy zálohování infrastruktury Azure Stack](../azure-stack-backup-best-practices.md).

## <a name="common-vulnerabilities-and-exposures"></a>Běžné chyby zabezpečení a ohrožení

Tato aktualizace nainstaluje tyto aktualizace zabezpečení:  

- [CVE-2018-8477](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8477)
- [CVE-2018-8514](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8514)
- [CVE-2018-8580](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8580)
- [CVE-2018-8595](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8595)
- [CVE-2018-8596](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8596)
- [CVE-2018-8598](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8598)
- [CVE-2018-8621](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8621)
- [CVE-2018-8622](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8622)
- [CVE-2018-8627](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8627)
- [CVE-2018-8637](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8637)
- [CVE-2018-8638](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8638)
- [ADV190001](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190001)
- [CVE-2019-0536](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0536)
- [CVE-2019-0537](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0537)
- [CVE-2019-0545](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0545)
- [CVE-2019-0549](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0549)
- [CVE-2019-0553](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0553)
- [CVE-2019-0554](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0554)
- [CVE-2019-0559](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0559)
- [CVE-2019-0560](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0560)
- [CVE-2019-0561](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0561)
- [CVE-2019-0569](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0569)
- [CVE-2019-0585](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0585)
- [CVE-2019-0588](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0588)


Další informace o těchto chybách zabezpečení získáte kliknutím na výše uvedené odkazy nebo v článku znalostní báze Microsoft Knowledge Base [4480977](https://support.microsoft.com/en-us/help/4480977).

## <a name="known-issues-with-the-update-process"></a>Známé problémy s procesem aktualizace

- Při pokusu o instalaci aktualizace Azure Stack se může stát, že se stav aktualizace nezdaří a změní se stav na **PreparationFailed**. To je způsobeno tím, že poskytovatel prostředků aktualizace (URP) nemůže správně přenést soubory z kontejneru úložiště do interní sdílené složky infrastruktury pro zpracování. Počínaje verzí 1901 (1.1901.0.95) můžete tento problém obejít tak, že znovu kliknete na **aktualizovat** ( **nepokračovat**). URP pak vyčistí soubory z předchozího pokusu a znovu spustí stahování.

- Při spuštění rutiny [test-AzureStack](../azure-stack-diagnostic-test.md), pokud **AzsInfraRoleSummary** nebo **AzsPortalApiSummary** test dojde k chybě, budete vyzváni ke spuštění rutiny **test-AzureStack** s `-Repair` příznakem.  Pokud spustíte tento příkaz, dojde k chybě s následující chybovou zprávou:  `Unexpected exception getting Azure Stack health status. Cannot bind argument to parameter 'TestResult' because it is null.`

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

<!-- ### Health and monitoring -->

### <a name="compute"></a>Compute

- Při vytváření nového virtuálního počítače s Windows se může zobrazit následující chyba:

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   K této chybě dojde, pokud povolíte diagnostiku spouštění na virtuálním počítači, ale odstraníte účet úložiště diagnostiky spouštění. Pokud chcete tento problém obejít, vytvořte znovu účet úložiště se stejným názvem, jako jste použili dříve.

<!-- 2967447 - IS, ASDK, to be fixed in 1902 -->
- Prostředí pro vytváření VMSS (Virtual Machine Scale set) poskytuje možnost nasazení na bázi CentOS 7,2. Vzhledem k tomu, že tato image není k dispozici na Azure Stack, vyberte jiný operační systém pro vaše nasazení nebo použijte šablonu Azure Resource Manager určující jinou image CentOS, která byla stažena před nasazením z webu Marketplace pomocí operátoru.  

<!-- TBD - IS ASDK --> 
- Po použití aktualizace 1901 se můžete setkat s následujícími problémy při nasazení virtuálních počítačů pomocí Managed Disks:

   - Pokud bylo předplatné vytvořeno před aktualizací 1808, může při nasazení virtuálního počítače s Managed Disks dojít k chybě s interní chybovou zprávou. Pokud chcete chybu vyřešit, postupujte podle těchto kroků u každého předplatného:
      1. Na portálu tenanta klikněte na **předplatná** a vyhledejte předplatné. Vyberte **poskytovatelé prostředků**, vyberte **Microsoft. COMPUTE** a potom klikněte na **znovu registrovat**.
      2. V rámci stejného předplatného, přejít na **Access Control (IAM)** a ověřte, že je v seznamu uvedeno **rozhraní AzureStack-DiskRP-Client** .
   - Pokud jste nakonfigurovali víceklientské prostředí, může se při nasazování virtuálních počítačů v rámci předplatného, které je přidružené k adresáři hosta, selhat chyba s interní chybovou zprávou. Pokud chcete chybu vyřešit, postupujte podle těchto kroků v [tomto článku](../azure-stack-enable-multitenancy.md#register-a-guest-directory) a překonfigurujte všechny adresáře hostů.

- Virtuální počítač Ubuntu 18,04 vytvořený s povoleným autorizací SSH vám neumožní používat klíče SSH k přihlášení. Alternativním řešením je použít pro rozšíření pro Linux přístup k virtuálnímu počítači k implementaci klíčů SSH po zřízení nebo použití ověřování na základě hesla.

- V okně **Virtual Machine Scale Sets** nemůžete odebrat sadu škálování. Jako alternativní řešení vyberte sadu škálování, kterou chcete odebrat, a pak klikněte na tlačítko **Odstranit** v podokně **Přehled** .

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
- Azure Stack nepodporuje současné připojení více než 4 síťových rozhraní k instancím virtuálních počítačů bez ohledu na velikost instance.

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

Balíček aktualizací Azure Stack 1901 si můžete stáhnout [odsud.](https://aka.ms/azurestackupdatedownload) 

Pouze v případě propojených scénářů Azure Stack nasazení pravidelně kontroluje zabezpečený koncový bod a automaticky vás upozorní, pokud je k dispozici aktualizace pro Cloud. Další informace najdete v tématu [Správa aktualizací pro Azure Stack](../azure-stack-updates.md#how-to-know-an-update-is-available).

## <a name="next-steps"></a>Další kroky

- Přehled správy aktualizací v Azure Stack najdete v tématu [Správa aktualizací v Azure Stack přehledu](../azure-stack-updates.md).  
- Další informace o tom, jak použít aktualizace s Azure Stack, najdete v tématu věnovaném [použití aktualizací v Azure Stack](../azure-stack-apply-updates.md).
- Informace o zásadách obsluhy pro Azure Stack integrovaných systémech a o tom, co je potřeba udělat, abyste zachovali svůj systém v podporovaném stavu, najdete v tématu [zásady pro obsluhu Azure Stack](../azure-stack-servicing-policy.md).  
- Postup pro monitorování a obnovení aktualizací pomocí privilegovaného koncového bodu (PEP) najdete v tématu [monitorování aktualizací v Azure Stack pomocí privilegovaného koncového bodu](../azure-stack-monitor-update.md).  
