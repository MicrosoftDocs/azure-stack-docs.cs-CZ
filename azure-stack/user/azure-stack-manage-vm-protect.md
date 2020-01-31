---
title: Ochrana virtuálních počítačů nasazených v centru Azure Stack
description: Naučte se, jak vytvořit plán obnovení pro ochranu virtuálních počítačů nasazených v Azure Stack hub před ztrátou dat a neplánovanými výpadky.
author: mattbriggs
ms.topic: conceptual
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: hectorl
ms.lastreviewed: 3/19/2018
ms.openlocfilehash: fdcae656e142f8981a41875daada938b5ce360ab
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2020
ms.locfileid: "76884721"
---
# <a name="protect-vms-deployed-on-azure-stack-hub"></a>Ochrana virtuálních počítačů nasazených v centru Azure Stack

Tento článek slouží jako vodítko pro vývoj plánu ochrany virtuálních počítačů, které uživatelé nasazují v Azure Stack hub.


Chcete-li chránit před ztrátou dat a neplánovanými výpadky, je nutné implementovat plán zálohování nebo zotavení po havárii pro uživatelské aplikace a jejich data. Tento plán může být pro každou aplikaci jedinečný, ale dodržuje rámec vytvořený v komplexní strategii pro provozní kontinuitu a zotavení po havárii (BC/DR) vaší organizace. Dobrým výchozím bodem je [Azure Stack centrum: požadavky na provozní kontinuitu a zotavení po havárii](https://aka.ms/azurestackbcdrconsiderationswp).

## <a name="azure-stack-hub-infrastructure-recovery"></a>Obnovení infrastruktury centra Azure Stack

Uživatelé zodpovídají za ochranu svých virtuálních počítačů nezávisle na službách infrastruktury centra Azure Stack.

Plán obnovení pro služby infrastruktury centra Azure Stack **nezahrnuje obnovení** virtuálních počítačů uživatele, účtů úložiště nebo databází. Jako vlastník aplikace zodpovídáte za implementaci plánu obnovení pro vaše aplikace a data.

Pokud je Cloud centra Azure Stack v režimu offline delší dobu nebo trvale neobnovitelné, je nutné mít k dispozici plán obnovení, který:

* Zajišťuje minimální prostoje.
* Udržuje důležité virtuální počítače, například databázové servery, které jsou spuštěny.
* Umožňuje aplikacím zachovat požadavky uživatelů na údržbu.

Operátor cloudu centra Azure Stack zodpovídá za vytvoření plánu obnovení pro základní infrastrukturu a služby centra Azure Stack. Další informace najdete v tématu [obnovení z závažné ztráty dat](../operator/azure-stack-backup-recover-data.md).

## <a name="considerations-for-iaas-vms"></a>Předpoklady pro virtuální počítače s IaaS
Operační systém nainstalovaný na virtuálním počítači IaaS omezuje produkty, které můžete použít k ochraně dat, která obsahuje. Pro virtuální počítače s IaaS založené na Windows můžete k ochraně dat použít Microsoft a partnerské produkty. U virtuálních počítačů s IaaS se systémem Linux je jedinou možností použití partnerských produktů. [Všechny partnery BC/Dr s ověřenými produkty pro centrum Azure Stack najdete v tomto datovém listu](https://aka.ms/azurestackbcdrpartners).

## <a name="sourcetarget-combinations"></a>Kombinace zdroje a cíle

Každý Cloud centra Azure Stack je nasazený v jednom datovém centru. K obnovení vašich aplikací se vyžaduje samostatné prostředí. Prostředí pro obnovení může být jiný Cloud centra Azure Stack v jiném datovém centru nebo ve veřejném cloudu Azure. Vaše požadavky na svrchovanost a ochranu dat určují prostředí pro obnovení vaší aplikace. Při povolování ochrany pro každou aplikaci máte flexibilitu při výběru konkrétní možnosti obnovení pro každé z nich. Můžete mít aplikace v jednom předplatném, které zálohují data do jiného datového centra. V jiném předplatném můžete replikovat data do veřejného cloudu Azure.

Naplánujte strategii zálohování a obnovení po havárii pro každou aplikaci a určete cíl pro každou aplikaci. Plán obnovení pomáhá vaší organizaci správně nastavit kapacitu úložiště povinnou pro místní a využití projektů ve veřejném cloudu.

|  | Globální Azure | Azure Stack centrum nasazené do Datacenter CSP a provozovaný CSP | Azure Stack centrum nasazené do Datacenter zákazníka a provozovaný zákazníkem |
|------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------|
| **Azure Stack centrum nasazené do Datacenter CSP a provozovaný CSP** | Virtuální počítače uživatele se nasazují do centra Azure Stack ovládaného CSP.<br><br>Virtuální počítače uživatelů se obnovily ze zálohy nebo převzaly služby při selhání přímo do Azure. | CSP pracuje s primárními a sekundárními instancemi centra Azure Stack ve vlastních datacentrech.<br><br>Virtuální počítače uživatele jsou obnoveny nebo převzetí služeb při selhání mezi těmito dvěma instancemi centra Azure Stack. | CSP funguje Azure Stack hub v primární lokalitě.<br><br>Datacenter zákazníka je cíl obnovení nebo převzetí služeb při selhání. |
| **Azure Stack centrum nasazené do Datacenter zákazníka a provozovaný zákazníkem** | Virtuální počítače uživatele se nasazují do centra Azure Stack ovládaného zákazníkem.<br><br>Virtuální počítače uživatelů se obnovily ze zálohy nebo převzaly služby při selhání přímo do Azure. | Zákazník provozuje Azure Stack centrum v primární lokalitě.<br><br>Datacentrum CSP je cíl obnovení nebo převzetí služeb při selhání. | Zákazník provozuje primární a sekundární instance centra Azure Stack ve vlastních datacentrech.<br><br>Virtuální počítače uživatele jsou obnoveny nebo převzetí služeb při selhání mezi těmito dvěma instancemi centra Azure Stack. |

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

Nejběžnějším schématem ochrany pro aplikace založené na virtuálním počítači je použití zálohovacího softwaru. Zálohování virtuálního počítače obvykle zahrnuje operační systém, konfiguraci operačního systému, binární soubory aplikace a data aplikací. Zálohy se vytvoří pomocí snímku svazků, disků nebo celého virtuálního počítače. S centrem Azure Stack máte flexibilitu při zálohování z kontextu hostovaného operačního systému nebo z rozhraní API pro úložiště Azure Stack centra a výpočetní prostředí. Centrum Azure Stack nepodporuje přebírání záloh na úrovni hypervisoru.
 
![Backup – ReSTOR](media/azure-stack-manage-vm-backup/vm_backupdataflow_03.png)

Obnovení aplikace vyžaduje obnovení jednoho nebo více virtuálních počítačů do stejného cloudu nebo do nového cloudu. Můžete cílit na Cloud ve vašem datovém centru nebo ve veřejném cloudu. Zvolený Cloud je zcela v rámci vašeho ovládacího prvku a je založen na požadavcích na ochranu osobních údajů a suverenity dat.

 - RTO: výpadek měřený v hodinách
 - RPO: proměnlivá ztráta dat (v závislosti na četnosti zálohování)
 - Topologie nasazení: aktivní/pasivní

#### <a name="planning-your-backup-strategy"></a>Plánování strategie zálohování

Plánování strategie zálohování a definování požadavků na škálování začíná vystanovením počtu instancí virtuálních počítačů, které je potřeba chránit. Zálohování všech virtuálních počítačů napříč všemi servery v prostředí je běžná strategie. U Azure Stackového centra ale existuje několik virtuálních počítačů, které je potřeba zálohovat. Například virtuální počítače v rámci škálované sady se považují za dočasné prostředky, které můžou pocházet a jít, někdy bez upozornění. Veškerá trvalá data, která je třeba chránit, se ukládají do samostatného úložiště, jako je databáze nebo úložiště objektů.

Důležité informace pro zálohování virtuálních počítačů v centru Azure Stack:

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

 - RTO: výpadek měřený v minutách
 - RPO: proměnlivá ztráta dat (v závislosti na frekvenci replikace)
 - Topologie nasazení: aktivní/pasivní – úsporný režim
 
### <a name="high-availabilityautomatic-failover"></a>Vysoká dostupnost/automatické převzetí služeb při selhání

U aplikací, kde může vaše firma tolerovat jenom pár sekund nebo minut výpadku a minimální ztrátu dat, zvažte konfiguraci s vysokou dostupností. Vysoce dostupné aplikace jsou navržené tak, aby se rychle a automaticky obnovily z chyb. U místních hardwarových chyb implementuje infrastruktura centra Azure Stack vysoké dostupnosti ve fyzické síti pomocí dvou horních přepínačů stojanu. V případě chyb na úrovni výpočetní služby používá rozbočovač Azure Stack více uzlů v jednotce škálování. Na úrovni virtuálního počítače můžete použít sady škálování v kombinaci s doménami selhání k zajištění, že selhání uzlu nevezmou aplikaci.

V kombinaci se sadami škálování bude vaše aplikace muset podporovat vysokou dostupnost nativně nebo podporovat používání softwaru clusteringu. Microsoft SQL Server například podporuje vysokou dostupnost nativně pro databáze pomocí režimu synchronního potvrzování. Pokud však můžete podporovat pouze asynchronní replikaci, dojde ke ztrátě dat. Aplikace je také možné nasadit do clusteru s podporou převzetí služeb při selhání, ve kterém software clusteringu zpracovává automatické převzetí služeb při selhání aplikace.

Při použití tohoto přístupu je aplikace aktivní jenom v jednom cloudu, ale software se nasadí do několika cloudů. Ostatní cloudy jsou v úsporném režimu připravené ke spuštění aplikace při aktivaci převzetí služeb při selhání.

 - RTO: výpadek měřený v sekundách
 - RPO: minimální ztráta dat
 - Topologie nasazení: aktivní/aktivní – úsporný režim

### <a name="fault-tolerance"></a>Odolnost proti chybám

Dostupnost fyzické redundance a dostupnosti služby infrastruktury Azure Stack k dispozici pouze proti chybám a selháním na úrovni hardwaru, jako je například disk, zdroj napájení, síťový port nebo uzel. Pokud ale vaše aplikace musí být vždycky k dispozici a nemůže nikdy ztratit žádná data, je třeba v aplikaci implementovat odolnost proti chybám nativně nebo použít další software k povolení odolnosti proti chybám.

Nejdřív je potřeba zajistit, aby virtuální počítače aplikace byly nasazeny pomocí sad škálování pro ochranu proti selháním na úrovni uzlu. Aby bylo možné chránit před cloudem offline, je nutné, aby byla stejná aplikace nasazena do jiného cloudu, aby mohla i nadále obsluhovat požadavky bez přerušení. Tento model se obvykle označuje jako nasazení typu aktivní-aktivní.

Mějte na paměti, že každý Cloud centra Azure Stack je nezávisle na sobě navzájem závislý, takže se cloudy vždy považují za aktivní z hlediska infrastruktury. V takovém případě se několik aktivních instancí aplikace nasadí do jednoho nebo víc aktivních cloudů.

 - RTO: žádné výpadky
 - RPO: žádná ztráta dat
 - Topologie nasazení: aktivní/aktivní

### <a name="no-recovery"></a>Bez obnovení

Některé aplikace ve vašem prostředí nemusí mít ochranu před neplánovanými výpadky nebo ztrátou dat. Například virtuální počítače používané pro vývoj a testování obvykle není nutné obnovovat. Je vaše rozhodnutí dělat bez ochrany pro aplikaci nebo konkrétní virtuální počítač. Centrum Azure Stack nenabízí zálohování nebo replikaci virtuálních počítačů z základní infrastruktury. Podobně jako v Azure, musíte se k ochraně každého virtuálního počítače v každém z vašich předplatných přihlásit.

 - RTO: neobnovitelné
 - RPO: úplná ztráta dat

## <a name="recommended-topologies"></a>Doporučené topologie

Důležité informace pro nasazení centra Azure Stack:

|     | Doporučení | Komentáře |
|-------------------------------------------------------------------------------------------------|-----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Zálohování nebo obnovení virtuálních počítačů do externího cíle zálohování, který je už ve vašem datovém centru nasazený | Doporučené | Využijte výhod stávající infrastruktury zálohování a provozních dovedností. Nezapomeňte změnit velikost infrastruktury zálohování, aby byla připravená chránit další instance virtuálních počítačů. Ujistěte se, že infrastruktura zálohování není v těsné blízkosti vašeho zdroje. Virtuální počítače můžete obnovit do zdrojového Azure Stackového centra, do sekundární instance centra Azure Stack nebo do Azure. |
| Zálohování nebo obnovení virtuálních počítačů do externího cíle zálohování vyhrazeného pro Azure Stack hub | Doporučené | Můžete zakoupit novou infrastrukturu zálohování nebo zřídit vyhrazenou infrastrukturu zálohování pro centrum Azure Stack. Ujistěte se, že infrastruktura zálohování není v těsné blízkosti vašeho zdroje. Virtuální počítače můžete obnovit do zdrojového Azure Stackového centra, do sekundární instance centra Azure Stack nebo do Azure. |
| Zálohování a obnovení virtuálních počítačů přímo do globálního Azure nebo do poskytovatele důvěryhodné služby | Doporučené | Pokud budete vyhovovat vašim požadavkům na ochranu osobních údajů a zákonné požadavky, můžete zálohy ukládat v globálním Azure nebo v případě poskytovatele důvěryhodné služby. V ideálním případě poskytovatel služeb také používá Azure Stack hub, abyste při obnovení mohli zajistit konzistenci v provozním prostředí. |
| Replikace virtuálních počítačů nebo jejich převzetí služeb při selhání do samostatné instance centra Azure Stack | Doporučené | V případě převzetí služeb při selhání je potřeba mít druhý Cloud centra Azure Stack plně funkční, takže se můžete vyhnout výpadkům rozšířené aplikace. |
| Replikace a převzetí služeb při selhání virtuálního počítače přímo do Azure nebo do poskytovatele důvěryhodné služby | Doporučené | Pokud můžete splnit požadavky na ochranu osobních údajů a legislativu vašich dat, můžete replikovat data do globálního Azure nebo do poskytovatele důvěryhodné služby. V ideálním případě poskytovatel služeb také používá Azure Stack hub, takže po převzetí služeb při selhání získáte konzistenci v provozním prostředí. |
| Nasazení cíle zálohování do stejného cloudu centra Azure Stack s daty aplikace | Nedoporučuje se | Neukládejte zálohy do stejného cloudu centra Azure Stack. Neplánované výpadky cloudu vám můžou zachovávat vaše primární data a zálohovaná data. Pokud se rozhodnete nasadit cíl zálohování jako virtuální zařízení (pro účely optimalizace zálohování a obnovení), musíte zajistit, aby se všechna data průběžně zkopírovala do externího záložního umístění. |
| Nasazení fyzického zálohovacího zařízení do stejného racku, kde je nainstalované řešení centra Azure Stack | Nepodporováno | V současné době nemůžete připojit žádná další zařízení k hornímu počtu přepínačů stojanu, které nejsou součástí původního řešení. |

## <a name="next-steps"></a>Další kroky

V tomto článku najdete obecné pokyny pro ochranu virtuálních počítačů, které jsou nasazené v centru Azure Stack. Informace o použití služeb Azure k ochraně virtuálních počítačů uživatele najdete v tématu:

- [Důležité informace týkající se provozní kontinuity a zotavení po havárii](https://aka.ms/azurestackbcdrconsiderationswp)

### <a name="azure-backup-server"></a>Azure Backup Server
 - [Použití Azure Backup k zálohování souborů a aplikací v centru Azure Stack](https://docs.microsoft.com/azure/backup/backup-mabs-files-applications-azure-stack)
 - [Azure Backup Server podpora centra Azure Stack](https://docs.microsoft.com/azure/backup/ ) 
 
 ### <a name="azure-site-recovery"></a>Azure Site Recovery
 - [Azure Site Recovery podpora centra Azure Stack](https://docs.microsoft.com/azure/site-recovery/)  
 
 ### <a name="partner-products"></a>Partnerské produkty
 - [Datový list ekosystému partnerského serveru pro integraci Datacenter centra Azure Stack](https://aka.ms/azurestackbcdrpartners)

Pokud se chcete dozvědět víc o partnerských produktech, které nabízejí ochranu virtuálních počítačů v Azure Stackovém centru, přečtěte si téma [Ochrana aplikací a dat v centru Azure Stack](https://azure.microsoft.com/blog/protecting-applications-and-data-on-azure-stack/).
