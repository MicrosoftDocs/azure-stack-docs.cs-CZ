---
title: Postup rozšiřování datacentra v centru Azure Stack
description: Naučte se, jak centrálně roztáhnout datacentrum do centra Azure Stack.
author: mattbriggs
ms.topic: how-to
ms.date: 12/13/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/13/2019
ms.openlocfilehash: d3884f877511c3a0d285b62ecf3136673e24a116
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2020
ms.locfileid: "77702444"
---
# <a name="extending-storage-to-azure-stack-hub"></a>Rozšíření úložiště do centra Azure Stack

Tento článek poskytuje Azure Stack informace o infrastruktuře úložiště centra, které vám pomůžou rozhodnout, jak integrovat Azure Stack hub do stávajícího síťového prostředí. Po poskytnutí Obecné diskuze o rozšíření datového centra obsahuje článek dva různé scénáře. Můžete se připojit k serveru Windows File Storage. Můžete se také připojit k serveru Windows iSCSI.

## <a name="overview-of-extending-storage-to-azure-stack-hub"></a>Přehled rozšíření úložiště do centra Azure Stack

Existují situace, kdy vaše data uložená ve veřejném cloudu nejsou dostatečná. Možná máte úlohu virtualizované databáze náročné na výpočetní výkon, která je citlivá na latenci a dobu odezvy na veřejný cloud může ovlivnit výkon databázového zatížení. Možná existují data v místním prostředí, která jsou držená na souborovém serveru, na serveru NAS nebo v poli úložiště iSCSI, ke kterému se musí přicházet v místních úlohách a musí se nacházet v místním prostředí, aby se splnily zákonné předpisy nebo cíle dodržování předpisů. Jedná se jenom o dva scénáře, ve kterých jsou data uložená místně, ale jsou důležitá pro mnoho organizací.

Proto nestačí hostovat tato data v účtech úložiště v Azure Stackovém centru nebo uvnitř virtualizovaného souborového serveru, který běží v systému Azure Stack hub? Na rozdíl od Azure je úložiště centra Azure Stack omezené. Kapacita, kterou máte k dispozici pro vaše využití, závisí výhradně na kapacitě jednotlivých uzlů, kterou jste si zvolili k nákupu, a navíc k počtu uzlů, které máte. A vzhledem k tomu, že Azure Stack hub je řešení sblížené pomocí technologie Hyper-v, měli byste chtít rozšířit kapacitu úložiště tak, aby splňovala požadavky na využití, a také je potřeba rozšířit výpočetní nároky prostřednictvím přidávání uzlů. To může být potenciálně nenáročné, zejména pokud je potřeba dodatečnou kapacitu pro studené, archivní úložiště, které by bylo možné přidat za nízké náklady mimo Azure Stack centrálního systému.

Díky tomu se zobrazí scénář, který se vám bude týkat. Jak můžete připojit Azure Stack systémy centra, virtualizované úlohy běžící v centru Azure Stack, a to jednoduše a efektivně, až do úložných systémů mimo centrum Azure Stack, přístupné prostřednictvím sítě.

### <a name="design-for-extending-storage"></a>Návrh pro rozšíření úložiště

Diagram znázorňuje scénář, ve kterém se na jednom virtuálním počítači, spuštění úlohy, připojuje k a využívá externí úložiště (k VIRTUÁLNÍmu počítači a Azure Stack samotného centra) pro účely čtení a zápisu dat atd. V tomto článku se zaměříte na jednoduché načítání souborů, ale tento příklad můžete rozšířit pro složitější scénáře, jako je vzdálené úložiště databázových souborů.

![](./media/azure-stack-network-howto-extend-datacenter/image1.png)

V diagramu uvidíte, že virtuální počítač v systému Azure Stack hub byl nasazen s více síťovými kartami. Z redundance, ale také z hlediska úložiště, je důležité mít více cest mezi cílem a cílem. V případě, že se něco stane složitějším, jsou virtuální počítače v Azure Stackovém rozbočovači jak veřejné, tak i soukromé IP adresy, stejně jako v Azure. Pokud externí úložiště potřebuje k přístupu k virtuálnímu počítači, může to provést jenom přes veřejnou IP adresu, protože privátní IP adresy se primárně používají v rámci systémů Azure Stack hub v rámci virtuální sítě a podsítí. Externí úložiště by nedokázalo komunikovat s privátním adresním prostorem virtuálního počítače, pokud ho neprojde lokalitou sítě VPN, aby se mohla vyseknout do samotné virtuální sítě. Proto se v tomto příkladu zaměříme na komunikaci prostřednictvím veřejného prostoru IP adres. Jednou z nich, jak si všimnout veřejné IP adresy v diagramu, je, že existuje 2 různých veřejných podsítí fondů IP adres. Ve výchozím nastavení služba Azure Stack hub vyžaduje jenom jeden fond pro účely veřejné IP adresy, ale pro redundantní směrování zvažte, že se může přidat druhý objekt. V tuto chvíli ale nemůžete vybrat IP adresu z konkrétního fondu, takže můžete mít ve skutečnosti virtuální počítače s veřejnými IP adresami ze stejného fondu na víc virtuálních síťových karet.

Pro účely této diskuze budeme předpokládat, že se bude věnovat směrování mezi hraničními zařízeními a externím úložištěm, a přenos může správně procházet síť. V tomto příkladu se nezáleží na tom, jestli je páteřní síť 10 GbE LOM, 10GbE, 25 nebo dokonce rychleji, ale je důležité vzít v úvahu při plánování integrace, aby bylo možné řešit potřeby výkonu všech aplikací, které přistupují k tomuto externímu úložišti.

## <a name="connect-to-a-windows-server-iscsi-target"></a>Připojení k cíli iSCSI Windows serveru

V tomto scénáři nasadíme a nakonfigurujeme virtuální počítač s Windows serverem 2019 na Azure Stack hub a připravíte ho pro připojení k externímu cíli iSCSI, který bude taky používat Windows Server 2019. Tam, kde je to vhodné, umožníme použití klíčových funkcí, jako je MPIO, k optimalizaci výkonu a připojení mezi virtuálním počítačem a externím úložištěm.

### <a name="deploy-the-windows-server-2019-vm-on-azure-stack-hub"></a>Nasazení virtuálního počítače s Windows serverem 2019 do centra Azure Stack

1.  Z **portálu pro správu centra Azure Stack**za předpokladu, že je tento systém správně zaregistrován a je připojený k webu Marketplace, vyberte **Správa Marketplace** a pak za předpokladu, že ještě nemáte bitovou kopii Windows serveru 2019, vyberte **Přidat z Azure** a pak vyhledejte **Windows server 2019**a přidejte image **Windows serveru 2019 Datacenter** .

    ![](./media/azure-stack-network-howto-extend-datacenter/image2.png)

    Stažení bitové kopie systému Windows Server 2019 může trvat delší dobu.

2.  Až budete mít v prostředí Azure Stackového centra bitovou kopii Windows serveru 2019, **Přihlaste se k portálu User Portal služby Azure Stack hub**.

3.  Po přihlášení k portálu User Portal služby Azure Stack hub se ujistěte, že máte [předplatné nabídky](https://docs.microsoft.com/azure-stack/operator/azure-stack-subscribe-plan-provision-vm?view=azs-1908), které vám umožní zřídit prostředky IaaS (výpočetní prostředky, úložiště a síť).

4.  Jakmile budete mít předplatné k dispozici, vraťte se na **řídicím panelu** na portálu User portal centra Azure Stack, vyberte **vytvořit prostředek**, vyberte **COMPUTE** a pak vyberte **položku galerie datacenter Windows serveru 2019**.

5.  V okně **základy** vyplňte následující informace:

    a.  **Název**: VM001

    b.  **Uživatelské jméno**: localadmin

    c.  **Heslo** a **potvrzení hesla**: \<hesla podle svého výběru >

    d.  **Předplatné**: \<předplatné podle vašeho výběru s >mi výpočetními prostředky, úložištěm a síťovými prostředky.

    e.  **Skupina prostředků**: storagetesting (vytvořit nový)

    f.  Vyberte **OK**.

6.  V okně **Zvolte velikost** vyberte **Standard_F8s_v2** a vyberte **Vybrat**.

7.  V okně **Nastavení** vyberte **virtuální síť** a v okně **vytvořit virtuální síť** upravte adresní prostor, který má být **10.10.10.0/23** , a aktualizujte rozsah adres podsítě na **10.10.10.0/24** a pak vyberte **OK**.

8.  Vyberte **veřejnou IP adresu**a v okně **vytvořit veřejnou IP adresu** vyberte **statický** přepínač.

9.  V rozevíracím seznamu **Vybrat veřejné příchozí porty** vyberte **RDP (3389)** .

10. Ponechte ostatní výchozí nastavení a vyberte **OK**.

    ![](./media/azure-stack-network-howto-extend-datacenter/image3.png)

11. Přečtěte si souhrn, počkejte na ověření a pak kliknutím na **OK** zahajte nasazení. Nasazení by se mělo dokončit během přibližně 10 minut.

12. Až se nasazení dokončí, v části **prostředek** vyberte název virtuálního počítače, **VM001** a otevřete **Přehled**.

    ![](./media/azure-stack-network-howto-extend-datacenter/image4.png)

13. V části název DNS vyberte **Konfigurovat** a zadejte popisek názvu DNS, **Vm001** a vyberte **Uložit**a pak vyberte **vm001**.

14. Na pravé straně okna Přehled vyberte **storagetesting-VNet/default** v textu virtuální sítě nebo podsítě.

15. V okně storagetesting-VNet vyberte **podsítě** a pak klikněte na **+ podsíť**, pak v okně Nová podsíť přidat, zadejte následující informace a pak vyberte **OK**:

    a.  **Název**: podsíť subnet2

    b.  **Rozsah adres (blok CIDR)** : 10.10.11.0/24

    c.  **Skupina zabezpečení sítě**: žádné

    d.  **Směrovací tabulka**: žádné

16. Po uložení vyberte **VM001**.

17. Na levé straně okna Přehled vyberte **sítě**.

18. Vyberte **připojit síťové rozhraní** a pak vyberte **vytvořit síťové rozhraní**.

19. V okně **vytvořit síťové rozhraní** zadejte následující informace.

    a.  **Název**: vm001nic2

    b.  **Podsíť**: Ujistěte se, že je podsíť 10.10.11.0/24.

    c.  **Skupina zabezpečení sítě**: VM001-NSG

    d. **Skupina prostředků**: storagetesting

20. Po úspěšném připojení vyberte **VM001** a vyberte **zastavit** , aby se virtuální počítač vypnul.

21. Jakmile je virtuální počítač zastavený (přidělení zrušeno), na levé straně okna Přehled vyberte **síť**, vyberte **připojit síťové rozhraní** a pak vyberte **vm001nic2**a pak vyberte **OK**. Další síťové rozhraní se za chvíli přidá do virtuálního počítače.

22. Pořád v okně **sítě** vyberte kartu **vm001nic2** a pak vyberte **síťové rozhraní: vm001nic2**.

23. V okně rozhraní vm001nic vyberte **Konfigurace protokolu IP**a ve středu okna vyberte **ipconfig1**.

24. V okně nastavení ipconfig1 vyberte možnost **povoleno** pro veřejnou IP adresu a vyberte **konfigurovat požadovaná nastavení**, **vytvořit nové** a jako název zadejte vm001nic2pip. Vyberte možnost **static** a pak klikněte na **tlačítko OK** a pak na **Uložit**.

25. Po úspěšném uložení se vraťte do okna Přehled VM001 a výběrem **Spustit** spusťte nakonfigurovaný virtuální počítač s Windows serverem 2019.

26. Po spuštění **navažte relaci RDP** do VM001.

27. Po připojení k VIRTUÁLNÍmu počítači otevřete **cmd** (jako správce) a zadejte název **hostitele** pro načtení názvu operačního systému. **Měl by odpovídat VM001**. Tuto poznámku si poznamenejte pro pozdější účely.

### <a name="configure-second-network-adapter-on-windows-server-2019-vm-on-azure-stack-hub"></a>Konfigurace druhého síťového adaptéru na virtuálním počítači s Windows serverem 2019 na rozbočovači Azure Stack

Ve výchozím nastavení Azure Stack centrum přiřadí výchozí bránu k prvnímu (primárnímu) síťovému rozhraní připojenému k virtuálnímu počítači. Centrum Azure Stack nepřiřazuje výchozí bránu k dalším (sekundárním) síťovým rozhraním připojeným k virtuálnímu počítači. Proto ve výchozím nastavení nemůžete komunikovat s prostředky mimo podsíť, ve které sekundární síťové rozhraní je. Sekundární síťová rozhraní však mohou komunikovat s prostředky mimo jejich podsíť, i když se postup pro povolení komunikace liší v různých operačních systémech.

1.  Pokud ještě nemáte otevřené připojení, navažte připojení RDP k **VM001**.

2.  Otevřete **cmd** jako správce a spusťte **Tisk trasy** , který by měl vracet dvě rozhraní (síťové adaptéry technologie Hyper-V) uvnitř tohoto virtuálního počítače.

    ![](./media/azure-stack-network-howto-extend-datacenter/image5.png)

3.  Nyní spusťte **příkaz ipconfig** a zjistěte, která IP adresa je přiřazena k sekundárnímu síťovému rozhraní. V tomto příkladu je 10.10.11.4 přiřazeno rozhraní 6. Pro sekundární síťové rozhraní se nevrátí žádná adresa výchozí brány.

    ![](./media/azure-stack-network-howto-extend-datacenter/image6.png)

4.  Chcete-li směrovat veškerý provoz určený pro adresy mimo podsíť sekundárního síťového rozhraní do brány pro podsíť, spusťte následující příkaz z **příkazu cmd:** .

    ```CMD  
    route add -p 0.0.0.0 MASK 0.0.0.0 <ipaddress> METRIC 5015 IF <interface>
    ```

    `<ipaddress>` je adresa výskytu aktuální podsítě a `<interface>` je číslo rozhraní.

    ![](./media/azure-stack-network-howto-extend-datacenter/image7.png)

5.  Pokud chcete potvrdit, že se přidaná trasa nachází v tabulce směrování, zadejte příkaz **Route Print** .

    ![](./media/azure-stack-network-howto-extend-datacenter/image8.png)

6.  Můžete také ověřit odchozí komunikaci spuštěním příkazu příkazu testovat:  
    `ping 8.8.8.8 -S 10.10.11.4`  
    Příznak `-S` umožňuje zadat zdrojovou adresu. v tomto případě je 10.10.11.4 IP adresa síťového adaptéru, který má nyní výchozí bránu.

7.  Ukončete program **cmd**.

### <a name="configure-the-windows-server-2019-iscsi-target"></a>Konfigurace cíle iSCSI Windows serveru 2019

Pro účely tohoto scénáře budete ověřovat konfiguraci, kde je cílovým serverem iSCSI Windows Server 2019 virtuální počítač, který běží na Hyper-V, mimo prostředí Azure Stack hub. Tento virtuální počítač se nakonfiguruje s 8 virtuálními procesory, jedním souborem VHDX a nejdůležitějším 2 virtuálními síťovými adaptéry. V ideálním scénáři budou mít tyto síťové adaptéry různé směrovatelné podsítě, ale v této validaci budou mít síťové adaptéry ve stejné podsíti.

![](./media/azure-stack-network-howto-extend-datacenter/image9.png)

Pro váš cílový server iSCSI může to být Windows Server 2016 nebo 2019, fyzický nebo virtuální, spuštěný v Hyper-V, VMware nebo jiné zařízení podle vašeho výběru, jako je vyhrazená fyzická síť SAN iSCSI. Klíč se tady zaměřuje, je připojení k systému Azure Stack hub a je z něj k dispozici, ale existuje několik cest mezi zdrojem a cílem, protože poskytuje další redundanci a umožňuje vyšší využití pokročilých funkcí. výkon, například MPIO.

Doporučuje se aktualizovat cíl iSCSI Windows serveru 2019 s nejnovějšími kumulativními aktualizacemi a opravami, a to v případě potřeby restartováním, než budete pokračovat v konfiguraci sdílených složek.

Po aktualizaci a restartu teď můžete tento server nakonfigurovat jako cíl iSCSI.

1.  Otevřete **Správce serveru** a vyberte **Spravovat**a **přidejte role a funkce**.

2.  Po otevření vyberte **Další**, vyberte **instalace na základě rolí nebo na základě funkcí**a Projděte si výběry, dokud se nedostanete na stránku **Vybrat role serveru** .

3.  Rozbalte **Souborová služba a služba úložiště**, rozbalte **soubor & služby iSCSI Services** a zaškrtnete políčko **cílový server iSCSI** a přijměte všechny místní výzvy, které přidají nové funkce a pak dokončí doplňování.

    ![](./media/azure-stack-network-howto-extend-datacenter/image10.png)

    Po dokončení zavřete **Správce serveru.**

4.  Otevřete **Průzkumníka souborů,** přejděte na C:\\ a **vytvořte novou složku**s názvem **iSCSI**.

5.  Znovu otevřete **Správce serveru** a v nabídce na levé straně vyberte **Souborová služba a služba úložiště** .

6.  Vyberte **iSCSI** a vyberte "Pokud**chcete vytvořit virtuální disk iSCSI, spusťte**v pravém podokně odkaz Průvodce vytvořením virtuálního disku iSCSI. vyberte ji. Průvodce se zobrazí v průvodci.

7.  Na stránce **Vybrat umístění virtuálního disku iSCSI** vyberte přepínač pro **zadání vlastní cesty** a přejděte k jednotce **C:\\iSCSI** a vyberte **Další**.

8.  Dejte virtuálnímu disku iSCSI název **iSCSIdisk1** a volitelně také popis a pak vyberte **Další**.

9.  Nastavte velikost virtuálního disku na **10 GB** a vyberte **Pevná velikost** a vyberte **Další**.

    ![](./media/azure-stack-network-howto-extend-datacenter/image11.png)

10) Vzhledem k tomu, že se jedná o nový cíl, vyberte **Nový cíl iSCSI** a vyberte **Další**.

11) Na stránce **Zadejte název cíle** zadejte **TARGET1** a vyberte **Další**.

12) Na stránce **zadat přístupové servery** vyberte **Přidat**. Tím se otevře dialogové okno, kde můžete zadat konkrétní **iniciátory** , které budou mít autorizaci připojit se k cíli iSCSI.

13) V **okně Přidat ID iniciátoru**vyberte **zadat hodnotu pro vybraný typ** a v části **typ** ověřte, že je v rozevírací nabídce vybraná možnost IQN. Zadejte název **IQN. 1991-05. com. Microsoft:\<názevpočítače >** kde \<ComputerName > je **název počítače** **VM001** a pak vyberte **Další**.

    ![](./media/azure-stack-network-howto-extend-datacenter/image12.png)

14) Na stránce **Povolit ověřování** ponechte pole prázdné a pak vyberte **Další**.

15) Potvrďte svoje výběry a vyberte **vytvořit**a pak zavřít. Měl by se zobrazit virtuální disk iSCSI vytvořený v Správce serveru.

    ![](./media/azure-stack-network-howto-extend-datacenter/image13.png)

### <a name="configure-the-windows-server-2019-iscsi-initiator-and-mpio"></a>Konfigurace iniciátoru iSCSI Windows serveru 2019 a funkce MPIO

Chcete-li nastavit iniciátor iSCSI, nejprve se přihlaste k **portálu pro uživatele centra Azure Stack** v systému **Azure Stack hub** a přejděte do okna **Přehled** pro **VM001.**

1.  Navažte připojení RDP k VM001. Po připojení otevřete **Správce serveru**.

2.  Vyberte možnost **Přidat role a funkce**a přijměte výchozí hodnoty, dokud se nedostanete na stránku **funkce** .

3.  Na stránce **funkce přidejte funkci** **Multipath i/O** a vyberte **Další**.

    ![](./media/azure-stack-network-howto-extend-datacenter/image14.png)

4.  V **případě potřeby automaticky restartovat cílový server** a vybrat **nainstalovat**a pak vybrat **Zavřít.** Po dokončení bude restart pravděpodobně požadován, takže po dokončení se znovu připojí k VM001.

5.  Zpět v **Správce serveru**počkejte, než se **Instalace funkce MPIO dokončí**, vyberte **Zavřít**a pak vyberte **nástroje** a vyberte **MPIO**.

6.  Vyberte kartu **zjistit více cest** a zaškrtnete políčko **Přidat podporu pro zařízení iSCSI** a vyberte **Přidat**a pak vyberte **Ano** , aby se VM001 **restart** . Pokud okno neobdržíte, vyberte **OK a** pak ručně restartujte.

    ![](./media/azure-stack-network-howto-extend-datacenter/image15.png)

7.  Po restartování navažte **nové připojení RDP k VM001**.

8.  Po připojení otevřete **Správce serveru**, vyberte **nástroje** a vyberte **iniciátor iSCSI**.

9.  Když se zobrazí okno Microsoft iSCSI, vyberte **Ano** , pokud chcete, aby se služba iSCSI spouštěla ve výchozím nastavení.

    ![](./media/azure-stack-network-howto-extend-datacenter/image16.png)

10. V okně Vlastnosti iniciátoru iSCSI vyberte kartu **zjišťování** .

11. Teď přidáte 2 cíle, takže nejdřív vyberte tlačítko **Vyhledat portál** .

12. Zadejte první IP adresu vašeho cílového serveru iSCSI a vyberte **Upřesnit**.

    ![](./media/azure-stack-network-howto-extend-datacenter/image17.png)

13. V okně **Upřesnit nastavení** vyberte následující a pak vyberte **OK**.

    a.  **Místní adaptér**: iniciátor iSCSI společnosti Microsoft.

    b.  **IP adresa iniciátoru**: 10.10.10.4.

14. Zpět v okně **zjistit cílový portál** vyberte **OK**.

15. Opakujte tento postup s následujícím:

    a. \* * IP adresa * *: vaše druhá cílová IP adresa iSCSI.

    b.  **Místní adaptér**: iniciátor iSCSI společnosti Microsoft.

    c.  **IP adresa iniciátoru**: 10.10.11.4.

16. Vaše cílové portály by měly vypadat takto, s vlastními IP **adresami** cíle iSCSI pod sloupcem adresa.

    ![](./media/azure-stack-network-howto-extend-datacenter/image18.png)

17. Zpátky na kartě **cíle** vyberte svůj cíl iSCSI ze středu okna a vyberte **připojit**.

18. V okně **připojit k cílovému** poli zaškrtněte políčko **Povolit víceřádkovou cestu** a vyberte **Upřesnit**.

    ![](./media/azure-stack-network-howto-extend-datacenter/image19.png)

19. Zadejte následující informace a vyberte **OK**a potom v okně **připojit k cílovému** systému vyberte **OK**.

    a.  **Místní adaptér**: iniciátor iSCSI společnosti Microsoft.

    b.  **IP adresa iniciátoru**: 10.10.10.4.

    c.  **IP adresa cílového portálu**: \<vaše první IP adresa cíle iSCSI/> 3260.

![](./media/azure-stack-network-howto-extend-datacenter/image20.png)

1.  Opakujte tento postup pro druhou kombinaci iniciátor/cíl.

    a. \* * Místní adaptér * *: iniciátor iSCSI společnosti Microsoft.

    b.  **IP adresa iniciátoru**: 10.10.11.4.

    c.  **IP adresa cílového portálu**: \<vaše druhá > cíle iSCSI IP/3260.

        ![](./media/azure-stack-network-howto-extend-datacenter/image21.png)

2.  Vyberte kartu **svazky a zařízení** a pak vyberte možnost **automaticky konfigurovat** – zobrazí se zobrazený svazek MPIO:

    ![](./media/azure-stack-network-howto-extend-datacenter/image22.png)

3.  Zpátky na kartě **cíle** vyberte **zařízení** a měli byste vidět 2 připojení k jednomu virtuálnímu pevnému disku iSCSI, který jste vytvořili dříve.

    ![](./media/azure-stack-network-howto-extend-datacenter/image23.png)

4.  Kliknutím na **tlačítko MPIO** zobrazíte další informace o zásadách vyrovnávání zatížení a cestách.

    ![](./media/azure-stack-network-howto-extend-datacenter/image24.png)

5.  Pokud chcete ukončit Windows a iniciátor iSCSI, vyberte třikrát **OK** .

6.  Spusťte správu disků (diskmgmt. msc) a měli byste být vyzváni v okně **inicializace disku** .

    ![](./media/azure-stack-network-howto-extend-datacenter/image25.png)

7.  Výběrem **OK** přijměte výchozí hodnoty, potom přejděte dolů na nový disk, klikněte pravým tlačítkem myši a vyberte **Nový jednoduchý svazek** .

8.  Projděte si průvodce a přijměte výchozí nastavení. Změňte jmenovku svazku na **iSCSIdisk1** a pak vyberte **Dokončit**.

    ![](./media/azure-stack-network-howto-extend-datacenter/image26.png)

9.  Jednotka by pak měla být formátována a dodávána s písmenem jednotky.

10. Otevřete **Průzkumníka souborů** a vyberte **Tento počítač** , aby se zobrazila vaše nová jednotka připojená k VM001.

### <a name="testing-external-storage-connectivity"></a>Testování připojení k externímu úložišti

Pokud chcete ověřit komunikaci a spustit test kopírování souborů základní, nejdřív se znovu přihlaste do **portálu pro uživatele centra Azure Stack** v systému **Azure Stack hub** a přejděte do okna **Přehled** pro **VM001** .

1.  Vyberte **připojit** a navažte připojení RDP k **VM001**

2.  Otevřete **Správce úloh** a vyberte kartu **výkon** a potom okno přichycení k pravé straně relace RDP.

3.  Otevřete **Integrované skriptovací prostředí (ISE) v prostředí Windows PowerShell** jako správce a přichycení k levé straně relace RDP. Na pravé straně ISE zavřete podokno **příkazy** a kliknutím na tlačítko **skript** rozbalte podokno bílého skriptu v horní části okna ISE.

4.  V tomto virtuálním počítači nejsou k dispozici žádné nativní moduly PowerShellu k vytvoření virtuálního pevného disku, který použijeme jako velký soubor k otestování přenosu souborů do cíle iSCSI. V takovém případě spustíme Nástroj DiskPart, který vytvoří soubor VHD. V ISE spusťte následující příkaz:

    1. `Start-Process Diskpart`

    2. Otevře se nové okno CMD a pak zadáte:  
        `**Create vdisk file="c:\\test.vhd" type=fixed maximum=5120**`
    
    ![](./media/azure-stack-network-howto-extend-datacenter/image27.png)
    
    3.  Vytvoření může chvíli trvat. Po vytvoření pro ověření vytvoření otevřete **Průzkumníka souborů** a přejděte na C:\\ – měli byste vidět nový soubor test. VHD a velikost 5 GB.

    ![](./media/azure-stack-network-howto-extend-datacenter/image28.png)

    4. Zavřete okno příkazového řádku a vraťte se do ISE a potom v okně skriptu zadejte následující příkaz. Nahraďte F:\\ písmenem cílové jednotky iSCSI použitým dříve.

    5. `Copy-Item "C:\\test.vhd" -Destination "F:\\"`

    6. Vyberte řádek v okně skriptu a spusťte stisknutím klávesy F8.

    7. Když je příkaz spuštěný, Sledujte dva síťové adaptéry a Sledujte přenos dat mezi síťovými adaptéry v VM001. Měli byste také všimnout, že každý síťový adaptér by měl zatížení nasdílet rovnoměrně.

    ![](./media/azure-stack-network-howto-extend-datacenter/image29.png)

Tento scénář byl navržený tak, aby zdůrazněn připojení mezi úlohami běžícími v Azure Stackovém centru a externím polem úložiště v tomto případě do cíle iSCSI založeného na Windows serveru. Nevedlo se k tomu, aby se jednalo o test výkonnosti, ani se nereflektují kroky, které byste měli provést, pokud jste používali alternativní zařízení založené na standardu iSCSI, ale vysvětlete některé základní důležité požadavky, které byste provedli při nasazování úloh do centra Azure Stack. a jejich propojením s úložnými systémy mimo prostředí Azure Stack hub.

## <a name="next-steps"></a>Další kroky

[Rozdíly a požadavky pro sítě Azure Stack hub](azure-stack-network-differences.md)
