---
title: Aktualizace 1803 Azure Stack | Microsoft Docs
description: Přečtěte si o tom, co je v aktualizaci 1803 pro Azure Stack integrovaných systémech, známých problémech a kde stáhnout aktualizaci.
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
ms.date: 07/11/2018
ms.author: brenduns
ms.reviewer: justini
ROBOTS: NOINDEX
ms.openlocfilehash: 6f15453ce20988929728a9d326ab5f7bf901beca
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248086"
---
# <a name="azure-stack-1803-update"></a>Aktualizace Azure Stack 1803

*Platí pro: Azure Stack integrovaných systémů*

Tento článek popisuje vylepšení a opravy v balíčku aktualizace 1803, známé problémy této verze a místo, kam stáhnout aktualizaci. Známé problémy jsou rozdělené na problémy přímo související s procesem aktualizace a problémy se sestavením (po instalaci).

> [!IMPORTANT]        
> Tento balíček aktualizace je určen pouze pro Azure Stack integrovaných systémů. Nepoužívejte tento balíček aktualizace na Azure Stack Development Kit.

## <a name="build-reference"></a>Referenční informace o buildu    
Číslo buildu aktualizace Azure Stack 1803 je **20180329,1**.


## <a name="before-you-begin"></a>Než začnete    
> [!IMPORTANT]    
> Nepokoušejte se během instalace této aktualizace vytvořit virtuální počítače. Další informace o správě aktualizací najdete v tématu [Správa aktualizací v Azure Stack Overview](../azure-stack-updates.md).


### <a name="prerequisites"></a>Požadavky
- Před instalací aktualizace Azure Stack 1803 nainstalujte aktualizaci Azure Stack 1802.   

- Před instalací aktualizace Azure Stack 1803 nainstalujte **AzS hotfix 1.0.180312.1-Build 20180222,2** . Tato oprava hotfix aktualizuje program Windows Defender a je k dispozici při stahování aktualizací pro Azure Stack.

  Chcete-li nainstalovat opravu hotfix, postupujte podle běžných postupů pro [instalaci aktualizací pro Azure Stack](../azure-stack-apply-updates.md). Název aktualizace se zobrazí jako **AzS hotfix 1.0.180312.1** a obsahuje následující soubory: 
    - PUPackageHotFix_20180222.2-1.exe
    - PUPackageHotFix_20180222.2-1. bin
    - Metadata.xml

  Po nahrání těchto souborů do účtu úložiště a kontejneru spusťte instalaci z dlaždice aktualizace na portálu pro správu. 
  
  Na rozdíl od aktualizací Azure Stack nemění instalace této aktualizace verze Azure Stack. Chcete-li ověřit instalaci této aktualizace, zobrazte seznam **nainstalovaných aktualizací**.



### <a name="new-features"></a>Nové funkce 
Tato aktualizace obsahuje následující vylepšení a opravy pro Azure Stack.

- **Aktualizace Azure Stack tajných** kódů – (účty a certifikáty). Další informace o správě tajných klíčů najdete v tématu [otočení tajných kódů v Azure Stack](../azure-stack-rotate-secrets.md). 

<!-- 1914853 --> 
- **Automatické přesměrování na https** při použití protokolu HTTP pro přístup k portálům pro správu a uživatele. Toto vylepšení bylo provedeno na základě zpětné vazby na webu [UserVoice](https://feedback.azure.com/forums/344565-azure-stack/suggestions/32205385-it-would-be-great-if-there-was-a-automatic-redirec) pro Azure Stack. 

<!-- 2202621  --> 
- **Přístup k Marketplace** : teď můžete otevřít web Azure Stack Marketplace pomocí možnosti [+ Nová](https://ms.portal.azure.com/#create/hub) v rámci portálů pro správu a uživatele stejným způsobem jako na portálech Azure.
 
<!-- 2202621 --> 
- **Azure monitor** -Azure Stack přidá [Azure monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) na portály pro správu a uživatele. To zahrnuje nové Průzkumníka pro metriky a protokoly aktivit. Pro přístup k tomuto Azure Monitor z externích sítí musí být port **13012** otevřený v konfiguracích brány firewall. Další informace o portech vyžadovaných nástrojem Azure Stack najdete v tématu [Azure Stack Integration Datacenter – publikování koncových bodů](../azure-stack-integrate-endpoints.md).

   V rámci této změny se teď v části **Další služby** zobrazují *protokoly auditu* jako *protokoly aktivit*. Tato funkce je teď konzistentní s Azure Portal. 

<!-- 1664791 --> 
- **Řídké soubory** – když přidáte nový obrázek do Azure Stack nebo přidáte obrázek prostřednictvím syndikace webu Marketplace, obrázek se převede na zhuštěný soubor. Obrázky přidané před použitím Azure Stack verze 1803 nelze převést. Místo toho je nutné pomocí syndikace na webu Marketplace znovu odeslat tyto image, abyste mohli tuto funkci využít. 
 
   Řídké soubory jsou efektivním formátem, který slouží ke snížení využití úložného prostoru a zlepšení vstupně-výstupních operací. ? Další informace najdete v tématu [fsutil sparse](https://docs.microsoft.com/windows-server/administration/windows-commands/fsutil-sparse) pro Windows Server. 

### <a name="fixed-issues"></a>Opravené problémy

<!-- 1739988 --> 
- Interní vyrovnávání zatížení (interního nástroje) teď správně zpracovává adresy MAC pro back-endové virtuální počítače, což způsobí, že interního nástroje při použití instancí Linux v back-endové síti vynechá pakety do back-endové sítě. INTERNÍHO nástroje funguje v back-endové síti s instancemi systému Windows správně. 

<!-- 1805496 --> 
- Problém, kdy se připojení VPN mezi Azure Stack odpojí z důvodu Azure Stack použití jiných nastavení pro zásady IKE než Azure. Hodnoty pro SALifetime (Time) a SALiftetime (Byte) nejsou kompatibilní s Azure a v 1803 se změnily tak, aby odpovídaly nastavení Azure. Hodnota pro SALifetime (sekundy) před 1803 byla 14 400 a nyní se změní na 27 000 v 1803. Hodnota pro SALifetime (bajty) před 1803 byla 819 200 a v 1803 se změnily hodnoty 33 553 408.

<!-- 2209262 --> 
- Problém s protokolem IP, ve kterém se na portálu dříve zobrazovala připojení VPN. povolení nebo přepnutí předávání IP adres ale nemá žádný vliv. Tato funkce je ve výchozím nastavení zapnutá a možnost změnit ji ještě není podporovaná.  Ovládací prvek byl odebrán z portálu. 

<!-- 1766332 --> 
- Azure Stack nepodporuje brány VPN založené na zásadách, a to i v případě, že se na portálu objeví možnost.  Možnost byla odebrána z portálu. 

<!-- 1868283 --> 
- Azure Stack nyní zabrání změně velikosti virtuálního počítače, který je vytvořen s dynamickými disky. 

<!-- 1756324 --> 
- Data o využití pro virtuální počítače se teď oddělují v hodinových intervalech. To je konzistentní s Azure. 

<!--  2253274 --> 
- Problém, ve kterém se v portálech pro správu a uživatele nedokáže načíst okno nastavení pro podsítě virtuální sítě. Jako alternativní řešení pomocí PowerShellu a rutiny [Get-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig?view=azurermps-5.5.0&preserve-view=true) tyto informace můžete zobrazit a spravovat.

- Když vytvoříte virtuální počítač, zpráva při výběru velikosti pro velikost virtuálního počítače už nebude *zobrazovat ceny* .

- Různé opravy pro výkon, stabilitu, zabezpečení a operační systém, který je používán nástrojem Azure Stack.


### <a name="changes"></a>Změny
- Způsob, jak změnit stav nově vytvořené nabídky z *privátní* na *veřejnou* nebo *vyřazenou z provozu* , se změnila. Další informace najdete v tématu [Vytvoření nabídky](../azure-stack-create-offer.md).


### <a name="known-issues-with-the-update-process"></a>Známé problémy s procesem aktualizace    
<!-- 2328416 --> Během instalace aktualizace 1803 může docházet k výpadkům služby BLOB Service a interním službám, které používají službu BLOB Service. To zahrnuje některé operace virtuálních počítačů. Tento čas může způsobit selhání operací klienta nebo výstrahy ze služeb, které mají přístup k datům. Tento problém se vyřeší při dokončení instalace aktualizace. 



### <a name="post-update-steps"></a>Kroky po aktualizaci
- Po instalaci 1803 nainstalujte všechny příslušné opravy hotfix. Další informace najdete v následujících článcích znalostní báze a v našich [zásadách pro obsluhu](../azure-stack-servicing-policy.md).

  - [KB 4344115 – Azure Stack hotfix 1.0.180427.15](https://support.microsoft.com/help/4344115).

- Po instalaci této aktualizace zkontrolujte konfiguraci brány firewall a zajistěte, aby byly [potřebné porty](../azure-stack-integrate-endpoints.md) otevřené. Tato aktualizace například zavádí *Azure monitor* , která zahrnuje změnu protokolů auditu do protokolů aktivit. V této změně se nyní používá port 13012 a musí být také otevřen.  


### <a name="known-issues-post-installation"></a>Známé problémy (po instalaci)
Níže jsou uvedené známé problémy po instalaci pro Build  **20180323,2**.

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

- Na portálu pro správu se může zobrazit kritická výstraha pro komponentu *Microsoft. Update. admin* . Název, popis a náprava výstrahy se zobrazí jako:  
    - *Chyba – šablona pro typ FaultType ResourceProviderTimeout chybí.*

  Tato výstraha se dá bezpečně ignorovat. 


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



- Azure Stack podporuje jednu *bránu místní sítě* na IP adresu. To platí pro všechna předplatná tenanta. Po vytvoření prvního připojení brány místní sítě se budou zablokované následné pokusy o vytvoření prostředku brány místní sítě se stejnou IP adresou.

- Na Virtual Network, která byla vytvořena pomocí *automatického* nastavení serveru DNS, se změna na vlastní server DNS nezdařila. Aktualizovaná nastavení nejsou vložena do virtuálních počítačů v této virtuální síti.

- Azure Stack nepodporuje přidání dalších síťových rozhraní do instance virtuálního počítače po nasazení virtuálního počítače. Pokud virtuální počítač vyžaduje více než jedno síťové rozhraní, musí být definované v době nasazení.

<!-- 2096388 --> 
- Pomocí portálu pro správu nelze aktualizovat pravidla pro skupinu zabezpečení sítě. 

    Alternativní řešení pro App Service: Pokud potřebujete vzdálenou plochu k instancím kontroleru, upravíte pravidla zabezpečení v rámci skupin zabezpečení sítě pomocí PowerShellu.  Níže jsou uvedeny příklady *Povolení* a obnovení konfigurace pro *odepření*:  
    
    - *Povolil*
 
      ```powershell    
      Add-AzureRmAccount -EnvironmentName AzureStackAdmin
      
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
        
        Add-AzureRmAccount -EnvironmentName AzureStackAdmin
        
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
> Po aktualizaci na Azure Stack 1803 můžete nadále používat poskytovatele prostředků SQL a MySQL, které jste předtím nasadili.  Pokud bude k dispozici nová verze, doporučujeme aktualizovat SQL a MySQL. Podobně jako u Azure Stack použijte postupně aktualizace pro poskytovatele prostředků SQL a MySQL.  Pokud například používáte verzi 1711, nejprve použijte verzi 1712, potom 1802 a pak aktualizujte na 1803.      
>   
> Instalace aktualizace 1803 nemá vliv na aktuální použití poskytovatelů prostředků SQL nebo MySQL vašimi uživateli.
> Bez ohledu na verzi poskytovatelů prostředků, které použijete, se data vašich uživatelů v jejich databázích nekontaktují a zůstávají dostupná.    



#### <a name="app-service"></a>App Service
- Před vytvořením první funkce Azure v předplatném musí uživatelé zaregistrovat poskytovatele prostředků úložiště.

- Pro horizontální navýšení kapacity infrastruktury (pracovní procesy, správy, front-endové role) musíte použít PowerShell, jak je popsáno v poznámkách k verzi pro výpočetní prostředky.


#### <a name="usage"></a>Využití  
- Data měřiče využití veřejných IP adres zobrazují stejnou hodnotu *EventDateTime* pro každý záznam místo razítka *timedate* , které ukazuje, kdy byl záznam vytvořen. V současné době můžete tato data použít k provedení přesného účtování využití veřejné IP adresy.

<!-- #### Identity -->

#### <a name="downloading-azure-stack-tools-from-github"></a>Stahování nástrojů Azure Stack z GitHubu
- Při použití rutiny *Invoke-WebRequest* prostředí PowerShell ke stažení nástrojů Azure Stack z GitHubu se zobrazí chyba:     
    -  *Invoke-WebRequest: požadavek byl zrušen: nelze vytvořit zabezpečený kanál SSL/TLS.*     

  K této chybě dochází z důvodu nedávných podpory GitHubu v rámci kryptografických standardů Tlsv1 a Tlsv 1.1 (výchozí nastavení pro PowerShell). Další informace najdete v tématu [Upozornění na odstranění slabých kryptografických standardů](https://githubengineering.com/crypto-removal-notice/).

  Pokud chcete tento problém vyřešit, přidejte `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` do horní části skriptu, aby konzole PowerShellu při stahování z úložišť GitHub vynutila použití tlsv 1.2.


## <a name="download-the-update"></a>Stáhnout aktualizaci
Balíček aktualizací Azure Stack 1803 si můžete stáhnout [odsud.](https://aka.ms/azurestackupdatedownload)


## <a name="see-also"></a>Viz také
- Postup pro monitorování a obnovení aktualizací pomocí privilegovaného koncového bodu (PEP) najdete v tématu [monitorování aktualizací v Azure Stack pomocí privilegovaného koncového bodu](../azure-stack-monitor-update.md).
- Přehled správy aktualizací v Azure Stack najdete v tématu [Správa aktualizací v Azure Stack přehledu](../azure-stack-updates.md).
- Další informace o tom, jak použít aktualizace s Azure Stack, najdete v tématu věnovaném [použití aktualizací v Azure Stack](../azure-stack-apply-updates.md).