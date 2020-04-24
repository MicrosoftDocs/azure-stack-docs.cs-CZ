---
title: Nasazení virtuálního počítače se systémem Linux do centra Azure Stack
description: Nasaďte aplikaci do centra Azure Stack.
author: mattbriggs
ms.topic: overview
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: 63fffbf1a9dcc5048286f93b18b20eb72174b8cd
ms.sourcegitcommit: 4138a2a15f78e7db38b3a29acc963a71937146fd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2020
ms.locfileid: "77704195"
---
# <a name="deploy-a-linux-vm-to-host-a-web-app-in-azure-stack-hub"></a>Nasazení virtuálního počítače se systémem Linux pro hostování webové aplikace v centru Azure Stack

Virtuální počítač se systémem Linux můžete vytvořit a nasadit pomocí bitové kopie Ubuntu v Azure Marketplace k hostování webové aplikace, kterou jste vytvořili s webovým rozhraním. 

Tento virtuální počítač může hostovat webové aplikace pomocí:

- **Python**: společná webová rozhraní Pythonu zahrnují baňky, láhev a Django.
- **Přejít**: architektury Common směřuje zahrnují Revel, Martini, Gocraft/web a Gorilla. 
- **Ruby**: nastavte Ruby na železnici jako architekturu pro doručování webových aplikací Ruby. 
- **Java**: k vývoji webových aplikací, které odesíláte na server Apache Tomcat, použijte Java. Tomcat můžete nainstalovat na Linux a potom nasadit soubory Java WAR přímo na server. 

Pokyny v tomto článku vám pomohou začít pracovat se všemi webovými aplikacemi, architekturou a technologií back-endu, které používají operační systém Linux. Pak můžete pomocí centra Azure Stack spravovat infrastrukturu a pomocí nástrojů pro správu v rámci vaší technologie zpracovávat úlohy údržby pro vaši aplikaci.

## <a name="deploy-a-linux-vm-for-a-web-app"></a>Nasazení virtuálního počítače se systémem Linux pro webovou aplikaci

V tomto procesu vytvoříte tajný klíč, použijete základní bitovou kopii virtuálního počítače se systémem Linux, určíte konkrétní atributy virtuálního počítače a potom vytvoříte virtuální počítač. Po vytvoření virtuálního počítače otevřete porty, které jsou nezbytné pro práci s virtuálním počítačem a pro virtuální počítač k hostování vaší aplikace. V dalším kroku vytvoříte název DNS. Nakonec se připojíte k VIRTUÁLNÍmu počítači a aktualizujte počítač pomocí nástroje apt-get. Po dokončení procesu budete mít virtuální počítač v instanci centra Azure Stack, který je připravený k hostování vaší webové aplikace.

Než začnete, ujistěte se, že máte všechno, co potřebujete.

## <a name="prerequisites"></a>Požadavky

- Předplatné centra Azure Stack s přístupem k imagi LTS serveru Ubuntu 16,04. Můžete použít novější verzi image, ale tyto pokyny se zapisují s 16,04 LTS. Pokud tuto image nemáte, obraťte se na svého operátora cloudu, abyste získali image do tržiště centra Azure Stack.

## <a name="deploy-the-vm-by-using-the-portal"></a>Nasazení virtuálního počítače pomocí portálu

Pokud chcete nasadit virtuální počítač, postupujte podle pokynů v následujících částech.

### <a name="create-your-vm"></a>Vytvoření virtuálního počítače

1. Vytvořte veřejný klíč Secure Shell (SSH) pro váš server. Další informace najdete v tématu [Jak používat veřejný klíč SSH](azure-stack-dev-start-howto-ssh-public-key.md).
1. Na portálu centra Azure Stack vyberte **vytvořit prostředek** > **COMPUTE** > **Ubuntu Server 16,04 LTS**.

    ![Nasazení webové aplikace do virtuálního počítače centra Azure Stack](media/azure-stack-dev-start-howto-deploy-linux/001-portal-compute.png)

4. V podokně **vytvořit virtuální počítač** pro **1. Konfigurovat základní nastavení**:

    a. Zadejte **název vašeho virtuálního počítače**.

    b. Vyberte **typ disku virtuálního počítače**, buď **SSD úrovně Premium** (pro disky Premium [SSD]) nebo **HDD úrovně Standard** (pro disky Standard [HDD]).

    c. Zadejte své **uživatelské jméno**.

    d. Jako **veřejný klíč SSH**vyberte **typ ověřování** .

    e. Načtěte veřejný klíč SSH, který jste vytvořili. Otevřete ho v textovém editoru, zkopírujte klíč a vložte ho do pole **veřejný klíč SSH** . Zahrňte text z `---- BEGIN SSH2 PUBLIC KEY ----` do `---- END SSH2 PUBLIC KEY ----`. Vložte celý blok textu do pole klíče:

    ```text  
    ---- BEGIN SSH2 PUBLIC KEY ----
    Comment: "rsa-key-20190207"
    <Your key block>
    ---- END SSH2 PUBLIC KEY ----
    ```

    f. Vyberte předplatné pro vaši instanci centra Azure Stack.

    g. Vytvořte novou skupinu prostředků nebo použijte existující, v závislosti na tom, jak chcete organizovat prostředky pro vaši aplikaci.

    h. Vyberte své umístění. Azure Stack Development Kit (ASDK) je obvykle v *místní* oblasti. Umístění závisí na vaší instanci centra Azure Stack.
1. Pro **2. Velikost**, zadejte:
    - Vyberte velikost dat a paměti RAM pro váš virtuální počítač, který je k dispozici ve vaší instanci centra Azure Stack.
    - Můžete buď procházet seznam, nebo filtrovat velikost virtuálního počítače podle **typu COMPUTE**, **CPU**a **prostoru úložiště**.
    
    > [!NOTE]
    > - Uvedené ceny jsou odhady ve vaší místní měně. Zahrnují jenom náklady na infrastrukturu Azure a všechny slevy pro předplatné a umístění. Nezahrnují žádné použitelné náklady na software. 
    > - Doporučené velikosti určuje Vydavatel vybrané image a vycházejí z požadavků na hardware a software.
    > - Použití disků Standard (HDD) místo disků Premium (SSD) může ovlivnit výkon operačního systému.

1. Ve **3. Nakonfigurujte volitelné** funkce, zadejte:

    a. V případě **vysoké dostupnosti** vyberte skupinu dostupnosti. Pokud chcete zajistit redundanci pro vaši aplikaci, seskupte dva nebo více virtuálních počítačů ve skupině dostupnosti. Tato konfigurace zajišťuje, že během plánované nebo neplánované události údržby bude k dispozici alespoň jeden virtuální počítač, který bude splňovat 99,95% Smlouvy o úrovni služeb Azure (SLA). Po vytvoření se skupina dostupnosti virtuálního počítače nedá změnit.

    b. V případě **úložiště**vyberte **disky úrovně Premium (SSD)** nebo **standardní disky (HDD)**. Disky úrovně Premium (SSD) jsou založené na jednotkách Solid-State a nabízejí konzistentní výkon s nízkou latencí. Poskytují nejlepší rovnováhu mezi cenou a výkonem a jsou ideální pro aplikace náročné na vstupně-výstupní operace a produkční úlohy. Standardní disky jsou zajištěny pomocí magnetických jednotek a jsou vhodnější pro aplikace, ve kterých se k datům přistupovalo zřídka. Redundantní disky v zóně jsou založené na zóně redundantního úložiště (ZRS), které replikují vaše data napříč několika zónami a jsou k dispozici i v případě, že je jedna zóna mimo provoz. 

    c. Vyberte **použít spravované disky**. Když tuto funkci povolíte, Azure automaticky spravuje dostupnost disků. Výhodou je redundance dat a odolnost proti chybám, aniž byste museli vytvářet a spravovat účty úložiště sami. Spravované disky nemusí být k dispozici ve všech oblastech. Další informace najdete v tématu [Úvod do služby Azure Managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview).

    d. Chcete-li konfigurovat síť, vyberte možnost **virtuální síť**. Virtuální sítě jsou od sebe logicky izolované v Azure. V datovém centru můžete nakonfigurovat rozsahy IP adres, podsítě, směrovací tabulky, brány a nastavení zabezpečení, podobně jako tradiční síť. Virtuální počítače ve stejné virtuální síti můžou ve výchozím nastavení přistupovat ke druhému. 

    e. Pokud chcete nakonfigurovat podsíť, vyberte **podsíť**. Podsíť je rozsah IP adres ve vaší virtuální síti. Podsíť můžete použít k izolaci virtuálních počítačů od sebe nebo od Internetu. 

    f. Pokud chcete nakonfigurovat přístup ke svému VIRTUÁLNÍmu počítači nebo ke službám běžícím na vašem VIRTUÁLNÍm počítači, vyberte **Veřejná IP adresa**. Pomocí veřejné IP adresy můžete komunikovat s virtuálním počítačem mimo virtuální síť. 

    g. Vyberte **Skupina zabezpečení sítě**, **základní**nebo **Rozšířená**. Nastavte pravidla, která povolují nebo zakazují síťový provoz na virtuálním počítači. 

    h. Pokud chcete pro virtuální počítač nastavit přístup pro běžné nebo vlastní protokoly, vyberte **veřejné příchozí porty**. Služba Určuje cílový protokol a rozsah portů pro toto pravidlo. Můžete zvolit předdefinovanou službu, například protokol RDP (Remote Desktop Protocol) (RDP) nebo SSH, nebo zadat vlastní rozsah portů. 
        Pro webový server použijte protokol HTTP (80), HTTPS (443) a SSH (22) Open. Pokud plánujete spravovat počítač pomocí připojení RDP, otevřete port 3389.

    i. Pokud chcete přidat rozšíření k VIRTUÁLNÍmu počítači, vyberte **rozšíření**. Rozšíření k virtuálnímu počítači přidávají nové funkce, jako je Správa konfigurace nebo antivirová ochrana. 

    j. Zakažte nebo povolte **monitorování**. Aby bylo možné diagnostikovat problémy se spouštěním, můžete pomocí monitorování zachytit výstup sériové konzoly a snímky obrazovky virtuálního počítače spuštěného na hostiteli. 

    k. Pokud chcete zadat účet úložiště, který obsahuje vaše metriky, vyberte **účet úložiště diagnostiky**. Metriky se zapisují do účtu úložiště, abyste je mohli analyzovat pomocí vlastních nástrojů. 

    l. Vyberte **OK**.

1. Přezkoumání **4. Shrnutí**:
    - Portál ověří vaše nastavení.
    - Pokud chcete nastavení znovu použít u pracovního postupu Azure Resource Manager, můžete si stáhnout šablonu Azure Resource Manager pro svůj virtuální počítač.
    - Po úspěšném ověření vyberte **OK**. Nasazení virtuálního počítače trvá několik minut.

### <a name="specify-the-open-ports-and-dns-name"></a>Zadejte otevřené porty a název DNS.

Pokud chcete, aby byla webová aplikace přístupná uživatelům ve vaší síti, otevřete porty používané pro připojení k počítači a přidejte popisný název DNS, například *MyWebApp. Local. cloudapp. azurestack. external*, který mohou uživatelé používat ve svých webových prohlížečích.

#### <a name="open-inbound-ports"></a>Otevřené porty pro příchozí spojení

Můžete upravit cílový protokol a rozsah portů pro předdefinovanou službu, například RDP nebo SSH, nebo zadat vlastní rozsah portů. Například můžete chtít pracovat s rozsahem portů vašeho webového rozhraní. Například komunikuje na portu 3000.

1. Otevřete portál centra Azure Stack pro vašeho tenanta.

1. Vyhledejte svůj virtuální počítač. Je možné, že jste virtuální počítač připnuli k řídicímu panelu, nebo ho můžete vyhledat v poli **Hledat prostředky** .

1. V podokně virtuálního počítače vyberte **sítě** .

1. Vyberte **Přidat pravidlo portu pro příchozí spojení** a otevřete port.

1. U položky **zdroj**ponechte výchozí **výběr.**

1. Pro **rozsah zdrojového portu**ponechte zástupný znak (*).

1. V poli **Rozsah cílových portů**zadejte port, který chcete otevřít, například **3000**.

1. Pro **protokol**ponechte výchozí výběr, **Any**.

1. V části **Akce** vyberte **Povolit**.

1. V poli **Priorita**ponechte výchozí výběr.

1. Zadejte **název** a **Popis** , který vám pomůžete mít na paměti, proč je port otevřený.

1. Vyberte **Přidat**.

#### <a name="add-a-dns-name-for-your-server"></a>Přidat název DNS pro váš server

Kromě toho můžete vytvořit název DNS pro váš server, aby se uživatelé mohli k webu připojit pomocí adresy URL.

1. Otevřete portál centra Azure Stack pro vašeho tenanta.

1. Vyhledejte svůj virtuální počítač. Je možné, že jste virtuální počítač připnuli k řídicímu panelu, nebo ho můžete vyhledat v poli **Hledat prostředky** .

1. Vyberte **Přehled**.

1. V části **virtuální počítač**vyberte **Konfigurovat**.

1. Jako **přiřazení**vyberte **Dynamická**.

1. Zadejte popisek názvu DNS, například **MyWebApp**, aby se vaše úplná adresa URL stala *MyWebApp. Local. cloudapp. azurestack. external* (pro aplikaci ASDK).

### <a name="connect-via-ssh-to-update-your-vm"></a>Připojte se přes SSH a aktualizujte virtuální počítač.

1. Ve stejné síti jako instance centra Azure Stack otevřete klienta SSH. Další informace najdete v tématu [použití veřejného klíče SSH](azure-stack-dev-start-howto-ssh-public-key.md).

1. Zadejte následující příkazy:

    ```bash  
        sudo apt-get update
        sudo apt-get -y upgrade
    ```

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak [nastavit vývojové prostředí v Azure Stack hub](azure-stack-dev-start.md).
