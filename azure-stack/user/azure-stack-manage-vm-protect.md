---
title: Chránit virtuální počítače nasazené v Azure Stack | Microsoft Docs
description: Naučte se, jak vytvořit plán obnovení pro ochranu virtuálních počítačů nasazených v Azure Stack před ztrátou dat a neplánovanými výpadky.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 4e5833cf-4790-4146-82d6-737975fb06ba
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: hectorl
ms.lastreviewed: 3/19/2018
ms.openlocfilehash: f633f000968fcf4f373fc502898fa18084f93f80
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71824303"
---
# <a name="protect-vms-deployed-on-azure-stack"></a>Ochrana virtuálních počítačů nasazených v Azure Stack

Tento článek slouží jako vodítko pro vývoj plánu ochrany virtuálních počítačů, které uživatelé nasazují na Azure Stack.


Chcete-li chránit před ztrátou dat a neplánovanými výpadky, je nutné implementovat plán zálohování nebo zotavení po havárii pro uživatelské aplikace a jejich data. Tento plán může být pro každou aplikaci jedinečný, ale dodržuje rámec vytvořený v komplexní strategii pro provozní kontinuitu a zotavení po havárii (BC/DR) vaší organizace. Dobrým počátečním bodem [je Azure Stack: Požadavky na provozní kontinuitu a zotavení](https://aka.ms/azurestackbcdrconsiderationswp)po havárii.

## <a name="azure-stack-infrastructure-recovery"></a>Azure Stack Recovery infrastruktury

Uživatelé zodpovídají za ochranu svých virtuálních počítačů odděleně od služeb infrastruktury Azure Stack.

Plán obnovení pro služby infrastruktury Azure Stack nezahrnuje obnovení virtuálních počítačů uživatele, účtů úložiště nebo databází. Jako vlastník aplikace zodpovídáte za implementaci plánu obnovení pro vaše aplikace a data.

Pokud je cloud Azure Stack v režimu offline delší dobu nebo trvale neobnovitelné, je nutné mít k dispozici plán obnovení, který:

* Zajišťuje minimální prostoje.
* Udržuje důležité virtuální počítače, například databázové servery, které jsou spuštěny.
* Umožňuje aplikacím zachovat požadavky uživatelů na údržbu.

Operátor Azure Stackho cloudu zodpovídá za vytvoření plánu obnovení pro podkladovou infrastrukturu a služby Azure Stack. Další informace najdete v tématu [obnovení z závažné ztráty dat](../operator/azure-stack-backup-recover-data.md).

## <a name="considerations-for-iaas-vms"></a>Předpoklady pro virtuální počítače s IaaS
Operační systém nainstalovaný na virtuálním počítači IaaS omezuje produkty, které můžete použít k ochraně dat, která obsahuje. Pro virtuální počítače s IaaS založené na Windows můžete k ochraně dat použít Microsoft a partnerské produkty. U virtuálních počítačů s IaaS se systémem Linux je jedinou možností použití partnerských produktů. [Všechny partnery BC/Dr s ověřenými produkty pro Azure Stack najdete v tomto datovém listu](https://aka.ms/azurestackbcdrpartners).

## <a name="sourcetarget-combinations"></a>Kombinace zdroje a cíle

Každý Azure Stack Cloud je nasazený v jednom datovém centru. K obnovení vašich aplikací se vyžaduje samostatné prostředí. Prostředí pro obnovení může být jiné Azure Stack Cloud v jiném datovém centru nebo ve veřejném cloudu Azure. Vaše požadavky na svrchovanost a ochranu dat určují prostředí pro obnovení vaší aplikace. Při povolování ochrany pro každou aplikaci máte flexibilitu při výběru konkrétní možnosti obnovení pro každé z nich. Můžete mít aplikace v jednom předplatném, které zálohují data do jiného datového centra. V jiném předplatném můžete replikovat data do veřejného cloudu Azure.

Naplánujte strategii zálohování a obnovení po havárii pro každou aplikaci a určete cíl pro každou aplikaci. Plán obnovení pomáhá vaší organizaci správně nastavit kapacitu úložiště povinnou pro místní a využití projektů ve veřejném cloudu.

|  | Globální Azure | Azure Stack nasazené do Datacenter CSP a provozovaný zprostředkovatelem CSP | Azure Stack nasazené do Datacenter zákazníka a provozovaný zákazníkem |
|------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------|
| **Azure Stack nasazené do Datacenter CSP a provozovaný zprostředkovatelem CSP** | Virtuální počítače uživatele jsou nasazeny do Azure Stack ovládaného CSP.<br><br>Virtuální počítače uživatelů se obnovily ze zálohy nebo převzaly služby při selhání přímo do Azure. | CSP pracuje s primárními a sekundárními instancemi Azure Stack ve vlastních datacentrech.<br><br>Virtuální počítače uživatele jsou obnoveny nebo převzetí služeb při selhání mezi těmito dvěma instancemi Azure Stack. | CSP funguje Azure Stack v primární lokalitě.<br><br>Datacenter zákazníka je cíl obnovení nebo převzetí služeb při selhání. |
| **Azure Stack nasazené do Datacenter zákazníka a provozovaný zákazníkem** | Uživatelské virtuální počítače se nasazují do Azure Stack ovládaného zákazníkem.<br><br>Virtuální počítače uživatelů se obnovily ze zálohy nebo převzaly služby při selhání přímo do Azure. | Zákazník funguje Azure Stack v primární lokalitě.<br><br>Datacentrum CSP je cíl obnovení nebo převzetí služeb při selhání. | Zákazník provozuje primární a sekundární instance Azure Stack ve vlastních datacentrech.<br><br>Virtuální počítače uživatele jsou obnoveny nebo převzetí služeb při selhání mezi těmito dvěma instancemi Azure Stack. |

![Kombinace zdroje a cíle](media/azure-stack-manage-vm-backup/vm_backupdataflow_01.png)

## <a name="app-recovery-objectives"></a>Cíle obnovení aplikace

Určete množství výpadku a ztráty dat, které vaše organizace může tolerovat pro každou aplikaci. Díky kvantitativnímu výpadku a ztrátě dat můžete vytvořit plán obnovení, který minimalizuje dopad havárie ve vaší organizaci. Pro každou aplikaci zvažte následující:

 - **Cíl doby obnovení (RTO)**  
RTO je maximální přijatelná doba, po kterou může být aplikace po incidentu nedostupná. Například RTO 90 minut znamená, že musíte být schopni obnovit aplikaci do běžícího stavu během 90 minut od začátku havárie. Pokud máte malou RTO, můžete zachovat druhé nasazení nepřetržitě běžící na pohotovostním režimu pro ochranu před oblastním výpadkem.
 - **Cíl bodu obnovení (RPO)**  
RPO je maximální doba ztráty dat, která je během havárie přijatelná. Pokud například ukládáte data do jediné databáze, která se zálohuje každou hodinu a nemá žádnou replikaci do jiných databází, můžete přijít o hodinu dat.

Plánovaná doba obnovení a cíl bodu obnovení jsou obchodní požadavky. Vyhodnocením rizika můžete definovat RTO a RPO aplikace.

Další metrikou je průměrná **Doba obnovení** (MTTR), což je průměrný čas potřebný k obnovení aplikace po selhání. MTTR je empirická hodnota pro systém. Pokud MTTR překročí RTO, pak selhání v systému způsobí nepřijatelné přerušení v podniku, protože nebude možné obnovit systém v rámci definovaného RTO.

### <a name="backup-restore"></a>Zálohování – obnovení

Nejběžnějším schématem ochrany pro aplikace založené na virtuálním počítači je použití zálohovacího softwaru. Zálohování virtuálního počítače obvykle zahrnuje operační systém, konfiguraci operačního systému, binární soubory aplikace a data aplikací. Zálohy se vytvoří pomocí snímku svazků, disků nebo celého virtuálního počítače. Díky Azure Stack máte flexibilitu při zálohování z kontextu hostovaného operačního systému nebo z rozhraní API pro Azure Stack úložiště a výpočetní prostředí. Azure Stack nepodporuje pořizování záloh na úrovni hypervisoru.
 
![Backup – ReSTOR](media/azure-stack-manage-vm-backup/vm_backupdataflow_03.png)

Obnovení aplikace vyžaduje obnovení jednoho nebo více virtuálních počítačů do stejného cloudu nebo do nového cloudu. Můžete cílit na Cloud ve vašem datovém centru nebo ve veřejném cloudu. Zvolený Cloud je zcela v rámci vašeho ovládacího prvku a je založen na požadavcích na ochranu osobních údajů a suverenity dat.

 - RTO Doba výpadku měřená v hodinách
 - OBNOVENÍ Proměnlivá ztráta dat (v závislosti na četnosti zálohování)
 - Topologie nasazení: Aktivní/pasivní

#### <a name="planning-your-backup-strategy"></a>Plánování strategie zálohování

Plánování strategie zálohování a definování požadavků na škálování začíná vystanovením počtu instancí virtuálních počítačů, které je potřeba chránit. Zálohování všech virtuálních počítačů napříč všemi servery v prostředí je běžná strategie. U Azure Stack ale existují některé virtuální počítače, které je potřeba zálohovat. Například virtuální počítače v rámci škálované sady se považují za dočasné prostředky, které můžou pocházet a jít, někdy bez upozornění. Veškerá trvalá data, která je třeba chránit, se ukládají do samostatného úložiště, jako je databáze nebo úložiště objektů.

Důležité informace pro zálohování virtuálních počítačů na Azure Stack:

 - **Kategorizaci**
    - Vezměte v úvahu model, ve kterém se uživatelé přihlašují k zálohování virtuálních počítačů.
    - Definujte smlouvu o úrovni služeb (SLA) pro obnovení na základě priority aplikací nebo dopadu na firmu.
 - **Škálování**
    - Při připojování k velkému počtu nových virtuálních počítačů (Pokud se vyžaduje zálohování) zvažte možnost rozložit zálohy.
    - Vyhodnoťte zálohovací produkty, které můžou efektivně zachytit a přenést data záloh, aby se minimalizoval obsah prostředků v řešení.
    - Vyhodnoťte záložní produkty, které efektivně ukládají zálohovaná data pomocí přírůstkových nebo rozdílových záloh, abyste minimalizovali potřebu úplných záloh napříč všemi virtuálními počítači v prostředí.
 - **Obnovení**
    - Záložní produkty můžou obnovit virtuální disky, data aplikací v existujícím virtuálním počítači nebo celý prostředek virtuálního počítače a přidružené virtuální disky. Schéma obnovení, které potřebujete, závisí na tom, jak plánujete aplikaci obnovit. Může být například snazší znovu nasadit systém SQL Server ze šablony a pak obnovit databáze namísto obnovení celého virtuálního počítače nebo sady virtuálních počítačů.

### <a name="replicationmanual-failover"></a>Replikace/ruční převzetí služeb při selhání

Alternativním přístupem k podpoře vysoké dostupnosti je replikace virtuálních počítačů vaší aplikace do jiného cloudu a spoléhá na ruční převzetí služeb při selhání. Replikace operačního systému, binárních souborů aplikace a dat aplikace se dá provést na úrovni virtuálního počítače nebo hostovaného operačního systému. Převzetí služeb při selhání se spravuje pomocí dalšího softwaru, který není součástí aplikace.

S tímto přístupem se aplikace nasadí v jednom cloudu a její virtuální počítač se replikuje do jiného cloudu. Pokud se aktivuje převzetí služeb při selhání, musí se sekundární virtuální počítače zapnout v druhém cloudu. V některých scénářích převzetí služeb při selhání vytvoří virtuální počítače a připojí k nim disky. Dokončení tohoto procesu může trvat dlouhou dobu, zejména u vícevrstvé aplikace, která vyžaduje konkrétní úvodní sekvenci. Může existovat také postup, který je třeba spustit, aby bylo možné spustit požadavky na údržbu.

![Replikace – ruční převzetí služeb při selhání](media/azure-stack-manage-vm-backup/vm_backupdataflow_02.png)

 - RTO Doba výpadku měřená v minutách
 - OBNOVENÍ Proměnlivá ztráta dat (v závislosti na frekvenci replikace)
 - Topologie nasazení: Aktivní/pasivní – úspora
 
### <a name="high-availabilityautomatic-failover"></a>Vysoká dostupnost/automatické převzetí služeb při selhání

U aplikací, kde může vaše firma tolerovat jenom pár sekund nebo minut výpadku a minimální ztrátu dat, zvažte konfiguraci s vysokou dostupností. Vysoce dostupné aplikace jsou navržené tak, aby se rychle a automaticky obnovily z chyb. V případě hardwarových chyb Azure Stack infrastruktura implementuje vysokou dostupnost ve fyzické síti pomocí dvou horních přepínačů stojanu. V případě chyb na úrovni výpočetní služby používá Azure Stack více uzlů v jednotce škálování. Na úrovni virtuálního počítače můžete použít sady škálování v kombinaci s doménami selhání k zajištění, že selhání uzlu nevezmou aplikaci.

V kombinaci se sadami škálování bude vaše aplikace muset podporovat vysokou dostupnost nativně nebo podporovat používání softwaru clusteringu. Microsoft SQL Server například podporuje vysokou dostupnost nativně pro databáze pomocí režimu synchronního potvrzování. Pokud však můžete podporovat pouze asynchronní replikaci, dojde ke ztrátě dat. Aplikace je také možné nasadit do clusteru s podporou převzetí služeb při selhání, ve kterém software clusteringu zpracovává automatické převzetí služeb při selhání aplikace.

Při použití tohoto přístupu je aplikace aktivní jenom v jednom cloudu, ale software se nasadí do několika cloudů. Ostatní cloudy jsou v úsporném režimu připravené ke spuštění aplikace při aktivaci převzetí služeb při selhání.

 - RTO Doba výpadku měřená v sekundách
 - OBNOVENÍ Minimální ztráta dat
 - Topologie nasazení: Aktivní/aktivní – úspora

### <a name="fault-tolerance"></a>Odolnost proti chybám

Azure Stack fyzickou redundanci a dostupnost služby infrastruktury stačí chránit jenom proti chybám a selháním na úrovni hardwaru, jako je disk, napájení, síťový port nebo uzel. Pokud ale vaše aplikace musí být vždycky k dispozici a nemůže nikdy ztratit žádná data, je třeba v aplikaci implementovat odolnost proti chybám nativně nebo použít další software k povolení odolnosti proti chybám.

Nejdřív je potřeba zajistit, aby virtuální počítače aplikace byly nasazeny pomocí sad škálování pro ochranu proti selháním na úrovni uzlu. Aby bylo možné chránit před cloudem offline, je nutné, aby byla stejná aplikace nasazena do jiného cloudu, aby mohla i nadále obsluhovat požadavky bez přerušení. Tento model se obvykle označuje jako nasazení typu aktivní-aktivní.

Mějte na paměti, že každý Azure Stack Cloud je nezávisle na sobě navzájem, takže cloudy jsou vždy považovány za aktivní z hlediska infrastruktury. V takovém případě se několik aktivních instancí aplikace nasadí do jednoho nebo víc aktivních cloudů.

 - RTO Bez výpadků
 - OBNOVENÍ Žádná ztráta dat
 - Topologie nasazení: Aktivní/aktivní

### <a name="no-recovery"></a>Bez obnovení

Některé aplikace ve vašem prostředí nemusí mít ochranu před neplánovanými výpadky nebo ztrátou dat. Například virtuální počítače používané pro vývoj a testování obvykle není nutné obnovovat. Je vaše rozhodnutí dělat bez ochrany pro aplikaci nebo konkrétní virtuální počítač. Azure Stack nenabízí zálohování ani replikaci virtuálních počítačů z základní infrastruktury. Podobně jako v Azure, musíte se k ochraně každého virtuálního počítače v každém z vašich předplatných přihlásit.

 - RTO Neopravitelná
 - OBNOVENÍ Dokončit ztrátu dat

## <a name="recommended-topologies"></a>Doporučené topologie

Důležité informace pro nasazení Azure Stack:

|     | Doporučení | Komentáře |
|-------------------------------------------------------------------------------------------------|-----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Zálohování nebo obnovení virtuálních počítačů do externího cíle zálohování, který je už ve vašem datovém centru nasazený | Doporučené | Využijte výhod stávající infrastruktury zálohování a provozních dovedností. Nezapomeňte změnit velikost infrastruktury zálohování, aby byla připravená chránit další instance virtuálních počítačů. Ujistěte se, že infrastruktura zálohování není v těsné blízkosti vašeho zdroje. Virtuální počítače můžete obnovit do zdrojového Azure Stack, do sekundární instance Azure Stack nebo do Azure. |
| Zálohování nebo obnovení virtuálních počítačů do externího cíle zálohování vyhrazeného pro Azure Stack | Doporučené | Můžete zakoupit novou infrastrukturu zálohování nebo zřídit vyhrazenou infrastrukturu zálohování pro Azure Stack. Ujistěte se, že infrastruktura zálohování není v těsné blízkosti vašeho zdroje. Virtuální počítače můžete obnovit do zdrojového Azure Stack, do sekundární instance Azure Stack nebo do Azure. |
| Zálohování a obnovení virtuálních počítačů přímo do globálního Azure nebo do poskytovatele důvěryhodné služby | Doporučené | Pokud budete vyhovovat vašim požadavkům na ochranu osobních údajů a zákonné požadavky, můžete zálohy ukládat v globálním Azure nebo v případě poskytovatele důvěryhodné služby. V ideálním případě je také spuštěný poskytovatel služeb Azure Stack, abyste získali konzistenci v provozním prostředí při obnovení. |
| Replikace virtuálních počítačů nebo jejich převzetí služeb při selhání do samostatné instance Azure Stack | Doporučené | V případě převzetí služeb při selhání musíte mít druhý Azure Stack Cloud plně funkční, abyste se mohli vyhnout výpadkům rozšířené aplikace. |
| Replikace a převzetí služeb při selhání virtuálního počítače přímo do Azure nebo do poskytovatele důvěryhodné služby | Doporučené | Pokud můžete splnit požadavky na ochranu osobních údajů a legislativu vašich dat, můžete replikovat data do globálního Azure nebo do poskytovatele důvěryhodné služby. V ideálním případě je také spuštěný poskytovatel služeb Azure Stack, takže po převzetí služeb při selhání získáte konzistenci v provozním prostředí. |
| Nasazení cíle zálohování do stejného cloudu Azure Stack s daty aplikace | Nedoporučuje se | Neukládejte zálohy do stejného Azure Stack cloudu. Neplánované výpadky cloudu vám můžou zachovávat vaše primární data a zálohovaná data. Pokud se rozhodnete nasadit cíl zálohování jako virtuální zařízení (pro účely optimalizace zálohování a obnovení), musíte zajistit, aby se všechna data průběžně zkopírovala do externího záložního umístění. |
| Nasazení fyzického zálohovacího zařízení do stejného stojanu, kde je nainstalované řešení Azure Stack | Nepodporuje se | V současné době nemůžete připojit žádná další zařízení k hornímu počtu přepínačů stojanu, které nejsou součástí původního řešení. |

## <a name="next-steps"></a>Další kroky

V tomto článku najdete obecné pokyny pro ochranu virtuálních počítačů, které jsou nasazené v Azure Stack. Informace o použití služeb Azure k ochraně virtuálních počítačů uživatele najdete v tématu:

 - [Použití Azure Backup k zálohování souborů a aplikací na Azure Stack](https://docs.microsoft.com/azure/backup/backup-mabs-files-applications-azure-stack)
 - [Podpora Azure Backup Server Azure Stack](https://docs.microsoft.com/azure/backup/ ) 
 - [Podpora Azure Site Recovery Azure Stack](https://docs.microsoft.com/azure/site-recovery/)  

Další informace o partnerských produktech, které nabízejí ochranu virtuálního počítače v Azure Stack, najdete [v tématu Ochrana aplikací a dat v Azure Stack](https://azure.microsoft.com/blog/protecting-applications-and-data-on-azure-stack/).
