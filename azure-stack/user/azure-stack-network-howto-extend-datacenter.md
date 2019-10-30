---
title: Postup rozšiřování datového centra v Azure Stack | Microsoft Docs
description: Naučte se, jak rozšířím datacentrum na Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 10/19/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/19/2019
ms.openlocfilehash: 94a9398a68be06d4735e2c082e8dc0a02281b6eb
ms.sourcegitcommit: 58e1911a54ba249a82fa048c7798dadedb95462b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73064787"
---
# <a name="how-to-extend-the-data-center-on-azure-stack"></a>Postup rozšiřování datového centra na Azure Stack

*Platí pro: Azure Stack integrovaných systémů a Azure Stack Development Kit*

Tento článek poskytuje informace o Azure Stack infrastruktury úložiště, které vám pomůžou rozhodnout, jak integrovat Azure Stack do stávajícího síťového prostředí. Prostor úložiště v Azure Stack je omezený. Úložiště můžete integrovat do úložiště mimo Azure Stack a do svého místního datového centra. Po poskytnutí Obecné diskuze o rozšíření datového centra se v článku zobrazí dva různé scénáře. Můžete se připojit k serveru Windows File Storage. Můžete se také připojit k serveru Windows iSCSI.

## <a name="overview-of-extending-storage-to-azure-stack"></a>Přehled rozšíření úložiště na Azure Stack

Diagram znázorňuje scénář, kde je jeden virtuální počítač, spouští se úloha, připojuje k a využívá externí (k VIRTUÁLNÍmu počítači a Azure Stack sám) úložiště pro účely čtení a zápisu dat atd. V tomto článku se zaměříte na jednoduché načítání souborů, ale tento příklad můžete rozšířit pro složitější scénáře, jako je vzdálené úložiště databázových souborů.

![Azure Stack rozšířené úložiště](./media/azure-stack-network-howto-extend-datacenter/image1.png)

V diagramu uvidíte, že virtuální počítač v systému Azure Stack byl nasazen s více síťovými kartami. Z redundance, ale také jako osvědčený postup úložiště, je důležité mít více cest mezi cílem a cílem. V případě, že se něco stanou složitější, jsou virtuální počítače v Azure Stack mít jak veřejné, tak privátní IP adresy, stejně jako v Azure. Pokud se k virtuálnímu počítači vyžaduje externí úložiště, může to provést jenom přes veřejnou IP adresu, protože privátní IP adresy se primárně používají v rámci Azure Stack systémů v rámci virtuální sítě a podsítí. Externí úložiště by nebylo schopné komunikovat s privátním adresním prostorem virtuálního počítače, pokud ho neprojde sítí VPN typu Site-to-site, aby se připojil k samotné virtuální síti. Proto se v tomto příkladu zaměřte na komunikaci prostřednictvím veřejného prostoru IP adres. Jedním z věcí, které se zobrazují s veřejným prostorem IP adres na obrázku, je, že existuje 2 různých veřejných podsítí fondů IP adres. Ve výchozím nastavení Azure Stack vyžaduje pouze jeden fond pro účely veřejné IP adresy, ale u redundantního směrování je třeba zvážit, aby bylo možné přidat druhý. Nemůžete ale vybrat IP adresu z konkrétního fondu, takže můžete mít virtuální počítače s veřejnými IP adresami ze stejného fondu napříč několika virtuálními síťovými kartami.

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

13. V části název DNS vyberte **Konfigurovat** a zadejte popisek názvu DNS, **Vm001** a vyberte **Uložit**. v cestě k oknu *Přehled* pak vyberte **vm001** a vraťte se do okna s popisem cesty.

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

4)  Rozbalte **Souborová služba a služba úložiště**, rozbalte **soubor & služby iSCSI Services** a zaškrtněte políčko **souborový server** . Po dokončení zavřete **Správce serveru**.

5)  Znovu otevřete **Správce serveru** a vyberte **Souborová služba a služba úložiště**.

6)  Vyberte **sdílené složky**.

7)  V **poli sdílené složky**vyberte možnost **Chcete-li vytvořit sdílenou složku, spusťte odkaz Průvodce vytvořením nové sdílené složky** .

8)  V poli **Průvodce novou sdílenou složkou** vyberte **sdílená složka SMB – rychlá**a potom vyberte **Další**a pokračujte v průvodci a vyberte **svazek C:\\\\** .

9)  Zadejte název sdílené složky **TestStorage** a pak vyberte **Další**.

10) Zpátky v **průvodci novou sdílenou složkou**vyberte **Další** a pak **vytvořit**a pak **Zavřít**.

Nyní jste vytvořili sdílenou složku na souborovém serveru.

### <a name="testing-file-storage-performance-and-connectivity"></a>Testování výkonu a připojení úložiště souborů

Pokud chcete zjistit komunikaci a spustit některé testy základní, přihlaste se zpátky do portálu Azure Stack User Portal v systému **Azure Stack** a přejděte do okna **Přehled** pro **VM001**.

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

Pokud jste ještě nenainstalovali virtuální počítač s Windows serverem 2019 na Azure Stack, postupujte prosím podle kroků v části [nasazení virtuálního počítače s Windows serverem 2019 na Azure Stack](#deploy-the-windows-server-2019-vm-on-azure-stack). Pak můžete nakonfigurovat cíl iSCSI Windows serveru 2019.

### <a name="configure-the-windows-server-2019-iscsi-target"></a>Konfigurace cíle iSCSI Windows serveru 2019

Pro účely tohoto druhého scénáře Ověřte konfiguraci, kde je cílovým serverem iSCSI Windows Server 2019 virtuální počítač, na kterém běží technologie Hyper-V. Tento virtuální počítač se nakonfiguruje s osmi virtuálními procesory, jedním souborem VHDX a nejdůležitějšími dvěma virtuálními síťovými adaptéry. V ideálním scénáři budou mít tyto síťové adaptéry různé směrovatelné podsítě, ale v tomto testu budete mít síťové adaptéry ve stejné podsíti.

![](./media/azure-stack-network-howto-extend-datacenter/image5.png)

Pro váš cíl iSCSI může to být Windows Server 2016 nebo 2019, fyzický nebo virtuální, spuštěný v Hyper-V, VMware nebo na jiné platformě podle vašeho výběru. Klíč se tady zaměřuje, je připojení k Azure Stackmu systému a odchází od něj, ale má několik cest mezi zdrojem a cílem, protože poskytuje další redundanci a propustnost.

Aktualizujte souborový server s nejnovějšími kumulativními aktualizacemi a opravami a restartujte ho, než budete pokračovat v konfiguraci cílového serveru iSCSI.

Po aktualizaci a restartu teď můžete tento server nakonfigurovat jako cílový server iSCSI.

## <a name="next-steps"></a>Další kroky

[Rozdíly a požadavky pro Azure Stack sítě](azure-stack-network-differences.md)  