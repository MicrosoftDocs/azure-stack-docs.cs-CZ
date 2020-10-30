---
title: Vytvoření clusteru Azure Stack HCI pomocí centra pro správu Windows
description: Zjistěte, jak vytvořit serverový cluster pro Azure Stack HCI pomocí centra pro správu Windows.
author: v-dasis
ms.topic: how-to
ms.date: 10/17/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 508bf39e9cdeb55485bc2a517c412cee7f3dcd80
ms.sourcegitcommit: 296c95cad20ed62bdad0d27f1f5246bfc1c81d5e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93064765"
---
# <a name="create-an-azure-stack-hci-cluster-using-windows-admin-center"></a>Vytvoření clusteru Azure Stack HCI pomocí centra pro správu Windows

> Platí pro Azure Stack HCI, verze v20H2

V tomto článku se dozvíte, jak pomocí centra pro správu Windows vytvořit cluster s Azure Stackem HCI s více instancemi, který používá Prostory úložiště s přímým přístupem. Průvodce vytvořením clusteru v centru pro správu Windows provede většinu těžkého zvedání za vás. Pokud to chcete udělat sami s PowerShellem, přečtěte si téma [vytvoření Azure Stack clusteru HCI pomocí PowerShellu](create-cluster-powershell.md). Článek v prostředí PowerShell je také dobrým zdrojem informací o tom, co se v digestoři průvodce vychází a pro účely řešení potíží.

Mezi vytvářením dvou typů clusteru můžete vybrat:

- Standardní cluster s aspoň dvěma serverovými uzly umístěný v jedné lokalitě.
- Roztažené clustery s nejméně čtyřmi serverovými uzly, které jsou rozloženy na dvě lokality a mají alespoň dva uzly na jeden web.

Další informace o roztažené clustery najdete v tématu [Přehled roztaženého clusteru](../concepts/stretched-clusters.md).

Pokud vás zajímá testování Azure Stack HCI, ale mají omezený nebo žádný náhradní hardware, Projděte si [příručku pro vyhodnocení Azure Stack HCI](https://github.com/Azure/AzureStackHCI-EvalGuide/blob/main/README.md), kde Vás provedeme Azure Stack HCL pomocí vnořené virtualizace, ať už v Azure, nebo v jednom fyzickém systému v místním prostředí.

## <a name="before-you-run-the-wizard"></a>Před spuštěním Průvodce

Před spuštěním Průvodce vytvořením clusteru se ujistěte, že jste provedli následující akce:

- Přečtěte si hardware a další požadavky v části [požadavky na systém](../concepts/system-requirements.md).
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

1. V centru pro správu Windows v části **všechna připojení** klikněte na **Přidat** .
1. Na panelu **Přidat prostředky** v části **cluster Windows serveru** vyberte **vytvořit novou** .
1. V části **zvolit typ clusteru** vyberte **Azure Stack HCL** .

    :::image type="content" source="media/cluster/create-cluster-type.png" alt-text="Průvodce vytvořením clusteru – možnost HCI" lightbox="media/cluster/create-cluster-type.png":::

1. V části **Vybrat umístění serveru** vyberte jednu z následujících možností:

    - **Všechny servery v jedné lokalitě**
    - **Servery ve dvou lokalitách** (pro roztažené clustery)

1. Jakmile budete hotovi, klikněte na **Vytvořit** . Nyní se zobrazí Průvodce vytvořením clusteru, jak je znázorněno níže.

    :::image type="content" source="media/cluster/create-cluster-wizard.png" alt-text="Průvodce vytvořením clusteru – možnost HCI" lightbox="media/cluster/create-cluster-wizard.png":::

## <a name="step-1-get-started"></a>Krok 1: Začněte

Krok 1 Průvodce vás provede tím, že budete mít jistotu, že jsou splněné všechny požadavky, přidáte uzly serveru, nainstalujete potřebné funkce a pak v případě potřeby restartujete jednotlivé servery.

1. Přečtěte si požadavky uvedené v průvodci, abyste zajistili, že každý uzel serveru je připravený pro cluster. Jakmile budete hotovi, klikněte na tlačítko **Další** .
1. Na stránce **Přidat servery na cluster** zadejte uživatelské jméno a heslo účtu a pak klikněte na **Další** . Tento účet musí být členem místní skupiny Administrators na každém serveru.
1. Zadejte název prvního serveru, který chcete přidat, a pak klikněte na **Přidat** .
1. Opakujte krok 3 pro každý server, který bude součástí clusteru. Jakmile budete hotovi, klikněte na tlačítko **Další** .
1. V případě potřeby na stránce **připojit servery k doméně** zadejte doménu a účet pro připojení serverů k doméně. Pak můžete volitelně přejmenovat servery na výstižnější názvy a kliknout na **Další** .
1. Klikněte na **nainstalovat funkce** . Jakmile budete hotovi, klikněte na tlačítko **Další** .

    Průvodce nainstaluje následující požadované funkce:

    - BitLocker
    - Přemostění Datacenter (pro síťové adaptéry RoCEv2)
    - Clustering s podporou převzetí služeb při selhání
    - Souborový server
    - FS – modul odstranění duplicitních dat
    - Hyper-V
    - RSAT – modul AD-PowerShell
    - Replika úložiště (je nainstalovaná jenom pro roztažené clustery)

1. V případě potřeby **nainstalujte aktualizace** kliknutím na **instalovat aktualizace** . Jakmile budete hotovi, klikněte na **Další** .
1. V případě potřeby **aktualizace řešení** klikněte na **instalovat rozšíření** . Jakmile budete hotovi, klikněte na **Další** .
1. V případě potřeby klikněte na **restartovat servery** . Ověřte, zda byl každý server úspěšně spuštěn.

## <a name="step-2-networking"></a>Krok 2: používání sítě

Krok 2 Průvodce vás provede konfigurací virtuálních přepínačů a dalších síťových prvků pro váš cluster.

> [!NOTE]
> Pokud se zobrazí chyby uvedené během jakýchkoli síťových nebo virtuálních přepínačů, zkuste znovu kliknout na **použít a otestovat** .

1. Až skončíte, vyberte **Další: Sítě** .
1. V části **ověřit síťové adaptéry** počkejte, než se vedle každého adaptéru objeví zelené zaškrtávací políčko, a pak vyberte **Další** .

1. V případě **Vyberte adaptéry pro správu** vyberte jeden nebo dva adaptéry pro správu, které chcete použít pro každý server. Je nutné vybrat alespoň jeden z adaptérů pro účely správy, protože průvodce vyžaduje alespoň jednu vyhrazenou fyzickou síťovou kartu pro správu clusteru.  Jakmile je adaptér určený ke správě, je vyloučený ze zbytku pracovního postupu Průvodce.

    Adaptéry pro správu mají dvě možnosti konfigurace:

    - **Jeden fyzický síťový adaptér pro správu** . Pro tuto možnost se podporuje přiřazení DHCP i statických IP adres.

    - **Pro správu jsou seskupeny dva fyzické síťové adaptéry** . Při seskupování dvojice adaptérů je podporována pouze přiřazování statických IP adres. Pokud vybrané adaptéry používají adresování DHCP (buď pro jednu nebo obě), bude IP adresa DHCP převedena na statické IP adresy před vytvořením virtuálního přepínače.

    Pomocí seskupených adaptérů máte jedno připojení k více přepínačům, ale použijete jenom jednu IP adresu. Vyrovnávání zatížení bude k dispozici a odolnost proti chybám je okamžitá místo čekání na aktualizaci záznamů DNS.

    U každého serveru teď proveďte tento postup:

    - Zaškrtněte políčko **Popis** . Všimněte si, že jsou vybrané všechny adaptéry a průvodce vám může nabídnout doporučení.
    - Zrušte zaškrtnutí políček u adaptérů, které nechcete používat pro správu clusteru.

    > [!NOTE]
    > Můžete použít 1 GB adaptérů jako adaptéry pro správu, ale pro přenos provozu a zatížení virtuálního počítače doporučujeme použít 10 GB nebo rychlejší adaptéry.

1. Když byly provedeny změny, klikněte na tlačítko **použít a otestovat** .
1. V části **definovat sítě** zajistěte, aby každý síťový adaptér každého serveru měl jedinečnou statickou IP adresu, masku podsítě a ID sítě VLAN. Najeďte myší na jednotlivé prvky tabulky a podle potřeby zadejte nebo změňte hodnoty. Po dokončení klikněte na **použít a testovat** .

    > [!NOTE]
    > Aby bylo možné podporovat konfiguraci ID sítě VLAN pro cluster, všechny síťové karty na všech serverech musí podporovat vlastnost VLANID.

1. Počkejte, dokud se ve sloupci **stav** zobrazí **předáno** pro každý server, a poté klikněte na tlačítko **Další** . Tento krok ověří síťové připojení mezi všemi adaptéry se stejnou podsítí a ID sítě VLAN. Zadané IP adresy se přenesou z fyzického adaptéru na virtuální adaptéry po vytvoření virtuálních přepínačů v dalším kroku. Dokončení může trvat několik minut v závislosti na počtu nakonfigurovaných adaptérů.

1. V části **virtuální přepínač** vyberte jednu z následujících možností podle potřeby. V závislosti na tom, kolik adaptérů jsou k dispozici, se nemusí zobrazit všechny možnosti:

    - **Přeskočit vytvoření virtuálního přepínače**
    - **Vytvořit jeden virtuální přepínač pro výpočetní prostředky a úložiště společně**
    - **Vytvořit jeden virtuální přepínač jenom pro výpočetní výkon**
    - **Vytvoření dvou virtuálních přepínačů**

    > [!NOTE]
    > Pokud chystáte nasazení síťového adaptéru pro SDN (v **kroku 5: SDN** průvodce), budete potřebovat virtuální přepínač. Takže pokud se sem nerozhodnete vytvořit virtuální přepínač a nevytvářet ho mimo průvodce, průvodce neimplementuje síťový adaptér.

    Následující tabulka uvádí, které konfigurace virtuálních přepínačů jsou podporovány a povoleny pro různé konfigurace síťových adaptérů:

    | Možnost | adaptéry 1-2 | 3 adaptéry | seskupené adaptéry |
    | :------------- | :--------- |:--------| :---------|
    | jednoduchý přepínač (COMPUTE + úložiště) | enabled | enabled  | nepodporováno |
    | jednoduchý přepínač (jenom výpočetní výkon) | nepodporováno| enabled | enabled |
    | dva přepínače | nepodporováno | enabled | enabled |

1. Podle potřeby změňte název přepínače a další nastavení konfigurace a klikněte na **použít a otestovat** . Po vytvoření virtuálních přepínačů by měl **být pro každý** Server zobrazený sloupec **stav** .

## <a name="step-3-clustering"></a>Krok 3: clusteringu

3. krok tohoto průvodce zajistí, že všechno, co se v současnosti správně nastavilo, automaticky nastaví dvě lokality v případě roztaženého nasazení clusteru a pak cluster skutečně vytvoří. Vaše weby můžete také nastavit předem ve službě Active Directory.

1. Vyberte **Další: clustering** .
1. V části **ověřit cluster** vyberte **ověřit** . Ověření může trvat několik minut.

    Pokud se zobrazí automaticky otevírané okno **poskytovatel zabezpečení přihlašovacích údajů (CredSSP)** , vyberte **Ano** , pokud chcete, aby mohl průvodce povolit zprostředkovatele CredSSP pro pokračování. Po vytvoření clusteru a dokončení průvodce zakážete CredSSP, aby se zvýšilo zabezpečení. Pokud máte problémy se zprostředkovatelem CredSSP, přečtěte si další informace v tématu [řešení potíží se zprostředkovatelem](../manage/troubleshoot-credssp.md) zabezpečení.

1. Zkontrolujte všechny stavy ověření, Stáhněte si sestavu, abyste získali podrobné informace o případných chybách, udělejte změny a pak **znovu** klikněte na ověřit podle potřeby. Opakujte akci podle potřeby, dokud nebudou všechny kontroly ověřování prošly.
1. V části **vytvořit cluster** zadejte název clusteru.
1. V části **sítě** vyberte upřednostňovanou konfiguraci.
1. V části **IP adresy** vyberte buď dynamické, nebo statické IP adresy, které chcete použít.
1. Po dokončení klikněte na **vytvořit cluster** .

1. U roztaženého clusteru v části **přiřadit servery k lokalitám** pojmenujte tyto dvě lokality, které budou použity.

1. Dále přiřaďte každý server k lokalitě. Replikaci budete nastavovat později v různých lokalitách. Po dokončení klikněte na **použít** .

## <a name="step-4-storage"></a>Krok 4: úložiště

Krok 4 Průvodce vás provede nastavením Prostory úložiště s přímým přístupem pro váš cluster.

1. Vyberte **Další: úložiště** .
1. V části **ověřit jednotky** klikněte na **>** ikonu u každého serveru a ověřte, zda jsou disky funkční a připojené, a potom klikněte na tlačítko **Další** .
1. V části **vyčistit jednotky** klikněte na **vyčistit jednotky** a vyprázdněte jednotky dat. Až budete připraveni, klikněte na tlačítko **Další** .
1. V části **ověřit úložiště** klikněte na **Další** .
1. Zkontrolujte výsledky ověřování. Pokud je vše v pořádku, klikněte na tlačítko **Další** .
1. V části **povolit prostory úložiště s přímým přístupem** klikněte na **Povolit** .
1. Stáhněte si sestavu a zkontrolujte ji. Pokud je vše v pořádku, klikněte na **Dokončit** .

Blahopřejeme, teď máte cluster.

Po vytvoření clusteru může trvat nějakou dobu, než se název clusteru replikuje napříč vaší doménou, zejména pokud byly servery pracovní skupiny nově přidané do služby Active Directory. I když se cluster může zobrazit v centru pro správu systému Windows, nemusí být k dispozici, aby se mohl ještě připojit.

Pokud po nějaké době řešení clusteru neproběhne úspěšně, můžete ve většině případů místo názvu clusteru nahradit název serveru v clusteru.

## <a name="step-5-sdn-optional"></a>Krok 5: SDN (volitelné)

Tento volitelný krok vás provede nastavením součásti síťového adaptéru [softwarově definovaných sítí (SDN)](../concepts/software-defined-networking.md). Jakmile je síťový adaptér nastavený, dá se použít ke konfiguraci dalších komponent SDN, jako je Load Balancer softwaru a brány RAS.

> [!NOTE]
> SDN není pro roztažené clustery podporovaná nebo dostupná.

:::image type="content" source="media/cluster/create-cluster-network-controller.png" alt-text="Průvodce vytvořením clusteru – možnost HCI" lightbox="media/cluster/create-cluster-network-controller.png":::

1. Vyberte **Další: SDN** .
1. V části **hostitel** zadejte název síťového adaptéru.
1. Zadejte cestu k souboru VHD Azure Stack HCL. Pro rychlejší vyhledání použijte **Procházet** .
1. Zadejte počet virtuálních počítačů, které se mají vyhradit pro síťový adaptér. Pro zajištění vysoké dostupnosti se doporučuje tři až pět virtuálních počítačů.
1. V části **síť** zadejte ID sítě VLAN.
1. V případě **adresování sítě virtuálních počítačů** vyberte možnost **DHCP** nebo **static** .
1. Pokud jste vybrali možnost **DHCP** , zadejte název a IP adresu pro virtuální počítače síťového adaptéru.
1. Pokud jste vybrali možnost **static** , udělejte toto:
    1. Zadejte předponu podsítě.
    1. Zadejte výchozí bránu.
    1. Zadejte jeden nebo více serverů DNS. Kliknutím na **Přidat** přidejte další servery DNS.
1. V části **přihlašovací údaje** zadejte uživatelské jméno a heslo, které se použije k připojení virtuálních počítačů síťového adaptéru k doméně clusteru.
1. Zadejte heslo místního správce pro tyto virtuální počítače.
1. V části **Upřesnit** zadejte cestu k virtuálním počítačům.
1. Zadejte hodnoty pro **začátek** a **konec fondu adres** Mac.
1. Jakmile budete hotovi, klikněte na tlačítko **Další** .
1. Počkejte, dokud průvodce nedokončí svoji úlohu. Zůstat na této stránce, dokud nebudou dokončeny všechny úlohy průběhu. Klikněte na **Dokončit** .

Pokud se nasazení síťového adaptéru nepovede, proveďte následující kroky, než to zkusíte znovu:

- Zastavte a odstraňte všechny virtuální počítače síťového adaptéru, které průvodce vytvořil.  

- Vyčistěte všechny přípojné body VHD, které průvodce vytvořil.  

- Ujistěte se, že máte na svých hostitelích Hyper-V aspoň 50 100 GB volného místa.  

## <a name="after-you-complete-the-wizard"></a>Po dokončení Průvodce

Po dokončení průvodce jsou stále k dispozici některé důležité úkoly, které je třeba provést.

Prvním úkolem je zakázat protokol CredSSP (Credential Security Support Provider) na každém serveru z hlediska zabezpečení. Zapamatujte si, že pro Průvodce je nutné povolit CredSSP. Pokud máte problémy se zprostředkovatelem CredSSP, přečtěte si další informace v tématu [řešení potíží se zprostředkovatelem](../manage/troubleshoot-credssp.md) zabezpečení.

1. V centru pro správu Windows v části **všechna připojení** vyberte cluster, který jste právě vytvořili.
1. V části **nástroje** vyberte **servery** .
1. V pravém podokně vyberte první server v clusteru.
1. V části **Přehled** vyberte **Zakázat CredSSP** . Uvidíte, že červená zpráva **CREDSSP povolená** v horní části zmizí.
1. Zopakujte kroky 3 a 4 pro každý server v clusteru.

OK, teď máte další úkoly, které budete muset udělat:

- Nastavte určující cluster. Viz [Nastavení určujícího clusteru](witness.md).
- Vytvořte si svazky. Viz [vytvoření svazků](../manage/create-volumes.md).
- U roztaženého clusteru vytvořte svazky a nastavte replikaci pomocí repliky úložiště. Přečtěte si téma [vytvoření svazků a nastavení replikace pro roztažené clustery](../manage/create-stretched-volumes.md).

## <a name="next-steps"></a>Další kroky

- Zaregistrujte svůj cluster v Azure. Viz [Správa registrace Azure](../manage/manage-azure-registration.md).
- Proveďte konečné ověření clusteru. Viz [ověření clusteru Azure Stack HCI](validate.md)
- Zřizování virtuálních počítačů. Přečtěte si téma [Správa virtuálních počítačů v Azure Stack HCI pomocí centra pro správu systému Windows](../manage/vm.md).
- Cluster můžete nasadit také pomocí prostředí PowerShell. Přečtěte si téma [vytvoření Azure Stack clusteru HCI pomocí prostředí PowerShell](create-cluster-powershell.md).
- Síťový adaptér můžete nasadit také pomocí prostředí PowerShell. Viz [nasazení síťového adaptéru pomocí PowerShellu](network-controller-powershell.md).
