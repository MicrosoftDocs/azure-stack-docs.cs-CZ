---
title: Chránit virtuální počítače nasazené ve službě Azure Stack | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit plán obnovení, který chcete chránit virtuální počítače nasazené ve službě Azure Stack proti ztrátě informací a neplánovaných výpadků.
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
ms.date: 05/06/2019
ms.author: mabrigg
ms.reviewer: hectorl
ms.lastreviewed: 3/19/2018
ms.openlocfilehash: 9f8fe959ca200b7000df65b6826a103535aac92a
ms.sourcegitcommit: eccbd0098ef652919f357ef6dba62b68abde1090
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/01/2019
ms.locfileid: "67492407"
---
# <a name="protect-vms-deployed-on-azure-stack"></a>Chránit virtuální počítače nasazené ve službě Azure Stack

Použijte tento článek jako vodítko k vývoji plánu ochrany virtuálních počítačů (VM), které vaši uživatelé nasazení ve službě Azure Stack.


Pro ochranu před únikem informací a neplánovaných prostojů, budete muset implementovat plán obnovení zálohování nebo zotavení po havárii pro uživatele aplikace a jejich data. Tento plán může být pro každou aplikaci jedinečná ale následuje framework stanovené vaší organizace komplexní obchodní kontinuity podnikových procesů a strategii (BC/zotavení po Havárii) zotavení po havárii. Dobrým výchozím bodem je [Azure Stack: Důležité informace týkající se obchodní kontinuity podnikových procesů a zotavení po havárii](https://aka.ms/azurestackbcdrconsiderationswp).

## <a name="azure-stack-infrastructure-recovery"></a>Obnovení infrastruktury Azure stacku

Uživatele odpovědni za ochranu svých virtuálních počítačích samostatně od služby infrastruktury Azure stacku.

Plán obnovení pro služby infrastruktury Azure stacku **nemá** zahrnují obnovení virtuálních počítačů uživateli, účty úložiště nebo databáze. Jako vlastníka aplikace zodpovídáte za implementaci plán obnovení pro vaše aplikace a data.

Pokud cloudu služby Azure Stack je offline po delší dobu nebo trvale Neopravitelná, musíte mít v plánu obnovení místo, které:

* Zajišťuje minimálními prostoji.
* Udržuje kritických virtuálních počítačů, jako jsou databázové servery.
* Umožňuje aplikacím zachovat zpracování požadavků uživatele.

Operátor cloudu služby Azure Stack je zodpovědná za vytvoření plánu obnovení pro základní infrastrukturu Azure Stack a služby. Další informace najdete v tématu [obnovit ze ztráty dat](../operator/azure-stack-backup-recover-data.md).

## <a name="considerations-for-iaas-vms"></a>Důležité informace týkající se virtuálních počítačů IaaS
Operační systém nainstalovaný na virtuálním počítači IaaS omezuje produktů, které vám pomůže chránit data, která obsahuje. Pro Windows je založené na virtuální počítače IaaS, můžete k ochraně dat produkty Microsoftu a jeho partnerů. Pro Linux založené na virtuální počítače IaaS, jedinou možností je použití partnerským produktům. Odkazovat na [tento datový list pro všechny partnery BC/zotavení po Havárii s ověřené produkty pro službu Azure Stack](https://aka.ms/azurestackbcdrpartners).

## <a name="sourcetarget-combinations"></a>Zdroj/cíl kombinace

Každý cloud služby Azure Stack je nasazený na jednom datacentru. Samostatné prostředí je potřeba obnovit své aplikace. Obnovení prostředí může být jiné cloudové služby Azure Stack v jiném datovém centru nebo veřejného cloudu Azure. Suverenita dat a požadavky na ochranu dat zjistit obnovení prostředí pro vaši aplikaci. Jako povolíte ochranu pro každou aplikaci, budete mít volnost ve výběru specifické možnosti obnovení pro každé z nich. Aplikace může mít v jednom předplatném zálohování dat do jiného datového centra. V jiném předplatném se dají replikovat data do veřejného cloudu Azure.

Plánování strategie obnovení zálohování a zotavení po havárii pro každou aplikaci k určení cíle pro každou aplikaci. Plán obnovení pomůže vaší organizaci správné velikosti úložiště kapacita požadovaná místní a projekt spotřebu ve veřejném cloudu.

|  | Global Azure | Azure Stack nasazené v datacentru CSP a provozuje CSP | Azure Stack nasazuje do datového centra zákazníka a provozuje zákaznická |
|------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------|
| **Azure Stack nasazené v datacentru CSP a provozuje CSP** | Virtuální počítače uživatele se nasadí do služby Azure Stack provozované CSP.<br><br>Virtuální počítače uživatele obnovit ze zálohy nebo převzetí služeb při selhání do Azure. | Zprostředkovatel kryptografických služeb pracuje primární a sekundární instance služby Azure Stack v jejich vlastních datových center.<br><br>Virtuální počítače uživatele jsou obnovení nebo převzetí služeb při selhání mezi dvěma instancemi služby Azure Stack. | Zprostředkovatel kryptografických služeb pracuje Azure Stack v primární lokalitě.<br><br>Datovým centrem zákazníka je cíl obnovení nebo převzetí služeb při selhání. |
| **Azure Stack nasazuje do datového centra zákazníka a provozuje zákaznická** | Virtuální počítače uživatele se nasadí do služby Azure Stack provozované zákazníka.<br><br>Virtuální počítače uživatele obnovit ze zálohy nebo převzetí služeb při selhání do Azure. | Zákazník používá Azure Stack v primární lokalitě.<br><br>Datacentra CSP je cíl obnovení nebo převzetí služeb při selhání. | Zákazník funguje primární a sekundární instance služby Azure Stack v jejich vlastních datových center.<br><br>Virtuální počítače uživatele jsou obnovení nebo převzetí služeb při selhání mezi dvěma instancemi služby Azure Stack. |

![Kombinace zdroj cíl](media/azure-stack-manage-vm-backup/vm_backupdataflow_01.png)

## <a name="app-recovery-objectives"></a>Cíle obnovení aplikace

Určete množství výpadky a ztráty dat, které vaše organizace může dovolit, pro každou aplikaci. Kvantifikace výpadky a ztráty dat můžete vytvořit plán obnovení, který minimalizuje vliv po havárii ve vaší organizaci. Pro každou aplikaci vezměte v úvahu:

 - **Plánovaná doba obnovení (RTO)**  
RTO je maximální přijatelná dobu, která aplikace může být k dispozici po incidentu. RTO 90 minut například znamená, že musíte být schopni obnovit aplikaci do funkčního stavu během 90 minut od začátku výpadku. Pokud máte nízké hodnoty RTO, bude asi třeba udržovat k ochraně před oblastním výpadkem v nepřetržitém provozu druhé nasazení.
 - **Cíl bodu obnovení (RPO)**  
Cíl bodu obnovení je maximální období ztráty dat, které je při havárii přijatelné. Například pokud ukládáte data do izolované databáze, který se zálohuje každou hodinu a nemá žádné replikace s jinými databázemi, můžete ztratit až na jednu hodinu data.

Plánovaná doba obnovení a cíl bodu obnovení jsou obchodní požadavky. Proveďte posouzení rizik k definování RTO a RPO aplikace.

Je další metriku **průměrný čas potřebný k obnovení** (MTTR), což je průměrná doba, která je potřebná pro obnovení po selhání aplikace. MTTR je empirický hodnota pro systém. Pokud MTTR překročí RTO, pak selhání v systému způsobí nepřijatelné narušení činnosti firmy protože nebude možné obnovit systém v definovaném čase RTO.

### <a name="backup-restore"></a>Obnovit zálohování

Nejběžnější schéma ochrany pro aplikace založené na virtuálním počítači je použít zálohovací software. Zálohování virtuálních počítačů obvykle obsahuje operační systém, konfigurace operačního systému, binární soubory aplikace a data aplikací. Zálohy jsou vytvořené pořizování snímku svazků, disků nebo celý virtuální počítač. Pomocí služby Azure Stack flexibilně zálohování z v rámci hostovaného operačního systému nebo z úložiště služby Azure Stack a výpočetní rozhraní API. Azure Stack nepodporuje pořízení zálohy na úrovni hypervisoru.
 
![Zálohování restor](media/azure-stack-manage-vm-backup/vm_backupdataflow_03.png)

Obnovení aplikace vyžaduje obnovení jednoho nebo více virtuálních počítačů na stejném cloudu nebo do nového cloudu. Zacílení na cloud ve vašem datovém centru nebo ve veřejném cloudu. V cloudu, kterou zvolíte je zcela v rámci ovládacího prvku a je na základě dat o ochraně osobních údajů a suverenity požadavků.

 - RTO: Výpadek měřeno v hodinách
 - RPO: Ztráta dat proměnných (v závislosti na četnosti zálohování)
 - Topologie nasazení: Aktivní/pasivní vysoká dostupnost

#### <a name="planning-your-backup-strategy"></a>Plánování strategie zálohování

Plánování strategie zálohování a definování požadavků na škálování začíná kvantifikace počet instancí virtuálních počítačů, které mají být chráněny. Běžné strategie zálohování všechny virtuální počítače napříč všemi servery v prostředí je. Pomocí služby Azure Stack, existuje však několik virtuálních počítačů, které je potřeba zálohovat. Například virtuální počítače ve škálovací sadě se považují za dočasné prostředky, které můžou pocházet a přejít, někdy bez předchozího upozornění. Trvalá data, kterou je potřeba chránit je uložen v oddělené úložiště, jako jsou databáze nebo objektu úložiště.

Důležité informace pro zálohování virtuálních počítačů ve službě Azure Stack:

 - **Kategorizace**
    - Vezměte v úvahu modelu, ve kterém uživatelé přihlásit se k zálohování virtuálních počítačů.
    - Definujte obnovení smlouvy o úrovni služeb (SLA) na základě priority aplikace nebo o dopad na firmu.
 - **Škálování**
    - Při zavádění velký počet nových virtuálních počítačů (Pokud je nutné zálohování), zvažte postupný zálohy.
    - Vyzkoušejte zálohování produktů, které můžete efektivně zachytit a přenést data záloh, chcete-li minimalizovat obsahu prostředku v řešení.
    - Vyzkoušejte zálohování produktů, které efektivně ukládat zálohovaná data pomocí přírůstkových nebo rozdílové zálohy minimalizovat nutnost pro úplné zálohování ve všech virtuálních počítačích v prostředí.
 - **Obnovení**
    - Zálohování produktů můžete obnovit virtuální disky, data aplikací v rámci stávajícího virtuálního počítače, nebo celý prostředek virtuálního počítače a přidruženými virtuálními disky. Obnovení schéma, které potřebujete závisí na tom, jak máte v plánu obnovení aplikace. Například může být jednodušší opětovné nasazení systému SQL server ze šablony a potom obnovte databáze místo obnovení celého virtuálního počítače nebo sadu virtuálních počítačů.

### <a name="replicationmanual-failover"></a>Replikace nebo ruční převzetí služeb při selhání

Alternativní přístup k podpoře vysoké dostupnosti je vaše aplikace, virtuální počítače replikovat do jiného cloudu a Spolehněte se na ruční převzetí služeb při selhání. Replikace z operačního systému, binární soubory aplikace a data aplikací můžete udělat na úrovni virtuálního počítače nebo na úrovni hostovaného operačního systému. Převzetí služeb při selhání se spravuje pomocí další software, který není součástí aplikace.

S tímto přístupem aplikace je nasazena v jeden cloud a jeho virtuální počítač se replikuje v cloudu. Pokud převzetí služeb při selhání se aktivuje, sekundární virtuální počítače potřeba zapnutý v druhé cloudu. V některých případech převzetí služeb při selhání vytvoří disky virtuálních počítačů a připojí k nim. Tento proces může trvat dlouhou dobu pro dokončení, zejména s vícevrstvé aplikace, která vyžaduje konkrétní spouštěcí sekvenci. Může to mít také kroky, které je nutné provést předtím, než bude aplikace připravená spustit požadavky obsluhy.

![Replikace ruční převzetí služeb při selhání](media/azure-stack-manage-vm-backup/vm_backupdataflow_02.png)

 - RTO: Prostoje v minutách
 - RPO: Ztráta dat proměnných (v závislosti na četnosti replikací)
 - Topologie nasazení: Neslo aktivní/pasivní vysoká dostupnost
 
### <a name="high-availabilityautomatic-failover"></a>Vysoká dostupnost a automatické převzetí služeb při selhání

Pro aplikace, kde vaši firmu můžete pouze tolerovat pár sekund nebo minut výpadky a ztráty dat minimální vezměte v úvahu konfiguraci s vysokou dostupností. Vysoká dostupnost aplikace jsou navržené tak rychle a automaticky zotavení z chyb. Pro místní hardwarových chyb infrastruktura Azure stacku implementuje vysokou dostupnost v rámci fyzické sítě pomocí dvou horní rack přepínače. Azure Stack pro výpočetní úrovně chyby, používá několik uzlů v jednotce škálování. Na úrovni virtuálních počítačů vám pomůže škálovací sady v kombinaci s doménami selhání zajistěte, aby selhání uzlů nevyřídí vaší aplikace.

V kombinaci se škálovacími sadami musíte aplikace nativně podporují vysokou dostupnost nebo nepodporují clustering softwaru. Například Microsoft SQL Server podporuje vysokou dostupnost nativně pro databáze pomocí režim synchronního potvrzování. Ale pokud se podporuje pouze asynchronní replikaci, pak bude ztrátu některých dat. Aplikace je také možné nasadit do clusteru převzetí služeb při selhání, kde software clusteru zpracovává automatické převzetí služeb při selhání aplikace.

Tento přístup aplikace je aktivní pouze v jeden cloud, ale bude software nasazen do několika cloudy. Jiných cloudech jsou v pohotovostní režim jste připravení začít aplikaci při aktivaci převzetí služeb při selhání.

 - RTO: Výpadku v sekundách
 - RPO: Minimální únikem informací
 - Topologie nasazení: Neslo aktivní/aktivní

### <a name="fault-tolerance"></a>Odolnost proti chybám

Azure Stack fyzické redundanci a dostupnost služeb infrastruktury pouze ochranu proti úrovně chyby nebo selhání hardwaru jako je například disk, napájení, síťový port nebo uzel. Nicméně pokud vaše aplikace musí mít vždycky k dispozici a nikdy ke ztrátě všech dat, potřebujete odolnost proti chybám nativně ve vaší aplikaci nebo používat další software umožňující odolnost proti chybám.

Nejprve je potřeba zajistit aplikaci, kterou jsou virtuální počítače nasazené pomocí škálovací sady pro ochranu před výpadky na úrovni uzlu. Pro ochranu před přechodem do offline režimu cloudu, stejná aplikace musí již být nasazeny na jiný cloud tak může pokračovat zpracování požadavků bez přerušení. Tento model se obvykle označuje aktivní aktivní nasazení.

Uvědomte si, že každý cloud služby Azure Stack je nezávisle na sobě navzájem, takže cloudy jsou vždy považovány za aktivní z hlediska infrastruktury. V takovém případě více aktivních instancích aplikace se nasadí na jeden nebo více cloudů aktivní.

 - RTO: Bez výpadků
 - RPO: Bez ztráty dat.
 - Topologie nasazení: Aktivní/aktivní

### <a name="no-recovery"></a>Bez obnovení

Ochrana proti neplánované výpadky nebo ztrátě dat nemusí potřebovat některé aplikace ve vašem prostředí. Například virtuální počítače používat pro vývoj a testování obvykle není potřeba obnovit. Je vaše rozhodnutí bez ochrany pro konkrétní virtuální počítač nebo aplikace. Azure Stack nenabízí zálohování nebo replikace virtuálních počítačů v základní infrastruktuře. Podobně jako u Azure, budete muset přihlásit k ochranu pro všechny virtuální počítače v každé z vašich předplatných.

 - RTO: Neopravitelné
 - RPO: Úplné ztrátě dat

## <a name="recommended-topologies"></a>Doporučená topologie

Důležité informace týkající se vašeho nasazení Azure stacku:

|     | Doporučení | Komentáře |
|-------------------------------------------------------------------------------------------------|-----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Zálohování a obnovení virtuálních počítačů na externí záložní cíl už nasazená ve vašem datovém centru | Doporučené | Využijte stávající infrastruktury pro zálohování a provozní zkušenosti. Ujistěte se, že pro nastavení velikosti infrastruktury zálohování tak, aby byl připravený k ochraně dalších instancí virtuálních počítačů. Zajistěte, aby infrastruktury zálohování není v těsné blízkosti ke zdroji. Obnovení virtuálních počítačů do zdroje Azure stacku, do sekundární instance služby Azure Stack nebo Azure. |
| Zálohování a obnovení virtuálních počítačů na externí záložní cíl vyhrazený pro Azure Stack | Doporučené | Nové infrastruktury zálohování nebo vyhrazená infrastruktura zálohování zřizování můžete zakoupit pro službu Azure Stack. Zajistěte, aby infrastruktury zálohování není v těsné blízkosti ke zdroji. Obnovení virtuálních počítačů do zdroje Azure stacku, do sekundární instance služby Azure Stack nebo Azure. |
| Zálohování a obnovení virtuálních počítačů přímo do globálního Azure nebo u důvěryhodného poskytovatele | Doporučené | Za předpokladu, ochrany osobních údajů a dodržování legislativních požadavků lze splnit, můžete ukládat zálohy v globálního Azure nebo u důvěryhodného poskytovatele. V ideálním případě poskytovatele služeb běží Azure Stack, při obnovení získáte v provozní prostředí konzistence. |
| Replikace a převzetí služeb při selhání virtuálních počítačů určených k samostatné instanci služby Azure Stack | Doporučené | V případě převzetí služeb při selhání musíte mít druhý cloudu služby Azure Stack plně funkční, takže se vyhnete výpadku rozšířených aplikací. |
| Replikace a převzetí služeb při selhání virtuálních počítačů přímo do Azure nebo u důvěryhodného poskytovatele | Doporučené | Za předpokladu, ochrany osobních údajů a dodržování legislativních požadavků lze splnit, můžete replikovat data do globálního Azure nebo u důvěryhodného poskytovatele. V ideálním případě poskytovatele služeb běží Azure Stack, konzistence získáte v provozní prostředí po převzetí služeb při selhání. |
| Nasazení záložní cíl ve stejném cloudu Azure Stack s daty vaší aplikace | Nedoporučuje se | Vyhněte se ukládání záloh v rámci stejné cloudové službě Azure Stack. Neplánované výpadky cloudu můžete vás zbaví primární data a data záloh. Pokud budete chtít nasadit záložní cíl jako virtuální zařízení (pro účely optimalizace pro zálohování a obnovení), ujistěte se, všechna data zkopírována průběžně do externího umístění zálohy. |
| Nasazení fyzického zálohování zařízení do stejné rack instalaci řešení Azure Stack | Nepodporuje se | V současné době nemůžete připojit všechna zařízení k hornímu okraji přepínače rack, které nejsou součástí v původním řešení. |

## <a name="next-steps"></a>Další postup

Tento článek poskytuje obecné pokyny pro ochranu uživatele virtuální počítače nasazené ve službě Azure Stack. Informace o používání služeb Azure pro ochranu virtuálních počítačů uživatele najdete:

 - [Použití Azure Backup k zálohování souborů a aplikací ve službě Azure Stack](https://docs.microsoft.com/azure/backup/backup-mabs-files-applications-azure-stack)
 - [Podpora Azure Backup serveru pro Azure Stack](https://docs.microsoft.com/azure/backup/ ) 
 - [Azure Site Recovery podporu pro Azure Stack](https://docs.microsoft.com/azure/site-recovery/)  

Další informace o partnerským produktům, které nabízejí ochranu virtuálního počítače ve službě Azure Stack, najdete v tématu [chrání aplikace a data ve službě Azure Stack](https://azure.microsoft.com/blog/protecting-applications-and-data-on-azure-stack/).
