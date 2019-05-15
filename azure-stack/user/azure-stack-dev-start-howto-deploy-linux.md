---
title: Nasazení virtuálního počítače s Linuxem do Azure stacku | Dokumentace Microsoftu
description: Nasazení aplikace do služby Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/24/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 61d9f21f35edf1a0e8ebf61c81580c4d53218970
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2019
ms.locfileid: "65617668"
---
# <a name="deploy-a-linux-vm-to-host-a-web-app-in-azure-stack"></a>Nasazení virtuálního počítače s Linuxem k hostování webové aplikace ve službě Azure Stack

Můžete vytvořit a nasadit základní virtuální počítač s Linuxem (VM) s použitím Ubuntu image v Azure Marketplace k hostování webové aplikace, kterou jste vytvořili pomocí webové rozhraní. 

Tento virtuální počítač může hostovat webové aplikace pomocí:

- **Python**: Běžné architektury webových Python zahrnují Flask, Bottle a Django.
- **Go**: Běžné přejít architektury patří Revel, Martini, Gocraft/web a váhou. 
- **Ruby**: Nastavení aplikace Ruby on Rails jako rozhraní k vytváření aplikací pro Ruby web. 
- **Java**: Použití Javy k vývoji webových aplikací, které pošlete příspěvek na serveru Apache Tomcat. Můžete nainstalovat Tomcat v Linuxu a potom nasazování souborů Java WAR přímo na server. 

Chcete-li začít pracovat se všemi webové aplikace, rozhraní a back endové technologie, která používá operační systém Linux postupujte podle pokynů v tomto článku. Azure Stack můžete pak použít ke správě vaší infrastruktury a použít nástroje pro správu v rámci technologie pro zpracování úloh údržby pro vaši aplikaci.

## <a name="deploy-a-linux-vm-for-a-web-app"></a>Nasazení virtuálního počítače s Linuxem pro webovou aplikaci

V tomto procesu vytvoření tajného klíče, použijte základní image virtuálního počítače s Linuxem, určit konkrétní atributy virtuálního počítače a pak vytvořte virtuální počítač. Po vytvoření virtuálního počítače můžete otevřít porty, které jsou nezbytné pro práci s virtuálním Počítačem a pro virtuální počítač pro hostování vaší aplikace. Dále vytvořte název DNS. Nakonec se připojit k virtuálnímu počítači a aktualizujte počítač pomocí nástroje apt-get. Po dokončení procesu, budete mít virtuální počítač v instanci služby Azure Stack, který je připravený k hostování webové aplikace.

Než začnete, ujistěte se, že máte všechno, co potřebujete na místě.

## <a name="prerequisites"></a>Požadavky

- Předplatné služby Azure Stack s přístupem k image Ubuntu Server 16.04 LTS. Můžete použít novější verzi image, ale tyto pokyny jsou vytvářeny s použitím 16.04 LTS v úvahu. Pokud nemáte tuto bitovou kopii, obraťte se na váš operátor cloudu k načtení image na marketplace služby Azure Stack.

## <a name="deploy-the-vm-by-using-the-portal"></a>Nasazení virtuálního počítače pomocí portálu

Pokud chcete nasadit virtuální počítač, postupujte podle pokynů v následujících částech několik.

### <a name="create-your-vm"></a>Vytvoření virtuálního počítače

1. Vytvoření veřejné klíče Secure Shell (SSH) pro váš server. Další informace najdete v tématu [jak použít veřejný klíč SSH](azure-stack-dev-start-howto-ssh-public-key.md).
1. Na portálu Azure Stack, vyberte **vytvořit prostředek** > **Compute** > **Ubuntu Server 16.04 LTS**.

    ![Nasazení webové aplikace na Virtuálním počítači Azure Stack](media/azure-stack-dev-start-howto-deploy-linux/001-portal-compute.png)

4. V **vytvoření virtuálního počítače** podokně pro **1. Konfigurace základního nastavení**:

    a. Zadejte **název vašeho virtuálního počítače**.

    b. Vyberte **typ disku virtuálního počítače**– buď **Premium SSD** (na disky Premium [SSD]) nebo **standardní HDD** (pro disky Standard [HDD]).

    c. Zadejte vaše **uživatelské jméno**.

    d. Vyberte **typ ověřování** jako **veřejného klíče SSH**.

    e. Načtěte veřejný klíč SSH, který jste vytvořili. Otevřete v textovém editoru, klíč si zkopírujte a vložte jej do **veřejný klíč SSH** pole. Vložit text z `---- BEGIN SSH2 PUBLIC KEY ----` k `---- END SSH2 PUBLIC KEY ----`. Vložte celý blok textu do pole klíčů:

    ```text  
    ---- BEGIN SSH2 PUBLIC KEY ----
    Comment: "rsa-key-20190207"
    <Your key block>
    ---- END SSH2 PUBLIC KEY ----
    ```

    f. Vyberte předplatné pro vaši instanci služby Azure Stack.

    g. Vytvořit novou skupinu prostředků nebo použijte již existující, v závislosti na tom, jak chcete uspořádat prostředky pro vaši aplikaci.

    h. Vyberte svou zemi. Azure Stack Development Kit (ASDK) je obvykle v *místní* oblasti. Umístění závisí na vaší instance služby Azure Stack.
1. Pro **2. Velikost**, typ:
    - Vyberte velikost dat a paměť RAM pro virtuální počítač, který je k dispozici ve vaší instanci služby Azure Stack.
    - Buď můžete procházet seznam nebo filtr pro velikost virtuálního počítače podle **typ výpočtu**, **procesory**, a **úložný prostor**.
    
    > [!NOTE]
    > - Uvedené ceny jsou odhadované částky v místní měně. Zahrnují jenom náklady na infrastrukturu Azure a zohledňují případné slevy pro dané předplatné a umístění. Že nezahrnují žádné náklady na software. 
    > - Doporučené velikosti stanovuje vydavatel vybrané bitové kopie a jsou založené na požadavcích na hardware a software.
    > - Použití standardní disky (HDD) namísto disky úrovně Premium (SSD) může mít vliv na výkon operačního systému.

1. V **3. Nakonfigurujte volitelné** funkce, typ:

    a. Pro **vysokou dostupnost,** vyberte skupinu dostupnosti. Pokud chcete zajistit redundanci pro vaši aplikaci, seskupit dvou nebo více virtuálních počítačů ve skupině dostupnosti. Tato konfigurace zajistí, že během plánované i neplánované údržby, alespoň jeden virtuální počítač bude mít k dispozici a musí splňovat 99,95 % Azure smlouvu o úrovni služeb (SLA). Po vytvoření nelze změnit skupinu dostupnosti virtuálního počítače.

    b. Pro **úložiště**vyberte **disky úrovně Premium (SSD)** nebo **standardní disky (HDD)**. Disky úrovně Premium (SSD) se zálohují na jednotky SSD a nabízejí konzistentní, nízkou latenci výkon. Poskytují nejlepší poměr mezi cenou a výkonem a jsou ideální pro aplikace I vstupně-výstupními operacemi a úloh v produkčním prostředí. Standardní disky jsou zajišťované magnetickými jednotkami a jsou vhodnější pro aplikace, kde se data přistupovat moc často. Zónově redundantní disky jsou zajišťované zónově redundantní úložiště (ZRS), která replikuje vaše data napříč několika zónami, a jsou k dispozici i v případě, že jedna zóna není mimo provoz. 

    c. Vyberte **použít spravované disky**. Když povolíte tuto funkci, Azure automaticky spravuje dostupnost disků. Můžete využívat redundanci dat a odolnost proti chybám, aniž byste museli vytvářet a spravovat vlastní účty úložiště. Spravované disky nemusí být k dispozici ve všech oblastech. Další informace najdete v tématu [Úvod do služby Azure managed disks](https://docs.microsoft.com/azure/virtual-machines/windows/managed-disks-overview).

    d. Konfigurace sítě, vyberte **virtuální sítě**. Virtuální sítě jsou logicky oddělená od sebe navzájem v Azure. Můžete nakonfigurovat jejich IP rozsahy adres, podsítě, směrovací tabulky, brány a nastavení zabezpečení, podobně jako u tradiční sítě ve vašem datovém centru. Virtuální počítače ve stejné virtuální síti mezi sebou ve výchozím nastavení přístup. 

    e. Chcete-li konfigurovat vaše podsíť, vyberte **podsítě**. Podsíť je rozsah IP adres ve virtuální síti. Podsítě můžete použít k izolaci virtuálních počítačů od sebe nebo od Internetu. 

    f. Pokud chcete nakonfigurovat přístup k vašemu virtuálnímu počítači nebo na služby spuštěné na virtuálním počítači, vyberte **veřejnou IP adresu**. Použijte veřejnou IP adresu ke komunikaci s virtuálním počítačem mimo virtuální síť. 

    g. Vyberte **skupinu zabezpečení sítě**, **základní**, nebo **Advanced**. Sada pravidel, která povolují nebo odpírají síťový provoz do virtuálního počítače. 

    h. Chcete-li nastavení přístupu pro běžné nebo vlastní protokoly k vašemu virtuálnímu počítači, vyberte **veřejné příchozí porty**. Tato služba určuje cílový protokol a port rozsah pro toto pravidlo. Můžete zvolit předdefinovanou službu, jako je protokol RDP (Remote Desktop) nebo SSH, nebo zadat vlastní rozsah portů. 
        Pro webový server pomocí protokolu HTTP (80), protokol HTTPS (443) a SSH (22) otevřete. Pokud chcete spravovat počítače pomocí připojení ke vzdálené ploše, otevřete port 3389.

    i. Chcete-li přidat rozšíření virtuálního počítače, vyberte **rozšíření**. Rozšíření přidat nové funkce, jako je například Správa konfigurace nebo antivirová ochrana ke svému virtuálnímu počítači. 

    j. Zakázání nebo povolení **monitorování**. Pro usnadnění diagnostiky potíží se spuštěním, můžete využít monitorování ke zachytávání výstupu sériové konzoly a snímků obrazovky virtuálního počítače, na kterém běží na hostiteli. 

    k. Chcete-li určit, která obsahuje metriky účtu úložiště, vyberte **účet úložiště diagnostiky**. Metriky se zapisují na účet úložiště, takže je můžete analyzovat svými vlastními nástroji. 

    l. Vyberte **OK**.

1. Kontrola **4. Souhrn**:
    - Na portálu ověří nastavení.
    - Pro opětovné použití nastavení pracovního postupu Azure Resource Manageru, můžete stáhnout šablony Azure Resource Manageru pro virtuální počítač.
    - Po uplynutí ověření, vyberte **OK**. Nasazení virtuálního počítače trvá několik minut.

### <a name="specify-the-open-ports-and-dns-name"></a>Zadejte název DNS a otevření portů

Webové aplikace zpřístupnit uživatelům ve vaší síti, otevřít porty, které se používají pro připojení k počítači a přidat popisný název DNS, jako například *mywebapp.local.cloudapp.azurestack.external*, že uživatelé můžou ve svých webových prohlížečů používat .

#### <a name="open-inbound-ports"></a>Otevřené příchozí porty

Můžete upravit rozsah cílový protokol a port pro předdefinovanou službu, jako je RDP nebo SSH, nebo zadat vlastní rozsah portů. Například můžete chtít pracovat s rozsah portů webové rozhraní. Přejít, například komunikuje na port 3000.

1. Otevřete na portálu Azure Stack pro vašeho tenanta.

1. Vyhledávání pro virtuální počítač. Virtuální počítač může mít připnout na řídicí panel nebo vám ho můžete vyhledat v **vyhledat prostředky** pole.

1. Vyberte **sítě** v podokně pro virtuální počítač.

1. Vyberte **přidat příchozí port** pravidlo pro otevření portu.

1. Pro **zdroj**, ponechte výchozí výběr, **jakékoli**.

1. Pro **zdrojový rozsah portů**, ponechte zástupný znak (*).

1. Pro **rozsah cílových portů**, zadejte port, který chcete otevřít, jako například **3000**.

1. Pro **protokol**, ponechte výchozí výběr, **jakékoli**.

1. V části **Akce** vyberte **Povolit**.

1. Pro **Priority**, ponechte výchozí výběr.

1. Zadejte **název** a **popis** který usnadní zapamatování, proč je otevřený port.

1. Vyberte **Přidat**.

#### <a name="add-a-dns-name-for-your-server"></a>Přidání názvu DNS pro server

Kromě toho můžete vytvořit název DNS pro váš server tak, aby uživatelé mohli připojit k webu pomocí adresy URL.

1. Otevřete na portálu Azure Stack pro vašeho tenanta.

1. Vyhledávání pro virtuální počítač. Virtuální počítač může mít připnout na řídicí panel nebo vám ho můžete vyhledat v **vyhledat prostředky** pole.

1. Vyberte **Přehled**.

1. V části **VM**vyberte **konfigurovat**.

1. Pro **přiřazení**vyberte **dynamické**.

1. Zadejte popisek názvu DNS, jako například **mywebapp**, aby se stal úplnou adresu URL *mywebapp.local.cloudapp.azurestack.external* (pro aplikace ASDK).

### <a name="connect-via-ssh-to-update-your-vm"></a>Připojte se přes SSH k aktualizaci vašeho virtuálního počítače

1. Ve stejné síti jako vaše instance služby Azure Stack otevřete klienta SSH. Další informace najdete v tématu [použít veřejný klíč SSH](azure-stack-dev-start-howto-ssh-public-key.md).

1. Zadejte následující příkazy:

    ```bash  
        sudo apt-get update
        sudo apt-get -y upgrade
    ```

## <a name="next-steps"></a>Další postup

Zjistěte, jak [nastavení vývojového prostředí ve službě Azure Stack](azure-stack-dev-start.md).
