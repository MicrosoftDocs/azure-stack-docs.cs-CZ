---
title: Postup rozšiřování datového centra v centru Azure Stack | Microsoft Docs
description: Naučte se, jak rozšířím datacentrum na Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 11/07/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 11/07/2019
ms.openlocfilehash: 92e82f549cddf51b1cbd6764cc122acc3ca8fdfc
ms.sourcegitcommit: ed44d477b9fd11573d1e0d1ed3a3c0ef4512df53
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73846086"
---
# <a name="how-to-extend-the-data-center-on--azure-stack-hub"></a>Postup rozšiřování datového centra v centru Azure Stack

*Platí pro: Azure Stack integrovaných systémů centra a Azure Stack centrum pro vývoj*

Tento článek poskytuje Azure Stack informace o infrastruktuře úložiště centra, které vám pomůžou rozhodnout, jak integrovat Azure Stack do stávajícího síťového prostředí. Po poskytnutí Obecné diskuze o rozšíření datového centra se v článku zobrazí dva různé scénáře. Můžete se připojit k serveru Windows File Storage. Můžete se také připojit k serveru Windows iSCSI.

## <a name="overview-of-extending-storage-to-azure-stack-hub"></a>Přehled rozšíření úložiště do centra Azure Stack

Existují situace, kdy vaše data uložená ve veřejném cloudu nejsou dostatečná. Možná máte úlohu virtualizované databáze náročné na výpočetní výkon, která je citlivá na latenci a dobu odezvy na veřejný cloud může ovlivnit výkon databázového zatížení. Možná existují data v místním prostředí, která jsou držená na souborovém serveru, na serveru NAS nebo v poli úložiště iSCSI, ke kterému se musí přicházet v místních úlohách a musí se nacházet v místním prostředí, aby se splnily zákonné předpisy nebo cíle dodržování předpisů.  Jedná se jenom o dva scénáře, ve kterých jsou data uložená místně, ale jsou důležitá pro mnoho organizací.

Proto nestačí hostovat tato data v účtech úložiště v Azure Stack nebo uvnitř virtualizovaného souborového serveru spuštěného v Azure Stackm systému? Na rozdíl od Azure je Azure Stack úložiště omezené. Kapacita, kterou máte k dispozici pro vaše využití, závisí výhradně na kapacitě jednotlivých uzlů, kterou jste si zvolili k nákupu, a navíc k počtu uzlů, které máte. A vzhledem k tomu, že Azure Stack je řešení sblížené s technologií Hyper-v případě, že chcete rozšířit kapacitu úložiště tak, aby splňovala požadavky na využití, musíte také rozšířit výpočetní nároky prostřednictvím přidávání uzlů.  To může být potenciálně nenáročné, zejména pokud je potřeba dodatečnou kapacitu pro studené, archivní úložiště, které by bylo možné přidat za nízké náklady mimo Azure Stack systém.

Díky tomu se zobrazí scénář, který se vám bude týkat. Jak můžete připojit Azure Stack systémy, virtualizované úlohy běžící v Azure Stack, a to jednoduše a efektivně až do úložných systémů mimo Azure Stack, přístupné prostřednictvím sítě.

## <a name="design-for-extending-storage"></a>Návrh pro rozšíření úložiště

Diagram znázorňuje scénář, kde je jeden virtuální počítač, spouští se úloha, připojuje k a využívá externí (k VIRTUÁLNÍmu počítači a Azure Stack sám) úložiště pro účely čtení a zápisu dat atd. V tomto článku se zaměříte na jednoduché načítání souborů, ale tento příklad můžete rozšířit pro složitější scénáře, jako je vzdálené úložiště databázových souborů.

![Azure Stack rozšířené úložiště](./media/azure-stack-network-howto-extend-datacenter/image1.png)

V diagramu uvidíte, že virtuální počítač v systému Azure Stack byl nasazen s více síťovými kartami. Z redundance, ale také jako osvědčený postup úložiště, je důležité mít více cest mezi cílem a cílem. V případě, že se něco stanou složitější, jsou virtuální počítače v Azure Stack mít jak veřejné, tak privátní IP adresy, stejně jako v Azure. Pokud se k virtuálnímu počítači vyžaduje externí úložiště, může to provést jenom přes veřejnou IP adresu, protože privátní IP adresy se primárně používají v rámci Azure Stack systémů v rámci virtuální sítě a podsítí. Externí úložiště by nebylo schopné komunikovat s privátním adresním prostorem virtuálního počítače, pokud ho neprojde sítí VPN typu Site-to-site, aby se připojil k samotné virtuální síti. Proto se v tomto příkladu zaměřte na komunikaci prostřednictvím veřejného prostoru IP adres. Jedním z věcí, které se zobrazují s veřejným prostorem IP adres na obrázku, je, že existují dvě různé veřejné podsítě fondů IP adres. Ve výchozím nastavení Azure Stack vyžaduje pouze jeden fond pro účely veřejné IP adresy, ale u redundantního směrování je třeba zvážit, aby bylo možné přidat druhý. Nemůžete ale vybrat IP adresu z konkrétního fondu, takže můžete mít virtuální počítače s veřejnými IP adresami ze stejného fondu napříč několika virtuálními síťovými kartami.

V tomto příkladu se můžete domnívat, že se zajímá směrování mezi hraničními zařízeními a externím úložištěm, a přenos může správně procházet síť. V tomto příkladu nezáleží na tom, jestli je páteřní síť 1 GbE, 10 GbEs, 25 GbE nebo ještě rychlejší, ale to by bylo důležité vzít v úvahu při plánování integrace, aby se vyřešily požadavky na výkon všech aplikací, které přistupují k tomuto externímu úložišti.

## <a name="connect-to-a-windows-server-file-server-storage"></a>Připojení k úložišti souborového serveru s Windows serverem

V tomto scénáři nasaďte a nakonfigurujte virtuální počítač s Windows serverem 2019 na Azure Stack a připravte ho pro připojení k externímu souborovému serveru, který může taky používat Windows Server 2019. V případě potřeby nastavte klíčové funkce, jako je například protokol SMB vícekanálový, a optimalizujte tak výkon a možnosti připojení mezi virtuálním počítačem a externím úložištěm.

### <a name="deploy-the-windows-server-2019-vm-on-azure-stack"></a>Nasazení virtuálního počítače s Windows serverem 2019 na Azure Stack

1.  Z **portálu pro správu Azure Stack**za předpokladu, že je tento systém správně zaregistrován a je připojený k webu Marketplace, vyberte **Správa Marketplace** a pak za předpokladu, že ještě nemáte bitovou kopii Windows serveru 2019, vyberte  **Přidejte z Azure** a pak vyhledejte **windows server 2019**a přidejte image **Windows serveru 2019 Datacenter** .

    ![](./media/azure-stack-network-howto-extend-datacenter/image2.png)

    Stažení bitové kopie systému Windows Server 2019 může trvat delší dobu.

2.  Až budete mít v prostředí Azure Stack image Windows serveru 2019, přihlaste se k portálu Azure Stack User Portal * *.

3.  Po přihlášení k portálu Azure Stack User Portal se ujistěte, že máte [předplatné nabídky](https://docs.microsoft.com/azure-stack/operator/azure-stack-subscribe-plan-provision-vm?view=azs-1908), které vám umožní zřídit prostředky IaaS (výpočty, úložiště a síť).

4.  Jakmile budete mít k dispozici předplatné, vraťte se na **řídicí panel** na portálu Azure Stack User Portal, vyberte **vytvořit prostředek**, vyberte **COMPUTE** a pak vyberte **položku galerie datacenter Windows serveru 2019**.

5.  V okně **základy** :

    a.  **Název**: VM001

    b.  **Uživatelské jméno**: localadmin

    c.  **Heslo** a **potvrzení hesla**: \<hesla podle svého výběru >

    d.  **Předplatné**: \<předplatné podle vašeho výběru s využitím výpočetních prostředků/úložiště/síťových prostředků >

    e. **Skupina prostředků**: vytvořit novou: storagetesting

    f.  Pak vyberte **OK** .

6.  V okně **Zvolte velikost** vyberte **Standard_F8s_v2**.

7.  V okně **Nastavení** vyberte **virtuální síť** a v okně **vytvořit virtuální síť** upravte adresní prostor, který má být **10.10.10.0/23** , a aktualizujte rozsah adres podsítě na **10.10.10.0/24** a pak vyberte **OK.** .

8.  Vyberte **veřejnou IP adresu**a v okně **Vytvoření veřejné IP adresy** vyberte **statické**.

9.  V **Vyberte veřejné příchozí porty**vyberte **RDP (3389)** .

10. Ponechte ostatní výchozí nastavení a vyberte **OK**.
    
    ![](./media/azure-stack-network-howto-extend-datacenter/image3.png)

11. Přečtěte si souhrn, počkejte na ověření a pak kliknutím na **OK** zahajte nasazení. Nasazení by se mělo dokončit během přibližně 10 minut.

12. Až se nasazení dokončí, v části **prostředek** vyberte název virtuálního počítače, **VM001** se bude moct přejít na okno *Přehled* .
    
    ![](./media/azure-stack-network-howto-extend-datacenter/image4.png)

13. V části název DNS vyberte **Konfigurovat** a zadejte popisek názvu DNS, * * vm001 a vyberte **Uložit**a potom v části s popisem cesty vyberte **vm001** a vraťte se do okna *Přehled* .

14. Na pravé straně okna Přehled vyberte **storagetesting-VNet/default** v textu virtuální sítě nebo podsítě.

15. V okně storagetesting-VNet vyberte **podsítě** a pak **+ podsíť**a potom v okně Nová podsíť zadejte následující informace: 

    a.  **Název**: podsíť subnet2

    b.  **Rozsah adres (blok CIDR)** : 10.10.11.0/24

    c. **Skupina zabezpečení sítě**: žádné

    d.  **Směrovací tabulka**: žádné

    e. Vyberte **OK**:

16. Po uložení vyberte v popisu cesty **VM001** a vraťte se do okna Přehled.

17. Vyberte **sítě**.

18. Vyberte **připojit síťové rozhraní** a pak vyberte **vytvořit síťové rozhraní**.

19. V okně **vytvořit síťové rozhraní** :

    a.  **Název**: vm001nic2

    b.  **Podsíť**: Ujistěte se, že je podsíť 10.10.11.0/24.

    c. **Skupina zabezpečení sítě**: VM001-NSG

    d.  **Skupina prostředků**: storagetesting

20. Po úspěšném vytvoření vyberte v navigačním panelu s popisem cesty možnost **VM001** a kliknutím na **zastavit** vypněte virtuální počítač.

21. Jakmile se virtuální počítač zastaví (přidělení zrušeno), vyberte **sítě**, vyberte **připojit síťové rozhraní** a pak vyberte **vm001nic2**a pak vyberte **OK**. Za chvíli přidáte další síťové rozhraní k virtuálnímu počítači.

22. V okně **sítě** vyberte kartu **vm001nic2** a pak vyberte **síťové rozhraní: vm001nic2**.

23. V okně rozhraní vm001nic vyberte **Konfigurace protokolu IP**a ve středu okna vyberte **ipconfig1**.

24. V okně nastavení ipconfig1 vyberte možnost **povoleno** pro veřejnou IP adresu a vyberte **konfigurovat požadovaná nastavení**, **vytvořit nové** a jako název zadejte vm001nic2pip. Vyberte možnost **static** a pak klikněte na **tlačítko OK** a pak na **Uložit**.

25. Po úspěšném uložení se vraťte do okna Přehled VM001 a výběrem **Spustit** spusťte nakonfigurovaný virtuální počítač s Windows serverem 2019.

### <a name="configure-the-windows-server-2019-file-server-storage"></a>Konfigurace úložiště souborového serveru systému Windows Server 2019

V tomto prvním příkladu ověřujete konfiguraci, ve které je souborový server s Windows serverem 2019 virtuálním počítačem, který běží na Hyper-V. Tento virtuální počítač se nakonfiguruje s osmi virtuálními procesory, jedním souborem VHDX a nejdůležitějšími dvěma virtuálními síťovými adaptéry. V ideálním scénáři budou mít tyto síťové adaptéry různé směrovatelné podsítě, ale v tomto testu budou mít síťové adaptéry ve stejné podsíti.

![](./media/azure-stack-network-howto-extend-datacenter/image5.png)

Na souborovém serveru může být Windows Server 2016 nebo 2019, fyzický nebo virtuální, spuštěný v Hyper-V, VMware nebo na jiné platformě podle vašeho výběru. Klíč se tady zaměřuje, je připojení k Azure Stackmu systému a odchází od něj, ale má několik cest mezi zdrojem a cílem, protože poskytuje další redundanci a umožňuje vyšší využití pokročilých funkcí. výkon, jako je například protokol SMB vícekanálový.

Aktualizujte souborový server s nejnovějšími kumulativními aktualizacemi a opravami a restartujte ho, než budete pokračovat v konfiguraci sdílených složek.

Po aktualizaci a restartu teď můžete tento server nakonfigurovat jako souborový server.

1) Na počítači souborového serveru spusťte příkaz **ipconfig/all** z **cmd** a poznamenejte si **Server DNS** , který souborový server používá.

2)  Otevřete **Správce serveru** a vyberte **Spravovat**a **přidejte role a funkce**.

3)  Vyberte **Další**, vyberte **instalace na základě rolí nebo na základě funkcí**a proveďte další výběry, dokud se nedostanete na stránku **Vybrat role serveru** .

4)  Rozbalte **Souborová služba a služba úložiště**, rozbalte **soubor & služby iSCSI** a vyberte **souborový server**. Po dokončení zavřete **Správce serveru**.

5)  Znovu otevřete **Správce serveru** a vyberte **Souborová služba a služba úložiště**.

6)  Vyberte **sdílené složky**.

7)  V **poli sdílené složky**vyberte možnost **Chcete-li vytvořit sdílenou složku, spusťte odkaz Průvodce vytvořením nové sdílené složky** .

8)  V poli **Průvodce novou sdílenou složkou** vyberte **sdílená složka SMB – rychlá**a potom vyberte **Další**a pokračujte v průvodci a vyberte **svazek C:\\\\** .

9)  Zadejte název sdílené složky **TestStorage** a pak vyberte **Další**.

10) Zpátky v **průvodci novou sdílenou složkou**vyberte **Další** a pak **vytvořit**a pak **Zavřít**.

Nyní jste vytvořili sdílenou složku na souborovém serveru.

### <a name="testing-file-storage-performance-and-connectivity"></a>Testování výkonu a připojení úložiště souborů

Pokud chcete zjistit komunikaci a spustit některé testy základní, přihlaste se k portálu Azure Stack User Portal v systému **Azure Stack** a přejděte do okna **Přehled** pro **VM001**.

1)  Vyberte **připojit** a navažte připojení RDP k VM001.

2)  Aby bylo možné komunikovat přes název hostitele, je třeba upravit soubor **hostitelů** . v produkčním prostředí by se ale služba DNS optimálně nakonfigurovali tak, aby se názvy automaticky vyřešily. Můžete také použít IP adresy. V našem příkladu ale upravíte soubor **hostitelů** .

3)  Otevřete **Poznámkový blok**a ujistěte se, že **spouštíte jako správce**.

4)  Po otevření poznámkového bloku vyberte **soubor**, **otevřete** a přejděte do části c:\\Windows\System32\Drivers\etc\, a v pravém dolním rohu dialogového okna Otevřít vyberte **všechny soubory** . Vyberte soubor **hostitelé** a vyberte **otevřít**.

5)  Úpravou souboru hostitelů přidejte IP adresu a název DNS pro souborový server.

    ![](./media/azure-stack-network-howto-extend-datacenter/image6.png)

6)  Uložte soubor a zavřete Poznámkový blok.

7)  Otevřete příkazy cmd a příkazového **řádku** pro zadání názvu souborového serveru, který jste zadali v souboru Hosts. To by mělo vrátit IP adresu jednoho ze síťových adaptérů na souborovém serveru, a tak ručně otestovat komunikaci s druhým adaptérem pomocí testování IP adresy.

## <a name="connect-to-a-windows-server-iscsi-target-server"></a>Připojení k cílovému serveru iSCSI ve Windows serveru

V tomto scénáři nasadíte a nakonfigurujete virtuální počítač s Windows serverem 2019 na Azure Stack a připravíte ho pro připojení k externímu cílovému serveru iSCSI, který bude taky používat Windows Server 2019.

> [!Note]  
> Pokud jste už nějaký scénář dokončili, přeskočte dopředu a přizpůsobte si počítače.

### <a name="deploy-the-windows-server-2019-vm-on-azure-stack"></a>Nasazení virtuálního počítače s Windows serverem 2019 na Azure Stack

Pokud jste ještě nenainstalovali virtuální počítač s Windows serverem 2019 na Azure Stack, postupujte podle kroků v části [nasazení virtuálního počítače s Windows serverem 2019 na Azure Stack](#deploy-the-windows-server-2019-vm-on-azure-stack). Pak můžete nakonfigurovat cíl iSCSI Windows serveru 2019.

### <a name="configure-the-windows-server-2019-iscsi-target"></a>Konfigurace cíle iSCSI Windows serveru 2019

Pro účely tohoto druhého scénáře Ověřte konfiguraci, kde je cílovým serverem iSCSI Windows Server 2019 virtuální počítač, na kterém běží technologie Hyper-V. Tento virtuální počítač se nakonfiguruje s osmi virtuálními procesory, jedním souborem VHDX a nejdůležitějšími dvěma virtuálními síťovými adaptéry. V ideálním scénáři budou mít tyto síťové adaptéry různé směrovatelné podsítě, ale v tomto testu budete mít síťové adaptéry ve stejné podsíti.

![](./media/azure-stack-network-howto-extend-datacenter/image5.png)

Pro váš cíl iSCSI může to být Windows Server 2016 nebo 2019, fyzický nebo virtuální, spuštěný v Hyper-V, VMware nebo na jiné platformě podle vašeho výběru. Klíč se tady zaměřuje, je připojení k Azure Stackmu systému a odchází od něj, ale má několik cest mezi zdrojem a cílem, protože poskytuje další redundanci a propustnost.

Aktualizujte souborový server s nejnovějšími kumulativními aktualizacemi a opravami a restartujte ho, než budete pokračovat v konfiguraci cílového serveru iSCSI.

Po aktualizaci a restartu teď můžete tento server nakonfigurovat jako cílový server iSCSI.

1. Otevřete **Správce serveru** > **Spravovat** > **Přidání rolí a funkcí**.

2. Po otevření vyberte **Další**, vyberte **instalace na základě rolí nebo na základě funkcí**a předem proveďte výběr, dokud se nedostanete na stránku **Vybrat role serveru** .

3. Rozbalte **Souborová služba a služba úložiště**, rozbalte **soubor & služby iSCSI Services** a vyberte **cílový server iSCSI**, přijměte výzvy k přidání nových funkcí a pak pokračujte až do dokončení.

    ![](./media/azure-stack-network-howto-extend-datacenter/image8.png)
    
    Po dokončení zavřete **Správce serveru.**

4. Otevřete **Průzkumníka souborů,** přejděte na `C:\\` a **vytvořte novou složku**s názvem `iSCSI`.

5. V nabídce na levé straně znovu otevřete **Správce serveru** > **Souborová služba a služba úložiště** .

6. Vyberte **iSCSI** a vyberte "Pokud**chcete vytvořit virtuální disk iSCSI, spusťte Průvodce vytvořením virtuálního disku iSCSI**.

7. Na stránce **Vyberte umístění virtuálního disku iSCSI** vyberte **zadat vlastní cestu** a vyhledejte `C:\\iSCSI`. Vyberte **Další**.

8. Dejte virtuálnímu disku iSCSI název `iSCSIdisk1` a volitelně také popis a pak vyberte **Další**.

9. Nastavte velikost virtuálního disku na `10GB` a vyberte možnost **Pevná velikost** a vyberte možnost **Další**.

    ![](./media/azure-stack-network-howto-extend-datacenter/image9.png)

10. Vzhledem k tomu, že se jedná o nový cíl, vyberte **Nový cíl iSCSI** a vyberte **Další**.

11. Na stránce **Zadejte název cíle** zadejte **TARGET1** a vyberte **Další**.

12. Na stránce **zadat přístupové servery** vyberte **Přidat**. Tím se otevře dialogové okno, kde můžete zadat konkrétní **iniciátory** , které budou mít autorizaci připojit se k cíli iSCSI.

13. V **okně Přidat ID iniciátoru** vyberte **zadat hodnotu pro vybraný typ** a v části **typ** ověřte, že je v rozevírací nabídce vybraný identifikátor IQN. Zadejte `iqn.1991-05.com.microsoft:<computername>`, kde `<computername>` je **název počítače** **VM001**. Vyberte **Další**.

    ![](./media/azure-stack-network-howto-extend-datacenter/image10.png)

14. Na stránce **Povolit ověřování** ponechte pole prázdné a pak vyberte **Další**.

15. Potvrďte svoje výběry a vyberte **vytvořit**a pak zavřít.

    ![](./media/azure-stack-network-howto-extend-datacenter/image11.png)

Měl by se zobrazit virtuální disk iSCSI vytvořený v Správce serveru.

### <a name="configure-the-windows-server-2019-iscsi-initiator-and-mpio"></a>Konfigurace iniciátoru iSCSI Windows serveru 2019 a funkce MPIO

Pokud chcete nastavit iniciátor iSCSI, přihlaste se k **portálu User Portal** v systému **Azure Stack** a přejděte k oknu **Přehled** pro **VM001.**

1.  Navažte připojení RDP k VM001. Po připojení otevřete **Správce serveru**.

2.  Vyberte možnost **Přidat role a funkce**a přijměte výchozí hodnoty, dokud se nedostanete na stránku **funkce** .

3.  Na stránce **funkce přidejte funkci** **Multipath i/O** a vyberte **Další**.

    ![](./media/azure-stack-network-howto-extend-datacenter/image12.png)

4.  V **případě potřeby automaticky Restartujte cílový server** a vyberte **nainstalovat**a pak vyberte **Zavřít.** Po dokončení bude restart pravděpodobně požadován, takže po dokončení se znovu připojí k VM001.

5.  Zpět v **Správce serveru**počkejte, než se **Instalace funkce MPIO dokončí**, vyberte **Zavřít**a pak vyberte **nástroje** a vyberte **MPIO**.

6.  Vyberte kartu **zjistit více cest** a zaškrtněte políčko pro **Přidání podpory pro zařízení iSCSI** > **Přidat**a pak vyberte **Ano** pro **restartování** VM001. Vyberte **OK a** pak ručně restartujte.

    ![](./media/azure-stack-network-howto-extend-datacenter/image13.png)

1.  Po restartování navažte **nové připojení RDP k VM001**.

2.  Po připojení otevřete **Správce serveru**vyberte **nástroje** > **iniciátor iSCSI**.

3.  Po otevření Microsoft iSCSI vyberte **Ano** , aby se služba iSCSI spouštěla ve výchozím nastavení.

    ![](./media/azure-stack-network-howto-extend-datacenter/image17.png)

4.  Vyberte kartu **zjišťování** .

5.  Vyberte tlačítko **Vyhledat portál** . Teď budete přidávat dva cíle.

6.  Zadejte první IP adresu vašeho cílového serveru iSCSI a vyberte **Upřesnit**.

    ![](./media/azure-stack-network-howto-extend-datacenter/image15.png)

7. Pro **Pokročilá nastavení**vyberte následující:

    - Místní adaptér: iniciátor iSCSI Microsoftu

    - IP adresa iniciátoru: 10.10.10.4

    - Když se nastaví, vyberte **OK**.

8.  V možnosti **vyhledat cílový portál**vyberte **OK** .

9.  Opakujte tento postup s následujícím:

    - IP adresa: vaše druhá cílová IP adresa iSCSI

    - Místní adaptér: iniciátor iSCSI Microsoftu

    - IP adresa iniciátoru: 10.10.11.4

10. Vaše cílové portály by měly vypadat takto, s vlastními IP **adresami** cíle iSCSI pod sloupcem adresa.

    ![](./media/azure-stack-network-howto-extend-datacenter/image16.png)

11. Vyberte kartu **cíle** a pak vyberte svůj cíl iSCSI. Vyberte **Connect** (Připojit).

12. V možnosti **připojit k cíli**vyberte **Povolit více cest** a vyberte **Upřesnit**.

    ![](./media/azure-stack-network-howto-extend-datacenter/image17.png)

13. Pro příkaz **připojit k cíli**zadejte následující informace:

    - Místní adaptér: iniciátor iSCSI Microsoftu

    - IP adresa iniciátoru: 10.10.10.4

    - IP adresa cílového portálu: \<vaše první IP adresu cíle iSCSI/3260 >

    - Vyberte **OK**.

    ![](./media/azure-stack-network-howto-extend-datacenter/image18.png)

1.  Opakujte tento postup pro druhou kombinaci iniciátor/cíl.

    - Místní adaptér: iniciátor iSCSI Microsoftu

    - IP adresa iniciátoru: 10.10.11.4

    - IP adresa cílového portálu: \<vaše druhá cílová IP adresa iSCSI/3260 >

    ![](./media/azure-stack-network-howto-extend-datacenter/image19.png)

1.  Vyberte kartu **svazky a zařízení** a pak vyberte možnost **automaticky konfigurovat** – zobrazí se zobrazený svazek MPIO:

    ![](./media/azure-stack-network-howto-extend-datacenter/image20.png)

2.  Zpátky na kartě **cíle** vyberte **zařízení** a měli byste vidět dvě připojení k jednomu virtuálnímu pevnému disku iSCSI, který jste vytvořili dříve.

    ![](./media/azure-stack-network-howto-extend-datacenter/image20.png)

3.  Kliknutím na **tlačítko MPIO** zobrazíte další informace o zásadách vyrovnávání zatížení a cestách.

    ![](./media/azure-stack-network-howto-extend-datacenter/image21.png)

4.  Pokud chcete ukončit Windows a iniciátor iSCSI, vyberte třikrát **OK** .

5.  Spusťte správu disků (diskmgmt. msc) a měli byste se zobrazit okno s výzvou k **inicializaci disku** .

    ![](./media/azure-stack-network-howto-extend-datacenter/image22.png)

6.  Výběrem **OK** přijměte výchozí hodnoty, potom přejděte dolů k novému disku, klikněte pravým tlačítkem myši a vyberte **Nový jednoduchý svazek**.

7.  Projděte si průvodce a přijměte výchozí nastavení. Změňte jmenovku svazku na **iSCSIdisk1** a pak vyberte **Dokončit**.

    ![](./media/azure-stack-network-howto-extend-datacenter/image23.png)

8.  Jednotka by pak měla být formátována a dodávána s písmenem jednotky.

9.  Otevřete **Průzkumníka souborů** > **tomto počítači** , abyste viděli novou jednotku připojenou k VM001.

### <a name="test-external-storage-connectivity"></a>Test připojení k externímu úložišti

Pokud chcete ověřit komunikaci a spustit test kopírování souborů základní, přihlaste se k **portálu User Portal** v systému **Azure Stack** a přejděte k oknu **Přehled** pro **VM001**.

1. Vyberte **připojit** a navažte připojení RDP k **VM001**.

2. Otevřete **Správce úloh** a pak vyberte kartu **výkon** . přichycení okna k pravé straně relace RDP.

3. Otevřete **Integrované skriptovací prostředí (ISE) v prostředí Windows PowerShell** jako správce a přichycení k levé straně relace RDP. Na pravé straně ISE zavřete podokno **příkazy** a kliknutím na tlačítko **skript** rozbalte podokno bílého skriptu v horní části okna ISE.

4. V tomto virtuálním počítači nejsou k dispozici žádné nativní moduly PowerShellu k vytvoření virtuálního pevného disku, který použijete jako velký soubor k otestování přenosu souborů do cíle iSCSI. V takovém případě spustíte nástroj DiskPart a vytvoříte soubor VHD. V ISE spusťte následující příkaz:

    1. `Start-Process Diskpart`

    2. Otevře se nový příkazový řádek. Zadejte následující příkaz:<br>`Create vdisk file="c:\\test.vhd" type=fixed maximum=5120`

    ![](./media/azure-stack-network-howto-extend-datacenter/image24.png)

    Vytvoření může chvíli trvat. Po vytvoření pro ověření vytvoření otevřete **Průzkumníka souborů** a přejděte na C:\\ – měli byste vidět nový soubor test. VHD a velikost 5 GB.

    ![](./media/azure-stack-network-howto-extend-datacenter/image25.png)

    Zavřete příkazový řádek. Vraťte se do ISE, do ISE zadejte následující příkaz. Nahraďte F:\\ písmenem cílové jednotky iSCSI použitým dříve.

    1. `Copy-Item "C:\\test.vhd" -Destination "F:\\"`

    2. Vyberte řádek v ISE. Na klávesnici stiskněte klávesu **F8** .

    3. Při spuštění příkazu Sledujte dva síťové adaptéry a Prohlédněte si přenos dat mezi síťovými adaptéry v VM001. Měli byste také všimnout, že každý síťový adaptér by měl zatížení nasdílet rovnoměrně.

        ![](./media/azure-stack-network-howto-extend-datacenter/image26.png)

Tento scénář byl navržený tak, aby zdůrazněn připojení mezi úlohami běžícími na Azure Stack a externím polem úložiště v tomto případě jako cíl iSCSI založený na systému Windows Server. Nevedlo se k tomu, aby se jednalo o test výkonnosti, ani se nereflektují kroky, které byste měli provést, pokud jste používali alternativní zařízení založené na standardu iSCSI, ale vysvětlete některé základní důležité požadavky, které byste provedli při nasazování úloh na Azure Stack. a jejich propojením s úložnými systémy mimo prostředí Azure Stack.

## <a name="next-steps"></a>Další kroky

[Rozdíly a požadavky pro Azure Stack sítě](azure-stack-network-differences.md)  