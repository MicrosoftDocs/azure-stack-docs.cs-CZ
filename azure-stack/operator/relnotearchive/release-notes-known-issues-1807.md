---
title: Aktualizace 1807 Azure Stack | Microsoft Docs
description: Přečtěte si, co je nového v aktualizaci 1807 pro Azure Stack integrovaných systémů, včetně známých problémů a místa, kde si můžete aktualizaci stáhnout.
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
ms.date: 09/26/2018
ms.author: sethm
ms.reviewer: justini
ROBOTS: NOINDEX
ms.openlocfilehash: 6c40a3be7bddf49166979458e755058b5aba47c9
ms.sourcegitcommit: e432e7f0a790bd6419987cbb5c5f3811e2e7a4a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102515936"
---
# <a name="azure-stack-1807-update"></a>Aktualizace Azure Stack 1807

*Platí pro: Azure Stack integrovaných systémů*

Tento článek popisuje obsah balíčku aktualizace 1807. Tato aktualizace zahrnuje vylepšení, opravy a známé problémy této verze Azure Stack a místo stažení aktualizace. Známé problémy jsou rozdělené na problémy přímo související s procesem aktualizace a problémy se sestavením (po instalaci).

> [!IMPORTANT]  
> Tento balíček aktualizace je určen pouze pro Azure Stack integrovaných systémů. Nepoužívejte tento balíček aktualizace na Azure Stack Development Kit.

## <a name="build-reference"></a>Referenční informace o buildu

Číslo buildu aktualizace Azure Stack 1807 je **1.1807.0.76**.  

### <a name="new-features"></a>Nové funkce

Tato aktualizace obsahuje následující vylepšení pro Azure Stack.

<!-- 1658937 | ASDK, IS --> 
- **Zahajte zálohování podle předem definovaného plánu** jako zařízení, Azure Stack teď můžou pravidelně automaticky spouštět zálohy infrastruktury, aby se vyloučil lidský zásah. Azure Stack také automaticky vyčistí externí sdílení pro zálohy, které jsou starší než definovaná doba uchování. Další informace najdete v tématu [Povolení zálohování pro Azure Stack pomocí prostředí PowerShell](../azure-stack-backup-enable-backup-powershell.md).

<!-- 2496385 | ASDK, IS -->  
- **Byla přidána doba přenosu dat do celkové doby zálohování.** Další informace najdete v tématu [Povolení zálohování pro Azure Stack pomocí prostředí PowerShell](../azure-stack-backup-enable-backup-powershell.md).

<!-- 1702130 | ASDK, IS -->  
- **Externí kapacita zálohování teď zobrazuje správnou kapacitu externího sdílení.** (Dřív to bylo pevný kód na 10 GB.) Další informace najdete v tématu [Povolení zálohování pro Azure Stack pomocí prostředí PowerShell](../azure-stack-backup-enable-backup-powershell.md).

<!-- 2508488 |  IS   -->
- **Kapacitu rozšíříte** [přidáním dalších uzlů jednotek škálování](../azure-stack-add-scale-node.md).

<!-- 2753130 |  IS, ASDK   -->  
- **Šablony Azure Resource Manager nyní podporují prvek podmínky** – nyní můžete nasadit prostředek do šablony Azure Resource Manageru pomocí podmínky. Můžete navrhnout šablonu pro nasazení prostředku na základě podmínky, jako je například vyhodnocení, zda je hodnota parametru k dispozici. Informace o použití šablony jako podmínky najdete v části [podmíněné nasazení prostředků](/azure/architecture/guide/azure-resource-manager/advanced-templates/conditional-deploy) a proměnných v tématu [šablony Azure Resource Manager](/azure/azure-resource-manager/resource-manager-templates-variables) v dokumentaci k Azure. 

   Šablony můžete použít také k [nasazení prostředků do více než jednoho předplatného nebo skupiny prostředků](/azure/azure-resource-manager/resource-manager-cross-resource-group-deployment).  

<!--2753073 | IS, ASDK -->  
- **Podpora verze prostředků Microsoft. Network API se aktualizovala** tak, aby zahrnovala podporu rozhraní api verze 2017-10-01 od 2015-06-15 pro Azure Stack síťové prostředky.  V této verzi není zahrnutá podpora pro verze prostředků mezi 2017-10-01 a 2015-06-15.  Rozdíly ve funkcích najdete [v tématu týkajícím se Azure Stack sítě](../../user/azure-stack-network-differences.md) .

<!-- 2272116 | IS, ASDK   -->  
- **Azure Stack se přidala podpora reverzních vyhledávání DNS pro externě Azure Stack koncových bodů infrastruktury** (to je portál, adminportal, Správa a adminmanagement). Tím umožníte, aby se názvy externích koncových bodů z IP adresy vyřešily Azure Stack.

<!-- 2780899 |  IS, ASDK   --> 
- **Azure Stack teď podporuje přidání dalších síťových rozhraní k existujícímu virtuálnímu počítači.**  Tato funkce je k dispozici pomocí portálu, PowerShellu a rozhraní příkazového řádku. Další informace najdete v tématu [Přidání nebo odebrání síťových rozhraní](/azure/virtual-network/virtual-network-network-interface-vm) v dokumentaci k Azure. 

<!-- 2222444 | IS, ASDK   -->  
- **Pro měřiče využití sítě se provedla vylepšení přesnosti a odolnosti proti chybám**.  Měřiče využití sítě jsou teď přesnější a berou v úvahu pozastavená předplatná, doby výpadků a konflikty časování.

<!-- 2753080 | IS -->  
- **Aktualizuje oznámení k dispozici.** Nasazení připojených Azure Stack nyní pravidelně kontrolují zabezpečený koncový bod a určí, jestli je pro váš Cloud k dispozici aktualizace. Toto oznámení se zobrazí na dlaždici aktualizace, jak by bylo po ruční kontrole a importu nové aktualizace. Přečtěte si další informace o [správě aktualizací pro Azure Stack](../azure-stack-updates.md).

<!-- 2297790 | IS, ASDK -->  
- **Vylepšení klienta Azure Stack syslog (funkce Preview)**. Tento klient umožňuje předávání auditu a protokolů vztahujících se k Azure Stack infrastruktuře na server syslog nebo software pro správu událostí a SIEM (Security Information and Event Management) mimo Azure Stack. Klient syslog teď podporuje protokol TCP s prostým textem nebo šifrováním TLS 1,2, přičemž ta má výchozí konfiguraci. Připojení TLS můžete nakonfigurovat buď pomocí serveru, nebo přes vzájemné ověřování.

  Chcete-li nakonfigurovat, jak klient syslog komunikuje (například protokol, šifrování a ověřování) se serverem syslog, použijte rutinu *set-SyslogServer* . Tato rutina je k dispozici z privilegovaného koncového bodu (PEP).

  Pokud chcete přidat certifikát na straně klienta pro vzájemné ověřování TLS 1,2 klienta syslog, použijte rutinu Set-SyslogClient v PEP.

  V této verzi Preview vidíte mnohem větší počet auditů a upozornění. 

  Vzhledem k tomu, že tato funkce je stále ve verzi Preview, nespoléhá se na ni v produkčním prostředí.

  Další informace najdete v tématu [Azure Stack předávání SYSLOG](../azure-stack-integrate-security.md).

<!-- ####### | IS, ASDK | --> 
- **Azure Resource Manager obsahuje název oblasti.** V této verzi budou objekty načtené z Azure Resource Manager nyní zahrnovat atribut název oblasti. Pokud existující skript prostředí PowerShell přímo předává objekt jiné rutině, může skript způsobit chybu a selhat. To je Azure Resource Manager vyhovující chování a vyžaduje, aby volající klient odčítání atributu region. Další informace o Azure Resource Manager najdete v [dokumentaci k Azure Resource Manager](/azure/azure-resource-manager/). ověření 8-10 MDB-->

<!-- TBD | IS, ASDK -->  
- **Změny funkcí delegovaných zprostředkovatelů.** Počínaje 1807 je model delegovaných zprostředkovatelů zjednodušený, aby se lépe zarovnaly modelu prodejce v systému Azure a delegovaní zprostředkovatelé nebudou moct vytvářet další delegované zprostředkovatele, a to v podstatě tak, aby byl model k dispozici na jedné úrovni. Aby bylo možné přejít k novému modelu a správě předplatných, mohou být odběry uživatelů přesunuty mezi nové nebo existující odběry delegovaných zprostředkovatelů, kteří patří do stejného tenanta adresáře. Uživatelská předplatná, která patří do výchozího předplatného poskytovatele, můžete také přesunout do předplatných delegovaných poskytovatelů ve stejném adresáři – tenant.  Další informace najdete [v tématu delegování nabídek v Azure Stack](../azure-stack-delegated-provider.md).

<!-- 2536808 IS ASDK --> 
- **Vylepšená doba vytváření virtuálních počítačů** pro virtuální počítače vytvořené pomocí imagí, které stáhnete z webu Azure Marketplace.

<!-- TBD | IS, ASDK -->  
- **Azure Stack vylepšení použitelnosti Capacity Planner**. Azure Stack [Capacity Planner](https://aka.ms/azstackcapacityplanner) teď nabízí zjednodušené prostředí pro vkládání mezipaměti s2d a kapacity s2d při definování SKU řešení. Byl odebrán limit počtu virtuálních počítačů 1000.


### <a name="fixed-issues"></a>Opravené problémy

<!-- TBD | ASDK, IS --> 
- V procesu aktualizace byly provedeny různé vylepšení, aby byla spolehlivější. Kromě toho jsme nastavili opravy základní infrastruktury, což minimalizuje možné výpadky úloh během aktualizace.

<!--2292271 | ASDK, IS --> 
- Opravili jsme problém, kdy se upravený limit kvóty nevztahoval na existující předplatná. Když teď vyvoláte omezení kvóty pro síťový prostředek, který je součástí nabídky a plánu, který je přidružený k předplatnému uživatele, bude se nové omezení vztahovat na stávající předplatné i na nové odběry.

<!-- 448955 | IS ASDK --> 
- Teď můžete úspěšně zadávat dotazy na protokoly aktivit pro systémy, které jsou nasazené v časovém pásmu UTC + N.    

<!-- 2319627 |  ASDK, IS --> 
- Předběžná kontroly parametrů konfigurace zálohování (cesta/uživatelské jméno/heslo/šifrovací klíč) už nenastaví nesprávná nastavení pro konfiguraci zálohování. (Dříve, nesprávná nastavení byla nastavena do zálohování a zálohování by pak při aktivaci nedošlo k chybě.)

<!-- 2215948 |  ASDK, IS -->
- Seznam zálohování se teď aktualizuje, když ručně odstraníte zálohu z externí sdílené složky.

<!-- 2332636 | IS -->  
- Aktualizace na tuto verzi už při nasazení s AD FS neobnoví výchozí vlastníka výchozího předplatného poskytovatele na integrovaného uživatele **CloudAdmin** .

<!-- 2360715 |  ASDK, IS -->  
- Když nastavíte integraci Datacenter, nebudete už mít k souboru AD FS metadat přístup ze sdílené složky Azure. Další informace najdete v tématu [Nastavení integrace AD FS poskytnutím souboru federačních metadat](../azure-stack-integrate-identity.md#setting-up-ad-fs-integration-by-providing-federation-metadata-file). 

<!-- 2388980 | ASDK, IS --> 
- Opravili jsme problém, který uživatelům zabránil v přiřazení stávající veřejné IP adresy, která byla dříve přiřazena k síťovému rozhraní nebo Load Balancer k novému síťovému rozhraní nebo Load Balancer.  

<!-- 2551834 - IS, ASDK --> 
- Když vyberete *Přehled* pro účet úložiště na portálech správce nebo uživatelů, v podokně *základy* se teď zobrazí všechny očekávané informace správně. 

<!-- 2551834 - IS, ASDK --> 
- Když vyberete *značky* pro účet úložiště buď na portálu pro správu, nebo na portálu User Portal, informace se nyní zobrazí správně.

<!-- TBD - IS ASDK --> 
- Tato verze Azure Stack řeší problém, který zabránil použití aktualizací ovladačů z balíčků rozšíření OEM.

<!-- 2055809- IS ASDK --> 
- Opravili jsme problém, který vám zabránil v odstranění virtuálních počítačů z okna COMPUTE, když se virtuální počítač nepovedlo vytvořit.  

<!--  2643962 IS ASDK -->  
- Výstraha na *nedostatečnou kapacitu paměti* se už nezobrazuje správně.

- **Různé opravy** pro výkon, stabilitu, zabezpečení a operační systém, který je používán nástrojem Azure Stack.


<!-- ### Additional releases timed with this update    -->

### <a name="common-vulnerabilities-and-exposures"></a>Běžné chyby zabezpečení a ohrožení
Azure Stack používá k hostování infrastruktury klíčů instalace jádra serveru systému Windows Server 2016. Tato verze nainstaluje na servery infrastruktury Azure Stack následující aktualizace Windows serveru 2016: 
- [CVE-2018-8206](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8206)
- [CVE-2018-8222](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8222)
- [CVE-2018-8282](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8282)
- [CVE-2018-8304](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8304)
- [CVE-2018-8307](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8307)
- [CVE-2018-8308](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8308) 
- [CVE-2018-8309](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8309)
- [CVE-2018-8313](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8313)  

Další informace o těchto chybách zabezpečení získáte kliknutím na výše uvedené odkazy nebo v článcích znalostní báze Microsoft Knowledge Base [4338814](https://support.microsoft.com/help/4338814)  a [4345418](https://support.microsoft.com/help/4345418).



## <a name="before-you-begin"></a>Než začnete

### <a name="prerequisites"></a>Požadavky

- Před instalací aktualizace Azure Stack 1807 nainstalujte aktualizaci Azure Stack 1805.  Nebyla k dispozici žádná aktualizace 1806.  

- Nainstalujte nejnovější dostupnou aktualizaci nebo opravu hotfix pro verzi 1805.  

- Před zahájením instalace této aktualizace spusťte rutinu [test-AzureStack](../azure-stack-diagnostic-test.md) s následujícími parametry, abyste ověřili stav svého Azure Stack a vyřešili jsme zjištěné provozní problémy, včetně všech upozornění a selhání. Zkontrolujte také aktivní výstrahy a vyřešte všechny akce, které vyžadují.  

  ```PowerShell
  Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
  ``` 

### <a name="known-issues-with-the-update-process"></a>Známé problémy s procesem aktualizace

<!-- 2468613 - IS --> 
- Během instalace této aktualizace se můžou zobrazit upozornění s *šablonou chyby nadpisu pro typ FaultType UserAccounts. chybí New.*  Tyto výstrahy můžete bezpečně ignorovat. Tyto výstrahy se po dokončení instalace této aktualizace automaticky zavřou.

<!-- 2489559 - IS --> 
- Nepokoušejte se během instalace této aktualizace vytvořit virtuální počítače. Další informace o správě aktualizací najdete v tématu [Správa aktualizací v Azure Stack Overview](../azure-stack-updates.md).

<!-- 2830461 - IS --> 
- V některých případech, kdy aktualizace vyžaduje pozornost, nemusí být vygenerována odpovídající výstraha. Přesný stav se stále projeví na portálu a nebude to mít vliv na.

### <a name="post-update-steps"></a>Kroky po aktualizaci
Po instalaci této aktualizace nainstalujte všechny příslušné opravy hotfix. Další informace najdete v následujících článcích znalostní báze a v našich [zásadách pro obsluhu](../azure-stack-servicing-policy.md). 
- [KB 4464231 Azure Stack hotfix Azure Stack hotfix 1.1807.1.78](https://support.microsoft.com/help/4464231)

<!-- 2933866 IS --> Po instalaci této aktualizace se můžete podívat **na vylepšený stav instalací aktualizací, které se nezdařily.** To může zahrnovat informace o chybách předchozí instalace aktualizací, které jsou revidovány tak, aby odrážely dvě nové kategorie STAVů. Nové kategorie STAVů jsou *PreparationFailed* a *InstallationFailed*.  

## <a name="known-issues-post-installation"></a>Známé problémy (po instalaci)

Níže jsou uvedené známé problémy po instalaci této verze buildu.

### <a name="portal"></a>Portál

- Technickou dokumentaci Azure Stack se zaměřuje na nejnovější verzi. Vzhledem k změnám portálu mezi verzemi, co vidíte při použití Azure Stack portály, se může lišit od toho, co vidíte v dokumentaci. 

- Možnost [otevření nové žádosti o podporu z rozevíracího seznamu](../azure-stack-manage-portals.md#quick-access-to-help-and-support) v rámci portálu pro správu není k dispozici. Místo toho pro Azure Stack integrovaných systémů použijte následující odkaz: [https://aka.ms/newsupportrequest](https://aka.ms/newsupportrequest) .

<!-- 2931230 IS  ASDK --> 
- Plány, které se přidají k předplatnému uživatele jako doplněk, nejde odstranit, i když z předplatného uživatele odeberete plán. Plán zůstane, dokud nebudou odstraněny také odběry, které odkazují na plán doplňku. 

<!--2760466 IS  ASDK --> 
- Při instalaci nového prostředí Azure Stack, ve kterém je spuštěna tato verze, se nemusí zobrazit výstraha, která indikuje, že se *vyžaduje aktivace* . Předtím, než budete moct použít syndikaci Marketplace, se vyžaduje [Aktivace](../azure-stack-registration.md) .  

<!-- TBD - IS ASDK --> 
- Nelze použít dva typy předplatného pro správu, které byly představeny s verzí 1804. Typy předplatného jsou odběry **měření** a **předplatné spotřeby**. Tyto typy předplatného jsou viditelné v nových Azure Stack prostředí od verze 1804, ale ještě nejsou připravené k použití. Měli byste dál používat výchozí typ předplatného **poskytovatele** .

<!-- 2403291 - IS ASDK --> 
- V dolní části portálů pro správu a uživatele možná nebudete používat vodorovný posuvník. Pokud nemůžete získat přístup k vodorovnému posuvníku, pomocí cesty přejděte na předchozí okno na portálu, a to tak, že vyberete název okna, které chcete zobrazit, v seznamu s popisem cesty, který najdete v levém horním rohu portálu.

<!-- TBD - IS --> 
- Na portálu pro správu možná nebude možné zobrazit výpočetní prostředky ani prostředky úložiště. Příčinou tohoto problému je chyba při instalaci aktualizace, která způsobí, že aktualizace bude nesprávně nahlášena jako úspěšná. Pokud k tomuto problému dojde, obraťte se na službu zákaznické podpory Microsoftu, kde najdete pomoc.

<!-- TBD - IS --> 
- Na portálu se může zobrazit prázdný řídicí panel. Řídicí panel obnovíte tak, že vyberete ikonu ozubeného kolečka v pravém horním rohu portálu a pak vyberete **Obnovit výchozí nastavení**.

<!-- TBD - IS ASDK --> 
- Výsledkem odstranění předplatných uživatelů jsou osamocené prostředky. Jako alternativní řešení nejprve odstraňte prostředky uživatelů nebo celou skupinu prostředků a pak odstraňte odběry uživatelů.

<!-- TBD - IS ASDK --> 
- Oprávnění k vašemu předplatnému nemůžete zobrazit pomocí Azure Stackch portálů. Alternativním řešením je ověřit oprávnění pomocí PowerShellu.


### <a name="health-and-monitoring"></a>Stav a monitorování
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

<!-- 2368581 - IS. ASDK --> 
- Operátor Azure Stack, pokud obdržíte výstrahu o nedostatku paměti a virtuální počítače klienta se nepodaří nasadit s **chybou vytvoření virtuálního počítače infrastruktury**, je možné, že Azure Stack razítko není dostupné paměti. [Capacity Planner Azure Stack](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) použijte k tomu, abyste nejlépe pochopili kapacitu dostupnou pro vaše úlohy.


### <a name="compute"></a>Compute

<!-- 2724873 - IS --> 
- Při použití rutin PowerShellu **Start-AzsScaleUnitNode** nebo  **stop-AzsScaleUnitNode** pro správu jednotek škálování se může stát, že první pokus o spuštění nebo zastavení jednotky škálování selže. Pokud rutina při prvním spuštění neprojde, spusťte rutinu podruhé. Druhý běh by měl úspěšně dokončit operaci. 

<!-- 2494144 - IS, ASDK --> 
- Při výběru velikosti virtuálního počítače pro nasazení virtuálního počítače nejsou některé velikosti virtuálních počítačů řady F-Series při vytváření virtuálního počítače viditelné jako součást selektoru velikostí. Následující velikosti virtuálních počítačů se nezobrazí v selektoru: *F8s_v2*, *F16s_v2*, *F32s_v2* a *F64s_v2*.  
  Jako alternativní řešení použijte k nasazení virtuálního počítače jednu z následujících metod. V každé metodě je nutné zadat velikost virtuálního počítače, kterou chcete použít.

  - **Šablona Azure Resource Manager:** Když použijete šablonu, nastavte *vmSize* v šabloně tak, aby se rovnala velikosti virtuálního počítače, kterou chcete použít. K nasazení virtuálního počítače, který používá *F32s_v2* velikost, se používá například následující položka:  

    ```
        "properties": {
        "hardwareProfile": {
                "vmSize": "Standard_F32s_v2"
        },
    ```  
  - Rozhraní příkazového **řádku Azure:** Můžete použít příkaz [AZ VM Create](/cli/azure/vm?preserve-view=true&view=azure-cli-latest#az-vm-create) a zadat velikost virtuálního počítače jako parametr, podobně jako `--size "Standard_F32s_v2"` .

  - **PowerShell:** Pomocí prostředí PowerShell můžete použít rutinu [New-AzureRMVMConfig](/powershell/module/azurerm.compute/new-azurermvmconfig?preserve-view=true&view=azurermps-6.0.0) s parametrem, který určuje velikost virtuálního počítače, podobně jako `-VMSize "Standard_F32s_v2"` .


<!-- TBD - IS ASDK --> 
- Nastavení škálování pro sady škálování virtuálních počítačů nejsou na portálu k dispozici. Jako alternativní řešení můžete použít [Azure PowerShell](/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Kvůli rozdílům v verzi PowerShellu musíte použít `-Name` parametr místo `-VMScaleSetName` .

<!-- TBD - IS --> 
- Když na portálu vytvoříte skupinu dostupnosti tak, že kliknete na **Nová**  >    >  **Skupina dostupnosti** služby COMPUTE, můžete vytvořit jenom skupinu dostupnosti s doménou selhání a aktualizovat doménu na 1. Alternativním řešením při vytváření nového virtuálního počítače je vytvoření skupiny dostupnosti pomocí PowerShellu, rozhraní příkazového řádku nebo v rámci portálu.

<!-- TBD - IS ASDK --> 
- Když vytváříte virtuální počítače na portálu Azure Stack User Portal, portál zobrazí nesprávný počet datových disků, které se můžou připojit k virtuálnímu počítači DS Series. Virtuální počítače řady DS můžou přijmout tolik datových disků jako konfigurace Azure.

<!-- TBD - IS ASDK --> 
- Pokud zřízení rozšíření na nasazení virtuálního počítače trvá příliš dlouho, uživatelé by měli povolit časový limit zřizování namísto pokusu o zastavení procesu navrácení nebo odstranění virtuálního počítače.  

<!-- 1662991 IS ASDK --> 
- Diagnostika virtuálního počítače se systémem Linux není v Azure Stack podporována. Když nasadíte virtuální počítač se systémem Linux s povoleným diagnostikou virtuálních počítačů, nasazení se nezdařilo. Nasazení se také nepovede, pokud povolíte základní metriky virtuálního počítače se systémem Linux prostřednictvím nastavení diagnostiky.  

<!-- 2724961- IS ASDK --> 
- Když zaregistrujete poskytovatele prostředků **Microsoft. Insight** v nastaveních předplatného a vytvoříte virtuální počítač s Windows s povolenou diagnostikou HOSTOVANÉHO operačního systému, na stránce Přehled virtuálních počítačů se nezobrazují data metrik. 

   Pokud chcete najít data metrik, jako je procento procesoru pro daný virtuální počítač, otevřete okno **metriky** a zobrazte všechny podporované metriky hostů virtuálních počítačů s Windows.

### <a name="networking"></a>Sítě  

<!-- 1766332 - IS ASDK --> 
- Pokud v části **sítě** kliknete na **Vytvořit VPN Gateway** pro nastavení připojení k síti VPN, bude **zásada založená** jako typ sítě VPN. Tuto možnost nevybírejte. V Azure Stack je podporována pouze možnost **na základě trasy** .

<!-- 1902460 - IS ASDK --> 
- Azure Stack podporuje jednu *bránu místní sítě* na IP adresu. To platí pro všechna předplatná tenanta. Po vytvoření prvního připojení brány místní sítě se budou zablokované následné pokusy o vytvoření prostředku brány místní sítě se stejnou IP adresou.

<!-- 16309153 - IS ASDK --> 
- Na Virtual Network, která byla vytvořena pomocí *automatického* nastavení serveru DNS, se změna na vlastní server DNS nezdařila. Aktualizovaná nastavení nejsou vložena do virtuálních počítačů v této virtuální síti.

<!-- 2702741 -  IS ASDK --> 
- Veřejné IP adresy, které jsou nasazeny pomocí metody dynamického přidělení, nejsou zaručeny, aby byly zachovány po vydání Stop-Deallocate.

<!-- 2529607 - IS ASDK --> 
- Během Azure Stack *rotace tajných klíčů* existuje období, ve kterém jsou veřejné IP adresy nedosažitelná po dobu dvou až pěti minut.

<!-- 2664148 - IS ASDK --> 
- Ve scénářích, kdy klient přistupuje ke svým virtuálním počítačům pomocí tunelového propojení VPN S2S, se může vyskytnout scénář, kdy se pokusy o připojení nezdaří, pokud byla místní podsíť přidána do brány místní sítě po vytvoření brány. 


### <a name="sql-and-mysql"></a>SQL a MySQL



<!-- No Number - IS, ASDK -->  
- Speciální znaky, včetně mezer a teček, se v názvu **rodiny** při vytváření SKU pro poskytovatele prostředků SQL a MySQL nepodporují. 

<!-- TBD - IS --> 
- Pouze poskytovatel prostředků je podporován k vytváření položek na serverech, které jsou hostiteli SQL nebo MySQL. Položky vytvořené na hostitelském serveru, které nejsou vytvořené poskytovatelem prostředků, můžou vést k neshodě stavu.  

<!-- TBD - IS -->
> [!NOTE]   
> Po aktualizaci na tuto verzi Azure Stack můžete nadále používat poskytovatele prostředků SQL a MySQL, které jste předtím nasadili.  Pokud bude k dispozici nová verze, doporučujeme aktualizovat SQL a MySQL. Podobně jako u Azure Stack použijte postupně aktualizace pro poskytovatele prostředků SQL a MySQL. Například pokud používáte verzi 1804, nejprve použijte verzi 1805 a pak aktualizujte na 1807.  
>  
> Instalace této aktualizace neovlivňuje aktuální použití poskytovatelů prostředků SQL nebo MySQL vašimi uživateli. 
> Bez ohledu na verzi poskytovatelů prostředků, které použijete, se data vašich uživatelů v jejich databázích nekontaktují a zůstávají dostupná.  

### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK --> 
- Před vytvořením první funkce Azure v předplatném musí uživatelé zaregistrovat poskytovatele prostředků úložiště.

<!-- 2489178 - IS ASDK --> 
- Pro horizontální navýšení kapacity infrastruktury (pracovní procesy, správy, front-endové role) musíte použít PowerShell, jak je popsáno v poznámkách k verzi pro výpočetní prostředky.

<!-- TBD - IS ASDK --> 
- V tuto chvíli se dá nasadit App Service jenom do *předplatného výchozího poskytovatele* . 


### <a name="usage"></a>Využití  
<!-- TBD - IS ASDK --> 
- Data měřiče využití veřejných IP adres zobrazují stejnou hodnotu *EventDateTime* pro každý záznam místo razítka *timedate* , které ukazuje, kdy byl záznam vytvořen. V současné době nemůžete tato data použít k provedení přesného účtování využití veřejné IP adresy.


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>Stáhnout aktualizaci
Balíček aktualizací Azure Stack 1807 si můžete stáhnout [odsud.](https://aka.ms/azurestackupdatedownload)


## <a name="next-steps"></a>Další kroky
- Informace o zásadách obsluhy pro Azure Stack integrovaných systémech a o tom, co je potřeba udělat, abyste zachovali svůj systém v podporovaném stavu, najdete v tématu [zásady pro obsluhu Azure Stack](../azure-stack-servicing-policy.md).  
- Postup pro monitorování a obnovení aktualizací pomocí privilegovaného koncového bodu (PEP) najdete v tématu [monitorování aktualizací v Azure Stack pomocí privilegovaného koncového bodu](../azure-stack-monitor-update.md).  
- Přehled správy aktualizací v Azure Stack najdete v tématu [Správa aktualizací v Azure Stack přehledu](../azure-stack-updates.md).  
- Další informace o tom, jak použít aktualizace s Azure Stack, najdete v tématu věnovaném [použití aktualizací v Azure Stack](../azure-stack-apply-updates.md).
