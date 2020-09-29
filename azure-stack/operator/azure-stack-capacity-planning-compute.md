---
title: Kapacita výpočetní kapacity centra Azure Stack
description: Přečtěte si o plánování kapacity pro Azure Stack nasazení centra.
author: IngridAtMicrosoft
ms.topic: conceptual
ms.date: 03/04/2020
ms.author: justinha
ms.reviewer: prchint
ms.lastreviewed: 06/13/2019
ms.openlocfilehash: 4577da446c11f1053372cdf3d6458cd7c90937be
ms.sourcegitcommit: 4af79f4fa2598d57c81e994192c10f8c6be5a445
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89742501"
---
# <a name="azure-stack-hub-compute-capacity"></a>Kapacita výpočetní kapacity centra Azure Stack

[Velikosti virtuálních počítačů](../user/azure-stack-vm-sizes.md) podporované v centru Azure Stack jsou podmnožinou těch, které jsou podporované v Azure. Azure ukládá omezení prostředků spolu s mnoha vektory, aby nedocházelo k přečerpání prostředků (místní server a úroveň služeb). Aniž byste museli zabírat některá omezení pro využití tenanta, prostředí klienta bude mít za následek, že budou prostředky využívaly i jiní klienti. V případě neodchozích síťových přenosů z virtuálního počítače je na Azure Stackm rozbočovači místo šířky pásma, které odpovídá omezením Azure. U prostředků úložiště v Azure Stackovém centru se při omezeních IOPS úložiště vyhnete základní spotřebě prostředků klienty pro přístup k úložišti.

>[!IMPORTANT]
>[Capacity Planner centra Azure Stack](https://aka.ms/azstackcapacityplanner) nebere v úvahu ani nezaručuje výkon IOPS.

## <a name="vm-placement"></a>Umístění virtuálního počítače

Modul umístění centra Azure Stack umístí virtuální počítače klientů mezi dostupné hostitele.

Azure Stack centrum používá při umísťování virtuálních počítačů dva předpoklady. Je k dispozici dostatek paměti na hostiteli pro tento typ virtuálního počítače? A dva jsou virtuální počítače součástí skupiny [dostupnosti](/azure/virtual-machines/windows/manage-availability) nebo se jedná o [sady škálování virtuálních počítačů](/azure/virtual-machine-scale-sets/overview)?

Aby se dosáhlo vysoké dostupnosti produkčních úloh s více virtuálními počítači v centru Azure Stack, virtuální počítače se umístí do skupiny dostupnosti, která je rozšíří napříč více doménami selhání. Doména selhání ve skupině dostupnosti je definována jako jeden uzel v jednotce škálování. Centrum Azure Stack podporuje skupinu dostupnosti s maximálním počtem tří domén selhání pro zajištění konzistence s Azure. Virtuální počítače, které jsou umístěné ve skupině dostupnosti, se fyzicky izolují tak, že je rozšíříte tak, jak je to možné, do více domén selhání (Azure Stack uzly centra). Pokud dojde k selhání hardwaru, virtuální počítače z neúspěšné domény selhání se restartují v jiných doménách selhání. Pokud je to možné, budou se uchovávat v samostatných doménách selhání z ostatních virtuálních počítačů ve stejné skupině dostupnosti. Když se hostitel vrátí zpátky do online režimu, virtuální počítače se znovu vyrovnávají, aby se zachovala vysoká dostupnost.  

Virtual Machine Scale Sets používá v back-endu skupiny dostupnosti a zajišťují, aby se všechny instance sady škálování virtuálních počítačů nastavily v jiné doméně selhání. To znamená, že používají samostatné uzly infrastruktury centra Azure Stack. Například v systému centra Azure Stack se čtyřmi uzly může nastat situace, kdy sada virtuálních počítačů se třemi instancemi selže při vytváření z důvodu nedostatku kapacity čtyř uzlů k umístění tří instancí sady škálování virtuálních počítačů na tři samostatné uzly centra Azure Stack. Před vyzkoušením umístění se navíc dají uzly centra Azure Stack vyplnit na různých úrovních.

Centrum Azure Stack neprovádí přepisování paměti. Je však povoleno přepisování počtu fyzických jader.

Vzhledem k tomu, že algoritmy umístění nevypadají na stávajícím poměru přepracovaného poměru virtuálních a fyzických jader jako faktor, může mít každý hostitel jiný poměr. Microsoft neposkytuje pokyny k poměru fyzického a základního jádra z důvodu variací úloh a požadavků na úroveň služeb.

## <a name="consideration-for-total-number-of-vms"></a>Zvážení celkového počtu virtuálních počítačů

Existují nové aspekty pro přesné plánování Azure Stack kapacity centra. S aktualizací 1901 (a všemi aktualizacemi, které budou dál), je teď limit celkového počtu virtuálních počítačů, které se dají vytvořit. Tento limit má být dočasný, aby nedošlo k nestabilitě řešení. Zdroj problému se stabilitou na vyšších číslech virtuálních počítačů se řeší, ale konkrétní časová osa pro nápravu se nezjistila. Teď je limit počtu 60 virtuálních počítačů na jeden server s celkovým limitem řešení 700. Například limit počtu virtuálních počítačů na osm Server Azure Stack centra by byl 480 (8 × 60). Pro řešení centra Azure Stack od 12 do 16 serverů by se mělo omezit na 700. Toto omezení se vytvořilo, takže se na razítko zachovávají všechny požadavky na výpočetní kapacitu, jako je třeba rezerva odolnosti a poměr mezi virtuálními a fyzickými PROCESORy. Další informace najdete v nové verzi plánovače kapacity.

Pokud je dosaženo limitu škálování virtuálního počítače, vrátí se následující chybové kódy jako výsledek: `VMsPerScaleUnitLimitExceeded` , `VMsPerScaleUnitNodeLimitExceeded` .

## <a name="consideration-for-batch-deployment-of-vms"></a>Zvážení dávkového nasazení virtuálních počítačů

Ve verzích starších než a včetně 2002 2-5 virtuálních počítačů na dávku s mezerou 5 minut v rozsahu mezi dávkami, které poskytovaly spolehlivé nasazení virtuálních počítačů, aby se dosáhlo rozsahu 700 virtuálních počítačů. Ve verzi 2005 centra Azure Stack je možné spolehlivě zřizovat virtuální počítače na velikostech dávek 40 a 5 minut mezi nasazeními Batch.

## <a name="azure-stack-hub-memory"></a>Paměť centra Azure Stack

Azure Stack hub je navržený tak, aby bylo možné zajistit, aby byly virtuální počítače se systémem úspěšně zřízené. Pokud je například hostitel offline z důvodu selhání hardwaru, Azure Stack centrum se pokusí tento virtuální počítač restartovat na jiném hostiteli. Druhý příklad při opravách a aktualizaci softwaru Azure Stack hub. Pokud je nutné restartovat fyzického hostitele, je proveden pokus o přesunutí virtuálních počítačů spuštěných na tomto hostiteli do jiného dostupného hostitele v řešení.

Tuto správu nebo pohyb virtuálních počítačů je možné dosáhnout, jenom pokud je kapacita rezervované paměti, která umožňuje restartování nebo migraci. Část celkové paměti hostitele je vyhrazena a není k dispozici pro umístění virtuálního počítače tenanta.

Výsečový graf můžete zkontrolovat na portálu pro správu, který zobrazuje volnou a využitou paměť v centru Azure Stack. Následující diagram znázorňuje kapacitu fyzické paměti na jednotce škálování centra Azure Stack v centru Azure Stack:

![Kapacita fyzické paměti na jednotce škálování centra Azure Stack](media/azure-stack-capacity-planning/physical-memory-capacity.png)

Použitá paměť se skládá z několika součástí. Následující komponenty využívají paměť v části použití výsečového grafu:  

- **Využití nebo rezervace operačního systému hostitele:** Paměť využívaná operačním systémem (OS) na hostiteli, tabulkách stránky virtuální paměti, procesy, které jsou spuštěny v hostitelském operačním systému, a mezipaměti prostorů s přímým přístupem. Vzhledem k tomu, že tato hodnota závisí na paměti používané různými procesy technologie Hyper-V běžícími na hostiteli, může to kolísat.
- **Služby infrastruktury:** Virtuální počítače infrastruktury, které tvoří centrum Azure Stack. Od verze 1904 Azure Stack centra se jedná o 31 virtuálních počítačů, které zabírají 242 GB + (4 GB × počet uzlů) paměti. Využití paměti komponent služby infrastruktury se může při práci se změnou škálovatelnosti a odolnosti pro naši službu infrastruktury změnit.
- **Rezerva odolnosti:** Centrum Azure Stack vyhrazuje část paměti, která umožňuje dostupnost tenanta během jednoho selhání hostitele a také během opravy a aktualizace, aby bylo umožněno úspěšné migrace virtuálních počítačů za provozu.
- **Virtuální počítače tenanta:** Klientské virtuální počítače vytvořené Azure Stack centrum uživatelů. Navíc k běžícím virtuálním počítačům je paměť spotřebovaná všemi virtuálními počítači, které se proložily v prostředcích infrastruktury. To znamená, že virtuální počítače ve stavu "vytvoření" nebo "selhání" nebo virtuální počítače, které se vypnou z hosta, budou spotřebovávat paměť. Virtuální počítače, které se nastavily pomocí možnosti zastavit navráceno z portálu nebo PowerShellu nebo CLI, ale nebudou spotřebovávat paměť z centra Azure Stack.
- **Přidat poskytovatele prostředků (RPS) s hodnotou:** Virtuální počítače nasazené pro hodnotu – přidejte RPs jako SQL, MySQL, App Service atd.

Nejlepším způsobem, jak pochopit využití paměti na portálu, je použití [Capacity Planner centra Azure Stack](https://aka.ms/azstackcapacityplanner) k zobrazení dopadu různých úloh. Následující výpočet je stejný jako ten, který používá Planner.

Výsledkem tohoto výpočtu je celková dostupná paměť, která se dá použít pro umístění virtuálního počítače tenanta. Tato kapacita paměti je za celou jednotku škálování centra Azure Stack.

Dostupná paměť pro umístění virtuálního počítače = celková paměť hostitele – Vyhrazená paměť – paměť využitá běžícími virtuálními počítači klienta – Azure Stack režijních nákladů na infrastrukturu centra <sup>1</sup>

Rezerva odolnosti = H + R * (N-1) * H) + V * (N-2)

> Kde:
> -    H = velikost paměti s jedním serverem
> - N = velikost jednotky škálování (počet serverů)
> -    R = rezerva operačního systému pro režijní náklady na operační systém, který je .15 v tomto vzorci<sup>2</sup>
> -    V = největší virtuální počítač v jednotce škálování

<sup>1</sup> Azure Stack režie infrastruktury centra = 242 GB + (4 GB x # uzlů). K hostování infrastruktury centra Azure Stack se používá přibližně 31 virtuálních počítačů a v celkovém počtu se spotřebují přibližně 242 GB + (4 GB x # z uzlů) paměti a 146 virtuálních jader. Pro tento počet virtuálních počítačů je nutné, aby splňovaly potřebné oddělení služeb pro splnění požadavků na zabezpečení, škálovatelnost, údržbu a opravy. Tato interní struktura služby umožňuje budoucí zavádění nových služeb infrastruktury, když se vyvíjí.

<sup>2</sup> rezerva operačního systému pro režii = 15% (. 15) paměti uzlu. Hodnota rezervovaného operačního systému je odhad a bude se lišit v závislosti na kapacitě fyzické paměti serveru a celkové režii operačního systému.

Hodnota V, největší virtuální počítač v jednotce škálování, je dynamicky založená na největší velikosti paměti virtuálního počítače tenanta. Například největší hodnota virtuálního počítače může být 7 GB nebo 112 GB nebo jakákoli jiná podporovaná velikost paměti virtuálního počítače v řešení Azure Stack hub. Změna největšího virtuálního počítače v prostředcích infrastruktury centra Azure Stack bude mít za následek zvýšení rezervy na odolnost a také zvýšení paměti samotného virtuálního počítače.

## <a name="considerations-for-deallocation"></a>Požadavky na zrušení přidělení

Když je virtuální počítač ve stavu _zrušeno přidělení_ , prostředky paměti se nepoužívají. To umožňuje umístit ostatní virtuální počítače do systému.

Pokud se znovu spustí uvolněný virtuální počítač, využití paměti nebo přidělení se považuje za nový virtuální počítač umístěný do systému a spotřebovává se dostupná paměť. Pokud není dostupná žádná paměť, virtuální počítač se nespustí.

Aktuálně nasazené velké virtuální počítače ukazují, že přidělená paměť je 112 GB, ale nároky na paměť těchto virtuálních počítačů jsou přibližně 2-3 GB.
    
| Název | Přiřazená paměť (GB) | Požadavky na paměť (GB) | ComputerName |  
| ---- | -------------------- | ------------------ | ------------ |                                        
| ca7ec2ea-40fd-4d41-9d9b-b11e7838d508 |                 112  |     2.2392578125  |  LISSA01P-NODE01 |
| 10cd7b0f-68f4-40ee-9d98-b9637438ebf4  |                112  |     2.2392578125  |   LISSA01P-NODE01 |
| 2e403868-ff81-4abb-b087-d9625ca01d84   |               112   |    2.2392578125  |   LISSA01P-NODE04 |

Existují tři způsoby, jak uvolnit paměť pro umístění virtuálního počítače pomocí vzorce odolného proti chybám **= H + R * (n-1) * H) + V * (n-2)**:
* Zmenšení velikosti největšího virtuálního počítače
* Zvýšení paměti uzlu
* Přidat uzel

### <a name="reduce-the-size-of-the-largest-vm"></a>Zmenšení velikosti největšího virtuálního počítače 

Když se zmenší velikost největšího virtuálního počítače na nejbližší nejbližší virtuální počítač v razítku (24 GB), zmenší se velikost rezervy na odolnost proti chybám.

![Zmenšení velikosti virtuálního počítače](media/azure-stack-capacity-planning/decrease-vm-size.png)        
        
 Rezerva odolnosti = 384 + 172,8 + 48 = 604,8 GB
        
| Celková paměť | Infrastruktura GB | GB klienta | Rezerva odolnosti | Celková Rezervovaná paměť          | Celkem GB k dispozici pro umístění |
|--------------|--------------------|---------------------|--------------------|--------------------------------|----------------------------------|
| 1536 GB      | 258 GB             | 329,25 GB           | 604,8 GB           | 258 + 329,25 + 604,8 = 1168 GB | **~ 344 GB**                         |
     
### <a name="add-a-node"></a>Přidat uzel

[Přidáním uzlu centra Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-add-scale-node) dojde k uvolnění paměti, a to rovnoměrně distribucí paměti mezi dva uzly.

![Přidat uzel](media/azure-stack-capacity-planning/add-a-node.png)

Rezerva odolnosti = 384 + (0,15) ((5) × 384) + 112 × (3) = 1008 GB
    
| Celková paměť | Infrastruktura GB | GB klienta | Rezerva odolnosti | Celková Rezervovaná paměť          | Celkem GB k dispozici pro umístění |
|--------------|--------------------|---------------------|--------------------|--------------------------------|----------------------------------|
| 1536 GB      | 258 GB             | 329,25 GB           | 604,8 GB           | 258 + 329,25 + 604,8 = 1168 GB | **~ 344 GB**                         |

### <a name="increase-memory-on-each-node-to-512-gb"></a>Zvětšete paměť na každém uzlu na 512 GB

[Zvětšením paměti každého uzlu](https://docs.microsoft.com/azure-stack/operator/azure-stack-manage-storage-physical-memory-capacity) se zvýší celková dostupná paměť.

![Zvětšit velikost uzlu](media/azure-stack-capacity-planning/increase-node-size.png)

Rezerva odolnosti = 512 + 230,4 + 224 = 966,4 GB
    
| Celková paměť    | Infrastruktura GB | GB klienta | Rezerva odolnosti | Celková Rezervovaná paměť | Celkem GB k dispozici pro umístění |
|-----------------|----------|-----------|--------------------|-----------------------|----------------------------------|
| 2048 (4 × 512) GB | 258 GB   | 505,75 GB | 966,4 GB           | 1730,15 GB            | **~ 318 GB**                         |

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

**Otázka**: můj tenant má nasazený nový virtuální počítač, jak dlouho bude trvat, než se v grafu schopností na portálu pro správu zobrazí zbývající kapacita?

Odpověď **: okno kapacity se aktualizuje**každých 15 minut, takže Vezměte v úvahu.

**Otázka**: Jak můžu zobrazit dostupné jádra a přiřazené jádra?

**A**Odpověď: v **prostředí PowerShell** `test-azurestack -include AzsVmPlacement -debug` , které generuje výstup podobný tomuto:

```console
    Starting Test-AzureStack
    Launching AzsVmPlacement
     
    Azure Stack Scale Unit VM Placement Summary Results
     
    Cluster Node    VM Count VMs Running Physical Core Total Virtual Co Physical Memory Total Virtual Mem
    ------------    -------- ----------- ------------- ---------------- --------------- -----------------
    LNV2-Node02     20       20          28            66               256             119.5            
    LNV2-Node03     17       16          28            62               256             110              
    LNV2-Node01     11       11          28            47               256             111              
    LNV2-Node04     10       10          28            49               256             101              
    
    PASS : Azure Stack Scale Unit VM Placement Summary
```

**Otázka**: počet nasazených virtuálních počítačů v centru Azure Stack se nezměnil, ale kapacita se pohybuje. Proč?

Odpověď **: dostupná**paměť pro umístění virtuálního počítače má několik závislostí, jedna z nich je rezerva HOSTITELSKÉHO operačního systému. Tato hodnota závisí na paměti používané různými procesy technologie Hyper-V spuštěné na hostiteli, což není konstantní hodnota.

**Otázka**: jaký stav mají virtuální počítače tenanta pro využívání paměti?

Odpověď **: Kromě**spuštěných virtuálních počítačů je paměť spotřebovaná všemi virtuálními počítači, které se proložily v prostředcích infrastruktury. To znamená, že virtuální počítače, které jsou ve stavu "vytvoření" nebo "selhání", budou spotřebovávat paměť. Virtuální počítače, které se vypnou z hosta, na rozdíl od zrušení přidělení z portálu nebo PowerShellu/CLI budou také spotřebovávat paměť.

**Otázka**: mám rozbočovač Azure Stack se čtyřmi hostiteli. Můj tenant má 3 virtuální počítače, které využívají 56 GB paměti RAM (D5_v2). U jednoho z virtuálních počítačů se změní velikost na 112 GB RAM (D14_v2) a dostupné generování sestav paměti na řídicím panelu vedlo k celkovému využití 168 GB v okně kapacity. Následná změna velikosti dalších dvou D5_v2 virtuálních počítačů na D14_v2 vedla pouze k navýšení 56 GB paměti RAM. Proč to jde?

Odpověď **: dostupná**paměť je funkce rezervy odolnosti udržované Azure Stack hub. Rezerva odolnosti je funkce největší velikosti virtuálního počítače na razítku centra Azure Stack. Nejdřív je největší virtuální počítač na razítku 56 GB paměti. Když se změnila velikost virtuálního počítače, největší virtuální počítač na razítku se stal 112 GB paměti, což nejen zvýšilo paměť využitou virtuálním počítačem tenanta, ale také zvýšila rezervu odolnosti. Tato změna způsobila zvýšení 56 GB (56 GB až 112 GB zvýšení paměti virtuálního počítače klienta) + 112 GB zvýšení odolnosti paměti virtuálního počítače. Když se změnila velikost dalších virtuálních počítačů, největší velikost virtuálního počítače zůstala 112 GB virtuálního počítače, a proto se nemusela zvýšit žádná rezerva v důsledku odolnosti proti chybám. Zvýšení spotřeby paměti bylo pouze zvýšení paměti virtuálního počítače klienta (56 GB).

> [!NOTE]
> Požadavky na plánování kapacity pro sítě jsou minimální, protože je konfigurovatelná jenom velikost veřejné virtuální IP adresy. Informace o tom, jak přidat další veřejné IP adresy do centra Azure Stack, najdete v tématu [Přidání veřejných IP adres](azure-stack-add-ips.md).

## <a name="next-steps"></a>Další kroky
Další informace o [Azure Stack úložiště centra](azure-stack-capacity-planning-storage.md)
