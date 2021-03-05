---
title: Aktualizace 1804 Azure Stack | Microsoft Docs
description: Přečtěte si o tom, co je v aktualizaci 1804 pro Azure Stack integrovaných systémech, známých problémech a kde stáhnout aktualizaci.
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
ms.openlocfilehash: eb55f42161095f9034b6c5cf726240d86366604b
ms.sourcegitcommit: f194f9ca4297864500e62d8658674a0625b29d1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102187074"
---
# <a name="azure-stack-1804-update"></a>Aktualizace Azure Stack 1804

*Platí pro: Azure Stack integrovaných systémů*

Tento článek popisuje vylepšení a opravy v balíčku aktualizace 1804, známé problémy této verze a místo, kam stáhnout aktualizaci. Známé problémy jsou rozdělené na problémy přímo související s procesem aktualizace a problémy se sestavením (po instalaci).

> [!IMPORTANT]        
> Tento balíček aktualizace je určen pouze pro Azure Stack integrovaných systémů. Nepoužívejte tento balíček aktualizace na Azure Stack Development Kit.

## <a name="build-reference"></a>Referenční informace o buildu    
Číslo buildu aktualizace Azure Stack 1804 je **20180513,1**.   

### <a name="new-features"></a>Nové funkce
Tato aktualizace obsahuje následující vylepšení pro Azure Stack.

<!-- 15028744 - IS -->  
- **Podpora sady Visual Studio pro odpojování Azure Stack nasazení pomocí AD FS**. V rámci sady Visual Studio teď můžete přidat předplatná a ověřit pomocí AD FS pověření federovaného uživatele. 
 
<!-- 1779474, 1779458 - IS --> 
- **Používejte virtuální počítače s Av2 a F Series**. Azure Stack teď můžou používat virtuální počítače založené na velikosti virtuálních počítačů řady Av2-Series a F-Series. Další informace najdete [v tématu velikosti virtuálních počítačů podporované v Azure Stack](../../user/azure-stack-vm-sizes.md). 

<!-- 1759172 - IS, ASDK --> 
- **Nová předplatná pro správu**. S 1804 jsou na portálu k dispozici dva nové typy předplatného. Tyto nové typy předplatného jsou kromě výchozího předplatného poskytovatele a jsou viditelné u nových Azure Stack instalací od verze 1804. *Nepoužívejte tyto nové typy předplatného s touto verzí Azure Stack*. V rámci budoucí aktualizace oznamujeme dostupnost používání těchto typů předplatných. 

  Pokud Azure Stack aktualizujete na verzi 1804, nebudou se tyto dva nové typy předplatného zobrazovat. Nová nasazení Azure Stack integrovaných systémů a instalací Azure Stack Development Kit verze 1804 nebo novější však mají přístup ke všem třem typům předplatného.  

  Tyto nové typy předplatného jsou součástí větší změny zabezpečení předplatného výchozího poskytovatele a usnadňují nasazení sdílených prostředků, jako jsou hostitelské servery SQL. Protože přidáváme další části této větší změny v budoucích aktualizacích Azure Stack, můžou se ztratit prostředky nasazené v těchto nových typech předplatných. 

  K dispozici jsou tři typy předplatného, které jsou teď:  
  - Předplatné výchozího poskytovatele: nadále používat tento typ předplatného. 
  - Odběr měření: *Nepoužívejte tento typ předplatného.*
  - Předplatné spotřeby: *Nepoužívat tento typ předplatného*

  



## <a name="fixed-issues"></a>Opravené problémy

<!-- IS, ASDK -->  
- Na portálu pro správu už nemusíte aktualizovat dlaždici aktualizace, než se zobrazí informace.
 
<!-- 2050709  -->  
- Nyní můžete pomocí portálu pro správu upravit metriky úložiště pro Blob service, Table service a Služba front.
 
<!-- IS, ASDK --> 
- Když v části **sítě** kliknete na **připojení** k nastavení připojení k síti VPN, je teď jedinou dostupnou možností **síť Site-to-Site (IPSec)** .

- **Různé opravy** pro výkon, stabilitu, zabezpečení a operační systém, který je používán nástrojem Azure Stack.

## <a name="additional-releases-timed-with-this-update"></a>U další verze vypršela Tato aktualizace.  
Následující jsou nyní k dispozici, ale nevyžadují Azure Stack Update 1804.
- **Aktualizujte na sadu Microsoft Azure Stack System Center Operations Manager monitoring Pack**. Novou verzi (1.0.3.0) sady Microsoft System Center Operations Manager monitoring Pack pro Azure Stack je k dispozici ke [stažení](https://www.microsoft.com/download/details.aspx?id=55184). V této verzi můžete při přidávání nasazení připojené Azure Stack použít instanční objekty. Tato verze také nabízí Update Managementé prostředí, které umožňuje provádět nápravné akce přímo v rámci Operations Manager. K dispozici jsou také nové řídicí panely, které zobrazují poskytovatele prostředků, jednotky škálování a uzly jednotek škálování.

- **Nové prostředí PowerShell pro správce Azure Stack verze 1.3.0**.  Azure Stack PowerShell 1.3.0 je teď k dispozici pro instalaci. Tato verze poskytuje příkazy pro všechny poskytovatele prostředků správců ke správě Azure Stack.  V této verzi se obsah v [úložišti](https://github.com/Azure/AzureStack-Tools)githubu Azure Stack Tools nepoužívá. 

   Podrobnosti o instalaci získáte podle [pokynů](../azure-stack-powershell-install.md) nebo obsahu pro [nápovědu](/powershell/azure/azure-stack/overview?preserve-view=true&view=azurestackps-1.3.0) pro Azure Stack modul 1.3.0. 

- **Počáteční verze referenčního Azure Stack rozhraní API REST** [Reference k rozhraní API pro všechny poskytovatele prostředků správce Azure Stack](/rest/api/azure-stack/) je teď publikovaná. 


## <a name="before-you-begin"></a>Než začnete    

### <a name="prerequisites"></a>Požadavky
- Před instalací aktualizace Azure Stack 1804 nainstalujte aktualizaci Azure Stack 1803.  
  
- Nainstalujte nejnovější dostupnou aktualizaci nebo opravu hotfix pro verzi 1803. 


### <a name="known-issues-with-the-update-process"></a>Známé problémy s procesem aktualizace   
- Během instalace aktualizace 1804 se může zobrazit upozornění s *šablonou chyby nadpisu pro typ FaultType UserAccounts. chybí New.*  Tyto výstrahy můžete bezpečně ignorovat. Tyto výstrahy se po dokončení aktualizace na 1804 automaticky zavřou.   
 
<!-- TBD - IS --> 
- Nepokoušejte se během instalace této aktualizace vytvořit virtuální počítače. Další informace o správě aktualizací najdete v tématu [Správa aktualizací v Azure Stack Overview](../azure-stack-updates.md).


### <a name="post-update-steps"></a>Kroky po aktualizaci
Po instalaci 1804 nainstalujte všechny příslušné opravy hotfix. Další informace najdete v následujících článcích znalostní báze a v našich [zásadách pro obsluhu](../azure-stack-servicing-policy.md).  
 - [KB 4344114 – Azure Stack hotfix 1.0.180527.15](https://support.microsoft.com/help/4344114).




### <a name="known-issues-post-installation"></a>Známé problémy (po instalaci)
Níže jsou uvedené známé problémy po instalaci pro Build  **20180513,1**.

#### <a name="portal"></a>Portál

- Technickou dokumentaci Azure Stack se zaměřuje na nejnovější verzi. Vzhledem k změnám portálu mezi verzemi, co vidíte při použití Azure Stack portály, se může lišit od toho, co vidíte v dokumentaci. 

<!-- TBD - IS ASDK --> 
- Aktualizace ovladačů nemůžete použít pomocí balíčku rozšíření OEM s touto verzí Azure Stack.  Pro tento problém neexistuje žádné alternativní řešení.

<!-- 1272111 - IS --> 
- Po instalaci nebo aktualizaci této verze Azure Stack možná nebudete moci zobrazit Azure Stack jednotky škálování na portálu pro správu.  
  Alternativní řešení: k zobrazení informací o jednotkách škálování použijte PowerShell. Další informace [najdete v tématu věnovaném obsahu aplikace](/powershell/azure/azure-stack/overview?preserve-view=true&view=azurestackps-1.3.0) Azure Stack 1.3.0 Module. 

<!-- 2332636 - IS -->  
- Když použijete AD FS pro Azure Stack systém identit a aktualizujete tuto verzi Azure Stack, Výchozí vlastník výchozího předplatného poskytovatele se obnoví na integrovaného uživatele **CloudAdmin** .  
  Alternativní řešení: Pokud chcete tento problém vyřešit po instalaci této aktualizace, použijte krok 3 od [Automatizace triggeru ke konfiguraci vztahu důvěryhodnosti zprostředkovatele deklarací v Azure Stack](../azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-hub) postupu pro resetování vlastníka výchozího předplatného poskytovatele.   

<!-- TBD - IS ASDK --> 
- Některé typy předplatného pro správu nejsou k dispozici.  Když upgradujete Azure Stack na tuto verzi, nebudou se v konzole zobrazovat dva typy předplatného, které byly [představeny s verzí 1804](#new-features) . To se očekává. K dispozici jsou nedostupné typy předplatného pro *měření* a *předplatné spotřeby*. Tyto typy předplatného jsou viditelné v nových Azure Stack prostředí od verze 1804, ale ještě nejsou připravené k použití. Měli byste dál používat výchozí typ předplatného *poskytovatele* .  


<!-- TBD -  IS ASDK -->
- Možnost [otevření nové žádosti o podporu z rozevíracího seznamu](../azure-stack-manage-portals.md#quick-access-to-help-and-support) v portálu pro správu není dostupná. Místo toho použijte následující odkaz:     
    - V případě Azure Stack integrovaných systémů použijte https://aka.ms/newsupportrequest .

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

<!-- TBD - IS ASDK --> 
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
 

#### <a name="compute"></a>Výpočetní prostředky
<!-- TBD - IS --> 
- Při výběru velikosti virtuálního počítače pro nasazení virtuálního počítače nejsou některé velikosti virtuálních počítačů řady F-Series při vytváření virtuálního počítače viditelné jako součást selektoru velikostí. Následující velikosti virtuálních počítačů se nezobrazí v selektoru: *F8s_v2*, *F16s_v2*, *F32s_v2* a *F64s_v2*.  
  Jako alternativní řešení použijte k nasazení virtuálního počítače jednu z následujících metod. V každé metodě je nutné zadat velikost virtuálního počítače, kterou chcete použít.

  - **Šablona Azure Resource Manager:** Když použijete šablonu, nastavte *vmSize* v šabloně tak, aby se rovnala požadované velikosti virtuálního počítače. K nasazení virtuálního počítače, který používá *F32s_v2* velikost, se používá například následující:  

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
- Když vytváříte virtuální počítače na portálu Azure Stack User Portal, portál zobrazí nesprávný počet datových disků, které se můžou připojit k virtuálnímu počítači D Series. Všechny podporované virtuální počítače řady D Series můžou pojmout tolik datových disků jako konfigurace Azure.

<!-- TBD - IS ASDK --> 
- Když se nepodaří vytvořit bitovou kopii virtuálního počítače, může být neúspěšná položka, kterou nemůžete odstranit, přidat do okna image virtuálních počítačů.

  Jako alternativní řešení vytvořte novou image virtuálního počítače se zástupným virtuálním pevným diskem, který se dá vytvořit pomocí technologie Hyper-V (New-VHD-Path C:\dummy.VHD-fixed-SizeBytes 1 GB). Tento proces by měl vyřešit problém, který brání odstranění položky, která selhala. Po vytvoření fiktivní image 15 minut ji můžete úspěšně odstranit.

  Pak se můžete pokusit znovu stáhnout image virtuálního počítače, která se dřív nezdařila.

<!-- TBD - IS ASDK --> 
- Pokud zřízení rozšíření na nasazení virtuálního počítače trvá příliš dlouho, uživatelé by měli povolit časový limit zřizování namísto pokusu o zastavení procesu navrácení nebo odstranění virtuálního počítače.  

<!-- 1662991 IS ASDK --> 
- Diagnostika virtuálního počítače se systémem Linux není v Azure Stack podporována. Když nasadíte virtuální počítač se systémem Linux s povoleným diagnostikou virtuálních počítačů, nasazení se nezdařilo. Nasazení se také nepovede, pokud povolíte základní metriky virtuálního počítače se systémem Linux prostřednictvím nastavení diagnostiky.  


#### <a name="networking"></a>Sítě
<!-- 1766332 - IS ASDK --> 
- Pokud v části **sítě** kliknete na **Vytvořit VPN Gateway** pro nastavení připojení k síti VPN, bude **zásada založená** jako typ sítě VPN. Tuto možnost nevybírejte. V Azure Stack je podporována pouze možnost **na základě trasy** .

<!-- 2388980 - IS ASDK --> 
- Po vytvoření virtuálního počítače a jeho přidružení k veřejné IP adrese nemůžete zrušit přidružení tohoto virtuálního počítače k této IP adrese. Zdá se, že zrušení přidružení funguje, ale dříve přiřazená veřejná IP adresa zůstane přidružená k původnímu virtuálnímu počítači.

  V současné době musíte pro nové virtuální počítače, které vytvoříte, použít jenom nové veřejné IP adresy.

  K tomuto chování dochází i v případě, že IP adresu znovu přiřadíte novému virtuálnímu počítači (obvykle se označuje jako *swap VIP*). U všech budoucích pokusů o připojení přes tuto IP adresu dojde k připojení k původně přidruženému virtuálnímu počítači, a ne k novému.

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
        
        Connect-AzureRmAccount -EnvironmentName AzureStackAdmin
        
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

<!-- TBD - IS --> 
- Pouze poskytovatel prostředků je podporován k vytváření položek na serverech, které jsou hostiteli SQL nebo MySQL. Položky vytvořené na hostitelském serveru, které nejsou vytvořené poskytovatelem prostředků, můžou vést k neshodě stavu.  

<!-- IS, ASDK --> 
- Při vytváření SKU pro poskytovatele prostředků SQL a MySQL se v názvech **rodin** nebo **vrstev** nepodporují speciální znaky, včetně mezer a teček.

<!-- TBD - IS -->
> [!NOTE]  
> Po aktualizaci na Azure Stack 1804 Můžete nadále používat poskytovatele prostředků SQL a MySQL, které jste předtím nasadili.  Pokud bude k dispozici nová verze, doporučujeme aktualizovat SQL a MySQL. Podobně jako u Azure Stack použijte postupně aktualizace pro poskytovatele prostředků SQL a MySQL.  Například pokud používáte verzi 1802, nejprve použijte verzi 1803 a pak aktualizujte na 1804.      
>   
> Instalace aktualizace 1804 nemá vliv na aktuální použití poskytovatelů prostředků SQL nebo MySQL vašimi uživateli.
> Bez ohledu na verzi poskytovatelů prostředků, které použijete, se data vašich uživatelů v jejich databázích nekontaktují a zůstávají dostupná.    



#### <a name="app-service"></a>App Service
<!-- 2352906 - IS ASDK --> 
- Před vytvořením první funkce Azure v předplatném musí uživatelé zaregistrovat poskytovatele prostředků úložiště.

<!-- TBD - IS ASDK --> 
- Pro horizontální navýšení kapacity infrastruktury (pracovní procesy, správy, front-endové role) musíte použít PowerShell, jak je popsáno v poznámkách k verzi pro výpočetní prostředky.

<!-- TBD - IS ASDK --> 
- V tuto chvíli se dá nasadit App Service jenom do **předplatného výchozího poskytovatele** .  V budoucí aktualizaci App Service se nasadí do nového předplatného měření představeného v Azure Stack 1804 a všechna stávající nasazení budou také migrována do tohoto nového předplatného.

#### <a name="usage"></a>Využití  
<!-- TBD - IS ASDK --> 
- Data měřiče využití veřejných IP adres zobrazují stejnou hodnotu *EventDateTime* pro každý záznam místo razítka *timedate* , které ukazuje, kdy byl záznam vytvořen. V současné době můžete tato data použít k provedení přesného účtování využití veřejné IP adresy.


<!-- #### Identity -->
<!-- #### Marketplace --> 


## <a name="download-the-update"></a>Stáhnout aktualizaci
Balíček aktualizací Azure Stack 1804 si můžete stáhnout [odsud.](https://aka.ms/azurestackupdatedownload)


## <a name="see-also"></a>Viz také
- Postup pro monitorování a obnovení aktualizací pomocí privilegovaného koncového bodu (PEP) najdete v tématu [monitorování aktualizací v Azure Stack pomocí privilegovaného koncového bodu](../azure-stack-monitor-update.md).
- Přehled správy aktualizací v Azure Stack najdete v tématu [Správa aktualizací v Azure Stack přehledu](../azure-stack-updates.md).
- Další informace o tom, jak použít aktualizace s Azure Stack, najdete v tématu věnovaném [použití aktualizací v Azure Stack](../azure-stack-apply-updates.md).