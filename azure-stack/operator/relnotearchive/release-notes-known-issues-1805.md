---
title: Aktualizace 1805 Azure Stack | Microsoft Docs
description: Přečtěte si, co je nového v aktualizaci 1805 pro Azure Stack integrovaných systémů, včetně známých problémů a místa, kde si můžete aktualizaci stáhnout.
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
ms.openlocfilehash: 16e11fcbc46065f512cc6b887b9c7159f6b0ac65
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248050"
---
# <a name="azure-stack-1805-update"></a>Aktualizace Azure Stack 1805

*Platí pro: Azure Stack integrovaných systémů*

Tento článek popisuje vylepšení a opravy v balíčku aktualizace 1805, známé problémy s touto verzí a místo stažení aktualizace. Známé problémy jsou rozdělené na problémy přímo související s procesem aktualizace a problémy se sestavením (po instalaci).

> [!IMPORTANT]        
> Tento balíček aktualizace je určen pouze pro Azure Stack integrovaných systémů. Nepoužívejte tento balíček aktualizace na Azure Stack Development Kit.

## <a name="build-reference"></a>Referenční informace o buildu    
Číslo buildu aktualizace Azure Stack 1805 je **1.1805.1.47**.  

> [!TIP]  
> Na základě zpětné vazby od zákazníků je k dispozici aktualizace schématu verze používaného pro Microsoft Azure Stack.  Od této aktualizace 1805 nové schéma lépe představuje aktuální verzi cloudu.  
> 
> Schéma verze je teď *verze. YearYearMonthMonth. podverze. BuildNumber* , kde druhá a třetí sada indikuje verzi a verzi. Například 1805,1 představuje verzi *1805 verze* .  


### <a name="new-features"></a>Nové funkce
Tato aktualizace obsahuje následující vylepšení pro Azure Stack.
<!-- 2297790 - IS, ASDK --> 
- **Azure Stack nyní zahrnuje klienta *SYSLOG*** jako *funkci verze Preview*. Tento klient umožňuje předávání protokolů auditu a zabezpečení souvisejících s infrastrukturou Azure Stack na server syslog nebo na software pro správu událostí a zabezpečení SIEM (Security Information and Event Management), který je externě Azure Stack. V současné době klient syslog podporuje jenom neověřená připojení UDP přes výchozí port 514. Datová část každé zprávy syslog je formátována ve formátu Common Event Format (CEF). 

  Chcete-li nakonfigurovat klienta syslog, použijte rutinu **set-SyslogServer** , která je vystavena v privilegovaném koncovém bodu. 

  V této verzi Preview se můžou zobrazit následující tři výstrahy. Když Azure Stack prezentují, obsahují tyto výstrahy *popisy* a pokyny k *nápravě* . 
  - TITLE: integrita kódu vypnuto  
  - TITLE: integrita kódu v režimu auditování 
  - Název: uživatelský účet se vytvořil.

  I když je tato funkce ve verzi Preview, neměla by se spoléhat na provozní prostředí.   




### <a name="fixed-issues"></a>Opravené problémy

<!-- # - applicability -->
- Opravili jsme problém, který zablokoval [otevření nové žádosti o podporu z rozevírací](../azure-stack-manage-portals.md#quick-access-to-help-and-support) nabídky v rámci portálu pro správu. Tato možnost teď funguje tak, jak má. 

- **Různé opravy** pro výkon, stabilitu, zabezpečení a operační systém, který je používán nástrojem Azure Stack.


<!-- # Additional releases timed with this update    -->



## <a name="before-you-begin"></a>Než začnete    

### <a name="prerequisites"></a>Požadavky
- Před instalací aktualizace Azure Stack 1805 nainstalujte aktualizaci Azure Stack 1804.  
- Nainstalujte nejnovější dostupnou aktualizaci nebo opravu hotfix pro verzi 1804.   
- Než začnete s instalací aktualizace 1805, spusťte rutinu [test-AzureStack](../azure-stack-diagnostic-test.md) a ověřte stav vašich Azure Stack a vyřešte případné zjištěné provozní problémy. Zkontrolujte také aktivní výstrahy a vyřešte všechny akce, které vyžadují. 

### <a name="known-issues-with-the-update-process"></a>Známé problémy s procesem aktualizace   
- Během instalace aktualizace 1805 se může zobrazit upozornění s *šablonou chyby nadpisu pro typ FaultType UserAccounts. chybí New.*  Tyto výstrahy můžete bezpečně ignorovat. Tyto výstrahy se po dokončení aktualizace na 1805 automaticky zavřou.   

<!-- 2489559 - IS --> 
- Nepokoušejte se během instalace této aktualizace vytvořit virtuální počítače. Další informace o správě aktualizací najdete v tématu [Správa aktualizací v Azure Stack Overview](../azure-stack-updates.md).


### <a name="post-update-steps"></a>Kroky po aktualizaci
Po instalaci 1805 nainstalujte všechny příslušné opravy hotfix. Další informace najdete v následujících článcích znalostní báze a v našich [zásadách pro obsluhu](../azure-stack-servicing-policy.md).  
 - [KB 4344102 – Azure Stack hotfix 1.1805.7.57](https://support.microsoft.com/help/4344102).


## <a name="known-issues-post-installation"></a>Známé problémy (po instalaci)
Níže jsou uvedené známé problémy po instalaci této verze buildu.

### <a name="portal"></a>Portál  

- Technickou dokumentaci Azure Stack se zaměřuje na nejnovější verzi. Vzhledem k změnám portálu mezi verzemi, co vidíte při použití Azure Stack portály, se může lišit od toho, co vidíte v dokumentaci. 

<!-- 2931230 IS  ASDK --> 
- Plány, které se přidají k předplatnému uživatele jako doplněk, nejde odstranit, i když z předplatného uživatele odeberete plán. Plán zůstane, dokud nebudou odstraněny také odběry, které odkazují na plán doplňku. 

<!-- TBD - IS ASDK --> 
- Aktualizace ovladačů nemůžete použít pomocí balíčku rozšíření OEM s touto verzí Azure Stack.  Pro tento problém neexistuje žádné alternativní řešení.

<!-- 2551834 - IS, ASDK --> 
- Když vyberete **Přehled** pro účet úložiště buď na portálu správce, nebo na portálu User Portal, informace z podokna *základy* se nezobrazí.  V podokně základy se zobrazí informace o účtu, jako je jeho *Skupina prostředků*, *umístění* a *ID předplatného*.  K dispozici jsou další možnosti pro přehled, jako jsou *služby* a *monitorování*, a také možnosti pro *otevření v Průzkumníkovi* nebo *odstranění účtu úložiště*. 

  Pokud chcete zobrazit nedostupné informace, použijte rutinu PowerShellu [Get-azureRMstorageaccount](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccount?view=azurermps-6.2.0&preserve-view=true) . 

<!-- 2551834 - IS, ASDK --> 
- Když vyberete **značky** pro účet úložiště buď na portálu pro správu, nebo na portálu User Portal, informace se nepovede načíst a nezobrazují se.  

  Pokud chcete zobrazit nedostupné informace, použijte rutinu PowerShellu [Get-AzureRmTag](https://docs.microsoft.com/powershell/module/azurerm.tags/get-azurermtag?view=azurermps-6.2.0&preserve-view=true) .


<!-- 2332636 - IS -->  
- Když použijete AD FS pro Azure Stack systém identit a aktualizujete tuto verzi Azure Stack, Výchozí vlastník výchozího předplatného poskytovatele se obnoví na integrovaného uživatele **CloudAdmin** .  
  Alternativní řešení: Pokud chcete tento problém vyřešit po instalaci této aktualizace, použijte krok 3 od [Automatizace triggeru ke konfiguraci vztahu důvěryhodnosti zprostředkovatele deklarací v Azure Stack](../azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-hub) postupu pro resetování vlastníka výchozího předplatného poskytovatele.   

<!-- TBD - IS ASDK --> 
- Některé typy předplatného pro správu nejsou k dispozici. Když upgradujete Azure Stack na tuto verzi, nebudou se v konzole zobrazovat dva typy předplatného, které byly představeny s verzí 1804. To se očekává. K dispozici jsou nedostupné typy předplatného pro *měření* a *předplatné spotřeby*. Tyto typy předplatného jsou viditelné v nových Azure Stack prostředí od verze 1804, ale ještě nejsou připravené k použití. Měli byste dál používat výchozí typ předplatného *poskytovatele* .  

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

  Výstrahy #1 a #2 lze bezpečně ignorovat a v průběhu času se automaticky zavřou. 

  Může se zobrazit také následující výstraha pro *kapacitu*. V případě této výstrahy se může procentuální hodnota dostupné paměti identifikované v popisu lišit:  

  #3 výstrahy:
   - Název: nenízká kapacita paměti
   - ZÁVAŽNOST: kritická
   - SOUČÁST: kapacita
   - Popis: oblast využila více než 80,00% dostupné paměti. Vytváření virtuálních počítačů s velkým množstvím paměti může selhat.  

  V této verzi Azure Stack se tato výstraha může aktivovat nesprávně. Pokud se virtuální počítače tenanta i nadále nasazují úspěšně, můžete tuto výstrahu ignorovat. 
  
  Výstraha #3 neukončí automaticky. Při zavření této výstrahy Azure Stack vytvoří stejnou výstrahu do 15 minut.  

<!-- 2368581 - IS. ASDK --> 
- Pokud je jako operátor Azure Stack obdržíte výstrahu o nedostatku paměti a virtuální počítače klienta se nepodařilo nasadit s *chybou vytvoření virtuálního počítače infrastruktury*, je možné, že Azure Stack razítko není dostupné paměti. [Capacity Planner Azure Stack](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) použijte k tomu, abyste nejlépe pochopili kapacitu dostupnou pro vaše úlohy. 


### <a name="compute"></a>Výpočetní prostředky
<!-- TBD - IS, ASDK --> 
- Při výběru velikosti virtuálního počítače pro nasazení virtuálního počítače nejsou některé velikosti virtuálních počítačů řady F-Series při vytváření virtuálního počítače viditelné jako součást selektoru velikostí. Následující velikosti virtuálních počítačů se nezobrazí v selektoru: *F8s_v2*, *F16s_v2*, *F32s_v2* a *F64s_v2*.  
  Jako alternativní řešení použijte k nasazení virtuálního počítače jednu z následujících metod. V každé metodě je nutné zadat velikost virtuálního počítače, kterou chcete použít.

  - **Šablona Azure Resource Manager:** Když použijete šablonu, nastavte *vmSize* v šabloně tak, aby se rovnala velikosti virtuálního počítače, kterou chcete použít. K nasazení virtuálního počítače, který používá *F32s_v2* velikost, se používá například následující položka:  

    ```
        "properties": {
        "hardwareProfile": {
                "vmSize": "Standard_F32s_v2"
        },
    ```  
  - Rozhraní příkazového **řádku Azure:** Můžete použít příkaz [AZ VM Create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create&preserve-view=true) a zadat velikost virtuálního počítače jako parametr, podobně jako `--size "Standard_F32s_v2"` .

  - **PowerShell:** Pomocí prostředí PowerShell můžete použít rutinu [New-AzureRMVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-6.0.0&preserve-view=true) s parametrem, který určuje velikost virtuálního počítače, podobně jako `-VMSize "Standard_F32s_v2"` .


<!-- TBD - IS ASDK --> 
- Nastavení škálování pro sady škálování virtuálních počítačů nejsou na portálu k dispozici. Jako alternativní řešení můžete použít [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Kvůli rozdílům v verzi PowerShellu musíte použít `-Name` parametr místo `-VMScaleSetName` .

<!-- TBD - IS --> 
- Když na portálu vytvoříte skupinu dostupnosti tak, že kliknete na **Nová**  >    >  **Skupina dostupnosti** služby COMPUTE, můžete vytvořit jenom skupinu dostupnosti s doménou selhání a aktualizovat doménu na 1. Alternativním řešením při vytváření nového virtuálního počítače je vytvoření skupiny dostupnosti pomocí PowerShellu, rozhraní příkazového řádku nebo v rámci portálu.

<!-- TBD - IS ASDK --> 
- Když vytváříte virtuální počítače na portálu Azure Stack User Portal, portál zobrazí nesprávný počet datových disků, které se můžou připojit k virtuálnímu počítači DS Series. Virtuální počítače řady DS můžou přijmout tolik datových disků jako konfigurace Azure.

<!-- TBD - IS ASDK --> 
- Když se nepodaří vytvořit bitovou kopii virtuálního počítače, může být neúspěšná položka, kterou nemůžete odstranit, přidat do okna image virtuálních počítačů.

  Jako alternativní řešení vytvořte novou image virtuálního počítače se zástupným virtuálním pevným diskem, který se dá vytvořit pomocí technologie Hyper-V (New-VHD-Path C:\dummy.VHD-fixed-SizeBytes 1 GB). Tento proces by měl vyřešit problém, který brání odstranění položky, která selhala. Po vytvoření fiktivní image 15 minut ji můžete úspěšně odstranit.

  Pak se můžete pokusit znovu stáhnout image virtuálního počítače, která se dřív nezdařila.

<!-- TBD - IS ASDK --> 
- Pokud zřízení rozšíření na nasazení virtuálního počítače trvá příliš dlouho, uživatelé by měli povolit časový limit zřizování namísto pokusu o zastavení procesu navrácení nebo odstranění virtuálního počítače.  

<!-- 1662991 IS ASDK --> 
- Diagnostika virtuálního počítače se systémem Linux není v Azure Stack podporována. Když nasadíte virtuální počítač se systémem Linux s povoleným diagnostikou virtuálních počítačů, nasazení se nezdařilo. Nasazení se také nepovede, pokud povolíte základní metriky virtuálního počítače se systémem Linux prostřednictvím nastavení diagnostiky.  


### <a name="networking"></a>Sítě
<!-- TBD - IS ASDK --> 
- Trasy definované uživatelem nemůžete vytvořit na portálu pro správu ani pro uživatele. Jako alternativní řešení použijte [Azure PowerShell](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-powershell).

<!-- 1766332 - IS ASDK --> 
- Pokud v části **sítě** kliknete na **Vytvořit VPN Gateway** pro nastavení připojení k síti VPN, bude **zásada založená** jako typ sítě VPN. Tuto možnost nevybírejte. V Azure Stack je podporována pouze možnost **na základě trasy** .

<!-- 2388980 - IS ASDK --> 
- Po vytvoření virtuálního počítače a jeho přidružení k veřejné IP adrese nemůžete zrušit přidružení tohoto virtuálního počítače k této IP adrese. Zdá se, že zrušení přidružení funguje, ale dříve přiřazená veřejná IP adresa zůstane přidružená k původnímu virtuálnímu počítači.

  V současné době musíte pro nové virtuální počítače, které vytvoříte, použít jenom nové veřejné IP adresy.

  K tomuto chování dochází i v případě, že IP adresu znovu přiřadíte novému virtuálnímu počítači (obvykle se označuje jako *swap VIP*). Při všech budoucích pokusech o připojení přes tuto IP adresu dojde k připojení k původnímu virtuálnímu počítači, a ne k novému.

<!-- 2292271 - IS ASDK --> 
- Pokud vyvoláte omezení kvóty pro síťový prostředek, který je součástí nabídky a plánu, který je přidružený k předplatnému tenanta, nový limit se pro toto předplatné nepoužije. Nový limit se ale uplatní i pro nová předplatná, která se vytvoří po zvýšení kvóty.

  Pokud chcete tento problém obejít, použijte plán Add-On, abyste zvýšili kvótu sítě, když je plán už přidružený k předplatnému. Další informace najdete v tématu Jak [nastavit plán doplňku k dispozici](../azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

<!-- 2304134 IS ASDK --> 
- Nemůžete odstranit předplatné, které obsahuje prostředky zóny DNS nebo které jsou k němu přidružené prostředky směrovací tabulky. Pokud chcete úspěšně odstranit předplatné, musíte nejdřív z předplatného tenanta odstranit prostředky zóny DNS a směrovací tabulky.


<!-- 1902460 - IS ASDK --> 
- Azure Stack podporuje jednu *bránu místní sítě* na IP adresu. To platí pro všechna předplatná tenanta. Po vytvoření prvního připojení brány místní sítě se budou zablokované následné pokusy o vytvoření prostředku brány místní sítě se stejnou IP adresou.

<!-- 16309153 - IS ASDK --> 
- Na Virtual Network, která byla vytvořena pomocí *automatického* nastavení serveru DNS, se změna na vlastní server DNS nezdařila. Aktualizovaná nastavení nejsou vložena do virtuálních počítačů v této virtuální síti.

<!-- TBD - IS ASDK --> 
- Azure Stack nepodporuje přidání dalších síťových rozhraní do instance virtuálního počítače po nasazení virtuálního počítače. Pokud virtuální počítač vyžaduje více než jedno síťové rozhraní, musí být definované v době nasazení.

<!-- 2096388 IS --> 
- Pomocí portálu pro správu nelze aktualizovat pravidla pro skupinu zabezpečení sítě.

    Alternativní řešení pro App Service: Pokud potřebujete vzdálenou plochu k instancím kontroleru, upravíte pravidla zabezpečení v rámci skupin zabezpečení sítě pomocí PowerShellu.  Níže jsou uvedeny příklady *Povolení* a obnovení konfigurace pro *odepření*:  

    - *Povolil*

      ```powershell    
      Connect-AzureRmAccount -EnvironmentName AzureStackAdmin

      $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

      $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

      ##This doesn't work. Need to set properties again even in case of edit

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

        Connect-AzureRmAccount -EnvironmentName AzureStackAdmin

        $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

        $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

        ##This doesn't work. Need to set properties again even in case of edit

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


### <a name="sql-and-mysql"></a>SQL a MySQL

<!-- TBD - IS --> 
- Pouze poskytovatel prostředků je podporován k vytváření položek na serverech, které jsou hostiteli SQL nebo MySQL. Položky vytvořené na hostitelském serveru, které nejsou vytvořené poskytovatelem prostředků, můžou vést k neshodě stavu.  

<!-- IS, ASDK --> 
- Při vytváření SKU pro poskytovatele prostředků SQL a MySQL se v názvech **rodin** nebo **vrstev** nepodporují speciální znaky, včetně mezer a teček.

<!-- TBD - IS --> 
> [!NOTE]  
> Po aktualizaci na Azure Stack 1805 můžete nadále používat poskytovatele prostředků SQL a MySQL, které jste předtím nasadili.  Pokud bude k dispozici nová verze, doporučujeme aktualizovat SQL a MySQL. Podobně jako u Azure Stack použijte postupně aktualizace pro poskytovatele prostředků SQL a MySQL. Například pokud používáte verzi 1803, nejprve použijte verzi 1804 a pak aktualizujte na 1805.      
>   
> Instalace aktualizace 1805 nemá vliv na aktuální použití poskytovatelů prostředků SQL nebo MySQL vašimi uživateli.
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
Balíček aktualizací Azure Stack 1805 si můžete stáhnout [odsud.](https://aka.ms/azurestackupdatedownload)


## <a name="see-also"></a>Viz také
- Postup pro monitorování a obnovení aktualizací pomocí privilegovaného koncového bodu (PEP) najdete v tématu [monitorování aktualizací v Azure Stack pomocí privilegovaného koncového bodu](../azure-stack-monitor-update.md).
- Přehled správy aktualizací v Azure Stack najdete v tématu [Správa aktualizací v Azure Stack přehledu](../azure-stack-updates.md).
- Další informace o tom, jak použít aktualizace s Azure Stack, najdete v tématu věnovaném [použití aktualizací v Azure Stack](../azure-stack-apply-updates.md).