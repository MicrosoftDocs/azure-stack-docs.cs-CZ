---
title: Ochrana virtuálních počítačů nasazených v centru Azure Stack
description: Naučte se, jak vytvořit plán obnovení pro ochranu virtuálních počítačů nasazených v Azure Stack hub před ztrátou dat a neplánovanými výpadky.
author: mattbriggs
ms.topic: conceptual
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: hectorl
ms.lastreviewed: 3/5/2020
ms.openlocfilehash: 8fe38a530477f080efafcf728e553a8194b74ec6
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97874060"
---
# <a name="protect-vms-deployed-on-azure-stack-hub"></a>Ochrana virtuálních počítačů nasazených v centru Azure Stack

Tento článek vám pomůže s vývojem strategie ochrany dat a zotavení po havárii pro uživatelsky nasazené virtuální počítače IaaS nasazené v centru Azure Stack.

Chcete-li chránit před ztrátou dat a prodlouženým výpadkem, implementujte plán zálohování nebo zotavení po havárii pro uživatelské aplikace a jejich data. Každá aplikace musí být vyhodnocena jako součást komplexní strategie pro provozní kontinuitu a zotavení po havárii (BC/DR) vaší organizace. Dobrým výchozím bodem je [Azure Stack centrum: požadavky na provozní kontinuitu a zotavení po havárii](https://aka.ms/azurestackbcdrconsiderationswp).

## <a name="considerations-for-protecting-iaas-vms"></a>Požadavky na ochranu virtuálních počítačů s IaaS

### <a name="roles-and-responsibilities"></a>Role a povinnosti

Nejprve se ujistěte, že existuje jasné porozumění rolím a odpovědnostem souvisejícím s vlastníky a operátory aplikace v souvislosti s ochranou a obnovením.

Uživatelé zodpovídají za ochranu virtuálních počítačů. Obsluha zodpovídá za udržení online a dobré služby Azure Stack hub. Centrum Azure Stack zahrnuje službu, která zálohuje interní data služby ze služeb **infrastruktury a neobsahuje** žádná uživatelská data, včetně uživatelem vytvořených virtuálních počítačů, účtů úložiště s daty uživatele nebo aplikace nebo uživatelských databází.


| Vlastník/architekt aplikace   | Operátor centra Azure Stack  |
|---    |---    |
| <ul><li>Pomocí principů návrhu cloudu zarovnejte aplikační architekturu.</li></br><li>Modernizovat tradiční aplikace podle potřeby, abyste je připravili pro cloudové prostředí.</li></br><li>Definujte přijatelné RTO a RPO pro aplikaci.</li></br><li>Identifikujte prostředky aplikace a úložiště dat, která je potřeba chránit.</li></br><li>Implementujte metodu obnovení dat a aplikací, která nejlépe odpovídá architektuře aplikací a požadavkům zákazníků.</li></ul>     | <ul><li>Identifikujte cíle pro provozní kontinuitu a zotavení po havárii organizace.</li></br><li>Nasaďte dostatek Azure Stackch instancí centra, aby splňovaly cíle BC/DR organizace.</li></br><li>Navrhněte a provozujte infrastrukturu aplikace/Data Protection.</li></br><li>Poskytněte spravovaná řešení nebo Samoobslužný přístup ke službám ochrany.</li></br><li>Práce s vlastníky a architekty aplikací pro pochopení návrhu aplikace a doporučení strategie ochrany.</li></br><li>Povolte zálohu infrastruktury pro službu retušovací a cloudovou obnovu.</li></ul>    |

## <a name="sourcetarget-combinations"></a>Kombinace zdroje a cíle

Uživatelé, kteří potřebují chránit před výpadkem datového centra nebo lokality, můžou pomocí jiného centra Azure Stack nebo Azure poskytovat vysokou dostupnost nebo rychlé obnovení. V případě primárního a sekundárního umístění mohou uživatelé v rámci dvou prostředí nasazovat aplikace v konfiguraci aktivní/aktivní nebo aktivní/pasivní. Pro méně důležité úlohy můžou uživatelé použít kapacitu v sekundárním umístění k provádění obnovení aplikací na vyžádání ze zálohy.

Jeden nebo více cloudů centra Azure Stack lze nasadit do datového centra. Nasazení aspoň jednoho cloudového centra Azure Stack v jiném datovém centru, aby se zajistila závažná havárie, zajistí, že budete moct úlohy s podporou převzetí služeb při selhání a minimalizovat neplánovaný výpadek. Pokud máte jenom jedno centrum Azure Stack, měli byste zvážit použití veřejného cloudu Azure jako cloudu pro obnovení. Určení místa, kde se vaše aplikace může spustit, bude určeno předpisy pro státní správu, podnikové zásady a přísné požadavky na latenci. Máte flexibilitu při určení vhodného umístění pro obnovení na aplikaci. Například můžete mít aplikace v jednom předplatném, které zálohují data do jiného datového centra a v jiném předplatném, a replikují data do veřejného cloudu Azure.

## <a name="application-recovery-objectives"></a>Cíle obnovení aplikace

Vlastníci aplikací jsou primárně odpovědni za určení množství výpadku a ztráty dat, které může aplikace a organizace tolerovat. Vynásobením přijatelného výpadku a přijatelné ztráty dat můžete vytvořit plán obnovení, který minimalizuje dopad havárie ve vaší organizaci. Pro každou aplikaci Vezměte v úvahu následující skutečnosti:

 - **Plánovaná doba obnovení (RTO)**  
RTO je maximální přijatelná doba, po kterou může být aplikace po incidentu nedostupná. Například RTO 90 minut znamená, že musíte být schopni obnovit aplikaci do běžícího stavu během 90 minut od začátku havárie. Pokud máte malou RTO, můžete zachovat druhé nasazení nepřetržitě běžící na pohotovostním režimu pro ochranu před oblastním výpadkem.
 - **Cíl bodu obnovení (RPO)**  
Cíl bodu obnovení (RPO) je maximální období ztráty dat, které je při havárii přijatelné. Pokud například ukládáte data do jediné databáze, která se zálohuje každou hodinu a nemá žádnou replikaci do jiných databází, můžete přijít o hodinu dat.

Další metrikou je *Střední doba obnovení* (MTTR), což je průměrná doba potřebná k obnovení aplikace po selhání. MTTR je empirická hodnota pro systém. Pokud MTTR překročí RTO, pak selhání v systému způsobí nepřijatelné přerušení v podniku, protože nebude možné obnovit systém v definovaném RTO.

## <a name="protection-options"></a>Možnosti ochrany 

### <a name="backup-restore"></a>Zálohování – obnovení

Zálohování aplikací a datových sad vám umožní rychle se zotavit z výpadků kvůli poškození dat, náhodnému odstranění nebo katastrofám. Pro aplikace založené na virtuálním počítači s IaaS můžete použít agenta hosta k ochraně dat aplikací, konfigurace operačního systému a dat uložených na svazcích. 

#### <a name="backup-using-in-guest-agent"></a>Zálohování pomocí agenta hostovaného v hostovi

Zálohování virtuálního počítače pomocí agenta hostovaného operačního systému obvykle zahrnuje zachytávání konfigurace operačního systému, souborů, složek, disků, binárních souborů aplikace nebo dat aplikací. 

Obnovování aplikace od agenta vyžaduje ruční opětovné vytvoření virtuálního počítače, instalaci operačního systému a instalaci agenta hosta. V tomto okamžiku je možné data obnovit do hostovaného operačního systému nebo přímo do aplikace.

#### <a name="backup-using-disk-snapshot-for-stopped-vms"></a>Zálohování pomocí snímku disku pro zastavené virtuální počítače

Záložní produkty můžou chránit konfiguraci virtuálních počítačů IaaS a disky připojené k zastavenému virtuálnímu počítači. Používejte záložní produkty, které se integrují s rozhraními API centra Azure Stack k zaznamenání konfigurace virtuálních počítačů a vytváření snímků disku. Pokud je možné naplánovat výpadky aplikace, před spuštěním pracovního postupu zálohování se ujistěte, že je virtuální počítač v zastaveném stavu.  

#### <a name="backup-using-disk-snapshot-snapshot-for-running-vms"></a>Zálohování pomocí snímku snímku disku pro běžící virtuální počítače

> [!Important]  
> Použití snímků disku v současnosti není pro virtuální počítač v běžícím stavu podporováno. Vytvoření snímku disku připojeného k běžícímu virtuálnímu počítači může snížit výkon nebo ovlivnit dostupnost operačního systému nebo aplikace ve virtuálním počítači. Doporučujeme použít agenta hosta k ochraně aplikace v případě, že plánované výpadky nepatří mezi možnosti. 

### <a name="vms-in-a-scale-set-or-availability-set"></a>Virtuální počítače ve skupině škálování nebo sady dostupnosti

Virtuální počítače v sadě škálování nebo skupině dostupnosti považované za dočasné prostředky by se neměly zálohovat na úrovni virtuálního počítače, zejména pokud je aplikace Bezstavová. Pro stavové aplikace nasazené v sadě škálování nebo skupině dostupnosti Zvažte ochranu aplikačních dat (například databáze nebo svazku ve fondu úložiště). 

### <a name="replicationmanual-failover"></a>Replikace/ruční převzetí služeb při selhání

Pro aplikace, které vyžadují minimální ztrátu dat nebo minimální prostoje, je možné replikaci dat povolit na úrovni hostovaného operačního systému nebo aplikace a replikovat data do jiného umístění. Některé aplikace, například Microsoft SQL Server, nativně podporují replikaci. Pokud aplikace nepodporuje replikaci, můžete použít software v hostovaném operačním systému k replikaci disků nebo partnerského řešení, které se nainstaluje jako agent v hostovaném operačním systému.

S tímto přístupem se aplikace nasadí v jednom cloudu a data se replikují do jiného cloudu místně nebo do Azure. Když se aktivuje převzetí služeb při selhání, musí být aplikace v cíli spuštěná a připojená k replikovaným datům, aby mohla spustit požadavky na údržbu.
 
### <a name="high-availabilityautomatic-failover"></a>Vysoká dostupnost/automatické převzetí služeb při selhání

U bezstavových aplikací, které můžou tolerovat jenom pár sekund nebo minut výpadků, zvažte konfiguraci s vysokou dostupností. Aplikace s vysokou dostupností jsou navržené tak, aby se nasadily na více místech aktivní/aktivní topologie, kde můžou všechny instance považovat požadavky. U místních hardwarových chyb implementuje infrastruktura centra Azure Stack vysoké dostupnosti ve fyzické síti pomocí dvou horních přepínačů stojanu. V případě chyb na úrovni výpočtů používá Azure Stack hub více uzlů v jednotce škálování a automaticky převzetí služeb při selhání virtuálního počítače. Na úrovni virtuálního počítače můžete použít sady škálování nebo virtuální počítače ve skupině dostupnosti, abyste zajistili, že selhání uzlu nevezmou aplikaci. Stejná aplikace by se musela nasadit do sekundárního umístění ve stejné konfiguraci. Aby byla aplikace aktivní/aktivní, můžete k přímému nasměrování požadavků na všechny dostupné instance použít nástroj pro vyrovnávání zatížení nebo DNS.

### <a name="no-recovery"></a>Bez obnovení

Některé aplikace ve vašem prostředí nemusí mít ochranu před neplánovanými výpadky nebo ztrátou dat. Například virtuální počítače používané pro vývoj a testování obvykle není nutné obnovovat. Pro aplikaci nebo datovou sadu je vaše rozhodnutí bez ochrany.

## <a name="recommended-topologies"></a>Doporučené topologie

Důležité informace pro nasazení centra Azure Stack:

|     | Doporučení | Komentáře |
|-------------------------------------------------------------------------------------------------|-----------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|**Zálohování nebo obnovení virtuálních počítačů do externího cíle zálohování, který je už ve vašem datovém centru nasazený**| Doporučeno | Využijte výhod stávající infrastruktury zálohování a provozních dovedností. Nezapomeňte změnit velikost infrastruktury zálohování, aby byla připravená chránit další instance virtuálních počítačů. Ujistěte se, že infrastruktura zálohování není v těsné blízkosti vašeho zdroje. Virtuální počítače můžete obnovit do zdrojového Azure Stackového centra, do sekundární instance centra Azure Stack nebo do Azure. |
|**Zálohování nebo obnovení virtuálních počítačů do externího cíle zálohování vyhrazeného pro Azure Stack hub**| Doporučeno | Můžete zakoupit novou infrastrukturu zálohování nebo zřídit vyhrazenou infrastrukturu zálohování pro centrum Azure Stack. Ujistěte se, že infrastruktura zálohování není v těsné blízkosti vašeho zdroje. Virtuální počítače můžete obnovit do zdrojového Azure Stackového centra, do sekundární instance centra Azure Stack nebo do Azure. |
|**Zálohování a obnovení virtuálních počítačů přímo do globálního Azure nebo do poskytovatele důvěryhodné služby**| Doporučeno | Pokud budete vyhovovat vašim požadavkům na ochranu osobních údajů a zákonné požadavky, můžete zálohy ukládat v globálním Azure nebo v případě poskytovatele důvěryhodné služby. V ideálním případě poskytovatel služeb také používá Azure Stack hub, abyste při obnovení mohli zajistit konzistenci v provozním prostředí. |
|**Replikace virtuálních počítačů nebo jejich převzetí služeb při selhání do samostatné instance centra Azure Stack**| Doporučeno | V případě převzetí služeb při selhání je potřeba mít druhý Cloud centra Azure Stack plně funkční, takže se můžete vyhnout výpadkům rozšířené aplikace. |
|**Replikace a převzetí služeb při selhání virtuálního počítače přímo do Azure nebo do poskytovatele důvěryhodné služby**| Doporučeno | Pokud můžete splnit požadavky na ochranu osobních údajů a legislativu vašich dat, můžete replikovat data do globálního Azure nebo do poskytovatele důvěryhodné služby. V ideálním případě poskytovatel služeb také používá Azure Stack hub, takže po převzetí služeb při selhání získáte konzistenci v provozním prostředí. |
|**Nasaďte cíl zálohování na stejném Azure Stackovém centru, které taky hostují všechny aplikace chráněné stejným cílem zálohování.**| Samostatný cíl: nedoporučuje se </br> Cíl, který replikuje data zálohování externě: doporučeno | Pokud se rozhodnete nasadit zálohovací zařízení do centra Azure Stack (pro účely optimalizace provozu obnovení), musíte zajistit, aby se všechna data průběžně zkopírovala do externího umístění zálohy. |
|**Nasazení fyzického zálohovacího zařízení do stejného racku, kde je nainstalované řešení centra Azure Stack**| Nepodporováno | V současné době nemůžete připojit žádná další zařízení k hornímu počtu přepínačů stojanu, které nejsou součástí původního řešení. |

## <a name="next-steps"></a>Další kroky

V tomto článku najdete obecné pokyny pro ochranu virtuálních počítačů, které jsou nasazené v centru Azure Stack. Informace o použití služeb Azure k ochraně virtuálních počítačů uživatele najdete v tématu:

- [Azure Stack IaaS – část 4 – Ochrana vašich věcí](https://azure.microsoft.com/blog/azure-stack-iaas-part-four/)
- [Důležité informace týkající se provozní kontinuity a zotavení po havárii](https://aka.ms/azurestackbcdrconsiderationswp)

### <a name="azure-backup-server"></a>Server Azure Backup
 - [Použití Azure Backup k zálohování souborů a aplikací v centru Azure Stack](/azure/backup/backup-mabs-files-applications-azure-stack)
 - [Azure Backup Server podpora centra Azure Stack](/azure/backup/ ) 
 
 ### <a name="azure-site-recovery"></a>Azure Site Recovery
 - [Azure Site Recovery podpora centra Azure Stack](/azure/site-recovery/)  
 
 ### <a name="partner-products"></a>Partnerské produkty
 - [Datový list ekosystému partnerského serveru pro integraci Datacenter centra Azure Stack](https://aka.ms/azurestackbcdrpartners)
