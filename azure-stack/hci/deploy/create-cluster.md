---
title: Vytvoření clusteru Azure Stack HCI pomocí centra pro správu Windows
description: Zjistěte, jak vytvořit serverový cluster pro Azure Stack HCI pomocí centra pro správu Windows.
author: v-dasis
ms.topic: how-to
ms.date: 12/10/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 35c7fc98b88029d6ab130859b2c3d1c550233936
ms.sourcegitcommit: 97ecba06aeabf2f30de240ac283b9bb2d49d62f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97011229"
---
# <a name="create-an-azure-stack-hci-cluster-using-windows-admin-center"></a>Vytvoření clusteru Azure Stack HCI pomocí centra pro správu Windows

> Platí pro Azure Stack HCI, verze v20H2

V tomto článku se dozvíte, jak pomocí centra pro správu Windows vytvořit cluster Azure Stack HCI, který používá Prostory úložiště s přímým přístupem. Průvodce vytvořením clusteru v centru pro správu Windows provede většinu těžkého zvedání za vás. Pokud to chcete udělat sami s PowerShellem, přečtěte si téma [vytvoření Azure Stack clusteru HCI pomocí PowerShellu](create-cluster-powershell.md). Článek v prostředí PowerShell je také dobrým zdrojem informací o tom, co se v digestoři průvodce vychází a pro účely řešení potíží.

Mezi vytvářením dvou typů clusteru můžete vybrat:

- Standardní cluster s aspoň dvěma serverovými uzly umístěný v jedné lokalitě.
- Roztažené clustery s nejméně čtyřmi serverovými uzly, které jsou rozloženy na dvě lokality a mají alespoň dva uzly na jeden web.

Další informace o roztažené clustery najdete v tématu [Přehled roztaženého clusteru](../concepts/stretched-clusters.md).

Pokud vás zajímá testování Azure Stack HCI, ale mají omezený nebo žádný náhradní hardware, Projděte si [příručku pro vyhodnocení Azure Stack HCI](https://github.com/Azure/AzureStackHCI-EvalGuide/blob/main/README.md), kde Vás provedeme Azure Stack HCL pomocí vnořené virtualizace, ať už v Azure, nebo v jednom fyzickém systému v místním prostředí.

## <a name="before-you-run-the-wizard"></a>Před spuštěním Průvodce

Před spuštěním Průvodce vytvořením clusteru se ujistěte, že jste provedli následující akce:

- Přečtěte si hardware a související požadavky v části [požadavky na systém](../concepts/system-requirements.md).
- Přečtěte si [požadavky na fyzickou síť](../concepts/physical-network-requirements.md) a [požadavky na hostitele](../concepts/host-network-requirements.md) pro Azure Stack HCI.
- Nainstalujte do každého serveru v clusteru Azure Stack operační systém HCI. Viz [nasazení operačního systému Azure Stack HCI](operating-system.md).
- Mít účet, který je členem místní skupiny Administrators na každém serveru.
- Nainstalujte centrum pro správu Windows na počítač nebo server pro správu. Viz téma [Instalace centra pro správu systému Windows](/windows-server/manage/windows-admin-center/deploy/install).
- Pro roztažené clustery nastavte ve službě Active Directory vaše dvě weby předem. Ale nedělejte si starosti, průvodce je může nastavit i pro vás.

Pokud používáte centrum pro správu systému Windows na serveru (místo místního počítače), použijte účet, který je členem skupiny Správci brány, nebo místní skupinu Administrators na serveru centra pro správu systému Windows.

Počítač pro správu centra pro správu systému Windows musí být také připojen ke stejné doméně služby Active Directory, ve které vytvoříte cluster, nebo plně důvěryhodnou doménu. Servery, které budete mít v clusteru, nemusíte ještě patřit do domény. můžou být během vytváření clusteru přidané do domény.

Tady jsou hlavní kroky v Průvodci vytvořením clusteru:

1. **Začínáme** – zajistí, že každý server splňuje požadavky na funkce a potřebné pro připojení ke clusteru.
1. **Sítě** – přiřadí a nakonfiguruje síťové adaptéry a vytvoří pro každý server virtuální přepínače.
1. **Clustering** – ověřuje, jestli je cluster nastavený správně. Pro roztažené clustery se také nastaví tyto dvě lokality.
1. **Storage** – konfiguruje prostory úložiště s přímým přístupem.

Po dokončení průvodce nastavte určující cluster, zaregistrujte se v Azure a vytvořte svazky (které také nastaví replikaci mezi lokalitami při vytváření roztaženého clusteru).

OK, pojďme začít:

1. V centru pro správu Windows v části **všechna připojení** klikněte na **Přidat**.
1. Na panelu **Přidat nebo vytvořit prostředky** v části **serverové clustery** vyberte **vytvořit novou**.
1. V části **1. Zvolte typ clusteru**, vyberte **Azure Stack HCI**.

    :::image type="content" source="media/cluster/create-cluster-type.png" alt-text="Průvodce vytvořením clusteru – možnost HCI" lightbox="media/cluster/create-cluster-type.png":::

1. V části **Vybrat umístění serveru** vyberte jednu z následujících možností:

    - **Všechny servery v jedné lokalitě**
    - **Servery ve dvou lokalitách** (pro roztažené clustery)

1. Jakmile budete hotovi, klikněte na **Vytvořit**. Nyní se zobrazí Průvodce vytvořením clusteru, jak je znázorněno níže.

    :::image type="content" source="media/cluster/create-cluster-wizard.png" alt-text="Průvodce vytvořením clusteru-Začínáme" lightbox="media/cluster/create-cluster-wizard.png":::

## <a name="step-1-get-started"></a>Krok 1: Začněte

Krok 1 Průvodce vás provede tím, že budete mít jistotu, že jsou splněné všechny požadavky, přidáte uzly serveru, nainstalujete potřebné funkce a pak v případě potřeby restartujete jednotlivé servery.

1. Přezkoumání **1,1 Zkontrolujte požadavky** uvedené v průvodci, abyste zajistili, že každý uzel serveru je připravený pro cluster. Jakmile budete hotovi, klikněte na tlačítko **Další**.
1. Na **1,2 přidat servery** zadejte uživatelské jméno a heslo účtu a pak klikněte na **Další**. Tento účet musí být členem místní skupiny Administrators na každém serveru.
1. Zadejte název prvního serveru, který chcete přidat, a pak klikněte na **Přidat**.
1. Opakujte krok 3 pro každý server, který bude součástí clusteru. Jakmile budete hotovi, klikněte na tlačítko **Další**.
1. V případě potřeby v **1,3 se připojte k doméně** a zadejte doménu, ke které se mají připojit servery, a účet, který se má použít. Případně můžete servery přejmenovat, pokud chcete. Potom klikněte na **Další**.
1. V **1,4 nainstalujte funkce**, podle potřeby zkontrolujte a přidejte funkce. Jakmile budete hotovi, klikněte na tlačítko **Další**.

    Průvodce nainstaluje následující požadované funkce:

    - BitLocker
    - Přemostění Datacenter (pro síťové adaptéry RoCEv2)
    - Clustering s podporou převzetí služeb při selhání
    - Souborový server
    - FS – modul odstranění duplicitních dat
    - Hyper-V
    - RSAT – modul AD-PowerShell
    - Replika úložiště (nainstalovaná pro roztažené clustery)

1. Na **1,5 nainstalujte aktualizace** kliknutím na **instalovat aktualizace** podle potřeby nainstalujte jakékoli aktualizace operačního systému. Jakmile budete hotovi, klikněte na **Další**.
1. Pokud chcete získat dostupné aktualizace hardwaru od dodavatele, v **1,6 instalace aktualizací hardwaru** klikněte na **získat aktualizace** podle potřeby.
1. Pokud chcete nainstalovat aktualizace na svůj hardware, postupujte podle kroků určených pro konkrétní dodavatele. Tyto kroky zahrnují provádění kontrol pro symetrii a dodržování předpisů na vašem hardwaru, aby se zajistila úspěšná aktualizace. Možná budete muset znovu spustit některé kroky.
1. Na **1,7 restartujte servery** a v případě potřeby klikněte na **restartovat servery** . Ověřte, zda byl každý server úspěšně spuštěn.

## <a name="step-2-networking"></a>Krok 2: používání sítě

Krok 2 Průvodce vás provede konfigurací virtuálních přepínačů, síťových adaptérů a dalších síťových prvků pro váš cluster. Podporují se síťové adaptéry RDMA (oba iWARP a RoCE).

Další informace o RDMA a sítích hostitele Hyper-V pro Azure Stack HCI najdete v tématu [požadavky na síť hostitele](../concepts/host-network-requirements.md).

> [!NOTE]
> Pokud se zobrazí chyby uvedené během jakýchkoli síťových nebo virtuálních přepínačů, vyberte **použít a otestovat** znovu.

1. Až skončíte, vyberte **Další: Sítě**.
1. Na **2,1 Zkontrolujte síťové adaptéry**, počkejte, až se vedle každého adaptéru zobrazí zelená políčka, a pak vyberte **Další**.

1. V **2,2 Vyberte adaptéry pro správu**, vyberte jeden nebo dva adaptéry pro správu, které chcete použít pro každý server. Je nutné vybrat alespoň jeden z adaptérů pro účely správy, protože průvodce vyžaduje alespoň jednu vyhrazenou fyzickou síťovou kartu pro správu clusteru.  Jakmile je adaptér určený ke správě, je vyloučený ze zbytku pracovního postupu Průvodce.

    :::image type="content" source="media/cluster/create-cluster-management-adapters.png" alt-text="Průvodce vytvořením clusteru – výběr adaptérů pro správu" lightbox="media/cluster/create-cluster-management-adapters.png":::

    Adaptéry pro správu mají dvě možnosti konfigurace:

    - **Jeden fyzický síťový adaptér pro správu**. Pro tuto možnost se podporuje přiřazení DHCP i statických IP adres.

    - **Pro správu jsou seskupeny dva fyzické síťové adaptéry**. Při seskupování dvojice adaptérů je podporována pouze přiřazování statických IP adres. Pokud vybrané adaptéry používají adresování DHCP (u jednoho nebo obou), budou IP adresy DHCP převedeny na statické IP adresy před vytvořením virtuálního přepínače.

    Pomocí seskupených adaptérů máte jedno připojení k více přepínačům, ale použijete jenom jednu IP adresu. Vyrovnávání zatížení bude k dispozici a odolnost proti chybám je okamžitá místo čekání na aktualizaci záznamů DNS.

    U každého serveru teď proveďte tento postup:

    - Zaškrtněte políčko **Popis** . Všimněte si, že jsou vybrané všechny adaptéry a průvodce vám může nabídnout doporučení.
    - Zrušte zaškrtnutí políček u adaptérů, které nechcete používat pro správu clusteru.

    > [!NOTE]
    > Můžete použít 1 GB adaptérů jako adaptéry pro správu, ale pro přenos provozu a zatížení virtuálního počítače doporučujeme použít 10 GB nebo rychlejší adaptéry.

1. Když byly provedeny změny, klikněte na tlačítko **použít a otestovat**.
1. V části **definovat sítě** zajistěte, aby každý síťový adaptér každého serveru měl jedinečnou statickou IP adresu, masku podsítě a ID sítě VLAN. Najeďte myší na jednotlivé prvky tabulky a podle potřeby zadejte nebo změňte hodnoty. Po dokončení klikněte na **použít a testovat**.

    > [!NOTE]
    > Aby bylo možné podporovat konfiguraci ID sítě VLAN pro cluster, všechny síťové adaptéry na všech serverech musí podporovat vlastnost VLANID.

1. Počkejte, dokud se ve sloupci **stav** zobrazí **předáno** pro každý server, a poté klikněte na tlačítko **Další**. Tento krok ověří síťové připojení mezi všemi adaptéry se stejnou podsítí a ID sítě VLAN. Zadané IP adresy se přenesou z fyzického adaptéru na virtuální adaptéry po vytvoření virtuálních přepínačů v dalším kroku. Dokončení může trvat několik minut v závislosti na počtu nakonfigurovaných adaptérů.

1. V části **2,3 virtuální přepínač** vyberte jednu z následujících možností podle potřeby. V závislosti na tom, kolik síťových adaptérů existují, nemusí být k dispozici všechny možnosti:

    - **Vytvoření virtuálního přepínače přeskočit** – vyberte, pokud chcete nastavit virtuální přepínače později.
    - **Vytvořte jeden virtuální přepínač pro výpočetní výkon a úložiště společně** – vyberte, jestli chcete použít stejný virtuální přepínač pro virtuální počítače a prostory úložiště s přímým přístupem. Toto je možnost Sblíženo.
    - **Vytvořit jeden virtuální přepínač jenom pro výpočetní** prostředí – vyberte, pokud chcete použít virtuální přepínač jenom pro virtuální počítače.
    - **Vytvoření dvou virtuálních přepínačů** – tuto možnost vyberte, pokud chcete, aby každý z virtuálních počítačů a prostory úložiště s přímým přístupem měl vyhrazený virtuální přepínač.

        :::image type="content" source="media/cluster/create-cluster-virtual-switches.png" alt-text="Průvodce vytvořením clusteru – virtuální přepínače" lightbox="media/cluster/create-cluster-virtual-switches.png":::

    > [!NOTE]
    > Pokud chystáte nasazení síťového adaptéru pro SDN (v **kroku 5: SDN** průvodce), budete potřebovat virtuální přepínač. Takže pokud se sem nerozhodnete vytvořit virtuální přepínač a nevytvářet ho mimo průvodce, průvodce neimplementuje síťový adaptér.

    Následující tabulka uvádí, které konfigurace virtuálních přepínačů jsou podporovány a povoleny pro různé konfigurace síťových adaptérů:

    | Možnost | adaptéry 1-2 | 3 adaptéry | seskupené adaptéry |
    | :------------- | :--------- |:--------| :---------|
    | jednoduchý přepínač (COMPUTE + úložiště) | enabled | enabled  | nepodporováno |
    | jednoduchý přepínač (jenom výpočetní výkon) | nepodporováno| enabled | enabled |
    | dva přepínače | nepodporováno | enabled | enabled |

1. Podle potřeby změňte název přepínače a další nastavení konfigurace a klikněte na **použít a otestovat**. Po vytvoření virtuálních přepínačů by měl **být pro každý** Server zobrazený sloupec **stav** .

1. Krok **2,4 RDMA** je volitelný. Pokud používáte RDMA, zaškrtněte políčko **Konfigurovat RDMA (doporučeno)** a klikněte na **Další**.

    :::image type="content" source="media/cluster/create-cluster-rdma.png" alt-text="Průvodce vytvořením clusteru – konfigurace RDMA" lightbox="media/cluster/create-cluster-rdma.png":::

    Informace o přiřazení rezervací šířky pásma najdete v části [přidělení šířky pásma přenosu](../concepts/host-network-requirements.md#traffic-bandwidth-allocation) v tématu [požadavky na síť hostitele](../concepts/host-network-requirements.md).

1. Vyberte **Upřesnit** a pak zaškrtněte políčko **přemostění datového centra (DCB)** .

1. V části **prezenční signál clusteru** přiřaďte úroveň priority a procento rezervace šířky pásma.

1. V části **úložiště** přiřaďte úroveň priority a procento rezervace šířky pásma.

1. Po dokončení vyberte **použít změny** a klikněte na **Další**.

1. V **2,5 definujte sítě**, zkontrolujte a upravte pole název, IP adresa, maska podsítě, ID sítě VLAN a výchozí brána pro každý z uvedených adaptérů.

    :::image type="content" source="media/cluster/create-cluster-define-networks.png" alt-text="Průvodce vytvořením clusteru – definice sítí" lightbox="media/cluster/create-cluster-define-networks.png":::

1. Po dokončení klikněte na **použít a testovat**. Je možné, že budete muset **Opakovat test připojení** , pokud stav není v pořádku pro adaptér.

## <a name="step-3-clustering"></a>Krok 3: clusteringu

3. krok tohoto průvodce zajistí, že všechno, co se v současnosti správně nastavilo, automaticky nastaví dvě lokality v případě roztaženého nasazení clusteru a pak cluster skutečně vytvoří. Vaše weby můžete také nastavit předem ve službě Active Directory.

1. Vyberte **Další: clustering**.
1. V **3,1 ověřte cluster**, vyberte **ověřit**. Ověření může trvat několik minut.

    Pokud se zobrazí automaticky otevírané okno **poskytovatel zabezpečení přihlašovacích údajů (CredSSP)** , vyberte **Ano** , pokud chcete, aby mohl průvodce povolit zprostředkovatele CredSSP pro pokračování. Po vytvoření clusteru a dokončení průvodce zakážete CredSSP, aby se zvýšilo zabezpečení. Pokud máte problémy se zprostředkovatelem CredSSP, přečtěte si další informace v tématu [řešení potíží se zprostředkovatelem](../manage/troubleshoot-credssp.md) zabezpečení.

1. Zkontrolujte všechny stavy ověření, Stáhněte si sestavu, abyste získali podrobné informace o případných chybách, udělejte změny a pak **znovu** klikněte na ověřit podle potřeby. Můžete také **Stáhnout sestavu** . Opakujte akci podle potřeby, dokud nebudou všechny kontroly ověřování prošly. Když je vše v pořádku, klikněte na **Další**.
1. V **3,2 vytvořit cluster** zadejte název clusteru.

    :::image type="content" source="media/cluster/create-cluster.png" alt-text="Průvodce vytvořením clusteru – vytvoření clusteru" lightbox="media/cluster/create-cluster.png":::

1. V části **IP adresa** vyberte buď statické, nebo dynamické IP adresy, které chcete použít.
1. Vyberte **Upřesnit**. Máte několik možností, které jsou tady:

    - **Registrace clusteru pomocí služby DNS a služby Active Directory**
    - **Přidání vhodného úložiště do clusteru (doporučeno)**

1. V části **sítě** vyberte, jestli se mají **používat všechny sítě (doporučeno)** , nebo **Zadejte jednu nebo víc sítí, které nechcete používat**.

1. Po dokončení klikněte na **vytvořit cluster**.

1. U roztaženého clusteru v **3,3 přiřaďte servery k lokalitám** názvy dvou lokalit, které budou použity.

1. Dále přiřaďte každý server k lokalitě. Replikaci budete nastavovat později v různých lokalitách. Po dokončení klikněte na **použít změny**.

## <a name="step-4-storage"></a>Krok 4: úložiště

Krok 4 Průvodce vás provede nastavením Prostory úložiště s přímým přístupem pro váš cluster.

1. Vyberte **Další: úložiště**.
1. Na **4,1 čistých jednotkách** můžete volitelně vybrat **Vymazat jednotky** , pokud to pro vaše nasazení dává smysl.
1. Na **4,2 Zkontrolujte jednotky**, klikněte na **>** ikonu u každého serveru a ověřte, zda jsou disky funkční a připojené. Pokud je vše v pořádku, klikněte na tlačítko **Další**.
1. V **4,3 ověřit úložiště** klikněte na **Další**.
1. Stáhněte si a zkontrolujte sestavu ověření. Pokud je vše v pořádku, klikněte na tlačítko **Další**. V takovém případě **znovu** spusťte příkaz ověřit.
1. V **4,4 prostory úložiště s přímým přístupem povolit** klikněte na **Povolit**.
1. Stáhněte a zkontrolujte sestavu. Pokud je vše v pořádku, klikněte na **Dokončit**. 
1. Vyberte možnost **Přejít k seznamu připojení**.
1. Po několika minutách by se měl cluster zobrazit v seznamu. Výběrem této stránky zobrazíte stránku s přehledem clusteru.

Může trvat nějakou dobu, než se název clusteru replikuje napříč vaší doménou, zejména pokud byly servery pracovní skupiny nově přidané do služby Active Directory. I když se cluster může zobrazit v centru pro správu systému Windows, nemusí být k dispozici, aby se mohl ještě připojit.

Pokud po nějaké době řešení clusteru neproběhne úspěšně, můžete ve většině případů místo názvu clusteru nahradit název serveru.

## <a name="step-5-sdn-optional"></a>Krok 5: SDN (volitelné)

Tento volitelný krok vás provede nastavením součásti síťového adaptéru [softwarově definovaných sítí (SDN)](../concepts/software-defined-networking.md). Po nastavení síťového adaptéru můžete podle potřeby nakonfigurovat jiné součásti SDN, jako je například software Load Balancer (SLB) a brána služby RAS.

> [!NOTE]
> Průvodce nekonfiguruje službu SLB a bránu RAS pro SDN. Ke konfiguraci těchto komponent můžete použít skripty SDN Express. Informace o tom, jak to udělat, najdete v [úložišti GitHub SDNExpress](https://github.com/microsoft/SDN/tree/master/SDNExpress/scripts).

> [!NOTE]
> Pro roztažené clustery není přípona SDN podporována.

1. Vyberte **Další: SDN**.

    :::image type="content" source="media/cluster/create-cluster-network-controller.png" alt-text="Průvodce vytvořením clusteru – síťový adaptér SDN" lightbox="media/cluster/create-cluster-network-controller.png":::

1. V **5,1 definujte cluster síťového adaptéru** v části **hostitel** zadejte název síťového adaptéru. Jedná se o název DNS, který používají klienti pro správu (například centrum pro správu systému Windows) ke komunikaci se síťovým adaptérem.
1. Zadejte cestu k souboru VHD Azure Stack HCL. Pro rychlejší vyhledání použijte **Procházet** .
1. Zadejte počet virtuálních počítačů, které se mají vyhradit pro síťový adaptér. Pro zajištění vysoké dostupnosti se doporučuje aspoň tři virtuální počítače.
1. V části **síť** zadejte ID sítě VLAN sítě pro správu. Síťový adaptér musí být připojen ke stejné síti pro správu jako hostitelé pro komunikaci a konfiguraci hostitelů.

    > [!NOTE]
    > Virtuální počítače síťového adaptéru používají virtuální přepínač používaný pro správu clusteru, pokud jsou k dispozici, jinak používají virtuální přepínač "COMPUTE" jako zbytek virtuálních počítačů clusteru. Další informace najdete v části [požadavky na síťový adaptér](../concepts/network-controller.md#network-controller-requirements) v tématu [Plánování nasazení síťového adaptéru](../concepts/network-controller.md).

1. V případě **adresování sítě virtuálních počítačů** vyberte možnost **DHCP** nebo **static**.
1. Pokud jste vybrali možnost **DHCP**, zadejte název pro virtuální počítače síťového adaptéru.
1. Pokud jste vybrali možnost **static**, zadejte následující:
    1. IP adresa.
    1. Předpona podsítě.
    1. Výchozí brána.
    1. Jeden nebo více serverů DNS. Kliknutím na **Přidat** přidejte další servery DNS.
1. V části **přihlašovací údaje** zadejte uživatelské jméno a heslo, které se použije k připojení virtuálních počítačů síťového adaptéru k doméně clusteru.
1. Zadejte heslo místního správce pro tyto virtuální počítače.
1. V části **Upřesnit** zadejte cestu k virtuálním počítačům.
1. Zadejte hodnoty pro **začátek** a **konec fondu adres** Mac.
1. Jakmile budete hotovi, klikněte na tlačítko **Další**.
1. Při **nasazení síťového adaptéru** počkejte, dokud průvodce nedokončí jeho úlohu. Zůstat na této stránce, dokud nebudou dokončeny všechny úlohy průběhu. Klikněte na **Dokončit**.

1. Po vytvoření virtuálních počítačů síťového adaptéru nakonfigurujte dynamické aktualizace DNS pro název clusteru síťového adaptéru na serveru DNS. Informace o tom, jak to provést, najdete v tématu [Konfigurace dynamické registrace DNS pro síťový adaptér](/windows-server/networking/sdn/plan/installation-and-preparation-requirements-for-deploying-network-controller#step-3-configure-dynamic-dns-registration-for-network-controller).

1. Pokud se nasazení síťového adaptéru nepovede, proveďte následující kroky, než to zkusíte znovu:

- Zastavte a odstraňte všechny virtuální počítače síťového adaptéru, které průvodce vytvořil.  

- Vyčistěte všechny přípojné body VHD, které průvodce vytvořil.  

- Ujistěte se, že máte alespoň 50 100 GB volného místa na hostitelích Hyper-V.  

## <a name="after-you-complete-the-wizard"></a>Po dokončení Průvodce

Po dokončení průvodce jsou stále k dispozici některé důležité úkoly, které je třeba provést.

Prvním úkolem je zakázat protokol CredSSP (Credential Security Support Provider) na každém serveru z hlediska zabezpečení. Zapamatujte si, že pro Průvodce je nutné povolit CredSSP. Pokud máte problémy se zprostředkovatelem CredSSP, přečtěte si další informace v tématu [řešení potíží se zprostředkovatelem](../manage/troubleshoot-credssp.md) zabezpečení.

1. V centru pro správu Windows v části **všechna připojení** vyberte cluster, který jste právě vytvořili.
1. V části **nástroje** vyberte **servery**.
1. V pravém podokně vyberte první server v clusteru.
1. V části **Přehled** vyberte **Zakázat CredSSP**. Uvidíte, že červená zpráva **CREDSSP povolená** v horní části zmizí.
1. Zopakujte kroky 3 a 4 pro každý server v clusteru.

OK, teď máte další úkoly, které budete muset udělat:

- Nastavte určující cluster. Viz [Nastavení určujícího clusteru](witness.md).
- Vytvořte si svazky. Viz [vytvoření svazků](../manage/create-volumes.md).
- U roztaženého clusteru vytvořte svazky a nastavte replikaci. Viz [Vytvoření roztažené svazky clusteru a nastavení replikace](../manage/create-stretched-volumes.md).

## <a name="next-steps"></a>Další kroky

- Zaregistrujte svůj cluster v Azure. Viz [Správa registrace Azure](../manage/manage-azure-registration.md).
- Proveďte konečné ověření clusteru. Viz [ověření clusteru Azure Stack HCI](validate.md)
- Zřizování virtuálních počítačů. Přečtěte si téma [Správa virtuálních počítačů v Azure Stack HCI pomocí centra pro správu systému Windows](../manage/vm.md).
- Cluster můžete nasadit také pomocí prostředí PowerShell. Přečtěte si téma [vytvoření Azure Stack clusteru HCI pomocí prostředí PowerShell](create-cluster-powershell.md).
- Síťový adaptér můžete nasadit také pomocí prostředí PowerShell. Viz [nasazení síťového adaptéru pomocí PowerShellu](network-controller-powershell.md).
