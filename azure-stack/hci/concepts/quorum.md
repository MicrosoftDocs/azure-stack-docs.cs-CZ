---
title: Principy kvora clusteru a fondu Azure Stack HCI
description: Principy kvora clusteru a fondu v Prostory úložiště s přímým přístupem na Azure Stack HCI s konkrétními příklady pro přechod na složitými rozhraními.
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.date: 07/21/2020
ms.openlocfilehash: d60ec2edb4247c72d35e69e199bf3fc28259e2ce
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90572119"
---
# <a name="understanding-cluster-and-pool-quorum-on-azure-stack-hci"></a>Principy kvora clusteru a fondu v Azure Stack HCI

> Platí pro: Azure Stack HCI, verze 20H2; Windows Server 2019

[Clustering s podporou převzetí služeb při selhání ve Windows serveru](/windows-server/failover-clustering/failover-clustering-overview) poskytuje vysokou dostupnost pro úlohy. Tyto prostředky se považují za vysoce dostupné, pokud jsou uzly, které jsou hostiteli prostředků. cluster ale obecně vyžaduje více než polovinu uzlů, které mají být spuštěny, což se označuje jako *kvorum*.

Kvorum je navrženo tak, aby se zabránilo scénářům *děleného mozku* , ke kterým může dojít, když je v síti oddíl a podmnožiny uzlů spolu nemůžou komunikovat. To může způsobit, že se obě podmnožiny uzlů pokusí vlastnit úlohu a zapisovat na stejný disk, což může vést k mnoha problémům. Je to ale zabráněno konceptu kvora clusteringu s podporou převzetí služeb při selhání, který vynutí, aby v běhu běžela jenom jedna z těchto skupin uzlů, takže v online režimu zůstane jenom jedna z těchto skupin.

Kvorum určuje počet selhání, která může cluster tolerovat, ale pořád zbývá online. Kvorum je navrženo pro zpracování scénáře, pokud dojde k potížím s komunikací mezi podmnožinami uzlů clusteru, takže se více serverů nepokouší současně hostovat skupinu prostředků a zapisovat na stejný disk současně. Díky tomuto konceptu kvora cluster vynutí zastavení Clusterové služby v jedné z podmnožiny uzlů, aby bylo zajištěno, že bude existovat pouze jeden skutečný vlastník konkrétní skupiny prostředků. Jakmile se uzly, které se zastavily, můžou znovu komunikovat s hlavní skupinou uzlů, automaticky se znovu připojí ke clusteru a spustí se Clusterová služba.

V systému Windows Server 2019 jsou k dispozici dvě součásti systému, které mají vlastní mechanismy kvora:

- **Kvorum clusteru**: funguje na úrovni clusteru (to znamená, že můžete přijít o uzly a nechat cluster stále zapnutý).
- **Kvorum fondu**: Tato akce funguje na úrovni fondu, pokud je povolená prostory úložiště s přímým přístupem (to znamená, že můžete přijít o uzly a jednotky a máte fond stále zapnutý). Fondy úložiště byly navržené tak, aby se používaly v clusterovaných i neclusterovaných scénářích, což má za následek, že mají jiný mechanismus kvora.

## <a name="cluster-quorum-overview"></a>Přehled kvora clusteru

Následující tabulka obsahuje přehled výsledků kvora clusteru na jeden scénář:

| Uzly serveru | Může zachována jedna selhání uzlu serveru. | Může zachována jedna chyba uzlu serveru, pak další | Může předržel dvě souběžné selhání uzlů serveru. |
|--------------|-------------------------------------|---------------------------------------------------|----------------------------------------------------|
| 2            | 50/50                               | Ne                                                | Ne                                                 |
| 2 + určující kopie  | Ano                                 | Ne                                                | Ne                                                 |
| 3            | Ano                                 | 50/50                                             | Ne                                                 |
| 3 + určující kopie  | Ano                                 | Ano                                               | Ne                                                 |
| 4            | Ano                                 | Ano                                               | 50/50                                              |
| 4. disk s kopií clusteru  | Ano                                 | Ano                                               | Ano                                                |
| 5 a vyšší  | Ano                                 | Ano                                               | Ano                                                |

### <a name="cluster-quorum-recommendations"></a>Doporučení kvora clusteru

- Pokud máte dva uzly, **vyžaduje**se určující kopie.
- Pokud máte tři nebo čtyři uzly, **důrazně doporučujeme**umístění určující.
- Pokud máte přístup k Internetu, použijte ** [disk s kopií cloudu](/windows-server/failover-clustering/deploy-cloud-witness)**
- Pokud používáte IT prostředí s dalšími počítači a sdílenými složkami, použijte určující sdílenou složku.

## <a name="how-cluster-quorum-works"></a>Princip kvora clusteru

Když dojde k selhání uzlů nebo když některá podmnožina uzlů ztratí kontakt s jinou podmnožinou, musí uzly, které jsou ve *většině* clusterů, ověřit, že tvoří většinu clusteru, aby zůstaly online. Pokud to nedokáže ověřit, přejde do režimu offline.

Koncept *většiny* ale funguje čistě jenom v případě, že celkový počet uzlů v clusteru je lichý (například tři uzly v clusteru s pěti uzly). Takže co se týká clusterů s sudým počtem uzlů (například cluster se čtyřmi uzly)?

Existují dva způsoby, jak může cluster *Celkový počet hlasů* označit jako liché:

1. Nejdřív to *může projít tím* , že přidá *svědka* s dalšími hlasovacími možnostmi. To vyžaduje nastavení uživatele.
2. Nebo může přejít *dolů* tak, že vynulová jeden hlas uzlu unlucky (provede se automaticky podle potřeby).

Pokaždé, když se přestanou uzly úspěšně ověřit, definice *většiny* se aktualizuje *tak, aby*byly v rámci jenom těch pozůstalých. To umožňuje, aby cluster ztratil jeden uzel, potom další, a tak dále. Tento koncept *celkového počtu hlasů* , který se přizpůsobuje po úspěšném selhání, se označuje jako ***dynamické kvorum***.

### <a name="dynamic-witness"></a>Dynamický určující disk

Dynamický určující disk přepíná hlas určujícího prvku, aby bylo zajištěno, že *Celkový počet hlasů* je lichý. Pokud je k dispozici lichý počet hlasů, svědk nemá žádný hlas. Pokud je k dispozici sudý počet hlasů, má určující prvek nějaký hlas. Dynamický určující disk významně snižuje riziko, že cluster přestane být v důsledku selhání určujícího disku. Cluster rozhodne, zda má být použit hlas určující, na základě počtu hlasovacích uzlů, které jsou v clusteru k dispozici.

Dynamické kvorum funguje s dynamickým určujícím prostředkem způsobem popsaným níže.

### <a name="dynamic-quorum-behavior"></a>Dynamické chování kvora

- Pokud máte **sudý** počet uzlů a žádný určující disk, *jeden uzel získá svůj hlas na nulu*. Například jenom tři ze čtyř uzlů získá hlasy, takže *Celkový počet hlasů* je tři a dva zbývající objekty s hlasy se považují za většinu.
- Pokud máte **lichý** počet uzlů a žádný určující disk, *získají hlasy všichni*.
- V případě, že máte **sudý** počet uzlů plus určující určující *, aby*bylo celkem liché.
- Pokud máte **lichý** počet uzlů plus určující disk, *svědk nehlasuje*.

Dynamické kvorum umožňuje dynamicky přiřadit hlas k uzlu, aby nedošlo ke ztrátě většiny hlasů a aby bylo možné cluster spustit s jedním uzlem (označovaným jako poslední člověk Man). Pojďme jako příklad využít cluster se čtyřmi uzly. Předpokládat, že kvorum vyžaduje 3 hlasy.

V takovém případě by se cluster dostal dolů, pokud jste ztratili dva uzly.

![Diagram znázorňující čtyři uzly clusteru, z nichž každý získá hlas](media/quorum/dynamic-quorum-base.png)

Nicméně dynamické kvorum brání tomu, aby se stalo. *Celkový počet hlasů* požadovaných pro kvorum se teď určuje podle počtu dostupných uzlů. Díky dynamickému kvora tak cluster zůstane, i když ztratíte tři uzly.

![Diagram znázorňující čtyři uzly clusteru s uzlem, které selžou po jednom, a počet požadovaných hlasů, které se po každém selhání upravují.](media/quorum/dynamic-quorum-step-through.png)

Výše uvedený scénář se vztahuje na obecný cluster, u kterého není povolený Prostory úložiště s přímým přístupem. Pokud je však povoleno Prostory úložiště s přímým přístupem, cluster může podporovat pouze dvě selhání uzlu. To je vysvětleno podrobněji v [části kvorum fondu](#pool-quorum-overview).

### <a name="examples"></a>Příklady

#### <a name="two-nodes-without-a-witness"></a>Dva uzly bez určujícího disku.
Hlas jednoho uzlu je nulový, takže *většina* hlasů je určena celkem **1 hlasů**. Pokud uzel bez hlasovacího práva neočekávaně skončí, zůstane u něj 1/1 a cluster se zachová. Pokud dojde k neočekávanému výpadku hlasovacího uzlu, zůstane u něj 0/1 a cluster se ukončí. Pokud je hlasovací uzel řádně vypnutý, hlas se přenese do druhého uzlu a cluster se zachová. ***To je důvod, proč je důležité nakonfigurovat určující disk.***

![Vysvětlené kvorum v případě se dvěma uzly bez určujícího disku](media/quorum/2-node-no-witness.png)

- Může zachována jedna selhání serveru: **50%**.
- Může zachována jedna selhání serveru a pak jiný: **ne**.
- Může zamezit dvěma selháními serveru najednou: **ne**.

#### <a name="two-nodes-with-a-witness"></a>Dva uzly s kopií clusteru.
Oba uzly spolu společně s určujícími hlasy, takže *většina* je určena celkem **3 hlasy**. Pokud dojde k výpadku některého uzlu, zůstane u něj 2/3 a cluster se zachová.

![Vysvětlené kvorum v případě se dvěma uzly s kopií clusteru](media/quorum/2-node-witness.png)

- Může zachována jedna selhání serveru: **Ano**.
- Může zachována jedna selhání serveru a pak jiný: **ne**.
- Může zamezit dvěma selháními serveru najednou: **ne**.

#### <a name="three-nodes-without-a-witness"></a>Tři uzly bez určujícího umístění.
Všechny uzly Hlasujte, takže *většina* je určena celkem **3 hlasy**. Pokud dojde k výpadku některého uzlu, jsou zbývající objekty 2/3 a cluster se zachová. Cluster se stane dvěma uzly bez určujícího umístění – v tomto okamžiku se nacházíte ve scénáři 1.

![Vysvětlené kvorum v případě se třemi uzly bez určujícího umístění](media/quorum/3-node-no-witness.png)

- Může zachována jedna selhání serveru: **Ano**.
- Může zachována jedna selhání serveru a pak další: **50% pravděpodobnost**.
- Může zamezit dvěma selháními serveru najednou: **ne**.

#### <a name="three-nodes-with-a-witness"></a>Tři uzly s kopií clusteru.
Všechny uzly hlas, takže svědk nezpočátku nahlasí. *Většina* je určena celkem **3 hlasy**. Po jednom selhání má cluster dva uzly s kopií clusteru, což se vrátí do scénáře 2. Takže teď dva uzly a hlasujte určující sdílené složky.

![Vysvětlené kvorum v případě se třemi uzly s kopií clusteru](media/quorum/3-node-witness.png)

- Může zachována jedna selhání serveru: **Ano**.
- Může zachována jedna selhání serveru a pak další: **Ano**.
- Může zamezit dvěma selháními serveru najednou: **ne**.

#### <a name="four-nodes-without-a-witness"></a>Čtyři uzly bez určujícího disku
Hlas jednoho uzlu je nulový, takže *většina* je určena celkem **3 hlasy**. Po jednom selhání se cluster stane třemi uzly a Vy jste ve scénáři 3.

![Vysvětlené kvorum v případě se čtyřmi uzly bez určujícího disku](media/quorum/4-node-no-witness.png)

- Může zachována jedna selhání serveru: **Ano**.
- Může zachována jedna selhání serveru a pak další: **Ano**.
- Může zamezit dvěma selháními serveru najednou: **50% pravděpodobnost**.

#### <a name="four-nodes-with-a-witness"></a>Čtyři uzly s kopií clusteru.
Všechny uzly hlasy a určující hlasy, takže *většina* je určena celkem **5 hlasů**. Po jednom selhání jste ve scénáři 4. Po dvou současných selháních přeskočíte na scénář 2.

![Vysvětlené kvorum v případě se čtyřmi uzly s kopií clusteru](media/quorum/4-node-witness.png)

- Může zachována jedna selhání serveru: **Ano**.
- Může zachována jedna selhání serveru a pak další: **Ano**.
- Může zamezit dvě chyby serveru: **Ano**.

#### <a name="five-nodes-and-beyond"></a>Pět uzlů a více než.
Všechny uzly hlas, nebo všechny, ale pouze jeden hlas, ať už celkový počet je lichý. Prostory úložiště s přímým přístupem v tuto chvíli nemůže zpracovávat více než dva uzly, takže v tomto okamžiku není potřeba žádný určující nebo vhodný.

![Vysvětlené kvorum v případě s pěti uzly a nad rámec](media/quorum/5-nodes.png)

- Může zachována jedna selhání serveru: **Ano**.
- Může zachována jedna selhání serveru a pak další: **Ano**.
- Může zamezit dvě chyby serveru: **Ano**.

Když teď chápeme, jak kvorum funguje, Podívejme se na typy určující sdílené složky kvora.

### <a name="quorum-witness-types"></a>Typy určující určující kvorum

Clustering s podporou převzetí služeb při selhání podporuje tři typy kopií kvora:

- **[Sdílená složka cloudu](/windows-server/failover-clustering/deploy-cloud-witness)** – služba BLOB Storage v Azure je přístupná pro všechny uzly clusteru. Udržuje informace o clusteringu v souboru. log, ale neukládá kopii databáze clusteru.
- **Určující sdílená složka** – sdílená složka SMB, která je nakonfigurovaná na souborovém serveru se systémem Windows Server. Udržuje informace o clusteringu v souboru. log, ale neukládá kopii databáze clusteru.
- **Disk s kopií** clusteru – malý clusterový disk, který je ve skupině úložišť k dispozici. Tento disk je vysoce dostupný a může převzetí služeb při selhání mezi uzly. Obsahuje kopii databáze clusteru.  ***Disk s kopií clusteru není u prostory úložiště s přímým přístupem podporován***.

## <a name="pool-quorum-overview"></a>Přehled kvora fondu

Právě jsme mluvili o kvoru clusteru, který funguje na úrovni clusteru. Teď se podrobně do fondu kvora, které funguje na úrovni fondu (tzn. můžete přijít o uzly a jednotky a mít fond na zůstat). Fondy úložiště byly navržené tak, aby se používaly v clusterovaných i neclusterovaných scénářích, což má za následek, že mají jiný mechanismus kvora.

Následující tabulka obsahuje přehled výsledků kvora fondu na jeden scénář:

| Uzly serveru | Může zachována jedna selhání uzlu serveru. | Může zachována jedna chyba uzlu serveru, pak další | Může předržel dvě souběžné selhání uzlů serveru. |
|--------------|-------------------------------------|---------------------------------------------------|----------------------------------------------------|
| 2            | Ne                                  | Ne                                                | Ne                                                 |
| 2 + určující kopie  | Ano                                 | Ne                                                | Ne                                                 |
| 3            | Ano                                 | Ne                                                | Ne                                                 |
| 3 + určující kopie  | Ano                                 | Ne                                                | Ne                                                 |
| 4            | Ano                                 | Ne                                                | Ne                                                 |
| 4. disk s kopií clusteru  | Ano                                 | Ano                                               | Ano                                                |
| 5 a vyšší  | Ano                                 | Ano                                               | Ano                                                |

## <a name="how-pool-quorum-works"></a>Jak funguje kvorum fondu

Když dojde k selhání jednotek nebo když některá z podmnožiny jednotek ztratí kontakt s jinou podmnožinou, musí jednotky se systémem ověřit, že představují *většinu* fondu, aby zůstaly online. Pokud to nedokáže ověřit, přejde do režimu offline. Fond je entita, která přejde do režimu offline nebo zůstane online na základě toho, jestli má dostatek disků pro kvorum (50% + 1). Vlastníkem prostředku fondu (aktivní uzel clusteru) může být + 1.

Ale kvorum fondu funguje jinak než kvorum clusteru, a to následujícími způsoby:

- fond používá jeden uzel v clusteru jako určující jako jako nesdílené jednotky, aby se zadržela polovina jednotek (Tento uzel je vlastníkem prostředku fondu).
- fond nemá dynamické kvorum.
- fond neimplementuje svou vlastní verzi pro odebrání hlasování.

### <a name="examples"></a>Příklady

#### <a name="four-nodes-with-a-symmetrical-layout"></a>Čtyři uzly s symetrickým rozložením.
Každá z těchto 16 jednotek má jeden hlas a druhý uzel má také jeden hlas (protože se jedná o vlastníka prostředku fondu). *Většina* je určena od celkem **16 hlasů**. Pokud uzly tři a čtyři rozcházejí dolů, zbývající podmnožina má 8 jednotek a vlastníka prostředku fondu, což je 9/16 hlasů. Takže se fond zachová.

![Kvorum fondu 1](media/quorum/pool-1.png)

- Může zachována jedna selhání serveru: **Ano**.
- Může zachována jedna selhání serveru a pak další: **Ano**.
- Může zamezit dvě chyby serveru: **Ano**.

#### <a name="four-nodes-with-a-symmetrical-layout-and-drive-failure"></a>Čtyři uzly s symetrickým rozložením a selháním jednotky.
Každá z těchto 16 jednotek má jeden hlas a uzel 2 má také jeden hlas (protože se jedná o vlastníka prostředku fondu). *Většina* je určena od celkem **16 hlasů**. Nejprve bude jednotka 7 mimo provoz. Pokud uzly tři a čtyři rozcházejí dolů, má zbývající podmnožina 7 jednotek a vlastníka prostředku fondu, což je 8/16 hlasů. Takže fond nemá většinu a nepracuje.

![Kvorum fondu 2](media/quorum/pool-2.png)

- Může zachována jedna selhání serveru: **Ano**.
- Může zachována jedna selhání serveru a pak jiný: **ne**.
- Může zamezit dvěma selháními serveru najednou: **ne**.

#### <a name="four-nodes-with-a-non-symmetrical-layout"></a>Čtyři uzly s rozložením bez symetrického zobrazení.
Každá z těchto 24 jednotek má jeden hlas a druhý uzel má také jeden hlas (vzhledem k tomu, že se jedná o vlastníka prostředku fondu). *Většina* je určena od celkem **24 hlasů**. Pokud uzly tři a čtyři rozcházejí dolů, zbývající podmnožina má 8 jednotek a vlastníka prostředku fondu, což je 9/24 hlasů. Takže fond nemá většinu a nepracuje.

![Kvorum fondu 3](media/quorum/pool-3.png)

- Může zachována jedna selhání serveru: **Ano**.
- Může zacházet s jedním selháním serveru, další: * * závisí * * (nedokáže se předržet, pokud oba uzly tři a čtyři rozcházejí dolů), ale mohou zachovány všechny ostatní scénáře.
- Může zacházet se dvěma selháními serveru najednou: * * závisí * * (nedaří se předržet, pokud oba uzly tři a čtyři rozcházejí dolů), ale mohou zachovány všechny ostatní scénáře.

### <a name="pool-quorum-recommendations"></a>Doporučení kvora fondu

- Ujistěte se, že každý uzel v clusteru má symetrický (každý uzel má stejný počet jednotek).
- Povolte trojrozměrné zrcadlení nebo duální paritu, abyste mohli tolerovat selhání uzlu a zachovat virtuální disky online.
- Pokud je více než dva uzly mimo provoz nebo dojde k nedostatku dvou uzlů a disku na jiném uzlu, svazky nemusí mít přístup ke všem třem kopiím svých dat, a proto budou odpojeny do offline režimu a nebudou k dispozici. Doporučuje se přenášet servery zpátky nebo vyměnit disky, aby se zajistila maximální odolnost pro všechna data ve svazku.

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích:

- [Konfigurace a Správa kvora](/windows-server/failover-clustering/manage-cluster-quorum)
- [Nastavení určujícího clusteru](../deploy/witness.md)
