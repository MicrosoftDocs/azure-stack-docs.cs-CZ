---
title: Aktualizace 1808 Azure Stack | Microsoft Docs
description: Přečtěte si, co je nového v aktualizaci 1808 pro Azure Stack integrovaných systémů, včetně známých problémů a místa, kde si můžete aktualizaci stáhnout.
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
ms.date: 10/02/2018
ms.author: sethm
ms.reviewer: justini
ROBOTS: NOINDEX
ms.openlocfilehash: 7d7ba1e7e3b6686002d24df2e286af81ddda5ce7
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248053"
---
# <a name="azure-stack-1808-update"></a>Aktualizace Azure Stack 1808

*Platí pro: Azure Stack integrovaných systémů*

Tento článek popisuje obsah balíčku aktualizace 1808. Balíček aktualizace obsahuje vylepšení, opravy a známé problémy pro tuto verzi Azure Stack. Tento článek obsahuje taky odkaz, abyste si mohli stáhnout aktualizaci. Známé problémy jsou rozdělené na problémy přímo související s procesem aktualizace a problémy se sestavením (po instalaci).

> [!IMPORTANT]  
> Tento balíček aktualizace je určen pouze pro Azure Stack integrovaných systémů. Nepoužívejte tento balíček aktualizace na Azure Stack Development Kit.

## <a name="build-reference"></a>Referenční informace o buildu

Číslo buildu aktualizace Azure Stack 1808 je **1.1808.0.97**.  

### <a name="new-features"></a>Nové funkce

Tato aktualizace obsahuje následující vylepšení pro Azure Stack.

<!--  2682594   | IS  --> 
- **Všechna Azure Stack prostředí teď používají formát časového pásma standard UTC (Coordinated Universal Time).**  Všechna data protokolu a související informace se nyní zobrazují ve formátu UTC. Pokud aktualizujete z předchozí verze, která nebyla nainstalovaná pomocí standardu UTC, vaše prostředí se aktualizuje tak, aby používalo UTC. 

<!-- 2437250  | IS  ASDK --> 
- **Managed Disks jsou podporovány.** Nyní můžete použít Managed Disks v Azure Stack virtuálních počítačích a Virtual Machine Scale Sets. Další informace najdete v tématu [Azure Stack Managed disks: rozdíly a požadavky](../../user/azure-stack-managed-disk-considerations.md).

<!-- 2563799  | IS  ASDK --> 
- **Azure monitor**. Podobně jako Azure Monitor v Azure Azure Monitor na Azure Stack poskytuje metriky infrastruktury a protokoly základní úrovně pro většinu služeb. Další informace najdete v tématu [Azure monitor v Azure Stack](../../user/azure-stack-metrics-azure-data.md).

<!-- 2487932| IS --> 
- **Připravte se na hostitele rozšíření**. Pomocí hostitele rozšíření můžete zvýšit zabezpečení Azure Stack snížením počtu požadovaných portů TCP/IP. Pomocí aktualizace 1808 můžete připravit, Azure Stack připraveni pro hostitele rozšíření. Další informace najdete v tématu [Příprava pro hostitele rozšíření pro Azure Stack](../azure-stack-extension-host-prepare.md).

<!-- IS --> 
- **Položky galerie pro Virtual Machine Scale Sets jsou teď integrované**. Položka galerie sady škálování virtuálního počítače je teď k dispozici na portálech uživatelů a správců, aniž by ji musela stahovat.  Pokud upgradujete na 1808, bude k dispozici po dokončení upgradu.  

<!-- IS, ASDK --> 
- **Škálování sady virtuálních počítačů**. Pomocí portálu můžete [škálovat sadu škálování virtuálního počítače](../azure-stack-compute-add-scalesets.md#scale-a-virtual-machine-scale-set) (VMSS).    

<!-- 2489570 | IS ASDK--> 
- **Podpora vlastních konfigurací zásad IPSec/IKE** pro [brány VPN v Azure Stack](../../user/azure-stack-vpn-gateway-about-vpn-gateways.md).

<!-- | IS ASDK--> 
- **Kubernetes položku Marketplace**. Clustery Kubernetes teď můžete nasadit pomocí [položky Kubernetes Marketplace](../azure-stack-solution-template-kubernetes-cluster-add.md). Uživatelé mohou vybrat položku Kubernetes a zadat několik parametrů pro nasazení clusteru Kubernetes do Azure Stack. Účelem šablon je zjednodušit uživatelům nastavení vývoje a testování Kubernetes nasazení v několika krocích.

<!-- | IS ASDK--> 
- **Blockchain šablony**. Nyní můžete spouštět [nasazení ethereem Consortium](../../user/azure-stack-ethereum.md) na Azure Stack. V [šablonách Azure Stack rychlé zprovoznění](https://github.com/Azure/AzureStack-QuickStart-Templates)můžete najít tři nové šablony. Umožňují uživateli nasadit a nakonfigurovat Ethereem síť konsorcia pro více členů s minimálními znalostmi Azure a Ethereem. Účelem šablon je zjednodušit uživatelům nastavení vývoje a testování blockchain nasazení v několika krocích.

<!-- | IS ASDK--> 
- **Profil verze rozhraní API 2017-03-09 – profil byl aktualizován na 2018-03-01 – Hybrid**. Profily rozhraní API určují poskytovatele prostředků Azure a verzi rozhraní API pro koncové body Azure REST. Další informace o profilech najdete v tématu [Správa profilů verzí rozhraní API v Azure Stack](../../user/azure-stack-version-profiles.md).

 ### <a name="fixed-issues"></a>Opravené problémy
<!-- IS ASDK--> 
- Opravili jsme problém s vytvořením skupiny dostupnosti na portálu, což vedlo k tomu, že sada obsahuje doménu selhání a aktualizuje doménu na 1. 

<!-- IS ASDK --> 
- Nastavení pro škálování virtuálních počítačů s měřítkem jsou teď dostupná na portálu.  

<!-- 2494144- IS, ASDK --> 
- Problém, který zabránil zobrazování některých velikostí virtuálních počítačů řady F-Series při výběru velikosti virtuálního počítače pro nasazení, je nyní vyřešen. 

<!-- IS, ASDK --> 
- Vylepšení výkonu při vytváření virtuálních počítačů a optimalizace využití základního úložiště.

- **Různé opravy** pro výkon, stabilitu, zabezpečení a operační systém, který je používán nástrojem Azure Stack.


### <a name="changes"></a>Změny
<!-- 1697698  | IS, ASDK --> 
- *Kurzy rychlý Start* na řídicím panelu User Portal teď odkazují na relevantní články v dokumentaci online Azure Stack.

<!-- 2515955   | IS ,ASDK--> 
- *Všechny služby* nahrazují *Další služby* na portálu Azure Stack správce a uživatelské portály. *Všechny služby* teď můžete použít jako alternativu k navigaci na portálech Azure Stack stejným způsobem jako na portálech Azure.

<!-- TBD | IS, ASDK --> 
- *+ Vytvořit prostředek* nahrazuje *+ Nový* ve Azure Stack portálech pro správu a uživatele.  Nyní můžete použít možnost *+ vytvořit prostředek* jako alternativu k navigaci na portálech Azure Stack stejným způsobem jako na portálech Azure.  

<!--  TBD | IS, ASDK --> 
- *Základní* : pro [vytváření virtuálních počítačů](../azure-stack-compute-add-scalesets.md) (VMSS) prostřednictvím portálu jsou vyřazené velikosti virtuálních počítačů. Pokud chcete vytvořit VMSS s touto velikostí, použijte PowerShell nebo šablonu.  

### <a name="common-vulnerabilities-and-exposures"></a>Běžné chyby zabezpečení a ohrožení

Tato aktualizace nainstaluje tyto aktualizace:  

- [CVE-2018-0952](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-0952)
- [CVE-2018-8200](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8200)
- [CVE-2018-8204](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8204)
- [CVE-2018-8253](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8253)
- [CVE-2018-8339](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8339)
- [CVE-2018-8340](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8340)
- [CVE-2018-8341](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8341)
- [CVE-2018-8343](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8343)
- [CVE-2018-8344](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8344)
- [CVE-2018-8345](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8345)
- [CVE-2018-8347](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8347)
- [CVE-2018-8348](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8348)
- [CVE-2018-8349](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8349)
- [CVE-2018-8394](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8394)
- [CVE-2018-8398](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8398)
- [CVE-2018-8401](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8401)
- [CVE-2018-8404](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8404)
- [CVE-2018-8405](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8405)
- [CVE-2018-8406](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8406)  

Další informace o těchto chybách zabezpečení získáte kliknutím na výše uvedené odkazy nebo v článku [4343887](https://support.microsoft.com/help/4343887)znalostní báze Microsoft Knowledge Base.

Tato aktualizace také obsahuje omezení rizik pro spekulativní problémy na straně služby, která se označuje jako chyba terminálu L1 (L1TF), popsané v [informačním zpravodaji zabezpečení společnosti Microsoft ADV180018](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180018).  

### <a name="prerequisites"></a>Požadavky

- Před instalací aktualizace Azure Stack 1808 nainstalujte aktualizaci Azure Stack 1807. 
- Před zahájením instalace této aktualizace spusťte rutinu [test-AzureStack](../azure-stack-diagnostic-test.md) s následujícími parametry, abyste ověřili stav svého Azure Stack a vyřešili jsme zjištěné provozní problémy, včetně všech upozornění a selhání. Zkontrolujte také aktivní výstrahy a vyřešte všechny akce, které vyžadují.  

  ```PowerShell
  Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
  ```   

### <a name="known-issues-with-the-update-process"></a>Známé problémy s procesem aktualizace

- Po spuštění rutiny [test-AzureStack](../azure-stack-diagnostic-test.md) po aktualizaci 1808 se zobrazí varovná zpráva z řadiče pro správu základní desky (BMC). Toto upozornění můžete klidně ignorovat.

<!-- 2468613 - IS --> 
- Během instalace této aktualizace se můžou zobrazit upozornění s *šablonou chyby nadpisu pro typ FaultType UserAccounts. chybí New.*  Tyto výstrahy můžete bezpečně ignorovat. Tyto výstrahy se po dokončení instalace této aktualizace automaticky zavřou.

<!-- 2489559 - IS --> 
- Nepokoušejte se během instalace této aktualizace vytvořit virtuální počítače. Další informace o správě aktualizací najdete v tématu [Správa aktualizací v Azure Stack Overview](../azure-stack-updates.md).

<!-- 2830461 - IS --> 
- V některých případech, kdy aktualizace vyžaduje pozornost, nemusí být vygenerována odpovídající výstraha. Přesný stav se stále projeví na portálu a nebude to mít vliv na.

### <a name="post-update-steps"></a>Kroky po aktualizaci
Po instalaci této aktualizace nainstalujte všechny příslušné opravy hotfix. Další informace najdete v následujících článcích znalostní báze a v našich [zásadách pro obsluhu](../azure-stack-servicing-policy.md). 
- [KB 4465859 Azure Stack hotfix Azure Stack hotfix 1.1808.2.104](https://support.microsoft.com/help/4465859/)

## <a name="known-issues-post-installation"></a>Známé problémy (po instalaci)

Níže jsou uvedené známé problémy po instalaci této verze buildu.

### <a name="portal"></a>Portál

- Technickou dokumentaci Azure Stack se zaměřuje na nejnovější verzi. Vzhledem k změnám portálu mezi verzemi, co vidíte při použití Azure Stack portály, se může lišit od toho, co vidíte v dokumentaci. 

<!-- TBD - IS ASDK --> 
- Na portálu se může zobrazit prázdný řídicí panel. Řídicí panel obnovíte tak, že kliknete na **Upravit řídicí panel**, kliknete pravým tlačítkem a vyberete **obnovit do výchozího stavu**.

<!-- 2930718 - IS ASDK --> 
- Když v portálu pro správu přistupujete k podrobnostem každého předplatného uživatele, po zavření okna a kliknutí na **Poslední** se nezobrazí název předplatného uživatele.

<!-- 3060156 - IS ASDK --> 
- Na portále správce i uživatelské portály klikněte na nastavení portálu a vyberte **Odstranit všechna nastavení a soukromé řídicí panely** nefungují podle očekávání. Zobrazí se oznámení o chybě. 

<!-- 2930799 - IS ASDK --> 
- Na portále správce i uživatele v části **všechny služby** jsou **plány ochrany Asset DDoS** nesprávně uvedené. Ve skutečnosti není k dispozici v Azure Stack. Pokud se pokusíte ho vytvořit, zobrazí se chyba oznamující, že portál nemohl vytvořit položku Marketplace. 

<!-- 2930820 - IS ASDK --> 
- Při hledání "Docker" na portále správce i v uživatelských portálech se položka nesprávně vrátí. Ve skutečnosti není k dispozici v Azure Stack. Pokud se pokusíte ho vytvořit, zobrazí se okno s indikací chyby. 

<!-- 2967387 IS, ASDK --> 
- Účet, který použijete pro přihlášení ke Správci Azure Stack nebo portálu User Portal, se zobrazí jako **neidentifikovaný uživatel**. K tomu dojde v případě, že účet nemá zadán buď *křestní* jméno, nebo *příjmení* . Pokud chcete tento problém obejít, upravte uživatelský účet tak, aby poskytoval buď jméno nebo příjmení. Musíte se odhlásit a pak znovu přihlásit na portál. 

<!--  2873083 - IS ASDK --> 
-  Když použijete portál k vytvoření sady škálování virtuálních počítačů (VMSS), rozevírací seznam *velikost instance* se při použití aplikace Internet Explorer nenačte správně. Pokud chcete tento problém obejít, použijte jiný prohlížeč a vytvořte VMSS pomocí portálu.  

<!-- 2931230 IS  ASDK --> 
- Plány, které se přidají k předplatnému uživatele jako doplněk, nejde odstranit, i když z předplatného uživatele odeberete plán. Plán zůstane, dokud nebudou odstraněny také odběry, které odkazují na plán doplňku. 

<!--2760466 IS  ASDK --> 
- Při instalaci nového prostředí Azure Stack, ve kterém je spuštěna tato verze, se nemusí zobrazit výstraha, která indikuje, že se *vyžaduje aktivace* . Předtím, než budete moct použít syndikaci Marketplace, se vyžaduje [Aktivace](../azure-stack-registration.md) .  

<!-- TBD - IS ASDK --> 
- Nelze použít dva typy předplatného pro správu, které byly představeny s verzí 1804. Typy předplatného jsou odběry **měření** a **předplatné spotřeby**. Tyto typy předplatného jsou viditelné v nových Azure Stack prostředí od verze 1804, ale ještě nejsou připravené k použití. Měli byste dál používat výchozí typ předplatného **poskytovatele** .

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


### <a name="compute"></a>Výpočetní prostředky

<!-- 3099544 IS, ASDK --> 
- Když vytváříte nový virtuální počítač pomocí portálu Azure Stack a vyberete velikost virtuálního počítače, zobrazí se sloupec USD/měsíc s **nedostupnou** zprávou. Tento sloupec by se neměl zobrazovat; zobrazení sloupce s cenami virtuálních počítačů není v Azure Stack podporováno.

<!-- 3090289 IS, ASDK --> 
- Po použití aktualizace 1808 se můžete setkat s následujícími problémy při nasazení virtuálních počítačů pomocí Managed Disks:

   1. Pokud se odběr vytvořil před aktualizací 1808, může nasazení virtuálního počítače pomocí Managed Disks selhat s interní chybovou zprávou. Pokud chcete chybu vyřešit, postupujte podle těchto kroků u každého předplatného:
      1. Na portálu tenanta klikněte na **předplatná** a vyhledejte předplatné. Klikněte na **poskytovatelé prostředků**, potom klikněte na **Microsoft. COMPUTE** a pak klikněte na **znovu registrovat**.
      2. V rámci stejného předplatného, přejít na **Access Control (IAM)** a ověřte, že je v seznamu uvedeno **Azure Stack spravovaný disk** .
   2. Pokud jste nakonfigurovali víceklientské prostředí, může se při nasazování virtuálních počítačů v rámci předplatného, které je přidružené k adresáři hostovaného uživatele, zdařit chyba a zobrazí se interní chybová zpráva. K vyřešení této chyby použijte následující postup:
      1. Použijte [opravu Hotfix 1808 Azure Stack](https://support.microsoft.com/help/4465859).
      2. Postupujte podle kroků v [tomto článku](../azure-stack-enable-multitenancy.md#register-azure-stack-hub-with-the-guest-directory) a změňte konfiguraci každého adresáře hostů.

<!-- 2869209 IS, ASDK --> 
- Při použití [rutiny **Add-AzsPlatformImage**](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0&preserve-view=true)musíte použít parametr **-OsUri** jako identifikátor URI účtu úložiště, kam se disk nahrává. Pokud použijete místní cestu k disku, rutina se nezdařila s následující chybou: *dlouhodobá operace se nezdařila se stavem selhalo*. 

<!--  2966665 IS, ASDK --> 
- Připojení datových disků SSD k virtuálním počítačům se spravovaným diskem (DS, DSv2, FS, Fs_V2) se nezdařilo s chybou:  *nepodařilo se aktualizovat disky pro VMName chybu virtuálního počítače: požadovanou operaci nelze provést, protože typ účtu úložiště Premium_LRS není podporován pro velikost virtuálního počítače Standard_DS/Ds_V2/fs/Fs_v2).*

   Pokud chcete tento problém obejít, použijte místo *Premium_LRS disků* *Standard_LRS* datové disky. Použití datových disků *Standard_LRS* nemění IOPS ani fakturační náklady. 

<!--  2795678 IS, ASDK --> 
- Když použijete portál k vytvoření virtuálních počítačů ve velikosti virtuálního počítače Premium (DS, Ds_v2, FS, FSv2), virtuální počítač se vytvoří v účtu úložiště úrovně Standard. Vytvoření v účtu úložiště úrovně Standard nemá vliv na funkce, IOPs ani fakturaci. 

   Upozornění, které říkáte, můžete bezpečně ignorovat: *rozhodli jste se použít standardní disk pro velikost, která podporuje prémiové disky. To může mít vliv na výkon operačního systému a nedoporučuje se. Zvažte místo toho použití Storage úrovně Premium (SSD).*

<!-- 2967447 - IS, ASDK --> 
- Prostředí pro vytváření VMSS (Virtual Machine Scale set) poskytuje možnost nasazení na bázi CentOS 7,2. Vzhledem k tomu, že tato image není k dispozici na Azure Stack, vyberte jiný operační systém pro vaše nasazení nebo použijte šablonu Azure Resource Manager určující jinou image CentOS, která byla stažena před nasazením z webu Marketplace pomocí operátoru.  

<!-- 2724873 - IS --> 
- Při použití rutin PowerShellu **Start-AzsScaleUnitNode** nebo  **stop-AzsScaleUnitNode** pro správu jednotek škálování se může stát, že první pokus o spuštění nebo zastavení jednotky škálování selže. Pokud rutina při prvním spuštění neprojde, spusťte rutinu podruhé. Druhý běh by měl úspěšně dokončit operaci. 

<!-- TBD - IS ASDK --> 
- Když vytváříte virtuální počítače na portálu Azure Stack User Portal, portál zobrazí nesprávný počet datových disků, které se můžou připojit k virtuálnímu počítači DS Series. Virtuální počítače řady DS můžou přijmout tolik datových disků jako konfigurace Azure.

<!-- TBD - IS ASDK --> 
- Pokud zřízení rozšíření na nasazení virtuálního počítače trvá příliš dlouho, uživatelé by měli povolit časový limit zřizování namísto pokusu o zastavení procesu navrácení nebo odstranění virtuálního počítače.  

<!-- 1662991 IS ASDK --> 
- Diagnostika virtuálního počítače se systémem Linux není v Azure Stack podporována. Když nasadíte virtuální počítač se systémem Linux s povoleným diagnostikou virtuálních počítačů, nasazení se nezdařilo. Nasazení se také nepovede, pokud povolíte základní metriky virtuálního počítače se systémem Linux prostřednictvím nastavení diagnostiky.  

<!-- 2724961- IS ASDK --> 
- Když zaregistrujete poskytovatele prostředků **Microsoft. Insight** v nastaveních předplatného a vytvoříte virtuální počítač s Windows s povoleným diagnostikou HOSTOVANÉHO operačního systému, graf procenta využití procesoru na stránce Přehled virtuálního počítače nebude moct zobrazovat data metrik.

   Pokud chcete pro virtuální počítač najít graf procenta využití procesoru, otevřete okno **metriky** a zobrazte všechny podporované metriky hostů virtuálních počítačů s Windows.



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


<!-- ### SQL and MySQL-->


### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK --> 
- Před vytvořením první funkce Azure v předplatném musí uživatelé zaregistrovat poskytovatele prostředků úložiště.

<!-- 2489178 - IS ASDK --> 
- Pro horizontální navýšení kapacity infrastruktury (pracovní procesy, správy, front-endové role) musíte použít PowerShell, jak je popsáno v poznámkách k verzi pro výpočetní prostředky.



### <a name="usage"></a>Využití  
<!-- TBD - IS ASDK --> 
- Data měřiče využití veřejných IP adres zobrazují stejnou hodnotu *EventDateTime* pro každý záznam místo razítka *timedate* , které ukazuje, kdy byl záznam vytvořen. V současné době nemůžete tato data použít k provedení přesného účtování využití veřejné IP adresy.


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>Stáhnout aktualizaci
Balíček aktualizací Azure Stack 1808 si můžete stáhnout [odsud.](https://aka.ms/azurestackupdatedownload)
  

## <a name="next-steps"></a>Další kroky
- Informace o zásadách obsluhy pro Azure Stack integrovaných systémech a o tom, co je potřeba udělat, abyste zachovali svůj systém v podporovaném stavu, najdete v tématu [zásady pro obsluhu Azure Stack](../azure-stack-servicing-policy.md).  
- Postup pro monitorování a obnovení aktualizací pomocí privilegovaného koncového bodu (PEP) najdete v tématu [monitorování aktualizací v Azure Stack pomocí privilegovaného koncového bodu](../azure-stack-monitor-update.md).  
- Přehled správy aktualizací v Azure Stack najdete v tématu [Správa aktualizací v Azure Stack přehledu](../azure-stack-updates.md).  
- Další informace o tom, jak použít aktualizace s Azure Stack, najdete v tématu věnovaném [použití aktualizací v Azure Stack](../azure-stack-apply-updates.md).  