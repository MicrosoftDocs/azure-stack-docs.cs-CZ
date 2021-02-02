---
title: Aktualizace 1802 Azure Stack | Microsoft Docs
description: Přečtěte si o tom, co je v aktualizaci 1802 pro Azure Stack integrovaných systémech, známých problémech a kde stáhnout aktualizaci.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: brenduns
ms.reviewer: justini
ROBOTS: NOINDEX
ms.openlocfilehash: b9334a3079a767f723240946af8091508204b8ae
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248101"
---
# <a name="azure-stack-1802-update"></a>Aktualizace Azure Stack 1802

*Platí pro: Azure Stack integrovaných systémů*

Tento článek popisuje vylepšení a opravy v balíčku aktualizace 1802, známé problémy této verze a místo, kam stáhnout aktualizaci. Známé problémy jsou rozdělené na problémy přímo související s procesem aktualizace a problémy se sestavením (po instalaci).

> [!IMPORTANT]        
> Tento balíček aktualizace je určen pouze pro Azure Stack integrovaných systémů. Nepoužívejte tento balíček aktualizace na Azure Stack Development Kit.

## <a name="build-reference"></a>Referenční informace o buildu    
Číslo buildu aktualizace Azure Stack 1802 je **20180302,1**.  


## <a name="before-you-begin"></a>Než začnete    
> [!IMPORTANT]    
> Nepokoušejte se během instalace této aktualizace vytvořit virtuální počítače. Další informace o správě aktualizací najdete v tématu [Správa aktualizací v Azure Stack Overview](../azure-stack-updates.md).


### <a name="prerequisites"></a>Požadavky
- Před instalací aktualizace Azure Stack 1802 nainstalujte aktualizaci Azure Stack 1712.    

- Před instalací aktualizace Azure Stack 1802 nainstalujte **AzS hotfix 1.0.180312.1-Build 20180222,2** . Tato oprava hotfix aktualizuje program Windows Defender a je k dispozici při stahování aktualizací pro Azure Stack.

  Chcete-li nainstalovat opravu hotfix, postupujte podle běžných postupů pro [instalaci aktualizací pro Azure Stack](../azure-stack-apply-updates.md). Název aktualizace se zobrazí jako **AzS hotfix 1.0.180312.1** a obsahuje následující soubory: 
    - PUPackageHotFix_20180222.2-1.exe
    - PUPackageHotFix_20180222.2-1. bin
    - Metadata.xml

  Po nahrání těchto souborů do účtu úložiště a kontejneru spusťte instalaci z dlaždice aktualizace na portálu pro správu. 
  
  Na rozdíl od aktualizací Azure Stack nemění instalace této aktualizace verze Azure Stack.  Chcete-li ověřit instalaci této aktualizace, zobrazte seznam **nainstalovaných aktualizací**.
 


### <a name="post-update-steps"></a>Kroky po aktualizaci
Po instalaci 1802 nainstalujte všechny příslušné opravy hotfix. Další informace najdete v následujících článcích znalostní báze a v našich [zásadách pro obsluhu](../azure-stack-servicing-policy.md). 
- Azure Stack hotfix **1.0.180302.4**. [KB 4131152 – existující Virtual Machine Scale Sets můžou být nepoužitelné]( https://support.microsoft.com/help/4131152) 

  Tato oprava také řeší problémy popsané v  [článku KB 4103348 – služba rozhraní API síťového adaptéru selže při pokusu o instalaci aktualizace služby Azure Stack](https://support.microsoft.com/help/4103348).


### <a name="new-features-and-fixes"></a>Nové funkce a opravy
Tato aktualizace obsahuje následující vylepšení a opravy pro Azure Stack.

- **Přidání podpory pro následující verze rozhraní API služby Azure Storage**:
    - 2017-04-17 
    - 2016-05-31 
    - 2015-12-11 
    - 2015-07-08 
    
    Další informace najdete v tématu [Azure Stack Storage: rozdíly a požadavky](../../user/azure-stack-acs-differences.md).

- **Podpora pro větší [objekty blob bloku](../../user/azure-stack-acs-differences.md)**:
    - Maximální povolená velikost bloku se zvyšuje ze 4 MB na 100 MB.
    - Maximální velikost objektu BLOB se zvyšuje z 195 GB na 4,75 TB.  

- V dlaždici poskytovatelé prostředků se nyní zobrazí **záloha infrastruktury** a jsou povolené výstrahy zálohování. Další informace o službě Infrastructure Backup najdete v tématu [zálohování a obnovení dat pro Azure Stack se službou Infrastructure Backup](../azure-stack-backup-infrastructure-backup.md).

- **Aktualizujte rutinu *test-AzureStack*** pro zlepšení diagnostiky pro úložiště. Další informace o této rutině najdete v tématu [ověření pro Azure Stack](../azure-stack-diagnostic-test.md).

- **Vylepšení Access Control na základě rolí (RBAC)** – nyní můžete použít RBAC k delegování oprávnění univerzálním skupinám uživatelů při nasazení Azure Stack pomocí AD FS. Další informace o RBAC najdete v tématu [Správa RBAC](../azure-stack-manage-permissions.md).

- **Podpora je přidána pro více domén selhání**.  Další informace najdete v tématu Vysoká dostupnost pro Azure Stack.

- **Podpora pro upgrady fyzické paměti** – nyní můžete po počátečním nasazení rozšířit kapacitu paměti Azure Stack integrovaného systému. Další informace najdete v tématu [Správa kapacity fyzické paměti pro Azure Stack](../azure-stack-manage-storage-physical-memory-capacity.md).

- **Různé opravy** pro výkon, stabilitu, zabezpečení a operační systém, který je používán nástrojem Azure Stack.

<!-- #### New features -->

<!-- #### Fixes -->

### <a name="known-issues-with-the-update-process"></a>Známé problémy s procesem aktualizace    
*Neexistují žádné známé problémy pro instalaci aktualizace Update 1802.*


### <a name="known-issues-post-installation"></a>Známé problémy (po instalaci)
Níže jsou uvedené známé problémy po instalaci pro Build **20180302,1** .

#### <a name="portal"></a>Portál
<!-- 2332636 - IS -->  
- Když použijete AD FS pro Azure Stack systém identit a aktualizujete tuto verzi Azure Stack, Výchozí vlastník výchozího předplatného poskytovatele se obnoví na integrovaného uživatele **CloudAdmin** .  
  Alternativní řešení: Pokud chcete tento problém vyřešit po instalaci této aktualizace, použijte krok 3 od [Automatizace triggeru ke konfiguraci vztahu důvěryhodnosti zprostředkovatele deklarací v Azure Stack](../azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-hub) postupu pro resetování vlastníka výchozího předplatného poskytovatele.   

- Možnost [otevření nové žádosti o podporu z rozevíracího seznamu](../azure-stack-manage-portals.md#quick-access-to-help-and-support) v portálu pro správu není dostupná. Místo toho použijte následující odkaz:     
    - V případě Azure Stack integrovaných systémů použijte https://aka.ms/newsupportrequest .

<!-- 2050709 --> 
- Na portálu pro správu není možné upravovat metriky úložiště pro Blob service, Table service nebo Služba front. Když přejdete do úložiště a pak vyberete dlaždici služby blob, Table nebo Queue, otevře se nové okno, ve kterém se zobrazí graf metrik pro danou službu. Pokud potom v horní části dlaždice grafu metriky vyberete možnost upravit, otevře se okno Upravit graf, ale nezobrazí se možnosti úprav metrik.

- Na portálu pro správu možná nebude možné zobrazit výpočetní prostředky ani prostředky úložiště. Příčinou tohoto problému je chyba při instalaci aktualizace, která způsobí, že aktualizace bude nesprávně nahlášena jako úspěšná. Pokud k tomuto problému dojde, obraťte se na službu zákaznické podpory Microsoftu, kde najdete pomoc.

- Na portálu se může zobrazit prázdný řídicí panel. Řídicí panel obnovíte tak, že vyberete ikonu ozubeného kolečka v pravém horním rohu portálu a pak vyberete **Obnovit výchozí nastavení**.

- Výsledkem odstranění předplatných uživatelů jsou osamocené prostředky. Jako alternativní řešení nejprve odstraňte prostředky uživatelů nebo celou skupinu prostředků a pak odstraňte odběry uživatelů.

- Oprávnění k vašemu předplatnému nemůžete zobrazit pomocí Azure Stackch portálů. Alternativním řešením je ověřit oprávnění pomocí PowerShellu.

- Na řídicím panelu portálu pro správu se na dlaždici aktualizace nepovede zobrazit informace o aktualizacích. Tento problém vyřešíte tak, že kliknete na dlaždici a aktualizujete ji.

-   Na portálu pro správu se může zobrazit kritická výstraha pro komponentu Microsoft. Update. admin. Název, popis a náprava výstrahy se zobrazí jako:  
    - *Chyba – šablona pro typ FaultType ResourceProviderTimeout chybí.*

    Tato výstraha se dá bezpečně ignorovat. 

<!-- 2253274 --> 
- Na portálech pro správu a uživatele se nepovede načíst okno nastavení pro podsítě virtuální sítě. Jako alternativní řešení pomocí PowerShellu a rutiny [Get-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig?view=azurermps-5.5.0&preserve-view=true) tyto informace můžete zobrazit a spravovat.

- Na portálu pro správu i na portálu User Portal se okno Přehled nepodařilo načíst, když vyberete okno Přehled pro účty úložiště, které byly vytvořeny pomocí starší verze rozhraní API (příklad: 2015-06-15). To zahrnuje účty systémových úložišť, jako je **updateadminaccount** , které se používají během opravy a aktualizace. 

  Alternativním řešením je použití PowerShellu ke spuštění skriptu **Start-ResourceSynchronization.ps1** k obnovení přístupu k podrobnostem účtu úložiště. [Skript je dostupný z GitHubu](https://github.com/Azure/AzureStack-Tools/tree/master/Support/scripts)a musí se spustit s přihlašovacími údaji správce služeb v privilegovaném koncovém bodu. 

- Nepodařilo se načíst okno **Service Health** . Když otevřete okno Service Health na portálu správce nebo na portálu User Portal, Azure Stack zobrazí chybu a nenačte informace. Toto chování je očekávané. I když můžete vybrat a otevřít Service Health, tato funkce ještě není k dispozici, ale bude implementována v budoucí verzi Azure Stack.


#### <a name="health-and-monitoring"></a>Stav a monitorování
<!-- 1264761 - IS ASDK -->  
- Můžou se zobrazit výstrahy pro součást *kontroleru stavu* s následujícími podrobnostmi:  

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

  Obě výstrahy se můžou ignorovat. V průběhu času se automaticky zavřou.  


#### <a name="marketplace"></a>Marketplace
- Uživatelé můžou procházet celou Marketplace bez předplatného a můžou zobrazit položky pro správu, jako jsou plány a nabídky. Tyto položky pro uživatele nejsou funkční.

#### <a name="compute"></a>Výpočetní prostředky
- Nastavení škálování pro sady škálování virtuálních počítačů nejsou na portálu k dispozici. Jako alternativní řešení můžete použít [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Kvůli rozdílům v verzi PowerShellu musíte použít `-Name` parametr místo `-VMScaleSetName` .

<!-- 2290877  --> 
- Nejde škálovat sadu virtuálních počítačů (VMSS), která se vytvořila při použití Azure Stack před verzí 1802. Důvodem je změna podpory pro používání skupin dostupnosti se službou Virtual Machine Scale Sets. Tato podpora se přidala s verzí 1802.  Když se pokusíte přidat další instance pro škálování VMSS, který byl vytvořen před tím, než je tato podpora přidána, akce se nezdaří se *stavem zřizování zprávy se nezdařilo*. 

  Tento problém je vyřešený ve verzi 1803. Pokud chcete tento problém vyřešit pro verzi 1802, nainstalujte Azure Stack hotfix **1.0.180302.4**. Další informace najdete v článku [KB 4131152: existující Virtual Machine Scale Sets můžou být nepoužitelná](https://support.microsoft.com/help/4131152). 

- Azure Stack podporuje jenom pevné typy VHD. Některé obrázky, které nabízí web Marketplace na Azure Stack používají dynamické virtuální pevné disky, ale ty se odebraly. Změna velikosti virtuálního počítače (VM) s připojeným dynamickým diskem opustí virtuální počítač ve stavu selhání.

  Pokud chcete tento problém zmírnit, odstraňte virtuální počítač, aniž byste odstranili disk virtuálního pevného disku (VHD) v účtu úložiště. Pak převeďte VHD z dynamického disku na pevný disk a pak znovu vytvořte virtuální počítač.

- Když na portálu vytvoříte skupinu dostupnosti tak, že kliknete na **Nová**  >    >  **Skupina dostupnosti** služby COMPUTE, můžete vytvořit jenom skupinu dostupnosti s doménou selhání a aktualizovat doménu na 1. Alternativním řešením při vytváření nového virtuálního počítače je vytvoření skupiny dostupnosti pomocí PowerShellu, rozhraní příkazového řádku nebo v rámci portálu.

- Když vytváříte virtuální počítače na portálu Azure Stack User Portal, portál zobrazí nesprávný počet datových disků, které se můžou připojit k virtuálnímu počítači D Series. Všechny podporované virtuální počítače řady D Series můžou pojmout tolik datových disků jako konfigurace Azure.

- Když se nepodaří vytvořit bitovou kopii virtuálního počítače, může být neúspěšná položka, kterou nemůžete odstranit, přidat do okna image virtuálních počítačů.

  Jako alternativní řešení vytvořte novou image virtuálního počítače se zástupným virtuálním pevným diskem, který se dá vytvořit pomocí technologie Hyper-V (New-VHD-Path C:\dummy.VHD-fixed-SizeBytes 1 GB). Tento proces by měl vyřešit problém, který brání odstranění položky, která selhala. Po vytvoření fiktivní image 15 minut ji můžete úspěšně odstranit.

  Pak se můžete pokusit znovu stáhnout image virtuálního počítače, která se dřív nezdařila.

-  Pokud zřízení rozšíření na nasazení virtuálního počítače trvá příliš dlouho, uživatelé by měli povolit časový limit zřizování namísto pokusu o zastavení procesu navrácení nebo odstranění virtuálního počítače.  

<!-- 1662991 --> 
- Diagnostika virtuálního počítače se systémem Linux není v Azure Stack podporována. Když nasadíte virtuální počítač se systémem Linux s povoleným diagnostikou virtuálních počítačů, nasazení se nezdařilo. Nasazení se také nepovede, pokud povolíte základní metriky virtuálního počítače se systémem Linux prostřednictvím nastavení diagnostiky.  




#### <a name="networking"></a>Sítě
- Po vytvoření virtuálního počítače a jeho přidružení k veřejné IP adrese nemůžete zrušit přidružení tohoto virtuálního počítače k této IP adrese. Zdá se, že zrušení přidružení funguje, ale dříve přiřazená veřejná IP adresa zůstane přidružená k původnímu virtuálnímu počítači.

  V současné době musíte pro nové virtuální počítače, které vytvoříte, použít jenom nové veřejné IP adresy.

  K tomuto chování dochází i v případě, že IP adresu znovu přiřadíte novému virtuálnímu počítači (obvykle se označuje jako *swap VIP*). U všech budoucích pokusů o připojení přes tuto IP adresu dojde k připojení k původně přidruženému virtuálnímu počítači, a ne k novému.

- Interní vyrovnávání zatížení (interního nástroje) nesprávně zpracovává adresy MAC pro back-endové virtuální počítače, což způsobuje, že interního nástroje při použití instancí Linux v Back-End síti dojde k přerušení.  INTERNÍHO nástroje funguje s instancemi systému Windows v Back-End síti.

-   Funkce předávání IP adres je na portálu viditelná, ale povolení předávání IP nemá žádný vliv. Tato funkce ještě není podporovaná.

- Azure Stack podporuje jednu *bránu místní sítě* na IP adresu. To platí pro všechna předplatná tenanta. Po vytvoření prvního připojení brány místní sítě se budou zablokované následné pokusy o vytvoření prostředku brány místní sítě se stejnou IP adresou.

- Na Virtual Network, která byla vytvořena pomocí *automatického* nastavení serveru DNS, se změna na vlastní server DNS nezdařila. Aktualizovaná nastavení nejsou vložena do virtuálních počítačů v této virtuální síti.

- Azure Stack nepodporuje přidání dalších síťových rozhraní do instance virtuálního počítače po nasazení virtuálního počítače. Pokud virtuální počítač vyžaduje více než jedno síťové rozhraní, musí být definované v době nasazení.

<!-- 2096388 --> 
- Pomocí portálu pro správu nelze aktualizovat pravidla pro skupinu zabezpečení sítě. 

    Alternativní řešení pro App Service: Pokud potřebujete vzdálenou plochu k instancím kontroleru, upravíte pravidla zabezpečení v rámci skupin zabezpečení sítě pomocí PowerShellu.  Níže jsou uvedeny příklady *Povolení* a obnovení konfigurace pro *odepření*:  
    
    - *Povolil*
 
      ```powershell    
      Login-AzureRMAccount -EnvironmentName AzureStackAdmin
      
      $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"
      
      $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"
      
      ##This doesn�t work. Need to set properties again even in case of edit
      
      #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  
      
      Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
        -Name $RuleConfig_Inbound_Rdp_3389.Name `
        -Description "Inbound_Rdp_3389" `
        -Access Allow `
        -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
        -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
        -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
        -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
        -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
        -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
        -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange
      
      # Commit the changes back to NSG
      Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
      ```

    - *Odmítnout*

        ```powershell
        
        Login-AzureRMAccount -EnvironmentName AzureStackAdmin
        
        $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"
        
        $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"
        
        ##This doesn�t work. Need to set properties again even in case of edit
    
        #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  
    
        Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
          -Name $RuleConfig_Inbound_Rdp_3389.Name `
          -Description "Inbound_Rdp_3389" `
          -Access Deny `
          -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
          -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
          -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
          -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
          -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
          -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
          -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange
          
        # Commit the changes back to NSG
        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg 
        ```





#### <a name="sql-and-mysql"></a>SQL a MySQL
 - Než budete pokračovat, přečtěte si důležité informace v části, [než začnete](#before-you-begin) na začátku těchto poznámek k verzi.
- Může trvat až jednu hodinu, než uživatelé můžou vytvořit databáze v novém nasazení SQL nebo MySQL.

- Pouze poskytovatel prostředků je podporován k vytváření položek na serverech, které jsou hostiteli SQL nebo MySQL. Položky vytvořené na hostitelském serveru, které nejsou vytvořené poskytovatelem prostředků, můžou vést k neshodě stavu.  

<!-- IS, ASDK --> 
- Speciální znaky, včetně mezer a teček, se v názvu **rodiny** při vytváření SKU pro poskytovatele prostředků SQL a MySQL nepodporují.

> [!NOTE]  
> Po aktualizaci na Azure Stack 1802 můžete nadále používat poskytovatele prostředků SQL a MySQL, které jste předtím nasadili.  Pokud bude k dispozici nová verze, doporučujeme aktualizovat SQL a MySQL. Podobně jako u Azure Stack použijte postupně aktualizace pro poskytovatele prostředků SQL a MySQL.  Pokud například používáte verzi 1710, nejprve použijte verzi 1711, potom 1712 a pak aktualizujte na 1802.      
>   
> Instalace aktualizace 1802 nemá vliv na aktuální použití poskytovatelů prostředků SQL nebo MySQL vašimi uživateli.
> Bez ohledu na verzi poskytovatelů prostředků, které použijete, se data vašich uživatelů v jejich databázích nekontaktují a zůstávají dostupná.    


#### <a name="app-service"></a>App Service
- Před vytvořením první funkce Azure v předplatném musí uživatelé zaregistrovat poskytovatele prostředků úložiště.

- Pro horizontální navýšení kapacity infrastruktury (pracovní procesy, správy, front-endové role) musíte použít PowerShell, jak je popsáno v poznámkách k verzi pro výpočetní prostředky.

<!-- #### Identity -->

#### <a name="downloading-azure-stack-tools-from-github"></a>Stahování nástrojů Azure Stack z GitHubu
- Při použití rutiny *Invoke-WebRequest* prostředí PowerShell ke stažení nástrojů Azure Stack z GitHubu se zobrazí chyba:     
    -  *Invoke-WebRequest: požadavek byl zrušen: nelze vytvořit zabezpečený kanál SSL/TLS.*     

  K této chybě dochází z důvodu nedávných podpory GitHubu v rámci kryptografických standardů Tlsv1 a Tlsv 1.1 (výchozí nastavení pro PowerShell). Další informace najdete v tématu [Upozornění na odstranění slabých kryptografických standardů](https://githubengineering.com/crypto-removal-notice/).

  Pokud chcete tento problém vyřešit, přidejte `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` do horní části skriptu, aby konzole PowerShellu při stahování z úložišť GitHub vynutila použití tlsv 1.2.


## <a name="download-the-update"></a>Stáhnout aktualizaci
Balíček aktualizací Azure Stack 1802 si můžete stáhnout [odsud.](https://aka.ms/azurestackupdatedownload)


## <a name="more-information"></a>Další informace
Společnost Microsoft poskytuje způsob, jak monitorovat a obnovovat aktualizace pomocí privilegovaného koncového bodu (PEP) nainstalovaného s aktualizací Update 1710.

- Další informace najdete v tématu [monitorování aktualizací v Azure Stack pomocí dokumentace k privilegovanému koncovému bodu](https://docs.microsoft.com/azure/azure-stack/azure-stack-monitor-update).

## <a name="see-also"></a>Viz také

- Přehled správy aktualizací v Azure Stack najdete v tématu [Správa aktualizací v Azure Stack přehledu](../azure-stack-updates.md).
- Další informace o tom, jak použít aktualizace s Azure Stack, najdete v tématu věnovaném [použití aktualizací v Azure Stack](../azure-stack-apply-updates.md).